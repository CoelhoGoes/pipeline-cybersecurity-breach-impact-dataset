# 📊 Relatório de Qualidade dos Dados - Camada Bronze

**Projeto:** Construção das Camadas Bronze e Prata para Dados de Machine Learning  

---

## 1. Visão Geral dos Datasets (Camada Bronze)

Resumo das características iniciais dos três arquivos de dados brutos após a ingestão:

| Atributo | `incidents_master.csv` | `market_impact.csv` | `financial_impact.csv` |
| :--- | :---: | :---: | :---: |
| **Total de Linhas** | 850 | 358 | 778 |
| **Total de Colunas** | 32 | 31 | 19 |
| **Hash MD5 da Carga** | `41816f8cb0a0` | `76ed3aacd262` | `8c3b7554bfd9` |

---

## 2. Matriz de Critérios de Qualidade

Esta tabela define as regras automáticas de validação e os critérios claros utilizados para identificar falhas nos datasets.

| Dimensão | Regra de Validação | Critério de Falha | Ação na Camada Prata |
| :--- | :--- | :--- | :--- |
| **Completude** | Colunas com alta taxa de nulos (informação ausente por natureza) | Mais de 40% de nulos na coluna | Preencher com `'Unknown'` (categóricas) ou `0` (numéricas) |
| **Completude** | Nulos em chaves primárias | > 0 nulos em `incident_id` | Remover a linha |
| **Unicidade** | Registros exatos duplicados | Linhas 100% idênticas | Remover as linhas duplicadas mantendo apenas a primeira |
| **Unicidade** | IDs de incidente duplicados | `incident_id` repetido no mesmo arquivo | Remover a linha duplicada mantendo apenas a primeira ocorrência |
| **Validade** | Formato de datas | Datas fora do padrão `YYYY-MM-DD` | Padronizar formato via `pd.to_datetime` ou remover registro se inválido |
| **Consistência** | Domínio de categorias (`attack_vector_primary`) | Valores fora da lista: `apt`, `backdoor`, `data_breach`, `ddos`, `malware`, `phishing`, `ransomware`, `supply_chain`, `trojan` | Substituir por `'Unknown'` |
| **Relevância** | Colunas com risco de Leakage | Colunas derivadas do evento futuro (ex: preços pós-incidente, multas, pagamentos) | Remover antes de exportar para a Prata |

---

## 3. Resultados das Validações

### 3.1. `incidents_master.csv`

#### 3.1.1. Análise de Valores Nulos

* **Linhas sem nulos:** **0 linhas** totalmente completas; todas as **850 linhas** contêm pelo menos um valor nulo.
* **Colunas mais críticas:**

| Coluna | % de Nulos | Ação Aplicada |
| :--- | :---: | :--- |
| `review_flag` | 780 de 850 (91,76%) | Preenchido com `'Unknown'` |
| `industry_secondary` | 697 de 850 (82,00%) | Preenchido com `'Unknown'` |
| `attack_vector_secondary` | 639 de 850 (75,18%) | Preenchido com `'Unknown'` |
| `notes` | 636 de 850 (74,82%) | Coluna descartada |
| `data_source_secondary` | 464 de 850 (54,59%) | Coluna descartada |
| `stock_ticker` | 438 de 850 (51,53%) | Preenchido com `'Unknown'` |
| `downtime_hours` | 430 de 850 (50,59%) | Preenchido com `0` |
| `attributed_group` | 368 de 850 (43,29%) | Preenchido com `'Unknown'` |
| `attribution_confidence` | 368 de 850 (43,29%) | Preenchido com `'Unknown'` |
| `attack_chain` | 275 de 850 (32,35%) | Preenchido com `'Unknown'` |
| `data_compromised_records` | 248 de 850 (29,18%) | Preenchido com `0` |
| `data_type` | 248 de 850 (29,18%) | Preenchido com `'Unknown'` |

#### 3.1.2. Análise de Duplicidade

* Foram identificadas **0 linhas completamente duplicadas** no dataset.
* Foram identificados **0 IDs duplicados** na coluna `incident_id`.

#### 3.1.3. Inconsistências em Categorias e Textos

* **Coluna `attack_vector_primary`:** Todos os valores pertencem ao domínio esperado (`apt`, `backdoor`, `data_breach`, `ddos`, `malware`, `phishing`, `ransomware`, `supply_chain`, `trojan`). Nenhuma inconsistência encontrada.
* **Coluna `quality_grade`:** Valores encontrados: `Bronze`, `Silver`, `Gold`. Nenhuma inconsistência encontrada.

#### 3.1.4. Validação de Datas

* **Colunas de data** (`incident_date`, `discovery_date`, `disclosure_date`): Todos os registros estão no padrão `YYYY-MM-DD`. **0 registros** com formato inválido.

---

### 3.2. `market_impact.csv`

#### 3.2.1. Análise de Valores Nulos

* **Linhas sem nulos:** **83 linhas** totalmente completas; **275 linhas** contêm pelo menos um valor nulo.
* **Colunas mais críticas:**

| Coluna | % de Nulos | Ação Aplicada |
| :--- | :---: | :--- |
| `notes` | 74,30% | Coluna descartada |
| `days_to_price_recovery` | 10,06% | Coluna descartada (Leakage) |

#### 3.2.2. Análise de Duplicidade

* Foram identificadas **0 linhas completamente
duplicadas** no dataset.
* Foram identificados **0 IDs duplicados** na coluna `incident_id`.

#### 3.2.3. Inconsistências em Categorias e Textos

* Não há colunas categóricas textuais neste arquivo além de `stock_ticker` (identificador). Nenhuma inconsistência encontrada.

#### 3.2.4. Validação de Formatos Numéricos

* As colunas de retorno anormal (`abnormal_return_*`) e preços (`price_*`) são numéricas contínuas. Nenhum valor fora do tipo esperado foi encontrado.

---

### 3.3. `financial_impact.csv`

#### 3.3.1. Análise de Valores Nulos

* **Linhas sem nulos:** **0 linhas** totalmente completas; todas as **778 linhas** contêm pelo menos um valor nulo.
* **Colunas mais críticas:**

| Coluna | % de Nulos | Ação Aplicada |
| :--- | :---: | :--- |
| `ransom_paid_usd` | 88,95% | Coluna descartada (Leakage) |
| `ransom_source` | 88,95% | Coluna descartada (Leakage) |
| `regulatory_fine_usd` | 83,03% | Coluna descartada (Leakage) |
| `ransom_demanded_usd` | 73,52% | Preenchido com `0` (sem pedido de resgate) |
| `notes` | 68,12% | Coluna descartada |
| `insurance_payout_usd` | 44,09% | Coluna descartada (Leakage) |

#### 3.3.2. Análise de Duplicidade

* Foram identificadas **0 linhas completamente duplicadas** no dataset.
* Foram identificados **0 IDs duplicados** na coluna `incident_id`.

#### 3.3.3. Inconsistências em Categorias e Textos

* **Coluna `direct_loss_method`** e **`total_loss_method`**: Colunas de texto descritivo livre, sem domínio fixo. Não foram aplicadas regras de padronização.

#### 3.3.4. Validação de Formatos Numéricos

* Todas as colunas de valor monetário (`direct_loss_usd`, `total_loss_usd`, etc.) são numéricas contínuas. Nenhum valor fora do tipo esperado foi encontrado.

---

## 4. Conclusão e Próximos Passos

Com base nas validações automáticas descritas acima, os três datasets foram filtrados e limpos. Os dados aprovados ou tratados mediante as ações estabelecidas na Matriz de Qualidade foram exportados com sucesso para a **Camada Prata** em formato Parquet, estando prontos para o cruzamento e criação dos labels finais.

| Arquivo Origem | Linhas Bronze | Linhas Prata | Taxa de Retenção |
| :--- | :---: | :---: | :---: |
| `incidents_master.csv` | 850 | 850 | 100% |
| `market_impact.csv` | 358 | 358 | 100% |
| `financial_impact.csv` | 778 | 778 | 100% |

> **Nota:** A taxa de retenção de 100% em todos os arquivos deve-se à ausência de linhas nulas em chaves primárias e de registros duplicados. As ações de qualidade aplicadas foram de preenchimento e remoção de colunas, sem necessidade de descarte de linhas.
