# GREP

clish -c "show configuration" | grep log-remote-address | awk '{print $4}'

\========================================================
tmsh list sys snmp allowed-addresses | grep -o -P '(?<=allowed-addresses { ).\*(?=})'
• -P, --perl-regexp
• -o, --only-matching
\--- without grep
\[voyence@pzdci5800inth01:Active:Standalone\] ~ # tmsh list sys snmp allowed-addresses
sys snmp {
allowed-addresses { 10.26.31.0/255.255.255.0 10.26.32.0/255.255.254.0 10.64.64.253 }
}

\--- With grep addition
10.26.31.0/255.255.255.0 10.26.32.0/255.255.254.0 10.64.64.253
\==========================================================

clish -c "show configuration" | grep "expert-password-hash" | awk '{print $3}'

pull management interface - Cisco
grep -B 5 %%HOST%% /data/tmp/%%DEVICE\_NAME%%\_running-config.txt | grep nameif | awk '{print $2}'

# SED

clish -c "show users" | awk 'NR > 1 {print $1}' | sed 's/voyence\\|algosec\\|chkptmonitor\\|admin\\|ciscoworks\\|\_lldpd\\|airwave\\|cp\_ender\\|corpsvcpatrolexp\\|monitor//'

nslookup %%HOST%% | awk 'NR==4' | awk -F'=' '{print $2}' | awk '{sub(/\\.$/, ""); print}' | sed 's/^\[\[:space:\]\]//;s/\[\[:space:\]\]\*$//'

clish -c "show users" | awk 'NR > 1 {print $1}' | sed 's/voyence\\|algosec\\|chkptmonitor\\|admin\\|ciscoworks\\|\_lldpd\\|airwave\\|cp\_ender\\|corpsvcpatrolexp\\|monitor//'

# AWK

nslookup %%HOST%% | awk '{print substr($4, 1, length($4)-1)}'

tmsh list sys management-route default one-line | awk '{print $8}'

nslookup %%HOST%% | awk 'NR==4' | awk -F'=' '{print $2}' | awk '{sub(/\\.$/, ""); print}' | sed 's/^\[\[:space:\]\]//;s/\[\[:space:\]\]\*$//'

cat %%CP\_CONFIG\_FILE%% | awk '/DNS-STANDARD/{if (flag) exit; flag=1; next} flag'

awk 'NR==FNR {a\[$0\]; next} {if ($0 in a) print $0 >> "match.txt"; else print $0 >> "unmatch.txt"}' dns\_config.txt cp\_standard.txt

nslookup %%HOST%% | awk 'NR==4' | awk -F'=' '{print $2}' | awk '{sub(/\\.$/, ""); print}'

echo "%%HOSTNAME%%" | awk -F'\\\\.mgmt.medcity.net' '{split($1, a, "."); print a\[length(a)\]}'

awk 'NR==FNR {a\[$0\]; next} {if ($0 in a) print $0 >> "match.txt"; else print $0 >> "unmatch.txt"}' local\_custom\_snmptraps.txt %%snmpcompare%%

echo %%f5ntp%% | awk '{ print $1 }'

cat sysinfo | grep "Product Name" | awk '{ print substr($0, index($0,$3)) }'

# MISC

if (($(echo "%%r1%% < %%r2%%" | bc -l))) && (($(echo "%%r1%% < %%r3%%" | bc -l) )); then echo %%site.xrdcsyslog%%; elif (($(echo "%%r2%% < %%r1%%" | bc -l))) && (($(echo "%%r2%% < %%r3%%" | bc -l) )); then echo %%site.frdcsyslog%%; else echo %%site.trdcsyslog%%; fi

ping -c 4 %%site.trdcsyslog%% | tail -1 | awk '{print $4}' | cut -d '/' -f2

MATCH=0; for a in %%subnetlist%%; do if \[ $a = "10.26.31.0/255.255.255.0" \] || \[ $a = "10.26.32.0/255.255.254.0" \]; then let MATCH++; fi; done; echo "$MATCH"

echo "D1 is greater than D2. Will use FRDC IP %%FRDCRadius%% as primary"; clish -c "add aaa radius-servers priority 1 host %%FRDCRadius%% port 1812 secret fYiqjiaw timeout 5"; clish -c "add aaa radius-servers priority 2 host %%XRDCRadius%% port 1812 secret fYiqjiaw timeout 5" -s