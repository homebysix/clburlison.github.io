---
layout: post
title: "Setup a Munki repo on Ubuntu 14.04 - Part 1"
date: 2014-10-06T22:55:49-05:00
modified: 2014-10-17
categories: munki ubuntu
excerpt: Gets the repo configured and shared via nginx. Plus, samba gets configured for remote administration.
comments: true
published: true
tags: []
image:
  feature:
  credit: 
  creditlink:
---

<section id="table-of-contents" class="toc">
  <header>
    <h3>Overview</h3>
  </header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->






#Intro

As you might have guessed from my previous [post](/blog/2014/10/02/reposado-guide/), I am trying to standardize at work. Part of this was to move many core OS X services away from OS X Server and towards Ubuntu. This will allow us to use our existing virtualization infrastructure. After reposado the next big service was our munki repo. 

{% img center /images/posts/2014-10-06/munki.jpg 400 400 %}

[Munki](http://github.com/munki/munki) is a very powerful open source tool for patch management and software updates for OS X clients. The client component is pretty easy to install but the server component can be a bit more tricky for newer administrators. The goal of this guide is to walk through setting up the server web share with http basic authentication (read simply security), and lastly setup samba so we can remote into our web server to manage files. 

In the past, our munki_repo has been shared using apache but due to some research and a few internal tests I will be using nginx as the backend in this guide. 

Since our Munki setup has many add-on projects including: [mandrill](https://github.com/wollardj/Mandrill),  [munkireport-php](https://github.com/munkireport/munkireport-php/), and our in-house rsync replication I will be splitting this series into multiple parts. 

{% img /images/posts/2014-10-06/managed_software_center.png 600 600 %}

#The Install

As a matter of good practice, we are going to make sure our Ubuntu server is fully patched before we start. Then we will install _git, curl, build-essential, nginx, and apache2-utils, samba_. 


##Installing Required Software

{% highlight bash %}

sudo apt-get update
sudo apt-get upgrade
sudo apt-get -y install git curl build-essential nginx apache2-utils samba

{% endhighlight %}


###Setup the directories:
{% highlight bash %}

sudo mkdir /usr/local/munki_repo
sudo mkdir -p /etc/nginx/sites-enabled/
ln -s /usr/local/munki_repo/ ~/
cd /usr/local/munki_repo
sudo mkdir catalogs client_resources icons manifests pkgs pkgsinfo

{% endhighlight %}


###Creating the service accounts & set directory permissions:
{% highlight bash %}

sudo addgroup --system munki
sudo adduser --system munki --ingroup munki
sudo usermod -a -G munki $USER # Adds the current console user to munki group
sudo usermod -a -G munki www-data # Adds web user to munki group
sudo chown -R $USER:munki /usr/local/munki_repo
sudo chmod -R 2774 /usr/local/munki_repo

{% endhighlight %}

##Setting up Nginx
Nginx is fast, light-weight, and uses a fraction of the resources that Apache uses. But don't take my word for it there are lots of [other reason](http://arstechnica.com/business/2011/11/a-faster-web-server-ripping-out-apache-for-nginx/) why [you might want to use Nginx](http://wiki.nginx.org/WhyUseIt).

Nginx's installation on Ubuntu is very similar to Apache's. All of its config files are stored in _/etc/nginx_.

Lets create a site conf for our munki_repo.   
``sudo nano /etc/nginx/sites-enabled/munki_repo.conf``

{% highlight html %}
# Munki Repo
server {
  listen 80;
  server_name munki;
  location /munki_repo/ {
    alias /usr/local/munki_repo/;
    autoindex off;
    auth_basic "Restricted";
    auth_basic_user_file /etc/nginx/.htpasswd;
  }
}

{% endhighlight %}

Now we must start the nginx service.  
``sudo /etc/init.d/nginx start``

###Securing your munki_repo
For my purpose, I will be securing my munki_repo with simple http basic authentication. Depending on the needs of your organization this might be enough but you might need to look into ssl and other advanced options. If you are interesting in these options check out the [munki wiki](https://github.com/munki/munki/wiki).

**Create an http user and password**
``sudo htpasswd -c /etc/nginx/.htpasswd munkihttpuser``

The tool will prompt you to enter a password (make it strong).
{% highlight bash %}

New password: ******
Re-type new password: ******
Adding password for user munkihttpuser

{% endhighlight %}

The structure of the htpasswd is ``login:password_hash``.

We must reload the nginx service to update the reflected change.  
``sudo /etc/init.d/nginx reload``

Now when you try to access your website, [http://yourmunkiserver/munki_repo/](), you will notice a browser prompt that asks you to enter the login and password. Enter the details that you used while creating the .htpasswd file. The prompt does not allow you to access the website till you enter the right credentials. The munki client supports this security feature with the AdditionalHttpHeaders key [more info](https://github.com/munki/munki/wiki/Using-Basic-Authentication#configuring-the-clients-to-use-a-password).

_Note:_ If you do not want to secure your munki repo you can remove this setting in the above ngix config file by removing the two lines that start with _auth_basic_.

##Setting up Samba
Now we just need a way to mount our munki_repo on a mac so we can do administrative things. Samba uses a separate set of passwords than the standard Linux system accounts (stored in /etc/samba/smbpasswd), so you'll need to create a Samba password for yourself.  

{% highlight bash %}
sudo smbpasswd -a munki
#output on the following lines
New SMB password: *****
Retype new SMB password: ****
Added user munki.
{% endhighlight %}

Now we need to share the munki_repo. Once "smb.conf" has loaded, add this to the very end of the file:  
``sudo nano /etc/samba/smb.conf``

{% highlight bash %}

[munki_repo]
path = /usr/local/munki_repo 
available = yes
valid users = munki      
read only = no
browseable = yes
public = no 
writable = yes
{% endhighlight %}

Test for errors with the config file with: ``testparm``

Now we must restart samba.  
``sudo restart smbd``

From your mac you will be able to access the munki_repo with the following [smb://munki.example.com/munki_repo]().

#Conclusion
We now have a working munki_repo fully configured and ready for use to start importing packages into the repo. If you are really new to Munki, this takes care of the "Demonstration Setup" section from the [munki wiki](https://github.com/munki/munki/wiki). To start populating Munki with manifests, packages, and more I would recommend using [MunkiAdmin](https://github.com/hjuutilainen/munkiadmin).

---

Articles:  
[How to configure Nginx](https://www.digitalocean.com/community/tutorials/how-to-configure-the-nginx-web-server-on-a-virtual-private-server),  
[Configuration - Official nginx documentation](http://wiki.nginx.org/Configuration),  
[Samba Setup](https://help.ubuntu.com/community/How%20to%20Create%20a%20Network%20Share%20Via%20Samba%20Via%20CLI%20(Command-line%20interface/Linux%20Terminal)%20-%20Uncomplicated,%20Simple%20and%20Brief%20Way!),  
[Basic Http Auth with Nginx](https://www.digitalocean.com/community/tutorials/how-to-set-up-http-authentication-with-nginx-on-ubuntu-12-10),  

Update:  
Oct. 16, 2014 - Removed note about the htpasswd that was incorrect.  
Oct. 17, 2014 - Move samba error test command before you restart the samba service. Add reference URL to your munkiserver for authentication testing purposes.  