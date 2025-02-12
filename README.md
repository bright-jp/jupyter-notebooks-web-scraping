# Web Scraping With Jupyter Notebooks

[![Promo](https://github.com/luminati-io/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.com/) 

This guide explains how to perform web scraping using use Jupyter Notebooks with interactive coding, data analysis, and visualization.

- [What Are Jupyter Notebooks?](#what-are-jupyter-notebooks)
- [Why Use Jupyter Notebooks for Web Scraping?](#why-use-jupyter-notebooks-for-web-scraping)
- [How to Use Jupyter Notebooks for Web Scraping](#how-to-use-jupyter-notebooks-for-web-scraping)
  - [Step 1: Set Up the Environment and Install Dependencies](#step-1-set-up-the-environment-and-install-dependencies)
  - [Step 2: Define the Target Page](#step-2-define-the-target-page)
  - [Step 3: Retrieve the Data](#step-3-retrieve-the-data)
  - [Step 4: Ensure Data Is Correct](#step-4-ensure-data-is-correct)
  - [Step 5: Visualize the Data](#step-5-visualize-the-data)
  - [Step 6: Put It All Together](#step-6-put-it-all-together)
- [Use Cases of Jupyter Notebook Web Scraping](#use-cases-of-jupyter-notebook-web-scraping)
- [Conclusion](#conclusion)

## What Are Jupyter Notebooks?

The Jupyter Notebook App is a server-client application that allows editing and running [notebook documents](https://jupyter-notebook-beginner-guide.readthedocs.io/en/latest/what_is_jupyter.html#notebook-document) via a web browser. A Jupyter [notebook](https://docs.jupyter.org/en/latest/) is “a shareable document that combines computer code, plain language descriptions, data, charts, graphs and figures, and interactive controls.” You can run the notebook application as a desktop application or install it on a remote server.

![The Jupyter Notebook App interface](https://brightdata.com/wp-content/uploads/2025/01/image-85-1024x250.png)

Jupyter Notebooks are built around a "kernel," a computational engine responsible for executing the code within a Notebook document. Specifically, the `ipython` kernel runs Python code, though kernels for other languages are also available.

![Launching a new document via the ipython kernel](https://brightdata.com/wp-content/uploads/2025/01/image-86.png)

The app's dashboard supports typical operations like showing local files, opening existing notebook documents, managing documents’ kernels, and more:

![The Jupyter Notebooks’ dashboard](https://brightdata.com/wp-content/uploads/2025/01/image-87-1024x272.png)

## Why Use Jupyter Notebooks for Web Scraping?

Some design specifics of Jupyter Notebooks make them very useful for web scraping purposes:

- **Interactive Development**: Write and execute code in small, manageable chunks called cells. Each cell operates independently, simplifying testing and debugging.
- **Organized Documentation**: Use Markdown within cells to document code, explain logic, and provide notes or instructions.  
- **Seamless Data Analysis Integration**: Process and analyze scraped data immediately with Python libraries like `pandas`, `matplotlib`, `seaborn`, and more.
- **Reproducibility and Sharing**: Easily share Jupyter Notebooks as `.ipynb` files or convert them to formats such as [ReST](https://docutils.sourceforge.io/rst.html), Markdown, and others.

### Pros and Cons

These are the pros and cons of using Jupyter Notebooks for data scraping:

#### **Pros**

- **Step-by-Step Debugging**: Each cell runs independently, allowing you to break down your data extraction code into smaller sections. This makes it easier to debug by running individual cells and identifying issues at a granular level.  
- **Comprehensive Documentation**: Use Markdown within cells to document your scraping code, explain its functionality, and justify design decisions.  
- **Flexibility**: Jupyter Notebooks enable seamless integration of web scraping, data cleaning, and analysis in a single environment. This eliminates the need to switch between different tools like an IDE for scraping and another environment for analysis.  

**Cons**  

- **Not Ideal for Large-Scale Projects**: Notebooks tend to become lengthy and unwieldy, making them less suitable for large-scale data scraping projects.
- **Performance Limitations**: Working with large datasets or running long scripts can slow down or even freeze Jupyter Notebooks.
- **Limited for Automation**: Jupyter Notebooks are designed for interactive, manual execution rather than scheduled or automated workflows. If you need to run your scraper on a schedule or integrate it into a larger system, a script-based approach is more suitable.

## How to Use Jupyter Notebooks for Web Scraping

### Step 1: Set Up the Environment and Install Dependencies

You need to have Python 3.6 or newer installed to be able to replicate this tutorial.

Create the `venv/` [virtual environment](https://docs.python.org/3/library/venv.html) directory:

```bash
python -m venv venv
```

To activate it, on Windows, run:

```bash
venv\Scripts\activate
```

On macOS/Linux:

```bash
source venv/bin/activate
```

Install all the libraries you need for this tutorial:

```bash
pip install requests beautifulsoup4 pandas jupyter seaborn
```

These libraries serve the following purposes:

- [**`requests`**](https://requests.readthedocs.io/en/latest/): To perform HTTP requests.
- [**`beautifulsoup4`**](https://beautiful-soup-4.readthedocs.io/en/latest/): For parsing HTML and XML documents.
- [**`pandas`**](https://pandas.pydata.org/): A powerful data manipulation and analysis library, ideal for working with structured data like CSV files or tables.
- [**`jupyter`**](https://pypi.org/project/jupyter/): A web-based interactive development environment for running and sharing Python code, great for analysis and visualization.
- [**`seaborn`**](https://seaborn.pydata.org/): A Python data visualization library based on [Matplotlib](https://matplotlib.org/).

Enter the `scraper/` folder:

```bash
cd scraper
```

Initialize a new Jupyter Notebook with this command:

```bash
jupyter notebook
```

You can now access your Jupyter Notebook App via the `locahost:8888`.

Create a new file by clicking on the “New > Python 3” option:

![Creating a new Jupyter Notebook file](https://brightdata.com/wp-content/uploads/2025/01/image-84.png)

The new file will be automatically called `untitled.ipynb`. Rename it to `analysis.ipynb` in the dashboard:

![Renaming a Jupyter Notebook file](https://brightdata.com/wp-content/uploads/2025/01/image-83-1024x390.png)

Here is your project's structure at the end of this step:

```
scraper/
    ├── analysis.ipynb
    └── venv/
```

### Step 2: Define the Target Page

Let's scrape the data from the website [worldometer](https://www.worldometers.info/) and use the target page related to [CO2 emissions in the United States](https://www.worldometers.info/co2-emissions/us-co2-emissions/) per year that provides this tabular data:

![The tabular data about the C02 emissions per year in United States](https://brightdata.com/wp-content/uploads/2025/01/image-82.png)

### Step 3: Retrieve the Data

Here is Python code to retrieve the data from the target page and save into a CSV file:

```python
import requests
from bs4 import BeautifulSoup
import csv

# URL of the website
url = "https://www.worldometers.info/co2-emissions/us-co2-emissions/"

# Send a GET request to the website
response = requests.get(url)
response.raise_for_status() 

# Parse the HTML content
soup = BeautifulSoup(response.text, "html.parser")

# Locate the table
table = soup.find("table") 

# Extract table headers
headers = [header.text.strip() for header in table.find_all("th")]

# Extract table rows
rows = []
for row in table.find_all("tr")[1:]:  # Skip the header row
    cells = row.find_all("td")
    row_data = [cell.text.strip() for cell in cells]
    rows.append(row_data)

# Save the data to a CSV file
csv_file = "emissions.csv"
with open(csv_file, mode="w", newline="", encoding="utf-8") as file:
    writer = csv.writer(file)
    writer.writerow(headers)  # Write headers
    writer.writerows(rows)    # Write rows

print(f"Data has been saved to {csv_file}")
```

Here is what this code does:

- It uses the `requests` library to send a GET request to the target page via the `requests.get()` method, then checks for request errors via the `response.raise_for_status()` method.
- It uses `BeautifulSoup` to parse the HTML content by instantiating the `BeautifulSoup()` class and by finding the `table` selector with the `soup.find()` method. In particular, this method is useful to locate the table containing the data.
- It uses a list comprehension to extract the table’s header.
- It uses a `for` loop to retrieve all the data from the table while skipping the header row.
- Finally, it opens a new CVS file and appends there all the data retrieved.

You can paste this code into a cell and run it by pressing `SHIFT+ENTER`.

Another way to run the cell is to select it and press the “Run” button in the dashboard:

![Running a cell in a Jupyter Notebook](https://brightdata.com/wp-content/uploads/2025/01/image-81-1024x709.png)

### Step 4: Ensure Data Is Correct

Open the CSV file with the saved data and see if the conversion was correct. To do that, type the following code into a new cell:

```python
import pandas as pd

# Load the CSV file into a pandas DataFrame
csv_file = "emissions.csv"
df = pd.read_csv(csv_file)

# Print the DataFrame
df.head()
```

This code does the following:

- Opens the CSV file as a data frame, thanks to `pandas`, with the `pd.read_csv()` method.
- Prints the first five rows of the data frame with the `df.head()` method.

This is the expected result:

![The first five rows of the data frame](https://brightdata.com/wp-content/uploads/2025/01/image-80-1024x238.png)

### Step 5: Visualize the Data

Use `seaborn` to create a line chart that shows the trend of the C02 emissions over the years:

```python
import seaborn as sns
import matplotlib.pyplot as plt

# Load the CSV file into a pandas DataFrame
csv_file = "emissions.csv"
df = pd.read_csv(csv_file)

# Clean column names be removing extra spaces
df.columns = df.columns.str.strip().str.replace('  ', ' ')

# Convert 'Fossil CO2 Emissions (tons)' to numeric
df['Fossil CO2 Emissions (tons)'] = df['Fossil CO2 Emissions (tons)'].str.replace(',', '').astype(float)

# Ensure the 'Year' column is numeric
df['Year'] = pd.to_numeric(df['Year'], errors='coerce')
df = df.sort_values(by='Year')

# Create the line plot
plt.figure(figsize=(10, 6))
sns.lineplot(data=df, x='Year', y='Fossil CO2 Emissions (tons)', marker='o')

# Add labels and title
plt.title('Trend of Fossil CO2 Emissions Over the Years', fontsize=16)
plt.xlabel('Year', fontsize=12)
plt.ylabel('Fossil CO2 Emissions (tons)', fontsize=12)
plt.grid(True)
plt.show()
```

Here is what this code does:

- It uses `pandas` to:
    - Open the CSV file.
    - Clean columns’ names by removing extra spaces with the `df.columns.str.strip().str.replace(' ', ' ')` method.
    - Accesses the column “Fossil CO2 Emissions (tons)” and converts data to numbers with the `df['Fossil CO2 Emissions (tons)'].str.replace(',', '').astype(float)` method.
    - Accesses the column “Years”, converts the values to numbers with the `pd.to_numeric()` method, and sorts values into ascendant order with the `df.sort_values()` method.
- It uses the libraries `matplotlib` and `seaborn` (which is built upon [`matplotlib`](https://matplotlib.org/), so it is installed when you install `seaborn`) to create the actual plot.

Here is the expected result:

![The resulting plot](https://brightdata.com/wp-content/uploads/2025/01/image-79.png)

### Step #6: Put It All Together

Your entire Jupyter Notebook for web scraping will look like this:

![The entire Jupyter Notebook document](https://brightdata.com/wp-content/uploads/2025/01/image-78.png)

## Use Cases of Jupyter Notebook Web Scraping

Here are some of the other possible uses for Jupyter Notebooks:

- **Tutorials for internal education**. You can use Jupyter notebooks as step-by-step tutorials for junior developers. Use Markdown for explanations and cells—for individual code blocks that can be executed independently.
- **Research and development**. When your scraping project requires multiple rounds of trial and error, you can keep all your tests in a single Notebook and use Markdown to highlight the tests that succeed.
- **Data exploration**. The Jupyter library has been specifically designed for data exploration and analysis, as illustrated by the tutorial above.

## Conclusion

While Jupyter Notebooks can be a powerful tool for web scraping, they are not the most efficient solution when it comes to scaling your web scraping operations or automating tasks.

Bright Data's [Web Scrapers](https://brightdata.com/products/web-scraper) simplify and enhance your data collection efforts. They feature dedicated endpoints for 100+ domains, bulk request handling, automatic IP rotation, and CAPTCHA solving.

Create a free Bright Data account today to try out our scraping solutions and test our proxies!