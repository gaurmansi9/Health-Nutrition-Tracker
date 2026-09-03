# Health & Nutrition Tracker

A command-line nutrition and weight-tracking tool built in a Jupyter notebook. It lets a user create a profile, calculate BMR/TDEE-based calorie targets, log daily food intake (manually or from the USDA FoodData Central database), get simple food recommendations, and visualize weight/calorie trends over time.

## Features

- **User profiles** — create, view, and update a profile (age, gender, height, weight, activity level, unit system) stored as JSON per user.
- **Unit conversion** — supports both Imperial (lbs/inches) and Metric (kg/meters) input.
- **Energy calculations** — BMR (Mifflin/Harris-style formula), TDEE based on activity level, and a daily calorie target derived from a weight goal and timeframe.
- **Food logging** — log food manually or by searching a local food database, with running totals for calories, protein, carbs, and fat.
- **Food database builder** — processes raw USDA FoodData Central CSVs into a simplified lookup table (`data/food_database_simple.csv`).
- **Food suggestions** — recommends foods based on remaining calorie/protein needs for the day.
- **History & visualization** — plots weight trend, calorie intake, and (simulated) calorie expenditure over time using Matplotlib.

## Requirements

- Python 3.12+ (see [Known Issues](#known-issues))
- `pandas`
- `numpy`
- `matplotlib`

Install dependencies:

```bash
pip install pandas numpy matplotlib
```

## Data Setup

Before running, download the [USDA FoodData Central](https://fdc.nal.usda.gov/download-datasets) CSV export and place these two files in a `FoodData_Central/` folder in the project root:

```
FoodData_Central/
├── food.csv
└── food_nutrient.csv
```

These files are **not included** in this repository due to their size. The notebook will fail on the food-database-building step if they are missing.

## Usage

1. Open `Tracker.ipynb` in Jupyter.
2. Run all cells in order. The first run will:
   - Create a local `data/` folder for saved profiles and logs.
   - Build `data/food_database_simple.csv` from the raw FoodData Central files.
3. When `main()` runs, follow the on-screen prompts to:
   - Create or load your profile.
   - Log food intake (manual entry or database search).
   - View today's totals and get food suggestions.
   - View your history as a chart of weight vs. calorie intake/expenditure.

All data is saved locally in the `data/` folder as JSON files (one profile file and one intake-history file per user).

## Project Structure

```
Tracker.ipynb          # Main notebook (all logic lives here)
FoodData_Central/       # Raw USDA data (user-supplied, not included)
data/                   # Generated at runtime — profiles, intake logs, food DB
```

## Known Issues

This notebook is a work in progress. A few things to be aware of before relying on it:

- **BMI category gap**: `get_bmi_category()` has a boundary gap — a BMI between 24.9 and 25 falls through to "Obesity" instead of "Overweight". These BMI functions are also currently unused by the rest of the program.
- **Inconsistent profile key when updating weight**: updating weight through the "Update Profile" menu writes to `weight_kg`, while the rest of the program (BMR calculations, history view, profile creation) reads `weight_kgs`. As a result, a weight update via that menu option won't actually be picked up elsewhere.
- **Requires raw USDA data**: the food-database cell (`create_simple_food_database`) runs automatically on notebook load and will raise `FileNotFoundError` if `FoodData_Central/food.csv` and `food_nutrient.csv` aren't present.
- **Simulated exercise calories**: the history/visualization step generates a random value for daily exercise calories rather than using real data, so the "expenditure" line in the chart is illustrative, not accurate.
- **No input validation on activity level**: entering anything other than `low`/`medium`/`high` when prompted will cause a `KeyError` in `calculate_tdee()`.
- **Python version sensitivity**: one f-string in `add_food_database()` nests double quotes inside a double-quoted f-string (e.g. `f"{food_entry["name"]}"`), which requires Python 3.12+ (PEP 701). It will raise a `SyntaxError` on earlier Python versions.
- **Interactive/CLI-based**: `main()` relies on `input()` prompts, so it's meant to be run interactively (not suitable for headless/batch execution as-is).

## Credits

Built as a learning project exploring pandas-based data processing, JSON-based persistence, and basic health/nutrition calculations. Includes data sourced from USDA FoodData Central.
