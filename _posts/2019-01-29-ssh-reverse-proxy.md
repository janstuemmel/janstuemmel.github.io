Here's a quick guide on how to use ssh reverse proxy for web development. It's useful when you want to access your local development server from outside your local network. 

You certainly are able to achive this by using [ngrok](https://ngrok.com/). But if you own a remote server why not use `ssh`, it's super easy!
 
```sh
ssh -nN -R <remote-port>:localhost:<local-port> <username>@<server-ip-or-hostname>
```

## Explanation

* `<remote-port>` Any free port on your remote server, e.g. `9999`
* `<local-port>` The port your local webserver is listening to, e.g. `8080`
* `<server-ip-or-hostname>` Your remote server's hostname or ip, e.g. `example.com`
* `<username>` A user on the remote server, e.g. `foo`
* `-R` Arg for reverse proxy
* `-n` Prevents reading from `STDIN`
* `-N` Prevents executing remote commands

## Example

Let's say your development server is running on `8080`, for example with Python:
`python -m SimpleHTTPServer 8080` (serves working directory contents). 
To access contens from the internet on `http://example.com:9999` type...

```sh
ssh -nN -R 9999:localhost:8080 foo@example.com
```

## Reference

* [How to expose a local development server to the Internet](https://medium.com/botfuel/how-to-expose-a-local-development-server-to-the-internet-c31532d741cc)
