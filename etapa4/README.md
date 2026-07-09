# Etapa 4 - Padroes Frequentes, Anomalias e Conclusoes

A Etapa 4 foi desenvolvida em um notebook separado:

* `etapa4/etapa4cd2.ipynb`

Esse notebook utiliza o arquivo `etapa3/clusters_etapa3.csv`, gerado na Etapa 3,
e complementa a analise com mineracao de padroes frequentes, deteccao de
anomalias por distancia e interpretacao final dos agrupamentos.

## O que foi feito

### 1. Carregamento dos dados da Etapa 3

Foram carregados **19.220 registros**, contendo as variaveis tratadas
`Lat`, `Lon`, `Hour`, `Day`, os rotulos `cluster_kmeans` e as coordenadas
originais `Lat_original` e `Lon_original`.

A coluna `Base` possui apenas o valor `B02512` em todos os registros. Por isso,
ela foi mantida como informacao de origem dos dados, mas nao foi usada como
variavel discriminante nos padroes.

### 2. Mineracao de padroes frequentes

Foi usado o algoritmo **Apriori** por meio da biblioteca `mlxtend`, deixando o
codigo mais curto e mais facil de acompanhar no notebook.

As transacoes foram formadas a partir de itens categoricos derivados das
variaveis do projeto:

* `cluster_kmeans`;
* periodo do dia, derivado de `Hour`;
* faixa do dia do mes, derivada de `Day`;
* faixa de latitude;
* faixa de longitude.

Foram usados:

* suporte minimo: **8%**;
* confianca minima para regras: **55%**;
* lift minimo para regras interpretaveis: **1,05**;
* tamanho maximo dos itemsets: **3**.

Principais padroes encontrados:

| Padrao | Suporte |
| ------ | ------- |
| `cluster=0 + faixa_dia=11-16` | 25,38% |
| `cluster=1 + faixa_dia=01-05` | 21,83% |
| `cluster=3 + periodo=manha` | 19,32% |
| `cluster=0 + faixa_dia=11-16 + periodo=tarde` | 13,32% |
| `cluster=1 + faixa_dia=01-05 + periodo=tarde` | 10,47% |
| `cluster=1 + faixa_dia=01-05 + periodo=noite` | 10,42% |

Regras de associacao relevantes:

| Regra | Suporte | Confianca | Lift |
| ----- | ------- | --------- | ---- |
| `periodo=manha -> cluster=3` | 19,32% | 83,25% | 3,06 |
| `faixa_dia=01-05 + periodo=tarde -> cluster=1` | 10,47% | 95,49% | 2,79 |
| `faixa_dia=01-05 + periodo=noite -> cluster=1` | 10,42% | 95,38% | 2,78 |
| `faixa_dia=11-16 + periodo=tarde -> cluster=0` | 13,32% | 96,93% | 2,73 |
| `faixa_dia=11-16 + periodo=noite -> cluster=0` | 9,64% | 96,11% | 2,71 |

Esses resultados mostram que os clusters nao representam apenas regioes
geograficas. Eles tambem capturam recortes temporais fortes.

### 3. Deteccao de anomalias por distancia

A deteccao de outliers foi feita no mesmo espaco usado para a clusterizacao:

```text
Lat, Lon, Hour, Day
```

As variaveis foram padronizadas e, para cada registro, foi calculada a distancia
media aos **5 vizinhos mais proximos**. Foram marcados como anomalias os pontos
acima do percentil **97,5%** dessa distancia.

Resultado:

* anomalias detectadas: **481 registros**;
* percentual de anomalias: **2,50%**;
* limiar da distancia media aos 5 vizinhos: **0,7116**.

Distribuicao por cluster:

| Cluster | Anomalias | Percentual dentro do cluster |
| ------- | --------- | ---------------------------- |
| 0 | 81 | 1,19% |
| 1 | 90 | 1,37% |
| 2 | 160 | 27,16% |
| 3 | 150 | 2,87% |

O cluster 2 concentrou a maior taxa de anomalias, o que reforca a interpretacao
da Etapa 3: ele representa um grupo menor e mais afastado espacialmente.

### 4. Interpretacao dos clusters

| Cluster | Registros | Interpretacao principal |
| ------- | --------- | ----------------------- |
| 0 | 6.816 | Concentrado nos dias 11 a 16, principalmente tarde e noite. |
| 1 | 6.590 | Concentrado nos dias 1 a 5, principalmente tarde e noite. |
| 2 | 589 | Grupo pequeno, mais afastado geograficamente e com maior proporcao de anomalias. |
| 3 | 5.225 | Fortemente associado ao periodo da manha, com pico entre 6h e 8h. |

### 5. Justificativas exigidas

**Escolha das variaveis:** `Lat`, `Lon`, `Hour` e `Day` foram mantidas porque
representam a dimensao espaco-temporal da mobilidade. `Base` foi descartada da
modelagem por ser constante no conjunto analisado.

**Escolha das metricas:** suporte, confianca e lift foram usados para avaliar
padroes frequentes porque medem, respectivamente, frequencia, forca condicional
e relevancia acima do acaso. Para anomalias, foi usada distancia Euclidiana apos
padronizacao, pois a analise mede isolamento no mesmo espaco numerico usado nos
clusters.

**Escolha dos algoritmos:** Apriori foi escolhido pela interpretabilidade e pelo
baixo numero de itens categoricos gerados. A implementacao via `mlxtend` foi
usada por ser uma biblioteca conhecida e simples para esse tipo de tarefa. A
deteccao por distancia foi escolhida porque o objetivo era encontrar pontos
isolados em relacao a registros proximos no espaco-tempo.

**Escolha do numero de clusters:** a Etapa 4 reaproveita `K=4`, definido na
Etapa 3 por equilibrar silhouette, Davies-Bouldin, Calinski-Harabasz,
estabilidade entre sementes e simplicidade interpretativa.

### 6. Implicacoes e limitacoes

Os resultados indicam que a demanda analisada possui padroes temporais bem
marcados: manha associada ao cluster 3, inicio do periodo analisado associado ao
cluster 1 e fim do periodo associado ao cluster 0. O cluster 2 sugere
deslocamentos mais incomuns ou regioes menos densas.

As principais limitacoes sao:

* o recorte possui apenas registros da base `B02512`;
* o arquivo tratado cobre os dias 1 a 16 de maio de 2014, nao o mes completo;
* os dados representam pontos de coleta, sem informacao de destino ou duracao;
* anomalias por distancia nao significam necessariamente erro: podem representar
  viagens reais para regioes mais distantes ou horarios menos comuns.

## Arquivos gerados

Ao executar o notebook, sao gerados:

```text
etapa4/padroes_frequentes_etapa4.csv
etapa4/regras_associacao_etapa4.csv
etapa4/anomalias_etapa4.csv
```
