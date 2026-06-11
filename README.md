# Detecção de Anomalias em Transações Financeiras

**Trabalho Final de Pensamento Analítico de Dados**  
**Professor: Fernando M. Federson**

---

**Grupo: Ana Clara Lowe, Bruno Barros, Gustavo Duarte, Jamily Vieira e Júlia Souza**

---

## Visão Geral

Este projeto é inspirado no projeto de referência **"Credit Card Fraud Detection"** (Rashmi, CBAP — Medium, 2024), seguindo a mesma estrutura metodológica **AGEMC** (Ask → Get → Explore → Model → Communicate).

O dataset utilizado é [Credit Card Fraud Detection](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud) do Kaggle: 284.807 transações europeias de setembro de 2013, com apenas 492 fraudes (≈ 0,17%), representando um problema altamente desbalanceado.

---

## Pergunta (Ask)

> **É possível detectar transações fraudulentas em cartões de crédito com alta precisão, mesmo diante de um conjunto de dados extremamente desbalanceado?**

Como sub-questões analíticas:
- Quais técnicas de balanceamento produzem melhores resultados neste contexto?
- Qual modelo oferece o melhor equilíbrio entre precisão e recall para detecção de fraude?
- Como o limiar de decisão (*threshold*) influencia o desempenho prático do modelo?

---

## ✨ Nossa Originalidade

A definir

---

## 📐 Metodologia AGEMC

```
A  →  Pergunta de negócio formulada
G  →  Obtenção e inspeção do dataset (Kaggle)
E  →  Análise exploratória: distribuições, correlações, desbalanceamento
M  →  Modelagem: pré-processamento, balanceamento (SMOTE), treinamento e threshold tuning
C  →  Comunicação dos resultados com visualizações e recomendação de negócio
```

---

## 🔬 Etapas do Projeto FMF

### 1. Get — Obtenção dos Dados
- Dataset: [Kaggle Credit Card Fraud Detection](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud)
- 284.807 transações · 30 features (V1–V28 via PCA, `Amount`, `Time`) · target: `Class`
- 492 fraudes (0,17%) → desbalanceamento severo

### 2. Explore — Análise Exploratória
- Verificação de valores nulos e distribuições
- Análise da feature `Amount` e `Time` em relação à classe
- Correlações entre features anonimizadas e o target
- Visualizações de desbalanceamento

### 3. Model — Modelagem
**Pré-processamento:**
- Normalização de `Amount` com `StandardScaler`
- Remoção de `Time` (seguindo referência)
- Divisão treino/teste estratificada (80/20)
- Balanceamento com **SMOTE** no conjunto de treino

**Modelos avaliados:**
- Regressão Logística
- Árvore de Decisão
- Random Forest
- XGBoost

### 4. Communicate — Comunicação dos Resultados
- Tabela comparativa de modelos com métricas padrão
- Curvas Precision × Recall por modelo
- Gráfico de custo estimado vs. threshold
- Recomendação final com justificativa de negócio
---

## 📊 Métricas de Avaliação

| Métrica | Descrição |
|---|---|
| **Accuracy** | Proporção de predições corretas (limitada em dados desbalanceados) |
| **Precision** | De tudo que o modelo apontou como fraude, quanto era fraude de fato |
| **Recall** | De todas as fraudes reais, quantas o modelo detectou |
| **F1-Score** | Média harmônica entre Precision e Recall |
| **ROC-AUC** | Capacidade de discriminação geral do modelo |
| **Custo estimado** | Métrica original — impacto financeiro por limiar de decisão |

---

## 📖 Referência

> Rashmi, CBAP. *Credit Card Fraud Detection — A Data Science Project*. Medium, nov. 2024.  
> Dataset: ULB Machine Learning Group. *Credit Card Fraud Detection*. Kaggle, 2018.

---

## 📌 Acompanhamento

O progresso do projeto é registrado via **commits no GitHub**, permitindo rastrear cada etapa do AGEMC conforme executada pelo grupo.
