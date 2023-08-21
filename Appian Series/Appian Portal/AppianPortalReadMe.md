
- Appian Portals is currently only supported for Appian Cloud customers. Additionally, if your environment is behind a VPN, you cannot connect a portal to it to read or write data.
- Portal worked on isolated architecture that is independent from Appian.To upload files in Appian ```File upload or signature component --->Use a!submitUploadedfiles() function in submit button ---> specify target document folder ---> service account with Viewer permissions to target folder```. To download document ```Document download link or document image ---> Files to download ---> Document folder for our files ---> service account with viewer permissions to documents and folder ```.
- To use partially compatible capabilities and functions, connect to them using a web API and integration.The ```a!submitUploadedFiles() and a!verifyRecaptcha()``` functions only work in a published portal. You can't test them in the interface object itself.
- In your Appian environment, make sure you are in the ```Portals Publishers group``` to update, delete,publish portals.If Portal Publishing Manager site is not visible to us make sure we are in Portals Publishers Group.
- To setup with ReCaptcha with Portal - ```Create project in Google Admin console``` ---> ```Note Project ID``` ---> ```Create API and SITE keys using Google documentation```(API Key allow portal to communicate with reCaptcha server,site key allow to use reCaptcha on our portal since Appian only supports v3 reCaptcha so our site key ```must be score-based site key```) ---> In portal page,click "Use google reCaptcha to protect your page" ---> paste site and API key in that box in manager ---> Enter reCaptcha project ID when we created project in google.


### [URL Parameters](https://docs.appian.com/suite/help/23.3/url-parameters.html#) 

- Url parameters are in format ```?search=<text>&<parameter1>=<parameter1Value>```
![URL Parameters](image/AppianPortalReadMe/1692598014087.png)
- ```a!urlForPortal()``` function to get encrypted URL link of portal page with parameter values as input and open the link which by default set the parameter values as input to that page.
<ul style="list-style-type: square; list-style-position: inside;">
<li>Parameter <b>portalPage:</b> use <span style="color:orange">portal!</span> to see a list of portals. Use <span style="color:orange">.</span> to see a list of portal pages</li>
<li>Parameter <b>urlParameter:</b> use<span style="color:orange"> a!map()</span> to list your URL parameter names and values as key-value pairs</li>
<li>Function allows to keep link upto date even web identifiers or RI are modified</li>
<li>If portal has only one page, the <span style="color:orange">page</span> won't display in url like in image, else it will show like in image 
<img src="image/AppianPortalReadMe/1692625996207.png" alt="Sample Url"></img>
</ul>

- set default value to Rule Input so it will redirect when parameter has no value.The rule input uses the default value when: 1.A user accesses the page via the header bar in the portal. 2.A link doesn't use the URL parameter. 3.A user changes or deletes any character in the encrypted URL parameter string. 4.Someone constructs a plaintext link that would otherwise ignore a URL parameter due to error handling, such as if they misspell the URL parameter name.<p style="color:red">If the default value isn't configured and a URL parameter doesn't provide the value, the rule input value is null.</p> 
<p style="color:red">Appian automatically republish portals that link to other portals the links are kept up to date when the linked portal is updated.</p>

- <b>To enable URL Parameters</b>
<ul style="list-style-type: square; list-style-position: inside;">
<li>Create rule input (should not be array,must contain primitive data types,should not present in precedents of portal page)</li>
<li>Configure rule inputs in portal page ( Add portal page > configure rule input ( Encrypt URL Parameters - To encrypt parameters, RI - RI that used to set initial values only appear, Enable in URL - Only appears when Encrypt URL parameters is deselected, URL Parameter Name - Parameter name, Default Value -  set default values that support <span style="color:orange">100 values or less<span></li>) > Done)
<li>Add service account if encrypted parameters used, then publish portal (<a href="https://docs.appian.com/suite/help/23.3/portals-create.html#add-a-service-account">Add service account</a> > Goto configurations and turn on publish toggle > Save changes)</li>
<li>Add,rename,delete RI in portal's page interface then edit the portal page to update the rule input configurations and republish it.</li>
<span style="color:black;background-color:yellow">Above only affects RI that used to set initial values on portal pages. Other RI don't need to be updated if they change.</span>
</ul>

- Several places you can link portal are
<ul style="list-style-type: square; list-style-position: inside;">
<li>Interface,Expression rule,process model</li>
<li>Another portal (Using <span style="color:orange">a!urlForPortal()</span> we can link, must have <span style="color:orange">service account configured</span></li>
<li><a href="https://docs.appian.com/suite/help/23.3/url-parameters.html#linking-from-an-external-website"> External website </a></li>
</ul>

- Use plaintext URL parameters when:
<ul style="list-style-type: square; list-style-position: inside;">
<li><b>Linking to the portal from an external website </b></li>
<ul style="list-style-type: square; list-style-position: inside;">
<li>Create Encrypted URL parameters using <span style="color:orange">a!urlForPortal()</span>. This encryption is specific to environment, you can't construct a link in development environment and modify it to be used in a production environment. You need to use <span style="color:orange">a!urlForPortal()</span> in a production environment to get an encrypted link for a production portal.
<li>To use URL parameters to link to a portal page from an external website,use plaintext URL parameters</li>
<li>If you must use encrypted parameters, use a web API that uses <span style="color:orange">a!urlForPortal()</span> to construct the link and invoke the web API from your external website.</li>
</ul>
<li>Using a third-party web service that needs to pass an exact value</li>
</ul>


# Recommendations

### URL Parameters

- Don't create portals that allow users to update information in a form they already submitted. If users need to update information they submitted, they should have an Appian account and update the information through a site rather than a portal.
- Don't use this method to pre-populate user-specific information on a form in portals. Portals are intended for unauthenticated users to fill out generic forms.



