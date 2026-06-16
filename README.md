# Global YouTube Statistics — Entrega 2: Análise Exploratória (EDA)

> Continuação do pipeline de Ciência de Dados iniciado na Entrega 1.  
> **Grupo:**  Eduardo Araujo · Eduardo Marson · Hugo Viana ·José Vitor Marson · Maria Clara


##  Visão Geral

Este notebook realiza a **Análise Exploratória de Dados (EDA) aprofundada** sobre o dataset *Global YouTube Statistics*, utilizando como ponto de partida o arquivo `df_std.csv` produzido na Entrega 1 (dados normalizados via StandardScaler, com transformação log1p e feature engineering aplicados).

O objetivo desta entrega é **preparar e fundamentar as decisões de clusterização** que serão implementadas na Entrega 3, respondendo às seguintes questões:

- Quais são as características estatísticas das 13 features selecionadas?
- Como as variáveis se correlacionam entre si?
- Quantas dimensões são realmente informativas (PCA)?
- Existem agrupamentos naturais nos dados (t-SNE / UMAP)?
- Qual métrica de distância é mais adequada para a clusterização futura?

---

##  Dependência com a Entrega 1

**Este notebook depende diretamente da Entrega 1.** O arquivo `df_std.csv` gerado no final do notebook `GlobalYoutubeStatistics.ipynb` é o input obrigatório desta entrega.

| Entrega | Notebook | Output principal |
|---------|----------|-----------------|
| Entrega 1 | `GlobalYoutubeStatistics.ipynb` | `df_std.csv` ← **necessário aqui** |
| **Entrega 2** | `entrega2_EDA_CD2__1_.ipynb` | Visualizações + fundamentos para Entrega 3 |

> Se você ainda não rodou a Entrega 1, execute-a primeiro e obtenha o `df_std.csv` antes de prosseguir.

---

##  Dataset de Entrada

| Propriedade | Valor |
|-------------|-------|
| Arquivo de entrada | `df_std.csv` (gerado pela Entrega 1) |
| Registros | 995 canais |
| Features utilizadas nesta entrega | 13 colunas numéricas |
| Escalonamento | StandardScaler (μ = 0, σ = 1) + log1p |

**As 13 features analisadas nesta entrega:**

| Feature | Origem | Descrição |
|---------|--------|-----------|
| `subscribers` | Original (log1p) | Total de inscritos |
| `video views` | Original (log1p) | Total de visualizações |
| `uploads` | Original (log1p) | Número de vídeos publicados |
| `video_views_for_the_last_30_days` | Original (log1p) | Views nos últimos 30 dias |
| `highest_monthly_earnings` | Original (log1p) | Ganhos mensais máximos |
| `earnings_spread` | Derivada (log1p) | Amplitude de monetização anual |
| `subscribers_for_last_30_days` | Original (log1p) | Novos inscritos nos últimos 30 dias |
| `Population` | Original (log1p) | População do país de origem |
| `views_per_subscriber` | Derivada (log1p) | Eficiência de consumo |
| `views_per_upload` | Derivada (log1p) | Performance média por vídeo |
| `subscriber_growth_rate` | Derivada (log1p) | Taxa de crescimento relativo |
| `channel_age` | Derivada | Idade do canal em anos |
| `urban_ratio` | Derivada | Taxa de urbanização do país |

**Features removidas em relação à Entrega 1 (antes de entrar neste notebook):**

| Feature removida | Motivo |
|-----------------|--------|
| `rank`, `video_views_rank`, `channel_type_rank` | Identificadores ordinais redundantes |
| `Youtuber`, `Title`, `Abbreviation` | Não numéricas |
| `created_year`, `created_month`, `created_date` | Redundância com `channel_age` |
| `lowest_yearly_earnings`, `highest_yearly_earnings`, `lowest_monthly_earnings` | Alta correlação Spearman (|r| ≥ 0.99) com `earnings_spread` |
| `Urban_population` | Correlação r = 0.995 com `Population` |
| `Latitude`, `Longitude` | Baixo poder explicativo numérico |

---

## Estrutura do Projeto

```
.
├── GlobalYoutubeStatistics.ipynb        # Entrega 1 (necessária)
├── entrega2_EDA_CD2__1_.ipynb           # Este notebook (Entrega 2)
├── df_std.csv                            # Input: gerado pela Entrega 1
├── Global YouTube Statistics.csv         # Dataset original (Kaggle)
├── README.md                             # README da Entrega 1
└── README_Entrega2.md                    # Este arquivo
```

---

## Requisitos e Instalação

### Python

Recomenda-se **Python 3.9+**.

### Dependências

```bash
pip install numpy pandas matplotlib seaborn scikit-learn umap-learn scipy
```

**Lista detalhada de pacotes:**

| Pacote | Versão testada | Finalidade |
|--------|---------------|------------|
| `numpy` | ≥ 1.23 | Operações numéricas |
| `pandas` | ≥ 1.5 | Manipulação de DataFrames |
| `matplotlib` | ≥ 3.6 | Visualizações estáticas e 3D |
| `seaborn` | ≥ 0.12 | Heatmaps, boxplots, clustermap |
| `scikit-learn` | ≥ 1.1 | PCA, t-SNE, métricas de distância |
| `umap-learn` | ≥ 0.5 | Redução de dimensionalidade UMAP |
| `scipy` | ≥ 1.9 | Correlação de Spearman, hierarquia |

> **Atenção para o UMAP:** o pacote `umap-learn` é instalado automaticamente no Google Colab pela primeira célula do notebook (`!pip install umap-learn --quiet`). Em ambiente local, instale manualmente antes de abrir o notebook.

### Ambientes compatíveis

| Ambiente | Suporte | Observação |
|----------|---------|------------|
| **Google Colab** | ✅ Recomendado | `umap-learn` instalado automaticamente; upload do CSV via `files.upload()` |
| **Jupyter Lab / Notebook** | ✅ | Instale `umap-learn` antes; coloque `df_std.csv` na mesma pasta |
| **VS Code (Jupyter)** | ✅ | Idem ao Jupyter local |

---

##  Como Reproduzir

Siga os passos na ordem indicada:

---

### Passo 1 — Executar a Entrega 1

Se ainda não fez, rode o notebook `GlobalYoutubeStatistics.ipynb` até o final. Ele produzirá o arquivo `df_std.csv` necessário para esta entrega. Consulte o `README.md` da Entrega 1 para instruções detalhadas.

---

### Passo 2 — Instalar as dependências

```bash
pip install numpy pandas matplotlib seaborn scikit-learn umap-learn scipy
```

---

### Passo 3 — Abrir o notebook da Entrega 2

**Google Colab:**
1. Acesse [colab.research.google.com](https://colab.research.google.com)
2. Faça upload do arquivo `entrega2_EDA_CD2__1_.ipynb`
3. A Célula 0 instalará `umap-learn` automaticamente

**Localmente (Jupyter):**
```bash
jupyter notebook entrega2_EDA_CD2__1_.ipynb
```

---

### Passo 4 — Carregar o CSV gerado na Entrega 1

Na **Célula 3** (Carregamento de dados), o notebook solicitará o upload do arquivo `df_std.csv`:

```python
from google.colab import files
uploaded = files.upload()  # selecione df_std.csv
```

> Em ambiente local, basta ter o `df_std.csv` na mesma pasta do notebook — ajuste o caminho na linha de leitura se necessário.

---

### Passo 5 — Executar todas as células em ordem

No Colab: **Runtime → Run All**  
No Jupyter: **Kernel → Restart & Run All**

> O notebook é projetado para execução linear, de cima para baixo. As células posteriores dependem das variáveis criadas nas células anteriores (especialmente `X`, `X_clean`, `FEATURES`).

---

### Passo 6 — Interpretar os outputs

Cada seção gera visualizações inline (gráficos) e outputs de texto. Não há arquivos CSV gerados neste notebook — os resultados são as visualizações e análises que fundamentam a Entrega 3.

---

## 🔬 Etapas do Notebook

### Etapa 0 — Setup e Carregamento

- Instalação do `umap-learn` (automaticamente no Colab)
- Importação de todas as bibliotecas necessárias
- Upload/leitura do `df_std.csv` (saída da Entrega 1)
- Definição da lista `FEATURES` com as 13 colunas numéricas selecionadas
- Criação do DataFrame `X` contendo apenas essas 13 features

---

### Etapa 1 — Estatísticas Descritivas

**Objetivo:** Caracterizar numericamente cada variável para orientar decisões posteriores.

Métricas calculadas por feature:

| Métrica | Significado |
|---------|-------------|
| `count`, `mean`, `std` | Estatísticas básicas |
| `min`, `25%`, `50%`, `75%`, `max` | Percentis |
| `skewness` | Assimetria da distribuição |
| `kurtosis` | Achatamento da distribuição |
| `cv (%)` | Coeficiente de variação = std / |média| × 100 |

Também é gerado um resumo classificando as features em:
- **Assimétricas positivas** (skewness > 1): potencialmente problemáticas para algoritmos lineares
- **Aproximadamente simétricas** (|skewness| ≤ 1): bem comportadas para distância euclidiana

---

### Etapa 2 — Matrizes de Correlação: Pearson e Spearman

**Objetivo:** Identificar relações lineares e monotônicas entre features, e detectar redundâncias remanescentes.

Duas matrizes são calculadas e exibidas lado a lado:

| Método | Tipo de relação medida | Sensibilidade a outliers |
|--------|----------------------|--------------------------|
| **Pearson** | Correlação linear | Alta — distorcido por outliers extremos |
| **Spearman** | Correlação monotônica (postos) | Baixa — robusto a outliers |

Adicionalmente, é calculada a **divergência entre Pearson e Spearman** (|Δr|) para cada par de features. Divergências acima de 0.1 indicam relações não-lineares ou influência de outliers, confirmando a preferência pelo Spearman neste dataset.

> **Por que Spearman como padrão?**  
> As variáveis apresentam alta assimetria mesmo após log1p. Outliers como T-Series (245M inscritos) e MrBeast (166M) distorcem o Pearson. O Spearman, baseado em postos, é invariante a transformações monotônicas e mais adequado aqui.

---

### Etapa 3 — Histogramas das Features Selecionadas

**Objetivo:** Verificar visualmente a distribuição de cada variável após o pré-processamento.

- Grade de histogramas (3 colunas × N linhas)
- Cada histograma inclui linha de densidade KDE
- Cores diferenciadas por feature (paleta `muted` do seaborn)
- Distribuições muito assimétricas indicam que algoritmos sensíveis à escala (K-Means com Euclidiana) podem ser prejudicados

---

### Etapa 4 — Boxplots das Features Selecionadas

**Objetivo:** Evidenciar outliers individuais e a dispersão interquartil de cada feature.

Dois tipos de boxplot são gerados:

**Boxplots individuais** (grade, um por feature): localizam visualmente outliers e confirmam os já documentados na Entrega 1 (T-Series, MrBeast, Cocomelon).

**Boxplot comparativo unificado** (todos no mesmo eixo): possível porque `df_std` já está padronizado (μ = 0, σ = 1), permitindo comparar a dispersão entre features diretamente.

> **Nota sobre outliers:** mantidos intencionalmente (conforme decisão da Entrega 1). São canais reais com comportamento legítimo no topo do mercado.

---

### Etapa 5 — Redução de Dimensionalidade: PCA

**Objetivo:** Quantificar a variância explicada e identificar quantas dimensões são realmente informativas.

**Por que PCA aqui?**

1. **Guia para a clusterização:** define quantos componentes usar como input para K-Means / DBSCAN na Entrega 3
2. **Elimina multicolinearidade residual:** mesmo após remover features correlacionadas na Entrega 1, correlações moderadas persistem
3. **Reduz ruído:** componentes com baixa variância provavelmente capturam ruído

**Outputs desta seção:**

| Visualização / Output | Descrição |
|----------------------|-----------|
| Tabela de variância explicada | % por componente e acumulada |
| **Scree plot (cotovelo)** | Barras da variância por componente + ponto de inflexão |
| **Curva de variância acumulada** | Linha com marcação dos limiares 80% e 90% |
| **Heatmap de loadings** | Contribuição de cada feature nos primeiros N componentes |
| **Scatter 2D** | Projeção PC1 × PC2 de todos os 995 canais |

**Resultado obtido:** 6 componentes capturam 80% da variância (redução de 13 → 6 dimensões, compressão de 54%).

**Recomendação para a Entrega 3:** testar clusterização com as 13 features originais normalizadas e também com as 6 componentes do PCA, comparando as métricas de avaliação.

---

### Etapa 6 — Visualização 2D: t-SNE e UMAP

**Objetivo:** Detectar estruturas não-lineares (clusters irregulares, manifolds curvos) que o PCA não captura.

**Por que métodos não-lineares?**  
O PCA é uma projeção linear. Se os dados residem em uma variedade curva (manifold), o PCA "achata" essa estrutura e esconde agrupamentos reais.

**t-SNE** (dois valores de perplexidade: 15 e 30):

| Hiperparâmetro | Efeito |
|---------------|--------|
| `perplexity = 15` | Enfatiza estrutura local (clusters menores e mais densos) |
| `perplexity = 30` | Captura estrutura global com mais suavidade |
| `n_iter = 1000` | Número de iterações para convergência |
| `init = 'pca'` | Inicialização estável via PCA (recomendada) |

**UMAP** (dois valores de n_neighbors: 15 e 30):

| Hiperparâmetro | Efeito |
|---------------|--------|
| `n_neighbors = 15` | Estrutura local detalhada |
| `n_neighbors = 30` | Estrutura global mais preservada |
| `min_dist = 0.1` | Compactação intra-cluster |

**t-SNE 3D** (perplexidade 30): visualização interativa via matplotlib para explorar estrutura volumétrica.

---

### Etapa 7 — Análise de Impacto das Métricas de Proximidade

**Objetivo:** Determinar qual métrica de distância é mais adequada para o dataset, fundamentando a escolha na Entrega 3.

Três métricas avaliadas:

| Métrica | Fórmula | Sensibilidade | Indicada quando |
|---------|---------|---------------|-----------------|
| **Euclidiana** | √Σ(xᵢ - yᵢ)² | Alta a outliers | Dados normalizados, sem assimetria extrema |
| **Manhattan** | Σ|xᵢ - yᵢ| | Moderada | Dados com skewness residual; K-Medoids |
| **Cosseno** | 1 - (x·y / \|x\|\|y\|) | Baixa à magnitude | Perfil relativo mais importante que valores absolutos |

**Análises realizadas:**

- **Distribuição das distâncias par-a-par** (histogramas + KDE): compara a "separabilidade" das métricas
- **Heatmaps das matrizes de distância** (primeiros 60 canais): visualiza padrões de similaridade
- **Clustermaps com reordenação hierárquica** (todos os 995 canais): revela quais métricas formam blocos mais coesos
- **Correlação de Spearman entre rankings** de distância: mede o quanto as métricas concordam entre si

---

### Etapa 8 — Resumo e Justificativas

Consolidação de todas as decisões tomadas nesta entrega, organizadas em quatro blocos:

**8.1 Features selecionadas:** justificativa para as 13 features mantidas e as descartadas.

**8.2 Pearson vs Spearman:** argumentação para adoção do Spearman como métrica principal de correlação (assimetria, outliers, invariância monotônica).

**8.3 PCA — Dimensões retidas:** 6 componentes capturam 80% da variância; recomendação de testar ambas as configurações (13 originais vs 6 componentes) na Entrega 3.

**8.4 Métrica de distância:** síntese das vantagens de cada métrica; decisão final adiada para a Entrega 3 com base em Silhouette Score, Davies-Bouldin e Calinski-Harabász.

---

## Decisões Técnicas

| Decisão | Alternativa considerada | Motivo da escolha |
|---------|------------------------|-------------------|
| **13 features** como input | Todas as 23 da Entrega 1 | Remoção de redundâncias (|Spearman| ≥ 0.99) e identificadores não numéricos |
| **Spearman** como correlação principal | Pearson | Distribuições assimétricas e outliers extremos distorcem o Pearson |
| **80% de variância** como limiar do PCA | 90% ou 95% | Equilíbrio entre compressão (54%) e retenção de informação |
| Testar **t-SNE com 2 perplexidades** (15 e 30) | Apenas uma configuração | Perplexidade afeta a escala da estrutura capturada; comparar ambas reduz viés |
| Testar **UMAP com 2 n_neighbors** (15 e 30) | Apenas uma configuração | Idem ao t-SNE — captura estruturas em escalas diferentes |
| Testar **3 métricas de distância** (Euclidiana, Manhattan, Cosseno) | Apenas Euclidiana | Diferentes métricas revelam diferentes aspectos da similaridade; decisão fundamentada para Entrega 3 |
| **Preservar outliers** para PCA/t-SNE/UMAP | Remover antes da visualização | Canais extremos (T-Series, MrBeast) são parte legítima do espaço de dados |

---

## Artefatos Gerados / Saídas Esperadas

Este notebook **não gera arquivos CSV**. Todos os outputs são **visuais e textuais inline**:

| Seção | Output gerado |
|-------|--------------|
| 1 | Tabela de estatísticas descritivas com skewness, kurtosis e CV |
| 2 | Duas matrizes de correlação (Pearson e Spearman) + tabela de divergências |
| 3 | Grade de histogramas com KDE (uma por feature) |
| 4 | Grid de boxplots individuais + boxplot comparativo unificado |
| 5 | Scree plot, curva de variância acumulada, heatmap de loadings, scatter 2D PCA |
| 6 | Scatter 2D t-SNE (perp. 15 e 30), scatter 2D UMAP (nn 15 e 30), scatter 3D t-SNE |
| 7 | Histogramas de distribuição de distâncias, heatmaps, clustermaps, correlação entre métricas |
| 8 | Células markdown de resumo e justificativas |

---

## Integrantes

| Nome |
|------|
| Eduardo de Oliveira Araujo |
| Eduardo Oliveira Marson |
| Hugo Alves Viana |
| José Vitor Oliveira Marson |
| Maria Clara Sailva Borges|


---
