This project analyzes FDA accelerated drug approvals, focusing on identifying relationships between various factors such as approval times, fees, and conversion times. Data is read from FDA's Accelerated Approval PDF and combined with OpenFDA's data. The analysis includes data cleaning, merging different datasets, and performing various calculations and transformations to derive meaningful insights. 

Steps Performed
1. Import Libraries and Load Data

    Langauge: Python 3.12.4
    Libraries: Import required libraries, including what is in requirements.txt
    Loading Data: Load the CDER Drug and Biologic Accelerated Approvals PDF from https://www.fda.gov/drugs/nda-and-bla-approvals/accelerated-approvals

2. Data Cleaning and Preparation

    Extract tables from each page of the PDF using tabula's OCR, and combine into a single dataframe
    Remove artifacts within values and remove background text rows pulled in via OCR
    Transform variables to create unique identifiers based on application type, number, ORIG or supplement, and their respective number. This will be used later as a pseudo primary key when joining other dataframes.
    Date Conversion: Date columns are converted to datetime objects for easier manipulation and calculation.
    Creating Age Categories: Age groups are created by finding the terms "Adult", "Pediatric", and "Adult" and "Pediatric" within the approved indication wording. Indications without these wordings included in the indication text are categorized as "Unspecified".
    
3. Data Merging with OpenFDA data (See the OpenFDA Notebook Steps)

    Import the OpenFDA parquet file as a pandas dataframe
    Initial Merges: The datasets are merged based the unique application type, number, and supplement number
    Transformations: Columns cleaned and route of administration was joined with the primary dataframe. Manual cleaning was performed to fill in missing values by manual review of Drugs@FDA labeling.
    Calculations: Additional columns are calculated, such as the time taken for approval in months.

4. Specific Data Handling

    Drug company origin: Manual review of drug company web sites was performed to find the company's primary origin.
    Fee Assignment: PDUFA fees are assigned to the merged data based on submission categories and dates.
    Conditions: Specific conditions are applied to update the 'Fees' column, particularly considering changes in fee collection policies over time given PDUFA VI removed efficacy supplement fees.

5. Export Dataframe

    Export: The final dataframe is exported to an Excel file for further analysis and visualizations in Tableau.

OpenFDA Notebook Steps Performed
1. Import Libraries and Load Data

    Create config file containing your personal API key to OpenFDA.
    Set the base url to direct at the drugs@fda database "drugsfda.json".
   
2. Set API Search Parameters

    Search by specific NDA and BLA numbers as taken from the Accelerated Approvals PDF, as stated in the AA.ipynb.
        
3. Store the data being pulled from OpenFDA

    Set requests per minute and delay for the max limit from OpenFDA
    If response code is returned 200, then the records will be pulled. If needed, additional data will be pulled at a delay to ensure all data is pulled given the pull limitations per minute.

4. Check and normalize the JSON data to be ingested into a pandas dataframe

    First, set a list for each section of the data we are interested in.
    For any item within each respective section, data is normalized from JSON to pandas.
    Lastly, drop the original nested columns to leave the normalized variables.

5. Export Dataframe as a Parquet file

    Due to potential performance issues when pulling much more data than in this project, storing the data in a Parquet file will provide a smaller and more efficient way of storing the data for use back in the AA.ipynb. This promotes overall efficiency and portability of the data being pulled from OpenFDA.