===
### Table of Contents
1. [Translate Default Template to Your Language](#translation)

2. [Saving Language File As Per ISO Language Code](#save-file)

3. [Change Front End Drop Down Menu](#change-menu)
___

<a name="create-lang-file"/>

###1. Translate Default Template to Your Language
Localization is supported in Aptus FonB v1.0.5 or higher. You will have to perform some minor steps to translate and use  FonB in your language. Stock FonB v1.0.5 is released with 5 languages, i.e. English, French, Arabic, Traditional Chinese, Hebrew and Urdu. We are currently working on more languages and will be updating the Documentation for any new translation.

The Default Template (with empty translated strings) is as under. You will either have to monitor the same Localization Directory for newest language files or populate it with your own translation:

```
{
	"Settings":"",
	"Your mobile number is used for transferring calls." :"",
	"General Settings" :"",
	"Select Allow" :"",
	"This application can capture what your microphone hears, until you close the website." :"",
	"Flash Mic Settings" :"",
	"Display by \"First Name\"" :"",
	"Show by Users" :"",
	"Advanced Search" :"",
	"All" :"",
	"None" :"",
	"Please upload your personal phonebook in Microsoft Outlook CSV file format:" : "",
	"Close" :"",
	"Upload" :"",
	"Select" :"",
	"Filter" :"",
	"Search by direction" :"",
	"Search by dialed number" :"",
	"Call History" :"",
	"Search by dialed number" :"",
	"Search by date" :"",
	"Recorded Call" :"",
	"Before this date:" : "",
	"More than:" : "",
	"minutes" :"",
	"Choose File..." :"",
	"Import Contacts in Outlook format CSV" :"",
	"Search" :"",
	"Records" :"",
	"of" :"",
	"Contacts" :"",
	"contacts" :"",
	"Quick Search" :"",
	"Action" :"",
	"Add New Contact" :"",
	"Delete Selected Contacts" :"",
	"Import from CSV" :"",
	"All Contacts" :"",
	"Internal Contacts" :"",
	"Personal Contacts" :"",
	"Google Contacts" :"",
	"Highrise Contacts" :"",
	"Synchronize" :"",
	"Recent Imports" :"",
	"Total" :"",
	"Import" :"",
	"a new personal list" :"",
	"Last synched at:" : "مشاركة مزامنته في",
	"All Calls" :"",
	"Inbound" :"",
	"Outbound" :"",
	"Missed Calls" :"",
	"Any Time" :"",
	"Today" :"",
	"Since Yesterday" :"",
	"Since Last Week" :"",
	"Since Last Month" :"",
	"General" :"",
	"Rewrite rules" :"",
	"Language" :"",
	"English" :"",
	"Español" :"",
	"Italiano" :"",
	"Nederlandse" :"",
	"العربية" :"",
	"русский" :"",
	"עברית" :"",
	"中国的" :"",
	"français" :"",
	"日本人" :"",
	"Mobile Phone" :"",
	"Display Preferences" :"",
	"Display by \"Last Name\"" :"",
	"Maximum page size" :"",
	"Show" :"",
	"conversations per page" :"",
	"Save Changes" :"",
	"Cancel" :"",
	"Contacts Settings" :"",
	"Grant access to Google contacts" :"",
	"Revoke access to Google contacts" :"",
	"Now you can synchronize your personal contacts with your Google account" :"",
	"Learn more" :"",
	"Highrise site name" :"",
	"Highrise authentication token" :"",
	"Log into your Highrise account, click the \"My Info\" link (top right), then click the \"API token\" tab" :"",
	"Verify and Sync Highrise" :"",
	"Delete Contacts" :"",
	"Delete All Your Contacts" :"",
	"Match" :"",
	"Replace" :"",
	"Match All Numbers" :"",
	"in the beginning" :"",
	"AND" :"",
	"prefix it" :"",
	"with" :"",
	"Add" :"",
	"in the beginning and prefix it with" :"",
	"in the beginning and replace it with" :"",
	"Delete" :"",
	"All Numbers" :"",
	"and prefix them with" :"",
	"Nothing" :"",
	"Haven't synchronized google contacts?" :"",
	"Begin Now" :"",
	"No rewrite rules set!" :"",
	"Begin Now" :"",
	"Internal" :"",
	"My" :"",
	"iPhone" :"",
	"Google" :"",
	"Highrise" :"",
	"Haven't synchronized google contacts?" :"",
	"CSV File Upload" :"",
	"Your Flashphone is now Ready" :"",
	"You are now able to make & receive calls from your web browser. Though you are strongly adviced to always test your mic before dialing or answering calls. Go to your <strong>Settings</strong> tab & configure your <span style='color:fb8a31;''>Flash Mic Settings</span>" : "",
	"25" :"",
	"50" :"",
	"100" :"",
	"Personal" :"",
	"Drop" :"",
	"Hold" :"",
	"Conference" :"",
	"Transfer" :"",
	"WebPhone" :"",
	"Phone" :"",
	"Mobile" :"",
	"Mute" :"",
	"There should be number to match against when using match or replace rule. Only numeric values and +, * and # sings are acceptable." :"",
	"There should be number for replacement when using match or match all rule." :"",
	"Number for matching should be numeric or may contain +, * and # signs" :"",
	"Number for replacement should be numeric" :"",
	"First Name" :"",
	"Last Name" :"",
	"Company" :"",
	"Company Name" :"",
	"Add phone number" :"",
	"Number" :"",
	"e.g. Mobile" :"",
	"Where to save this contact?" :"",
	"My Contacts" :"",
	"Work Mobile Home Car Fax Skype Other" :"",
	"Add this contact" :"",
	"All contacts deleted." :"",
	"contacts imported from Google contacts" :"",
	"From:" :"",
	"To:" :"",
	"Add Contact" :"",
	"Date:" :"",
	"Duration:" :"",
	"Recording:" :"",
	"Download audio" :"",
	"Search contacts or type number to dial" :"",
	"Dial Pad" :"",
	"Webphone" :"",
	"IP Phone" :"",
	"Mobile" :"",
	"Quick Dial" :"",
	"Logout" :"",
	"Extension Number:" :"",
	"Pin Code:" :"",
	"Login" :"",
	"Wrong username/password" :"",
	"Note:" :"",
	"Goto Highrise page" :"",
	"Update Highrise note" :"",
	"No Active Calls" : ""
}

```
<a name="change-name"/>
###2. Saving Language File As Per ISO Language Code
Once you have translated your file, you need to save it as per corresponding [ISO 639-1 Standard Language Code](http://http://en.wikipedia.org/wiki/List_of_ISO_639-1_codes). For instance, you have translated your file to German, you will have to save it as `de.json` in directory `/usr/local/PhoneB/i18n/`. Same goes for other languages. You can always check if your JSON file is valid or not at http://jsonlint.com/ .


<a name="change-menu" />
3. [Change Front End Drop Down Menu](#change-menu)
Open using settings file using `vim` file:

```
/usr/local/PhoneB/templates/default/settings.html
```
Please open the same file on corresponding path if you are using different BaseDir. Once opened, search for string `general_language` in `vim` by typing `/general_language` followed by `Enter`. It will take you to the corresponding string in `index.html`. Here, you will see HTML equivalent of Drop Down options like this:

```
 <select id="general_language">
                          <option value="en">{{t "English"}}</option>
                          
                          <option value="ar">{{t "ﺎﻠﻋﺮﺒﻳﺓ"}}</option>
                          
                          <option value="cn">{{t "中国的"}}</option>
                          
                          <option value="ur">{{t "ﺍﺭﺩﻭ"}}</option>
```
Add your language ISO 639-1 Standard Code in Option Value to be picked from JSON files and the name of your language you want to display after that. Thus, if you want to add German Language option (assuming you have already added `de.json` in previous step), youw will have to add another line in this code block so that it becomes:

```
 <select id="general_language">
                          <option value="en">{{t "English"}}</option>
                          
                          <option value="ar">{{t "ﺎﻠﻋﺮﺒﻳﺓ"}}</option>
                          
                          <option value="cn">{{t "中国的"}}</option>
                          
                          <option value="ur">{{t "ﺍﺭﺩﻭ"}}</option>
                          
                          <option value="de">{{Deutsch}}</option>
```
