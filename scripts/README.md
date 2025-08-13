# User guide

## Getting started

### pre-requisites

In order to be able to use this set of bash functions, you need:

* a Raspberry Pi device turned on and connected to the internet
* a camera module attached to the Raspberry Pi device
* the IP address of the Raspberry Pi device
* the username and the password for one user on the Raspberry Pi device
* a host machine able to run bash (e.g. using terminal on a Mac OS X or linux, using Putty on Windows)

### Making my first photo

First of all, you need to clone this repo onto your host machine:

```
$ cd my_rpi_dir && git@github.com:knudhansen/rpi.git
```

Then you need to source the file defining all the function of this repo:

```
$ source scripts/rpi.source
```

Now you need to specify the Raspberry Pi device you want to use. This is done by calling the rpiSetup function

```
$ rpiSetup 192.168.0.77 raspberrypi
raspberrypi@192.168.0.7's password:
```

This will create a `.rpi` directory under the home directory of your user on the host machine and save the information about the Raspberry Pi device in it.
It will also write the authentication public key onto the Raspberry Pi device. This requires typing the password for the chosen user on the Raspberry Pi device.

```
$ rpiCapturePhoto my_first_photo.jpeg
```

This function will have the Raspberry Pi device take a photo and will then fetch the photo back onto the host machine.

```
$ ls -al my_first_photo.jpg
-rw-r--r--@ 1 myuser  mygroup  1131714 11 Aug 11:59 my_first_photo.jpg
```

### Scheduling a daily video

Now that we have taken our first photo, we know that our Raspberry Pi is connected correctly to our host machine, and we now can try to setup daily video recording.

First let's check that we do not have any scheduled daily videos:

```
$ rpiSchedVideoListDaily
$ rpiSchedVideoListRecordings
```

Both of these functions should return with no output.

Now let's schedule a 56 seconds video recording every day at 12:34.

```
$ rpiSchedVideoAddDaily 12:34 56
```

If we look at the list of scheduled videos, we now should see the created schedule:

```
$ rpiSchedVideoListDaily
12:34 56     -- /etc/cron.d/rpicam_12_34_56: 34 12 * * * raspberrypi . ~/rpi.source >>/home/raspberrypi/.rpi/rpi.log 2>&1 && rpiCaptureVideo $(rpiSchedVideoFileName 56) 56 >>/home/raspberrypi/.rpi/rpi.log 2>&1
$ rpiSchedVideoListRecordings
```

It is not 12:34 yet, so There are still no recordings on the Raspberry Pi device, why rpiSchedVideoListRecordings outputs nothing. Once it is 12:35 (the recording finishes around 12:34:56), we can check that the recording has been made and is present on the Raspberry Pi device:

```
$ rpiSchedVideoListRecordings
rpicamvid_2025_08_11_12_34_56.mp4
```

We can then retrieve the recording to the host machine:

```
$ rpiSchedVideoRetrieveRecordings
receiving file list ... done
./
12_34_56/rpicamvid_2025_08_11_12_34_56.mp4

sent 44 bytes  received 905626 bytes  603780.00 bytes/sec
total size is 905219  speedup is 1.00
```

They are saved on the host machine under the rpi_sched_video directory

```
$ find rpi_sched_video/ -type f
rpi_sched_video/12_34_56/rpicamvid_2025_08_11_12_34_56.mp4
```

# Implementation details

## Raspberry Pi device file organization

On the Raspberry Pi device, all files used by the functions in this repo are located under the home directory of the used user on the Raspberry Pi device.

### Function definition files

```
~/rpi.source
```

This is a file that is updated and sourced every time rpissh is used. This file makes sure that the functions defined for the Raspberry Pi in the rpi.source file are available and up-to-date. 

### Scheduled video files

```
~/rpi_sched_video/<schedule id>/<video file name>
```

where:

`schedule id`: ID of the schedule is `<schedule time hours>_<schedule minutes>_<duration seconds>`

`video file name`: the name of the video is not based on the schedule time, but on the actual date and time where the recording is started (they of course relate to each other)

example of file:

```
~/rpi_sched_video/12_34_56/rpicamvid_2025_08_11_12_34_56.mp4
```

## Authentication

Communication with THe Raspberry Pi device is done via SSH (and rsync, but that also uses SSH). In order to avoid being prompted a password for every communication, SSH communication uses an authentication key instead.

When setting up the Raspberry Pi device, which key to use can be specified. If not, an rsa key stored under the .spi directory will be used. If the specified key does not exist, it is created by rpiSetup and added to the .ssh/authorized_keys file on the Raspberry Pi device. Adding the key to the Raspberry Pi device requires entering the password for the specified user on the Raspberry Pi device, but after this, no password will be required when communicating with the Raspberry Pi from the host machine that ran rpiSetup.

# RPI Functions Reference

## Host functions

### rpiSetup

```
rpiSetup <rpi ip address> <username> [<ssh key>]
```

This bash function sets-up the information needed to connect to the Raspberry pi device and makes it possible to access it without repeating these information on every access.
Calling this is necessary to be able to use the other functions described here.
Note that there is currently only support for one such device.

The information about the Raspberry Pi device are stored inside the .rpi directory in the home of the user who calls rpiSetup.

`rpi ip address`: IP address of the Raspberry Pi device to communicate with.

`username`: The name of the user to use on the Raspberry Pi device.

`key`: key to use when ssh'ing with the Raspberry Pi device. This defaults to ~/.rpi/rpi_rsa. If the key is not present, it will be created byt the rpiSetupFunction. The key is sent to the Raspberry Pi device to avoid being prompted a password on each ssh connection to the Raspberry Pi device. That key is also used for all the rsync functions provided.

### rpiGetAccess

```
rpiGetAccess
```

This bash function prints on stdout the access string <username>@<ip address> for the Raspberry Pi device that has been setup using [rpiSetup](#rpiSetup). It is mainly thought of as an internal function.

### rpiGetSshOptions

```
rpiGetSshOptions
```

This bash function returns the options to be added to ssh commands for using the key specified during rpiSetup.

### rpissh

```
rpissh [<command>]
```

This bash function is used to ssh to the Raspberry Pi that has been setup using [rpiSetup](#rpiSetup).

`command`: optional command to execute on the Raspberry Pi device. If command is passed, then the command is executed on the Raspberry Pi device (make sure to use quotes and escape elements in the command as needed). If command is omitted, a shell will be created on the Raspberry Pi device allowing interaction with it.

### rpiGetSerialNumber

```
rpiGetSerialNumber
```

This bash functions returns the serial number of the Raspberry Pi that has been specified when calling rpiSetup.

### rpirsync

```
rpirsync <source> <destination>
```

This bash function rsyncs files from source to destination. Files on the Raspberry Pi device have to be prefixed with the user to use on the Raspberry Pi device and the Raspberry Pi device IP address.

`source`: file/dir to rsync

`destination`: where to copy file/dir

Example:

```
rpirsync myFavouriteDir/ raspberrypi@192.168.0.77:myFavouriteDirOnRpi/
```

### rpirsyncFrom

```
rpirsyncFrom <path>
```

This bash function rsyncs from the Raspberry Pi setup using [rpiSetup](#rpiSetup) to the machine that the function is run on.

`path`: path that should be rsynce'ed. The path is relative to the Raspberry Pi user home and is copied to the same path relative to the current work directory on the host machine.

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

This function schedules a video of <duration in seconds> to be recorded daily at HH:MM. It will fail if another
video is already scheduled for the same time and return error code 1.

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

### rpiSchedVideoRetrieveRecordings

```
rpiSchedVideoRetrieveRecordings
```

Fetches the scheduled recordings from the Raspberry Pi device

### rpiSchedVideoDeleteRecordings

```
rpiSchedVideoDeleteRecordings
```

Delete all the scheduled recordings from the Raspberry Pi device

### rpiSchedVideoListRecordings

```
rpiSchedVideoListRecordings
```

List the scheduled recordings present on the Raspberry Pi device

## Raspberry Pi functions

FIXME

# Improvement list

* use tool to create a proper rpi server (like numbat does)
* have a cleaner way of handling the files on the Raspberry Pi
* maybe make the functions work on rpi as on remote host... Or is it too much abstraction?
* make sure that there is space enough before recording a video. Investigate how much space is needed
* investigate what additional parameter are needed for the video (resolution and exposure control)
* make the calls from host to rpi a bit less stupid: i.e. use FUNCNAME instead of duplicating it in ssh
* add number of executed schedules to the list function
* add function to list the videos on host and raspberry pi
* add argument to be able to choose the dir where video are placed on host
* how to control camera settings
* tidy up error codes
* DONE -- check that the Raspberry Pi device has been setup when calling the other functions
* DONE -- have a handler to make sure that concurrent uses of the Raspberry Pi do not collide
* DONE -- cron setup helper functions
* DONE -- cleanup command naming for schedule
* DONE -- add function to retrieve the videos
* DONE -- add client to authorized_keys as part of setup
* DONE -- add raspberry pi serial number to saved videos
* DONE -- place the scheduled video files somewhere a bit cleaner
* DONE -- fail when trying to create the same schedule twice
* DONE -- warning when adding a schedule overlaping with another schedule
* DONE -- tidy up the schedule stuff: different formats "HH:MM duration" (user friendly) "HH:MM_duration" (script friendly - no spaces) "MM HH * * * ...duration..." (cron)
* DONE -- add a function to fetch the scheduled videos (maybe including deleting them...)
* DONE -- currently, the chosen key is added to authorized_keys for every rpiSetup. Maybe remove duplicates?
