# Netdisco-CVE-2023-37624
## Description
Netdisco < 2.063000 was found to contain an open redirect vulnerability due to insufficient validation of an input parameter. An attacker may exploit this vulnerability to redirect users to arbitrary web URLs by tricking them into clicking on a specially crafted link.

## Technical Details
If a user attempts to access a page within Netdisco without a valid session, they are redirected to the login page. Once logged in, the user will be redirected to the page they originally attempted to visit. Insufficient validation of the original url allows a malicious actor to specify an arbitrary URL in the original GET request to the login page.

To reproduce the vulnerability the following URL may be provided:

```
http://netdiscoexample.host///www.google.com
```
This will redirect the user to Google after the login process is complete.

# Netdisco-CVE-2023-37623
## Description
Netdisco < 2.063000 was found to contain multiple stored cross-site scripting (XSS) vulnerabilities. An attacker may exploit this to perform unauthorised actions on behalf of a user.

## Technical Details
A stored Cross-Site Scripting vulnerability was discovered in the main search box of the web application. This vulnerability is the result of insufficient sanitisation of the System Name device field. When a search for a device is performed by typing in an IP address, once at least three characters are entered in the search bar matching device System Names are presented in a drop down list by the typeahead feature. If the System Name contains HTML tags, the browser will interpret the contents as valid HTML.

To reproduce the vulnerability, perform the following steps:

1. Log in to Netdisco as an administrator.
2. Go to the admin dropdown menu and select Pseudo Devices.
3. Enter any IPV4 address as the Device IP (eg. 192.168.0.1), then enter the Device Name below, and click the Add button.

```
<script>alert(1)</script>
```

![addpseudodevice](https://github.com/benjaminpsinclair/Netdisco-CVE-2023-37623/assets/93361940/d7d043bf-18c7-4115-ab99-21d17674c108)

4. Then in the main search box containing the text "Find Anything", enter the first three numbers of the Device IP (i.e. 192).
5. Observe that the alert box has executed.

![searchbarpayload](https://github.com/benjaminpsinclair/Netdisco-CVE-2023-37623/assets/93361940/4a3dc319-4b07-4013-ae46-dea74da761f0)

Note: There are other ways to inject a payload into the System Name without creating a pseudo device and requiring an admin login.

For example an attacker may set up SNMP on their machine and insert a Java Script payload as the Device Name, as in the configuration file below:

<img width="713" alt="SNMPconf" src="https://github.com/benjaminpsinclair/Netdisco-2023-Advisory/assets/93361940/5f2cf608-4f35-473a-a306-645be0ab5d48">


To inject the payload into Netdisco it is possible to submit a discovery request for the attacker controlled IP. This can be done by enticing an administrator with a valid session into clicking on a link that utilises the Open Redirect vulnerability from CVE 2023-37624. For example; 'https://netdiscoexample.host///attacker.host/csrf.html', where netdiscoexample.host is the Netdisco URL and attacker.host/csrf.html is a malicious link containing the following HTML:

```html
<body onload="document.form.submit()">
   <form action="https://netdiscoexample.host/admin/discover?device=attackerIP" method="POST" name="form" style="display;none;">
</form>
</body>
```
If the administrator clicks on this link then attacker's machine will be discovered, the payload loaded through SNMP and finally executed when the IP is entered in the search bar, as in the screenshot below.

![SNMPXSS](https://github.com/benjaminpsinclair/Netdisco-2023-Advisory/assets/93361940/28172d61-79bd-4f71-9eb8-b17a01456602)



An additional stored Cross-Site Scripting vulnerability was found in the Job Queue page. This vulnerability is the result of insufficient sanitisation of the Param job field.

To reproduce the vulnerability,  perform the following steps:

1. Log into Netdisco as an administrator.
2. Go to the Inventory page and open any device. If a device is not present, then add a Pseudo Device as above.
3. Click in the contact field and enter the following text:

```
<script>alert(1)</script>
```

![editcontact](https://github.com/benjaminpsinclair/Netdisco-CVE-2023-37623/assets/93361940/3cfbd22e-31ed-43e1-8ee7-f7c74dbeae7e)

4. Go to the Admin dropdown menu and select Job Queue.
5. Esnure the job has a Status of "Done", then hover the mouse point over the Param field containing the text "<script>alert(1)</script>".
6. Observe that the alert box has executed.
   
![mouseovercontact](https://github.com/benjaminpsinclair/Netdisco-CVE-2023-37623/assets/93361940/6de9f291-682b-48b4-bcc3-de006cfc2ee0)

## Additional Credit
Thanks to stanleyjobson <https://stanleyjobsonau.github.io>
