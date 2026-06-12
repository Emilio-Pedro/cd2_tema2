# Etapa 2 — Análise Exploratória de Dados

A Etapa 2 foi desenvolvida em um notebook separado:

* `etapa2/etapa2cd2.ipynb`

Esse notebook utiliza diretamente o arquivo `ubernymaio14_tratado.csv`, produzido
pela Etapa 1.

## O que foi feito

### 1. Carregamento e validação dos dados

Foram carregados os dados tratados e verificada sua qualidade antes da análise.
O conjunto possui **19.220 registros**, sem valores ausentes e sem duplicatas.
As variáveis numéricas analisadas foram `Lat`, `Lon`, `Hour` e `Day`.

### 2. Estatísticas descritivas e distribuições

Foram calculadas média, desvio padrão, mínimo, máximo, quartis, mediana,
assimetria e quantidade de possíveis outliers pelo critério de `1,5 × IQR`.
Também foram produzidos:

* histogramas e boxplots das variáveis numéricas;
* gráfico da distribuição espacial das corridas;
* gráfico e tabela da quantidade de corridas por base.

Foram identificados **855 possíveis outliers em `Lat`** e **1.856 em `Lon`**.
Esses registros foram mantidos, pois podem representar corridas reais em regiões
mais afastadas.

### 3. Correlações e seleção preliminar de atributos

Foram calculadas as matrizes de correlação de Pearson e Spearman. A maior
correlação linear ocorreu entre `Lat` e `Lon`, com Pearson de aproximadamente
**0,147**. Para Spearman, a correlação entre essas variáveis foi de
aproximadamente **0,496**.

Nenhum par de variáveis atingiu o limiar de redundância definido em
`|correlação| ≥ 0,80`. Portanto, todas as variáveis numéricas foram mantidas.

### 4. Análise de Componentes Principais

Antes da aplicação do PCA, todas as variáveis foram padronizadas para evitar que
`Day`, cuja escala era diferente, tivesse influência excessiva nos resultados.

As duas primeiras componentes principais explicaram aproximadamente **54,58%**
da variância. Para preservar ao menos **90%**, foram necessárias as quatro
componentes. Assim, o PCA não proporcionou uma redução dimensional relevante
sem perda de informação, mas a projeção em duas dimensões foi utilizada para
visualização exploratória.

### 5. Impacto das representações na proximidade

A vizinhança calculada apenas com `Lat` e `Lon` foi usada como referência e
comparada com os dados completos padronizados, o PCA com quatro componentes e o
PCA em duas dimensões. Foram avaliadas as métricas sobreposição dos 10 vizinhos
mais próximos, Jaccard médio e correlação de Spearman entre distâncias.

Os dados completos padronizados preservaram aproximadamente **3,1%** dos dez
vizinhos espaciais originais, enquanto o PCA em duas dimensões preservou cerca
de **2,0%**. Isso mostra que a inclusão das variáveis temporais altera
significativamente o conceito de proximidade entre os registros.

### 6. Conclusões

A análise identificou concentração espacial das corridas, variações de demanda
ao longo do dia e baixa redundância entre os atributos. Para agrupamentos
puramente espaciais, recomenda-se utilizar `Lat` e `Lon`. Para análises
espaçotemporais, `Hour` e `Day` também devem ser incluídos, com pesos definidos
de acordo com o objetivo da análise.

Todos os resultados, gráficos e conclusões estão incorporados no próprio notebook.

---
