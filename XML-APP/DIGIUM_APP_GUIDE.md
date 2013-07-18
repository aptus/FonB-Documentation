Aptus has developed custom dedicated App for Digium that works on Javascript instead of XML. This was possible because of Digium API for their IP Phones. By far, Digium displays one of the most elegant Apps for IP Phones and stand unique among all the other vendors. Further details about FonB App for Digium can be viewed here. Further details can be viewed on [this blog](this blog) by Digium talking about Aptus FonB.

To enable FonB on Digium IP Phones, you need to follow these steps:

1. In order to enable any custom application on Digium Phones, you need to enable Development Environment on your Digium Phones. This can be done using steps mentioned [here](http://phones.digium.com/phone-api/how-to/content/enabling-app-development-mode).

2. Download the Source Code for FonB App for Digium from [here](https://github.com/aptus/Digium-Phonebook-App).

3. Decompress the file (if you've downloaded it as zipped). Open `config.js` file and replace 10.0.8.6 with your FonB Host IP address.

4. Create a zip archive containing all source files. You can do it by executing `zip app.zip *` command on linux terminal, inside directory that contains Digium app source files. From GUI, you might select all files and choose compress or archive from context menu.

5. Open **http://{your.digium.phone.ip}/app_dev** and login with **admin** account credentials.

6. Upload zip file you created in step 3 and save App settings.

You can access the app now in Digium IP Phone by navigating to **Menu > Applications** and selecting **Aptus FonB**
