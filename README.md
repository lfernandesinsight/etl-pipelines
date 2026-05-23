# 🔄 ETL Pipelines

<div align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=2E86C1&height=120&section=header&text=ETL%20Pipelines&fontSize=36&fontColor=ffffff&fontAlignY=55&desc=SSIS%20%7C%20Talend%20%7C%20SQL%20%7C%20Python%20%7C%20Extract%20%E2%86%92%20Transform%20%E2%86%92%20Load&descAlignY=80&descSize=14&descColor=ffffff" alt="ETL Pipelines" />
</div>

---

## 📌 Sobre este repositório

Coleção de projetos e scripts de **ETL (Extract, Transform, Load)** para ingestão, tratamento e carga de dados em ambientes analíticos. Inclui pipelines desenvolvidos com SSIS, Talend, SQL e Python, com foco em Data Warehouse e ambientes de BI.

---

## 📁 Estrutura do Repositório

```
📁 etl-pipelines/
│
├── 📁 sql/
│   ├── 📁 procedures/
│   ├── 📁 views/
│   └── README.md
│
├── 📁 ssis/
│   ├── 📁 packages/
│   └── README.md
│
├── 📁 python/
│   ├── 📁 scripts/
│   ├── requirements.txt
│   └── README.md
│
├── 📁 talend/
│   └── README.md
│
├── .gitignore
└── README.md
```

---

## 🚀 Projetos

| Pipeline | Descrição | Ferramenta | Status |
|---|---|---|---|
| 🗄️ [SQL Procedures](#) | Procedures e views para staging e DW | SQL Server | 🚧 Em breve |
| ⚙️ [SSIS Packages](#) | Pacotes de carga e atualização de dados | SSIS | 🚧 Em breve |
| 🐍 [Python ETL](#) | Scripts de extração e transformação | Python / Pandas | 🚧 Em breve |
| 🔧 [Talend Jobs](#) | Jobs de integração de dados | Talend | 🚧 Em breve |

---

## 🛠️ Tecnologias Utilizadas

![SSIS](https://img.shields.io/badge/SSIS-CC2927?style=for-the-badge&logo=microsoft-sql-server&logoColor=white)
![SQL Server](https://img.shields.io/badge/SQL%20Server-CC2927?style=for-the-badge&logo=microsoft-sql-server&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Talend](https://img.shields.io/badge/Talend-FF6D70?style=for-the-badge&logo=talend&logoColor=white)
![Pentaho](https://img.shields.io/badge/Pentaho-0066CC?style=for-the-badge&logoColor=white)

---

## 💡 Padrões e Boas Práticas

### Nomenclatura de Objetos SQL
```sql
-- Tabelas
stg_<nome>    → Staging:    stg_clientes
dim_<nome>    → Dimensão:   dim_clientes
fct_<nome>    → Fato:       fct_vendas
tmp_<nome>    → Temporária: tmp_processamento

-- Procedures
usp_load_<nome>    → Carga:      usp_load_clientes
usp_update_<nome>  → Atualização: usp_update_vendas
usp_clean_<nome>   → Limpeza:    usp_clean_staging
```

### Exemplo de Procedure de Carga
```sql
CREATE PROCEDURE usp_load_clientes
AS
BEGIN
    SET NOCOUNT ON;

    -- 1. Staging → limpeza
    TRUNCATE TABLE stg_clientes;

    -- 2. Inserção incremental na dimensão
    INSERT INTO dim_clientes (id_cliente, nome, cidade, dt_carga)
    SELECT
        s.id_cliente,
        TRIM(s.nome),
        TRIM(s.cidade),
        GETDATE()
    FROM stg_clientes s
    LEFT JOIN dim_clientes d ON s.id_cliente = d.id_cliente
    WHERE d.id_cliente IS NULL;

    -- 3. Log
    INSERT INTO log_execucao (processo, dt_execucao, status)
    VALUES ('load_clientes', GETDATE(), 'OK');
END;
```

### Exemplo de Script Python ETL
```python
import pandas as pd
import sqlalchemy as sa

def extract(filepath: str) -> pd.DataFrame:
    """Extrai dados de arquivo CSV."""
    return pd.read_csv(filepath, encoding='utf-8', sep=';')

def transform(df: pd.DataFrame) -> pd.DataFrame:
    """Limpa e padroniza os dados."""
    df.columns = df.columns.str.lower().str.strip()
    df.dropna(subset=['id_cliente'], inplace=True)
    df['nome'] = df['nome'].str.title().str.strip()
    df['dt_carga'] = pd.Timestamp.now()
    return df

def load(df: pd.DataFrame, table: str, engine) -> None:
    """Carrega dados no banco de destino."""
    df.to_sql(table, con=engine, if_exists='append', index=False)
    print(f"✅ {len(df)} registros carregados em {table}")

if __name__ == "__main__":
    engine = sa.create_engine("mssql+pyodbc://...")
    df = extract("data/clientes.csv")
    df = transform(df)
    load(df, "stg_clientes", engine)
```

---

## 🗃️ Arquitetura de Referência

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Fontes    │────▶│   Staging   │────▶│     DW      │────▶│     BI      │
│  (CSV, DB,  │     │  (stg_*)    │     │ (dim_/fct_) │     │  (Power BI) │
│   API...)   │     │             │     │             │     │             │
└─────────────┘     └─────────────┘     └─────────────┘     └─────────────┘
   Extract               Transform           Load               Consume
```

---

## ⚙️ Como executar os scripts Python

```bash
# 1. Clone o repositório
git clone https://github.com/lfernandesinsight/etl-pipelines.git
cd etl-pipelines/python

# 2. Crie o ambiente virtual
python -m venv .venv
source .venv/bin/activate  # Linux/Mac
.venv\Scripts\activate     # Windows

# 3. Instale as dependências
pip install -r requirements.txt

# 4. Configure as variáveis de ambiente
cp .env.example .env
# Edite o .env com suas credenciais

# 5. Execute
python scripts/load_clientes.py
```

---

## 📬 Contato

<div align="left">
  <a href="mailto:lfernandes.insight@gmail.com">
    <img src="https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white" />
  </a>
  <a href="https://www.linkedin.com/in/lfernandesinsight" target="_blank">
    <img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white" />
  </a>
  <a href="https://github.com/lfernandesinsight" target="_blank">
    <img src="https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white" />
  </a>
</div>

---

<div align="center">
  <sub>Desenvolvido por <a href="https://github.com/lfernandesinsight">Leandro Fernandes</a></sub>
</div>
