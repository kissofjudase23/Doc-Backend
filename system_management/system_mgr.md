- [Text Manipulation](#text-manipulation)
- [Network](#network)
- [Process Monitoring](#process-monitoring)
- [Service Management](#service-management)

# Text Manipulation
  * find:
    * usage:
      * ```$ find -name "*.sh" -type f maxdepth 5| xargs rm -f```
    * options:
      * -type:
        * file type
      * -name:
        * True if the last component of the pathname being examined matches pattern
      * -iname: case insensitive of version of name
      * maxdepth
      * mindepth
  * ag:
    * usage:
    * options:
      * depth
        * -1 is unlimited
  * grep (egrep)
    * Usage
      * ```$grep -inrH ".*darwin" *.sh ```
      * ```$grep ```
    * options:
      * r:
        * recursive
      * -i
        * case insensitive
      * -n:
        * line number
      * -h:
        * print filename headers
      * --include
        * specify file pattern
  * fgrep
  * aws
  * sed
  * sort
  * uniq
  * cat
  * cut
  * wc
  * fmt
  * echo

# Network
  * ping
  * dig
    * Usage:
      * NS
        * find Name server of the from.ers.trendmicro.com
        * ```$dig NS from.ers.trendmicro.com```
      * A (IPv4)
        * ```$dig A hashsrv.ers.trendmicro.com```
      * AAAA (IPv6)
        * ```$dig AAAA hashsrv.ers.trendmicro.com```
    * Ref:
      * http://dns-learning.twnic.net.tw/bind/intro6.html
  * tcpdump
  * traceroute
    * each endpoint tests 3 times.
  * netstat
    * Usage
      * list listening services
        * ```$ netstat -tulnp```
      * list all services
        * ```$ netstat -atunp```
    * options:
      * -a: all
      * -l: list listen service
      * -p: list pid and program
      * -t: tcp
      * -u: udp
      * -c: -c 5 update every 5 second
      * -n: use IP and port number
        * default is hostname and service
  * iptables
  * airmon
  * airodump

# Process Monitoring
 * ps
   * Usage:
     * ```$ps aux|grep ${pattern}```
 * top
 * htop
 * atop
 * glace
 * lsof
   * list open files

# Service Management
  * Sysvinit
    * config:
      * Path
        * ```/etc/init.d```
    * Usage:
      * start the service
      * ```$ service ${service_name} start```
      * stop the service
        * ```$ service ${service_name} stop```
      * reload the service
        * ```$ service ${service_name} reload ```
      * restart the service
        * ```$ service ${service_name} restart```
        * ```$ service ${service_name} condrestart```
          * restart the service if it is running.
    * Ref:
      * https://www.ibm.com/developerworks/cn/linux/1407_liuming_init1/index.html?ca=drs-
  * Upstart
    * config:
      * path
        * ```/etc/init```
      * example:
          ```sh
          start on started network-services
          stop on stopping network-services
          respawn
          script
              PROGRAM="/usr/local/bin/uwsgi"
              OPTIONS="--ini /etc/uwsgi.d/uwsgi_postman.ini"
              exec $PROGRAM $OPTIONS
          end script
          ```
    * Usage:
      * list
        * ```$ initctl list```
      * start the service
        * ```$ initctl start ${service_name}```
        * ```$ start ${service_name}```
      * stop the service (SIGTEM)
        * ```$ initctl stop ${service_name}```
        * ```$ stop ${service_name}```
      * reload (SIGHUP)
        * ```$ initctl reload ${service_name}```
        * ```$ reload ${service_name}```
      * reload
        * ```$ initctl reload ${service_name}```
        * ```$ reload ${service_name}```
    * Ref:
      * http://upstart.ubuntu.com/cookbook/
      * https://www.ibm.com/developerworks/cn/linux/1407_liuming_init2/index.html?ca=drs-
  * Systemd
     * **Systemd is Compatible with Sysvinit**.
     * config
       * path
         * /usr/lib/systemd
     * Usage:
       * start the service
         * ```$ systemctl start ${service_name}.service```
       * stop the service
         * ```$ systemctl stop ${service_name}.service```
       * reload the service
         * ```$ systemctl reload ${service_name}.service```
       * restart the service
         * ```$ systemctl restart ${service_name}.service```
         * ```$ systemctl condrestart  ${service_name}.service```
           * restart the service if it is running.
       * enable the service when start up
         * ```$ systemctl enable  ${service_name}.service```
       * disable the service when start up
         * ```$ systemctl disable  ${service_name}.service```
     * Ref:
       * https://www.ibm.com/developerworks/cn/linux/1407_liuming_init3/index.html?ca=drs-
       * https://blog.gtwang.org/linux/linux-basic-systemctl-systemd-service-unit-tutorial-examples/
  * Systemd Journal
    * Usage:
      * ```$ journalctl -f -u prometheus.service```
       * -f: follow mode
       * -u: unit
      * ```$ journalctl -r -n 20 prometheus.service```
       * -r : reverse, show the latest
       * -n : lines
      * ```journalctl -u cron.service -n 1 --no-pager -o json-pretty```
       * output to pretty json format
    * Ref:
      * https://linuxtoy.org/archives/systemd-journal.html