# GraySquid
A Graylog squid content pack.

This content pack will launch a SYSLOG_TCP input on port 19302 and will parse your squid logs to be ingested and processed into GrayLog.

The following fields are extracted from the standard squid log.

* Duration
* Client_Address
* Method
* Result_Code
* Status_Code
* URI
* URI_Protocol (optional)
* URI_User (optional)
* URI_Host
* URI_Port (optional)
* URI_Path (optional)
* URI_Fragment (optional)
* User
* Peer_Status
* Peer_Host
* Content-Type

Example Squid log
~~~~
1478495112.396    357 10.1.2.3 TCP_MISS/200 555 HEAD http://ds.download.windowsupdate.com/v11/2/windowsupdate/redir/v6-wuredir.cab? - FIRSTUP_PARENT/241.242.243.245 application/octet-stream
~~~~

## Installation

Open GrayLog interface System / Inputs -> Content Packs -> Import Content Pack.

Activate the content pack.

## Usage

On linux with rsyslogd v7.6 and newer 

~~~~
rsyslogd -v
rsyslogd 8.16.0
~~~~

~~~~
nano /etc/rsyslog.d/60-squid.conf
~~~~
change 10.x.x.x in the below config to your GrayLog server ip.
~~~~
# Load Modules
module(load="imfile")

# rsyslog Input Modules
input(type="imfile"
         File="/var/log/squid/access.log"
         Tag="squid-access"
         Severity="info"
         Facility="local7"
         ruleset="SquidForward")

# rsyslog RuleSets
ruleset(name="SquidForward") {
        action(type="omfwd"
        Target="10.x.x.x"
        Port="19302"
        Protocol="tcp"
        template="RSYSLOG_SyslogProtocol23Format")
}
~~~~
Add graylog to have permissions to the proxy logs. (probbly not how to do it)
~~~~
usermod -a -G proxy syslog
~~~~
Restart rsyslog
~~~~
/etc/init.d/rsyslog restart
~~~~

