# Relatório Técnico — Descoberta de Conhecimento em Bases de Dados (KDD)
**Projeto:** Análise de Inadimplência e Risco de Crédito (Home Credit)
**Consultor(Aluno):** Gabriel Gomes dos Santos
**Matrícula:** 72400515

---

## 1. Seleção do Dataset e Compreensão do Problema
O objetivo desta consultoria foi identificar padrões preditivos de inadimplência (default) para otimizar as esteiras de concessão de empréstimos. 

Trabalhar com indivíduos desbancarizados ou com histórico de crédito insuficiente impõe um risco severo para as instituições financeiras. Optou-se pela base `application_train.csv` (originária do *Home Credit Default Risk* via Kaggle). A tabela selecionada atendeu perfeitamente ao requisito de volumetria do escopo, contendo 307.511 registros e 122 variáveis explicativas focadas no perfil socioeconômico, histórico profissional e capacidade de endividamento dos tomadores. A variável dependente (`TARGET`) é um rótulo binário onde 1 indica dificuldade estrutural no pagamento.

## 2. Ingestão e Pré-processamento Distribuído
Como exigido pelo ecossistema de Big Data, o processamento analítico foi centralizado no motor do Apache Spark. A ingestão foi feita preservando a inferência nativa de schema do PySpark.

O pipeline de limpeza revelou um desafio clássico de tabelas bancárias: a altíssima concentração de valores ausentes (Nulos). Para mitigar o enviesamento dos modelos posteriores, foi implementada a seguinte lógica de pré-processamento (ETL):
* **Filtro de Ruído:** O processamento distribuído varreu a tabela e eliminou sumariamente colunas com mais de 50% de dados ausentes, uma vez que imputar a maioria dos registros geraria informações sintéticas irretreais.
* **Tipagem e Imputação:** Nas colunas retidas, os nulos numéricos (como tempo de emprego) foram normalizados para 0, enquanto campos descritivos vazios (como ocupação) foram segregados sob a categoria "Nao_Informado".
* **Sanitização:** Finalizamos o bloco garantindo a unicidade dos dados via remoção de linhas duplicadas, entregando um DataFrame enxuto para as fases de exploração e ML.

## 3. Mineração Descritiva e Análise Exploratória (Spark SQL)
Registramos a tabela tratada como uma view em memória e executamos queries SQL para testar cinco hipóteses gerenciais. A extração dos insights ocorreu com forte apelo visual via Matplotlib e Seaborn (após amostragem pandas das tabelas agregadas), destacando os seguintes achados:

1. **Risco de Gênero e Escolaridade:** Homens apresentam taxas históricas de inadimplência mais acentuadas que mulheres. Simultaneamente, o nível superior atua como fator isolador de risco, enquanto o nível médio concentra as quebras de contrato.
2. **Dependentes Financeiros:** Observou-se uma correlação positiva clara entre o aumento da quantidade de dependentes na família e a dificuldade sistêmica de honrar os pagamentos, reduzindo a liquidez doméstica.
3. **Fator Lastro (Bens):** Clientes completamente descapitalizados (sem automóvel ou imóvel próprio) encabeçam a lista de calotes. O modelo demonstrou que a posse de bens materiais atua como uma âncora de segurança para a liquidação das dívidas.

## 4. Modelagem Preditiva (Supervisionada)
O problema foi estruturado como uma Classificação Binária. Para treinar a máquina preditiva, montamos um pipeline (MLlib) com transformadores categóricos (`StringIndexer`) agrupando as features num vetor denso (`VectorAssembler`). Utilizamos separação temporal padrão (80% treino / 20% teste).

Foram treinados dois algoritmos concorrentes:
* **Baseline (Logistic Regression):** Alcançou Área ROC de 0.6180.
* **Ensemble (Random Forest):** Entregou Área ROC de 0.6003.

**Decisão Metodológica:** A regressão linear simples operou melhor sobre as features desta amostra. Diferente de bases artificiais padronizadas, lidar com bases financeiras desbalanceadas exige cautela, e o modelo logístico provou sofrer menos de overfitting na fase de generalização.

## 5. Segmentação Descritiva (Clusterização)
Com as previsões consolidadas, partimos para a criação de "esteiras de análise" automatizadas usando o `K-Means`.
Foi aplicado um normalizador (`StandardScaler`) antes do treinamento para impedir que a grandeza de colunas milionárias ofuscasse o peso da idade. 

O número de grupos ($k=3$) foi validado através do *Silhouette Score*, resultando em três personas corporativas:
* **Cluster 0:** Baixa renda cruzada, ticket de crédito reduzido, perfil imaturo profissionalmente.
* **Cluster 1:** O grupo do trabalhador intermediário (maior volume e concentração no meio da pirâmide).
* **Cluster 2:** Alta Renda e limites de crédito elevados, consolidado através do tempo prolongado em vínculo empregatício e maturidade de idade.

## 6. Conclusões Práticas e Próximos Passos
O ciclo KDD transformou 160 MB de dados brutos e ruidosos em uma política acionável.

Com base nos resultados, o gestor da carteira financeira deve aplicar duas medidas imediatas: 
1. Submeter tomadores do "Cluster 0" a travas automáticas e exigência de garantias reais, dada a correlação do risco pela ausência de lastro imobiliário identificada no bloco SQL. 
2. Redirecionar os limites de crédito do modelo para perfis maduros e acadêmicos.

**Limitações do Estudo:** O desbalanceamento severo da variável alvo limitou a acurácia dos modelos arbóreos. Como passo futuro, recomenda-se aplicar técnicas estatísticas de oversampling distribuído (como SMOTE adaptado ao PySpark) na etapa de engenharia de features para alavancar a métrica de AUC-ROC em modelos mais complexos.
