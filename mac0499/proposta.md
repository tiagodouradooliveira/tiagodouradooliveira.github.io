## Proposta para TCC/2026

### Detecção de Objetos Astronômicos com YOLO em Imagens Multi-banda do S-PLUS

### Tiago Gomes Dourado de Oliveira

### Orientadora: Nina Hirata (IME - USP)

### Coorientadora: Lilianne Nakazono (IAG - USP)

---

### Introdução

#### O mapeamento de grandes áreas do céu gera volumes de dados que poderiam ser beneficiados com métodos automatizados para a identificação de objetos astronômicos. O projeto visa aprimorar a detecção de galáxias, estrelas e quasares em imagens do Southern Photometric Local Universe Survey (S-PLUS). Imagens do S-PLUS possuem uma menor relação sinal-ruído, o que justifica a melhoria e otimização do pré-processamento e treinamento de modelos da arquitetura YOLO para detectar objetos com métricas aprimoradas. O objetivo central é treinar modelos da família YOLO para identificar quasares, estrelas e galáxias, elevando as métricas de Mean Average Precision (mAP). O projeto investigará a mitigação do desbalanceamento de classes e a inconsistência das anotações automáticas por meio de alguns incrementos: pré-processamento mais robusto baseado em weight maps e outras informações do S-PLUS e o cruzamento com catálogos externos mais precisos.

---

### Background/fundamentos

#### O levantamento S-PLUS utiliza um sistema fotométrico de 12 bandas para caracterizar morfologias e redshifts. Em experimentos de iniciação científica, utilizou-se a arquitetura YOLOv3 da região Stripe 82. Os resultados indicaram um mAP50 com desempenho crítico para a classe de quasares em comparação a estrelas e galáxias. Identificou-se que o pré-processamento atual e a anotação automática baseada em restrições a partir dos dados tabulares é insuficiente, pois pode introduzir viés e ruído. Portanto, o problema persiste na sensibilidade do modelo à consistência dos rótulos e à natureza pontual de estrelas e quasares em relação ao fundo.

---

### Metodologia

#### Para superar as limitações observadas, o trabalho adotará algumas abordagens principais a serem seguidas paralelamente. Primeiramente, será envolvida a busca por anotações confiáveis em outros levantamentos (como o Sloan Digital Sky Survey) que cobrem as mesmas regiões do céu. O pré-processamento das imagens será implementado de maneira mais robusta por meio do uso de weight maps extraídos do S-PLUS e outros incrementos, como a maior restrição em relação a áreas úteis dos campos ou regiões do céu obtidos. Também poderá ser experimentado o treinamento com dados sintéticos gerados pelo Photon Simulator, visando equilibrar a representação das classes e reduzir a influência do ruído de fundo. A avaliação será baseada em matrizes de confusão normalizadas e no mAP.

---

### Plano de atividades

#### 1. Estudo de literatura e novas versões YOLO - abril e maio

#### 2. Cruzamento de catálogos e refinamento de rótulos - maio e junho

#### 3. Treinamentos e experimentos - julho e setembro

#### 4. Análise de métricas - outubro

#### 5. Escrita da monografia - outubro e novembro
