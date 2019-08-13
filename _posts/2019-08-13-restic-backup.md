[Restic](https://restic.net/) is a backup program written in go that makes doing backups easy. Here's how i do it.

## Install 

Install restic with your distribution package manager [here](https://restic.readthedocs.io/en/stable/020_installation.html) or just download the binary from [github.com/restic/restic/releases](https://github.com/restic/restic/releases):

```sh
wget https://github.com/restic/restic/releases/download/v0.9.5/restic_0.9.5_linux_amd64.bz2

bunzip2 restic_0.9.5_linux_amd64.bz2

sudo mv restic_0.9.5_linux_amd64.bz2 /usr/local/bin/restic

sudo chmod + /usr/local/bin/restic
```

Now restic should be in your `$PATH`. Test it with `restic help`.

## Usage

With restic you put your backups into a repository. A repository can be on your local disk, a ssh server or even cloud storage provider like AWS S3. I am using a simple webspace where i can login via ssh to store my server backup. To do so your ssh server needs to know your ssh key:

```sh
# on your local machine
cat .ssh/id_rsa.pub

# login to your ssh server
ssh <user>@<ip>

echo <id_rsa.pub contents> >> .ssh/authorized_keys

# create a backup folder on your server
mkdir backup

# logout
exit
```

Now you're able to backup some files with restic. Specify a `RESTIC_REPOSITORY` and `RESTIC_PASSWORD` environment variable. The password is for the encryption on the server. You cannot restore your backup anymore without knowing your password.

```sh
export RESTIC_REPOSITORY=sftp:<user>@<ip>:backup
export RESTIC_PASSWORD=s3cret

# init the repository on your server
restic init

# backup your .config directory for example
restic backup .config

# list your backups
restic snapshots

# restore your backup
restic restore <id> --target /tmp/restic_restore 
```

### Environment variables

If you don't want to `export` your environment variables everytime you use restic, just put them in `~/.profile` or `~/.bashrc` file. 

### Automatic backup with docker

I've created a docker image wich is hosted on [hub.docker.com/r/janstuemmel/restic](https://hub.docker.com/r/janstuemmel/restic). It runs [tinycron](https://github.com/bcicen/tinycron) with a simple sh script. You can use it to backup your container volumes:

```yml
version: '3'

services:

  # some service that saves data in /var/service/data
  app:
    image: someservice
    volumes:
      - data:/var/service/data

  backup:
    image: janstuemmel/restic 
    restart: always
    volumes:
      # mount your volume to /backup/<your backup name>, because backup 
      # script will backup each folder in /backup seperatly
      - data:/backup/data
    environment:
      CRON: '@daily'
      # set your hostname here, because in a container restic will see 
      # the guest hostname
      RESTIC_HOST: your_host_name
      RESTIC_ARGS: --exclude cache
      RESTIC_REPOSITORY: sftp:<user>@<ip>:backup
      RESTIC_PASSWORD: s3cret

volumes:
  data:
```