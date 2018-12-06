I am always tired of administrating servers, that's why i'm constantly searching for easier tools. Caddy is one of them, and i easily replaced apache with it.

## Whats Caddy?

Caddy is a fast HTTP Server with a lot of modern features. It comes with built-in support for letsencrypt, you don't have to configure it manually. There are a lot of nice features, like markdown rendering, just check out the docs at [caddyserver.com/docs][docs].

## How to use?

Download binaries at [caddyserver.com/download][download] or just piping in a bash script via `curl https://getcaddy.com | bash`. After installing to your `PATH` you will have the `caddy` command in your shell.

Place a simple `Caddyfile` in a directory with following contents:

```
:1337
browse
```

Then type `caddy` in your commandline and visit `http://localhost:1337` in your browser. You can now browse the files in that directory.

## Serving websites like apache

### Goals

* caddy runs as a service (systemd)
* caddy runs in user mode
* one Caddyfile to configure all sites
* caddy proxies to my webservices via subdomain
* HTTP**S**

### Example Webservice

Just an example server listening on port 1337, serving static content in current directory.

```sh
python -m SimpleHTTPServer 1337
```

### Configuartion

We will create a user called caddy, the systemd service runs on that user. The `Caddyfile` will be located at `/etc/caddy/Caddyfile`.

```sh
# adding a user caddy and creating /home/caddy
useradd -m caddy
```

Create the service file at `/etc/systemd/system/caddy.service`, this is actually from [here][systemd]:

```text
[Unit]
Description=Caddy Web Server
Documentation=https://caddyserver.com/docs
After=network.target

[Service]
User=caddy
StartLimitInterval=86400
StartLimitBurst=5
LimitNOFILE=16535
ExecStart=/usr/local/bin/caddy -agree=true -conf=/etc/caddy/Caddyfile -pidfile=/var/run/caddy/caddy.pid -log=stderr
PIDFile=/var/run/caddy/caddy.pid
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Give execution rights to caddy and enable/start the service:

```sh
chmod 644 /etc/systemd/system/caddy.service
systemctl enable caddy.service
systemctl start caddy.service
```

Create the Caddyfile in `/etc/caddy/Caddyfile`, with contents:

```text
foo.example.com {
  proxy / 127.0.0.1:1337
}
```

Thats it! Run `sytemctl restart caddy.service` and visit `foo.example.com`, you will have a https encrypted webservice.

[docs]: https://caddyserver.com/docs
[download]: https://caddyserver.com/download
[examples]: https://github.com/caddyserver/examples
[systemd]: https://github.com/caddyserver/examples/tree/master/systemd
