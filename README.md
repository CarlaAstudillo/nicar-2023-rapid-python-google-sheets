# NICAR 2023: Rapid Python analysis to Google Sheets

This is a repository for an example on how to quickly analyze recurringly released datasets and create Google Sheets for reporters. Here is a link to the [Google Slides](https://docs.google.com/presentation/d/1e2t5bhzh6YcuGU2dgpy2E6fE3ELUlTD7YRZ3fqrOF9g/edit?usp=sharing).


## Setup

This project requires **Python 3.7** and uses [`pipenv`](https://pipenv.readthedocs.io/en/latest/) to manage dependencies.

First create your `pipenv` environment.

```sh
pipenv --three
```

In your `Pipfile`, make sure these dependencies are included: `gspread`, `oauth2client`, `gspread-dataframe`, `google-api-python-client`. These dependencies are the ones needed to upload to Google Sheets.

Then install the dependencies.

```sh
pipenv install
```

Now you can step into the `pipenv` shell (AKA environment) and get to work!

```sh
pipenv shell
```

## Set up Google Cloud Console project and service account

You can use your Google Account to sign up for [Google Cloud Console](https://console.cloud.google.com/). Create a new project and give it a human-readable name. You can follow the instructions on how to do that [here](https://cloud.google.com/resource-manager/docs/creating-managing-projects).

Next, create a service account within the project, which you can follow the instructions [here](https://cloud.google.com/iam/docs/creating-managing-service-accounts) This is basically an account which will handle the Google Sheets API and let us upload to Google. Give it a good account name and description. You also don't have to set access controls now and can just click Done. However, there are different levels of access to can grant others to this account.


## Get credentials

Next, create the service account keys to your newly created service account. Select the project and the email account of the service account you have just created. Click the `Keys` tab and then the `Add key` drop-down menu. From there, select `Create new key` and then `Select JSON` as the Key type. Click `Create`. You should be able to download a JSON file that looks like this:

```
{
  "type": "service_account",
  "project_id": "PROJECT_ID",
  "private_key_id": "KEY_ID",
  "private_key": "-----BEGIN PRIVATE KEY-----\nPRIVATE_KEY\n-----END PRIVATE KEY-----\n",
  "client_email": "SERVICE_ACCOUNT_EMAIL",
  "client_id": "CLIENT_ID",
  "auth_uri": "https://accounts.google.com/o/oauth2/auth",
  "token_uri": "https://accounts.google.com/o/oauth2/token",
  "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
  "client_x509_cert_url": "https://www.googleapis.com/robot/v1/metadata/x509/SERVICE_ACCOUNT_EMAIL"
}
```

Give it a good name and save it as a hidden file somewhere in the same level as your jupyter notebook where you will be able to access. These are your credentials. Also, pay special attention to the `client_email` in your JSON. That's the email you're going to be granting access to your Google directories and sheets. Save that somewhere.

For more detailed instructions for this step, go [here](https://cloud.google.com/iam/docs/creating-managing-service-account-keys).

## Turn on the API

Go to the [Google Cloud console API Library](https://console.cloud.google.com/apis/library) and select your project. In the API Library, select the API you want to enable, in our case, it will be the Google Drive and Google Sheets API under Google Workspace. Click each API page and click `ENABLE.`

For more detailed instructions and information on adding APIs, go [here](https://cloud.google.com/apis/docs/getting-started)

## Add the credentials your notebook

After creating and saving your JSON credentials, go to the [`globals.ipynb`](globals.ipynb) Jupyter notebook and change the name of the JSON credentials to the name that you gave your credentials. This is found in the Google Sheets permissions cell.

```
creds = ServiceAccountCredentials.from_json_keyfile_name('.[CHANGE this name file].json', scope)

```

## Do your analysis and create an aggregate dataframe

In `Rapid Python analysis to Google Sheets for reporters EXAMPLE.ipynb`, I've provided an example of some analysis that I did with a dataset that I won't go into much detail. It all culminates with me creating a dataframe with some aggregate numbers that I want to add to my first tab in my Google Sheet. 

```
# This is where we put the fields that we have to include
agg_columns = ['texas_growth', 'texas_growth_pct_change', 'county_most_pct_change',
               'county_most_num_change', 'number_lost_population']

# This is the data
agg_data = [comma_format(TX_population_change), pct_format(TX_pop_pct_change),county_most_pct_change['county'].iat[0], county_most_num_change['county'].iat[0], number_lost_population]

# This is where I create the dataframe for the aggregate numbers
data_to_add = {'Criteria': agg_columns, 'Totals': agg_data}

df_agg = pd.DataFrame(data_to_add)
```

Afterward, you can add the aggregate dataframe as a hidden sheet called `aggregate_hidden` by adding it here. You can also add other dataframes as other tabs, including the raw data.

```
data_frames_to_add = [{
        'file_data': df_agg,
        'sheet': 'aggregate_hidden',
        'hidden': 'true',
    },
    {
        'file_data': no_small_counties.head(10),
        'sheet': 'top_10_counties_over_10000',
        'hidden': 'false',
    },
    {
        'file_data': df_merge_short.sort_values(by='pop_change_pct', ascending=False),
        'sheet': 'raw_data',
        'hidden': 'false',
    }]
```


## Create a folder and template in Google Drive

In your Google Drive with the account that you used to set up Google Cloud Console service account, create a folder and then create a template with a tab called `aggregate` and a tab called `aggregate_hidden`. In the `aggregate`, you can decorate however you want the Google Sheet to show up for your reporters. However, in the cells where you want numbers to show up, add a formula so the appropriate number in `aggregate_hidden` shows up in the `aggregate` tab. You can see an example [here](https://docs.google.com/spreadsheets/d/1DBcNmB0x1zp3neEFjs1A7ADGIse3tgrZe5oWCibZfOs/).


## Connect the Google Drive folder and template url

In `Rapid Python analysis to Google Sheets for reporters EXAMPLE.ipynb`, there's a cell near the top where you can fill out several variables. Here you can give your new Google Sheet a name. Also, get the folder ID for the folder where you're going to be creating the Google Sheet and put it in `google_folderID`. Then, get the Google Sheet URL for the template sheet you just created and put it in `source_gsheet_url`.

```
## Google Sheet Name

google_sheet_name = "Texas Census analysis (2016 5-year estimate) and (2021 5-year estimate)"

## Google Sheet Directory ID

google_folderID = 'TKTKTKTKTK'


## Google Sheet url for the template

source_gsheet_url = 'https://docs.google.com/spreadsheets/d/TKTKTKTKTKTKTK/'
```

## Run the notebook

Hopefully, the notebook should run and create a new spreadsheet with your analysis in a nicely formatted Google Sheet based on the template you just made. 

A few things that I want to point out that make it work:

The function `createNewSheetIfNotExist` which you can find in [`globals.ipynb`](globals.ipynb) basically goes into your Google Drive folder and looks for a Google Sheet with the name that you specified in `google_sheet_name`. If the Google Sheet with that specific name doesn't exist, it will create a brand new Google Sheet. If it does exist, it will just get the existing one.

The notebook will then open your newly created or already existed Google Sheet and loop through `data_frames_to_add` and uploads each dataframe to a different tab in the Google Sheet. If it already exists, it will erase all of the existing data before adding it again. If in `data_frames_to_add` the key "hidden" has a value of "true" like in the `aggregate_hidden`, it will hide it.

```
# This checks to see if the worksheet exists. If not, it will create a new one.
for file in data_frames_to_add:
    print('Uploading the ' + file['sheet'] + ' file to its Google spreadsheet')
    #If the tab is already in the worksheet, update it. If not, add a new tab
    data_sheet = data_workbook.worksheet(file['sheet']) if file['sheet'] in worksheets_list else data_workbook.add_worksheet(file['sheet'], rows=1000, cols=40, index=None)
    #Clears any existing data in the existing datasheet
    data_sheet.clear()
    # Add the data to tab
    set_with_dataframe(data_sheet, file['file_data'])
    if file['hidden'] == 'true':
        # Hide this tab if it says in needs to be hidden
        body = {'requests': [{
            'updateSheetProperties': {
                'properties': {
                    'sheetId': data_sheet.id,
                    'hidden': True
                },
                'fields': 'hidden'
            }
        }]}
        data_workbook.batch_update(body=body)
```

Finally, the notebook then checks all of the tabs in your Google Sheet.

```
# First, check to see if tabs already exist. Need to create a list of tabs in this particular worksheet
worksheet_objs = data_workbook.worksheets()
worksheets_list = []

for worksheet in worksheet_objs:
    worksheets_list.append(worksheet.title)
```

If there's a sole `Sheet1` tab, that means that this is a brand new Google Sheet and so it will need to get Google Sheet template that you created and copy the first tab (along with all of the formulas and formatting) to your newly created Google Sheet. It will then rename it `aggregate` and rearrange it so it's the first tab you see and erase `Sheet1`.

```
# This finds if this is a new Google Sheet that was just created and copies the template Google Sheet `aggregate` section to the new Google Sheet.
# Then it renames that new tab aggregate, erases the existing 'Sheet1' and reorders 'aggregate' to be the first tab shown
if 'Sheet1' in worksheets_list:
    source_worksheet_name = 'aggregate'
    dest_gsheet_url = new_sheet['id']

    templateWS = client.open_by_url(source_gsheet_url).worksheet(source_worksheet_name)
    target_ws_props = templateWS.copy_to(dest_gsheet_url)
    target_ws = gspread.Worksheet(client.open_by_key(dest_gsheet_url), target_ws_props)
    target_ws.update_title("aggregate")

    created_sheet = client.open_by_key(dest_gsheet_url)

    sheet_to_be_deleted = created_sheet.worksheet('Sheet1')

    created_sheet.del_worksheet(sheet_to_be_deleted)
    created_sheet.reorder_worksheets([created_sheet.worksheet('aggregate'), created_sheet.worksheet('aggregate_hidden')
                                        , created_sheet.worksheet('top_10_counties_over_10000'), created_sheet.worksheet('raw_data')])
```


