[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/sfuqua/sot-hourglass-data/main?labpath=SoT-S8-Data.ipynb)

# SoT Hourglass Notebook
This repo contains a [Jupyter Notebook](https://jupyter.org/) for tracking and analyzing Sea of Thieves data, specifically Allegiance ("XP") and gold earned from engaging with Season 8 PvP features.

## Disclosure & methods

**The author of this project is a Microsoft employee.** I play Sea of Thieves in my free time and do **not** have private access to internal game data. All data in this Notebook was gathered via publicly inspectable API calls made from https://www.seaofthieves.com/profile/reputation.

At the time of writing, there is an API call with the following structure:

```json
    "PirateLord": {
        "Motto": "Defenders of the Waves",
        "Intro": "The Guardians of Fortune fight with honour to protect the Sea of Thieves and all that threatens the pirate's life",
        "Level": 27,
        "Xp": 2740,
        "NextCompanyLevel": {
            "Level": 28,
            "XpRequiredToAttain": 10386
        },
```

Note that this snapshot contains my current level, current XP, and metadata about the *next* level. By checking these values before and after an event that grants XP, you can use arithmetic to figure out how much you earned between API calls. By doing this many times while progressing through the system, you can slowly gather how much XP is required to level from 1-100 and how much you are earning for each activity along the way.

Any comments in this project about how the game "works" are based on assessment of this data, as a fan, and are not definitive statements, and are not backed by any private information from Rare.

The data available here is accurate to the best of my knowledge, but could be changed by Rare at any time with game service updates, and there could always be errors/typos in transcription or in my code.

## Getting started

1. Install Python 3
2. `pip install`: notebook, numpy, scipy, matplotlib, pandas
3. Open the Notebook file using Jupyter, e.g., `jupyter SoT-S8-Data.ipynb`
4. Run all cells. Once every cell has executed once, you can start to play with the widgets on the progression estimator and simulator.

## Features

### Progression curve
The first cell contains a list of "XP required" values to progress from one level to the next.

e.g., this line: `(1, 2000),` is data indicating going from level 1 -> 2 requires 2000 XP.

This data is used in the second cell to fit a curve to the data, which we can use to estimate the amount of time/effort required to move between levels.

The first cell also contains the amount of XP earned for various activities. 

For example:

```python
# Allegiance/XP for losing a fight
GAIN_LOSS = 560

# Allegiance/XP for sinking another ship
# Index == streak
GAIN_WIN = [
    4200,
    4675,
    5160, # Needs confirmation
    5688, # Needs confirmation
    6600  # Needs confirmation
]
```

XP is earned for the following activities:

* Losing/Sinking (static value)
* Winning (increases based on current streak; likely caps at 5 wins)
* "Cashing out" an hourglass (increases nonlinearly)
* Selling an emissary flag (increases linearly based on flag grade)

Bonus XP is earned when "defending" at various treasure grades.

### "Effort required" estimator

You can use a slider to pick a start level and end level, and the model will estimate (using real data where possible) how much XP is required. For reference, it will let you know how that value compares to the effort from 1-100, and how many sinks or wins are needed to get there.

### Progression simulator

A common question is "how many wins do I need to get to level 100?". This is a hard question to answer, because it depends on:

* Your win rate
* Your hourglass strategy (how often you bank)
* How many emissary flags you find and sell

To help, the Notebook includes a simulator that will generate 1000 random trials of leveling from 1-100 (or whatever level range you choose) based on these values and let you know the median and mean number of wins/losses it took. It will also estimate how much gold you will earn (or lose) based on hourglasses, emissary flags, and the cost resupplying.
