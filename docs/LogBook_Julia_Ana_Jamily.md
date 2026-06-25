## Tarefas realizadas — Jamily:

- Criou o notebook de modelagem (`Modelagem_Jamily.ipynb`) do zero, cobrindo toda a etapa M do AGEMC:
  1. Limpeza dos dados: remoção de duplicatas e normalização de `Amount` com `RobustScaler`.
  2. Implementação do benchmarking comparando três modelos: Regressão Logística, Random Forest e XGBoost com Cost-Sensitive Learning (`scale_pos_weight`).
  3. Escolha e justificativa do XGBoost como modelo final pela métrica PR-AUC.
  4. Implementação da otimização de hiperparâmetros com **Optuna** (busca bayesiana), focando exclusivamente em PR-AUC.
  5. Uso de `GroupShuffleSplit` para separar conjunto de validação durante a busca, mantendo o teste intocado.

---

## Tarefas realizadas — Ana Clara:

- **Correção de vazamento de dados na otimização** identificou que o Optuna estava otimizando e avaliando no mesmo conjunto de teste, inflando o PR-AUC artificialmente. Corrigiu separando um conjunto de validação a partir do treino via `GroupShuffleSplit`. Resultado honesto após correção: ganho real de −0,0004 no teste (antes aparecia +0,0023).

- **Validação robusta e teste de robustez** substituiu a validação única por `StratifiedGroupKFold` dentro do Optuna para uma estimativa mais estável do PR-AUC por trial. Adicionou teste de robustez com múltiplas seeds para verificar se o ganho do modelo otimizado é real ou ruído — conclusão: ganho dentro do ruído estatístico.

- **Refatoração e padronização do pipeline** removeu o experimento `GroupSplit` (manter duplicatas gerava artefatos e upweighting indesejado). O Optuna passou a rodar sobre o split sem duplicatas com `StratifiedKFold`. Reescreveu a análise crítica da seção 3 com conclusão honesta: o tuning não supera um XGBoost bem configurado com bons parâmetros manuais.

---

## Tarefas realizadas — Júlia:

- **Correção de data leakage no `RobustScaler`:** o scaler estava sendo ajustado sobre o dataset completo antes do split, contaminando o conjunto de teste com informações da mediana e IQR globais. Corrigido em três pontos do notebook:
  - `cell-5`: remoção do escalonamento pré-split; `Amount` passa para o split na escala original.
  - `cell-8`: `RobustScaler` ajustado (`fit`) apenas no treino, aplicado (`transform`) ao teste.
  - `cell-15` (teste de robustez): cada seed ganha seu próprio scaler ajustado no treino daquela divisão.

- **Correção da justificativa de normalização no EDA (`EDA_CreditCardFraudDetection_Duarte.ipynb`):** o texto original afirmava que modelos de árvore podem ser prejudicados pela magnitude das features, o que é incorreto, pois árvores são invariantes a transformações monotônicas. Reescrito para identificar corretamente quais modelos precisam de escalonamento (Regressão Logística, SVM) e documentar que o escalonamento no EDA é apenas para fins de visualização, com link para o protocolo correto no notebook de modelagem.

- **Comparação empírica Cost-Sensitive vs. SMOTE:** adicionada Seção 2 no notebook de modelagem com comparação em 5 seeds independentes. Resultado: Cost-Sensitive (PR-AUC 0,811 ± 0,034) vs. SMOTE (0,804 ± 0,035) — diferença de +0,007, dentro do ruído. Conclusão documentada: abordagens equivalentes, Cost-Sensitive preferível por não gerar dados sintéticos e ter pipeline mais simples.

- **Calibração de probabilidades — Seção 4:** adicionada ao final do notebook de modelagem.
  - Problema identificado: com `scale_pos_weight ≈ 600`, mais de 50% das fraudes recebem score 1,000 — o modelo não diferencia graus de suspeita.
  - Implementado `CalibratedClassifierCV` com Platt Scaling (`sigmoid`, `cv=3`) e Isotonic Regression, com diagrama de confiabilidade (*reliability diagram*) e tabela comparativa.
  - Resultado: Platt Scaling reduz alertas diários de 84 → 74 (−12%) e aumenta precisão de 90,5% → 97,3%, com queda mínima em PR-AUC (0,8199 → 0,8151).
  - Recomendação: Platt Scaling — mais estável com poucos exemplos positivos que Isotonic.

- **Stage C — notebook `Communicate.ipynb`:** criado notebook de comunicação dos processo todo

- **README atualizado:** sub-questões analíticas, seção de originalidade com resultados empíricos, pipeline de pré-processamento corrigido, tabela de modelos com PR-AUC real, seção de métricas com PR-AUC como principal e Accuracy marcada como não-usada
