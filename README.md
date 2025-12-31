# Jupyter NotebooksでのWebスクレイピング

[![Promo](https://github.com/luminati-io/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.jp/) 

このガイドでは、インタラクティブなコーディング、データ分析、可視化を行えるJupyter Notebooksを使用してWebスクレイピングを実行する方法を説明します。

- [Jupyter Notebooksとは？](#what-are-jupyter-notebooks)
- [WebスクレイピングにJupyter Notebooksを使う理由](#why-use-jupyter-notebooks-for-web-scraping)
- [Jupyter NotebooksをWebスクレイピングに使う方法](#how-to-use-jupyter-notebooks-for-web-scraping)
  - [ステップ1: 環境をセットアップして依存関係をインストールする](#step-1-set-up-the-environment-and-install-dependencies)
  - [ステップ2: 対象ページを定義する](#step-2-define-the-target-page)
  - [ステップ3: データを取得する](#step-3-retrieve-the-data)
  - [ステップ4: データが正しいことを確認する](#step-4-ensure-data-is-correct)
  - [ステップ5: データを可視化する](#step-5-visualize-the-data)
  - [ステップ6: すべてをまとめる](#step-6-put-it-all-together)
- [Jupyter Notebook Webスクレイピングのユースケース](#use-cases-of-jupyter-notebook-web-scraping)
- [結論](#conclusion)

## What Are Jupyter Notebooks?

Jupyter Notebook Appは、Webブラウザ経由で[notebook documents](https://jupyter-notebook-beginner-guide.readthedocs.io/en/latest/what_is_jupyter.html#notebook-document)を編集・実行できるサーバー/クライアントアプリケーションです。Jupyterの[notebook](https://docs.jupyter.org/en/latest/)は、「コンピューターコード、平易な言語による説明、データ、チャート、グラフや図、そしてインタラクティブなコントロールを組み合わせた共有可能なドキュメント」です。Notebookアプリケーションはデスクトップアプリとして実行することも、リモートサーバーにインストールすることもできます。

![The Jupyter Notebook App interface](https://github.com/luminati-io/jupyter-notebooks-web-scraping/blob/main/Images/image-85.png)

Jupyter Notebooksは「kernel」を中心に構築されており、これはNotebookドキュメント内のコードを実行する計算エンジンです。具体的には、`ipython` kernelがPythonコードを実行しますが、他の言語向けのkernelも利用できます。

![Launching a new document via the ipython kernel](https://github.com/luminati-io/jupyter-notebooks-web-scraping/blob/main/Images/image-86.png)

アプリのダッシュボードでは、ローカルファイルの表示、既存のnotebook documentsのオープン、ドキュメントのkernel管理など、一般的な操作をサポートしています。

![The Jupyter Notebooks’ dashboard](https://github.com/luminati-io/jupyter-notebooks-web-scraping/blob/main/Images/image-87.png)

## Why Use Jupyter Notebooks for Web Scraping?

Jupyter Notebooksのいくつかの設計上の特性により、[web scraping](https://brightdata.jp/blog/how-tos/what-is-web-scraping)用途で非常に有用です。

- **インタラクティブな開発**: セルと呼ばれる小さく扱いやすい単位でコードを書いて実行します。各セルは独立して動作するため、テストやデバッグが簡単になります。
- **整理されたドキュメント化**: セル内でMarkdownを使用してコードを文書化し、ロジックを説明し、メモや手順を記載できます。  
- **データ分析とのシームレスな統合**: `pandas`、`matplotlib`、`seaborn`などのPythonライブラリで、スクレイピングしたデータをすぐに処理・分析できます。
- **再現性と共有**: Jupyter Notebooksを`.ipynb`ファイルとして簡単に共有でき、[ReST](https://docutils.sourceforge.io/rst.html)、Markdownなどの形式に変換することもできます。

### Pros and Cons

以下は、データスクレイピングにJupyter Notebooksを使用するメリットとデメリットです。

#### **Pros**

- **ステップバイステップのデバッグ**: 各セルは独立して実行されるため、データ抽出コードをより小さなセクションに分割できます。個々のセルを実行して、粒度の細かいレベルで問題を特定しやすくなります。  
- **包括的なドキュメント化**: セル内でMarkdownを使用してスクレイピングコードを文書化し、その機能を説明し、設計上の判断を正当化できます。  
- **柔軟性**: Jupyter Notebooksでは、Webスクレイピング、データクレンジング、分析を単一の環境でシームレスに統合できます。これにより、スクレイピング用のIDEと分析用の別環境といった複数ツールを切り替える必要がなくなります。  

**Cons**  

- **大規模プロジェクトには不向き**: Notebookは長くなりがちで扱いにくくなるため、大規模なデータスクレイピングプロジェクトには適しにくいです。
- **パフォーマンスの制限**: 大規模データセットを扱ったり、長時間動作するスクリプトを実行したりすると、Jupyter Notebooksが遅くなったり、フリーズしたりすることがあります。
- **自動化には限定的**: Jupyter Notebooksは、スケジュール実行や自動化されたワークフローではなく、インタラクティブで手動の実行を想定して設計されています。スクレイパーをスケジュールで動かしたい場合や、より大きなシステムに統合したい場合は、スクリプトベースのアプローチの方が適しています。

## How to Use Jupyter Notebooks for Web Scraping

### Step 1: Set Up the Environment and Install Dependencies

このチュートリアルを再現するには、Python 3.6以降がインストールされている必要があります。

`venv/` [virtual environment](https://docs.python.org/3/library/venv.html)ディレクトリを作成します。

```bash
python -m venv venv
```

有効化するには、Windowsでは次を実行します。

```bash
venv\Scripts\activate
```

macOS/Linuxでは次の通りです。

```bash
source venv/bin/activate
```

このチュートリアルに必要なライブラリをすべてインストールします。

```bash
pip install requests beautifulsoup4 pandas jupyter seaborn
```

これらのライブラリは以下の目的で使用します。

- [**`requests`**](https://requests.readthedocs.io/en/latest/): HTTPリクエストを実行するためです。
- [**`beautifulsoup4`**](https://beautiful-soup-4.readthedocs.io/en/latest/): HTMLおよびXMLドキュメントを解析するためです。
- [**`pandas`**](https://pandas.pydata.org/): 強力なデータ操作・分析ライブラリで、CSVファイルやテーブルのような構造化データの取り扱いに最適です。
- [**`jupyter`**](https://pypi.org/project/jupyter/): Pythonコードを実行・共有するためのWebベースの対話型開発環境で、分析や可視化に適しています。
- [**`seaborn`**](https://seaborn.pydata.org/): [Matplotlib](https://matplotlib.org/)をベースにしたPythonのデータ可視化ライブラリです。

`scraper/`フォルダに入ります。

```bash
cd scraper
```

次のコマンドで新しいJupyter Notebookを初期化します。

```bash
jupyter notebook
```

これで`locahost:8888`からJupyter Notebook Appにアクセスできます。

“New > Python 3”オプションをクリックして新しいファイルを作成します。

![Creating a new Jupyter Notebook file](https://github.com/luminati-io/jupyter-notebooks-web-scraping/blob/main/Images/image-84.png)

新しいファイルは自動的に`untitled.ipynb`という名前になります。ダッシュボードで`analysis.ipynb`にリネームします。

![Renaming a Jupyter Notebook file](https://github.com/luminati-io/jupyter-notebooks-web-scraping/blob/main/Images/image-83.png)

このステップ終了時点でのプロジェクト構成は次の通りです。

```
scraper/
    ├── analysis.ipynb
    └── venv/
```

### Step 2: Define the Target Page

Webサイト[worldometer](https://www.worldometers.info/)からデータをスクレイピングし、年ごとの[米国のCO2排出量](https://www.worldometers.info/co2-emissions/us-co2-emissions/)に関連するターゲットページを使用します。このページは次の表形式データを提供しています。

![The tabular data about the C02 emissions per year in United States](https://github.com/luminati-io/jupyter-notebooks-web-scraping/blob/main/Images/image-82.png)

### Step 3: Retrieve the Data

以下は、ターゲットページからデータを取得してCSVファイルに保存するPythonコードです。

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

このコードの動作は以下の通りです。

- `requests`ライブラリを使用して、`requests.get()`メソッドでターゲットページにGETリクエストを送信し、`response.raise_for_status()`メソッドでリクエストエラーを確認します。
- `BeautifulSoup`を使用して、`BeautifulSoup()`クラスをインスタンス化し、`soup.find()`メソッドで`table`セレクタを見つけることでHTMLコンテンツを解析します。特にこのメソッドは、データを含むテーブルを特定するのに役立ちます。
- リスト内包表記でテーブルのヘッダーを抽出します。
- `for`ループを使用して、ヘッダー行をスキップしながらテーブルからすべてのデータを取得します。
- 最後に、新しいCVSファイルを開き、取得したすべてのデータをそこに追記します。

このコードをセルに貼り付け、`SHIFT+ENTER`を押して実行できます。

セルを実行する別の方法として、セルを選択してダッシュボードの“Run”ボタンを押す方法もあります。

![Running a cell in a Jupyter Notebook](https://github.com/luminati-io/jupyter-notebooks-web-scraping/blob/main/Images/image-81.png)

### Step 4: Ensure Data Is Correct

保存されたデータのCSVファイルを開き、変換が正しく行われたか確認します。そのために、新しいセルに次のコードを入力します。

```python
import pandas as pd

# Load the CSV file into a pandas DataFrame
csv_file = "emissions.csv"
df = pd.read_csv(csv_file)

# Print the DataFrame
df.head()
```

このコードは以下を行います。

- `pandas`の`pd.read_csv()`メソッドにより、CSVファイルをデータフレームとして開きます。
- `df.head()`メソッドでデータフレームの先頭5行を表示します。

期待される結果は次の通りです。

![The first five rows of the data frame](https://github.com/luminati-io/jupyter-notebooks-web-scraping/blob/main/Images/image-80.png)

### Step 5: Visualize the Data

`seaborn`を使用して、年ごとのC02排出量の傾向を示す折れ線グラフを作成します。

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

このコードの動作は以下の通りです。

- `pandas`を使用して:
    - CSVファイルを開きます。
    - `df.columns.str.strip().str.replace(' ', ' ')`メソッドで余分なスペースを削除し、列名をクリーンアップします。
    - “Fossil CO2 Emissions (tons)”列にアクセスし、`df['Fossil CO2 Emissions (tons)'].str.replace(',', '').astype(float)`メソッドでデータを数値に変換します。
    - “Years”列にアクセスし、`pd.to_numeric()`メソッドで値を数値に変換し、`df.sort_values()`メソッドで値を昇順に並べ替えます。
- `matplotlib`および`seaborn`（[`matplotlib`](https://matplotlib.org/)をベースにしているため、`seaborn`をインストールすると一緒にインストールされます）を使用して、実際のプロットを作成します。

期待される結果は次の通りです。

![The resulting plot](https://github.com/luminati-io/jupyter-notebooks-web-scraping/blob/main/Images/image-79.png)

### Step #6: Put It All Together

Webスクレイピング用のJupyter Notebook全体は次のようになります。

![The entire Jupyter Notebook document](https://github.com/luminati-io/jupyter-notebooks-web-scraping/blob/main/Images/image-78.png)

## Use Cases of Jupyter Notebook Web Scraping

以下は、Jupyter Notebooksの他の利用例です。

- **社内教育向けチュートリアル**。Jupyter notebooksを、ジュニア開発者向けのステップバイステップチュートリアルとして使用できます。説明にはMarkdownを使用し、セルには独立して実行できる個別のコードブロックを配置します。
- **研究開発**。スクレイピングプロジェクトで複数回の試行錯誤が必要な場合、すべてのテストを単一のNotebookにまとめ、Markdownで成功したテストを強調できます。
- **データ探索**。上記のチュートリアルが示すように、Jupyterライブラリはデータ探索と分析のために特別に設計されています。

## Conclusion

Jupyter NotebooksはWebスクレイピングにおいて強力なツールになり得ますが、Webスクレイピング運用のスケールやタスクの自動化という観点では、最も効率的なソリューションではありません。

Bright Dataの[Web Scrapers](https://brightdata.jp/products/web-scraper)は、データ収集の取り組みを簡素化し、強化します。100以上のドメイン向けの専用エンドポイント、一括リクエスト処理、自動IPローテーション、そして[CAPTCHA solving](https://github.com/luminati-io/Captcha-solver)を備えています。

今すぐ無料のBright Dataアカウントを作成して、当社のスクレイピングソリューションを試し、プロキシをテストしてください！