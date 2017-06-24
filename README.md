tRemedy
=======

Remedy connectors for Talend.

Although the two examples below are focused mainly on attachments, they are aimed to give the reader a general understanding of how to use the connectors individualy without having to include too many examples.

**_Invitation to contribute:_**  If you have experience in developing connectors for Talend, I need some assistance, especially to connect to Remedy and extract schemas;  Feel free to contribute.

#### Context parameters

First define your context parameters to include the Remedy host, port, username and password.

![alt tag](https://github.com/johanwasserman/images/blob/master/context.png)


#### Find a record and download an attachment


The job establishes a Remedy connection, finds a record in the TMS:Task form and download the first attachment to disk.
We are using Task as an example, any form that contains attachments can be used in the same way.
![alt tag](https://github.com/johanwasserman/images/blob/master/save.png)



###### tRemedyConnection
You can use a single connection to execute all your Remedy transactions in.  Just remember to close that connection again by using the tRemedyClose connector.  This way you only need to specify your connection parameters once.  In this example we are using the Context parameters defined above.

![alt tag](https://github.com/johanwasserman/images/blob/master/tRemedyConnection.png)


Note that we are mapping the Task ID as the "file store".  This will be explained further in the tRemedyAttachment connector below.

![alt tag](https://github.com/johanwasserman/images/blob/master/saveMap.png)


###### tRemedyIn
Notice the use of "Use Existing Connection".  Alternatively you can specify connection parameters for individual tRemedy components.
The schema needs to be defined manually (for now) and you have to replace all special characters, including spaces, in the Remedy field names with underscore (\_) or Talend will not accept them.  The connectors are clever enough to translate the underscore back to the correct Remedy field name.  

For this job, the Remedy schema was pretty much defined in the tMap above.
The query parameter use the standard Remedy query format, as if you would use the Advanced Search bar.  The only difference is that you would need to use escape (\\") for double quotes (") as in the example below.

![alt tag](https://github.com/johanwasserman/images/blob/master/tRemedyIn.png)


###### tRemedyAttachment (downloading)
To download attachments, you have to provide a base path.  We will use the Task ID as a subdirectory for saving the attachment in.  This neatly ties up all attachments for a specific Task.
In this example we only download the file in the first attachment position.  You can add more in the "Attachment/s" field.

![alt tag](https://github.com/johanwasserman/images/blob/master/tRemedyAttachDown.png)


This is just an example output.  If you notice any warnings (below in red) those are simply becasue I'm running Talend as a user that does not have permission to open or close Registry Keys on Windows.

![alt tag](https://github.com/johanwasserman/images/blob/master/output.png)


###### tRemedyClose
A tRemedyClose only need to be used where there is a tRemedyConnection.  This connector ensures that your Remedy connection is properly closed.

![alt tag](https://github.com/johanwasserman/images/blob/master/tRemedyClose.png)


#### Create a record with an attachment

This example connects to MongoDB (our staging data store), and for each matching record it will update an existing Task's Work Info by uploading an attachment from a directory and in this case, create a new "TMS:WorkInfo" record.

![alt tag](https://github.com/johanwasserman/images/blob/master/load.png)

I am excluding descriptions for the tRemedyConnection and tRemedyClose connectors, see [above](https://github.com/johanwasserman/tRemedy#find-a-record-and-download-an-attachment) for details.

Note that in this case the data from MongoDB includes a file name string in the attachment1 field, as well as the Task ID for which a Work Info record will be created.

![alt tag](https://github.com/johanwasserman/images/blob/master/loadMap.png)


###### tRemedyAttachment (upload)
Again we secify the attachment base directory, and this time we include the Task ID and file name from MongoDB to build a "File Name" parameter as a complete path to where the file is stored (on disk).  These two parameters forms the "subdirectory"/"filename" part to append t the base directory for a full path to the file.  This example also deletes the file on disk once it's uploaded to the atatchment field.

![alt tag](https://github.com/johanwasserman/images/blob/master/tRemedyAttachUp.png)

###### tRemedyOut
The tRemedyOut connector pretty much works exactly like the Remedy Import Tool.  Don't forget to define the Unique Key in the schema!
![alt tag](https://github.com/johanwasserman/images/blob/master/tRemedyOut.png)


#### Deleting records

###### tRemedyDelete
This connector will permanently delete a Remedy record based on the identifier you specify in the Entry ID field.  Use carefully!
![alt tag](https://github.com/johanwasserman/images/blob/master/tRemedyDelete.png)

#### Other

###### tRemedyGUID

This connector will put a Remedy generated GUID in the field you specify.  It works exactly tghe same as Remedy's runprocess "Application-Generate-GUID".  You can even specify a prefix if you want.

See the [BMC doumnets](https://docs.bmc.com/docs/display/public/ars9000/Process+commands) for more info.
