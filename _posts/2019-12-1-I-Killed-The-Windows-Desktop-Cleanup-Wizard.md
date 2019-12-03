Over almost two decades at Microsoft I have worked on a lot of stuff.  While shipping great products and features makes you feel great, I've found removing the terrible stuff is far more satisfying.  Nothing brought me more joy than killing the Windows Desktop Cleanup Wizard.

<p align="center">
  <img src="/assets/images/DesktopCleanupWizBalloon.png" alt="Desktop Cleanup Wizard Balloon Tip">
</p>

Many will recall, [and not fondly](https://medium.com/@dfeldman/writing-notifications-that-dont-suck-60a90256efe5), that annoying balloon tip that would pop up from the system tray in Windows XP.  While it was distracting it was also confusing for those with no icons on their desktop aside from the Recycle Bin.  Microsoft's own design docs call it out amongst the [Windows XP Hall of Shame](https://docs.microsoft.com/en-us/windows/win32/uxguide/mess-notif#design-concepts) for "annoying, inappropriate, useless, irrelevant notifications".

The Desktop Cleanup Wizard first appeared in late 2000 during Windows XP development and was referred to internally as the folder cleaner (fldrclnr.dll).  I removed the code late in the Windows Vista product cycle.    Digging around recently I found the code change:

> Change 280177 by WINGROUP\chrdavis@SHBLD25 on 2006/04/30 23:24:22
> BUG: 1575578: Remove fldrclnr from build

Unlike big game hunters who can hang their trophies over their mantel to humble brag to their acquaintances, this blog post will have to do.

<p align="center">
  <b>R.I.P.</b><br>
  <b>Desktop Cleanup Wizard</b><br>
  <b>2000 - 2006</b><br>
</p>

### UPDATE
After going through the code changes some more I found that my friend Aidan Low had removed the infamous balloon tip almost a year prior to my change that removed the feature entirely.  Ah well.  

> Change 166922 by NTDEV\aidanl@SHBLD04 on 2005/07/12 13:30:35
> Remove "you have unused icons" balloon. The end of an era; one of the major annoyances of XP is now gone.


