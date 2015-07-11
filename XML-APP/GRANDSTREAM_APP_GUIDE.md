1. Go to **Menu > Status > Network Status** and check the IP assigned to your IP Phone.

2. Open the Web Interface of your Grandstream Phone by browsing to this IP in your Web Browser.

3. Login using your credentials.

4. Go to **Phonebook** Tab and click **Phonebook Management** under the drop down menu.

5. For option **Enable Phonebook XML Download** select the parameter **Enabled, Use HTTP**.

6. The field **Phonebook XML Server Path** is a bit tricky part of Grandstream XML Phonebook. The Syntax for this path is:

```
http://[Server Address]:[Listening Port]/[Path to XML App]?user=[Username]&pass=[Password]&PageSize=[Phone Page Size]&abc=
```

where:

`[Server Address]`  = Server Hostname or IP Address

`[Listening Port]`  = Port Number that FonB running on

`[Path to XML App]` = Absolute Location for XML File to be used by Grandstream

`[Username]`        = Username as defined in `users.cfg` config file

`[Password]`        = Password as defined in `users.cfg` config file

`[Phone Page Size]` = Page Size as per Grandstream Phone. For GXP21xx Series it's 2000, for GXP14xx & GXP116x Series it's 500.

The practical example, where we were using GXP2120 Series, our URI was:

```
http://10.0.8.6:8889/xml/en/Grandstream/listphonebook.xml?user=7575&pass=3333&PageSize=2000&abc=
```

7. Provide the optimal Refresh time under **Phonebook Download Interval** field. We recomend 300 to be a normal value making your Phonebook refresh one every 300 seconds.

8. **Save & Apply** to write changes to phone.

9. Once done, go to **Menu > Phone Book** and press **Download** softkey (if it's available. On models where this softkey is not available, just wait for 300 seconds (or the value you have provided in step 7) to be available.

10. Enter into **Local Phonebook** to view your contact details and dial right from there.
