# Spotify Track Analytics

Analyze ~114k Spotify tracks to understand what drives popularity, explore mood and genre patterns, and prototype models that predict or recommend songs based on audio features.

## Repository Layout
- data/spotify_dataset.csv: Full Kaggle dataset export (raw).
- data/spotify_cleaned_data.csv: Preprocessed subset used for analysis/modeling.
- notebooks/Spotify_track_analysis.ipynb: EDA, cleaning, feature exploration, and visualizations.
- notebooks/ml_models.ipynb: Modeling experiments (e.g., regression/classification for popularity or mood).

## Dataset
Source: https://www.kaggle.com/datasets/maharshipandya/-spotify-tracks-dataset  
Rows: ~114,000 tracks across 125 genres.  
Example columns: track_id, artists, track_name, album_name, track_genre, popularity (0-100), duration_ms, explicit, danceability, energy, key, loudness, mode, speechiness, acousticness, instrumentalness, liveness, valence, tempo, time_signature.

## Goals
- Identify musical features most associated with popularity.
- Classify or cluster songs by mood/energy (valence, energy, danceability).
- Surface genre-level patterns and emerging styles.
- Prototype similarity or recommendation workflows for playlist building.

## Getting Started
Prereqs: Python 3.9+ and Jupyter (Lab or Notebook).

1) Create and activate a virtual environment:
```bash
python -m venv .venv
.\.venv\Scripts\activate   # Windows
source .venv/bin/activate  # macOS/Linux
```

2) Install common dependencies (adjust as needed):
```bash
pip install pandas numpy matplotlib seaborn plotly scikit-learn gradio jupyter
```

3) Launch notebooks:
```bash
jupyter lab  # or jupyter notebook
```

4) Open and run:
- notebooks/Spotify_track_analysis.ipynb for data cleaning, EDA, and visualizations.
- notebooks/ml_models.ipynb for feature engineering and model training.

## Suggested Workflow
- Start with the cleaned file (`data/spotify_cleaned_data.csv`) to skip heavy preprocessing.
- Reproduce visual summaries (distributions, correlations, genre/mood charts) in the EDA notebook.
- Experiment with regression for popularity and classification/clustering for mood/energy in the modeling notebook.
- Compare feature importance across algorithms (e.g., tree-based models vs. linear baselines).

## Notes and Cautions
- Large files: the raw dataset is ~20 MB; loading may be slow on low-memory machines.
- Metrics and feature importances depend on the train/validation split and preprocessing choices; rerun cells when you change them.
- If you add a Gradio demo, throttle file/row sizes to keep inference responsive.

## Ethics
- Data contains only public track metadata and no listener-level information.
- Popularity and genre trends reflect platform/user bias; interpret model outputs as descriptive, not prescriptive.
- Be transparent about model scope and avoid using outputs to marginalize artists or genres.

## Credits
- Dataset: Maharshi Pandya (Kaggle).
- Tooling: Python, Jupyter, pandas, numpy, matplotlib, seaborn, plotly, scikit-learn, gradio.
- Template inspiration: Code Institute README patterns.
