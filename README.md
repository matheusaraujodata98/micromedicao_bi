# 🚰 Revenue Recovery Analytics para Saneamento Comercial

> Projeto prático focado na análise técnica e estratégica de uma base de dados hipotética pertencente ao sistema comercial de uma concessionária de saneamento, com foco em otimizar faturamento, arrecadação e sustentabilidade financeira.

---

## 🛠️ Tecnologias Utilizadas

![Python](https://img.shields.io/badge/Python-3.14%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-F37626?style=for-the-badge&logo=jupyter&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Google Sheets](https://img.shields.io/badge/Google%20Sheets-34A853?style=for-the-badge&logo=googlesheets&logoColor=white)
![Status](https://img.shields.io/badge/Status-Conclu%C3%ADdo-28a745?style=for-the-badge)

---

## 🎯 Objetivos Estratégicos da Análise

O desenvolvimento deste projeto foi guiado por cinco objetivos principais demandados para a inteligência de negócios da gestão comercial:

- **Recuperação de Receitas:** Identificação proativa de oportunidades financeiras ocultas na base de clientes.
- **Detecção de Anomalias:** Mapeamento de potenciais fraudes, inconsistências cadastrais ou erros sistêmicos de faturamento e consumo.
- **Análise de Comportamento:** Detecção de padrões atípicos e desvios de consumo histórico.
- **Priorização Estratégica:** Seleção e ranqueamento de ações operacionais com base no maior potencial de retorno financeiro.
- **Estimativa de Ganhos:** Cálculo matemático do potencial financeiro recuperável e do payback associado às recomendações.

---

## 💾 Arquitetura e Estrutura dos Dados

A base de dados utilizada no projeto compreende informações de micro-medição estruturadas da seguinte forma:

- **Dados Cadastrais:** Registro individualizado contendo identificação de cada ligação (`MATRICULA`), situação de ligação de água e esgoto (`SIT._LIG_AGUA` / `SIT._LIG_ESGOTO`), categoria do imóvel (`CATEGORIA_PRINCIPAL`) e número de economias ativas.
- **Dados do Ativo (Hidrômetro):** Número de identificação técnica, data de instalação, tipo de tecnologia (Unijato, Multijato, Ultrassônico), fabricante e classe metrológica do dispositivo.
- **Histórico Temporal (Últimos 12 Meses):** Matriz lateralizada contendo o histórico detalhado mês a mês de volume lido, volume real e volume faturado.
- **Histórico Financeiro (Últimos 12 Meses):** Valores faturados abertos por rubricas de negócio, incluindo tarifas de água, esgoto, serviços adicionais, impostos e descontos aplicados.

---

## ⚙️ Pipeline de Dados & Engenharia de Features

A modelagem e o tratamento dos dados foram executados utilizando **Python** com a biblioteca **Pandas**, garantindo agilidade e rigor metodológico para o tratamento de dados em lote via **Jupyter Notebooks**.

### 1. Higienização e Tratamento de Nulos
- Padronização de tipos primitivos de dados, strings e formatos de datas.
- Tratamento de campos nulos: colunas de contagem de economias e rubricas de descontos foram preenchidas com `0` para evitar quebras matemáticas em agregações.

### 2. Tratamento de Outliers e Eventos Técnicos
- **Regra de Negócio Implementada:** Identificação e isolamento de códigos de ocorrência de leitura (≥ 900.000) na coluna `VOLUME_LIDO`. Esses valores foram convertidos em `NaN` para que o cálculo das médias históricas de consumo real não sofresse distorções provocadas por anomalias sistêmicas.

### 3. Mecanismo de Detecção de Anomalias — Engine de 14 Flags

Para automatizar a varredura das 1.912 ligações da carteira, foi projetada uma **matriz de risco composta por 14 flags binárias** (`0` ou `1`). As regras de negócio mais severas incluídas no modelo são:

| Flag | Descrição |
|------|-----------|
| `FLAG_FATURAMENTO_ZERADO` | Matrículas ativas com consumo físico registrado, mas faturamento total zerado no mês base. |
| `FLAG_CONSUMO_ZERO_RECORRENTE` | Ligações com mais de 3 meses consecutivos de consumo e faturamento zerados. |
| `FLAG_INCONSISTENCIA_SITUACAO` | Ligações com esgoto "Ativo", porém água "Cortada" ou "Cancelada" — forte indício de bypass ou religação clandestina. |
| `FLAG_ALTA_VARIABILIDADE` | Oscilações severas no consumo histórico mensuradas por Coeficiente de Variação (CV) > 1.5. |

As 14 flags foram consolidadas em uma única métrica ponderada denominada **`SCORE_RISCO`**, permitindo segmentar os clientes em categorias de criticidade para a operação.

---

## 📊 Impacto Financeiro e Ordem de Ataque

> 💡 **Premissa Técnica Documentada:** O custo operacional unitário para emissão e execução de uma Ordem de Serviço (OS) de fiscalização em campo foi estipulado em **R$ 150,00**.

O modelo filtrou apenas ligações classificadas como **Alto Risco (`SCORE_RISCO` ≥ 3)** e com potencial de recuperação superior ao custo da OS.

### Resultados Consolidados

| Indicador | Valor |
|-----------|-------|
| Carteira Total Analisada | 1.912 ligações |
| Ligações Selecionadas (Malha Fina) | 6 ligações |
| Potencial Bruto de Recuperação (1 mês) | R$ 6.864,32 |
| Custo Total de Mobilização (6 OSs) | R$ 900,00 |
| **Lucro Líquido Estimado** | **R$ 5.964,32** |

### Lista de Corte Otimizada — Ordem de Ataque

| Matrícula | Categoria | Score de Risco | Receita Recuperável | Lucro Líquido | Prioridade |
|:---------:|:---------:|:--------------:|:-------------------:|:-------------:|:----------:|
| 1374107-1 | Comercial | 4 | R$ 1.439,94 | R$ 1.289,94 | **1º** |
| 1374108-0 | Comercial | 4 | R$ 1.439,94 | R$ 1.289,94 | **2º** |
| 1219707-6 | Comercial | 3 | R$ 1.439,94 | R$ 1.289,94 | **3º** |
| 1373459-8 | Comercial | 3 | R$ 1.439,94 | R$ 1.289,94 | **4º** |
| 1373693-0 | Residencial | 3 | R$ 552,27 | R$ 402,27 | **5º** |
| 1374231-0 | Residencial | 3 | R$ 552,27 | R$ 402,27 | **6º** |

> A inteligência do modelo comprovou que o foco inicial deve ser direcionado à categoria **Comercial**, que concentra os maiores tickets médios e os maiores retornos financeiros por quilômetro rodado da equipe de fiscalização.

---

## 📈 Painel Executivo e de Indicadores (Power BI)

A etapa final consolida a inteligência gerada na modelagem em uma ferramenta de visualização executiva interativa construída no **Microsoft Power BI**, desenhada sob dois níveis de visualização:

### Visão Executiva (Estratégico)
- **KPI Cards:** Potencial Financeiro Bruto Recuperável, Lucro Líquido Projetado, Quantidade de Alvos Críticos e Volume Total Desviado.
- **Análise de Pareto de Perdas:** Distribuição do retorno financeiro por Categoria Principal de Imóvel.
- **Matriz de Alerta:** Volumetria de ligações afetadas por cada uma das 14 flags de anomalia.

### Visão de Campo (Operacional)
- **Filtros Dinâmicos:** Segmentação por nível de risco, categoria e fabricante de hidrômetro.
- **Lista de Acionamento Direto:** Tabela analítica com matrículas ordenadas por Lucro Líquido Estimado, servindo como cronograma de roteirização para as equipes de inspeção.

> *(Prints do dashboard e fórmulas DAX serão consolidados nesta seção assim que a publicação do painel `.pbix` for finalizada)*

---

## 📁 Estrutura do Repositório

```text
├── data/
│   ├── raw/                              # Base de dados original (bruta) do sistema comercial
│   └── processed/                        # Bases limpas e enriquecidas com as flags (.xlsx)
├── notebooks/
│   ├── analise_comercial.ipynb           # Limpeza, higienização e engenharia de flags
│   └── analise_exploratoria.ipynb        # Análise exploratória (EDA) e cálculo de ROI
├── dashboards/
│   └── painel_recuperacao_receita.pbix   # Arquivo de desenvolvimento do Power BI
└── README.md                             # Documentação técnica do projeto
```
