- [kernel:](#kernel)
  - [sysctl](#sysctl)
- [Text Manipulation](#text-manipulation)
  - [find:](#find)
  - [ag:](#ag)
  - [grep (egrep)](#grep-egrep)
  - [fgrep](#fgrep)
  - [aws](#aws)
  - [sed](#sed)
  - [sort](#sort)
  - [uniq](#uniq)
  - [cat](#cat)
  - [cut](#cut)
  - [wc](#wc)
  - [fmt](#fmt)
  - [echo](#echo)
- [Network](#network)
  - [ping](#ping)
  - [dig](#dig)
  - [nslookup](#nslookup)
  - [route](#route)
  - [traceroute](#traceroute)
  - [iptables](#iptables)
  - [netstat](#netstat)
  - [ss](#ss)
  - [tcpdump](#tcpdump)
  - [airmon](#airmon)
  - [airodump](#airodump)
- [Process Monitoring](#process-monitoring)
  - [ps](#ps)
  - [top](#top)
  - [htop](#htop)
  - [atop](#atop)
  - [glace](#glace)
  - [lsof](#lsof)
- [Service Management](#service-management)
  - [Sysvinit](#sysvinit)
  - [Upstart](#upstart)
  - [Systemd](#systemd)
  - [Systemd Journal](#systemd-journal)

# kernel:
## sysctl
  * Configure kernel parameters at runtime
  * -p [FILE], --load
    * Load in sysctl settings from the file specified or /etc/sysctl.conf if none given.
# Text Manipulation
## find:
  * usage:
    * `$ find -name "*.sh" -type f maxdepth 5| xargs rm -f`
  * options:
    * -type:
      * file type
    * -name:
      * True if the last component of the pathname being examined matches pattern
    * -iname: case insensitive of version of name
    * maxdepth
    * mindepth
## ag:
  * usage:
  * options:
    * depth
      * -1 is unlimited
## grep (egrep)
  * Usage
    * `$grep -inrH ".*darwin" *.sh `
    * `$grep `
  * options:
    * r:
      * recursive
    * -i
      * case insensitive
    * -n:
      * line number
    * -H:
      * print filename for each match
    * --include
      * specify file pattern
## fgrep
## awk
## sed
## sort
## uniq
## cat
## cut
## wc
## fmt
## echo

# Network
## ifconfig
  * Usage:
    * Show all network interfaces
      * `$ifconfig `
    * Shoe the specific network interface
      * `$ifconfig ${interface}`
## ifup, ifdown, ifquery
  * up
    * Bring a network interface up
  * down
    * Take a network interface up
  * query
    * Parse interface configuration
  * Usage:
    * `ifup -i ${interface_file}`
    * `ifup -a`
    * `ifup eth0`
    * `ifquery -l`
    * `ifquery eth0`
  * Options:
    * -a: --all 
      * If given to ifup
        * affect all interfaces marked auto. Interfaces are brought up in the order in which they are defined in /etc/network/interfaces. 
      * If given to ifdown, 
        * affect all defined interfaces. Interfaces are brought down in the order in which they are currently listed in the state file. Only  interfaces  defined  in /etc/network/interfaces will be brought down.
    * -i FILE:
      * Read interface definitions from FILE instead of from /etc/network/interfaces.


## ping
  * Usage:
    * `$ping -c 2${domain}`
    * `$ping -c 2${ip}`
    *  
  * Options:
    * -c: count
    * -W: timeout
    * 
## dig
  * Usage:
    * NS
      * find Name server of the from.ers.trendmicro.com
      * `$dig NS from.ers.trendmicro.com`
    * A (IPv4)
      * `$dig A hashsrv.ers.trendmicro.com`
    * AAAA (IPv6)
      * `$dig AAAA hashsrv.ers.trendmicro.com`
    * Reverse DNS lookup
      * `$dig -x 8.8.8.8` 
    * Specify DNS server
      * `@{ip_or_hostname}`
     
  * Ref:
    * http://dns-learning.twnic.net.tw/bind/intro6.html
## nslookup
  * query Internet name servers interactively
    * `$nslookup google.com.tw`
## route
  * Usage:
    * `$route -n`
    * `$netstat -nr` can show routing table as well.
  * show / manipulate the IP routing table
 
## traceroute
  * each endpoint tests 3 times.
  * Options:
    * -4: 
      * ipv4
    * -6: 
      * ipv6
    * -w 
      * (--wait)
    * -n: 
      * do not resolve IP addresses to their domain names 
    * -U (--udp): 
      * default
      * use UDP with port 53
    * -T (--tcp): 
      * use TCP SYN to tracerouting
    * -I (--icmp) 
      * use ICMP echo for tracerouting
    * -p:
      * port
  * UDP
    * `$ traceroute google.com.tw`
  * TCP, wait 1 sec, do not resolve host name
    * `$ traceroute -T -n -w 1 google.com.tw`
## iptables
## netstat
  * Usage
    * list listening services
      * `$ netstat -tulnp`
    * list all services
      * `$ netstat -atunp`
    * show routing table
      * `$ netstat -nr`
  * Options:
    * -a: all
    * -l: list listen services
    * -p: list pid and program
    * -t: tcp
    * -u: udp
    * -n: don't resolve hostname
    * -c: -c 5 update every 5 second
    * -n: use IP and port number
      * default is hostname and service
    * -r: --route
      * Display the routing table
## ss
  * ss is included in iproute2 package and is the substitute of the netstat. ss is used to dump socket statistics. It shows information similar to netstat. It can display more TCP and state information than other tools. It is a new, incredibly useful and faster (compared to netstat) tool for tracking TCP connections and sockets.
  * Usage:
    * list all services
      * `$ ss -atup`
    * list all tcp socket with port 3306 (mysql)
      * `$ ss -atnp | grep 3306`
      * `$ ss -atrp | grep 3306`  (resolve hostname)

  * Options
    * -a: all
    * -l: list listen services
    * -t: tcp
    * -u: udp
    * -n: don't resolve service name
    * -r: resolve hostname
    * -p: show processes using socket
    * -4: ipv4
    * -6: ipv6
## tcpdump
  * options:
    * -i: 
      * interface
    * -n: 
      * Don't convert addresses (i.e., host addresses, port numbers, etc.) to names.
    * -q:
      * quick output
    * -X:
      * In addition to printing the headers of each packet, print the data of each packet (minus its link level  header)  in  hex
      and ASCII.
    * -XX:
      * In addition to printing the headers of each packet, print the data of each packet, including its link level header, in hex and ASCII.
    * -w file:
      * Write  the raw packets to file rather than parsing and printing them out.  
      * They can later be printed with the -r option.
    * -r file:
      * Read packets from file
    * -c: 
      * count, exit after receiving count packets
    * protocol :
      * tcp, udp, ip, arp, rarp, fddi, icmp
    * src ${condition}
    * dst ${condition}
    * host
      * hostname
    * net
      * IP range  
    * port ${port_number}:
      * specify port number
    * portrange
      * portrange 21-23
    * logical operator:
      * not(!), and(&&), or(||)
    * Example:
      * https://danielmiessler.com/study/tcpdump/#protocol
      * https://colobu.com/2019/07/16/a-tcpdump-tutorial-with-examples/#%E6%A0%B9%E6%8D%AE%E7%BD%91%E6%AE%B5%E8%BF%9B%E8%A1%8C%E6%9F%A5%E6%89%BE

## airmon
## airodump

# Process Monitoring
## ps
  * Usage:
  * `$ps aux|grep ${pattern}`
## top
## htop
## atop
## glace
## lsof
  * list open files

# Service Management
## Sysvinit
  * config:
    * Path
      * `/etc/init.d`
  * Usage:
    * start the service
    * `$ service ${service_name} start`
    * stop the service
      * `$ service ${service_name} stop`
    * reload the service
      * `$ service ${service_name} reload `
    * restart the service
      * `$ service ${service_name} restart`
      * `$ service ${service_name} condrestart`
        * restart the service if it is running.
  * Ref:
    * https://www.ibm.com/developerworks/cn/linux/1407_liuming_init1/index.html?ca=drs-
## Upstart
  * config:
    * path
      * `/etc/init`
    * example:
        `sh
        start on started network-services
        stop on stopping network-services
        respawn
        script
            PROGRAM="/usr/local/bin/uwsgi"
            OPTIONS="--ini /etc/uwsgi.d/uwsgi_postman.ini"
            exec $PROGRAM $OPTIONS
        end script
        `
  * Usage:
    * list
      * `$ initctl list`
    * start the service
      * `$ initctl start ${service_name}`
      * `$ start ${service_name}`
    * stop the service (SIGTEM)
      * `$ initctl stop ${service_name}`
      * `$ stop ${service_name}`
    * reload (SIGHUP)
      * `$ initctl reload ${service_name}`
      * `$ reload ${service_name}`
    * reload
      * `$ initctl reload ${service_name}`
      * `$ reload ${service_name}`
  * Ref:
    * http://upstart.ubuntu.com/cookbook/
    * https://www.ibm.com/developerworks/cn/linux/1407_liuming_init2/index.html?ca=drs-
## Systemd
  * **Systemd is Compatible with Sysvinit**.
  * config
   * path
     * /usr/lib/systemd
  * Type:
    * service, socket, target
  * Usage:
   * List installed unit files
     * `$ systemctl list-unit-files`
   * List socket units currently in memory.
     * `$ systemctl list-sockets`
   * List jobs
     * `$ systemctl list-jobs`
   * List units currently in memory
     * `$ systemctl list-units`
   * List services currently in memory
     * `$ systemctl list-units --type=service --state=active`
     * `$ systemctl list-units --type=service --state=running`
     * `$ systemctl list-units --type=service --state=active,failed`
   * Check the config
     * `$ systemctl cat ${service_name}.service`
   * start the service
     * `$ systemctl start ${service_name}.service`
   * stop the service
     * `$ systemctl stop ${service_name}.service`
   * reload the service
     * `$ systemctl reload ${service_name}.service`
   * restart the service
     * `$ systemctl restart ${service_name}.service`
     * `$ systemctl condrestart  ${service_name}.service`
       * restart the service if it is running.
   * enable the service when start up
     * `$ systemctl enable  ${service_name}.service`
   * disable the service when start up
     * `$ systemctl disable  ${service_name}.service`
  * Ref:
    * https://www.ibm.com/developerworks/cn/linux/1407_liuming_init3/index.html?ca=drs-
    * https://blog.gtwang.org/linux/linux-basic-systemctl-systemd-service-unit-tutorial-examples/
    * http://linux.vbird.org/linux_basic/0560daemons.php#systemctl_cmd
## Systemd Journal
  * Usage:
    * Show the newest entries first
      * `$ journalctl -r -n 20 prometheus.service`
        * -r : reverse, show the latest
        * -n : lines
    * List all values that a specified field takes
      * `$ journalctl -F=field`
    * Show log of the specific unit
      * `$ journalctl -f -u prometheus.service`
        * -f: follow mode
        * -u: unit
    * Output to pretty json format
      * `journalctl -u cron.service -n 1 --no-pager -o json-pretty`
    * Show entries with the specified priority
      * `journalctl -p 3`
      * `journalctl -p err`
      * -p --priority=RANGE
      * 0: emerg
      * 1: alert
      * 2: crit
      * 3: err
      * 4: warning
      * 5: notice
      * 6: info
      * 7: debug

  * Ref:
    * https://linuxtoy.org/archives/systemd-journal.html