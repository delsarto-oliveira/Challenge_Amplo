README!!!

Obs: Todos os itens descritos abaixo foram executados em ambiente Python. A "PASTA 1", referente ao códigos desenvolvidos, não é pública, logo, as estruturas de desenvolvimento dos códigos, assim como as suas execuções, serão demonstradas no dia da apresentação dos dados.

### Descrição do conteúdos das pastas e metodologias

### PASTA 2 – Limites de calibração, baixados de plataformas de dados livres, referentes aos polígonos das UCs federais nos limites do estado de Minas Gerais

### PASTA 3 – Geração de AEs e ADAs

Objetivo: Criar 20 Áreas de Estudo (polígonos irregulares) e 20 ADAs (buffers circulares internos), garantindo que:  

- AEs não se intersectem entre si.  
- AEs fiquem fora das UCs, mas até 5 km de distância.  
- Cada ADA esteja contida na respectiva AE e não intercepte UCs ou outras ADAs.  

Metodologia e escolhas: 

- Sistema de Referência: SIRGAS 2000 / UTM Zona 23S (EPSG:31983), adequado para cálculos métricos.  
- Geração de polígonos irregulares: Polígonos criados com raio médio variável e deformação aleatória, usando `shapely` e `numpy`.  
- Validação geométrica: Correção via `buffer(0)` para resolver autointerseções.  
- Controle de interseção: Checagem com `intersects()` e `distance()` para respeitar restrições espaciais.  
- Exportação: GeoPackage com camadas “AE” e “ADA”.  

### PASTA 4 – Validação e Simplificação de Geometrias

Objetivo (i): Garantir integridade geométrica das AEs e ADAs, verificar relações espaciais com UCs e gerar relatório detalhado.  

Metodologia e escolhas:  

- Correção de geometrias: `buffer(0)` para corrigir polígonos inválidos.  
- Simplificação: Tolerância controlada (`0.0001`) para reduzir complexidade, mantendo topologia.  
- Validações espaciais:  
  - ADA contida na AE (`within()`).  
  - ADA a no máximo 5 km de UCs (`distance()`).  
- Exportação:  
  - GeoPackage atualizado com camadas validadas.  
  - CSV contendo informações de validade, simplificação e distância.  

### PASTA 5 – Tratamento de Ocorrências

Objetivo (i): Obter registros de aves para cada AE a partir do GBIF e gerar uma amostra aleatória proporcional para análise.  

Metodologia e escolhas:  

- Reprojeção: AE para EPSG:4326, necessária para consultas GBIF em coordenadas geográficas.  
- Simplificação pré-consulta: Redução de vértices com preservação da topologia (`simplify()`), evitando envelopes excessivamente complexos.  
- Consulta GBIF: Pesquisa por polígono WKT (`pygbif.occurrences.search`), limitando registros por AE e total agregado.  
- Amostragem proporcional: Seleção aleatória para uniformizar o número de registros por AE.  
- Exportação: CSV consolidado com atributos relevantes de ocorrência.  

Objetivo (ii): Corrigir coordenadas, filtrar registros fora da área de interesse e preparar dados geoespaciais para análise.  

Metodologia e escolhas: 

- Correção de coordenadas: Tratamento de pontos mal formatados, excesso de pontos decimais e ajustes de escala/sinal.  
- Filtragem geográfica: Apenas registros dentro de Minas Gerais e entorno.  
- Remoção de duplicatas: Evita repetição de observações.  
- Conversão em GeoDataFrame: Para integração espacial e cálculo de indicadores.  
- Reprojeção: SIRGAS 2000 / UTM Zona 23S, compatível com AEs e ADAs.  
- Exportação: CSV com coordenadas projetadas e Shapefile para GIS.  

### PASTA 6 – Cálculo de Indicadores Ambientais

Objetivo: Computar indicadores ecológicos para AEs e ADAs usando registros de aves validados.  

Indicadores calculados:  

- Riqueza de espécies: Número de espécies distintas por zona.  
- Número de registros: Contagem total de observações.  
- Número de indivíduos: Soma da coluna `n_individu` ou contagem de registros.  
- Área (ha): Medida da zona em hectares.  
- Distância (m) mínima até UC: Proximidade da zona mais próxima de uma UC.  
- Número de UCs em raio de 5 km: Avaliação de cobertura de conservação próxima.  

Metodologia e escolhas:  

- Filtragem espacial: Contagem apenas de aves dentro de cada AE/ADA (`within()`).  
- Unificação dos resultados: Concatenação das tabelas de AE e ADA para análise integrada.  
- Exportação: CSV para análise tabular e Shapefile para visualização em GIS.

### PASTA 7 - Mapa Interativo
