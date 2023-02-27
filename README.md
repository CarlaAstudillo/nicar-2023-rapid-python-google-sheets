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

Give it a good name and save it as a hidden file somewhere in your project where you will be able to access. These are your credentials. Also, pay special attention to the `client_email` in your JSON. That's the email you're going to be granting access to your Google directories and sheets. Save that somewhere.

For more detailed instructions for this step, go [here](https://cloud.google.com/iam/docs/creating-managing-service-account-keys).

## Turn on the API

Go to the [Google Cloud console API Library](https://console.cloud.google.com/apis/library) and select your project. In the API Library, select the API you want to enable, in our case, it will be the Google Drive and Google Sheets API under Google Workspace. Click each API page and click `ENABLE.`

For more detailed instructions and information on adding APIs, go [here](https://cloud.google.com/apis/docs/getting-started)

## Create your notebook


Add credentials


## Create a directory and template in Google Drive

Add the gmail

