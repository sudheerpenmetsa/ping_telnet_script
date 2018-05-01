# My project's README

### Ref url : http://www.techpaste.com/2012/01/bash-script-test-ping-telnet-hosts-ports-nix/ 
##Ref url(for diff scenario) :http://www.techpaste.com/2012/01/bash-script-check-application-url-status-curl-linux/
Few times in administrative jobs we need to make sure some services or ports are always open and listening to monitor the same we can use below script can be used to ping and telnet to different hosts running on different ports. This bash script to ping multiple hosts can be used also to  telnet multiple hosts.


ADVERTISEMENT

Things required:

Host_PortFile.txt – This file contains all the host names and port numbers needs to be pinged and telnet. Put your host names and ports like below and save it.

techpaste.com:80
yahoo.com:80
google.com:443
gmail.com:443
noerror.com:81

 

 Below is the bash script:

 #!/bin/bash
 #bash to check ping and telnet status.
 #set -x;
 #
 #clear
 SetParam() {
 export URLFILE="Host_PortFile.txt"
 export TIME=`date +%d-%m-%Y_%H.%M.%S`
 export port=80
 export STATUS_UP=`echo -e "\E[32m[ RUNNING ]\E[0m"`
 export STATUS_DOWN=`echo -e "\E[31m[ DOWN ]\E[0m"`
 export MAIL_TO="admin(at)techpaste(dot)com"
 export SHELL_LOG="`basename $0`.log"
 }

 Ping_Hosts() {

 SetParam
 cat $URLFILE | while read next
 do

 server=`echo $next | cut -d : -f1`

 ping -i 2 -c 6 $server > /dev/null 2>&1

 if [ $? -eq 0 ] ; then
 echo "$TIME : Status Of Host $server = $STATUS_UP";
 else
 echo "$TIME : Status Of Host $server = $STATUS_DOWN";
 echo "$TIME : Status Of Host $server = $STATUS_DOWN" | mailx -s "$server Host DOWN!!!" $MAIL_TO

 fi
 done;
 }

 Telnet_Status() {

 SetParam

 cat $URLFILE | while read next
 do

 server=`echo $next | cut -d : -f1`
 port=`echo $next | awk -F":" '{print $2}'`

 TELNETCOUNT=`sleep 5 | telnet $server $port | grep -v "Connection refused" | grep "Connected to" | grep -v grep | wc -l`

 if [ $TELNETCOUNT -eq 1 ] ; then

 echo -e "$TIME : Port $port of URL http://$server:$port/ is \E[32m[ OPEN ]\E[0m";
 else
 echo -e "$TIME : Port $port of URL http://$server:$port/ is \E[31m[ NOT OPEN ]\E[0m";
 echo -e "$TIME : Port $port of URL http://$server:$port/ is NOT OPEN" | mailx -s "Port $port of URL $server:$port/ is DOWN!!!" $MAIL_TO;

 fi
 done;
 }
 Main() {
 Ping_Hosts
 Telnet_Status
 }
 SetParam
 Main | tee -a $SHELL_LOG

 Above script will create a log with the script name like if the shell script is stored as shell.sh then the log name for this script will be shell.log, once you run the script. A sample output is given below

 ping and telnet script sample output

  

   

   Thanks to Fr3dY for a new script which can also be used for the same tasks like ping, telnet and curl. 
