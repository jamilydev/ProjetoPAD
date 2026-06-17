# 17/06/2026

## Tarefas realizadas:

- Nós já entendemos a pergunta do AGEMC e fizemos o "get" do dataset, que foi o carregamento dos dados.

- Comecei então a fazer a análise exploratória dos dados (o "explore" do AGEMC) 
1. Vimos que o dataset está desbalanceado, então começamos a pensar em como lidar com isso. 
2. Foi identificado que a coluna 'Time' é relevante para identificar possíveis fraudes, então, ao invés de remover essa feature, decidimos mantê-la. 
3. Como a feature 'Time' está em segundos, foi necessário convertê-la para horas para que fizesse mais sentido.
4. Fizemos a visualização do t-SNE das transações fraudulentas e não fraudulentas, verificando que, mesmo com ruídos, as transações fraudulentas possuem uma tendência de se agruparem em certas regiões.
5. Fizemos também a normalização da coluna 'Amount' para que os valores ficassem entre 0 e 1.
6. Definimos os próximos passos para a modelagem dos dados:

    - **Divisão Estratificada**: Realizar o split entre treino e teste (80/20) garantindo que a proporção de fraudes seja mantida em ambos os sets.
    - **Cost-Sensitive Learning**: Em vez de balancear o dataset (o que pode remover informações valiosas das transações legítimas), utilizaremos o parâmetro `scale_pos_weight` no **XGBoost**. Isso forçará o modelo a penalizar severamente erros cometidos na classe minoritária (Fraude).
    - **Otimização com Optuna**: Utilizar busca de hiperparâmetros focada exclusivamente na métrica **PR-AUC** (Area Under the Precision-Recall Curve), que é mais robusta para datasets desbalanceados do que a acurácia ou a curva ROC.
    - **Calibração de Probabilidades**: Implementar **Platt Scaling** ou **Isotonic Regression** para calibrar as saídas do modelo, garantindo que as probabilidades estimadas reflitam o risco real para a operação de negócio.

- Finalizei a análise exploratória do dia e salvei os resultados no arquivo `docs/EDA_CreditCardFraudDetection_Duarte.ipynb`
- As visualizações devem ser executadas após a normalização da coluna 'Amount'. Portanto, na próxima vez executaremos as visualizações após a normalização da coluna 'Amount'.