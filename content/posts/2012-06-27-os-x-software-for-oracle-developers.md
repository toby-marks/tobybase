+++
draft       = false
date        = "2012-06-27T21:39:00+00:00"
lastmod     = "2017-02-22"
title       = "OS X Software for Oracle Developers"
categories  = ["Tools", "Database"]
tags        = ["macos", "oracle", "utilities"]
+++
One of the great things about working for [Enkitec](http://www.enkitec.com/) is that it's a very Mac-friendly shop. When I arrived there in 2007 there were a handful of us running Macs and the attitude at the top seemed to be respectful, but skeptical. They allowed me to use my own laptop, which at the time was a 15-inch Intel Core2 Duo Macbook Pro, with the understanding that I could request a standard-issue Dell laptop at any time should the need arise. It never did. Since that time a large number of my coworkers have made the switch, abandoning their bulky, slow Dell Latitude laptops for 13-inch Macbook Pros, as have both owners of the company.

I've been a Mac user since 1998, since the advent of the original [bondi blue iMac](http://en.wikipedia.org/wiki/IMac_G3), a machine that I greatly loved, as far as you can love a machine (and no farther!). I've used a Mac at work since about the year 2000, a good while before [OS X](http://en.wikipedia.org/wiki/OS_X) really hit the mainstream. Back in those days about the only thing I could hope to accomplish on it at the office was email, iTunes, looking up something on the internet, and running an `SSH` session or two. Still, I faithfully carried my little [white plastic iBook G3](http://www.everymac.com/systems/apple/ibook/specs/ibook_900_14.html) to work every day just because I enjoyed using the damn thing so much. I looked for any opportunity at all to integrate it into whatever I was doing at work. Over the years that came to include more and more stuff. I still remember how excited I was the day Oracle Applications earned [Gold Certification on OS 9](http://support.apple.com/kb/DL1184?viewlocale=en_US&locale=en_US)! Woohoo! I desperately needed a life.

The advent and acceptance of OS X changed everything. The Unix underpinnings gave legitimacy to the Mac as a serious "office" platform. I went from using my laptop as a toy to using it as a companion system, then a few years later as my primary system, then finally as my only system. Likewise, the number of Windows applications I needed to run on a daily basis, even in VMWare, was reduced from several to none. Hallelujah. In the same timeframe I also went from being the "office mac weirdo" to one of several "elite mac-using jackasses", to just another one of the guys. Or so they tell me to my face. 

One of the questions I'm asked most often by coworkers [switching to the Mac](http://www.youtube.com/watch?v=l2-UuIEOcss) is what software they should download first. This is my list.

_\[Note: I provide links to the developer's sites, but many of these apps are available in the App Store.\]_

## Crucial Stuff

These are the tools any Mac user, but particularly those working in IT, should have in their arsenal.

<span style="text-decoration:line-through">* [Butler](http://manytricks.com/butler/)</span>  
<span style="text-decoration:line-through"> The one and only truly critical app in this list, Butler is an application launcher, search tool, universal bookmark manager, iTunes remote controller, Applescript launcher, keyboard macro tool, pasteboard manager and more. It puts the contents of your Mac at your fingertips and I couldn't imagine using a computer without it. Indispensable.</span>

_Update:_ Since I wrote this article I've made the switch to [Alfred](http://www.alfredapp.com) for accomplishing essentially the same tasks. I was a longtime Butler user and supporter, even while more popular apps like [Quicksilver](http://qsapp.com) came and went(?), but it has become evident that Butler is no longer being actively developed, while Alfred continues to add significant new functionality, like the enhanced scripting capabilities in version 2.0. There is currently a sizable community of people [developing sophisticated workflows](http://www.alfredforum.com/forum/3-share-your-workflows/) for Alfred, but the most compelling feature is the integration with 1Password (see below) that allows you to open and log in to your password protected web sites using a keyboard shortcut. My only complaint is that I preferred the iTunes automation in Butler (also found in Alfred, but works differently), but I imagine Butler's conceits could be mimicked by using a custom workflow in Alfred.

[1Password](https://agilebits.com/onepassword/mac)  
Universal password manager, syncs with iPad and iPhone version. Use it to store the hundreds of passwords you accumulate both personally and on the job. A plugin for Safari, Chrome, and Firefox handles automatic entry of password credentials on websites you visit.
    
[MS Office](http://www.microsoft.com/mac)  
Or at least just Word and Excel. *Never* use Powerpoint. See below. It's true that there are plenty of alternatives these days like OpenOffice, LibreOffice, Google Docs, etc., but unless everybody in your workplace has standardized on those tools, you're bound to run across conversion issues that can sometimes be a serious nuisance.
    
[iWork](http://www.apple.com/iwork/)  
Yes, I recommend both office suites, as Pages is vastly superior to Word as a composition tool and Keynote simply demolishes Powerpoint for presentations. Faster, lighter, easier to use — the whole iWork suite is worth it just for Keynote. Companion apps for iPad and iPhone are cheap and automatically sync your documents. Personally I use always use Keynote for creating presentations and working with Powerpoint slideshows, I use Pages whenever I can, and I use Numbers when I want to create a visually sharp chart/spreadsheet combo for presentations that I won't be handing off to other people to modify.
    
<span style="text-decoration:line-through">[Growl](http://growl.info/)</span>
<span style="text-decoration:line-through">Perhaps soon to be obsoleted by the new [Notification Center](http://www.apple.com/osx/whats-new/features.html#notification) feature of [OS X Mountain Lion](http://www.apple.com/osx/), Growl provides a heads-up display for various things happening on the Mac. I use it mostly for email notifications, but the feedback it provides when attaching an external device or connecting to a network is often helpful.</span>

_Update:_ Dead in the water. Absorbed into notifications in Mountain Lion.

[BBEdit](http://www.barebones.com/products/bbedit/)/[TextWrangler](http://www.barebones.com/products/textwrangler/)  
A good text editor goes a very long way, as any coder can tell you. Some people [organize their whole lives](http://www.43folders.com/2005/12/12/text-setup) with text files, though. Emacs is also available, but it doesn't have the Mac sensibilities of this storied old tool. If you'd rather not spend the money, TextWrangler is the free, stripped-down counterpart.
    
[Evernote](http://evernote.com/evernote/)  
Solves the problem of how to catalog, archive, and make available the vast library of information you accumulate over the years. Companion apps for iPad and iPhone put all your information at your fingertips. Can also be used as a collaborative tool for teams.

## Software for the Oracle Developer

[SQL Developer](http://www.oracle.com/technetwork/developer-tools/sql-developer/overview/index.html)  
Oracle's free tool for SQL and PL/SQL development.

[NetBeans](http://netbeans.org/) and/or [JDeveloper](http://www.oracle.com/technetwork/developer-tools/jdev/overview/index.html)  
For J2EE development. Netbeans is the better tool, IMO, unless you need to work with [Oracle ADF](http://www.oracle.com/technetwork/developer-tools/adf/overview/index.html).

[VirtualBox](https://www.virtualbox.org/)  
I've used VMWare Fusion for years and recently tried VirtualBox to take advantage of [Oracle's developer VMs](http://www.oracle.com/technetwork/community/developer-vm/index.html), and have been impressed with the performance and configurability. Beat's VMWare's price, as well.

[Oracle 10g Instant Client](http://www.oracle.com/technetwork/topics/intel-macsoft-096467.html)  
Can't beat the convenience of being able to connect to a database without having to boot a VM.

[CoRD](http://cord.sourceforge.net/)  
For remote desktop (RDP) connections. Traditionally, it's been a better, more reliable option than [Microsoft's Remote Desktop app](http://www.microsoft.com/mac/remote-desktop-client)

[Citrix Receiver](http://www.citrix.com/English/ss/downloads/details.asp?downloadId=2323407&productId=1689163)  
Needed If your company publishes corporate apps with Citrix Webapps, and many do.

[VisualVM](http://visualvm.java.net/)  
Performance analysis/debugging tool for Java apps.

[Apache Directory Studio](http://directory.apache.org/studio/)  
A useful and fully-featured LDAP tool.

[Wireshark](http://www.wireshark.org/)  
Packet sniffer.

[SmartCVS](http://www.syntevo.com/smartcvs/index.html)/[SmartSVN](http://www.syntevo.com/smartsvn/index.html)  
 My tools of choice for easy source code manaagement.

[MacPorts](http://www.macports.org/)  
Download and compile Unix software.

[Kindle](http://www.amazon.com/gp/feature.html/ref=kcp_mac_mkt_lnd?docId=1000464931) /[Send to Kindle](http://www.amazon.com/gp/feature.html/?docId=1000778781)  
Take advantage of Kindle's massive collection of technical ebooks. The 'Send to Kindle' tool allows you to upload your own PDFs for reading in the Kindle app.

[Bwana](http://www.bruji.com/bwana/)  
Read `man` pages in a browser window.

## Productivity Apps

[OmniFocus](http://www.omnigroup.com/products/omnifocus/)  
[GTD](http://www.davidco.com/about-gtd) task/life management tool. Companion apps for iPhone, iPad keep everything in sync and support location-based notes. 

[Acorn](http://flyingmeat.com/acorn/)  
Also might have chosen [Pixelmator](http://www.pixelmator.com/), but this is the one I own. Almost everybody could use a good image editor at some time or other. Pixelmator is supposed to be closer to Photoshop.

[Skitch](http://skitch.com/)  
Screenshot markup tool. Integrates with [Evernote](http://www.evernote.com/evernote/).

## For Sharing and Collaboration

[Dropbox](https://www.dropbox.com/downloading?os=mac)  
Disk space in the cloud with sharing and privacy features. Integrates with many applications on Mac, iPhone, iPad.

[SpiderOak](https://spideroak.com/)  
More secure alternative to Dropbox for sensitive files.

[Wuala](http://www.wuala.com/)  
Another secure cloud-based storage option.

[DropKey](http://www.wellredapps.com/products/dropkey/)  
A brain dead easy way to send secure, encrypted email attachments!

[Skype](http://www.skype.com/intl/en-us/get-skype/on-your-computer/macosx/)  
Video phone for talking to your PC-using buddies. For your Mac brothers you should use [Facetime](http://www.apple.com/mac/facetime/).

[Google Hangouts](http://www.google.com/+/learnmore/hangouts/)  
I have some anecdotal evidence that this may be a more stable video conferencing solution than Skype. I'll update as I gain more experience using the two.

[TeamViewer](http://www.teamviewer.com/en/download/index.aspx)  
Free screen sharing software, if you don't have access to [GoToMeeting](http://www.gotomeeting.com/).

## System Tools

[MenuMeters](http://www.ragingmenace.com/software/menumeters/)  
Lightweight system monitor that sits in the menu bar. Measures CPU, memory, network, and disk usage.

[SuperDuper!](http://www.shirt-pocket.com/SuperDuper/SuperDuperDescription.html)  
Creates bootable, up-to-date copies of your entire hard drive! Great, possibly indispensable, for recovering from a hard disk failure. Can also be used just to sync disk contents. Only copies changed data during file sync. Very efficient.

[iDefrag](http://www.coriolis-systems.com/iDefrag.php/)  
Defrag utility. You shouldn't need to do it ever in OS X, but I'm hoping it prolongs the life of my hard drive. Anybody got any real statistics on that? By the way don't try this on an SSD drive! It'll decrease the life of the drive.

[iPartition](http://www.coriolis-systems.com/iPartition.php)  
Disk Utility does pretty much everything this app does, except for allowing you to partition a disk without data loss. Unfortunately, this will not work if the disk is too full or badly fragmented. That's when iDefrag really comes in handy.

[CLIX](http://rixstep.com/4/0/clix/)  
GUI tool for executing Unix commands. Comes with a large library of useful commands, including many Mac-specific ones. Can be used as a learning tool and you can add your own scripts.

[OmniDiskSweeper](http://www.omnigroup.com/products/omnidisksweeper/) and/or [GrandPerspective](http://grandperspectiv.sourceforge.net/)  
Analyze the contents and sizes of the stuff on your hard drive. GrandPerspective provides a better visualization, but you don't always need the "fluff".

## Internet Tools

[Interarchy](https://nolobe.com/interarchy/)  
FTP/SFTP tool with numerous useful features. Download an entire website or keep remote sites in sync with local folders. [Transmit](http://panic.com/transmit/) is also popular.

[Chrome](http://www.google.com/mac/)  
The web browser. (I use Webkit.)

[Firefox](http://www.mozilla.org/en-US/firefox/new/)  
The web browser. (See above.)

[Webkit](http://nightly.webkit.org/)  
See what's coming down the pipe in Safari.

[Transmission](http://www.transmissionbt.com/)  
BitTorrent client.

[VLC](http://www.videolan.org/vlc/download-macosx.html)  
Open source media player. Plays a wide variety of formats.

## If You Have The Money...

[PDFPenPro](http://www.smilesoftware.com/PDFpenPro/)  
Create, edit, sign, mark up, footnote, redact, and otherwise manipulate PDFs. Also does OCR and tables of contents. Very nice tool. Companion app for iPad.

[OmniGraffle](http://www.omnigroup.com/products/omnigraffle/)  
Like Visio, only a thousand times better. The Omni Group's flagship product. Wish very much I owned this one.

[OmniPlan](http://www.omnigroup.com/products/omniplan/)  
Does to MS Project what Keynote does to Powerpoint. Bought my copy a few versions back when it was far less expensive, and it still has all the project tracking features I need.

## Obscure Use Cases

[MailServe for Lion](http://cutedgesystems.com/software/MailServeForLion/)  
Automates configuration of OS X's built in Postfix mail server. I've had to use this in the past when I was unable to connect to the corporate email server and didn't want to send files through Gmail. (Did I mention I despise most web mail clients?)

[TNEF's Enough](http://www.joshjacob.com/mac-development/tnef.php)  
Used it once to extract the Word document embedded in a `winmail.dat` attachment from MS Outlook.

[PST Bridge](http://www.arrowbit.com/info/pstbridgepro)  
Used convert or read PST files (basically mail archives) generated by MS Outlook.

## Just for Fun

[Nocturne](http://www.blacktree.com/)  
Invert your screen colors to reduce strain on the eyes at night, or to conserve battery power. Neat effect.

[Audio Overload](http://www.bannister.org/software/ao.htm)  
Pass the time by listening to music from the console video games of long ago. Video game music files found separately, somewhere out there on the [internet](http://www.google.com/).

That's it for now. Hope you find something useful.