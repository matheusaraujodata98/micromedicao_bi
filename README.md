# 🚰 Revenue Recovery & Engenharia de Analytics em Saneamento Comercial

[cite_start]Este repositório contém o desenvolvimento completo de um projeto prático focado na análise técnica e estratégica de uma base de dados hipotética pertencente ao sistema comercial de uma concessionária de saneamento[cite: 1, 4, 5]. [cite_start]O foco central deste trabalho é otimizar a gestão comercial da companhia, atuando diretamente sobre pilares vitais como faturamento, arrecadação e sustentabilidade financeira[cite: 5]. [cite_start]O objetivo final é identificar oportunidades latentes de melhoria operacional, promovendo o aumento de receita e a mitigação de perdas comerciais[cite: 6].

---

## 🎯 Objetivos Estratégicos da Análise

[cite_start]O desenvolvimento deste projeto foi guiado por cinco objetivos principais demandados para a inteligência de negócios da gestão comercial[cite: 15, 16]:
* [cite_start]**Recuperação de Receitas:** Identificação de forma proativa de oportunidades financeiras ocultas na base de clientes[cite: 17].
* [cite_start]**Detecção de Anomalias:** Mapeamento de potenciais fraudes, inconsistências cadastrais ou erros sistêmicos de faturamento e consumo[cite: 18].
* [cite_start]**Análise de Comportamento:** Detecção de padrões atípicos e desvios de consumo histórico[cite: 19].
* [cite_start]**Priorização Estratégica:** Seleção e ranqueamento de ações operacionais com base no maior potencial de retorno financeiro[cite: 21].
* [cite_start]**Estimativa de Ganhos:** Cálculo matemático do potencial financeiro recuperável e do payback associado às recomendações[cite: 22].

---

## 💾 Arquitetura e Estrutura dos Dados Disponibilizados

[cite_start]A base de dados utilizada no projeto compreende informações de micro-medição estruturadas da seguinte forma[cite: 7, 8]:
* [cite_start]**Dados Cadastrais:** Registro individualizado contendo identificação de cada ligação (`MATRICULA`), situação de ligação de água e esgoto (`SIT._LIG_AGUA` / `SIT._LIG_ESGOTO`), categoria do imóvel (`CATEGORIA_PRINCIPAL`) e número de economias ativas[cite: 9].
* **Dados do Ativo (Hidrômetro):** Número de identificação técnica, data de instalação, tipo de tecnologia (Unijato, Multijato, Ultrassônico), fabricante e classe metrológica do dispositivo.
* [cite_start]**Histórico Temporal (Últimos 12 Meses):** Matriz lateralizada contendo o histórico detalhado mês a mês de volume lido, volume real e volume faturado[cite: 10, 11, 12, 13].
* [cite_start]**Histórico Financeiro (Últimos 12 Meses):** Valores faturados abertos por rubricas de negócio, incluindo tarifas de água, esgoto, serviços adicionais, impostos e descontos aplicados[cite: 10, 12, 13, 14].

---

## ⚙️ Pipeline de Dados & Engenharia de Features (Python)

[cite_start]A modelagem e o tratamento dos dados foram executados utilizando a linguagem **Python** através da biblioteca **Pandas**, visando garantir a agilidade e o rigor metodológico necessários para o tratamento de dados em lote[cite: 34].

### 1. Higienização e Tratamento de Nulos
* Padronização de tipos primitivos de dados, strings e formatos de datas.
* Tratamento de campos nulos: as colunas de contagem de economias e rubricas de descontos foram preenchidas com `0` para evitar quebras matemáticas em agregações.

### 2. Tratamento de Outliers e Eventos Técnicos
* **Regra de Negócio Implementada:** Identificação e isolamento de códigos de ocorrência de leitura ($\ge$ 900.000) na coluna `VOLUME_LIDO`. Esses valores foram convertidos em `NaN` para que o cálculo das médias históricas de consumo real não sofresse distorções provocadas por anomalias sistêmicas ou impedimentos de leitura.

### 3. Mecanismo de Detecção de Anomalias (Engine de 14 Flags)
Para automatizar a varredura das 1.912 ligações da carteira, foi projetada uma matriz de risco composta por **14 flags binárias** (`0` ou `1`). As regras de negócio mais severas incluídas no modelo são:
* `FLAG_FATURAMENTO_ZERADO`: Matrículas ativas com consumo físico registrado, mas faturamento total zerado no mês base (identificando perdas operacionais imediatas).
* `FLAG_CONSUMO_ZERO_RECORRENTE`: Ligações que apresentaram mais de 3 meses consecutivos de consumo e faturamento zerados.
* `FLAG_INCONSISTENCIA_SITUACAO`: Ligações com status de esgoto "Ativa", porém com status de água "Cortada" ou "Cancelada" (forte indício de bypass ou religação clandestina).
* `FLAG_ALTA_VARIABILIDADE`: Identificação de oscilações estatísticas severas no consumo histórico, mensuradas através de um Coeficiente de Variação (CV) $> 1.5$.

As 14 flags foram consolidadas em uma única métrica ponderada denominada **`SCORE_RISCO`**, permitindo segmentar os clientes em categorias de criticidade para a operação.

---

## 📊 Impacto Financeiro e Ordem de Ataque (Priorização)

[cite_start]Seguindo as diretrizes de metodologia do estudo, adotou-se uma premissa técnica devidamente justificada no contexto de saneamento para modelar a viabilidade das ações de campo[cite: 23, 24, 26]:

> [cite_start]💡 **Premissa Técnica Documentada:** O custo operacional unitário para a emissão e execução de uma Ordem de Serviço (OS) de fiscalização em campo (envolvendo deslocamento de viatura, equipe técnica e hora/homem) foi estipulado em **R$ 150,00**[cite: 25].

[cite_start]Para otimizar o ganho financeiro e garantir o payback das recomendações, o modelo filtrou apenas ligações classificadas como **Alto Risco (`SCORE_RISCO` $\ge$ 3)** e cujo potencial de recuperação superasse o custo de emissão da OS[cite: 21, 22].

### Resultados Consolidados do Modelo

* **Carteira Total Analisada:** 1.912 ligações ativas.
* **Ligações Selecionadas para Ação Imediata (Malha Fina):** 6 ligações.
* **Potencial Bruto de Recuperação (Estimativa de 1 Mês):** R$ 6.864,32.
* **Custo Total de Mobilização de Campo (6 Ordens de Serviço):** R$ 900,00.
* **Lucro Líquido Estimado para a Concessionária:** **R$ 5.964,32**.

### A Lista de Corte Otimizada (Ordem de Ataque)

A inteligência do modelo comprovou que o foco inicial deve ser direcionado à categoria **Comercial**, que concentra os maiores tickets médios e os maiores retornos financeiros por quilômetro rodado da equipe de fiscalização.

| Matrícula | Categoria Principal | Score de Risco | Receita Recuperável Est. | Lucro Líquido Estimado | Ordem de Ataque |
| :---: | :---: | :---: | :---: | :---: | :---: |
| **1374107-1** | Comercial | 4 | R$ 1.439,94 | R$ 1.289,94 | **1º Lugar** |
| **1374108-0** | Comercial | 4 | R$ 1.439,94 | R$ 1.289,94 | **2º Lugar** |
| **1219707-6** | Comercial | 3 | R$ 1.439,94 | R$ 1.289,94 | **3º Lugar** |
| **1373459-8** | Comercial | 3 | R$ 1.439,94 | R$ 1.289,94 | **4º Lugar** |
| **1373693-0** | Residencial | 3 | R$ 552,27 | R$ 402,27 | **5º Lugar** |
| **1374231-0** | Residencial | 3 | R$ 552,27 | R$ 402,27 | **6º Lugar** |

---

## 📈 Painel Executivo e de Indicadores (Power BI)

A etapa final do projeto consolida a inteligência gerada na modelagem em uma ferramenta de Visualização Executiva interativa construída no **Microsoft Power BI**[cite: 30, 31]. O painel foi desenhado sob a perspectiva de dois níveis de visualização de negócio:

### 1. Visão Executiva (Estratégico)
* **Métricas Principais (KPI Cards):** Potencial Financeiro Bruto Recuperável, Lucro Líquido Projetado, Quantidade de Alvos Críticos e Volume Total Desviado.
* **Análise de Pareto de Perdas:** Gráficos de distribuição indicando a concentração do retorno financeiro por Categoria Principal de Imóvel.
* **Matriz de Alerta:** Visão consolidada da volumetria de ligações afetadas por cada uma das 14 flags de anomalia identificadas.

### 2. Visão de Campo (Operacional)
* **Filtros Dinâmicos:** Segmentação por nível de risco, categoria comercial/residencial e marcas de hidrômetro para apoio à engenharia técnica.
* **Lista de Acionamento Direto:** Tabela analítica contendo as matrículas ordenadas de forma decrescente pelo Lucro Líquido Estimado, servindo como o cronograma exato de roteirização para as equipes de inspeção de campo.

*(Nota: Prints do dashboard e fórmulas DAX serão consolidados nesta seção assim que a publicação do painel .pbix for finalizada)*

---

## 📁 Estrutura do Projeto no Repositório

```text
├── data/
│   ├── raw/          # Base de dados original (bruta) do sistema comercial
│   └── processed/    # Bases geradas em .xlsx limpas e enriquecidas com as flags
├── notebooks/
│   ├── analise_comercial.ipynb      # Script de limpeza, higienização e engenharia de flags
│   └── analise_exploratoria.ipynb   # Script de análise exploratória (EDA) e cálculo de ROI
├── dashboards/
│   └── painel_recuperacao_receita.pbix  # Arquivo de desenvolvimento do Power BI
└── README.md         # Documentação técnica do projeto