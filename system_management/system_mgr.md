
## Service Management Tool

### Sysvinit
  * config file:
    * /etc/init.d
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

### Upstart
 * config file:
   * /etc/init/
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

### Systemd
 * **Systemd is Compatible with Sysvinit**.

 * config file
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

### Systemd Journal
 * Ref:
   * https://linuxtoy.org/archives/systemd-journal.html
 * Usage:
   * ```$ journalctl -f -u prometheus.service```
     * -f: follow mode
     * -u: unit
   * ```$ journalctl -r -n 20 prometheus.service```
     * -r : reverse, show the latest
     * -n : lines
   * ```journalctl -u cron.service -n 1 --no-pager -o json-pretty```
     * output to pretty json format