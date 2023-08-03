# Etherpad lite Datagateway-API authentication and authorization

This plugin, based on the sessionId passed by query param, authenticates and authorizes an user.


## Plugin installation

In your etherpad-lite dir:

    npm install ep_dg_auth

Add to settings.json:

```
"users": {
    "dgserver": {
        "server": "https://datagateway.server.com"
    },
}
Change authentication and authorization settings to true depending on which hooks you will be using.
```

## Integration on the client

It is supposed to be used inside an iframe on the same site to use the Lax cookie otherwise a reverse proxy is required and the cookie must be set to none in etherpad settings:

```
              <div>
                <iframe
                  title="Etherpad"
                  src={`${etherpadURL}/auth_session?sessionID=${session}&padName=${padId}`}
                  width="100%"
                  height={window.innerHeight}
                />
              </div>
```

# Etherpad 

## Installation based on Ubunto 20.04 Focal no gui

Etherpad can be fully installed by doing the following:
```
git clone --branch master https://github.com/ether/etherpad-lite.git &&
cd etherpad-lite &&
npm install --legacy-peer-deps ep_dg_auth ep_auth_session &&
./bin/run.sh

```

I did experience problems with the latest version of node. I work around the issue by installing the version 14.18.2 via nvm

Use node version at least 12.22.12 with nvm otherwise it will fail to load lock files. https://www.javatpoint.com/install-nvm-ubuntu
```
nvm install 14.18.2
```
## This is a guide for self signed certificates for developing

Generate private key

```
openssl genpkey -algorithm RSA -out /path/to/private.key
```

Generate CSR (Certificate Signing Request) and Self-Signed Certificate

```
openssl req -new -key /path/to/private.key -out /path/to/certificate.csr

openssl x509 -req -days 365 -in /path/to/certificate.csr -signkey /path/to/private.key -out /path/to/certificate.crt
```

Replace /path/to/private.key, /path/to/certificate.csr, and /path/to/certificate.crt with appropriate file paths.

Configure Nginx:

Edit the Nginx configuration file, which is usually located at /etc/nginx/nginx.conf or in a separate file inside /etc/nginx/conf.d/ or /etc/nginx/sites-available/.

Add the following server block:


```
server {
    listen 443 ssl;
    server_name YOUR_SERVER_IP;  # Replace with your server's IP address

    ssl_certificate /path/to/certificate.crt;
    ssl_certificate_key /path/to/private.key;

    location / {
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_pass http://127.0.0.1:9001;  # Replace with the Etherpad's actual listening address and port
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

Replace YOUR_SERVER_IP with the public IP address of your server, and update the proxy_pass directive with the address and port where Etherpad is running.


Restart Nginx:
```
sudo systemctl restart nginx
```
Save the configuration file and restart Nginx to apply the changes

If you are using ssh through visual studio and have set the port forwarding protocol to https you must disable this before as it will conflict

### Installing datagateway api to a VM

```
git clone https://github.com/ral-facilities/datagateway-api.git
curl https://pyenv.run | bash
export PATH="~/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
sudo apt update
sudo apt install build-essential zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev libssl-dev liblzma-dev libffi-dev findutils
curl -sSL https://install.python-poetry.org | python3 -
export PATH="~/.local/bin:$PATH"
source ~/.bashrc
Restart your terminal
poetry install
poetry run python -m datagateway_api.src.main
```

### Configuring datagateway api

```
Please point this to an ICAT instance
```


### Installing datagateway to a VM

```
git clone https://github.com/ral-facilities/datagateway.git
sudo npm install -g yarn
yarn install
yarn datagateway-dataview
```

### Configuring datagateway
```
Please head over to datagateway-dataview-settings.json and configure the IDS, API, downloadAPI and etherpad urls.
You can use preprod urls from here https://scigateway-preprod.esc.rl.ac.uk/plugins/datagateway-dataview/datagateway-dataview-settings.json
Please use the IP from your etherpad machine with https:// and without the port if you are using nginx
```

### Helpful Links
```
Use this link to get the correct version of node https://learnubuntu.com/update-node-js/?utm_content=cmp-true
Reverse proxy help https://github.com/ether/etherpad-lite/wiki/How-to-put-Etherpad-Lite-behind-a-reverse-Proxy
Etherpad documentation https://etherpad.org/doc/v1.3.0/#index_overview
Installing postgresql on your developer instance https://louisroyer.github.io/tutorial/2019/09/12/migrate-etherpad-lite-dirtydb-to-postgres-debian-buster.html
```
### Troubleshooting
If you are having trouble connecting make sure the ports are open and nginx is running
```
sudo iptables -S | grep [port number]
sudo systemctl status nginx
```
### If you have to use openstacks console instead of SSH the following link will help you run tasks in the background so you can run dg. dg-api and etherpad all on the same VM.
```
https://www.baeldung.com/linux/detach-process-from-terminal
```
