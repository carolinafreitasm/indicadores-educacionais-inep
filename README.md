# Desigualdade Educacional Municipal no Brasil (2010–2022)

Análise exploratória, modelo preditivo e mapas geográficos interativos sobre indicadores educacionais municipais brasileiros, com foco na Região Metropolitana de Curitiba.

🗺️ **[Ver mapa interativo (clique aqui)](https://carolinafreitasm.github.io/indicadores-educacionais-inep/mapa_rmc_aprovacao_ef_em_interativo.html)**

![Evolução da aprovação na RM Curitiba](images/evolucao_aprovacao_rmc.gif)

## Sobre o projeto

Este projeto consulta dados públicos do INEP, disponibilizados pela [Base dos Dados](https://basedosdados.org/), diretamente via SQL no Google BigQuery (sem download manual de CSV). Os dados são tratados em Python (pandas), explorados com visualizações, usados para treinar um modelo preditivo, e por fim mapeados geograficamente com geometrias municipais reais.

**Pergunta de pesquisa:** quais fatores estruturais (alunos por turma, carga horária, localização, região) mais influenciam a taxa de aprovação escolar no Brasil, e como essa realidade varia entre municípios da Região Metropolitana de Curitiba ao longo do tempo?

## Pipeline

1. **Extração** — consulta SQL direta no BigQuery via [`basedosdados`](https://pypi.org/project/basedosdados/), trazendo indicadores educacionais de todos os municípios brasileiros (2010–2022)
2. **Enriquecimento** — junção com dados de UF e mapeamento manual de região (Norte, Nordeste, Sul, Sudeste, Centro-Oeste)
3. **Limpeza** — tratamento de valores nulos, concentrados principalmente no Ensino Médio rural (municípios pequenos frequentemente não oferecem essa etapa)
4. **EDA** — evolução temporal, comparação entre regiões, comparação urbano/rural, correlação entre alunos por turma e aprovação
5. **Modelo preditivo** — Random Forest Regressor prevendo a taxa de aprovação no Ensino Médio a partir de variáveis estruturais
6. **Mapas geográficos** — geometrias municipais reais (polígonos) consultadas via BigQuery, visualizadas como mapa estático, mapa interativo (Plotly + Mapbox) e GIF animado mostrando a evolução ano a ano

## Principais achados

- A taxa de aprovação no Ensino Médio passou de **81,4%** em 2010 para **88,1%** em 2022.
- A região **Sudeste** apresentou a maior taxa de aprovação no Ensino Médio (**90,8%**) e a menor taxa de abandono (**3,8%**); a região **Norte** com **84,9%** de aprovação e **8,2%** de abandono.
- Municípios com mais alunos por turma (`atu_em`) tendem a ter menor aprovação: a correlação entre as duas variáveis foi de **-0,24** no Ensino Médio e **-0,17** no Fundamental, turmas mais cheias, em média, associam-se a pior desempenho.
- O modelo preditivo (Random Forest) atingiu **MAE de 6,41 pontos percentuais** e **R² de 0,377**; explica parte relevante da variação, mas indica que fatores não capturados pelas variáveis disponíveis (qualidade de ensino, contexto socioeconômico do município, etc.) também têm peso considerável.
- A variável de maior peso na previsão foi **horas-aula diária no Ensino Médio (`had_em`)**, seguida por **alunos por turma (`atu_em`)**. Juntas, essas duas variáveis estruturais superam até a região geográfica como preditoras da taxa de aprovação.

## Resultados visuais

| Mapa estático (RM Curitiba) | Importância das variáveis |
|---|---|
| ![Mapa RMC](images/mapa_rmc_aprovacao_estatico.png) | ![Feature importance](images/feature_importance.png) |

## Tecnologias utilizadas

Python, pandas, scikit-learn, matplotlib, seaborn, geopandas, Plotly, BigQuery (via `basedosdados`), Pillow.

## Como reproduzir

```bash
git clone https://github.com/carolinafreitasm/indicadores-educacionais-inep.git
cd indicadores-educacionais-inep
pip install -r requirements.txt
```
Abra `notebook/analise_indicadores_educacionais.ipynb`, substitua `billing_id` pelo seu Project ID do Google Cloud (gratuito, [crie aqui](https://console.cloud.google.com/)), e rode todas as células.

## Limitações

Os dados são agregados por município, não por escola ou aluno individual. Isso limita a granularidade da análise. Correlação entre variáveis (ex: alunos por turma e aprovação) não implica causalidade. A concentração de valores nulos em municípios pequenos pode introduzir viés nas análises agregadas e no modelo preditivo.

## Próximos passos

Cruzar com dados do IDEB para avaliar correlação entre infraestrutura e desempenho; testar outros modelos (XGBoost, regressão linear como baseline); expandir a análise geográfica para todo o estado do Paraná ou nível nacional.

## Fonte dos dados

[Base dos Dados](https://basedosdados.org/) — Indicadores Educacionais (INEP) e Diretório de Municípios do Brasil.

---
.
