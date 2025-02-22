# Running netboot.xyz with OpenWRT
*A brief guideline to run netboot.xyz with OpenWRT.*

## Set up OpenWRT
```bash
# /etc/dnsmasq.conf
dhcp-match=set:efi64,60,PXEClient:Arch:00007
dhcp-boot=tag:efi64,netboot.xyz.efi,,YOURSERVERIP

# /etc/config/dhcpd.conf
config boot linux
        option filename 'pxelinux.0'
        option serveraddress 'YOURSERVERIP'
        option servername 'netboot.xyz'
```


## Setup netboot.xyz docker

`docker-compose.yml` example:
```yaml
---
services:
  netbootxyz:
    image: linuxserver/netbootxyz:latest
    container_name: netbootxyz
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - MENU_VERSION=2.0.84 		# optional
      - PORT_RANGE=30000:30010 	# optional
      - SUBFOLDER=/ 						# optional
      - NGINX_PORT=80 					# optional
      - WEB_APP_PORT=3000 			# optional
    volumes:
      - ./config:/config
      - /path/to/assets:/assets # optional
    ports:
      - 3000:3000 # netboot.xyz web UI
      - 69:69/udp # TFTP
      - 8080:80 	# optional, asset http server
    restart: unless-stopped
```



### References
* https://hub.docker.com/r/linuxserver/netbootxyz
* https://openwrt.org/docs/guide-user/services/tftp.pxe-server
* https://gist.github.com/rikka0w0/4c4caac493ce682cbed9ba3e928693d5
* https://hub.docker.com/r/linuxserver/netbox
