# Anuratna Credit Dashboard (Power BI)

This is a two-page Power BI dashboard for the Anuratna credit and inventory project. It is saved as a Power BI Project (`.pbip`), which is a folder of text files that Power BI Desktop opens like a `.pbix`.

| Page | What it shows |
|---|---|
| **Receivables** | Five KPI cards: billed, unpaid, overdue 90+ days, median days to pay, and % of invoices paid within terms. Billing broken down by how it was paid (within 30 / 31-60 / 61-90 / over 90 days / unpaid). Monthly DSO against the 30-day terms. Unpaid balance by dealer. A dealer scorecard with segment, risk band, score, median days, % paid within 60 days, unpaid amount and oldest unpaid bill. Slicers: invoice year and dealer segment. |
| **Sales & stock** | Four cards: units, billing, average price per unit and models sold. Monthly units by category, billing split by category, units by model, and a model × year matrix. Slicers: invoice year and category. |

The dealer segments and risk bands come from Part 2 of the analysis: the K-means segments and the point-in-time logistic scorecard.

## Opening it

1. Unzip so that the data sits at `C:\AnuratnaDashboard\data\`. If you unzip `AnuratnaDashboard.zip` straight into `C:\`, it lands there.
2. Double-click `Anuratna Credit Dashboard.pbip`. You need a recent Power BI Desktop on Windows.
3. The visuals stay empty until the data is loaded. Click **Home > Refresh**.
4. If the data is somewhere else:
   - go to **Home > Transform data > Edit parameters**;
   - set `DataFolder` to that folder, keeping the trailing `\`;
   - click **Apply changes**.
5. To send someone a single file, use **File > Save as** and pick `.pbix`.

On an older Desktop version, if it refuses to open the project, turn on these options under **File > Options and settings > Options > Preview features**, then restart Desktop:
- *Power BI Project (.pbip) save option*
- *Store semantic model using TMDL format*
- *Store reports using enhanced metadata format (PBIR)*

## Checking the refresh

With no slicers selected, the Receivables cards should read:

| Card | Value |
|---|---|
| Billed | 601.3 lakh |
| Unpaid | 85.2 lakh |
| Overdue 90+ days | 76.8 lakh |
| Median days to pay | 60 |
| % of invoices paid within terms | 9.8% |

DSO should rise from about 61 days (Jun-2022) to about 138 (Dec-2023). The Sales & stock page should show:

| Card | Value |
|---|---|
| Units sold | 4,789 |
| Billing | 601.3 lakh |
| Average price per unit | INR 12,555 |
| Models sold | 18 |

## Model

```
Calendar --< Invoices >-- Dealers
    |           |            |
    |      InvoiceItems >-- Products
    |
    +----< Receivables >-----+     (dealer x month-end: open receivables, trailing 90-day billing)
```

- All relationships are many-to-one and filter in a single direction.
- The measures live in the `KPI` table. DSO is calculated as open receivables / trailing 90-day billing × 90, the same definition as Part 1, so it also responds to the segment slicer.
- `DaysOpen` is each unpaid invoice's age on 29-Feb-2024. It is blank for invoices that have been paid.

## Data

The `data/` folder holds the six CSV tables the dashboard loads. It is listed in `.gitignore`, and the project files themselves contain no data, so the project folder can go on GitHub without the dataset. If you post screenshots, keep in mind that they show dealer names.
