# Pihole Adlist, Whitelist, Blacklist collection
## Also usefull commands 


# POWERSHELL
## Convert Pihole Backup json to txt
### whitelist.exact
$json = (Get-Content .\whitelist.exact.json) | Out-String | ConvertFrom-Json
$json | Select domain | Sort | FT -hidetableheaders | Out-File .\whitelist.exact.txt
### whitelist.regex
$json = (Get-Content .\whitelist.regex.json) | Out-String | ConvertFrom-Json
$json | Select domain | Sort | FT -hidetableheaders | Out-File .\whitelist.regex.txt

# remove all adlist entries
sudo sqlite3 /etc/pihole/gravity.db "DELETE FROM adlist;"
