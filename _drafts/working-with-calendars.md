---
layout: post
title: Working with calendars
subtitle: Calendars in different languages
meta_robots: noindex
tags: [powershell, calendar, permissions]
---
Since I live in Norway, working with calendar folders in Powershell gets tricky.  
Especially when trying to script or fetching/changing several calendar permissions at once.

As we all know, when working with calendarpermissions we need to use `*-MailboxFolderPermission` to get, add, set or remove permissions. This is beacause the calendar is seen as just another Folder in the mailbox.  
In order to succesfully only target the calendarfolder, we need to spesify it by name

```powershell
get-mailboxfolderpermission -ideentity user@domain.com:\calendar
```

Easy enough.  
But when working with a mulit lanuguage tenant, the calendar folder is not promptly named `calendar` every single time.  
Previously I would solve this either by running the commands two times, with each of the names.

```powershell
get-mailboxfolderpermission -ideentity user@domain.com:\calendar
#and
get-mailboxfolderpermission -ideentity user@domain.com:\kalender
```

Now this works when doing one or two calendars, we just get some nice red error messages from time to time. But when trying to script or do these changes to tens and even hundreds of calendar at once it both time consuming and inpractical to do this.

In the past i would solve this using a simple if statement.

```powershell
if (get-mailboxfolderpermission -identity user@domain.com:\calendar)
  { Do-Someting}
else
  {
