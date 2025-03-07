# Retail_startegy_analyticals

## Project Overview and Objectives

This project focuses on analyzing retail data to extract insights and trends. The primary objectives include:

- Identifying customer purchase behavior
- Analyzing transaction patterns
- Understanding the impact of marketing promotions on sales
- Predicting future sales trends

## What We Have Found

- **Customer Segmentation**: We have successfully segmented our customer base based on their purchasing behavior. This segmentation allows for targeted marketing strategies.
- **Sales Trends**: Our analysis has revealed key sales trends, including seasonal variations and the impact of promotional activities.
- **Product Performance**: We have identified top-performing products and categories, as well as those that may need adjustments in marketing or placement.

## Repository Structure

  # Datasets
  [transaction_data](https://github.com/edigasreelekha/Retail_startegy-analyticals/blob/main/transaction_data%20csv.csv)
  [purchase_behaviour](https://github.com/edigasreelekha/Retail_startegy-analyticals/blob/main/purchase_behaviour%20csv.csv)
  # Jupyter notebooks with analysis
  [click here]()


# Python scripts for data processing and analysis

import pandas as pd
import matplotlib.pyplot as plt
from matplotlib.backends.backend_pdf import PdfPages

def generate_analysis_pdf(transaction_file, customer_file, output_pdf):
    """
    Generates a PDF containing the analysis of transaction and customer data.

    Args:
        transaction_file (str): Path to the transaction data CSV file.
        customer_file (str): Path to the customer data CSV file.
        output_pdf (str): Path to save the output PDF file.
    """
    try:
        # Load data
        transaction_data = pd.read_csv(transaction_file)
        customer_data = pd.read_csv(customer_file)

        # Convert DATE column to datetime
        transaction_data['DATE'] = pd.to_datetime(transaction_data['DATE'], origin='1899-12-30', unit='D')

        # Remove salsa products
        transaction_data = transaction_data[~transaction_data['PROD_NAME'].str.lower().str.contains('salsa')]

        # Remove outlier customer
        transaction_data = transaction_data[transaction_data['LYLTY_CARD_NBR'] != 226000]

        # Pack size
        transaction_data['PACK_SIZE'] = transaction_data['PROD_NAME'].str.extract(r'(\d+)').astype(float)

        # Brands
        transaction_data['BRAND'] = transaction_data['PROD_NAME'].str.split().str[0].str.upper()
        transaction_data['BRAND'] = transaction_data['BRAND'].replace({'RED': 'RRD', 'SNBTS': 'SUNBITES', 'INFZNS': 'INFUZIONS', 'WW': 'WOOLWORTHS', 'SMITH': 'SMITHS', 'NCC': 'NATURAL', 'DORITO': 'DORITOS', 'GRAIN': 'GRNWVES'})

        # Merge transaction data to customer data
        data = pd.merge(transaction_data, customer_data, on='LYLTY_CARD_NBR', how='left')

        # Calculate average price per unit by LIFESTAGE and PREMIUM_CUSTOMER
        avg_price = data.groupby(['LIFESTAGE', 'PREMIUM_CUSTOMER'])['TOT_SALES'].sum() / data.groupby(['LIFESTAGE', 'PREMIUM_CUSTOMER'])['PROD_QTY'].sum()

        # Create PDF
        with PdfPages(output_pdf) as pdf:
            # Plot transactions over time
            transactions_by_date = transaction_data.groupby('DATE')['TXN_ID'].count()
            all_dates = pd.date_range(start='2018-07-01', end='2019-06-30')
            transactions_by_day = pd.merge(pd.DataFrame(all_dates, columns=['DATE']), pd.DataFrame(transactions_by_date), on='DATE', how='left')
            plt.figure(figsize=(10, 5))
            plt.plot(transactions_by_day['DATE'], transactions_by_day['TXN_ID'])
            plt.xlabel('Day')
            plt.ylabel('Number of transactions')
            plt.title('Transactions over time')
            plt.xticks(rotation=90)
            pdf.savefig()
            plt.close()

            # Plot histogram of PACK_SIZE
            plt.figure(figsize=(10, 5))
            plt.hist(transaction_data['PACK_SIZE'])
            plt.xlabel('PACK_SIZE')
            plt.ylabel('Frequency')
            plt.title('Histogram of PACK_SIZE')
            pdf.savefig()
            plt.close()

            # Plot average price per unit by LIFESTAGE and PREMIUM_CUSTOMER
            x_labels = ['-'.join(map(str, idx)) for idx in avg_price.index]
            plt.figure(figsize=(10, 5))
            plt.bar(x_labels, avg_price.values)
            plt.xlabel('LIFESTAGE and PREMIUM_CUSTOMER')
            plt.ylabel('Average Price per Unit')
            plt.title('Average Price per Unit by LIFESTAGE and PREMIUM_CUSTOMER')
            plt.xticks(rotation=90)
            plt.tight_layout()
            pdf.savefig()
            plt.close()

        print(f"Analysis PDF saved to {output_pdf}")

    except Exception as e:
        print(f"An error occurred: {e}")


# Images for visualizations



## How to Use

 Clone the repository: [Click Here](https://github.com/edigasreelekha/Retail_startegy-analyticals)


## Contributing

Feel free to contribute to this project by submitting issues or pull requests.

## License

MIT License

Copyright c 2025 Ediga sreelekha

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.


