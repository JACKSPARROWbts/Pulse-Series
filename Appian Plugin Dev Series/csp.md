# Appianplugin.xml

- The key here should be unique among the all smart services
- To generate automatic appian-plugin.xml use appian extension

# Properties

- To define name for error in output, Use ```output.<annotation name>.displayName``` in properties file

# Errors

- ```The smart service module was invalid: Smart service input and output count does not match the count for the existing smartservice registered``` - change the key value of smart service in appian-plugin.xml file

# Some Codes to use

- <p style="color:orange">public void run() throws <span style="color:red">SmartServiceException</span>{ LOG.debug("something");err="The text to display at error goes here";}</p> and <p style="color:orange">@Name("<span style="color:red">error</span>") <br>public String get<span style="color:red">Error</span>(){return err;}</p> for stop flow when err occurs at smart service.
- ```public Boolean modifyFileName(ContentService cs,@Parameter(@required=true()) String name, @Parameter @DocumentDataType Long Document){try{ Document doc=cs.download(document,ContentConstants.VERSION_CURRENT,false)[0]; System.out.println("External fileName"+doc.getExternalFilename()); System.out.println("Internal FileName:"+doc.getInternalFilename()); doc.setExternalFilename(nameToModify); cs.upload(doc,ContentConstants.VERSION_CURRENT); if(nameToModify!= doc.getExternalFilename()){ Log.error("File not modified");}} catch (PrivilegeException | InvalidContentException | InvalidVersionException | StorageLimitExceptio ){ Log.eror(e);}}```
- To convert download file stream to File use ```File sourceFile=new File(cs.download(sourceDocument,ContentConstants.VERSION_CURRENT,false)[0].getInternalFilename())```
- Use <span style="color:orange"> throw new SmertServiceException.Builder(PublicClassName.class,e).build();</span> to throw exception in smart service
- <p style="color:red"> @PaletteInfo(paletteCategory="Appian Smart Services",palette="Document generation")</p> for values in process model
- To use annotation refer [here](https://docs.appian.com/suite/help/21.4/Custom_Smart_Service_Plug-ins.html#defining-data-types-used-by-inputs-and-outputs) jand in java to return dictionary use Typedvalue like ```public TypedValue className(){}```

# Java packages

- ```com.apache.log4j.Logger``` to store logs when CS face any error or working fine

# Best Practice

- Don't include all classes like import java.* , just import required class only
- Remove System.out.println at finally
- Don't include contentService, AppianService in everywhere , use it in injection service only that is in parameter passing part

# Others

- OOTB connected system has 6 authentication methods whereas Custom connected system means which we built
- On premises means within our database
-For java packages search in MVNRepository website
-For smart service, we can define our own custom functions.If we create any class we need to define function in .xml file.See [here](https://docs.appian.com/suite/help/21.4/Custom_Function_Plug-ins.html).To describe it in properties file, Use ```function.<name>.param.query.description```

![Sample HelloWorld](image/readme/sampleCode.PNG)
![Types of datatypes](image/readme/datatypes1.png)
![Types of Datatypes](image/readme/datatypes2.png)
![Types of datatypes](image/readme/datatypes3.png)
![Function declaration](image/readme/functions.png)
![sample code for using cdt with smartservice](image/readme/cdtWithSmartService.png)


