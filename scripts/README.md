# User guide

The bash functions provided are simple wrappers that make it possible to access a *single* Raspberry Pi device without having to repeatedly specify the device's address and user.

To do so, first call the [rpiSetup](#rpiSetup) function with the IP of the Raspberry Pi device and the username to use on it. Once this is done, the remaining functions described in the [reference](#Reference) section can be used.

# Reference

## Host functions

### rpiSetup

```
rpiSetup <rpi ip address> <username>
```

This bash function sets-up the information needed to connect to the Raspberry pi device and makes it possible to access it without repeating these information on every access.
Calling this is necessary to be able to use the other functions described here.
Note that there is currently only support for one such device.

The information about the Raspberry Pi device are stored inside the .rpi directory in the home of the user who calls rpiSetup.

`rpi ip address`: IP address of the Raspberry Pi device to communicate with.

`username`: The name of the user to use on the Raspberry Pi device.

### rpiGetAccess

```
rpiGetAccess
```

This bash function prints on stdout the access string <username>@<ip address> for the Raspberry Pi device that has been setup using [rpiSetup](#rpiSetup). It is mainly thought of as an internal function.

### rpissh

```
rpissh [<command>]
```

This bash function is used to ssh to the Raspberry Pi that has been setup using [rpiSetup](#rpiSetup).

`command`: optional command to execute on the Raspberry Pi device. If command is passed, then the command is executed on the Raspberry Pi device (make sure to use quotes and escape elements in the command as needed). If command is omitted, a shell will be created on the Raspberry Pi device allowing interaction with it.

### rpirsyncTo

```
rpirsyncTo <path>
```

This bash function rsyncs from the machine that the function is run on and to the Raspberry Pi setup using [rpiSetup](#rpiSetup).

`path`: path that should be rsynce'ed.

### rpirsyncFrom

```
rpirsyncFrom <path>
```

This bash function rsyncs from the Raspberry Pi setup using [rpiSetup](#rpiSetup) to the machine that the function is run on.

`path`: path that should be rsynce'ed.

### rpiCapturePhoto

```
rpiCapturePhoto <file>
```

This bash function takes a photo on the Raspberry Pi setup using [rpiSetup](#rpiSetup) and transfers the photo back to the machine running the function.

`file`: name of the file that the photo should be saved as.

### rpiCaptureVideo

```
rpiCaptureVideo <file> <time in seconds>
```

This bash function makes a video on the Raspberry Pi setup using [rpiSetup](#rpiSetup) and transfers the video back to the machine running the function.

`file`: name of the file that the video should be saved as.

`time in seconds`: number of seconds that the video should be recorded for.

### rpiSchedVideoAddDaily

```
rpiSchedVideoAddDaily <time as HH:MM> <duration in seconds>
```

This function schedules a video of <duration in seconds> to be recorded daily at HH:MM

`time as HH:MM`: time at which a video should be recorded every day. Format is hours (24 hour notation) and
                 minutes, e.g. 23:39.

`duration in seconds`: number of seconds to record the video for

### rpiSchedVideoListDaily

```
rpiSchedVideoListDaily
```

Lists the daily scheduled video captures. The format of the output is e.g.

```
23:39 12     -- 39 23 * * * raspberrypi . ~/rpi.source >>/home/raspberrypi/.rpi/rpi.log 2>&1 && rpiCaptureVideo rpicamvid_2025_08_09_Aug_08_12 12 >>/home/raspberrypi/.rpi/rpi.log 2>&1
23:37 7      -- 37 23 * * * raspberrypi . ~/rpi.source >>/home/raspberrypi/.rpi/rpi.log 2>&1 && rpiCaptureVideo rpicamvid_2025_08_09_Aug_08_7 7 >>/home/raspberrypi/.rpi/rpi.log 2>&1
```

### rpiSchedVideoRemoveDaily

```
rpiSchedVideoRemoveDaily <time as HH:MM> <duration in seconds>
```

Remove one scheduled daily video specified by the same parameters as were used to create it.
The values of the parameter are also presented when using the (#rpiSchedListDailyVideo) command.

`time as HH:MM`: time at which a video should be recorded every day. Format is hours (24 hour notation) and minutes, e.g. 23:39.

`duration in seconds`: number of seconds to record the video for


### rpiSchedVideoRemoveAllDaily

```
rpiSchedVideoRemoveAllDaily
```

Removes all the scheduled daliy videos

## Raspberry Pi functions

FIXME

# Improvement list

* use tool to create a proper rpi server (like numbat does)
* have a cleaner way of handling the files on the Raspberry Pi
* maybe make the functions work on rpi as on remote host... Or is it too much abstraction?
* make sure that there is space enough before recording a video. Investigate how much space is needed
* investigate what additional parameter are needed for the video (resolution and exposure control)
* fail when trying to create the same schedule twice
* warning when adding a schedule overlaping with another schedule
* place the scheduled video files somewhere a bit cleaner
* make the calls from host to rpi a bit less stupid: i.e. use FUNCNAME instead of duplicating it in ssh
* add a function to fetch the scheduled videos (maybe including deleting them...)
* add number of executed schedules to the list function
* add function to list the videos on host and raspberry pi
* add argument to be able to choose the dir where video are placed on host
* add raspberry pi serial number to saved videos
* add client to authorized_keys as part of setup
* how to control camera settings
* DONE -- check that the Raspberry Pi device has been setup when calling the other functions
* DONE -- have a handler to make sure that concurrent uses of the Raspberry Pi do not collide
* DONE -- cron setup helper functions
* DONE -- cleanup command naming for schedule
* DONE -- add function to retrieve the videos
