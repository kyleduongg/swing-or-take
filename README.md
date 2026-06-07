# Swing or Take?

**GitHub Repository:** https://github.com/kyleduongg/swing-or-take

## Project Overview

We will study one of the most important decisions in baseball! Will the batter **swing** or **take** a pitch?

This project will frame this decision as a binary classification problem. The goal is to understand how several variables such as pitch location, pitch type, velocity, count, handedness, and game context influence whether a hitter swings.

The main research question is:

> Can we predict whether a batter will swing or take a pitch based on pitch characteristics and game context?

## Repository Contents

This repository contains:

- `DSC148FP.ipynb`  
  The main Jupyter Notebook containing the full project workflow, including data loading, cleaning, feature engineering, exploratory data analysis, modeling, evaluation, and interpretation.

- `DSC 148 Final Project Report.pdf`  
  The final written report explaining the research question, methods, results, and conclusion.

- `mlb_statcast_2025_75k.csv`  
  The Statcast pitch-level dataset used in the notebook. This file should be in the same folder as `DSC148FP.ipynb`.

## Project Workflow

### 1. Load the Statcast Dataset

The project uses a 2025 MLB Statcast dataset containing 75,000 randomly sampled pitches.

The notebook loads the data from:

```text
mlb_statcast_2025_75k.csv
```

The dataset includes pitch-level information such as pitch location, pitch type, velocity, count, batter handedness, pitcher handedness, and pitch outcome.

### 2. Define the Swing/Take Target Variable

Statcast does not directly provide a simple `swing` column, so the target variable is created from the `description` column.

The notebook classifies pitch outcomes into two groups:

- `swing = 1`: the batter swung  
  Examples include `foul`, `hit_into_play`, `swinging_strike`, and `foul_tip`.

- `swing = 0`: the batter took the pitch  
  Examples include `ball`, `called_strike`, `blocked_ball`, and `hit_by_pitch`.

Automatic balls, automatic strikes, and pitchouts are excluded because they are not normal swing/take decisions.

### 3. Clean Missing Values

The notebook checks missingness for core pitch features and tracking features.

Core columns such as pitch location, strike-zone height, velocity, pitch type, count, and handedness are required. Rows missing these core features are dropped.

Tracking columns such as movement, spin rate, and effective speed are imputed using the median.

### 4. Engineer Baseball-Specific Features

The notebook creates several groups of features to make the raw Statcast data more useful for prediction.

#### Location Features

These features describe where the pitch crossed the plate:

- `norm_x`
- `norm_z`
- `radial_dist`
- `in_zone`
- `abs_px`

These normalize pitch location relative to each batter's strike zone.

#### Count-Context Features

These features describe the pressure of the count:

- `two_strike`
- `three_ball`
- `count_diff`
- `hitter_count`
- `pitcher_count`

These help capture how batter behavior changes in different count situations.

#### Pitch-Type Features

Raw pitch codes are grouped into broader pitch categories:

- `Fastball`
- `Breaking`
- `Offspeed`
- `Other`

This reduces the number of pitch-type categories and matches how hitters often think about pitch types.

#### Matchup Features

These features describe batter-pitcher handedness:

- `platoon`
- `stand_R`
- `throw_R`

#### Sequence Features

These features describe what happened earlier in the same plate appearance:

- `prev_swing`
- `prev_in_zone`
- `is_first_pitch`

#### Player Tendency Features

The notebook also creates smoothed target-encoding features for batter and pitcher tendencies:

- `bat_swing_te`
- `pit_swing_te`

These are based only on the training set to avoid test-set leakage.

### 5. Perform Exploratory Data Analysis

The notebook uses visualizations and summary statistics to understand the swing/take decision before modeling.

The EDA includes:

- class balance of swings vs. takes
- swing rate by plate location
- swing rate by distance from the zone center
- swing rate by count
- swing rate by number of strikes
- swing rate by pitch group
- velocity distributions for swings and takes
- handedness matchup comparisons
- feature correlation heatmap
- PCA on physical pitch descriptors
- K-Means clustering on pitch locations
- association-rule mining
- robustness checks across subsamples

The main EDA finding is that pitch location and count are strongly related to whether a batter swings.

### 6. Create the Predictive Task

The predictive task is binary classification:

> Given pitch information and game context, predict whether the batter swings or takes.

The notebook uses an 80/20 stratified train-test split so that the train and test sets have similar swing/take proportions.

### 7. Build the Model Feature Matrix

The model uses numeric features directly and one-hot encodes the categorical `pitch_group` feature.

The final feature matrix includes:

- location geometry
- pitch movement and velocity
- count and inning context
- handedness matchup
- previous-pitch sequence features
- batter and pitcher swing-tendency encodings
- one-hot encoded pitch group

### 8. Train and Compare Models

The notebook compares several classification models:

- Majority-class baseline
- Gaussian Naive Bayes
- Logistic Regression with L2 regularization
- Linear SVM
- Decision Tree
- Random Forest
- Histogram Gradient Boosting

Each model is evaluated on the held-out test set.

### 9. Evaluate Model Performance

The notebook evaluates models using:

- accuracy
- F1 score
- ROC-AUC
- log loss
- average precision
- ROC curves
- confusion matrix

The project uses ROC-AUC as a major comparison metric because it measures how well the model separates swings from takes across different classification thresholds.

### 10. Interpret the Results

The notebook does not only report the best model. It also studies why the model works.

The analysis includes:

- comparing a location-only logistic regression model to the full engineered-feature model
- comparing linear models to boosted tree models
- permutation feature importance
- ablation studies by feature group
- calibration curves
- hyperparameter sensitivity checks
- case studies of confident correct and incorrect predictions

This helps separate the value of feature engineering from the value of using a more flexible model.

## Main Findings

Swing & Take decisions are strongly connected to pitch location and the count during an at-bat. Batters swing more at pitches that are in or near the strike zone, and swing behavior is adjusted based on the context of the at-bat through ball count.

Engineered features add predictive value beyond just raw location alone. The boosted tree model performs the best overall because it captures nonlinear relationships and interactions between several variables.

Batter decisions are not random, but they follow clear patterns based on context and the geometry of a pitch. 

## How to Duplicate This Project

Follow the steps below to reproduce the project on your own computer.

### 1. Clone the Repository

Open a terminal or Git Bash and run:

```bash
git clone https://github.com/kyleduongg/swing-or-take.git
```

Then, open the notebook and run the cells!
