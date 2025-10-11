[main][main-link]

# Implementation details

## Overview

In order to achieve the goal of the repository, the main tools the bash functions provided use are:

* ssh: to communicate between the host and the Raspberry Pi device.

* rsync: to download files from the Raspberry Pi device to the host

* cron: to manage the scheduling of videos

FIXME: maybe a figure to illustrate

## Bash functions

We have chosen to use bash, as it makes it possible to easily and directly use the tools provided by
both the host and the Raspberry Pi device.

## Host file organization

### .rpi directory

The `.rpi` directory contains some files with information about the targetted Raspberry Pi device. These files are:

* rpi.ip containing the IP address of the Raspberry Pi device
* rpi.username containing the username of the user on the Raspberry Pi device
* rpi.key containing the name of the key file for communicating with the Raspberry Pi device
* the key files themselves (by default rpi_rsa and rpi_rsa.pub)

```
$ find ~/.rpi -type f | sort
/Users/knudhansen/.rpi/rpi.ip
/Users/knudhansen/.rpi/rpi.key
/Users/knudhansen/.rpi/rpi.username
/Users/knudhansen/.rpi/rpi_rsa
/Users/knudhansen/.rpi/rpi_rsa.pub
```

## Raspberry Pi device file organization

On the Raspberry Pi device, all files used by the functions in this repo are located under the home directory of the used user on the Raspberry Pi device.

### .rpi directory

The `.rpi` directory contains some files internally used by the RPI bash function run on the Raspberry Pi device. These files are:

* rpicam.flock for ensuring that the camera is only used by one thread at a time
* rpi.log file used to store information about the execution of the functions on the Raspberry Pi device.
  Among others, the output of the actions scheduled by cron will append their output to this file.

```
$ rpissh "find ~/.rpi -type f | sort"
/home/raspberrypi/.rpi/rpicam.flock
/home/raspberrypi/.rpi/rpi.log
```

### Function definition files

```
~/rpi.source
```

This is a file that is updated and sourced every time rpissh is used. This makes sure that the functions defined for the Raspberry Pi in the rpi.source file are available and up-to-date.

### Cron schedules

The scheduling of videos is done using Cron. This is done by adding files to the `/etc/cron.d` directory. One file is added for each schedule, and the files are named `rpicam_<hour of start>_<minute of start>_<duration in s>`.

In order to see all the scheduled videos, use the rpiSchedVideoListDaily.

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

### Log file

```
~/.rpi/rpi.log
```

This is the file where all the internal bash functions running on the Raspberry Pi device and
the RPI action scheduled by cron write there output.

## Authentication

Communication with the Raspberry Pi device is done via SSH (and rsync, but that also uses SSH). In order to avoid being prompted a password for every communication, SSH communication uses an authentication key instead.

When setting up the Raspberry Pi device, which key to use can be specified. If not, an rsa key stored under the .rpi directory will be used. If the specified key does not exist, it is created by rpiSetup and added to the .ssh/authorized_keys file on the Raspberry Pi device. Adding the key to the Raspberry Pi device requires entering the password for the specified user on the Raspberry Pi device, but after this, no password will be required when communicating with the Raspberry Pi from the host machine that ran rpiSetup.

[main-link]: ./README.md