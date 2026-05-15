
-------------------------------------------------------------------------
              RELATÓRIO DE LIMPEZA E TRATAMENTO DE DADOS
              Sistema Comercial de Saneamento — Micromedição
-------------------------------------------------------------------------

BASE ORIGINAL
-------------------------------------------------------------------------
  Registros totais:          1.912 ligações
  Colunas originais:           132 (15 cadastrais + 117 históricas)
  Períodos na base:             13 (mês base + _01 a _12)

PREMISSAS ADOTADAS
-------------------------------------------------------------------------
  - Colunas _01 a _12 compõem o histórico dos 12 meses conforme o case.
  - O mês base (sem sufixo) refere-se ao período atual de referência,
    usado para comparações e KPIs — não compõe o histórico.
  - VOLUME_LIDO >= 900.000 m³ é tratado como código de ocorrência de leitura.
  - VOLUME_REAL é considerado o dado confiável de consumo.
  - Nulos em economias e descontos são interpretados como ausência legítima (convertidos para 0).
  - Valores negativos em VALOR_SERVICOS representam estornos/créditos reais.
  - Outlier da matrícula 1371190-3 foi mantido no VOLUME_REAL, pois
    o sistema confirmou o estorno correspondente de R$ 280.240,43.

ETAPAS DE TRATAMENTO
-------------------------------------------------------------------------
  1. Padronização:
     - Normalização de strings e conversão de datas (formato dayfirst=True).
     - 82 datas nulas identificadas como esperadas (ligações sem hidrômetro).

  2. Tratamento de Nulos:
     - Economias e descontos convertidos para 0 (ausência legítima de categoria).
     - Demais casos sinalizados via flags de rastreabilidade.

  3. Tratamento de Volumes:
     - 107 leituras absurdas identificadas em 87 ligações (4,6% da base).
     - Pico de ocorrências em outubro (_10) com 35 casos.
     - Valores substituídos por NaN no VOLUME_LIDO para não distorcer médias.
     - VOLUME_REAL preservado intacto como referência de consumo efetivo.

  4. Inconsistências Financeiras:
     - 128 ligações com estornos em VALOR_SERVICOS (valores negativos).
     - 17 ligações com faturamento zerado e consumo > 0 no mês base.
       - 9 zeradas por estorno aplicado     -> R$ 15.022,62 a recuperar
       - 8 zeradas sem justificativa        -> R$  1.878,71 a recuperar
     - Potencial de recuperação no mês base: R$ 16.901,33.
     - Outlier extremo em agosto: estorno de R$ 280.240,43 (matrícula 1371190-3).

  5. Engenharia de Features (12 meses históricos — _01 a _12):
     - Métricas descritivas por ligação: média, total, min, max e desvio padrão.
     - Coeficiente de variação do consumo (CV) para detecção de instabilidade.
     - Contagem de meses com consumo zero por ligação.
     - Desvio do mês base frente à média histórica (absoluto e percentual).

DIAGNÓSTICO FINAL (FLAGS E SCORE DE RISCO)
-------------------------------------------------------------------------
  Total de flags geradas: 14

  Flags Cadastrais:
  - Situação água nula:          15 ligações  (0.8%)
  - Situação esgoto nula:        36 ligações  (1.9%)
  - Sem categoria:               17 ligações  (0.9%)
  - Sem hidrômetro (ativa):       1 ligação   (0.1%)
  - Sem economia preenchida:      6 ligações  (0.3%)

  Flags de Anomalia:
  - Leitura com ocorrência:      87 ligações  (4.6%)
  - Inconsistência água/esgoto:  65 ligações  (3.4%)
  - Serviço negativo:           128 ligações  (6.7%)
  - Faturamento zerado:          17 ligações  (0.9%)
  - Serviço extremo:              1 ligação   (0.1%)

  Flags Comportamentais:
  - Consumo zero recorrente:    124 ligações  (6.5%)
  - Alta variabilidade (CV>1.5): 89 ligações  (4.7%)
  - Todos os meses zerados:      10 ligações  (0.5%)
  - Subfaturamento:               0 ligações  (0.0%)
  - Hidrômetro antigo:            0 ligações  (0.0%)

  SCORE DE RISCO CONSOLIDADO:
  - Alto risco  (score >= 3):    27 ligações  (1.4%)
  - Risco médio (score >= 2):   120 ligações  (6.3%)
  - Sem anomalia (score = 0): 1.552 ligações (81.2%)

BASE FINAL EXPORTADA
-------------------------------------------------------------------------
  Colunas após tratamento: 161
  Arquivos gerados:
  - base_completa_tratada.xlsx   (1.912 registros)
  - base_ativas_tratada.xlsx     (1.815 registros)
  - base_nao_ativas_tratada.xlsx (   97 registros)

