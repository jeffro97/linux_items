# Zombie processes

Command to see zombies: ps aux | awk '$8 ~ /^\[Zz\]/'

Number of zombies: ps aux | awk '$8 ~ /^\[Zz\]/' | wc -l

Show PIDs for zombies: ps aux | awk '$8 ~ /^\[Zz\]/' | awk '{print $2}'

Show parent PIDs of the zombie processes: ps -A -ostat,ppid | grep -e '\[zZ\]'| awk '{ print $2 }' | sort -u \[sort -u removes duplicates\]

To try and remove zombies, need to remove parent process:
\-- kill -SIGKILL <PID>

Resource Links:
[https://www.howtogeek.com/701971/how-to-kill-zombie-processes-on-linux/](https://www.howtogeek.com/701971/how-to-kill-zombie-processes-on-linux/)

Not going to work.
~~Clear all zombies:~~
~~#!/bin/bash~~

~~zpids="$(ps aux | awk '$8 ~ /^\[Zz\]/' | awk '{print $2}')"~~
~~for i in $zpids~~
~~do~~
~~echo "Killing $i process"~~
~~kill -9 $i~~
~~done~~

~~One liner to Clear:~~
~~zpids="$(ps aux | awk '$8 ~ /^\[Zz\]/' | awk '{print $2}')"; for i in $zpids; do echo "Killing $i process"; kill -9 $i; done~~