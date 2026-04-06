# 🛡️ Pipeline de Ciência de Dados - Construção das Camadas Bronze e Prata para Dados de Machine Learning 

## projeto consiste na construção de um pipeline de dados organizado em camadas (Bronze e Prata) para um dataset de cibersegurança, com o objetivo de preparar os dados para uso em modelos de Machine Learning.

---

### 🏗️ Arquitetura do Projeto
O pipeline segue as boas práticas de engenharia e governança, garantindo o rastreio do fluxo de transformações:
1. **Camada Origin**: Recebimento dos arquivos brutos em formato .csv.
2. **Camada Bronze**: Armazenamento dos dados brutos em formato .parquet, com padronização de nomes de colunas e registro de metadados de ingestão.
3. **Camada Prata**: Dados transformados, limpos e com tratamento de **Data Leakage**, prontos para análise e modelagem prediti**va.

---

### 📊 Data Lineage (Linhagem de Dados)
A representação visual abaixo detalha o fluxo dos dados, as validações e as colunas tratadas para evitar data leakage:
````mermaid
graph TD
    subgraph "1. Origem (CSV)"
        A[Arquivos Originais]
    end

    subgraph "2. Camada Bronze (Raw)"
        A --> B{Ingestão e Metadados}
        B -->|Parquet| C[(Dataset Bronze)]
        B --- D[Metadados: Nome, Linhas, Data/Hora]
    end

    subgraph "3. Validação e Qualidade"
        C --> E{Regras Automáticas}
        E -->|Check| F[Nulos, Duplicados, Inconsistências]
    end

    subgraph "4. Camada Prata (Trusted)"
        F --> G{Transformações}
        G -->|Limpeza| H[(Dataset Prata)]
        G --- I[Tratamento: Datas, Categorias, Anti-Leakage]
    end

    subgraph "5. Output Machine Learning"
        H --> J[Dataset Final + Label]
    end

    style C fill:#cd7f32,color:#fff
    style H fill:#c0c0c0,color:#000
````

---

### 🛠️ Transformações e Qualidade

* **Validação Bronze**: Implementação de regras automáticas para identificar falhas como valores nulos e categorias inconsistentes.
* **Limpeza Prata**: Remoção de duplicidades e padronização de datas e categorias.
* **Prevenção de Leakage**: Identificação e remoção de colunas que poderiam comprometer a integridade dos modelos preditivos.
* **Análise Exploratória (EDA)**: Construção de visualizações gráficas para identificar padrões e compreender a estrutura dos dados.

---

### 📂 Estrutura de Entrega
Os seguintes itens compõem esta entrega:Notebook Principal: Contendo todo o pipeline desenvolvido.
* **Arquivos Parquet**: Datasets das camadas Bronze e Prata.
* **Relatório de Qualidade**: Detalhamento dos problemas encontrados na Bronze.
* **Checklist Anti-Leakage**: Documentação das colunas removidas e justificativas.
* **Data Lineage**: Representação clara do fluxo de dados.

---

### 🚀 Instruções de Execução
1. Certifique-se de ter as bibliotecas pandas, pyarrow e matplotlib instaladas.
2. Rode as três preimeiras linhas de main.ipynb
3. Coloque os arquivos .csv originais no diretório configurado como origem no notebook.
4. Execute o notebook principal para gerar as camadas Bronze e Prata automaticamente.
5. Os arquivos resultantes serão salvos em formato .parquet nas respectivas pastas organizadas.