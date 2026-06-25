# Detecção de anomalias em transações financeiras

**Trabalho Final de Pensamento Analítico de Dados**  
**Professor: Fernando M. Federson**

---

**Grupo: Ana Clara Lowe, Bruno Barros, Gustavo Duarte, Jamily Vieira e Júlia Souza**

---

## Visão geral

Este projeto é inspirado no projeto de referência **"Credit Card Fraud Detection"** (Rashmi, CBAP — Medium, 2024), seguindo a mesma estrutura metodológica **AGEMC** (Ask → Get → Explore → Model → Communicate).

O dataset utilizado é [Credit Card Fraud Detection](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud) do Kaggle: 284.807 transações europeias de setembro de 2013, com apenas 492 fraudes (≈ 0,17%), representando um problema altamente desbalanceado.

---

## Pergunta (ask)

> **É possível detectar transações fraudulentas em cartões de crédito com alta precisão, mesmo diante de um conjunto de dados extremamente desbalanceado?**

Como sub-questões analíticas:
- Cost-Sensitive Learning supera SMOTE neste contexto — e por qual margem?
- Qual modelo oferece o melhor equilíbrio entre precisão e recall para detecção de fraude?
- Calibrar as probabilidades do modelo melhora a experiência operacional do analista de fraude?

---

## Nossa originalidade

Nosso grupo manteve a pergunta do AGEMC e o mesmo dataset da referência, mas aprofundou a análise exploratória e modelou os dados de forma diferente — com resultados empíricos que sustentam cada escolha.

- **`Time` → `Hour`:** em vez de descartar a variável temporal, transformamos segundos em hora do dia (0–23), capturando o padrão circadiano das fraudes.
- **Cost-Sensitive Learning em vez de SMOTE:** testamos as duas abordagens em 5 divisões independentes e confirmamos que são equivalentes em PR-AUC (~0.811 vs ~0.804), com leve vantagem para Cost-Sensitive — que não gera dados sintéticos e é mais simples de manter.
- **`RobustScaler` aplicado após o split:** a normalização do `Amount` é ajustada exclusivamente no conjunto de treino, eliminando vazamento de dados (*data leakage*) em direção ao teste.
- **Otimização com Optuna (PR-AUC):** busca bayesiana de hiperparâmetros com validação cruzada estratificada dentro do treino — teste intocado durante toda a busca.
- **Calibração com Platt Scaling:** os scores brutos do XGBoost ficam comprimidos próximos de 1.0 com `scale_pos_weight` alto. Platt Scaling distribui as probabilidades corretamente, reduzindo alertas diários de 84 → 74 e aumentando a precisão de 90,5% → 97,3%.

---

## Metodologia AGEMC

```
A  →  Pergunta formulada + sub-questões sobre desbalanceamento e calibração
G  →  Obtenção e verificação de integridade do dataset (Kaggle, kagglehub)
E  →  EDA: distribuição de Amount, padrão circadiano, features mais discriminativas, t-SNE
M  →  Benchmarking → Cost-Sensitive XGBoost → Optuna → Robustez (5 seeds) → Calibração
C  →  Comunicação dos resultados em linguagem acessível, com pipeline de produção recomendado
```

---

## Etapas do Projeto

### 1. Get — Obtenção dos Dados
- Dataset: [Kaggle Credit Card Fraud Detection](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud)
- 284.807 transações · 30 features (V1–V28 via PCA, `Amount`, `Time`) · target: `Class`
- 492 fraudes (0,17%) → desbalanceamento severo
- Remoção de 1.081 duplicatas antes da modelagem

### 2. Explore — Análise Exploratória
- Verificação de nulos, duplicatas e distribuições gerais
- Distribuição de `Amount` por classe: mediana fraude ≈ €9 vs legítima ≈ €22 (hipótese de *card testing*)
- Padrão temporal: conversão de `Time` para `Hour`, análise de sazonalidade circadiana
- Features mais discriminativas: V14, V17, V12 (correlação negativa) e V4, V11 (correlação positiva)
- Visualização t-SNE: clusters de fraude separáveis no espaço de baixa dimensão

### 3. Model — Modelagem

**Pré-processamento (sem leakage):**
- `Time` → `Hour` (padrão circadiano); `Time` descartado
- `Amount` normalizado com `RobustScaler` **após** o split treino/teste
- Divisão estratificada 80/20

**Modelos comparados (benchmarking):**

| Modelo | PR-AUC |
|--------|--------|
| Regressão Logística | 0.694 |
| Random Forest | 0.805 |
| **XGBoost (Cost-Sensitive)** | **0.810** |

**Otimização e validação:**
- Optuna (20 trials, StratifiedKFold, PR-AUC): ganho de +0.0004 — dentro do ruído estatístico para este dataset
- Teste de robustez em 5 seeds: modelo estável (desvio-padrão ≈ ±0.034)
- Comparação empírica Cost-Sensitive vs. SMOTE: equivalentes em PR-AUC, CS preferido por simplicidade

**Calibração de probabilidades:**
- Platt Scaling: 74 alertas/dia, precisão 97,3%, Brier Score 0.000429
- Isotonic Regression: mais preciso, mas menos estável com poucos positivos
- Recomendação: **Platt Scaling**

### 4. Communicate — Comunicação dos Resultados
- Exposição dos resultados e considerações finais (`Communicate.ipynb`)

---

## Métricas de avaliação

| Métrica | Descrição |
|---|---|
| **PR-AUC** | Métrica principal — área sob a curva Precisão-Recall; robusta para classes desbalanceadas |
| **Precision** | De tudo que o modelo sinalizou como fraude, quanto era fraude de fato |
| **Recall** | De todas as fraudes reais, quantas o modelo detectou |
| **Brier Score** | Qualidade da calibração — penaliza probabilidades mal estimadas |
| **F1-Score** | Média harmônica entre Precision e Recall (limiar fixo) |
| **Accuracy** | Não usada como critério — enganosa em dados desbalanceados |

---

## Referência

> Rashmi, CBAP. *Credit Card Fraud Detection — A Data Science Project*. Medium, nov. 2024.  
> Dataset: ULB Machine Learning Group. *Credit Card Fraud Detection*. Kaggle, 2018.

---

O logbook detalhado dos membros está em [`docs/`](docs/).
