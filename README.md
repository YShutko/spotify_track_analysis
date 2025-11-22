# Spotify Track Analytics

Analyze ~114k Spotify tracks to understand what drives popularity, explore mood/genre patterns, and prototype lightweight prediction tools for playlist building or A&R triage.

## SPOTIFY POPULARITY PREDICTOR (GRADIO DEMO)

Gradio on local host: Running on local [URL](http://127.0.0.1:7865)

## Business Problems and Goals
- What audio and metadata signals most influence a track’s popularity score (0–100)?
- Can we quickly triage large catalogs to surface likely hits or candidate tracks for playlists?
- How do energy/valence/danceability differ by macro-genre, and where are outliers worth A&R follow-up?
- Deliver simple, reproducible tooling (notebook widgets + Gradio demo) that product or data teams can test with minimal setup.

## Data
- Source: [Kaggle – Spotify Tracks Dataset](https://www.kaggle.com/datasets/maharshipandya/-spotify-tracks-dataset)
- Files: [raw data](https://github.com/YShutko/CI_spotify_track_analysis/blob/3c1d5b469e04c4a46cf01e3d99477fac8d672044/data/spotify_dataset.csv) and [cleaned dataset](https://github.com/YShutko/CI_spotify_track_analysis/blob/3c1d5b469e04c4a46cf01e3d99477fac8d672044/data/spotify_cleaned_data.csv) (preprocessed subset used in all notebooks).
- The dataset consists of the following columns:
  * track_id: Unique Spotify identifier for each track.
  * artists: List of artists performing the track, separated by semicolons.
  * album_name: Title of the album where the track appears.
  * track_name: Title of the song.
  * popularity: Score from 0–100 based on recent play counts; higher means more popular.
  * duration_ms: Length of the track in milliseconds.
  * explicit: Indicates whether the track contains explicit content (True/False).
  * danceability: Score (0.0–1.0) measuring how suitable the song is for dancing.
  * energy: Score (0.0–1.0) reflecting intensity, speed, and loudness.
  * key: Musical key using Pitch Class notation (0 = C, 1 = C♯/D♭, etc.).
  * loudness: Overall volume of the track in decibels.
  * mode: Indicates scale type (1 = major, 0 = minor).
  * speechiness: Score estimating spoken content in the track.
  * cousticness: Likelihood (0.0–1.0) that the song is acoustic.
  * instrumentalness: Probability that the track has no vocals.
  * liveness: Measures if the song was recorded live (higher = more live).
  * valence: Positivity of the music (0.0 = sad, 1.0 = happy).
  * tempo: Speed of the song in beats per minute (BPM).
  * time_signature: Musical meter (e.g. 4 = 4/4 time).
  * track_genre: Musical genre classification of the track.

## Repository Layout
* [Spotify_track_analysis.ipynb](https://github.com/YShutko/CI_spotify_track_analysis/blob/3c1d5b469e04c4a46cf01e3d99477fac8d672044/notebooks/Spotify_track_analysis.ipynb) – EDA, cleaning, feature exploration, visualizations (correlations, genre summaries, energy/valence scatter, duration vs popularity).
* [ml_models.ipynb](https://github.com/YShutko/CI_spotify_track_analysis/blob/3c1d5b469e04c4a46cf01e3d99477fac8d672044/notebooks/ml_models.ipynb) – Feature engineering and model training for popularity prediction.
* [ipywidgets.ipynb](https://github.com/YShutko/CI_spotify_track_analysis/blob/3c1d5b469e04c4a46cf01e3d99477fac8d672044/models_widgets/ipywidgets.ipynb) – In-notebook prediction widget (sliders/dropdowns) using downloaded models.
* [gradio.ipynb](https://github.com/YShutko/CI_spotify_track_analysis/blob/3c1d5b469e04c4a46cf01e3d99477fac8d672044/models_widgets/gradio.ipynb) – Gradio UI for quick web demos (multiple downloadable models).
* [flagged](https://github.com/YShutko/CI_spotify_track_analysis/blob/3c1d5b469e04c4a46cf01e3d99477fac8d672044/models_widgets/.gradio/flagged/dataset1.csv) – Sample flagged input from a previous Gradio run.
  
## Plot Highlights
* Pairplot:
  <img src="plots/pairplot.png" width="600">
Key Insights:
1. Popularity shows no strong linear relationships
* Scatterplots with popularity form wide clouds → no clear trend.
* Confirms that simple linear models won’t work well.

2. Strong feature relationships exist between audio characteristics
* Energy ↗ Loudness (clear strongest correlation)
* Energy ↗ Danceability
* Danceability ↗ Valence
These justify creating interaction features (energy × valence, loudness × danceability).

3. Several features are highly skewed
* Acousticness, instrumentalness, and speechiness show uneven distributions.
* They add information but are not dominant predictors.

4. Some features carry very little useful signal
* Key, mode, and time_signature appear as vertical stripes (discrete, low-variance).
* Correct choice to drop or de-prioritize them.

5. Duration and tempo show only weak relationships
* Scatterplots reveal noisy clouds → limited influence on popularity.  

*  Correlation Heatmap:
<img src="plots/correlation_heatmap.png" width="600">

Overall Interpretation: 
The heatmap illustrates the relationship between different audio features of songs, with values ranging from –1 (strong negative correlation) to +1 (strong positive correlation). Most correlations are weak, indicating that the features are largely independent; however, there are a few notable strong relationships worth highlighting.

* Key Positive Correlations (Strong/Moderate)
    * Feature Pair	Correlation	Interpretation
    * Energy ↔ Loudness	0.76	Louder tracks tend to have more energy.
    * Danceability ↔ Energy	0.28	More energetic tracks are slightly more danceable.
    * Valence ↔ Danceability	0.48	More danceable songs tend to have happier/positive vibes.
    * Liveness ↔ Acousticness	0.21	More live-sounding tracks tend to be more acoustic.
* Strong Negative Correlations
    * Feature Pair	Correlation	Interpretation
    * Acousticness ↔ Energy	–0.73	Highly acoustic songs are much less energetic.
    * Acousticness ↔ Loudness	–0.59	Acoustic songs are generally quieter than others.
    * Instrumentalness ↔ Loudness	–0.43	Instrumental songs tend to be quieter.

* Popularity shows very weak correlations with all features, meaning:
    * Popularity cannot be explained well by audio features alone.
    * External factors like marketing, artist reputation, playlisting, and trends likely drive popularity more than musical traits.
    * Highest (still weak) correlations with popularity:
        * Danceability (0.03)
        * Loudness (0.05) → Slight preference for louder songs

* Additional Observations
    * Explicit content has no meaningful effect on popularity (correlation = 0.04).
    * Tempo, mode, and time signature have close to zero correlation with most metrics — musical structure isn't a major differentiator.
    * Instrumentalness is negatively related to energy and popularity, implying mainstream hits are normally non-instrumental and energetic.
 Popularity has modest positive ties to loudness/energy; acousticness is negatively related to loudness/energy; energy and loudness are strongly correlated.
- Popularity Distribution (`plots/distribution_of_track_popularity.png`): Long tail of low-popularity tracks with a broad mid-popularity hump; few tracks exceed 70+.
- Top Genres by Volume (`plots/top_10_genres_by_number of tracks.png`): Acoustic, electronic, j rock, reggaeton, and mandopop dominate track counts.
- Popularity by Top Genres (`plots/pupolarity_distribution_by 5_top_genres.png`): Electronic and mandopop show higher medians; reggaeton is wide-spread with many low outliers but some very high-popularity tracks.
- Mean Feature Values (`plots/mean_audio_feature_values_by_genre_top5.png`): Reggaeton and j rock run highest on energy; acoustic leads in acousticness; electronic leads in instrumentalness.
- Energy vs Valence (`plots/energy_vs_valence.png`): Higher popularity quantiles cluster more in high energy/valence areas but with overlap—these features alone are not decisive.
- Duration vs Popularity (`plots/track_duration_vs_popularity.png`): Most tracks are 2–6 minutes with no strong duration effect; very long tracks are rare and not consistently popular.
- Takeaway: Popularity is multi-factor; energy/loudness help, but genre context and danceability/valence interactions matter. Nonlinear models align with these patterns.
  
## Models and Comparison (from [ml_models.ipynb](https://github.com/YShutko/CI_spotify_track_analysis/blob/3c1d5b469e04c4a46cf01e3d99477fac8d672044/notebooks/ml_models.ipynb))
- Linear Regression baseline: MAE 14.17, RMSE 19.32, R² 0.252 — weak fit, high error.
- Random Forest (300 trees): MAE 4.86, RMSE 9.98, R² 0.801 — strongest performer in this run.
- XGBoost (untuned): MAE 5.92, RMSE 11.03, R² 0.756 — good, but behind RF.
- XGBoost (tuned): MAE 5.11, RMSE 9.99, R² 0.800 — closes the gap with RF after tuning.
- Takeaway: tree ensembles give the best accuracy; tuning XGB nearly matches RF while offering faster inference knobs for deployment.
  

  
## Interactive Prediction Tools
- **Notebook widget ([ipywidgets.ipynb](https://github.com/YShutko/CI_spotify_track_analysis/blob/3c1d5b469e04c4a46cf01e3d99477fac8d672044/models_widgets/ipywidgets.ipynb))**  
  Downloads a selected model from the Hugging Face repo `YShutko/spotify-popularity-models`, loads macro-genre options from the cleaned data, and exposes sliders/dropdowns to test popularity predictions inline.
- **Gradio app ([gradio.ipynb](https://github.com/YShutko/CI_spotify_track_analysis/blob/3c1d5b469e04c4a46cf01e3d99477fac8d672044/models_widgets/gradio.ipynb))**  
  Loads multiple Hugging Face models and builds a Gradio UI with sliders and a genre dropdown. Use it to share a quick web demo; Gradio handles launching and optional sharing links. (Note: Ensure the selected model is passed through in the predict function before production use.)

## Suggested Workflow
1) Use [cleaned dataset](https://github.com/YShutko/CI_spotify_track_analysis/blob/3c1d5b469e04c4a46cf01e3d99477fac8d672044/data/spotify_cleaned_data.csv) to skip heavy preprocessing.
2) Run [Spotify_track_analysis.ipynb](https://github.com/YShutko/CI_spotify_track_analysis/blob/3c1d5b469e04c4a46cf01e3d99477fac8d672044/notebooks/Spotify_track_analysis.ipynb) to explore distributions, correlations, and genre-level mood/energy patterns.
3) Train and compare models in [ml_models.ipynb](https://github.com/YShutko/CI_spotify_track_analysis/blob/3c1d5b469e04c4a46cf01e3d99477fac8d672044/notebooks/ml_models.ipynb); focus on RF vs tuned XGB.
4) Demo predictions with the ipywidgets notebook or the Gradio app for stakeholder feedback.

## Conclusion
- Popularity is predictable with tree-based models using standard audio features; RF and tuned XGB achieve MAE ≈ 5–5.1 and R² ≈ 0.80 on this dataset.
- Macro-genre and energy/valence interactions remain useful signals for triage; additional metadata (artist history, release timing) could further reduce error.
- The Gradio and notebook widgets provide fast, shareable prototypes for product or A&R teams; harden by validating model selection wiring and adding input validation if deploying beyond experiments.
