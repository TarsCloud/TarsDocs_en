# @ tars / notify

Report business (framework) messages (alarms) to the `TARS` platform.

## report (message [, id])

Report the message to the platform and view it on the web management page.

* _message_: message content (__required__)
* _id_: service thread (process) ID, *default value is process.pid*

## notify (message [, level, id])

Report notification information to the platform.

* _message_: notification content (__required__)
* _level_: Level of notification content, LEVEL enumeration, *The default value is LEVEL.NOTIFYNORMAL*
* _id_: service thread (process) ID, *default value is process.pid*

There are 3 options in the `LEVEL` enumeration:

* _LEVEL.NOTIFYNORMAL_: Normal (default)
* _LEVEL.NOTIFYWARN_: warning
* _LEVEL.NOTIFYERROR_: error

The platform alerts the reported anomalies every 10 minutes.