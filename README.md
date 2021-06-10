# Pihole Adlist, Whitelist, Blacklist collection
Also some usefull commands and in the future my wireshark vpn configuration

## Adlist
At the moment i am using 56 adlists. All sorted inside adlist.txt.
Together there are more than 2 million entries inside these lists.

![DomainsOnBlocklist](https://gitlab.com/lichs/Pihole/-/raw/master/Image/Domains.png)

### my own blocklists as raw file

https://raw.githubusercontent.com/lichsimo/Pihole/master/blocklist.samsung.txt
https://raw.githubusercontent.com/lichsimo/Pihole/master/blocklist.spotify.txt
https://raw.githubusercontent.com/lichsimo/Pihole/master/blocklist.youtube.txt

## Blacklist
blacklist.txt is my blacklist with Youtube regex domains and some entries for specific programs that I use on multiple clients and don't want to let them talk to their vendor.

## Whitelist
Collection from own experience, many many reddit threats and other github repositories.

# Config
### DoH via cloudflared argo tunnel

### Installing
``` bash
# arm64 architecture (64-bit Raspberry Pi)
wget -O cloudflared https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-arm64
sudo mv cloudflared /usr/local/bin
sudo chmod +x /usr/local/bin/cloudflared
cloudflared -v
```

### Configuration 
Cloudflared will be installed as root<br>
Under proxy-dns-upstream you can define your doh counterpart
``` bash
# create configuration
sudo mkdir /etc/cloudflared/
sudo nano /etc/cloudflared/config.yml

# configuration 
proxy-dns: true
proxy-dns-port: 5053
proxy-dns-upstream:
    #- https://dns.digitale-gesellschaft.ch/dns-query
    - https://1.1.1.1/dns-query #cloudflared

# install service via cloudflared 
sudo cloudflared service install --legacy

# start service
sudo systemctl start cloudflared
sudo systemctl status cloudflared
```
Configure Pi-hole to use the local cloudflared service as the upstream DNS server. Set `Custom 1 (IPv4)` to `127.0.0.1#5053` - don't forget to hit Return or click on `Save`

### Test
``` bash
dig @127.0.0.1 -p 5053 www.gitlab.com
```

### Update
``` bash
cloudflared update
```

### TODO
- Autoupdate cloudflared

# Catch  WPAD Request76
### wpad.fritz.box
More informations (in german): https://technikbench.de/pi-hole-wpad-deaktivieren/
### Step 1: Direct all wpad domains to the Pi-Hole

First we tell the Pi-Hole that it is responsible for all possible wpad addresses. Just add the following line to "/etc/hosts" and to "/etc/pihole/lan.list":
``` 
192.168.0.4 wpad.fritz.box wpad.box wpad.com wpad.de wpad
```
This ensures that all wpad domains are only resolved locally. Now we have to make sure that the Pi-Hole delivers a wpad.dat.

### Step 2: Create another VirtualHost
The Pi-Hole uses the lighttpd webserver. We can configure it to deliver a wpad.dat. To do this, we add the following lines to the configuration file "/etc/lighttpd/external.conf":
```
$HTTP["host"] =~ "wpad" {
server.document.root = "/var/www/wpad" {
    mimetype.assign = (
    ".dat" => "application/x-ns-proxy-autoconfig",
    ".pac" => "application/x-ns-proxy-autoconfig"
    )
}
```
### Step 3: Create wpad.dat
We navigate to the directory /var/www/wpad and create the file wpad.dat there. The content is simple and only indicates that there is no proxy server:
```
function FindProxyForURL(url, host) {
return "DIRECT";
}
```
We create a second file named proxy.pac and insert the same lines again.

### Step 4: Restart lighttpd 
```
sudo service lighttpd restart
```
That's it. The proxy configuration on the network is now safe.

# Usefull Tools
[PADD](https://github.com/pi-hole/padd)

[Windows Terminal](https://github.com/microsoft/terminal)

![Windows Terminal Profiles](https://gitlab.com/lichs/Pihole/-/raw/master/Image/WTProfiles.png)

# Commands
### Sort adlist
``` powershell
Get-Content .\adlist.txt | Sort | Out-File .\adlist.txt
```
### Convert Pihole Domain json to txt
With `Format-Table` (FT) and the `-hidetableheaders` parameter i bypass the default PowerShell header output. With (... | Out-String).trim() i delete the empty limes.

``` powershell
(Get-Content .\blacklist.exact.json | ConvertFrom-Json | Select domain -Unique | Sort domain | FT -HideTableHeaders | Out-String).trim() | Out-File .\blacklist.exact.txt

# explanation
# 1. Import the json file
$json = Get-Content .\list.json | ConvertFrom-Json
# 2. only select unique domains
$domains = $json | Select domain -Unique
# 3. sort the domains
$domains = $domains | Sort
# 4. format es table and hide the "header"
$table = $domains | FT -HideTableHeaders
# 5. remove empty lines
$outString = ($table | Out-String).trim()
# 6. write file
$outString | Out-File .\list.txt
```

### Convert Pihole adlist json to txt
``` powershell
(Get-Content .\adlist.json | ConvertFrom-Json | Select address -Unique | Sort address | FT -HideTableHeaders | Out-String).trim() | Out-File .\adlist.txt
```

### Select unique entries from a list
``` powershell
(Get-Content .\unsorted_not_unique_list.txt) | Select -Unique | Sort | Out-File .\sorted_unique_list.txt
```

### remove all adlist entries from gravity.db
``` bash
sudo sqlite3 /etc/pihole/gravity.db "DELETE FROM adlist;"
```