#### First phase of the course: points about the AMD DRM work

##### 08/04:

I formed a duo with Luiz Fernandes and we are working on two of the suggested issues from the course, which are related to code duplication. We cloned the [AMD DRM repository](https://gitlab.freedesktop.org/agd5f/linux.git). The first pair of duplicated functions is `jpeg_v5_0_0.c::jpeg_v5_0_0_process_interrupt` and `jpeg_v5_3_0.c::jpeg_v5_3_0_process_interrupt`. The second pair of duplicated functions is `jpeg_v2_0.c::jpeg_v2_0_process_interrupt` and `jpeg_v3_0.c::jpeg_v3_0_process_interrupt`. Both pairs are under `drivers/gpu/drm/amd/amdgpu/`.

##### 15/04 until the submission of the patch:

We deduplicated the functions by introducing a new function `jpeg_v5_process_interrupt` that is called by both `jpeg_v5_0_0_process_interrupt` and `jpeg_v5_3_0_process_interrupt`; we also added a new function `jpeg_v2_process_interrupt` that is called by both `jpeg_v2_0_process_interrupt` and `jpeg_v3_0_process_interrupt`. The deduplication and export of the new functions were possible thanks to the reuse of macro aliases. Below are implementation details of the deduplication of `v5_0_0` and `v5_3_0` (very similar to the other deduplication) through `diff`:

```cpp
 drivers/gpu/drm/amd/amdgpu/jpeg_v5_0_0.c |  2 +-
 drivers/gpu/drm/amd/amdgpu/jpeg_v5_0_0.h |  4 ++++
 drivers/gpu/drm/amd/amdgpu/jpeg_v5_3_0.c | 22 +++-------------------
 3 files changed, 8 insertions(+), 20 deletions(-)

diff --git a/drivers/gpu/drm/amd/amdgpu/jpeg_v5_0_0.c b/drivers/gpu/drm/amd/amdgpu/jpeg_v5_0_0.c
index 46bf15dce..4575d1f9d 100644
--- a/drivers/gpu/drm/amd/amdgpu/jpeg_v5_0_0.c
+++ b/drivers/gpu/drm/amd/amdgpu/jpeg_v5_0_0.c
@@ -625,7 +625,7 @@ static int jpeg_v5_0_0_set_interrupt_state(struct amdgpu_device *adev,
        return 0;
 }

-static int jpeg_v5_0_0_process_interrupt(struct amdgpu_device *adev,
+int jpeg_v5_0_0_process_interrupt(struct amdgpu_device *adev,
                                      struct amdgpu_irq_src *source,
                                      struct amdgpu_iv_entry *entry)
 {
diff --git a/drivers/gpu/drm/amd/amdgpu/jpeg_v5_0_0.h b/drivers/gpu/drm/amd/amdgpu/jpeg_v5_0_0.h
index 5abb96159..4eeb0c147 100644
--- a/drivers/gpu/drm/amd/amdgpu/jpeg_v5_0_0.h
+++ b/drivers/gpu/drm/amd/amdgpu/jpeg_v5_0_0.h
@@ -32,4 +32,8 @@

 extern const struct amdgpu_ip_block_version jpeg_v5_0_0_ip_block;

+int jpeg_v5_0_0_process_interrupt(struct amdgpu_device *adev,
+                                 struct amdgpu_irq_src *source,
+                                 struct amdgpu_iv_entry *entry);
+
 #endif /* __JPEG_V5_0_0_H__ */
diff --git a/drivers/gpu/drm/amd/amdgpu/jpeg_v5_3_0.c b/drivers/gpu/drm/amd/amdgpu/jpeg_v5_3_0.c
index 1821dced9..92aaf5dea 100644
--- a/drivers/gpu/drm/amd/amdgpu/jpeg_v5_3_0.c
+++ b/drivers/gpu/drm/amd/amdgpu/jpeg_v5_3_0.c
@@ -32,8 +32,11 @@
 #include "vcn/vcn_5_3_0_offset.h"
 #include "vcn/vcn_5_3_0_sh_mask.h"
 #include "ivsrcid/vcn/irqsrcs_vcn_5_0.h"
+#include "jpeg_v5_0_0.h"
 #include "jpeg_v5_3_0.h"

+#define jpeg_v5_3_0_process_interrupt jpeg_v5_0_0_process_interrupt
+
 static void jpeg_v5_3_0_set_dec_ring_funcs(struct amdgpu_device *adev);
 static void jpeg_v5_3_0_set_irq_funcs(struct amdgpu_device *adev);
 static int jpeg_v5_3_0_set_powergating_state(struct amdgpu_ip_block *ip_block,
@@ -608,25 +611,6 @@ static int jpeg_v5_3_0_set_interrupt_state(struct amdgpu_device *adev,
        return 0;
 }

-static int jpeg_v5_3_0_process_interrupt(struct amdgpu_device *adev,
-                                     struct amdgpu_irq_src *source,
-                                     struct amdgpu_iv_entry *entry)
-{
-       DRM_DEBUG("IH: JPEG TRAP\n");
-
-       switch (entry->src_id) {
-       case VCN_5_0__SRCID__JPEG_DECODE:
-               amdgpu_fence_process(adev->jpeg.inst->ring_dec);
-               break;
-       default:
-               DRM_DEV_ERROR(adev->dev, "Unhandled interrupt: %d %d\n",
-                         entry->src_id, entry->src_data[0]);
-               break;
-       }
-
-       return 0;
-}
-
 static int jpeg_v5_3_0_ring_reset(struct amdgpu_ring *ring,
                                  unsigned int vmid,
                                  struct amdgpu_fence *timedout_fence)
```

##### First patch (21/04):

We submitted our [first patch series changes](https://lists.freedesktop.org/archives/amd-gfx/2026-April/143292.html) to the AMD DRM mailing list. The successful run of the course's CI pipeline (ran before sending to the maintainers) is available [here](https://gitlab.freedesktop.org/marcelomspessoto/dsl-linux/-/pipelines/1650688).

##### First response (16/06):

Maintainer Alex Deucher said that we could just assign the earlier versions’ ```_process_interrupt``` functions directly in the newer ones’ ```_irq_funcs```. We will follow his suggestion and send new patches for review.

##### Second patch (26/06):

Implemented the simple refactoring suggested by Alex Deucher:
- Macro aliases in the newer files removed, e.g.,

```cpp
#define jpeg_v5_3_0_process_interrupt jpeg_v5_0_0_process_interrupt;
```

- Direct function assignment implemented, e.g.

```cpp
--- a/drivers/gpu/drm/amd/amdgpu/jpeg_v5_3_0.c
+++ b/drivers/gpu/drm/amd/amdgpu/jpeg_v5_3_0.c

 static const struct amdgpu_irq_src_funcs jpeg_v5_3_0_irq_funcs = {
        .set = jpeg_v5_3_0_set_interrupt_state,
-       .process = jpeg_v5_3_0_process_interrupt,
+       .process = jpeg_v5_0_0_process_interrupt,
 };
```

##### Discussion and application of the second patch, chronologically (26/06 - 02/07):
[Response from Christian König:](https://lists.freedesktop.org/archives/amd-gfx/2026-June/147671.html)
> Usually we intentionally don't do any cross IP version reuse, that has cause tons of problems in the past.
> 
> @Leo any particular reason why we don't have a separate SRCID file for VCN3?
> 
> My educated guess is that nothing changed compared to VCN2 and nobody cared to re-generate the file from the HW definition.

[Response from Alex Deucher:](https://lists.freedesktop.org/archives/amd-gfx/2026-June/147706.html)
> I think it's the same so no need for a separate one.  There are a
number of existing places where VCN or jpeg use one function
implementation across multiple generations.

[Our response:](https://lists.freedesktop.org/archives/amd-gfx/2026-July/147917.html)
> Thanks for the feedback. Given what both of you pointed out, what do
you suggest that we can do?

[Resolution and application of the patch series by Alex Deucher:](https://lists.freedesktop.org/archives/amd-gfx/2026-July/147907.html)
> I've applied the series.  Thanks.

[Relevant point made by Christian König after the application of the patch series:](https://lists.freedesktop.org/archives/amd-gfx/2026-July/147909.html)
> I think we should add a comment to functions which are used for more than one IP generation.
> 
> Just to make sure that we don't accidentially add modifications which are specific to one generation.
