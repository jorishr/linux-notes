# Add Tor support on existing Nginx server
## Install Tor
The Tor project does *not* recommend the use of the package in the standard apt repository. Add the recommended Debian repo: [Debian repo install](https://support.torproject.org/apt/tor-deb-repo/)
## Tor config file
The standard configuration file is: `/etc/tor/torrc` and follow the instructions for the [onion services setup](https://community.torproject.org/onion-services/setup/)
```bash
# enable logs
Log notice file /var/log/tor/notices.log

HiddenServiceDir /var/lib/tor/<my_website>/
HiddenServicePort 80 127.0.0.1:8081

# The port may cause conflict with existing services on the nginx server
# check via the `ss -lnt` command
```
Save the settings and restart the tor service: `systemctl restart tor`. Check the log and/or service status for errors.

The onion url and public-private key pair is now available in the `/var/lib/` folder. Get the onion address: `cat /var/lib/tor/<my-site>/hostname`. 
## Nginx settings
The main configuration file may need one tweak to account for the long onion address: increase the `server_names_hash_bucket_size 128;` to 128 or even 256.

The existing server block configuration needs to be updated as well:
```bash
server {
    listen 8181;
    listen [::]:8181; # use the port from the tor config file

    server_name <add onion url>;

    location / { 
        add_header Onion-Location "http://<onion-url>$request_uri" always;
        # this header is picked up by browser to display an icon in the clear-net browser that a tor connection is available
    }
}
```
Check the configuration `nginx -t` and reload the Nginx.

## Test connection
A test via the Tor browser should work now but their may be issues when you make multiple updates in a short period of time. Use cURL:
```bash
curl -v --socks5-hostname localhost:9050 http://<onion-url>
```
## Vanity onion address
Finding an address that start with a *human readable string* is a brute force process that has a significant cost in terms of computing power. For example, it took my old business laptop from 2008 two full days to find three onion addresses starting with 'liondig'. Running at max capacity for two days. Modern cpu's will be faster and better but it's still a random guessing exercise.

Follow the instruction links via the Tor Project website. Compile the mining program and configure a number of filters in a file. It's important to think in advance of possible combination you are willing to accept. My hope was to get to 'liondgts' but that was too much to ask for, so I settled on 'liondig'. I did get hundreds of results for 'liond' and some for 'liondi' but for each additional character it gets exponentially more difficult. Seven characters seems do-able on an old machine or raspberry pi.

*Note:* since this program is going to run for days on your linux machine, use a terminal multiplexer like `tmux` to put this process into a shell that you can attach to and from at will.
- [Vanity Addresses](https://community.torproject.org/onion-services/advanced/vanity-addresses/)
- [mkp224o](https://github.com/cathugger/mkp224o#faq-and-other-useful-info)