# Consultoria KDD: Análise de Risco de Crédito e Inadimplência

Projeto prático desenvolvido para a disciplina de **Ciência de Dados II**, estruturado segundo o processo de Descoberta de Conhecimento em Bases de Dados (KDD — *Knowledge Discovery in Databases*).

---

## 👤 Integrante / Identificação
* **Nome:** Gabriel Gomes dos Santos
* **Matrícula:** 72400515
* **Curso/Disciplina:** Ciência de Dados II
* **Docente:** Prof. Romes

---

## 🎯 Contexto e Objetivo do Projeto
O projeto simula a atuação de uma consultoria de Ciência de Dados contratada para avaliar uma carteira de tomadores de crédito com histórico bancário insuficiente. 

O objetivo principal é identificar padrões comportamentais e preditores de inadimplência (variável `TARGET`), percorrendo desde a ingestão e tratamento distribuído em larga escala até a entrega de métricas executivas e perfis descritivos de tomadores.

### Pipeline KDD Implementado:
1. **Seleção e Ingestão:** Carga e validação de schema do dataset bruto com Apache Spark.
2. **Pré-processamento (ETL):** Descarte de atributos com >50% de dados faltantes, imputação estratégica de valores nulos e eliminação de duplicatas.
3. **Análise Exploratória (Spark SQL):** Investigação de 5 hipóteses críticas de negócio através de consultas estruturadas e visualizações executivas.
4. **Modelagem Preditiva:** Treinamento supervisionado com Spark MLlib (Baseline Logístico vs. Random Forest).
5. **Modelagem Descritiva:** Segmentação de tomadores de crédito via K-Means e avaliação do número ideal de grupos pelo método Silhouette.
6. **Interpretação e Conclusões:** Mapeamento de regras de concessão, criação de esteiras de aprovação e recomendações gerenciais consolidadas.

---

## 📦 Fonte dos Dados
* **Dataset Original:** [Home Credit Default Risk (Kaggle)](https://www.kaggle.com/c/home-credit-default-risk/data)
* **Tabela Utilizada:** `application_train.csv` (307.511 registros e 122 colunas brutas).

---

## 🛠️ Tecnologias e Ambiente
* **Linguagem:** Python 3.10+
* **Processamento Distribuído:** Apache Spark (PySpark DataFrames, Spark SQL e Spark MLlib)
* **Visualização:** Matplotlib e Seaborn
* **Ambiente de Desenvolvimento:** Google Colaboratory

---

## 🚀 Instruções de Execução (Reprodutibilidade)
1. Faça o clone ou download deste repositório.
2. Abra o arquivo `Sistematizacao_RiscoCredito.ipynb` no Google Colab.
3. Faça o download do arquivo `application_train.csv` na página oficial do Kaggle.
4. Salve o CSV no seu Google Drive (na pasta `/Sistematizacao_CD2/application_train.csv`) ou ajuste o caminho do arquivo na célula de ingestão.
5. Execute as células sequencialmente (`Ambiente de Execução > Executar tudo` ou `Ctrl + F9`).
