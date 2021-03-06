# EC2 Bootstrapping - Hands On Lab

> In this lab you will learn how to create an RStudio Server instance in Amazon EC2. Follow the lab instructions below at your own pace. Please raise your hand or get the attention of an instructor if you have any questions. If you complete the tasks early, try one of the bonus challenges.

EC2 - Elastic Compute Cloud

  * [Create or set up an SSH keypair](#set-up-ssh-keypairs).
  * [Launch an EC2 compute instance](#launch-an-ec2-instance).
  * [Bootstrap an EC2 instance for launch](#bootstrap-your-instance).
  * [Use a web browser to access your instance](#use-a-web-browser-to-access-your-instance).
  * [Use SSH to log into your instance](#use-ssh-to-log-into-your-instance).

[EC2 Bonus Exercises](#ec2-bonus-exercises)

  * Install a Web Server
  * Stop and Start your Instance
  * Resize your Instance
  * Add Extra EBS Storage

[Related Bonus Topics](#related-bonus-topics)

- - -

# Create and Bootstrap an Instance

1. Log into the **AWS Console** using the account given to you in class.
2. From the SERVICES tab in the upper-left corner, select the **EC2** service.
3. Verify your region. EC2 instances can be placed within various regional locations around the world. For today's workshop please make sure that the OREGON region is selected for your region by checking the upper-right corner of your screen. This region is also known as US-West-2 (Oregon).

- - -

## Set up SSH Keypairs

* If this is a new AWS account, you do not yet have any SSH keys created. SSH key pairs are a secure way of signing into your EC2 instance, instead of a username and password.
* From the EC2 dashboard, select the "Key Pairs" menu item down the lefthand side of the page.
* Click on **Create Keypair**, and give it a memorable name (no spaces). Then click "Create".
* The private half of the kaypair will be immediately downloaded to your browser. Store this half in a safe location on your computer. The public half of the key pair is now in your AWS account.
* Mac/Linux users - you will use the Terminal app to SSH into your new instance.
* Windows users - you will use the Firefox browser extension FireSSH to SSH into your new instance.

- - -

## Launch an EC2 instance

4. From the EC2 dashboard, click **LAUNCH INSTANCE**.
5. Find the **Ubuntu Server 16.04** instance and select it using the blue button.
6. Select Instance type **t2.xlarge**. Take note of other instance types (sizes) available to you.
7. Click **NEXT: Configure Instance Details**.

## Bootstrap your instance

8. Leave the settings as they are configured by default, but take time to note what they are. Scroll down the page and open the "Advanced" portion of the screen.
9. In the **User Data** field, paste the code below into the text box:

```bash
#!/bin/bash

apt-get -y update
apt-get -y upgrade

echo 'ubuntu:rstudio'|chpasswd

apt-get -y install r-base
apt-get -y install gdebi-core

R --no-save << EOF
install.packages(c('dplyr','readr'), repos = 'https://cran.rstudio.com/')
EOF

cd /tmp
wget https://download2.rstudio.org/rstudio-server-1.0.136-amd64.deb
gdebi --n rstudio-server-1.0.136-amd64.deb

cd /home/ubuntu/
curl https://s3.amazonaws.com/somrc-workshop-data/checkouts.zip > checkouts.zip
unzip checkouts.zip
rm checkouts.zip
```

10. Click **Next: Add Storage**. Set this to 12GB. Note that for other projects you could add additional drives.
11. Click **Next: Add Tags**. Create a tag for your instance (optional) and give it a simple name. Click the gray button to add a tag. They "key" should be "Name" and the "value" should be whatever you'd like to name your instance.
12. Click **Next: Security Group**. Here you will create a new security group for your instance, and grant access to users.
13. Making sure that the "Create a new security group" option is selected, click the **Add Rule** button.
14. Create a new security group rule. It should be of the "Custom TCP Rule" type, the port range should be 8787, and the source should be "Anywhere".
15. Note that security groups are an important way you control visibility and access to your instance. Some ports and services, such as web servers (port 80) are normally visible to "Anywhere", whereas administrative ports such as 22 are normally restricted to specific sources.
16. Click **Review and Launch**.
17. Review your instance settings (which you can change if necessary, taking you through the setup wizard again). Once ready, click the blue **Launch** button.
18. Finally, confirm the SSH key you will be using to access this instance. You must specify a key pair (such as the one you created in the steps above), and check the acknowledgement box before you can click **Launch Instances**.
19. Your server is now being created and configured! Take a short break (5-8 minutes) while your server is built.
20. Click on **View Instances**, or select "Instances" from the lefthand navigation.

## Use a web browser to access your instance

21. Find the instance you just created and select it using the checkbox on the left of the table.
22. Find the IPv4 Public IP of your instance. Write that down or copy it to your clipboard.
23. Using a web browser, go to your instance and specify port 8787:

```
http://<YOUR-INSTANCE-IP>:8787/
```

24. You can log into your RStudio Server using the username `ubuntu` and the password `rstudio`. You will use RStudio in the afternoon session.

## Use SSH to log into your instance

25. Now let's SSH into the instance you created.

Mac / Linux users:

* Open a terminal window.
* Using the location of the private half of your SSH key and the IPv4 Public IP you copied earlier, enter this command:

```bash
ssh -i /where/is/your/key.pem ubuntu@<YOUR-PUBLIC-IP>
```

* Press RETURN. Accept the key signature of your instance. You should now be logged in.

Windows users:

* Open FireSSH in the Firefox browser. After installation this can be found under Tools -> Web Developer in your menus.
* In the "Host Name" field, paste the public IP of your EC2 instance.
* In the "Login" category enter `ubuntu`.
* In the "Private Key" box, browse to the `.pem` file you created and saved earlier.
* Click **CONNECT**.
* If you get an additional window asking for "Login" and "Password", enter `ubuntu` for the "Login" and leave "Password" blank.

- - -

26. You are now logged into your instance as the "Ubuntu" user, a regular user. 
27. You can now `sudo su` to become root, or run `sudo` commands.

- - -

## Conclusion

Congratulations! You have successfully done the following in EC2:

* Logged into the AWS Console.
* Created or imported an SSH key pair.
* Created an EC2 instance, bootstrapped to run RStudio Server.
* Customized an EC2 security group.
* Accessed your instance through a web browser.
* Accessed and modified your instance via SSH.

# EC2 Bonus Exercises

## Install a Web Server

Run this command from a terminal in your instance:

```bash
sudo apt-get -y install apache2
```

Visit your new web server's home page:

```
http://YOUR-INSTANCE-IP/
```

Do you see the Apache welcome page? Why or why not? If not, troubleshoot and solve accordingly.

## Stop and Start your Instance

* Stop your instance and start it. 
* Do this from within the web console.
* Perform the same operation from the command-line. (If you need the CLI documentation, [check here](http://docs.aws.amazon.com/cli/latest/reference/)).

## Resize your Instance

* Stop your instance.
* Resize it to another instance type, smaller or larger. 
* Do this from within the web console.
* Perform this operation again from the command-line.

## Add Extra EBS Storage

* Try adding an additional 10GB drive to your instance. You will need to do the following:
    * Create the volume
    * Attach the volume
    * Format the volume
    * Mount the volume

Instructions can be found here: 
https://discuss.rc.virginia.edu/t/amazon-ec2-add-a-secondary-ebs-volume/211

# Related Bonus Topics

* [Review how to work with S3 storage](https://github.com/uvasomrc/dsi-workshop/blob/master/scripts/s3.md)
* [Learn how to work with relational databases](https://github.com/uvasomrc/dsi-workshop/blob/master/scripts/sql.md)
* [Start and Stop an EC2 instance using code (Python)](https://github.com/uvasomrc/dsi-workshop/blob/master/scripts/run-ec2.py)
* [Interact with the AWS command-line and parse JSON](https://discuss.rc.virginia.edu/t/jq-a-simple-json-parser/91)
