## Second phase of the course: points about the ArKanjo work

### Motivation:

The duplications in the AMD DRM repository that we worked on were detected by the use of the [ArKanjo tool](https://github.com/arkanjo-tool/arkanjo/). Because of that, we found it to be a good idea to try to improve the tool further.

### Issue chosen:

[Store preprocess metadata in a structured configuration file
 #22](https://github.com/arkanjo-tool/arkanjo/issues/22)
>Currently, preprocess metadata is stored in a plain text file (```config.txt```) using a human-readable format.
>
> ```bash
> $ cat config.txt
> path of the current preprocess: /dev/shm/tmpuy_04dtt
> Finished time: Sat Jun 20 20:23:07 2026
> ```
> As the preprocess cache evolves, additional metadata fields may need to be stored alongside the existing information.
>
>The current format is difficult to extend and does not provide a generic way to store arbitrary metadata. Using a structured configuration format (e.g. INI or JSON) would make it easier to add new metadata fields while keeping the file readable and machine-processable.


### History of the changes (01/07 - 03/07):

[Pull request history (GitHub) link](https://github.com/arkanjo-tool/arkanjo/pull/42).

We interacted with the maintainer Guilherme Ivo.

**Commit 1: `refactor(preprocessor): use JSON for preprocess cache metadata`**

Replaced `config.txt`'s plain-text prefix format with `config.json`. `save_current_run_params`/`read_current_run_params` switched from string concatenation and manual line parsing to `nlohmann::json`. `test.cpp` skip list file extension renamed to match.

**Conflict 1 (merge upstream main)**

- `test.cpp`: both sides touched `skip_check_list`. Kept upstream's structure, swapped their `"config.txt"` entry for `"config.json"`.
- `config.txt`: modify/delete conflict. We deleted it, upstream changed its `path` value to relative. Resolved with `git rm`, then edited `config.json`'s content to match upstream's relative-path convention.

**Commit 2: `refactor(preprocessor): address maintainer review feedback`**

- Introduced `PreprocessRunParams` struct with `to_json`/`from_json` and adapted the rest of the code, replacing string-keyed json and positional vector access. The maitainer requested implementing a struct to make the data more explicit and type-safe.
- Restored Doxygen `///<` comments. The maintainer requested to keep them for Doxygen documentation. 
- Reused `Utils::write_file` instead of manual `ofstream`/`create_directories`. The maintainer requested the change because the `Utils` function performs additional checks to ensure the file can be safely opened before writing.
- From the maintainer suggestion, rewrote the JSON data assignment to be line-separated, so that formatting keeps consistency and `git blame` history is uncluttered:
```cpp
json data = {
    {PATH_KEY, path.string()},
    {TIME_KEY, time_str},
};
```

**Conflict 2 (merge upstream main again)**

`function.cpp`: upstream converted `relative_path` to `fs::path` typing on the same line we'd already changed to `params.path`. Kept our struct field access, layered upstream's `fs::path`/`.string()` typing on top, since the two changes were complementary.

**Commit 3: `refactor(preprocessor): use fs::path for run params path field`**

Changed `PreprocessRunParams::path` from `std::string` to `fs::path`, aligning with upstream's path-safety convention. Removed the now-redundant `fs::path{params.path}` wraps at both call sites. `to_json` updated to call `.string()` on write.

### Merge of the PR and final response from the maintainer Guilherme Ivo (03/07):
> Thanks for the PR.
>
> This feature will enable several other useful improvements in ArKanjo and make it easier to add new functionality going forward. I also resolved a few merge conflicts manually.
