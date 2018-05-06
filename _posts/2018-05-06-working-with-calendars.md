---
layout: post
title: Working with calendars
subtitle: Calendars in different languages
date: 2018-05-06 21:00
category: calendar
tags: [powershell, calendar, permissions]
---
Since I live in Norway, working with calendar folders in Powershell gets tricky.  
Especially when trying to script or fetching/changing several calendar permissions at once.

As we all know, when working with calendarpermissions we need to use `*-MailboxFolderPermission` to get, add, set or remove permissions. This is beacause the calendar is seen as just another Folder in the mailbox.  
In order to succesfully only target the calendarfolder, we need to spesify it by name

```powershell
get-mailboxfolderpermission -identity user@domain.com:\calendar
```

Easy enough.

But when working with a mulit lanuguage tenant, the calendar folder is not promptly named `calendar` every single time.  
Previously I would solve this either by running the commands two times, with each of the names.

```powershell
get-mailboxfolderpermission -identity user@domain.com:\calendar
#and
get-mailboxfolderpermission -identity user@domain.com:\kalender
```

Now this works when doing one or two calendars, we just get some nice red error messages from time to time. But when trying to script or do these changes to tens and even hundreds of calendar at once it both time consuming and inpractical to do this.

In the past i would solve this using a simple if statement.

```powershell
if (get-mailboxfolderpermission -identity user@domain.com:\calendar)
  { set-mailboxfolderpermission -identity user@domain.com:\calendar -user default -AccessRights Reviewer}
else
  { set-mailboxfolderpermission -identity user@domain.com:\kalender -user default -AccessRights Reviewer}
```

Then the other day i was browsing some forums for a solution to another problem, and came over this clever little thing:
```powershell
$Mailbox = Get-Mailbox -Identity $($user.SamAccountName)
$Calendar = (($Mailbox.SamAccountName)+ ":\" + (Get-MailboxFolderStatistics -Identity $Mailbox.SamAccountName -FolderScope Calendar | Select-Object -First 1).Name)
```
And with this I am able to get all my `username+\calendar` values lined up without any error messages, making it easy to du get, add, set and remove.

If anyone has similiar or even better ways of getting to the same result, i would love to hear about it.
