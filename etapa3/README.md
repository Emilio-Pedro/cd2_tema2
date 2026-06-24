# Etapa 3 — Aplicação de Algoritmos

A Etapa 3 foi desenvolvida em um notebook separado:

* `etapa3/etapa3cd2.ipynb`

Esse notebook utiliza diretamente o arquivo `ubernymaio14_tratado.csv`, produzido
pela Etapa 1, e usa as conclusões da Etapa 2 para orientar a escolha dos
atributos e a interpretação dos agrupamentos.

## O que foi feito

### 1. Carregamento e preparação dos dados

Foram carregados **19.220 registros** e usadas as variáveis `Lat`, `Lon`,
`Hour` e `Day` para a clusterização.

Como `Lat`, `Lon` e `Hour` já estavam normalizadas pela Etapa 1, mas `Day`
permanecia na escala de 1 a 16, foi aplicado `StandardScaler` antes dos
algoritmos baseados em distância. Essa padronização evita que `Day` tenha peso
excessivo apenas por estar em uma escala numérica maior.

### 2. Escolha da quantidade de clusters

Foi avaliado o K-Means com valores de `K` entre 2 e 10. Para cada valor, foram
analisados:

* inércia, pelo método do cotovelo;
* silhouette;
* Davies-Bouldin;
* Calinski-Harabasz;
* estabilidade entre diferentes sementes.

O valor escolhido foi **K = 4**.

Na execução registrada, `K=4` obteve silhouette de aproximadamente **0,2568**,
Davies-Bouldin de **1,1588** e Calinski-Harabasz de **4.631,1208**.

Embora `K=5` tenha apresentado silhouette ligeiramente maior, com **0,2679**,
sua estabilidade caiu abaixo do critério adotado, com ARI mínimo de
aproximadamente **0,7623**. O valor `K=7` também foi estável, mas aumentava a
quantidade de grupos por um ganho pequeno de silhouette. Assim, `K=4` foi o
menor valor que atendeu simultaneamente aos critérios de silhouette maior ou
igual a **0,25** e ARI maior que **0,8**.

### 3. Comparação de métricas de distância

Os rótulos do K-Means foram avaliados com diferentes métricas de
similaridade/dissimilaridade:

* Euclidiana: silhouette **0,2568**;
* Manhattan: silhouette **0,2415**;
* Cosine: silhouette **0,4130**.

A métrica Cosine apresentou maior silhouette ao avaliar os rótulos já
encontrados, mas o K-Means otimiza diretamente a inércia Euclidiana. Por isso,
a interpretação dos resultados considera essa diferença entre a métrica usada
para treinar o modelo e a métrica usada para avaliar a separação.

### 4. Teste de diferentes algoritmos

Além do K-Means, foram testados Agglomerative Clustering e DBSCAN. Para manter o
notebook executável em máquinas comuns, os algoritmos mais custosos foram
avaliados em uma amostra reprodutível de até **6.000 registros**.

No resumo comparativo da amostra:

* Agglomerative Average com distância Euclidiana obteve silhouette **0,7057**;
* Agglomerative Average com distância Manhattan obteve silhouette **0,7018**;
* DBSCAN com Manhattan obteve silhouette **0,5028**, com **3 clusters** e cerca
  de **3,28%** de ruído;
* DBSCAN com Euclidiana obteve silhouette **0,4751**, com **3 clusters** e
  cerca de **3,25%** de ruído;
* DBSCAN com Cosine teve desempenho inferior, com silhouette negativa na melhor
  configuração avaliada.

O K-Means foi mantido como resultado final por ter sido executado no conjunto
completo, apresentar alta estabilidade entre sementes e gerar uma solução mais
direta para exportação e visualização posterior.

### 5. Estabilidade do K-Means

A estabilidade foi avaliada executando o K-Means com as sementes
`0`, `1`, `2`, `42` e `100`.

Foram calculadas as métricas Adjusted Rand Index e Normalized Mutual
Information entre todos os pares de execuções. O resultado foi:

* ARI mínimo: **0,996**;
* NMI mínimo: **0,992**.

Como os dois valores ficaram muito próximos de 1, conclui-se que o agrupamento
final é estável em relação à inicialização do K-Means.

### 6. Exportação dos clusters

O notebook gera o arquivo:

```text
etapa3/clusters_etapa3.csv
```

Esse arquivo contém as colunas originais tratadas, o rótulo `cluster_kmeans` e,
quando o arquivo original está disponível, também `Lat_original` e
`Lon_original`, permitindo a criação de mapas geográficos reais na etapa de
visualização.

A distribuição final dos clusters foi:

| Cluster | Registros | Percentual |
| ------- | --------- | ---------- |
| 0 | 6.816 | 35,46% |
| 1 | 6.590 | 34,29% |
| 2 | 589 | 3,06% |
| 3 | 5.225 | 27,19% |

O cluster 2 é o menor grupo e possui centro geográfico mais afastado dos demais,
enquanto os clusters 0, 1 e 3 concentram a maior parte dos registros e se
diferenciam principalmente pela combinação entre localização, horário e dia.

### 7. Conclusões

A Etapa 3 aplicou e comparou algoritmos de agrupamento sobre os dados tratados.
O K-Means com `K=4` foi escolhido como solução final por equilibrar qualidade,
estabilidade e simplicidade.

Os resultados indicam que a mobilidade registrada não forma apenas grupos
espaciais. A inclusão de `Hour` e `Day` faz com que os clusters também reflitam
diferenças temporais, confirmando a conclusão da Etapa 2 de que a definição de
proximidade muda quando atributos temporais são considerados.

Todos os resultados, gráficos, métricas e conclusões estão incorporados no
próprio notebook.

---