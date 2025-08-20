# ///////////////////// GREP /////////////////////

        grep [OPTION...] PATTERNS [FILE...]
        rep [OPTION...] -e PATTERNS ... [FILE...]
        grep [OPTION...] -f PATTERN_FILE ... [FILE...]
\========================================================


```bash
tmsh list sys snmp allowed-addresses | grep -o -P '(?<=allowed-addresses { ).\*(?=})'
```



• -P, --perl-regexp
• -o, --only-matching

--- without grep
tmsh list sys snmp allowed-addresses
sys snmp {
allowed-addresses { 10.26.31.0/255.255.255.0 10.26.32.0/255.255.254.0 10.64.64.253 }
}

-- With grep addition
10.26.31.0/255.255.255.0 10.26.32.0/255.255.254.0 10.64.64.253
\==========================================================

clish -c "show configuration" | grep "expert-password-hash" | awk '{print $3}'

pull management interface - Cisco
grep -B 5 %%HOST%% /data/tmp/%%DEVICE\_NAME%%\_running-config.txt | grep nameif | awk '{print $2}'


grep -vFx -f test_acl53.txt /data/jail/data/file_repository/acl53.txt
* -v -- inverse match
* -F -- fixed-strings - Interpret PATTERNS as fixed strings, not regular expressions.
* -x -- line-regexp - Select only those matches that exactly match the whole line.  For a regular expression pattern,
        this is like parenthesizing the pattern and then surrounding it with ^ and $.
* -f -- file - Obtain  patterns  from  FILE,  one  per line.
** Will display everything that does not match (compare first to second.)

grep -A 5 -E '^(interface )' 07848-9800-1-l-h.csc.na.mgmt.medcity.net_running-config.txt | grep -B 3 "10.114.16.5" | grep 'interface' | awk '{print $2}'

# ///////////////////// SED /////////////////////

* Search and Replace
```
sed -i 's/SEARCH_REGEX/REPLACEMENT/g' INPUTFILE
```

* Remove spaces/empty lines at end of file.
```
sed 's/^[ \t]*//'
```

```
sed -i '1d'  servergrouptoremove.txt
sed -i '1d'  missingupdated.txt
```
* -i is to edit file in place
* '1d' is delete line 1

```
sed '$d'
```
* - remove last line
* - $   Match the last line.
* - d   Delete pattern space.  Start next cycle.
sed -n -e '/access-list standard 55/,/[^\s+\\]/p'  07848-9800-1-l-h.csc.na.mgmt.medcity.net_running-config.txt | sed '$d'

```
sed -ne '/access-list standard 12/,/^\w/p' 07848-9800-1-l-h.csc.na.mgmt.medcity.net_running-config.txt | sed '1d; $d'
```
* find string that starts with 'access-list standard 12' and ends with a word (^\w).
* Then removed first and last line

```
sed -ne '/^interface /,/\!/p' 07848-9800-1-l-h.csc.na.mgmt.medcity.net_running-config.txt | tac | sed -ne '/ip address 10.114.16.5/,/^interface /p' | grep 'interface' | awk '{print $2}'
```
* tac command is opposite of cat. concatenate and print files in reverse


```
sed -ne '/access-list standard 53/,/^\w/p'  99926-3850-4-q-h.xdc.na.mgmt.medcity.net_running-config.txt | sed '$d; 1d; s/^[ \t]*//'
```
* The sed '$d; 1d; s/^[ \t]*//'  on the end will delete the last line, the first line, and space on front of line.



clish -c "show users" | awk 'NR > 1 {print $1}' | sed 's/voyence\\|algosec\\|chkptmonitor\\|admin\\|ciscoworks\\|\_lldpd\\|airwave\\|cp\_ender\\|corpsvcpatrolexp\\|monitor//'

```
nslookup %%HOST%% | awk 'NR==4' | awk -F'=' '{print $2}' | awk '{sub(/\\.$/, ""); print}' | sed 's/^\[\[:space:\]\]//;s/\[\[:space:\]\]\*$//'
```

clish -c "show users" | awk 'NR > 1 {print $1}' | sed 's/voyence\\|algosec\\|chkptmonitor\\|admin\\|ciscoworks\\|\_lldpd\\|airwave\\|cp\_ender\\|corpsvcpatrolexp\\|monitor//'

```
sed -e 's/^[[:space:]]*//'   /////// removes leading spaces from string.
sed -e 's/^\[\[:space:\]\]//;s/\[\[:space:\]\]\*$//'   ////// Remove both leading and trailing spaces.
```

# ///////////////////// AWK /////////////////////

nslookup %%HOST%% | awk '{print substr($4, 1, length($4)-1)}'

tmsh list sys management-route default one-line | awk '{print $8}'
```
nslookup %%HOST%% | awk 'NR==4' | awk -F'=' '{print $2}' | awk '{sub(/\\.$/, ""); print}' | sed 's/^\[\[:space:\]\]//;s/\[\[:space:\]\]\*$//'
```
cat %%CP\_CONFIG\_FILE%% | awk '/DNS-STANDARD/{if (flag) exit; flag=1; next} flag'
```
awk 'NR==FNR {a\[$0\]; next} {if ($0 in a) print $0 >> "match.txt"; else print $0 >> "unmatch.txt"}' dns\_config.txt cp\_standard.txt

nslookup %%HOST%% | awk 'NR==4' | awk -F'=' '{print $2}' | awk '{sub(/\\.$/, ""); print}'

echo "%%HOSTNAME%%" | awk -F'.mgmt.medcity.net' '{split($1, a, "."); print a\[length(a)\]}'

awk 'NR==FNR {a\[$0\]; next} {if ($0 in a) print $0 >> "match.txt"; else print $0 >> "unmatch.txt"}' local\_custom\_snmptraps.txt %%snmpcompare%%

echo %%f5ntp%% | awk '{ print $1 }'

cat sysinfo | grep "Product Name" | awk '{ print substr($0, index($0,$3)) }'

awk '/^interface /,/!/ { block = block $0 RS } /!/ { if (block ~ /ip address [0-9]+\.[0-9]+\.[0-9]+\.[0-9]+/ && block !~ /shutdown/) print block; block = "" }' 07848-9800-1-l-h.csc.na.mgmt.medcity.net_running-config.txt | sed -n 1p | awk '{print $2}'
//// output is management interface of device.
```


# ///////////////////// COMM /////////////////////
```
comm -3 <(cut -d ' '  -f4 test_acl53.txt | sort) <(cut -d ' '  -f4 /data/jail/data/file_repository/acl53.txt | sort)
```
* Using comm to compare 2 files.
* Use cut with delimeter of a space to only review fourth field in each file
* sorting output so comm and compare
* output is the difference between
* -3 is suppress lines that appear in both files


# ///////////////////// MISC /////////////////////
```
if (($(echo "%%r1%% < %%r2%%" | bc -l))) && (($(echo "%%r1%% < %%r3%%" | bc -l) )); then echo %%site.xrdcsyslog%%; elif (($(echo "%%r2%% < %%r1%%" | bc -l))) && (($(echo "%%r2%% < %%r3%%" | bc -l) )); then echo %%site.frdcsyslog%%; else echo %%site.trdcsyslog%%; fi

ping -c 4 %%site.trdcsyslog%% | tail -1 | awk '{print $4}' | cut -d '/' -f2

MATCH=0; for a in %%subnetlist%%; do if \[ $a = "10.26.31.0/255.255.255.0" \] || \[ $a = "10.26.32.0/255.255.254.0" \]; then let MATCH++; fi; done; echo "$MATCH"

echo "D1 is greater than D2. Will use FRDC IP %%FRDCRadius%% as primary"; clish -c "add aaa radius-servers priority 1 host %%FRDCRadius%% port 1812 secret fYiqjiaw timeout 5"; clish -c "add aaa radius-servers priority 2 host %%XRDCRadius%% port 1812 secret fYiqjiaw timeout 5" -s
```
