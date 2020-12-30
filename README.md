# Pihole Adlist, Whitelist, Blacklist collection
also some usefull commands


## Powershell
### Convert Pihole Domain json to txt
**whitelist.exact**
$json = (Get-Content .\whitelist.exact.json) | Out-String | ConvertFrom-Json
$json | Select domain | Sort | FT -hidetableheaders | Out-File .\whitelist.exact.txt
**whitelist.regex**
$json = (Get-Content .\whitelist.regex.json) | Out-String | ConvertFrom-Json
$json | Select domain | Sort | FT -hidetableheaders | Out-File .\whitelist.regex.txt

### Convert Pihole adlist json to txt
$json = (Get-Content .\adlist.json) | Out-String | ConvertFrom-Json
$json | Select address | Sort | FT -hidetableheaders | Out-File .\adlist.txt

## Pihole / Linux
### remove all adlist entries from gravity.db
sudo sqlite3 /etc/pihole/gravity.db "DELETE FROM adlist;"
