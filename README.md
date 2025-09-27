# Detecção de Fraudes em Mobile Money - PaySim Dataset

## 📋 Sobre o Projeto

Este projeto faz parte da especialização em Machine Learning da FIAP e tem como objetivo desenvolver um sistema de detecção de fraudes para transações de Mobile Money utilizando o dataset PaySim. O PaySim é um simulador de pagamentos móveis baseado em uma amostra de transações reais extraídas de logs financeiros de uma implementação de mobile money.

## 🎯 Objetivos

- **Objetivo Principal**: Desenvolver um modelo de Machine Learning capaz de detectar fraudes em transações financeiras com alta precisão (≥90%) e recall satisfatório (≥80%)
- **Objetivo Secundário**: Superar a regra atual (`isFlaggedFraud`) que apresenta recall extremamente baixo (~0,2%)
- **Objetivo Operacional**: Criar um pipeline de detecção que possa ser implementado em ambiente de produção

## 📊 Dataset - PaySim

O dataset contém **2.770.409 transações** com as seguintes características:

### Tipos de Transação
- **CASH_IN**: Depósito de dinheiro
- **CASH_OUT**: Saque de dinheiro (⚠️ **associado a fraudes**)
- **DEBIT**: Débito em conta
- **PAYMENT**: Pagamento
- **TRANSFER**: Transferência (⚠️ **associado a fraudes**)

### Variáveis Principais
- `step`: Unidade de tempo (1 step = 1 hora, simulação de 30 dias = 744 steps)
- `type`: Tipo da transação
- `amount`: Valor da transação
- `nameOrig`: ID do cliente que originou a transação
- `oldbalanceOrg`: Saldo inicial da conta origem
- `newbalanceOrig`: Saldo final da conta origem
- `nameDest`: ID do cliente destinatário
- `oldbalanceDest`: Saldo inicial da conta destino
- `newbalanceDest`: Saldo final da conta destino
- `isFraud`: **Target** - Indica se a transação é fraudulenta
- `isFlaggedFraud`: Flag da regra atual de detecção

## 🔍 Principais Descobertas da EDA

### Problema da Regra Atual
- **Recall crítico**: Apenas 16 fraudes detectadas de 8.213 fraudes reais (~0,2%)
- **Cobertura insuficiente**: A regra atual não é adequada como gate de fraude

### Padrões de Fraude Identificados
- **Concentração por tipo**: Fraudes ocorrem predominantemente em `TRANSFER` e `CASH_OUT`
- **Padrão temporal**: Maioria das transações ocorre em step = 1 hora
- **Cadeias suspeitas**: Padrão `TRANSFER → CASH_OUT` para o mesmo destinatário

### Performance dos Modelos

#### Divisão Temporal
- **Treino**: 2.063.665 transações
- **Teste**: 706.744 transações

#### Métricas de Baseline

**Logistic Regression**
- ROC AUC: 0,9812
- **PR AUC: 0,6775** (métrica principal para classe desbalanceada)

**Random Forest @ threshold 0,5**
- Precision: 95,15%
- Recall: 83,62%
- F1-Score: 89,01%
- Matriz de Confusão: TN=702.067, FP=191, FN=735, TP=3.751

#### Ajuste de Threshold (Precision ≥ 90%)
- **Logistic Regression**: threshold ≈ 0,9961, recall ≈ 46,08%
- **Random Forest**: threshold ≈ 0,3093, recall ≈ 85,33% ✅

## 🚀 Roadmap de Desenvolvimento

### Fase 1: Consolidação do Baseline
- [x] Implementação de modelos baseline (LR, RF)
- [x] Definição de métricas (PR AUC como principal)
- [x] Ajuste de threshold para precision ≥ 90%
- [ ] Análise de erros por valor da transação
- [ ] Relatório dos top 20 falsos negativos de maior valor

### Fase 2: Feature Engineering Avançada
- [ ] **Deltas de balanço**: Inconsistências entre saldos
- [ ] **Relações com saldo**: Proporções amount/saldo
- [ ] **Janelas temporais**: Agregações por entidade (24h/48h/72h)
- [ ] **Padrões de cadeia**: Detecção TRANSFER → CASH_OUT
- [ ] **Sinais de rede**: Análise de grafos (opcional)

### Fase 3: Modelagem Avançada
- [ ] Teste de modelos (LightGBM, XGBoost, BalancedRandomForest)
- [ ] Calibração de probabilidades (Isotonic/Platt)
- [ ] Validação temporal robusta
- [ ] Otimização de threshold por custo

### Fase 4: Estratégia Híbrida (Regras + ML)
- [ ] Transformar `isFlaggedFraud` em feature
- [ ] Implementar listas de observação
- [ ] Sistema de priorização para revisão humana

### Fase 5: Operacionalização
- [ ] Pipeline de produção
- [ ] Painel de monitoramento
- [ ] Detecção de drift
- [ ] A/B testing de thresholds
- [ ] Sistema de explainability

## 🛠️ Tecnologias Utilizadas

- **Python 3.13.2**
- **Pandas**: Manipulação de dados
- **Scikit-learn**: Modelos de Machine Learning
- **NumPy**: Computação numérica
- **Matplotlib/Seaborn**: Visualização
- **Jupyter Notebook**: Desenvolvimento e análise

## 📁 Estrutura do Projeto

```
fraudDetection/
├── AIML Dataset.csv              # Dataset PaySim
├── EDA_Fraude_MobileMoney_PaySim.ipynb  # Notebook principal de análise
├── README.md                     # Este arquivo
└── .venv/                       # Ambiente virtual Python
```

## 🚀 Como Executar

1. **Clone o repositório**
```bash
git clone [repository-url]
cd fraudDetection
```

2. **Configure o ambiente Python**
```bash
# O ambiente virtual já está configurado
C:/Users/argus/workspace/fraudDetection/.venv/Scripts/python.exe --version
```

3. **Instale as dependências**
```bash
pip install pandas numpy scikit-learn matplotlib seaborn jupyter
```

4. **Execute o notebook**
```bash
jupyter notebook EDA_Fraude_MobileMoney_PaySim.ipynb
```

## 📈 Métricas Principais

### Métrica Primária
- **PR AUC**: Área sob a curva Precision-Recall (ideal para classes desbalanceadas)

### Métricas Operacionais
- **Precision ≥ 90%**: Minimizar falsos positivos
- **Recall ≥ 80%**: Capturar a maioria das fraudes
- **F1-Score**: Harmônica entre precision e recall

### Métricas de Referência
- **ROC AUC**: Para comparação com literatura
- **Matriz de Confusão**: Análise detalhada de erros

## 💡 Insights Importantes

1. **PR AUC é mais informativa que ROC AUC** para dados desbalanceados
2. **Threshold padrão (0,5) raramente é ótimo** - necessário ajuste por custo
3. **Random Forest supera Logistic Regression** no trade-off precision/recall
4. **Fraudes concentram-se em tipos específicos** (TRANSFER/CASH_OUT)
5. **Regra atual deve ser feature, não decisor** devido ao baixo recall

## 👥 Equipe

Projeto desenvolvido como parte da especialização em Machine Learning da FIAP.

## 📚 Referências

- [PaySim Dataset Documentation](https://github.com/EdgarLopezPhD/PaySim)
- [PR AUC vs ROC AUC for Imbalanced Classes](https://machinelearningmastery.com/)
- [Threshold Selection and Cost-Sensitive Learning](https://scikit-learn.org/)

## 📄 Licença

Este projeto é desenvolvido para fins acadêmicos como parte da especialização em Machine Learning da FIAP.

---

**Status**: 🔄 Em desenvolvimento | **Fase Atual**: Baseline implementado | **Próxima**: Feature Engineering

