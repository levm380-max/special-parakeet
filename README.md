# Anime AI Finder

**Anime AI Finder** is an educational machine learning project that recommends anime based on the semantic meaning of a selected query.

The project uses precomputed embeddings instead of downloading a model during execution.  
This makes the notebook suitable for Kaggle environments without internet access.

---

## Project overview

Anime AI Finder is a semantic recommendation system.

The user selects a query from a prepared list, for example:

```text
sports anime about teamwork and friendship
```

The system compares the embedding of this query with embeddings of anime descriptions and returns the most relevant recommendations.

Each recommendation includes:

- anime title;
- genres;
- rating;
- type;
- number of episodes;
- semantic similarity;
- final ranking score;
- short synopsis.

---

## Main idea

The core idea of the project is:

```text
Text → Embedding → Cosine Similarity → Ranking → Recommendations
```

Instead of searching by exact words, the system searches by meaning.

For example, a query about friendship and teamwork can return relevant sports anime even if the exact words from the query are not repeated in the synopsis.

---

## Important note

This version of the project does **not** download or load a machine learning model.

The project works only with precomputed files:

```text
clean_anime_dataset_v2.csv
anime_embeddings_v2.npy
query_queries.csv
query_embeddings.npy
```

Because of this, the project:

- works without Kaggle internet access;
- does not require `SentenceTransformer`;
- does not require model downloading;
- uses only prepared query embeddings;
- allows users to choose queries from a fixed list.

The user cannot type completely new custom queries in this version.  
Only prepared queries from `query_queries.csv` are supported.

---

## Kaggle datasets

To run the notebook, add both Kaggle datasets through **Add Data**.

### 1. Anime recommender artifacts

```text
https://www.kaggle.com/datasets/leodaiser/anime-recommender-artifacts
```

Required files from this dataset:

```text
clean_anime_dataset_v2.csv
anime_embeddings_v2.npy
```

### 2. Query embeddings

```text
https://www.kaggle.com/datasets/leodaiser/query-embeddings
```

Required files from this dataset:

```text
query_queries.csv
query_embeddings.npy
```

---

## Required files

The notebook expects the following files to be available inside `/kaggle/input`:

```text
clean_anime_dataset_v2.csv
anime_embeddings_v2.npy
query_queries.csv
query_embeddings.npy
```

The notebook automatically searches for these files inside Kaggle input folders.

---

## Dataset files

### `clean_anime_dataset_v2.csv`

This file contains the cleaned anime dataset.

Main columns:

| Column | Description |
|---|---|
| `Name` | Anime title |
| `Genres` | Anime genres |
| `Synopsis` | Anime description |
| `Score` | User rating |
| `Type` | Anime type: TV, Movie, OVA, etc. |
| `Episodes` | Number of episodes |
| `text` | Combined text used for embedding generation |

### `anime_embeddings_v2.npy`

This file contains precomputed embeddings for all anime from `clean_anime_dataset_v2.csv`.

The order of rows in `clean_anime_dataset_v2.csv` must match the order of embeddings in `anime_embeddings_v2.npy`.

### `query_queries.csv`

This file contains the prepared list of available queries.

Example structure:

| query_id | query |
|---|---|
| 0 | dark cyberpunk anime with philosophy |
| 1 | romantic school comedy |
| 2 | sports anime about teamwork and friendship |

### `query_embeddings.npy`

This file contains precomputed embeddings for all queries from `query_queries.csv`.

The order of rows in `query_queries.csv` must match the order of embeddings in `query_embeddings.npy`.

---

## How to run on Kaggle

1. Open the notebook in Kaggle.
2. Click **Add Data**.
3. Add the first dataset:

```text
https://www.kaggle.com/datasets/leodaiser/anime-recommender-artifacts
```

4. Add the second dataset:

```text
https://www.kaggle.com/datasets/leodaiser/query-embeddings
```

5. Run all notebook cells.
6. Choose a query ID from the displayed query list.
7. Run the final recommendation cell.

---

## Example usage

The final notebook displays a list of prepared queries.

Example:

```text
0. dark cyberpunk anime with philosophy
1. romantic school comedy
2. sports anime about teamwork and friendship
3. fantasy adventure with magic
```

To get recommendations, choose a query ID:

```python
SELECTED_QUERY_ID = 2

show_recommendations_by_query_id(
    query_id=SELECTED_QUERY_ID,
    top_k=5,
    min_score=7.0,
    anime_type=None,
    genre_filter=None,
    genre_mode="any",
    alpha=0.8
)
```

The system will return top-5 anime recommendations for the selected query.

---

## Recommendation parameters

The recommendation function supports several parameters.

| Parameter | Description |
|---|---|
| `query_id` | ID of the selected prepared query |
| `top_k` | Number of recommendations |
| `min_score` | Minimum anime rating |
| `anime_type` | Anime type filter: `TV`, `Movie`, `OVA`, or `None` |
| `genre_filter` | Genre filter, for example `["Sports"]` |
| `genre_mode` | `any` or `all` |
| `alpha` | Balance between semantic similarity and anime rating |

---

## Ranking formula

The system calculates two values:

```text
similarity_norm
score_norm
```

Then it combines them into a final ranking score:

```text
final_score = alpha * similarity_norm + (1 - alpha) * score_norm
```

Where:

- `similarity_norm` measures how close the anime is to the selected query;
- `score_norm` is the normalized anime rating;
- `alpha` controls the balance between meaning and rating.

Examples:

```text
alpha = 1.0 → only semantic similarity matters
alpha = 0.8 → semantic similarity is more important than rating
alpha = 0.5 → semantic similarity and rating have equal weight
```

---

## How the system works

The system works in several steps:

```text
1. Load cleaned anime dataset
2. Load precomputed anime embeddings
3. Load prepared query list
4. Load precomputed query embeddings
5. Select query by query_id
6. Compare selected query embedding with all anime embeddings
7. Calculate cosine similarity
8. Apply filters
9. Calculate final_score
10. Return top-k recommendations
```

---

## Cosine similarity

Cosine similarity is used to compare the selected query embedding with each anime embedding.

Higher similarity means the anime is closer to the query by meaning.

```text
similarity close to 1 → very similar
similarity close to 0 → weakly related
```

---

## Project structure

Recommended GitHub repository structure:

```text
anime-ai-finder/
│
├── README.md
├── requirements.txt
├── .gitignore
│
└── notebooks/
    └── anime_ai_finder_final_no_model.ipynb
```

Large data files are not stored directly in the GitHub repository.  
They are stored in Kaggle datasets and connected through **Add Data**.

---

## requirements.txt

For the final no-model version, the project requires only basic libraries:

```text
pandas
numpy
ipython
```

No `sentence-transformers` package is required for the final notebook.

---

## .gitignore

Recommended `.gitignore`:

```gitignore
__pycache__/
.ipynb_checkpoints/
*.pyc
*.pyo
*.pyd

# Local data files
data/
embeddings/
*.npy
*.csv

# System files
.DS_Store
Thumbs.db
```

---

## Educational purpose

This project was created as a final educational project for a Python Pro course.

The project demonstrates:

- working with tabular data;
- working with embeddings;
- semantic search;
- cosine similarity;
- filtering and ranking;
- recommendation systems;
- final project packaging;
- publishing a project on GitHub;
- using Kaggle datasets as external data storage.

---

## Course project stages

The project was developed in four stages.

### Lesson 1 — Semantic search

Students learned:

- what embeddings are;
- how text can be represented as numbers;
- how cosine similarity works;
- how to find anime similar to a query by meaning.

### Lesson 2 — Improved recommendation system

Students added:

- rating filters;
- genre filters;
- anime type filters;
- final ranking score;
- better recommendation logic.

### Lesson 3 — Experiments and evaluation

Students learned:

- how parameters affect recommendations;
- how to compare different configurations;
- how to manually evaluate results;
- how to use Precision@5.

### Lesson 4 — Final project

Students created the final usable version:

- prepared query selection;
- no-model execution;
- clean recommendation output;
- final demonstration;
- GitHub-ready project structure.

---

## Limitations

Current limitations:

- users can select only prepared queries;
- arbitrary custom text queries are not supported in the final no-model version;
- recommendations depend on the quality of anime descriptions;
- the system does not learn personal user preferences;
- the system does not use user history;
- the project is not a production web service;
- embeddings must be prepared beforehand.

---

## Possible improvements

Possible future improvements:

- add more prepared queries;
- support custom queries with a locally stored model;
- add user ratings;
- add favorite anime list;
- add posters and links;
- add Streamlit interface;
- add FAISS for faster vector search;
- add multilingual queries;
- add personalized recommendations;
- add hybrid recommendation logic;
- deploy as a small web application.

---

## Example final demonstration

A typical final demonstration looks like this:

```python
show_query_list()
```

Then select a query:

```python
SELECTED_QUERY_ID = 14
```

Run recommendations:

```python
show_recommendations_by_query_id(
    query_id=SELECTED_QUERY_ID,
    top_k=5,
    min_score=7.0,
    anime_type=None,
    genre_filter=None,
    genre_mode="any",
    alpha=0.8
)
```

The notebook displays a clean recommendation block with titles, genres, ratings, similarity scores and descriptions.

---

## Author

Educational project by Leo Daiser.

---

## License

This project is created for educational purposes.
