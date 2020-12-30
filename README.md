# Pihole Adlist, Whitelist, Blacklist collection
also some usefull commands and in the future my wireshark vpn configuration

## Adlist
At the moment i am using 56 adlists. All sorted inside adlist.txt.
Together there are more than 2.5 million entries
Domains on Blocklist 2020.30.12

![DomainsOnBlocklist](https://raw.githubusercontent.com/lichsimo/Pihole/master/Image/DomainsOnBlocklist.png)

### my own blocklists as raw file

https://raw.githubusercontent.com/lichsimo/Pihole/master/blocklist.samsung.txt
https://raw.githubusercontent.com/lichsimo/Pihole/master/blocklist.spotify.txt
https://raw.githubusercontent.com/lichsimo/Pihole/master/blocklist.youtube.txt

## Blacklist
blacklist.txt is the "real" blacklist with Youtube regex domains and some entries for specific programs that I use on multiple clients and don't want to let them talk to their vendor.

Blacklist 2020.30.12
![Blacklist-Image](https://raw.githubusercontent.com/lichsimo/Pihole/master/Image/Blacklist.png)

## Powershell
### Sort txt
```console
(Get-Content .\adlist.txt) | Sort | Out-File .\adlist.txt
```
### Convert Pihole Domain json to txt
With `Format-Table` (FT) and the `-hidetableheaders` parameter i bypass the default PowerShell header output.

**whitelist.exact**

```console
$json = (Get-Content .\whitelist.exact.json) | Out-String | ConvertFrom-Json
$json | Select domain | Sort | FT -hidetableheaders | Out-File .\whitelist.exact.txt
```

**whitelist.regex**
```console
$json = (Get-Content .\whitelist.regex.json) | Out-String | ConvertFrom-Json
$json | Select domain | Sort | FT -hidetableheaders | Out-File .\whitelist.regex.txt
```

### Convert Pihole adlist json to txt
```console
$json = (Get-Content .\adlist.json) | Out-String | ConvertFrom-Json
$json | Select address | Sort | FT -hidetableheaders | Out-File .\adlist.txt
```

## Pihole | Linux
### remove all adlist entries from gravity.db
```console
sudo sqlite3 /etc/pihole/gravity.db "DELETE FROM adlist;"
```