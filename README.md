# Pihole Adlist, Whitelist, Blacklist collection
also some usefull commands

### my own blacklist as raw file
https://raw.githubusercontent.com/lichsimo/Pihole/master/blocklist.samsung.txt
https://raw.githubusercontent.com/lichsimo/Pihole/master/blocklist.spotify.txt
https://raw.githubusercontent.com/lichsimo/Pihole/master/blocklist.youtube.txt

blacklist.txt is the "real" Blacklist with youtube regex and some entries for specific programs

Blacklist 2020.30.12
![Blacklist-Image](https://raw.githubusercontent.com/lichsimo/Pihole/master/Image/Blacklist.png

## Powershell
### Sort txt
```console
(Get-Content .\adlist.txt) | Sort | Out-File .\adlist.txt
```
### Convert Pihole Domain json to txt
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