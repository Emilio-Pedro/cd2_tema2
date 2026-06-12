# Etapa 1 — Coleta e Pré-processamento

## 1. Carregamento dos Dados

Inicialmente foi realizado o carregamento do dataset CSV utilizando a biblioteca Pandas.

Exemplo:

```python
import pandas as pd

df = pd.read_csv("../ubernymaio14.csv", nrows=20000)
```

Foi utilizada uma amostra de 20.000 registros para facilitar o processamento e análise inicial dos dados.

---

## 2. Verificação de Valores Faltantes

Foi realizada uma análise para verificar a existência de valores nulos no dataset:

```python
df.isnull().sum()
```

Resultado:

* Não foram encontrados valores faltantes.

---

## 3. Verificação e Remoção de Duplicados

Foi realizada a verificação de registros duplicados:

```python
df.duplicated().sum()
```

Em seguida, os dados duplicados foram removidos:

```python
df = df.drop_duplicates()
```

Esse processo ajuda a evitar inconsistências e repetições durante as análises.

---

## 4. Transformações Temporais

A coluna `Date/Time` foi convertida para o formato datetime:

```python
df['Date/Time'] = pd.to_datetime(df['Date/Time'])
```

Após isso, foram criados novos atributos temporais:

```python
df['Hour'] = df['Date/Time'].dt.hour
df['Day'] = df['Date/Time'].dt.day
```

Esses atributos permitem analisar:

* horários de pico
* comportamento temporal das corridas
* padrões urbanos ao longo do dia

---

## 5. Análise de Outliers

Foi realizada análise visual utilizando boxplots:

```python
plt.boxplot(df['Lat'])
```

Os possíveis outliers encontrados permaneceram dentro dos limites esperados para dados geográficos da cidade de Nova York.

Dessa forma, não foi necessária a remoção de outliers.

---

## 6. Normalização dos Dados

Foi utilizado o método `MinMaxScaler` da biblioteca Scikit-Learn.

```python
from sklearn.preprocessing import MinMaxScaler
```

Aplicação:

```python
scaler = MinMaxScaler()

df[['Lat', 'Lon', 'Hour']] = scaler.fit_transform(
    df[['Lat', 'Lon', 'Hour']]
)
```

---

# Por que utilizamos MinMaxScaler?

O método MinMaxScaler foi escolhido porque o projeto trabalha com:

* coordenadas geográficas
* distância espacial
* análise de agrupamentos

Esse método normaliza os dados para um intervalo entre 0 e 1, preservando a proporcionalidade entre os atributos.

Isso é importante para algoritmos baseados em distância, como:

* K-Means
* DBSCAN

Além disso, o MinMaxScaler funciona muito bem com:

* latitude
* longitude
* dados espaciais

---

# Dataset Tratado

Após o pré-processamento, o dataset passou a conter as seguintes colunas:

* `Date/Time`
* `Lat`
* `Lon`
* `Base`
* `Hour`
* `Day`

---

