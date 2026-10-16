# Python Notes

Common Python snippets — formatting, environments, pandas, and syntax conventions.

## Table of Contents

- [[#String Formatting]]
- [[#Virtual Environments and Package Managers]]
- [[#Project Repo Commands (uv)]]
- [[#Alembic (Database Migrations)]]
- [[#Pandas DataFrames]]
- [[#Statistics (NumPy)]]
- [[#Functions]]
- [[#Making a Map (GeoPandas)]]
- [[#Misc Utilities]]
- [[#Classes]]
- [[#Python Syntax Conventions]]
- [[#Data Validation]]
- [[#Project-Specific Notes]]
- [[#AutoML (H2O)]]

---

## Basic
### String Formatting

F-strings with alignment formatting:

- `>` means right-align — `{:>10}`
- `<` means left-align — `{:<10}`
- `^` means center-align — `{:^10}`
- `.2f` means 2 decimal places — `3.14`
- `.2e` means scientific notation — `3.14e+00`

```python
print(f'{"Model Coefficients":>9}')
for col, coef in zip(X.columns, lm.coef_):
    print(f'{col:>9}: {coef:>6.3f}')
print(f'\nR^2: {lm.score(X,y):.4}')
```

Other numeric formatting:

```python
# Python's numeric underscores
1_000_000

# Scientific notation
1e6, 1e7
```

Make file path
```python
# Method 1:
from pathlib import Path
## Combine strings using the / operator
full_path = Path(folder_1) / folder_2 / file_name

# Method 2:
import os # Pass all the strings as arguments
full_path = os.path.join(folder_1, folder_2, file_name)
```



## Virtual Environments and Package Managers

### Windows (PowerShell)

```powershell
# Create the environment
python -m venv .venv

# Activate it — first time only, allow scripts for this process
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass

# Activate it
.venv\Scripts\Activate.ps1

# Install your libraries
pip install pandas numpy matplotlib jupyter

# Write down the package versions (if there is a requirements.txt in a package)
pip freeze > requirements.txt

# Deactivate
deactivate
```

### Mac (Terminal)

```bash
# CLI = Command Line Interface, e.g.:
python upload_discovery.py
python upload_discovery.py --display load_experiments

# When using uv, you don't have to activate the environment if you don't want
# to — uv is smart enough to find it for you.

# Create a virtual environment
python3 -m venv .venv

# Update pip
python3 -m pip install --upgrade pip

# Install packages
python3 -m pip install numpy pandas

# Activate the virtual environment
source .venv/bin/activate

# Deactivate the virtual environment
deactivate
```

### pip vs uv

| Task                  | Standard pip                      | uv equivalent                  |
|-----------------------|------------------------------------|---------------------------------|
| Create venv           | `python3 -m venv .venv`            | `uv venv`                       |
| Install package       | `pip install requests`             | `uv pip install requests`       |
| Install from file     | `pip install -r requirements.txt`  | `uv pip install -r requirements.txt` |
| Record package versions | `pip freeze > requirements.txt`  | `uv pip freeze > requirements.txt` |

## Project Repo Commands (uv)

```bash
uv add <package_name>
uv sync
uv sync -P <package_name>
uv remove <package_name>
```

## Alembic (Database Migrations)

Good practices:

```bash
# Check if the local database syncs with the production database
uv run alembic current --check-heads

# If not, do something
uv run db-upgrade

# If it still does not work, click "drop(force)" for the local database in
# pgAdmin, then:
uv run copy-data -d
# OR
uv run copy-data
```

There are three types of databases in the system:

- **Production** — the real one
- **Development** — local one
- **Testing** — only for GitHub CI tests

```bash
uv run db-upgrade                     # Will upgrade my local database
uv run alembic current --check-heads  # Only checks my local database
```

Logic for changes:

- For data schema: when the branch is merged into main, the data schema updates in Production.
- Data upload: manual upload (old process).

Revision chain example:

```
bc359a449fb7 → 6823b8fa9202 (soil) → 195aecdd54f6 (fertilizer) → cf3f6a29bfb9 (greenhouse)
```

```bash
# Step 1: downgrade to before the soil table
alembic downgrade bc359a449fb7

# Step 2: upgrade only up to fertilizer tables
alembic upgrade 195aecdd54f6

# Then modify cf3f6a29bfb9_create_greenhouse_tables.py as needed, and when ready:

# Step 3: run the greenhouse migration
alembic upgrade head

# Check the ID of the latest revision
uv run alembic heads

# Check whether your latest revision matches the one in the database
uv run alembic current --check-heads

# Check the history of all revisions
uv run alembic history
```

## Pandas DataFrames

### Reading Excel

```python
import pandas as pd
df = pd.read_excel("file_name.xlsx")

# Read Excel values directly
import openpyxl
FILE_PATH = "file_name.xlsx"
SHEET_NAME = "sheetname"

wb = openpyxl.load_workbook(FILE_PATH, data_only=True)
ws = wb[SHEET_NAME]

ws.cell(row=3, column=1).value
ws['A2']

# Show merged cells
worksheet.merged_cells.ranges
```

### Building DataFrames

The pattern: `pd.DataFrame(list_of_dicts)` — each dict becomes one row, dict keys become column names.

```python
import pandas as pd

# Method 1: list of dicts
summary_df = pd.DataFrame(
    [
        {
            "measure": "Math test",
            "passed": 2,
            "failed": 2,
        },
        {
            "measure": "Science test",
            "passed": 3,
            "failed": 1,
        },
    ]
)
print(summary_df)
#         measure  passed  failed
# 0     Math test       2       2
# 1  Science test       3       1

# Method 2: dict of columns
data = {
    "strain_id": ["STR-001", "STR-002", "STR-003"],
    "starch_type": ["Corn", "Wheat", "Cassava"],
    "yield_pct": [88.5, 91.2, 85.0],
}

df = pd.DataFrame(data)

# Method 3:
design_loc = pd.DataFrame(
	[
		("India", crop, loc, season)
		for season in INDIA_SEASONS
		for crop, loc in INDIA_CROP_LOCATIONS
	]
	+ [
		("Denmark", crop, loc, "one harvest season")
		for crop, loc in DENMARK_CROP_LOCATIONS
	],
	columns=["country", "crop", "location_research_centers", "season"],
)
```

### Inserting & Renaming Columns

```python
# Insert new values
# df.insert(loc, column, value, allow_duplicates=False)
# Example: insert 101 into the first row of a new column "ID"
df.insert(0, 'ID', 101)

# Rename some column names
import pandas as pd
df = df.rename(columns={'old_colname_1': 'new_colname_1', 'old_colname_2': 'new_colname_2'})
```

### Combine DataFrames

```python
# Join two dataframes based on one column
## If the joining columns have the same name
df = df.merge(
    index_df[["project_name", "id"]],
    on="project_name",
    how="left",
)

## If the joining columns have different names
df = (
    df.merge(
        isolate_df[["other_isolate_name", "id"]],
        left_on="project_isolate_id",
        right_on="other_isolate_name",
        how="left",
    )
    .drop(columns=["other_isolate_name"])
)

## Assign every df_2 row to every df_1 row
cross_df = pd.merge(df_1, df_2, how="cross")


# Join two dataframes based on two columns
merged_df = pd.merge(df1, df2, on=["strain_id", "starch_type"], how="inner")

merged_df = pd.merge(
	df1, df2,
	left_on=["strain_id", "starch_type"],
	right_on=["isolate_id", "crop_type"],
	how="inner"
)
```

Column bind
```python
df3 = pd.concat([df1, df2["value"]], axis=1)
```


### Sorting & Reordering

```python
# Reorder and reset index
df = df.sort_values(by="score", ascending=False)
# Or
df = df.sort_values(["map_id"])

df = df.reset_index(drop=True)

# --- Reorder columns ---

# Specify the exact order you want
new_order = ["a", "b", "c"]
df = df[new_order]

# Move 'id' to index 0, keeping all other columns in their original order
first_col = "id"
other_cols = [col for col in df.columns if col != first_col]
df = df[[first_col] + other_cols]

# Move two columns to index 0, keeping all other columns in their original order
first_cols = ["value", "yield_pct"]
other_cols = [col for col in df.columns if col not in first_cols]
df = df[first_cols + other_cols]

# Extract B_ columns first, then A_ columns
b_cols = [col for col in df.columns if col.startswith("B_")]
a_cols = [col for col in df.columns if col.startswith("A_")]
df = df[a_cols + a_cols]
```

### Mapping Values

```python
import pandas as pd

data = {
    "strain_id": ["STR-001", "STR-001", "STR-001", "STR-003"],
    "starch_type": ["Corn", "Wheat", "Wheat", "Cassava"],
}
df = pd.DataFrame(data)

# Map one column
starch_mapping = {
    "Corn": 10,
    "Wheat": 20,
    "Cassava": 5,
}
df["new_value"] = df["starch_type"].map(starch_mapping)

# Map multiple columns
mapping = {
    ("STR-001", "Corn"): 10,
    ("STR-001", "Wheat"): 20,
    ("STR-003", "Cassava"): 5,
}
df["new_value"] = [
    mapping.get(key, None)
    for key in zip(df["strain_id"], df["starch_type"])
]

print(df)
```

### Filtering Rows

```python
# Filter for rows where strain_id is "STR-001"
filtered_df = df[df["strain_id"] == "STR-001"]

# Filter for rows where strain_id is not "STR-001"
filtered_df = df[df["strain_id"] != "STR-001"]

# Filter rows based on multiple criteria
filtered_df = df[(df["strain_id"] == "STR-001") & (df["starch_type"] == "Wheat")]

filtered_df = df[
    (df["status"].isin(["Active", "Unknow"])) &
    ((df["starch_type"] == "Corn") | (df["yield_pct"] > 90.0))
]

# Keep rows where status is NOT in the list
filtered_df = df[~df["status"].isin(["Active", "Unknow"])]

# Using .query()
df.query("strain_id == 'STR-001' and starch_type == 'Wheat'")

filtered_data = data.query(
    "country == 'DK' and "
    "location == 'Copenhagen' and "
    "crop_type == 'maize'"
)
```

### Dropping Rows / Duplicates

```python
# Drop rows where 'strain_id' is NaN
filtered_df = df.dropna(subset=["strain_id"])

# Drop duplicated rows, like distinct() in R
distinct_df = df.drop_duplicates()
```

### Unpack data

df.explode()

```python
import pandas as pd

df = pd.DataFrame({
    "experiment_id": [1, 2],
    "location": ["Farm_A", "Farm_B"],
    "strains_tested": [["Corn", "Wheat"], ["Soy"]]
})

# Explode the lists into separate rows
exploded_df = df.explode("strains_tested")

print(exploded_df)
```

| **index** | **experiment_id** | **location** | **strains_tested** |
| --------- | ----------------- | ------------ | ------------------ |
| 0         | 1                 | Farm_A       | Corn               |
| 0         | 1                 | Farm_A       | Wheat              |
| 1         | 2                 | Farm_B       | Soy                |
Look closely at the "After Exploding" table above. Notice that the row index (`0`) is duplicated for both "Corn" and "Wheat". If you want a clean, sequential index after exploding, you should chain `.reset_index(drop=True)` to the end.

### Inspecting DataFrames

```python
# Batch-comment multiple lines of code in Positron: CMD+/

df.columns            # Show all column names
df.columns.tolist()   # as a plain Python list
df.dtypes             # column names + their data types
df.info()             # column names, types, and non-null counts
df.head()
df["strain_id"].unique()

# Returns just the integer count of unique IDs
df["id"].nunique()

# Show the data types in all the columns
strain_df.dtypes

# Save DataFrame into a csv file
strain_df.to_csv('strain_table.csv', index=True)

# Locate a certain cell
.loc[row, col]
df.loc["id_15", "name"]="AAAB" # Assign a value into a cell

# .loc — selects by label (the actual index/column names)
df.loc[10]

# .iloc — selects by integer position (0, 1, 2, ... regardless of labels)
df.iloc[0]    # Returns a Series
df.head(1)    # Returns a DataFrame

# Select rows where column 'A' is NaN
missing_a_df = df[df["A"].isna()]

# Use .eq() to assign True/False (can also use | & for multiple conditions)
df["has_money"] = df["money"].eq("+")

# Loop through a DataFrame row by row — returns an iterator yielding
# (index, row) where row is a pandas Series.
df.iterrows()
```

### Splitting Columns

```python
# Split by "-" and assign to two new columns
df = pd.DataFrame({
    "strain_id": ["STR-001", "STR-002"],
    "starch_type": ["Corn", "Wheat"]
})
df[["strain_prefix", "strain_number"]] = df["strain_id"].str.split("-", expand=True)
```

### Column Name Cleanup

```python
df.columns = df.columns.str.lower()             # lowercase
df.columns = df.columns.str.upper()             # → ID, VALUE, SALES
df.columns = df.columns.str.strip()             # remove leading/trailing spaces
df.columns = df.columns.str.replace(' ', '_')   # replace spaces with _
df.columns = df.columns.str.lower().str.replace(' ', '_')  # combine both
```

Cleanup using certain patterns
```python
import re
pattern_commas = re.compile(r"\,|and")
messy_text = "Wheat,Corn andSoybeans"
# Replace commas and "and" with a standard dash
clean_text = pattern_commas.sub("-", messy_text)
print(clean_text)
# Wheat-Corn -Soybeans
```

### Count
```python
# Count unique items in the strain_id column
unique_count = df["strain_id"].nunique()
```

## Statistics (NumPy)

```python
# Creates a random number generator object in NumPy, initialized with a seed of 42
RNG = np.random.default_rng(42)

# RNG uses NumPy's modern Generator object (e.g. RNG = np.random.default_rng()),
# Generates a random decimal number between 0.0 (inclusive) and 1.0 (exclusive)
RNG.random()

# Generates a single random number from a normal (bell curve) distribution.
# which is faster and statistically superior to the older np.random.normal().
RNG.normal(mean_value, sd)

# Generates a single random number from a Uniform Distribution (flat probability) —
# every number in that range has an equal chance of being picked.
RNG.uniform(min_value, max_value)

# Randomly pick elements from a list, array, or sequence
RNG.choice(a, size=None, replace=True, p=None)
strains = ["STR-A", "STR-B", "STR-C", "STR-D"]
chosen = RNG.choice(strains)
```

### Summary Tables (groupby)

```python
df_summary = (
    df.groupby(["country"])[
        ["value", "uncertainty", "combined_value"]
    ]
    .mean()
    .sort_values(by="mean", ascending=False)
    .reset_index()
)

df_summary = (
    df.groupby("Treatment")["Symptom_Area_Pct"]
    .agg(mean="mean", std="std", n="count")
)

summary_df = df.groupby(["strain_id", "starch_type"])["value"].sum().reset_index()
```

## Functions

```python
# Define default values in arguments
def process_data(file_path: str, batch_size: int = 100):
    pass

# Beware of mutable defaults (lists, dicts, DataFrames)
def add_item(item, item_list: list | None = None):
    if item_list is None:
        item_list = []  # Creates a fresh list every time
    item_list.append(item)
    return item_list
```

## Making a Map (GeoPandas)

```python
import pandas as pd
import matplotlib.pyplot as plt
import geopandas as gpd

# Count strains per country
counts = strain_df['isolation_country'].value_counts(dropna=False).reset_index()
counts.columns = ['isolation_country', 'count']
counts['isolation_country'] = counts['isolation_country'].fillna('Unknown')

# Load world map
url = "https://naturalearth.s3.amazonaws.com/110m_cultural/ne_110m_admin_0_countries.zip"
world = gpd.read_file(url)

# Check available country name columns (run this once to verify)
# print(world.columns.tolist())

# Merge world map with strain counts using 'NAME' column
world = world.merge(counts, how='left', left_on='NAME', right_on='isolation_country')

# Plot
fig, ax = plt.subplots(figsize=(14, 7))

# Base map (all countries in light grey)
world.plot(ax=ax, color='#f0f0f0', edgecolor='#cccccc', linewidth=0.5)

# Highlighted countries (with strain data)
world[world['count'].notna()].plot(
    ax=ax, column='count',
    cmap='YlOrRd',
    edgecolor='#333333', linewidth=0.5,
    legend=True,
    legend_kwds={'label': 'Number of Strains', 'shrink': 0.5}
)

# Add country name labels on highlighted countries
for _, row in world[world['count'].notna()].iterrows():
    ax.text(row.geometry.centroid.x, row.geometry.centroid.y,
            f"{row['NAME']}\n({int(row['count'])})",
            fontsize=8, ha='center', color='#222')

ax.set_title('Distribution of Strains by Isolation Country',
             fontsize=15, fontweight='bold', pad=15)
ax.axis('off')

plt.tight_layout()
# plt.savefig('strain_map.png', dpi=150, bbox_inches='tight')
plt.show()
```

## Misc Utilities

```python
# Create a folder if it does not exist
image_folder = output_folder / "images"
image_folder.mkdir(parents=True, exist_ok=True)
```


```python
# __dir__() is a built-in Python method that returns a list of everything
# attached to an object — its methods, properties, and internal attributes.
data_loader.__dir__()

# Return a specific file name or path
from pathlib import Path

entry_file = Path('my_file/document/report.xlsx')

entry_file.stem      # → 'report'
entry_file.name      # → 'report.xlsx'
entry_file.suffix    # → '.xlsx'
entry_file.parent    # → 'my_file/document'
```

## Classes

```python
# Regular function — no self
def bark(name):
    print(f"{name} says Woof!")

# Class method — uses self
class Dog:
    def __init__(self, name, age):  # called when creating the object
        self.name = name            # store 'name' in the object
        self.age = age              # store 'age' in the object

    def bark(self):
        print(f"{self.name} says Woof!")

    def get_age(self):
        return self.age

dog1 = Dog('Rex', 3)
dog2 = Dog('Buddy', 5)

dog1.bark()   # → Rex says Woof!
dog2.bark()   # → Buddy says Woof!
```

## Python Syntax Conventions

```python
# The Ternary Operator (if/else in one line)
status = "Pass" if score >= 50 else "Fail"

# Generator Expressions
total = sum(x**2 for x in range(1, 6))

# Decorators (@decorator)
# 1. You write this first...
@my_decorator
def greet():
    print("Hello!")

# 2. But under the hood, Python executes it like this AFTER greet is created:
# greet = my_decorator(greet)

# Dictionary Comprehensions
squares = {x: x * x for x in range(5)}

# List Comprehensions
numbers = [1, 2, 3, 4, 5]
even_doubled = [x * 2 for x in numbers if x % 2 == 0]

# try ... except ... else blocks
try:
    result = 10 / 2
except ZeroDivisionError:
    print("Error occurred!")
else:
    # Runs ONLY if the try block succeeded without errors
    print("Operation successful:", result)
```

## Data Validation

```python
# Data validation test
assert set({"Field_A", "Field_B"}) <= set({"Field_A", "Field_B", "Field_C"})  # True
assert set({"Field_A", "Field_C"}) <= set({"Field_A", "Field_B", "Field_C"})  # False

# set(field_data["location_name"]):
# Converts values into a Python set, which strips out duplicates and keeps
# only unique location names.
```

## Project-Specific Notes

```bash
# In biofertilizer repo — install Python packages, no need to activate the virtual env
uv add new_package_name
```

## AutoML (H2O)

Result file: a binary model object.

```python
import h2o
h2o.init()

loaded_model = h2o.load_model(model_path)  # the path printed by the save cell
loaded_model.predict(some_h2o_frame)       # use it on new data
```

## Quarto
Check the execute environment
```python
import sys
print(sys.executable)
```

## Image

Read image and do some basic operations
```python
import cv2
image_path = "XXX.png"
image = cv2.imread(str(image_path))

# Counter-rotate image
rotated_image=cv2.rotate(image, cv2.ROTATE_90_COUNTERCLOCKWISE)

# Flip an image upside down
flipped_image=cv2.flip(image, 0) # 0 = flip around the horizontal axis
```

Show image
```python
import matplotlib.pyplot as plt
plt.imshow(cv2.cvtColor(image, cv2.COLOR_BGR2RGB))
plt.axis("off") # hide the axis ticks
plt.show()
```