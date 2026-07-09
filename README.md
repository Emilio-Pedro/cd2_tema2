# Tema 2 - Mobilidade Urbana Inteligente

## Integrantes

* Pedro Emilio Castro Lemos
* Matheus Gualter Silva Resende
* João Vitor Dantas Barbosa
* Paulo Daniel Forti da Fonseca
* Pedro Henrique Lopes Duarte

---
Projeto desenvolvido para analisar padrões espaciais e temporais de mobilidade urbana a partir de dados reais de corridas da Uber em Nova York. O trabalho foi organizado em quatro etapas: tratamento dos dados, análise exploratória, aplicação de algoritmos e interpretação final com padrões frequentes, regras de associação e anomalias.


## Objetivo do projeto

O objetivo é construir um fluxo reprodutível de ciência de dados para responder como variáveis de localização e tempo ajudam a descrever a demanda por corridas da Uber no recorte analisado.

As perguntas principais foram:

* em quais horários a demanda se concentra;
* se existem padrões espaciais relevantes nas coordenadas;
* se `Lat`, `Lon`, `Hour` e `Day` carregam informação complementar;
* qual algoritmo de agrupamento representa melhor os dados;
* quais padrões temporais e espaciais aparecem nos clusters finais;
* quais registros se comportam como anomalias no espaço-tempo.

## Dataset utilizado

Base: **Uber Pickups in New York City**.

Arquivo local utilizado:

```text
ubernymaio14.csv
```

O arquivo completo possui **652.435 registros**, mas o projeto trabalha com os **20.000 primeiros registros** para manter o processamento adequado ao escopo da disciplina e aos notebooks. Após a remoção de duplicatas, o conjunto tratado contém **19.220 registros válidos**.

Resumo do recorte:

| Item | Valor |
| ---- | ----- |
| Registros lidos | 20.000 |
| Duplicatas removidas | 780 |
| Registros tratados | 19.220 |
| Período coberto | 01/05/2014 00:02 a 16/05/2014 20:10 |
| Base operacional no recorte | `B02512` |
| Valores ausentes após tratamento | 0 |

Colunas principais:

| Coluna | Descrição |
| ------ | --------- |
| `Date/Time` | data e horário da corrida |
| `Lat` | latitude |
| `Lon` | longitude |
| `Base` | base operacional da Uber |
| `Hour` | hora extraída de `Date/Time` |
| `Day` | dia do mês extraído de `Date/Time` |


## Etapa 1 - Tratamento dos dados

Notebook:

```text
etapa1/etapa1cd2.ipynb
```

Entrada:

```text
ubernymaio14.csv
```

Saída:

```text
ubernymaio14_tratado.csv
```

O que foi feito:

* leitura dos 20.000 primeiros registros;
* verificação de valores ausentes;
* remoção de duplicatas;
* conversão de `Date/Time` para formato de data e hora;
* criação das colunas `Hour` e `Day`;
* análise inicial por boxplots;
* normalização de `Lat`, `Lon` e `Hour` com Min-Max Scaling;
* exportação da base tratada.

Decisão importante: os valores extremos de latitude e longitude foram mantidos, pois não indicavam erro evidente. Eles podem representar corridas reais em regiões menos frequentes.

## Etapa 2 - Análise exploratória

Notebook:

```text
etapa2/etapa2cd2.ipynb
```

A Etapa 2 usa diretamente o arquivo `ubernymaio14_tratado.csv`.

O que foi analisado:

* estatísticas descritivas de `Lat`, `Lon`, `Hour` e `Day`;
* histogramas e boxplots;
* possíveis outliers pelo critério de `1,5 x IQR`;
* distribuição espacial dos registros;
* distribuição temporal por hora;
* correlações de Pearson e Spearman;
* redundância entre atributos;
* PCA;
* impacto das representações na vizinhança dos pontos.

Principais resultados:

| Resultado | Valor |
| --------- | ----- |
| Pico de registros | 17h |
| Registros às 17h | 1.622 |
| Registros entre 14h e 19h | 8.385, ou 43,63% |
| Registros entre 0h e 5h | 1.163, ou 6,05% |
| Outliers em `Lat` pelo IQR | 855 |
| Outliers em `Lon` pelo IQR | 1.856 |
| Maior Pearson | `Lat`--`Lon`: 0,147 |
| Maior Spearman | `Lat`--`Lon`: 0,496 |
| Variância explicada por PC1 + PC2 | 54,58% |
| Componentes para preservar 90% da variância | 4 |

Conclusões da Etapa 2:

* não houve correlação forte o suficiente para remover variáveis;
* o PCA não permitiu reduzir dimensionalidade sem perda relevante;
* a projeção PCA 2D é útil para visualização, mas não substitui os atributos originais;
* incluir `Hour` e `Day` muda fortemente a noção de proximidade entre registros;
* análises puramente espaciais e análises espaço-temporais respondem a perguntas diferentes.

## Etapa 3 - Aplicação de algoritmos

Notebook:

```text
etapa3/etapa3cd2.ipynb
```

Entrada:

```text
ubernymaio14_tratado.csv
```

Saída:

```text
etapa3/clusters_etapa3.csv
```

Variáveis usadas:

```text
Lat, Lon, Hour, Day
```

Como `Lat`, `Lon` e `Hour` estavam normalizadas, mas `Day` permaneceu na escala 1 a 16, os algoritmos baseados em distância foram aplicados após padronização com `StandardScaler`.

Algoritmos avaliados:

* K-Means;
* Agglomerative Clustering;
* DBSCAN.

Métricas usadas:

| Métrica | Papel na análise |
| ------- | ---------------- |
| Inércia | avalia a compactação interna no K-Means |
| Silhouette | mede coesão e separação dos clusters |
| Davies-Bouldin | compara dispersão interna e separação; menor é melhor |
| Calinski-Harabasz | mede separação entre grupos em relação à dispersão interna |
| ARI | mede estabilidade dos rótulos entre sementes |
| NMI | mede consistência de informação entre diferentes execuções |

O K-Means foi testado com `K` entre 2 e 10. O valor escolhido foi:

```text
K = 4
```

Justificativa:

* `K=4` obteve silhouette de 0,2568;
* Davies-Bouldin foi 1,1588;
* Calinski-Harabasz foi 4.631,12;
* ARI mínimo entre sementes foi 0,996;
* NMI mínimo entre sementes foi 0,992;
* `K=5` teve silhouette levemente maior, mas menor estabilidade;
* `K=7` também foi estável, mas aumentava a complexidade com ganho pequeno.

Distribuição final dos clusters:

| Cluster | Registros | Percentual | Interpretação principal |
| ------- | --------- | ---------- | ----------------------- |
| 0 | 6.816 | 35,46% | concentrado nos dias 11 a 16, principalmente tarde/noite |
| 1 | 6.590 | 34,29% | concentrado nos dias 1 a 5, principalmente tarde/noite |
| 2 | 589 | 3,06% | grupo pequeno e mais afastado geograficamente |
| 3 | 5.225 | 27,19% | associado ao período da manhã, com pico próximo de 7h |

## Visualização

Notebook:

```text
visualizacao/visualizacao.ipynb
```

Entradas principais:

```text
etapa3/clusters_etapa3.csv
ubernymaio14.csv
```

Arquivos gerados:

```text
visualizacao/mapa_ny.html
visualizacao/mapa_clusters_ny.html
visualizacao/heatmap_ny.html
```

Os mapas usam as coordenadas originais quando disponíveis (`Lat_original` e `Lon_original`) e permitem inspecionar visualmente os pontos, os clusters e a densidade espacial.

## Etapa 4 - Padrões frequentes, anomalias e conclusões

Notebook:

```text
etapa4/etapa4cd2.ipynb
```

Entrada:

```text
etapa3/clusters_etapa3.csv
```

Saídas:

```text
etapa4/padroes_frequentes_etapa4.csv
etapa4/regras_associacao_etapa4.csv
etapa4/anomalias_etapa4.csv
```

### Padrões frequentes e regras de associação

Foi usado o algoritmo Apriori com itens derivados das seguintes categorias:

* cluster do K-Means;
* período do dia;
* faixa do dia do mês;
* faixa de latitude;
* faixa de longitude.

Parâmetros usados:

| Parâmetro | Valor |
| --------- | ----- |
| Suporte mínimo | 8% |
| Confiança mínima | 55% |
| Lift mínimo para regras interpretáveis | 1,05 |
| Tamanho máximo dos itemsets | 3 |

Resultados:

| Item | Valor |
| ---- | ----- |
| Padrões frequentes encontrados | 88 |
| Regras de associação filtradas | 17 |

Principais regras:

| Regra | Suporte | Confiança | Lift |
| ----- | ------- | --------- | ---- |
| `periodo=manha -> cluster=3` | 19,32% | 83,25% | 3,06 |
| `faixa_dia=01-05 + periodo=tarde -> cluster=1` | 10,47% | 95,49% | 2,79 |
| `faixa_dia=01-05 + periodo=noite -> cluster=1` | 10,42% | 95,38% | 2,78 |
| `faixa_dia=11-16 + periodo=tarde -> cluster=0` | 13,32% | 96,93% | 2,73 |
| `faixa_dia=11-16 + periodo=noite -> cluster=0` | 9,64% | 96,11% | 2,71 |

Interpretação: os clusters não representam apenas regiões geográficas. Eles também refletem recortes temporais fortes, principalmente a separação entre manhã, início do período analisado e fim do período analisado.

### Detecção de anomalias

As anomalias foram detectadas no mesmo espaço usado para clusterização:

```text
Lat, Lon, Hour, Day
```

Procedimento:

* padronização das variáveis;
* cálculo da distância média aos 5 vizinhos mais próximos;
* marcação como anomalia dos registros acima do percentil 97,5%.

Resultados:

| Item | Valor |
| ---- | ----- |
| Anomalias detectadas | 481 |
| Percentual da amostra | 2,50% |
| Limiar da distância média aos 5 vizinhos | 0,7116 |

Distribuição por cluster:

| Cluster | Anomalias | Percentual dentro do cluster |
| ------- | --------- | ---------------------------- |
| 0 | 81 | 1,19% |
| 1 | 90 | 1,37% |
| 2 | 160 | 27,16% |
| 3 | 150 | 2,87% |

O cluster 2 concentra a maior taxa de anomalias, reforçando a interpretação de que ele é um grupo pequeno, mais afastado e menos denso.

## Principais conclusões

* A demanda se concentra fortemente no fim da tarde e início da noite.
* O pico da amostra ocorre às 17h.
* As variáveis `Lat`, `Lon`, `Hour` e `Day` carregam informações complementares.
* Não houve redundância forte entre os atributos numéricos.
* O PCA não reduziu a dimensionalidade de forma satisfatória sem perda relevante.
* O K-Means com `K=4` foi a solução final por equilíbrio entre qualidade, estabilidade e interpretação.
* Os clusters finais combinam informação espacial e temporal.
* A mineração de padrões mostrou associações fortes entre clusters, períodos do dia e faixas do mês.
* A detecção de anomalias destacou principalmente o cluster 2.

## Como executar

Crie e ative um ambiente virtual a partir da pasta principal do projeto:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

Inicie o Jupyter:

```bash
jupyter notebook
```

Execute os notebooks nesta ordem:

1. `etapa1/etapa1cd2.ipynb`
2. `etapa2/etapa2cd2.ipynb`
3. `etapa3/etapa3cd2.ipynb`
4. `visualizacao/visualizacao.ipynb`
5. `etapa4/etapa4cd2.ipynb`

## Dependências principais

As dependências estão listadas em:

```text
requirements.txt
```

Principais bibliotecas:

* pandas;
* numpy;
* scipy;
* scikit-learn;
* matplotlib;
* folium;
* mlxtend;
* jupyter.
