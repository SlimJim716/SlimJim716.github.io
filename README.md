# PiVPN Static Status Page

Monitor your PiVPN connections with a simple static HTML page

![PiVPN-Simple-Status-Example](https://user-images.githubusercontent.com/80802641/118336219-3b44bf80-b4df-11eb-9a4d-94c0aa9efdff.jpg)


### Overview

This script allows you to easily monitor your PiVPN connections by visiting a static web page, which is easier than SSHing into a machine to view clients.<br />
**NOTE**: This is *NOT* a GUI for PiVPN. This is purely a static HTML page for viewing connected clients. You will *NOT* be able to create, modify, disable, or delete any VPN profiles. That still must be done through the host machine.

### Prerequisites

* PiVPN - see https://www.pivpn.io/ for more info
* Web Server of your choice - There are many options avaliable, i.e. NGINX, Apache, Lighttpd, php built in server. Anything is fine.
* (Optional) figlet - For a nice banner at the top of your page. can be installed with: `sudo apt install figlet`

### Installing

1. Move to your directory of choice, clone the repo, and cd into it or download the `generate` file.

```
git clone https://github.com/SlimJim716/PiVPN-Static-Status
```
```
cd PiVPN-Static-Status
```

2. Open `generate` with an editor of your choice, like `nano`, or `vim`.

```
nano generate
```

3. Edit the `CONFIG AREA` section to match your installation. Save the file and exit when done

```
#-------------------------------------------------------
#                   CONFIG AREA
#-------------------------------------------------------
wg_enabled=true #set to true if you have Wireguard enabled in PiVPN, set to false otherwise
ovpn_enabled=true #set to true if you have OpenVPN enabled in PiVPN, set to false otherwise
banner_name="PiVPN Status" #(OPTIONAL) change the name of the top ascii banner. Requires figlet.
#-------------------------------------------------------
#-------------------------------------------------------
```

4. Make `generate` executable
```
chmod 744 generate
```

5. Open crontab

```
crontab -e
```

6. Add the following line to cron so the script will update periodically. This will refresh the HTML every minute - change the timing as desired. Be sure to change the placeholder paths below.

```
* * * * * /path/to/script/generate > /path/to/output/index.html
```
6a. Optional. Since this is most likely deployed on a Raspberry Pi, instead of writing to disk, we can write to a tmpfs partition instead to minimize disk usage. We can change the line above to this.

```
* * * * * mkdir -p /dev/shm/pivpnss && /path/to/script/generate > /dev/shm/pivpnss/index.html
```

7. Serve the page with your favorite web server. This step is up to you. Below is a basic config example for NGINX using a subdomain for the page.

```
server {
        listen 80;
        server_name pivpnstatus.example.com;
        location / {
                root /path/to/PiVPN-Static-Status;
        }
        
}
```

You could also use PHP's built in web server while inside the PiVPN-Static-Status directory.
```
php -S <YOURIP>:8080 index.html
```
