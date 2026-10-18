# Predicting Player Data Contribution on a Minecraft Research Server

Analysis of which player demographics generate the most gameplay data on PLAICraft, a Minecraft
research server run by the PLAI group at UBC. Completed as the final project for DSCI 100 (Data
Science I) in December 2024, and lightly revised since for clarity and to document the model's
limitations.

## The question

The PLAI group collects real player data in order to train AI Minecraft players, so the value of
a recruited player depends on how much they actually play. This analysis asks: **what kind of
player is most likely to contribute a large amount of data?**

## Data

197 player records, each with experience level, age, gender, subscription status, and total hours
played. Playtime is heavily zero-inflated: most players logged close to no hours, while a small
number logged dozens.

## Approach

- Cleaned and transformed the data with `tidyverse`: dropped unusable identifier columns,
  collapsed sparse gender categories, ordinal-encoded experience level, and binary-encoded
  subscription status.
- Explored each candidate predictor against playtime with seven `ggplot2` visualisations, using
  a log scale to make the skewed target legible.
- Fit a k-nearest-neighbours regression with `tidymodels`, tuning k by 5-fold cross-validation on
  a stratified 75/25 split, with predictors centred and scaled inside the recipe.

## Findings

- **Subscription status is the strongest signal.** Subscribed players logged roughly 950 total
  hours against about 10 for non-subscribers.
- **"Regular" players contribute most by experience level**, averaging around 20 hours against
  about 6 for the next highest group.
- **Total and per-player playtime diverge by gender.** Males account for the most total hours,
  but females and non-binary players average substantially more hours each. Which group to
  prioritise therefore depends on whether the goal is total hours or hours per recruit.
- **Playtime falls off sharply above age 50**, and almost all contribution comes from players
  aged 25 and under.

## Limitations

The regression does not predict playtime well, and the notebook says so directly. KNN averages
over neighbours, which pulls predictions toward the near-zero case that dominates the data and
makes it unable to anticipate the rare heavy contributors who account for most of the total
playtime. The sample is also small, the non-binary group especially so. Gender was used in the
exploratory plots but not as a model predictor.

The k search grid also ran only from 1 to 10, and cross-validated error was still falling at the
upper end, so the selected k sits at the boundary of the search rather than at a true optimum.

Framing the problem as classification — will this player become a high contributor at all — is
the more promising direction, and is noted as the next step.

## Running it

Open `final-project.ipynb` in Jupyter with an R kernel. The notebook downloads `players.csv` on
first run and reads the local copy thereafter. Stored outputs are included, so the analysis and
all plots are readable on GitHub without running anything.

Requires `tidyverse`, `tidymodels`, and `kknn`.

## Stack

R, tidyverse (dplyr, ggplot2), tidymodels, Jupyter
