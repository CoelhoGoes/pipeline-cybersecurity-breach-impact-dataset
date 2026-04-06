# 🛡️ Checklist Anti-Leakage (Prevenção de Vazamento de Dados)

**Projeto:** Construção das Camadas Bronze e Prata para Dados de Machine Learning  

## 1. O que é Data Leakage neste contexto?

Neste projeto de cibersegurança, o *data leakage* (vazamento de dados) ocorreria se fornecêssemos ao modelo informações que representam o **resultado** ou o **impacto futuro** de um ataque, antes mesmo de o modelo tentar prever a severidade ou o impacto do incidente. Analisamos todas as colunas e removemos aquelas que contêm informações do "futuro" para garantir a integridade da Camada Prata.

---

## 2. Checklist de Colunas Removidas por Risco de Leakage

Abaixo está o mapeamento das variáveis originais da Camada Bronze que foram excluídas durante a transformação para a Camada Prata, separadas por dataset, junto com a justificativa técnica.

### 📈 Dataset: Market Impact (`market_impact.parquet`)

*Nota: A variável alvo (`target_market_impact`) foi criada com base na coluna `ABNORMAL_RETURN_1D`. Após a criação do label, a coluna original foi removida para evitar vazamento da variável alvo.*

| Coluna Removida | Ação | Justificativa do Vazamento (Leakage) |
| :--- | :---: | :--- |
| `ABNORMAL_RETURN_1D` | ❌ Removida | Usada para gerar o Target. Mantê-la faria o modelo prever o Target usando a exata métrica que o definiu. |
| `PRICE_7D_AFTER` / `PRICE_30D_AFTER` | ❌ Removida | Preço da ação 7 e 30 dias após o incidente. É informação direta do futuro. |
| `ABNORMAL_RETURN_7D` / `30D` | ❌ Removida | Retorno anormal calculado em janelas de tempo pós-incidente. |
| `CAR_0_TO_7`, `0_TO_30`, `0_TO_90` | ❌ Removida | Retorno Anormal Cumulativo após o incidente. Mede o impacto semanas ou meses depois do dia 0. |
| `POST_INCIDENT_VOLATILITY_30D` | ❌ Removida | Volatilidade dos 30 dias seguintes ao ataque. |
| `DAYS_TO_PRICE_RECOVERY` | ❌ Removida | Só é possível saber quantos dias as ações levaram para se recuperar depois que o evento acabou. |
| `T_STATISTIC_30D` / `P_VALUE_30D` | ❌ Removida | Métricas estatísticas calculadas com base nos 30 dias após a ocorrência do incidente. |

### 💰 Dataset: Financial Impact (`financial_impact.parquet`)

*Nota: A variável alvo (`TARGET_SEVERE_LOSS`) foi derivada da coluna `TOTAL_LOSS_USD`.*

| Coluna Removida | Ação | Justificativa do Vazamento (Leakage) |
| :--- | :---: | :--- |
| `TOTAL_LOSS_USD` (e os `bounds`) | ❌ Removida | Usadas para gerar o Target. Se o modelo souber a perda total exata, prever se a perda é "severa" perde o sentido (vazamento de variável alvo). |
| `RANSOM_PAID_USD` | ❌ Removida | O modelo não tem como saber quanto será pago de resgate no momento que o ataque começa (apenas o valor pedido). |
| `RANSOM_SOURCE` | ❌ Removida | Informação sobre a origem do dinheiro do resgate só existe após o pagamento. |
| `REGULATORY_FINE_USD` | ❌ Removida | O valor de multas regulatórias só é definido meses ou anos após o ataque, viciando o modelo. |
| `INSURANCE_PAYOUT_USD` | ❌ Removida | O pagamento de seguro ocorre apenas após a investigação final e contabilização dos danos pós-incidente. |

---

## 3. Colunas Removidas por Falta de Utilidade Preditiva (Não-Leakage)

Algumas colunas foram descartadas da camada Prata apenas por não possuírem utilidade analítica ou por gerarem ruído em todos os três datasets (`incidents`, `market_impact` e `financial_impact`).

| Coluna Removida | Dataset(s) Afetado(s) | Justificativa |
| :--- | :--- | :--- |
| `NOTES` | Todos | Anotações em texto livre, difíceis de estruturar sem uso de NLP complexo. |
| `CREATED_AT`, `UPDATED_AT` | Todos | Timestamps de auditoria de sistema (data de inserção da linha), irrelevantes para as características do ciberataque. |
| `DATA_SOURCE_SECONDARY` | Incidents | Fonte de dados secundária (URLs), mantida apenas a fonte primária para referência. |

## 4. Conclusão

Todas as variáveis que expunham a "resposta" do problema (vazamento de target) ou que representavam métricas temporalmente posteriores aos incidentes foram descartadas utilizando os métodos `.drop()`. A Camada Prata agora é composta apenas por atributos acessíveis e válidos no "Dia 0" do incidente.
