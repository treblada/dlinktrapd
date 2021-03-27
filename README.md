This daemon is designed to receive and write syslog traps for the
SmartConsole which is used to control some models of
D-Link switches. Some of these models (e.g. DES-1100) have no other
notification functions (i.e. no syslog or SNMP support).

**WARNING:** not to be confused with SNMP-traps!

Dependencies: PCRE library (http://www.pcre.org/)

# Installation and running under FreeBSD:
1. make all install (root privileges required)
1. add the variable `dlinktrapd_enable="YES"` to /etc/rc.conf,
   If necessary, add `dlinktrapd_flags="YES"`.

If pcre is suddenly not installed with the system, install it from the devel/pcre port.

Compiling under linux-base systems is done with a command like:

    gcc -Wall -O1 -lpcre -o dlinktrapd dlinktrapd.c

You may need to specify include and lib paths explicitly:

    gcc -Wall -O1 -I /usr/include -L /usr/lib -lpcre -o dlinktrapd dlinktrapd.c

pcre is put in place the way used in the distribution, e.g. for
Debian "squeeze":

    apt-get install libpcre3 libpcre3-dev

# Command line switches:

* -d: debug mode, does not go into daemon mode if specified twice,
    additionally shows package hex-dump.
* -i <IP>: listen address, by default listens on all interfaces with IP address.
* -P <port>: listen port (default 64514)
* -p <file>: pid file (default /var/run/dlinktrapd.pid)
* -s: enable recording the switch port status when receiving "port up"/"port down" traps
* -f </some/dir/>: directory to write the port state file. The file name is the MAC of the device.

# Bugs
The message that is received inside the trap is not checked for correctness and is sent
to syslog as it came in. Since there is no trace of sender authentication
is detected in the protocol, someone pretending to be a switch may send syslog
syslog message up to 468 bytes long.


# Message format
    switch_brand space(s) (message_code)message

## Message types
* (1001) System bootup
* (1002) WEB authenticate error from remote IP: xxx.xxx.xxx.xxx
* (3003) Port *X* copper link up
* (3004) Port *X* copper link down
* (5001) Firmware upgraded success
* (5002) Firmware upgraded failure
* (5005) Wrong file checksum causes firmware upgrade failure.
