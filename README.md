# King County Housing EDA — A Resale Strategy for Zachary Brooks

This repository contains an exploratory data analysis (EDA) of the King County home-sales
dataset, built around a specific client brief. It started from the
[`ds-eda-project-template`](.) starter template and has been filled in with the actual
analysis, cleaning steps, and client deliverables described below.

## Project Overview

**Client:** Zachary Brooks — an investor who buys historical houses and plans to resell them
later. He wants to know which neighborhoods are the most liquid, whether renovating before a
sale is worth it, and where the hard risk floors are when sourcing a property.

**Three hypotheses tested:**

1. Resold houses never fall below construction grade 5.
2. Renovation lifts sale price on older homes, but the payoff depends on how historical the
   house really is.
3. Repeat sales cluster in specific zip codes rather than spreading evenly across the county.

**Key findings and recommendations:**

| # | Insight | Recommendation for the client |
|---|---|---|
| 1 | Zero of 176 resold houses fall below construction grade 5, even though 28 lower-grade houses exist county-wide | Use grade 5+ as a hard acquisition screen |
| 2 | Renovation lifts price-per-sqft 12–35% for homes built 1940–1999, but costs 5% on true pre-1940 historical stock | Renovate mid-century acquisitions before listing; preserve character on true pre-1940 homes instead of modernizing |
| 3 | Just 10 of the county's 70 zip codes account for 41% of every repeat sale | Prioritize zip codes 98055, 98146, 98133, 98118, and 98006 when sourcing for future resale |

## Learning Objectives

By the end of this repository, you should be able to:

- Connect to a PostgreSQL database from Python and load query results into a pandas DataFrame.
- Frame an exploratory data analysis around clear research questions and hypotheses.
- Clean and wrangle a real-world dataset by handling missing values, outliers, and feature transformations.
- Explore distributions and the relationships between features and the target variable (price).
- Translate your analysis into at least three insights and three client-specific recommendations.
- Present your work to a non-technical audience.

## Learning Path

Work through the files in order. Start with the assignment to understand the goal, follow the
workflow as your guide, fetch the data, then run the analysis notebook.

> [!TIP]
> The data lives in the **eda** schema of the database and is split across two tables. Before
> fetching anything in code, connect with DBeaver and explore that schema: inspect both
> tables, check [**Column Names**](column_names.md) for what each field means, and work out
> how to join them. Once you have a working `JOIN`, use it as the query in
> [**03 - Fetching the Data**](03_fetching_the_data_eda.ipynb) to load the combined dataset
> into pandas.

| File / Folder | Description |
| --- | --- |
| [**01 - Assignment**](01_assignment.md) | The project brief: the dataset, your tasks, deliverables, and the list of clients to choose from. |
| [**02 - Workflow**](02_workflow.md) | A recommended EDA workflow, from understanding and questioning the data through cleaning, relationships, and presenting. |
| [**03 - Fetching the Data**](03_fetching_the_data_eda.ipynb) | Connect to the PostgreSQL database with psycopg2 and SQLAlchemy, then pull the data into a pandas DataFrame. |
| [**04 - EDA**](04_eda_zachary_brooks.ipynb) | The final analysis notebook: data cleaning (including a unit-encoding bug fix and a renovation/build-date consistency check), then the three hypotheses above, each with its supporting chart and finding. |
| [**Column Names**](column_names.md) | Data dictionary describing each column in the King County housing dataset. |

### Presentation

| File | Description |
| --- | --- |
| [**presentation/Zachary_Brooks_Resale_Strategy.pptx**](presentation/Zachary_Brooks_Resale_Strategy.pptx) | The client-facing deck: one slide per hypothesis (chart + finding), followed by a one-line recommendation slide, closing with the overall "ideal class of units" recommendation. Built for a non-technical audience per the learning objectives. |

### Additional Folders and Files

| File / Folder | Description |
| --- | --- |
| [**Data**](data/) | Where the dataset CSV is saved locally. The folder is tracked, but its data files are kept out of git — re-fetch with [**03 - Fetching the Data**](03_fetching_the_data_eda.ipynb) after cloning. |
| [**.env.example**](.env.example) | Template for the database credentials. Copy it to `.env` and fill in your values. |
| [**pyproject.toml**](pyproject.toml) | Project configuration and dependencies. |
| [**uv.lock**](uv.lock) | Dependency lock file. |

## A Note on Data Cleaning

Two data-quality issues were found and handled in [**04 - EDA**](04_eda_zachary_brooks.ipynb),
section 3, before any hypothesis was tested:

- **`yr_renovated` unit bug.** Every non-zero value was exactly 10x too large (e.g. `19910`
  instead of `1991`) — a systematic export bug, fixed by dividing all non-zero values by 10.
- **Renovation-date consistency check.** After the fix, `yr_renovated` was cross-checked
  against `yr_built` (renovated-before-built: none found) and against the sale `date`
  (renovated-after-this-sale: 6 rows found, none of them resold houses, so not explained by a
  later renovation between two sales). These 6 rows were excluded from the renovation
  analysis. This exclusion was verified to leave the headline renovation-lift figures used in
  the client presentation unchanged (still −5%, +12%, +33%, +35% rounded).

## Setup

> [!NOTE]
> Throughout these steps, text in angle brackets like `<repo-name>` is a **placeholder**.
> Replace it, including the `< >` brackets, with your own value. For example, `cd <repo-name>`
> becomes `cd ds-eda-project-template`.

### 1. Create the Repository from the Template

Click **Use this template** on GitHub.

When creating the repository:

- Set yourself as the **Owner**
- Choose a repository name
- Disable **Include all branches**
- Click **Create repository**

> [!IMPORTANT]
> If you are working in pairs or groups, only **one person** should complete this step.

---

### 2. Add Collaborators (Pairs/Groups Only)

If working with teammates:

1. Open the repository on GitHub
2. Go to **Settings → Collaborators**
3. Add your teammates as collaborators
4. Share the repository link with your team

Teammates should accept the invitation before continuing.

---

### 3. Clone the Repository

Copy the SSH URL from the **Code** button on GitHub, then run:

```bash
git clone <copied-ssh-url>
```

The copied SSH URL will look like `git@github.com:<your-username>/<repo-name>.git`.

---

### 4. Move into the Project Folder and Install Dependencies

This installs all dependencies and creates a virtual environment in `.venv/`.

```bash
cd <repo-name>
uv sync
```

> [!TIP]
> Need a library that is not installed yet (for example a mapping)? Add it with
> `uv add <package-name>`. This updates `pyproject.toml` and `uv.lock` and installs it into
> your `.venv`. Commit both files; teammates then run `uv sync` after pulling to get the same
> environment.

---

### 5. Set up your Database Credentials

The data-fetching notebook reads the database connection details from a `.env` file. Copy the
template and fill in your own values:

```bash
cp .env.example .env
```

Open `.env` and replace the placeholders with the credentials for the King County housing
database (the same ones you use in DBeaver). These values feed
[**03 - Fetching the Data**](03_fetching_the_data_eda.ipynb).

> [!CAUTION]
> `.env` holds secrets and must never be committed. It is already listed in `.gitignore`.
> Only `.env.example`, with placeholder values, belongs in the repository.

---

### 6. Open the Notebooks

> [!NOTE]
> Make sure you open VS Code from the project root so it automatically detects the
> environment created by `uv sync`.

Launch VS Code in the project root folder:

```bash
code .
```

Then open a notebook and select the Python environment created by `uv sync` as the kernel.

## References & Further Reading

- [**House Sales in King County dataset**](https://www.kaggle.com/datasets/harlfoxem/housesalesprediction): The source dataset, with column descriptions and community notebooks.
- [**Pandas user guide**](https://pandas.pydata.org/docs/user_guide/index.html): The official guide to data manipulation with pandas.
- [**Seaborn tutorial**](https://seaborn.pydata.org/tutorial.html): Statistical data visualization in Python.
- [**SQLAlchemy documentation**](https://docs.sqlalchemy.org/en/20/): The database toolkit used to query PostgreSQL from Python.
- [**Hypothesis generation for EDA**](https://www.analyticsvidhya.com/blog/2020/11/an-efficient-way-of-performing-eda-hypothesis-generation/): How to form research questions and hypotheses before diving into the data.
- [**EDA Checklist**](https://github.com/neuefische/datascience-infographics/blob/main/EDA_Checklist.md): A phase-by-phase checklist for working through an exploratory analysis.
- [**Detailed EDA with Python**](https://www.kaggle.com/code/ekami66/detailed-exploratory-data-analysis-with-python): A worked example of a thorough EDA notebook on real data — the style this project's notebook follows.
- [**Tips for data science presentations**](https://www.dataknowsall.com/storytelling.html): Storytelling techniques for presenting results to a non-technical audience.
