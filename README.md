# mvp-vmm-machineLearning-analytics
MVP Machine Learning &amp; Analytics - Aluna Viviane M Matos - PUC-Rio

# MVP: Machine Learning - Clusterização Não Supervisionada - Plataforma de Streaming Disney+

MVP de **recomendação de títulos** usando **aprendizado não supervisionado** com **Clusterização Hierárquica (Ward)**,
baseado **apenas em dados tabulares** `release_year` (ano) e `rating` (classificação indicativa) do dataset *Disney+ Movies and TV Shows* (Kaggle).
O objetivo é formar clusters coerentes por época/faixa etária e, a partir deles, sugerir títulos **similares** (mesmo cluster + vizinhança no espaço transformado).

> **Por que tão simples?** Mantemos **baixo custo**, **explicabilidade** e **reprodutibilidade** (bom ponto de partida).  
> **Métricas internas:** Silhouette↑, Calinski–Harabasz↑, Davies–Bouldin↓.

---

## 📁 Estrutura do repositório

```
.
├── MVP_ML_&_Analytics_Simples_VMM.ipynb  # Notebook principal (Colab-ready)
├── artifacts/
│   ├── preprocess_pipeline.joblib           # Pipeline sklearn salvo
│   └── clustering_artifacts.joblib          # Parâmetros do corte/centróides
└── README.md                                # Este arquivo
```

> Os artefatos são gerados ao executar a seção **13. Salvando artefatos** no notebook.

---

## 🧰 Ambiente e dependências

- Python 3.8+
- pandas, numpy, scikit-learn, scipy, matplotlib, joblib, psutil, pyyaml (opcional)

Instalação rápida (ambiente local):

```bash
pip install -U pandas numpy scikit-learn scipy matplotlib joblib psutil pyyaml
```

> No Google Colab, esses pacotes geralmente já estão disponíveis. Se faltar algo, há uma célula de instalação no início do notebook.

---

## 🗂️ Dataset

- **Fonte:** Kaggle — *Disney+ Movies and TV Shows* (utilize a planilha principal `disney_plus_titles.csv`).
- **Escopo do MVP:** apenas **filmes** (`type == 'Movie'`, se a coluna existir).
- **Features usadas:** `release_year` (numérica), `rating` (categórica One-Hot).  
- **Metadado:** `title` (para exibir recomendações).

O dataset utilizado é o [`disney_plus_titles.csv`](https://www.kaggle.com/datasets/shivamb/disney-movies-and-tv-shows), disponível no Kaggle.
---

### Visualizações de resultados (scatter)
- **6.2** Baseline K-Means — PCA 2D (cores por cluster)  
- **8.1** Hierárquica (TRAIN+VAL) — PCA 2D (cores por cluster)  

> A redução PCA é usada **somente para visualização**, não para treinar os modelos.

---

## 🧪 Metodologia (resumo)

- **Pré-processamento**: imputação (mediana/mais frequente), One-Hot para `rating`, `StandardScaler` para `release_year`.
- **Baseline**: K-Means (k=3) apenas para referência de métricas internas.
- **Modelo alvo**: **Hierárquico (Ward)**; seleção de `k` por varredura (`2..8`) com Silhouette/CH/DB.
- **Split**: `train` (70%), `val` (15%), `test` (15%) para evitar sobreajuste na escolha de `k` (mesmo em não supervisionado).
- **Atribuições**: rótulos em *val* e *test* por **centróides** calculados em *train* / *train+val*.
- **Recomendador**: dentro do **mesmo cluster**, retorna **top‑N vizinhos** por distância euclidiana no espaço transformado.

---

## 📊 Métricas internas

- **Silhouette** (↑) — coesão/separação dos clusters  
- **Calinski–Harabasz** (↑) — separação inter vs intra-cluster  
- **Davies–Bouldin** (↓) — razão média de similaridade intra-inter  

As métricas são reportadas para **train/val/test** (com as ressalvas do cenário não supervisionado).

---

## 💾 Artefatos e Deploy

Executando a seção **13** do notebook:
- `artifacts/preprocess_pipeline.joblib`
- `artifacts/clustering_artifacts.joblib` (contém `k`, `linkage`, `metric`, `centroids`)

Uso típico em produção (**pseudo-passos**):
1. Carregar artefatos (`joblib.load(...)`).
2. Transformar novos dados com o **pipeline** salvo.
3. Atribuir rótulos por **centróides** (ou por corte hierárquico consistente).
4. Para recomendação, buscar **títulos do mesmo cluster** e ranquear por distância.

---

## 🔁 Reprodutibilidade

- **Seeds fixas** (`SEED = 42`) e controle de versões (`pip freeze`) sugeridos.  
- **Pipelines `sklearn`** para evitar vazamento e garantir reprodutibilidade.  
- **Config externo** (YAML/JSON) pode armazenar `k`, `linkage`, etc.
---

## ⚠️ Limitações e próximos passos

- Apenas **2 features** → recomendações **coarse**.  
- `rating` varia por política/região; `release_year` não captura gênero/tema.  
- Métricas internas não garantem impacto de negócio — recomenda-se **A/B testing**.

**Evoluções sugeridas:**
- Incluir **gênero/duração/país** (multihot) e avaliar **HDBSCAN**.  
- Recomendação **híbrida** (cluster + similaridade contínua).  
- Usar **embeddings** de texto (sinopses) para enriquecer o espaço.

---

## 📜 Licença e ética

- Verifique a **licença do dataset** no Kaggle e o uso conforme os termos.  
- Este projeto usa apenas **metadados** (ano/rating/título) e não trata dados sensíveis.

---

## 🙌 Agradecimentos

- Comunidade Kaggle pelo dataset.  
- Equipe scikit‑learn / scipy / numpy / pandas pela base técnica.
