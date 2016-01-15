---
layout: post
title: "Demystify Office 2016 for Mac"
modified:
categories: 
  - microsoft
  - osx
  - office2016
excerpt: An pratical guide on installing and managing Office 2016 for Mac. Includes - common questions, best practices, bugs/fixes, preferences, etc.
comments: true
published: true
image:
  feature:
  credit: 
  creditlink:
tags: [office2016, Mac, microsoft, os x, munki, casper, deployment, installation, dummies, guide]
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
Microsoft Office 2016 for Mac had a [rough](https://macops.ca/whats-wrong-with-the-office-2016-volume-license-installer/) [start](https://macops.ca/the-office-for-mac-2016-volume-license-installer-two-months-later/) [for](http://www.richard-purves.com/?p=79) [sure](https://derflounder.wordpress.com/2015/08/05/creating-an-office-2016-15-12-3-installer/). With that being said things have gotten better and quite rapidly. This post will focus on where Office 2016 for Mac currently stands, commonly asked questions, best practices, and solutions for updates.

This post should serve as a overview of the changes to Office 2016 for Mac from about November 2015 to Present (January 14th).

Before we can get too far into this post it needs to be noted that none of this would be possible without the hard work and support of the following individuals. William Smith ([@meck](https://twitter.com/meck)/talkingmoose) took it on himself to reach out to the Mac Development Team at Microsoft and helped push them into our community Slack group. Paul Bowden ([@mrexchange](https://twitter.com/mrexchange)/pbowden) and Erik Schwiebert ([@Schwieb](https://twitter.com/Schwieb)) are both Software Engineers at Microsoft inside the Mac Development group. They have been very responsive to feedback from the community.

And so you don't think I'm making up facts I will be supplying quotes from Microsoft's pbowden on almost everything. You will need to join our MacAdmins Slack group if you wish to view the source. Sign up page is [http://macadmins.org](http://macadmins.org). 

---

#Office Packages

Five months into Office 2016 being out and there are five different kinds of installers/updaters. This table should help to understand what is available and the differences. 

<table border="1">
  <tr>
    <th>Name</th>
    <th>Type</th>
    <th>Comments</th>
    <th>File Size★</th>
  </tr>
  <tr>
    <th>SKU-less★★</th>
    <th>Installer</th>
    <th>Contains all of the apps, good for O365 installations. This package can be used in combination with the VL Serializer to create a VL Installer without needing to visit Microsoft's Volume Licensing Service Center (VLSC).</th>
    <th>~1.4 GB</th>
  </tr>
  <tr>
    <th>Volume License (VL)</th>
    <th>Installer</th>
    <th>Contains all apps, has Serializer package embed. Will <b>not</b> function properly on a non-live system. AKA - must be installed on a Mac that is currently running OS X (not target disk mode, AutoDMG, etc.)</th>
    <th>~1.4 GB</th>
  </tr>
  <tr>
    <th>Standalone</th>
    <th>Installer</th>
    <th>Available from <u><a href="http://macadmins.software">http://macadmins.software</a></u>. These install packages can be combined with the VL Serializer below to have a custom install solution of just "one" or a "handful" of O2016 apps★★★. These will also work with O365 installs. OneNote standalone not available (obtain from MAS if needed).</th>
    <th>~550 MB per app</th>
  </tr>
  <tr>
    <th>Combo</th>
    <th>Update</th>
    <th>These updates can take any previous Office 2016 installed app and make them current. These are per app upgrade packages, a difference from Office 2011's combo update packages.</th>
    <th>~1 GB per app</th>
  </tr>
  <tr>
    <th>Delta</th>
    <th>Update</th>
    <th>These were first publicly available with 15.18. These significantly reduce the file size that is required to update an Office app. Also per app upgrade packages.<u><a href="#delta-updates">Additional info</a></u></th>
    <th>~350 MB for suiteStandalone</th>
  </tr>
  <tr>
    <th>VL Serializer</th>
    <th>Serializer</th>
    <th>This is a special package that was released with 15.17. The purpose of this package is to apply the Office serialization process to the SKU-less installations. <u><a href="#vl-serializer">more info</a></u></th>
    <th>~5 MB</th>
  </tr>
</table>


★All file sizes should be considered estimates and subject to change  
★★This installer is often the most confusing as it is also commonly called: **Full Suite**, **O365**, or a **Retail** installer. They are all the same.  
★★★This level of customization can also be achieved by using a [choice xml](#i-dont-want-to-install-mau-is-that-supported) with the SKU-less and VL installers.  



#How do I install O2016?
  
You should install the package that Microsoft is giving you. **PERIOD**. End of story. Stop taking their package apart and "fixing things". It is fine to poke your nose under the hood but there is absolutely no reason for you to be re-packaging anything Office 2016 related. This was a common practice with Office 2011 and at the start of Office 2016. All issues that were resolved with re-packaging have been resolved. Microsoft has listened and now it is your turn. STAHP.

###Method 1 - Office 365 installation
You can download a retail full suite installer from your VLSC dashboard or from [http://macadmins.software](http://macadmins.software). Both locations point to the same file as it a public release. This installer can be installed:

* On never booted machines (AutoDMG, Casper Imaging, etc.)
* Via the Apple's Installer.app GUI
* Via Apple Remote Desktop
* Via Munki
* Via Casper
* While at the loginwindow

It all-around should be good to go!

###Method 2 - Volume License Installation
If you are a Volume License (VL) customer your first step is to log into the VLSC and download the latest Office 2016 for Mac iso file. Inside of this file you will find the latest VL Installer package and the VL Serializer package. At this time, the VLSC has 15.17 however 15.18 has been released. Microsoft is working to sync the development and VLSC teams so that the latest update is available on the VLSC faster.

Your options are to now deploy the VL installer package or you can now download the latest SKU-less installer package ([http://macadmins.software](http://macadmins.software)) to deploy. When you use the second option you are able to deploy your SKL-less installer and then apply your VL Serializer package to create a supported install.

A third, and slightly less preferred (personal opinion) option would be to download the Standalone Installer(s) from [http://macadmins.software](http://macadmins.software). After one or more Standalone installs have taken place you can run the VL Serializer in order to properly serialize your computers. This solution looks like it might be the most flexible upfront however, the same can be accomplished by using a [choice xml](#i-dont-want-to-install-mau-is-that-supported) (detail later in this post). Plus, after two standalone installers you would be much better off with the SKU-less installer package due to file size. With that said it is supported, I however do not believe it is the correct workflow for 99% of Mac Admins.

> VLSC won’t be updated with the 15.17 build until first week of January. While the portal team is working on getting faster at making new builds available, the underlying reason for releasing the VL Serializer separately is so that you can take the Office 365/Retail (aka SKU-less) installer from future builds (15.18, 15.19, etc), deploy it, run the Serializer, and you have a fully up to date VL install. <u>This is a fully supported mechanism for deployment</u>.
>   
> @pbowden -- [source link #1](https://macadmins.slack.com/archives/microsoft-office/p1449897132003874) 


###Munki
If you are a munki shop pick one of the methods above and run the following to import Office 2016 into your repo:

{% highlight bash %}

## VL Installer or SKU-less Installer

$ munkiimport /path/to/Microsoft_Office_2016_Installer.pkg \
--name=MSOffice2016 \
--catalog=testing \
--minimum_os_version=10.10 \
--developer=Microsoft \
--category=Productivity \
--description="Unmistakably Office, designed for Mac.

The new versions of Word, Excel, PowerPoint, Outlook and OneNote provide the best of both worlds for Mac users - the familiar Office experience paired with the best of Mac.

If you already use Office on a PC or iPad, you will find yourself right at home in Office 2016 for Mac. It works the way you expect, with the familiar ribbon interface and powerful task panes. Mac users will appreciate the modernized Office experience and the integration of Mac capabilities like Full Screen view and Multi-TouchTM gestures. With full Retina display support, your Office documents look sharper and more vibrant than ever.

Office for Mac is cloud-connected, so you can quickly get to the documents you’ve recently used on other devices and pick up where you left off. New, built-in document sharing tools make it easy to invite teammates to work on a document together. When sharing documents, you won’t have to worry about losing content or formatting, as Office for Mac offers unparalleled compatibility with Office on PCs, tablets, phones and online." \
--blocking_application="Microsoft Autoupdate" \
--blocking_application="Microsoft Outlook" \
--blocking_application="Microsoft Excel" \
--blocking_application="Microsoft PowerPoint" \
--blocking_application="Microsoft Word" \
--blocking_application="Microsoft OneNote" \
--unattended_install

## VL Installer (only required if using the SKU-less Installer)
$ munkiimport /path/to/Microsoft_Office_2016_VL_Serializer-15.17.pkg \
--name=MSOffice2016_Serializer \
--catalog=testing \
--developer=Microsoft \
--category=Productivity \
--update_for=MSOffice2016 \
--unattended_install

{% endhighlight %}

You can optionally supply the `--installer_choices_xml` key to apply a [choice xml](#i-dont-want-to-install-mau-is-that-supported). Also, if you wish to apply an icon supply the `--icon_path=/path/to/MSOffice2016.png` key (the icon I used can be downloaded below).

{% img center /images/2016-01-14/MSOffice2016.png 100 100 %}

Right Click > Save Image As...

If you are planning on having a hybrid setup with MAU and munki both pushing updates to Office 2016 applications I highly recommend adding an install array to your Office 2016 installer. I also recommend this if your users have admin access and might "accidentally" delete an office application that you want to make sure doesn't disappear. This can be done two ways, during the `munkiimport` of your installer or by running `makepkginfo` and manually pasting the install array into your pkginfo:

**NOTE**: In order for the following to work the installer you are importing must be first installed on your admin machine.

{% highlight bash %}

## via munkiimport (appended to the above example)

$ munkiimport /path/to/Microsoft_Office_2016_Installer.pkg \
...
--file "/Applications/Microsoft Excel.app" \
--file "/Applications/Microsoft OneNote.app" \
--file "/Applications/Microsoft Outlook.app" \
--file "/Applications/Microsoft PowerPoint.app" \
--file "/Applications/Microsoft Word.app" \
--file "/Library/Application Support/Microsoft/MAU2.0/Microsoft AutoUpdate.app" \
...
--unattended_install

##via makepkginfo

$ makepkginfo \
--file "/Applications/Microsoft Excel.app" \
--file "/Applications/Microsoft OneNote.app" \
--file "/Applications/Microsoft Outlook.app" \
--file "/Applications/Microsoft PowerPoint.app" \
--file "/Applications/Microsoft Word.app" \
--file "/Library/Application Support/Microsoft/MAU2.0/Microsoft AutoUpdate.app"

{% endhighlight %}


###Casper
I was going to try and give resources for Casper...then I realized how completely unqualified for this section. Luckily Rich Trouton [has got you covered](https://derflounder.wordpress.com/2016/01/14/creating-an-office-2016-15-18-0-installer/){:target="_blank"}.

Using his method you could add a [choice xml](#i-dont-want-to-install-mau-is-that-supported) file in order to customize your installation. Inside of Packages under the "Additional Resources" tab your would copy in your choice xml (downloadable [copy here](https://gist.github.com/clburlison/8fd09ae20de4279281f4#file-office2016-xml)).

Then a modified postinstall script would look like:

{% gist clburlison/8fd09ae20de4279281f4 postinstall %}

Make sure and walk through the remaining steps in Rich's guide. 

**NOTE**: Using his method is repackaging the installer however he is making no-modifications to the Office installer 😊.  

---

#FAQ
Due to the shear quantity of content I am attempting to cover the remanding section of this post will be in FAQ format. 

##Why is O2016 so different from O2011?
The main reason why Office 2016 is so different is the fact that each application is now [Sandboxed](https://developer.apple.com/library/mac/documentation/Security/Conceptual/AppSandboxDesignGuide/AboutAppSandbox/AboutAppSandbox.html). Due to that fact, Microsoft is now just one step away from releasing Office 2016 apps on the Mac App Store (figuratively speaking at least). Obviously time frame for this move would depend greatly on internal policies, process with Apple, and other factors unknown to me. Sandboxing also means it is easier to remove an Application after it has been installed IE - `sudo rm -rf "/Applications/Microsoft Word.app"`. However, one downside of Sandboxed applications is the large quantity of duplicated data (mostly Fonts) being stored on the disk in five applications. 

Bonus regarding the MAS: 

> To add to the optional statement, one of the reasons why MAU is optional is because we don’t/can’t use it for apps that are delivered through the Mac AppStore. Technically, those apps (such as OneNote) are the same regardless of whether you get them from the Mac AppStore vs. Suite installer. MAU does have some additional logic to look for _MASReceipt in the app bundle so that it doesn’t attempt to update an MS app that came through the Mac AppStore.
> 
> @pbowden -- [source link](https://macadmins.slack.com/archives/microsoft-office/p1447219900000887)

##O2016 takes up so much more space.
True. This is due to Sandboxing. This is a limitation is due to Apple's guidelines on how Sandboxing is implemented. 

Nothing can be done about the increased size at this point in time. 

Now is a good time to mention [OfficeThinner](https://github.com/goodbest/OfficeThinner) is bad, will break code signing, and is **not** supported. If you did this on a personal machine you should revert the changes sooner rather than later (easiest fix is to install latest SKU-less installer).

##How do I manage updates for Office 2016?
You're limited to two solutions:

1. Use MAU
1. Manually apply the updates with management system (munki, casper, etc.)

Technically speaking if you aren't using MAU, you really need to look into [AutoPkg](#autopkg).

> If anyone wants to try out delta updates (we’ve now got them down to 350 MB for the entire suite), just change MAU to the Internal channel
> 
> @pbowden -- [source link](https://macadmins.slack.com/archives/microsoft-office/p1452622212007576)

Add Blocking Array.

##AutoPkg
> As MAU is the recommended solution for updates, it was important to make that flow as seamless as possible. When you run custom install/update solutions you need to take care of those things yourself.
> 
> @pbowden -- [source link](https://macadmins.slack.com/archives/microsoft-office/p1452617616007496)

Normally quotes go at the end however that one was important. Go ahead read it again. So AutoPkg users: the importance of that statement is you need to make sure MS applications are closed when installing updates. For munki users this means incorporating ``blocking_applications`` arrays in your pkginfos. When installing "update" packages (delta or combo) you only need to force one application to be closed per update package. However, if you wish to utilize the "SKU-less Installer" package for both fresh machine installs and updates your ``blocking_applications`` will need to make sure all five applications are closed (the example I listed in the munki section covers this).

EX 1 - Update Packages: 

This applies to the 'Delta' and 'Combo' updates and the 'Standalone Installer' packages. If you wish to deploy one of the "updates" your end-users will only need to close the application(s) that are being updated. Each update package correlates to one application on the file system. IE - Jane has Outlook open but the other O2016 applications closed. If your management solution supports unintended installs Word, PowerPoint, Excel, & OneNote could all be updated without bothering Jane. However, when it comes time to install Outlook she will need to close the application in order for the installation to properly complete.


**NOTE**: The standalone installer packages currently do not have an AutoPkg recipe. Nor do I personally feel a need for them.

EX 2 - SKU-less Installer:

If you go this route new machines will only ever need to install one package to get the latest Office 2016 (two packages if you could the VL Serializer). The down side to this solution is all five apps (Word, Excel, OneNote, PowerPoint, & Outlook plus MAU) will need to be closed prior to starting an update. Due to file size this package will take longer to download from your management system to the client computer, and will take longer to install compared to the update packages. 

If you aren't in the autopkg loop you'll want to add the main recipe repo (combo & delta update packages) and Allister repo (SKU-less & MAU installer packages).


{% highlight bash %}

autopkg repo-add https://github.com/autopkg/recipes
autopkg repo-add https://github.com/autopkg/arubdesu-recipes

{% endhighlight %}

Using AutoPkg is outside of the scope of this guide.

##http://macadmins.software
This is a site that is _not_ affiliated with Microsoft but ran by a friendly face (*cough* pbowden *cough*). This website is simple and has one purpose: get current software links/version easier. With that said all of the links are actually redirects to Microsoft's Official CDN so software downloaded from this site is safe. The packages can be verified as they are all signed by Microsoft's developer certs.

##When saving I get a blank directory!
{% img center /images/2016-01-14/saving_online_default.png 600 600 %}

By default the Office 2016 suite of applications have 'Online Locations' selected in the save window. This might be considered confusing to end-users. In addition some environments are not allowed to save files to cloud based storage locations. This is now manageable with a preference, example below:

**NOTE**: This is a user-level setting. This can be ran with [outset](https://github.com/chilcote/outset) or some other scripting solution.

{% highlight bash %}

$ defaults write $HOME/Library/Group\ Containers/UBF8T346G9.Office/com.microsoft.officeprefs.plist DefaultsToLocalOpenSave -bool TRUE

{% endhighlight %}

This is not a perfect solution however if you really need to change the default save location we have a solution!

> And one more from me for **15.18**, you can now set the following plist preference to force the open/save panel to ‘On my Mac’ instead of 'Online Locations' -- [preference above]. I know it’s a little long, but it’s a single place that will affect the behavior of Word, Excel and PowerPoint 2016.
> 
> @pbowden -- [source link](https://macadmins.slack.com/archives/microsoft-office/p1450417854005113)

##Can't I just copy com.microsoft.office.licensingV2.plist?
No. This was a bug. Microsoft is aware of the issue and this will be fixed in 15.20 which is scheduled to be released in March. 

The first editions of the Volume License installer from Microsoft caused issues with licensing. The Mac Admin Community collectively came to the solution of packaging `/Library/Preferences/com.microsoft.office.licensingV2.plist` separately and applying this file to new installations. This was wrong. Do not do it.

If you did copy the `com.microsoft.office.licensingV2.plist` file see [VL Serializer](#vl-serializer)


##Dialog Prompt(s) - IE What's New
Eric Holtam has a great post regarding these dialog prompts and managing them with profiles [located here](https://osxbytes.wordpress.com/2015/09/17/not-much-whats-new-with-you/){:target="_blank"}. The only part that I didn't understand, initially, was Office is actually providing your with two separate "What's New" dialog boxes. The first (shown below with Excel) gives you a splash screen and then prompts you to select your colour theme: Classic or Colorful. Along with prompting O365 users to sign-in for activation.

{% img center /images/2016-01-14/WhatsNew1.png 420 200 %}

The second "What's New" dialog prompt will only be shown for O365 users. With 15.15 and up VL customers will not see these prompts. It does not matter how you installed O2016, even the SKU-less installer, if you are a VL customer these dialog prompts will not be shown. 

{% img center /images/2016-01-14/WhatsNew2.png 420 200 %}

Both of these dialog boxes can be disabled, however if you are a O365 customer disabling the first dialog prompt is not recommended. (see quote from Eric below)

> If you're an O365 customer and you suppress the 1507 key your users won't be prompted to authorize the license on first launch.  You'll have to do that from in-app and the dialog that appears makes it sound like it needs to be purchased.  There's a small link to click to specify you already have a license.
> 
> @eholtam -- [source link](https://macadmins.slack.com/archives/microsoft-office/p1452712063008493)

##Delta Updates
With the release of 15.18 Microsoft has released Delta updates. Paul has released a public document that goes over these updates in quite a bit of detail (see below). The biggest note for these delta updates is that they will only work on the current release minus one major revision (N-1). 

IE - I have Office 15.17 installed when 15.18 was released MAU offered to install the latest 15.18 updates as delta packages. However, if I was on 15.16 I would need the 15.18 combo update packages (quite a bit larger in file size) in order to update to the latest release.

The nice part about these Delta updates is if you are using MAU these are automatically offered to your users without any intervention on your part. 

[{% img center /images/pdf.png 100 100 %}](/images/2016-01-14/Delta_Updates.pdf){:target="_blank"}


##VL Serializer
Again, Paul has released a document that will cover more information than I can. The only thing I want to highlight is if you are coming from a known bad install of office (IE - you copied com.microsoft.office.licensingV2.plist) you need to run a `rm -f /Library/Preferences/com.microsoft.office.licensingV2.plist` prior to running the VL Serializer package.

[{% img center /images/pdf.png 100 100 %}](/images/2016-01-14/VL_Serializer.pdf){:target="_blank"}


##Office Insider

> The	Office	Insider	program	is	a	new	initiative	to	provide	Office	customers	on	all	platforms	to	get	early	access	to	the	very	latest	builds	of	the	
product.	The	public	description	can	be	found	at	https://products.office.com/office-insider

[{% img center /images/pdf.png 100 100 %}](/images/2016-01-14/Office_Insider.pdf){:target="_blank"}


#MAU
Sorry, MAU is a very complex topic. I do not plan on utilizing it in my environment so below are important notes from Paul.

> Warning …long paragraph coming up  😉
>
Here’s a new feature that I implemented in MAU 3.3 which got released alongside 15.17 yesterday. I was talking to an enterprise customer a couple of months ago, and they had a semi-managed environment where they ‘slow’ pushed updates to Office 2016, but they also wanted to allow users to use MAU and get updates. Problem is that they wanted to control the ‘maximum’ version that MAU would see. For example, even though 15.17 got recently released, they didn’t want users to update to it until IT had a chance to validate it and make sure it didn’t break anything. However, they were happy for users to upgrade to 15.16 even though they hadn’t pushed it out to everyone.
>
> So, while I was implementing the new ‘channels’ feature in MAU, I allow a ‘Custom’ channel to be set. You can then point MAU at your own HTTPS server that has a copy of the manifests you want to use, instead of the latest ones at https://www.microsoft.com/mac/autoupdate
>
> The manifests are named as follows, and these are the files you’ll want to copy to your own web server:
Word 2016 for Mac - `0409MSWD15.xml` & `0409MSWD15.cat`
Excel 2016 for Mac - `0409XCEL15.xml` & `0409XCEL15.cat`
PowerPoint 2016 for Mac - `0409PPT315.xml` & `0409PPT315.cat`
Outlook 2016 for Mac - `0409OPIM15.xml` & `0409OPIM15.cat`
OneNote 2016 for Mac - `0409ONMC15.xml` & `0409ONMC15.cat`
>
> As MAU is version agnostic, you can also use this same mechanism to control Office 2011 and Lync updates.
Now, MAU verifies the hash across the CAT and XML, so you can’t tamper with the XML, or try to use your own server for PKGs, but you can at least prevent MAU from ‘seeing’ versions that you don’t support.
>
> The MAU configuration for this is as follows:
`defaults write com.microsoft.autoupdate2 ChannelName -string 'Custom'`
`defaults write com.microsoft.autoupdate2 ManifestServer -string 'https://yourwebserver/yourpath/'`
>
> NOTE: That MAU 3.3 will only work with HTTPS servers. In MAU 3.4 which is coming next month, I’ve relaxed this so you can also use HTTP.
Let me know if you have any questions or comments! When we get closer to end of this month, I’ll talk more about how channels work in MAU 3.4 and how to take advantage of them(edited)
>
> Also note that it’s important to remember the trailing `/` at the end of the ManifestServer path value
>
> @pbowden -- [source link](https://macadmins.slack.com/archives/microsoft-office/p1449904185003906) 

##MAU 3.4 update 
{% img center /images/2016-01-14/insider_builds.png 300 300 %}

> Lots of good progress today towards our 15.18 January release that I wanted to share. First up, MAU 3.4 which is scheduled to release on 1/4. The big change here is support for the new Office Insiders program, and the official release of multiple update channels. 
>
> There’s a new checkbox in the UI which you can see in the screenshot below. If you check this box, it sets the underlying ChannelName key to ‘External’ which means you’ll get new builds roughly 7 days before the rest of the world. Not exposed through the UI, but if you’re feeling super keen, you can manually set the ChannelName key to ‘Internal’ (`defaults write com.microsoft.autoupdate2 ChannelName -string 'Internal'`) and get new builds roughly 14 days before the rest of the world. It goes without saying that release quality improves as we head toward worldwide release. However, this mechanism of getting early builds means that all of you can enjoy early access and new feature testing without needing special FTP creds - all the PKGs for all channels will be published on our public Office CDN. For those of you who don’t want users opting in to early builds, I’ve added a pref to disable the checkbox (`defaults write com.microsoft.autoupdate2 DisableInsiderCheckbox -bool TRUE`).
>
>
> Mainly for @allister, the channel names map to the following URLs:
>
> * Internal == https://www.microsoft.com/mac/Internal
>
> * External == https://www.microsoft.com/mac/External
> 
> @pbowden -- [source link](https://macadmins.slack.com/files/pbowden/F0GUKFW4C/pasted_image_at_2015_12_17_09_36_pm.png)

##Can I disable MAU from auto-checking for updates? 
Sure. This process is the same as it was with Office 2011. My recommended method for making this change is with a profile. Example from Greg Neagle: [disable_autoupdates_microsoftautoupdater.mobileconfig](https://github.com/gregneagle/profiles/blob/master/autoupdate_disablers/disable_autoupdates_microsoftautoupdater.mobileconfig)

The change can also be applied per user with defaults:

{% highlight bash %}

## These should not be ran as root. They are user-level preferences.

$ defaults write com.microsoft.autoupdate2 HowToCheck -string 'Manual'
$ defaults write com.microsoft.autoupdate2 LastUpdate -date '2001-01-01T00:00:00Z'

{% endhighlight %}

##I don't want to install MAU, is that supported?
Yes, MAU is completely optional. In fact each of the five core MS applications are optional. Just because the installer has all five available does not mean you must install them. 

If you choose to _not_ install MAU, all updates to the Office suite will be completely on you as the administrator. In my environment MAU is removed when I uninstall Office 2011. During the rollout of Office 2016 I use a choice xml file to disable the installation from the SKU-less installer. 

In the example below:

Notice the `attributeSetting` setting for `com.microsoft.autoupdate` is set to a 0. That is telling the installer to not install MAU. You can modify the example choice xml to customize which applications you do/don't want installed.

* 1 - install
* 0 - don't install

{% gist clburlison/8fd09ae20de4279281f4 office2016.xml %}

Lastly, once you make these changes you can install from the command line like so: 

{% highlight bash %}

$ /usr/sbin/installer -applyChoiceChangesXML /path/to/office2016.xml \
-pkg /path/to/Microsoft_Office_2016_Installer.pkg \
-target /

{% endhighlight %}


> Okay, makes sense - thanks for the clarification. MAU is designed to be independent from the apps, so it is considered ​*optional*​ ….you’re fully supported if you want to omit it from your installs. However, the same is not true for the licensing and fonts/frameworks/proofing tools packages - those are required
>
> @pbowden -- [source link](https://macadmins.slack.com/archives/microsoft-office/p1447219794000886)


<div class="note info">
  <h5>Following section:</h5>
  <p>I debated adding the following section or not. The TL;DR is make sure you are using release 15.17.0 or greater. If you want more details then carry on.</p>
</div>


#Bugs
Oh the bugs that have been squashed. This is not an all encompassing list however "big issues" should be listed below.

#15.17 Release Log for Admins

Included in 15.17 mid-December Release (i.e. work already complete and verified)*​

1.    Support for ARD installs, and other solutions like Casper, Munki (as verified by your good selves)
2.    New package in VL ISO called �Microsoft_Office_2016_VL_Serializer.pkg� which you can use to generate a bona fide licensingv2.plist file on top of a SKU-less install
3.    Installer GUI now supports selecting which volume to install on
4.    Installer GUI now supports selecting which app packages to install (including MAU)
5.    MAU Daemon pop-up now suppressed for unattended installs
6.    Word is no longer auto-launched at the end of the GUI install
7.    OneNote Sign-in button now works
8.    New plist preference to control whether Error Reporting shows the �send additional files� dialog. Will never send additional files or prompt the user if set


> @pbowden -- [source link](https://macadmins.slack.com/archives/microsoft-office/p1448495549001887)

##Keychain Prompt
{% img center /images/2016-01-14/bad_mau_daemon.png 420 200 %}

FIXED - 15.17

Issue - MAU (Microsoft Auto Update app) has a background process that would run to see if updates are available. This  

> Next up, those pesky keychain access errors on first app launch are now a thing of the past. Most of you on VL probably don’t see these, but O365 users see them a lot. I’ve changed the code so that the Office 2016 apps are automatically provisioned with access on keychain creation.
> 
> @pbowden -- [source link](https://macadmins.slack.com/archives/microsoft-office/p1450417340005109)

More on the actually fix to this issue. Inside of `$HOME/Library/Preferences/com.microsoft.autoupdate2.plist` the following key value is created during a GUI installation: 

**NOTE:** In case it isn't clear _you_ as the admin should **NOT** be creating or modifying this setting. That is the responsibility of Microsoft and the installer. 

{% highlight xml %}

<key>StartDaemonOnAppLaunch</key>
<true/>

{% endhighlight %}

> Yep, that’s a new key in MAU 3.3 and 15.17 - it’s how I implemented the fix for the MAU daemon issue. Basically, if this pref exists and set to 1, then the MAU daemon attempts to launch on app boot. The pref is written by the MAU postinstall script for a GUI install, so it won’t exist for unattended installs
> 
> @pbowden -- [source link](https://macadmins.slack.com/archives/microsoft-office/p1449899823003896)


##Software not actually becoming licensed
FIXED - 15.14

Issue - If the VLSC instal package was installed while a user is was not logged in a GUI session, Office 2016 would fail to serialize the suite of applicaitons.

> @chrfr no, you won’t break if you manually extracted the VL serializer from 15.14 and run it with a 15.17 install. It’s just that there’s a bug with that VL serializer where it won’t run properly if the user is not logged on. That’s fixed in the 15.17 VL serializer.
> 
> @pbowden -- [source link #5](https://macadmins.slack.com/archives/microsoft-office/p1449897132003874)

##Outlook/Word Resizing Bug
FIXED - 15.17.1

Issue - A bug that caused issues when resizing Outlook and some bug fixes for Word. 

> Just a heads-up that Outlook and Word 15.17.1 will release this afternoon. Outlook has a fix for the resizing issue (thanks to everyone that tested the build from yesterday). Word has some targeted fixes for some crashing issues we’ve seen with custom keyboards and invalid document URLs)
>
> @pbowden -- [source link](https://macadmins.slack.com/archives/microsoft-office/p1450377975004954)


#Wrap up
Hopefully that covers all the bits and pieces a Mac Administrator would want to know.


---

Links:  
[What's Wrong with Office 2016 VL Installer](https://macops.ca/whats-wrong-with-the-office-2016-volume-license-installer/),  
[VL Installer two months later](https://macops.ca/the-office-for-mac-2016-volume-license-installer-two-months-later/),  
[Office 2016 Packaging:](http://www.richard-purves.com/?p=79),  
[Office 2016 Volume Installer findings](https://jamfnation.jamfsoftware.com/discussion.html?id=16761),  
[JAMF Nation - Suppress this Dialog](https://jamfnation.jamfsoftware.com/discussion.html?id=17013),  
[Not much what's new with you](https://osxbytes.wordpress.com/2015/09/17/not-much-whats-new-with-you/),  
[Office 2016 Mac Admin Resource Links](https://osxbytes.wordpress.com/2015/09/23/office-2016-mac-admin-resource-links/),  
[What's New and Improved in Office 2016 for Mac for Office 365](https://support.office.com/en-us/article/What-s-New-and-Improved-in-Office-2016-for-Mac-for-Office-365-44d5a464-3051-41b0-b44e-c6cee569f545),  
[Creating an Office 2016 Installer](https://derflounder.wordpress.com/2016/01/14/creating-an-office-2016-15-18-0-installer/)