# Enterprise Sales & Performance Analytics (Qlik Sense)

This project was developed to model enterprise sales, cost, customer, and geographic datasets according to relational data warehouse principles, and to transform them into interactive dashboards using Qlik Sense.

## 📊 Data Modeling and Architecture

To maximize analytical query performance and optimize memory (RAM) consumption, a **Star Schema** architecture was implemented in this project:

* **Central Fact Table (`sales`):** Contains calculable metrics such as revenue, cost, and quantity.
* **Dimension Tables:** The `customer`, `Itemmaster`, `SalesRep`, and `MasterCalender` tables are connected to the central `sales` table through 1-to-Many relationships. The `customer` table is further linked to the `cities` table for location analytics, offering a hybrid Snowflake Schema touch.
* **Synthetic Key Optimization:** To prevent data bloat and circular loops arising during table imports, redundant data columns were strategically removed from the architecture; inter-table links were reduced entirely to unique keys.

## 🛠️ Technical Code Explanations (ETL Processes)

Core algorithms used during the data loading and cleaning stage (within `qlik_load_script.txt`):

**1. Region Standardization and Data Cleaning**

* In the Cities table, the `If(Match(...))` function was used to normalize different countries such as `Germany`, `Spain`, and `UK` under a single `Europe` region.
* Missing/undefined `Region Code` values in the Customers table were checked with `If-IsNull` and assigned to the `Europe` region; codes ranging from 1–5 were dynamically converted into the string values `Nordic`, `Europe`, `USA`, `Asia`, and `Japan` using a `Pick(Match(...))` structure.

**2. Dynamic Time Dimension Generation (Master Calendar)**

* The minimum and maximum dates in the sales table were captured into memory as `MinDate`/`MaxDate` using the `Peek()` function.
* Using an `AutoGenerate` and `While` loop, every day between these two dates was generated row by row from scratch with an `IterNo()` counter.
* From the raw dates, the `Year`, `Quarter` (using the `Ceil(Month/3)` formula), `Month`, `Week`, and `YearMonth` dimensions were derived to ensure temporal consistency.

## 📂 Project Artifacts

* 💻 ETL Source Code: [qlik_load_script.txt](https://github.com/melikekara3/Enterprise-Sales-Performance-Analytics/blob/main/qlik_load_script.txt)
* 📦 Qlik Source App: [sales_performance_analysis.qvf](https://github.com/melikekara3/Enterprise-Sales-Performance-Analytics/blob/main/sales_performance_analysis.qvf)
* 📊 Executive Summary Report: [Dashboard.pdf](https://github.com/melikekara3/Enterprise-Sales-Performance-Analytics/blob/main/Dashboard.pdf)
* 🛍️ Product Analytics Report: [Product Details.pdf](https://github.com/melikekara3/Enterprise-Sales-Performance-Analytics/blob/main/Product%20Details.pdf)
* 👥 Customer Analytics Report: [Customer Details.pdf](https://github.com/melikekara3/Enterprise-Sales-Performance-Analytics/blob/main/Customer%20Details.pdf)
* 🗺️ Geographic Location Report: [Customer Location.pdf](https://github.com/melikekara3/Enterprise-Sales-Performance-Analytics/blob/main/Customer%20Location.pdf)
* 📈 Data Storytelling Report: [story telling.pdf](https://github.com/melikekara3/Enterprise-Sales-Performance-Analytics/blob/main/story%20telling.pdf)## 📦 Opening the .qvf File

GitHub does not support previewing `.qvf` files in the browser (due to file size and format limitations). To view the app:

1. **Download Qlik Sense Desktop** (free): [https://www.qlik.com/us/trial/qlik-sense-desktop](https://www.qlik.com/us/trial/qlik-sense-desktop)
2. Click the `sales_performance_analysis.qvf` link above, then use the **"Download raw file"** button in the top-right corner to download the file to your computer.
3. Open Qlik Sense Desktop → **"Open App"** → select the downloaded `.qvf` file.
4. Once the app opens, browse the **App Overview**, **Dashboard**, and **Story** tabs in the left menu to explore the data model and interactive visuals.

> 💡 Qlik Sense Desktop only runs on Windows. Mac/Linux users can instead refer to the PDF reports above (Dashboard, Customer Details, Product Details, Customer Location, Story Telling) for a static preview.
