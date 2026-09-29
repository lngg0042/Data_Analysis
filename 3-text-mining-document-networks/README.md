# Text Mining & Network Analysis of a Multi-Genre Corpus (R)

An NLP pipeline in R that builds a corpus of 20 documents from four genres (**music, news, recipes and
film/TV reviews**) and asks whether the documents can be grouped by genre from their words alone. It uses
hierarchical clustering, sentiment analysis, and document, word and bipartite networks.

![Document network](figures/T6_Improved.png)

## Key findings

- **Clustering recovered genre 75% of the time.** Cosine-distance hierarchical clustering separated
  recipes and reviews perfectly (100%), but mixed up music and news (40% / 60%). The music articles were
  news-style pieces (tour announcements, releases) with the same reporting vocabulary: *said, year, new,
  first*.
- **Genre didn't change overall sentiment.** Net sentiment (Harvard-IV dictionary) was nearly the same
  across genres (one-way ANOVA F = 0.008, p = 0.999). Looking at positive and negative words separately
  showed more: reviews used the most positive *and* negative language, while music articles were the most
  neutral. One news article (a court case) was the only document with negative net sentiment, and it
  stands out in the network.
- **Networks show writing style, not just topic.** Louvain community detection on the document network
  found two groups: *procedural/evaluative* writing (recipes + reviews) and *journalistic* writing
  (news + music). The word network split the vocabulary the same way (*make, use, work* vs *said, year,
  social*), which explains the document clusters.
- **Central vs bridging nodes.** Eigenvector centrality picked out the core documents (reviews and
  recipes). Betweenness picked out the bridges: one news article and one recipe that link the two
  communities.

## Pipeline

| Step | Method |
|---|---|
| Corpus | 20 documents (5 per genre, ≥ 200 words each), cleaned and saved as UTF-8 text files named `<genre>_<n>.txt` |
| Pre-processing | Lower-casing, URL/number/punctuation removal, stop words plus custom filler words, stemming (`tm`, `SnowballC`) |
| Document-term matrix | 3,038 terms reduced to **26** with sparse-term removal (threshold tuned by trial and error) |
| Clustering | Cosine distance + Ward's linkage (`ward.D2`), cut at k = 4, accuracy scored against genre labels |
| Sentiment | `SentimentAnalysis` with the General Inquirer dictionary; one-way ANOVA across genres |
| Document network | Binary DTM × its transpose → shared-term adjacency; weighted `igraph` graph |
| Word network | Term co-occurrence matrix → 26-node weighted graph |
| Bipartite network | 46 nodes (20 documents + 26 tokens), 254 edges |
| Visual encoding | Node size = degree, colour = genre / centrality, border = sentiment, edge width = weight, Louvain community hulls |

## Selected figures

| | |
|---|---|
| ![](figures/T4_genre.png) | ![](figures/T7_improved.png) |
| Dendrogram coloured by genre | Word co-occurrence network |
| ![](figures/T5_sentiment.png) | ![](figures/T8_improved.png) |
| Sentiment by genre | Document–word bipartite network |

## What I'd do differently

Keeping only 26 frequent words made every network almost fully connected (most nodes had maximum degree),
which hides genre differences. Next steps would be:
- **TF-IDF weighting** to favour genre-specific words over common ones
- **Lemmatisation and n-grams** instead of plain stemming (e.g. "red wine", "box office")
- **Sentence embeddings** (e.g. transformer models) to cluster by meaning rather than exact word overlap

## Tech stack

R · tm · SnowballC · proxy · SentimentAnalysis · igraph · ggplot2

## Data

The source documents are copyrighted, so only their titles and URLs are listed, in
[`data/sources.csv`](data/sources.csv).

## Context

Individual project for a university data analytics unit (Monash University, 2026).
