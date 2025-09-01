# Implementation details

## Overview

FIXME: explain that we use ssh, rsync and cron and why.
FIXME: maybe a figure to illustrate

## Bash functions

FIXME: Explain that the functionality is provided as bash functions and why this was chosen

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

When setting up the Raspberry Pi device, which key to use can be specified. If not, an rsa key stored under the .rpi directory will be used. If the specified key does not exist, it is created by rpiSetup and added to the .ssh/authorized_keys file on the Raspberry Pi device. Adding the key to the Raspberry Pi device requires entering the password for the specified user on the Raspberry Pi device, but after this, no password will be required when communicating with the Raspberry Pi from the host machine that ran rpiSetup.
