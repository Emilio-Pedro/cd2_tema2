# Tema 2 – Mobilidade Urbana Inteligente

## Integrantes

* Pedro Emilio Castro Lemos
* Matheus Gualter Silva Resende
* João Vitor Dantas Barbosa
* Paulo Daniel Forti da Fonseca
* Pedro Henrique Lopes Duarte

---

# Etapa 2 — Análise Exploratória de Dados

A Etapa 2 foi desenvolvida em um notebook separado:

* `etapa2/etapa2_eda.ipynb`

Esse notebook utiliza diretamente o arquivo `ubernymaio14_tratado.csv`, produzido
pela Etapa 1. Ele contém:

* estatísticas descritivas
* histogramas e boxplots
* matrizes de correlação de Pearson e Spearman
* identificação de variáveis altamente correlacionadas
* seleção preliminar de atributos
* PCA e análise da variância explicada
* visualização bidimensional
* análise do impacto das representações na proximidade

Todos os resultados, gráficos e conclusões estão incorporados no próprio notebook.

---

# Como Executar o Projeto

## 1. Instale as dependências

```bash
python3 -m pip install jupyter pandas numpy scipy matplotlib scikit-learn
```

## 2. Inicie o Jupyter

Execute o comando a partir da pasta principal do projeto:

```bash
jupyter notebook
```

## 3. Execute a Etapa 1

Abra `etapa1/etapa1cd2.ipynb` e execute as células em ordem.

Essa etapa gera o arquivo:

```text
ubernymaio14_tratado.csv
```

## 4. Execute a Etapa 2

Abra `etapa2/etapa2_eda.ipynb` e execute as células em ordem.

A Etapa 2 deve permanecer em um notebook separado, pois representa uma fase diferente
do projeto, mas utiliza como entrada os dados tratados produzidos pela Etapa 1.

---
