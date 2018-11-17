---
layout: post
title: Leave GMail in 10 steps
date: 2018-11-17 08:05
categories:
---

# Blog post - 10 steps guide to leave Gmail

> In exchange for free mails, would you let your postman open your letters, read them, and insert ads related to their contents ?

This is a common analogy for GMail's model. The privacy implications of using GMail actually go much farther than that, because GMail is not only your postman (with Gmail), it also owns your car ( your browser), your address book (with Search), your TV (with Youtube) it's a great land owner (with Adsense) ... There are many reasons not to trust google, [especially with your privacy](http://precursorblog.com/?q=content/googles-top-35-privacy-scandals).

I decided to start leaving Google when they recently decided to make Chrome auto-connect you to your Google account. This can appear anecdotal and I’m sure it was also a matter of timing as I happened to have some time and energy to pursue this endeavour.

Migrating away from any email service, like changing addresses in real world life, is always going to be tedious. I did not find that GMail makes it particularly harder, but this guide should help you nevertheless.

## Step 1 : Get a new mail address and provider

I chose to go with [Fastmail](https://fastmail.com/) which looks like the most popular privacy-respecting alternative.

Signing up on [Fastmail](https://fastmail.com/) is very easy. You can use one of the many Fastmail domains (fastmail.com, mailbolt.com, sent.com ...). You may also purchase one and use it with Fastmail. [Gandi](https://www.gandi.net/) is a good registrar for buying a domain. Fastmail has an easy-setup option for custom domains, where you use their nameservers and they do all the hardlifting. And you’ll very much be able to add custom DNS entries later if you will, for example to your personal website or blog.

## Step 2 : Clients setup and update round

By client, here I mean the applications that you are going to use to read and write mail. GMail has both a web client (a website) and mobile apps.

There are lots of options for mail clients. Fastmail comes with a web client too, that is quite different from GMail but works well. For desktop and mobile apps, I suggest you start with the native apps of your OS (the ones that come pre-installed with Windows, OSX, iOS, Android). Fastmail has some [great guides](https://www.fastmail.com/help/clients/applist.html) for this and it's sometimes as easy as scanning a personalized QR Code.

You can try sending a mail from your GMail to your new mail and check that you're receiving it on all the devices that you care about. Once you're up and running with your new address, it's the perfect moment to try and update your mail address on the services you use daily (Social networks, Github, Amazon). If you use a password manager it can help you identify the services you’ve forgot.

## Step 3 : Delete useless mails from gmail

I strongly suggest that you take the time to clean up the clutter you’ve accumulated in your GMail before anything. Here are some reasons to do so :

- migrating your mails will be faster
- you won't reach the storage limit on your new provider as fast
- it feels good :)

It’s important to do it before migrating or else you’ll be cleaning up 2 mailboxes (I did and lost a lot of time).

Look at your space usage for mail on [this Google page](https://drive.google.com/settings/storage), by clicking on the details link. If it looks reasonable, you may skip this step.

The first way to find useless mails is easy : look for mails with big attachments. You can use this GMail search query `size:5MB` or any other threshold.

The second way is trickier : you want to look for redundant, similar mails : facebook notifications, google alerts, GitHub comments... This can represent a very high number of mail and thus of space. Surprisingly I could not find a good tool to identify these mails. It cannot be done with google queries. There is a plug-in in Thunderbird but it was very buggy for me and opening Thunderbird feels like travelling back to the 90s :( The best way I found was to use python surprisingly good mbox files support out of the box. I published a super small library [here TODO](). You'll need to export your data from GMail another time (see Step 4 below). I may turn it into a simpler to use service for non devs in the future. It will list all sender mails that have sent over 100 mails to you. You can then go filter them out on google and decide if you want to drop them.

## Step 4 : Export your data from google and archive it

I have to hand it out to google they make it quite easy to export your mails. Head to [Google Takeout](https://takeout.google.com) and select mails. You’ll need to have it locally so I suggest you take the download option. They’ll mail you a link once they’ve gathered it all in a nice mbox file.

I suggest you store this mbox archive somewhere safe, ie. not a hard drive :) I went with S3 Cloud Storage. You want to keep this file for a long while, maybe a year or so.

## Step 5 : Migrate all mails

Important : do not use Fastmail's Identities / Fetch feature at this point.

Fastmail has a very nice [Migrate IMAP Mailbox](https://www.fastmail.com/go/migrateimap) tool. GMail's IMAP server is `imap.gmail.com`, your username is your full GMail address, and if you are using 2-Step Verification, you need to use an [App Password](https://security.google.com/settings/security/apppasswords).

This step can take a while, a whole night for me. Once it's done, do give a look at the logs as you don’t want to miss anything. You should also have a look at your mails, browsing to the oldest is a good idea.

## Step 6 : Delete all mails from gmail

Once you’re confident you have everything on your new account and have backed up the archive file, you may go back to gmail and delete all your mails. That is a very thrilling step !

Note : Because the migration process takes a while, you may have received mail to your GMail account between steps 5 and 6, and that mail was not migrated to your new account. In that case, you may want to limit the mails you're deleting using a search filter.

## Step 7 : Monitor mails still incoming to gmail

At this point I suggest to keep the GMail app on your mobile device and monitor two mailboxes for a week or two. It will force you to realize which mails are still coming to the "wrong" mailbox. As soon as you receive a mail to GMail, try and fix it for the future. If it’s an automated mail from a service you use, go update your address. If it’s from a person, then you can let them know you’ve changed your mail address.

## Step 8 : Synchronise leftover mails to gmail with your new provider

Now, you should have only few mails coming to your GMail account. However, it will hardly be zero, and you don't want to miss mails. Also, you want to import the mails that arrived in your GMail during these 2 weeks of transition. You can now setup the [Identities & Fetch](https://www.fastmail.com/settings/accounts) feature with your GMail account. All the transition mails and the future incoming ones will now be imported to your new account. You can also setup a sender alias to still be able to write mails with your GMail address.

You can now remove the GMail apps from all your devices, you're almost free !

## Step 9 : Actually close your GMail account

You can follow [Google's guide](https://support.google.com/accounts/answer/61177) to do so. Some nice facts are that your address may never be used by anyone in the future, and that your Google Account won't be deleted in the same time.

I'm a cheater : I haven’t actually gone through this step yet. I think I will wait at least one year in order to be sure I'm not receiving any import mail to my GMail address.

## Step 10 : Go the extra mile

While you’re at it you may want to try and replace other google services.

Natural followers are the Calendar and Contacts apps. These are two pieces of sensitive data that you can very easily export from Google and import into your new Fastmail account. Then you can again replace the Google Calendar and Google Contacts apps with alternatives. The Apple ones have proven far good enough for my moderate usage.

If you want to go further, you can check the [No More Google list](https://nomoregoogle.com/) to find popular alternatives to Google Services.

I have to say I was incredibly surprised how easy it was to stop using Google Chrome. As it was noted the new Firefox is fast and almost bug free. It also features all the web development tools I need. I was also using Chrome on my iPhone 5SE but I’m actually liking Safari more now that I’ve switched. The transition was smooth and intuitive and it’s obviously better integrated with iOS.

The only apps I really couldn’t stop using are Maps and Docs (specifically the spreadsheets). They both have features I could not find anywhere else and which I don't want to stop using altogether.
