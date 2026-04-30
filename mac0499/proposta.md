## Proposta para TCC/2026

### Detecção de Objetos Astronômicos com YOLO em Imagens Multi-banda do S-PLUS

### Tiago Gomes Dourado de Oliveira

### Orientadora: Nina Hirata (IME - USP)

### Coorientadora: Lilianne Nakazono (IAG - USP)

---

### Introdução

#### O mapeamento de grandes áreas do céu gera volumes de dados que exigem métodos automatizados para a identificação de fontes luminosas. A Astronomia depende fortemente de dados observacionais para avançar modelos teóricos sobre a evolução do universo, com telescópios ópticos capturando milhares de objetos simultaneamente (MENDES DE OLIVEIRA et al., 2019). Tradicionalmente, algoritmos baseados em limites de intensidade, como o SExtractor, são empregados para detecção, mas apresentam falhas, como a geração de múltiplas detecções espúrias ao redor de estrelas saturadas e ao longo de seus artefatos ópticos (BERTIN; ARNOUTS, 1996). Adicionalmente, investigações conduzidas por Perin et al. (2023) na tarefa de classificação (categorização de objetos individuais em cada imagem sem localizá-los) demonstraram que modelos ensemble combinando redes convolucionais e classificadores baseados em dados tabulares atingem maior acurácia geral, com as redes neurais apresentando desempenho superior especificamente na classificação de objetos com sinais fracos, os quais constituem a maioria das observações disponíveis. Dessa forma, o projeto visa aprimorar a detecção de galáxias, estrelas e quasares em imagens do *Southern Photometric Local Universe Survey* (S-PLUS) por meio do uso de redes convolucionais YOLO (You Only Look Once). 

#### O objetivo central é treinar modelos da família YOLO para identificar simultaneamente as três classes, elevando as métricas de *Mean Average Precision* (mAP). O projeto investigará a mitigação do desbalanceamento de classes e a inconsistência das anotações automáticas por meio do cruzamento com catálogos externos mais precisos e de pré-processamento mais robusto (possivelmente também pela geração de dados sintéticos por ferramentas pré-existentes).

---

### Background

#### O levantamento S-PLUS disponibiliza seus dados por meio de Data Releases (DRs), fornecendo imagens fotométricas e tabelas de atributos catalogados (MENDES DE OLIVEIRA et al., 2019). As imagens consistem em campos inteiros de alta resolução cobrindo grandes áreas do céu, possuindo dimensões de 11000 por 11000 pixels distribuídas em 12 bandas fotométricas. Os dados tabulares contêm características morfológicas dos objetos detectados, como o raio de Kron e atributos de forma espacial, além de estimativas probabilísticas de classificação estruturadas a partir de modelos de Random Forest (NAKAZONO et al., 2021). O cruzamento das coordenadas espaciais com as métricas tabulares possibilita a geração automática de *bounding boxes* (ou caixas delimitadoras) para o embasamento de tarefas de visão computacional. 
#### Para processar essas informações visuais, empregam-se redes neurais convolucionais (CNNs), que utilizam sequências de camadas de convolução e *pooling* para a extração progressiva de características espaciais das imagens astronômicas. A arquitetura YOLO adapta essa base para a tarefa de detecção ao processar a imagem integralmente e predizer espacialmente as coordenadas das *bounding boxes* e as probabilidades de classe em uma etapa única (REDMON et al., 2015).
#### O resto das etapas de aprendizado de máquina consiste na otimização de uma função de perda (ou *loss function*), que quantifica a divergência de localização e categórica entre as predições do modelo e as anotações de referência. Durante a fase de treinamento, o algoritmo de *backpropagation* computa os gradientes de erro e ajusta iterativamente os pesos internos da rede neural. O objetivo é minimizar a função de perda de forma estável, maximizando consequentemente métricas de precisão, como a Mean Average Precision (mAP). No contexto de levantamentos astronômicos, a otimização dessa função é frequentemente prejudicada por anotações ruidosas e vieses derivados de catálogos automatizados, exigindo metodologias robustas para evitar que o modelo seja penalizado erroneamente por detectar objetos não catalogados ou categorizados incorretamente na base de dados original (DHAR; SHAMIR, 2022).

---

### Metodologia

#### Para superar as limitações observadas, o trabalho adotará algumas abordagens principais a serem seguidas paralelamente. As primeiras tarefas estão relacionadas à busca por anotações confiáveis em outros levantamentos (como o Sloan Digital Sky Survey) que cobrem as mesmas regiões do céu. O pré-processamento das imagens será implementado de maneira mais robusta por meio de dados tabulares e matriciais extraídos do S-PLUS e outros incrementos, como a maior restrição em relação a áreas úteis dos campos ou regiões do céu obtidos. Também poderá ser experimentado o treinamento com dados sintéticos gerados pelo Photon Simulator (PETERSON et al., 2015), visando equilibrar a representação das classes e reduzir a influência do ruído de fundo. A avaliação será baseada em matrizes de confusão normalizadas e no mAP.

---

### Plano de atividades

| Atividade | Abr | Mai | Jun | Jul | Ago | Set | Out | Nov |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 1. Estudo de literatura e novas versões YOLO | X | X | | | | | | |
| 2. Cruzamento de catálogos e refinamento de rótulos | | X | X | X | | | | |
| 3. Treinamentos e experimentos | | | | X | X | | | |
| 4. Análise de métricas | | | | X | X | X | | |
| 5. Escrita da monografia | | | | | | X | X | X |

<br>

---

### Referências Bibliográficas

BERTIN, E.; ARNOUTS, S. SExtractor: Software for source extraction. **Astronomy and Astrophysics**, Supplement, v. 117, p. 393-404, jun. 1996. 

DHAR, S.; SHAMIR, L. Systematic biases when using deep neural networks for annotating large catalogs of astronomical images. **Astronomy and Computing**, v. 38, p. 100545, 2022. ISSN 2213-1337. Disponível em:
<https://www.sciencedirect.com/science/article/pii/S2213133722000014>.

MENDES DE OLIVEIRA, C. et al. The Southern Photometric Local Universe Survey (S-PLUS): improved SEDs, morphologies and redshifts with 12 optical filters. **Monthly Notices of the Royal Astronomical Society**, v. 489, n. 1, p. 241-267, out. 2019.

NAKAZONO, L. et al. On the discovery of stars, quasars, and galaxies in the Southern Hemisphere with S-PLUS DR2. Monthly Notices of the Royal Astronomical Society, v. 507, n. 4, p. 5847-5868, jul. 2021.

PERIN, G. J. et al. Combinação de dados tabulares e imagens para a classificação de objetos astronômicos. In: Workshop de Trabalhos da Graduação, Anais Estendidos do XXXVI Conference on Graphics, Patterns and Images. [S.l.: s.n.], 2023.

PETERSON, J. R. et al. Simulation of astronomical images from optical survey telescopes using a comprehensive photon monte carlo approach. **The Astrophysical Journal Supplement Series**, v. 218, n. 1, p. 14, maio 2015. ISSN 1538-4365. Disponível em: <http://dx.doi.org/10.1088/0067-0049/218/1/14>

REDMON, J. et al. You only look once: Unified, real-time object detection. CoRR,
abs/1506.02640, 2015. Disponível em: <http://arxiv.org/abs/1506.02640>.