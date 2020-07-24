<h1>OnlineFruitStore</h1>

You'll first need to get the information from the supplier that is currently stored in a Google Drive file.
The supplier has sent data as large images with an associated description of the products in two files
(.TIF for the image and .txt for the description).

There was a supplier-data directory in the linux virtual machine.
Listing its content would give two subdirectories -> images and descriptions
'/images' contain images of various fruits, while '/descriptions' has text files containing the description of each fruit.

>>> cat ~/supplier-data/descriptions/007.txt <br />
would return: <br />
    Mango <br />
    300 lbs <br />
    Mango contains higher levels of vitamin C than ordinary fruits. Eating mango ... <br />

The first line contains the name of the fruit followed by the weight of the fruit and finally the description of the fruit.



<h2> Working with supplier images </h2>
In this section, you will write a Python script named 'changeImage.py' to process the supplier images.
You will be using the PIL library to update all images within ~/supplier-data/images directory to the following specifications:
•	Size: Change image resolution from 3000x2000 to 600x400 pixel
•	Format: Change image format from .TIFF to .JPEG
After processing the images, save them in the same path ~/supplier-data/images, with a JPEG extension.



<h2> Uploading images to web server </h2>
You have modified the fruit images through changeImage.py script. Now, you will have to upload these modified images to
the web server that is handling the fruit catalog. To do that, you'll have to use the Python requests module to send the
file contents to the [linux-instance-IP-Address]/upload URL.
Copy the external IP address of your instance from the Connection Details Panel on the left side and enter the IP address
in a new web browser tab. This opens a web page displaying the text "Fruit Catalog".
you are going to write a script named supplier_image_upload.py that takes the jpeg images from the supplier-data/images
directory that you've processed previously and uploads them to the web server fruit catalog.



<h2> Uploading the descriptions </h2>
The Django server was already set up inn the linux virtual machine to show the fruit catalog for your company.
To add fruit images and their descriptions from the supplier on the fruit catalog web-server, create a new Python script
that will automatically POST the fruit images and their respective description in JSON format.
Write a Python script named run.py to process the text files (001.txt, 003.txt ...) from the supplier-data/descriptions
directory. The script should turn the data into a JSON dictionary by adding all the required fields, including the image
associated with the fruit (image_name), and uploading it to http://[linux-instance-external-IP]/fruits using the Python
requests library.
Note that all files are written in the following format, with each piece of information on its own line:
•	name
•	weight (in lbs)
•	description
The data model in the Django application fruit has the following fields: name, weight, description and image_name.
The weight field is defined as an integer field. So when you process the weight information of the fruit from the
.txt file, you need to convert it into an integer. For example if the weight is "500 lbs", you need to drop "lbs" and
convert "500" to an integer.
The image_name field will allow the system to find the image associated with the fruit. Don't forget to add all fields,
including the image_name! The final JSON object should be similar to:
{"name": "Watermelon", "weight": 500, "description": "Watermelon is good for relieving heat, eliminating annoyance and
quenching thirst. It contains a lot of water, which is good for relieving the symptoms of acute fever immediately.
The sugar and salt contained in watermelon can diuretic and eliminate kidney inflammation. Watermelon also contains
substances that can lower blood pressure.", "image_name": "010.jpeg"}
Iterate over all the fruits and use post method from Python requests library to upload all the data to the URL
http://[linux-instance-external-IP]/fruits



<h2> Generate a PDF report and send it through email </h2>
Once the images and descriptions have been uploaded to the fruit store web-server, you will have to generate a PDF file
to send to the supplier, indicating that the data was correctly processed. To generate PDF reports, you can use the
ReportLab library. The content of the report should look like this:
Processed Update on <Today's date>
[blank line]
name: Apple
weight: 500 lbs
[blank line]
name: Avocado
weight: 200 lbs
[blank line]
...
You will need to pass the following arguments to the reports.generate_report method: the text description processed
from the text files as the paragraph argument, the report title as the title argument, and the file path of the PDF
to be generated as the attachment argument (use ‘/tmp/processed.pdf')

<h4> Send report through email </h4>
Once the PDF is generated, you need to send the email using the emails.generate_email() and emails.send_email() methods.
Define generate_email and send_email methods by importing necessary libraries.
Use the following details to pass the parameters to emails.generate_email():
•	From: automation@example.com
•	To: username@example.com
•	Replace username with the username given in the Connection Details Panel on the right hand side.
•	Subject line: Upload Completed - Online Fruit Store
•	E-mail Body: All fruits are uploaded to our website successfully. A detailed list is attached to this email.
•	Attachment: Attach the path to the file processed.pdf



<h2> Health check </h2>
This is the last part of the lab, where you will have to write a Python script named health_check.py that will run in
the background monitoring some of your system statistics: CPU usage, disk space, available memory and name resolution.
Moreover, this Python script should send an email if there are problems, such as:
•	Report an error if CPU usage is over 80%
•	Report an error if available disk space is lower than 20%
•	Report an error if available memory is less than 500MB
•	Report an error if the hostname "localhost" cannot be resolved to "127.0.0.1"

Complete the script to check the system statistics every 60 seconds, and in event of any issues detected among the ones
mentioned above, an email should be sent with the following content:
•	From: automation@example.com
•	To: username@example.com
•	Replace username with the username given in the Connection Details Panel on the right hand side.
•	Subject line:
Case                                                           Subject line
CPU usage is over 80%                                          Error - CPU usage is over 80%
Available disk space is lower than 20%                         Error - Available disk space is less than 20%
available memory is less than 500MB                            Error - Available memory is less than 500MB
hostname "localhost" cannot be resolved to "127.0.0.1"         Error - localhost cannot be resolved to 127.0.0.1

•	E-mail Body: Please check your system and resolve the issue as soon as possible.
Note: There is no attachment file here, so you must be careful while defining the generate_email() method in the
emails.py script or you can create a separate generate_error_report() method for handling non-attachment email.
Next, go to the webmail inbox and refresh it. There should only be an email something goes wrong, so hopefully you
don't see a new email.

NOTE: You can test the health_check script by using stress module as below:
>>> import stress <br />
>>> stress --cpu 8 <br />
Allow the stress test to run, as it will maximize our CPU utilization. Now run health_check.py by opening another
SSH connection to the linux-instance.
It will result in an email sent to the webmail inbox. Its content is as below:
Subject: "Error - CPU usage is over 80%"
Body: "Please check your system and resolve the issue as soon as possible"

Close the stress --cpu command by clicking Ctrl-c.
Now, you will be setting a cron job that executes the script health_check.py every 60 seconds and sends health status to the respective user.
To set a user cron job use the following command:
>>> crontab -e
