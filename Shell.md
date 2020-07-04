## grep crontab lines 

```
crontab -l | grep -v ^# | grep "\s"
```

## Set
To write the command and the result
```
set -x  
```
To stop the script when one command gives error code
```
set -e
```

## inotify script

https://raw.githubusercontent.com/yousong/gists/master/shell/inotify_watchers.sh

## Force ntp client to update 

Old SO

```sh
service stop ntpd
ntpd -gq
service start ntpd
```
## Execute commands without save in history file

```
export HISTFILE=/dev/null
```

## Remove Special characters from linux console

https://www.linux.com/blog/linux-shell-tip-remove-files-names-contains-spaces-and-special-characters-such


## How test HTTPS URL with a given IP Address using CURL

```
curl -vs https://www.realizaunicred.com.br/ --resolve 'www.realizaunicred.com.br:443:189.10.179.93' -o /dev/null
```

## List all files recursively in an order by date and human readly

```
stat --printf="%y %n\n" $(ls -tr $(find * -type f))
```

## Delete old files using find 

Ex: delete older files, more than 5 days
```sh
find /path/to/directory/ -mindepth 1 -mtime +5 -delete
```
Replace `-delete` with `-depth -print` to test this command before you run it (-delete implies -depth).


## Find zombies process

```
ps aux | grep 'Z'
```
What you get is Zombies and anything else with a Z in it, so you will also get the grep:
```
USER       PID     %CPU %MEM  VSZ    RSS TTY      STAT START   TIME COMMAND
usera      13572   0.0  0.0   7628   992 pts/2    S+   19:40   0:00 grep --color=auto Z
usera      93572   0.0  0.0   0      0   ??       Z    19:40   0:00 something
```
Find the zombie's parent:
```
pstree -p -s 93572
```
will give you 
```
init(1)---cnid_metad(1311)---cnid_dbd(5145)
```

## List all repositories and PPAs 
```
egrep -v '^#|^ *$' /etc/apt/sources.list /etc/apt/sources.list.d/*
```

## Strace
Single process
```
sudo strace -p `pidof apache2 | tr ' ' ','`
```
Multiple processes
```
sudo strace -f -tt -o /tmp/php.trace -s1024 -p `pidof php5-fpm | tr ' ' ','`
```
Alternative 1:
```
strace -tt -s2048 $(pidof httpd |sed 's/\([0-9]*\)/\-p \1/g')
```
Alternative 2:
```
ps auxw | grep sbin/apache | awk '{print"-p " $2}' | xargs strace
```

## Single boot in Centos 7
Step 1: On the boot, press any key to enter in grub2 menu

Step 2: Press “e” key to edit arguments of kernel.

Step 3: Go to second last line (Starts with linux 16 or linuxefi) using up and down arrow then modify the ""ro"" argument.

Step 4: Modify it to “rw init=/sysroot/bin/sh”. Once done, press “Ctrl+x”

Step 5: Boot and enter the operative system

Step 5: Boot and enter the operative system
```
chroot /sysroot
```

## Cron.daily doesn’t run in Centos 7
```
yum -y install cronie-noanacron
```

## Kill processes orphaned
```
kill -9 `pidof -x 'ansible-playbook'`
```

## Verify permissions file with stat

```
[root@Niflheim tmp]# ls -alF .
total 1632
drwxrwxrwt 15 root root    4096 Apr  7 04:24 ./
drwxr-xr-x 28 root root    4096 Apr  2 21:02 ../
[root@Niflheim tmp]# stat -c '%A %a %n' .
drwxrwxrwt 1777 .

```

## Runnig Redis Monitor

```
redis-cli monitor
```
or 
```
telnet localhost 6379
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
MONITOR
+OK
```
Alternative way, via ngrep
```
ngrep -d any -pqt -W single '' port 6379
```

## Find

Using regexp

```
find ./ ! -regex  '.*\(deb\|vmdk\)$'
```
http://unix.stackexchange.com/questions/50612/how-to-combine-2-name-conditions-in-find

Find by time

```
find /home/you -iname "*.txt" -mtime -60 -print
```
http://www.cyberciti.biz/faq/howto-finding-files-by-date/

## Create file using EOF

```
cat <<EOF >> brightup.sh
!/bin/bash
curr=`cat /sys/class/backlight/intel_backlight/actual_brightness`
if [ $curr -lt 4477 ]; then
   curr=$((curr+406));
   echo $curr  > /sys/class/backlight/intel_backlight/brightness;
fi
EOF
```
If you don't want to translate environment vars. Literally text will be written, use simple quotes

```
cat << 'EOF' >> brightup.sh
!/bin/bash
curr=`cat /sys/class/backlight/intel_backlight/actual_brightness`
if [ $curr -lt 4477 ]; then
   curr=$((curr+406));
   echo $curr  > /sys/class/backlight/intel_backlight/brightness;
fi
EOF
```

## Replace text recursively in a tree of files  

```
find . -name '*' -type f -exec sed -i 's/elastic_version/elk_version/' {} \;
```

## Check Network traffic 

```
iftop -i eth0:34 -P -nN
```

## why-doesnt-this-show-the-hidden-files-folders

```
du -sch .[!.]* * |sort -h
```

```
sudo apt-get install ncdu
```
