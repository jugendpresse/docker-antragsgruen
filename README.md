# Apache for Antragsgrün / motion.tools #
## README ##

### What is this repository for? ###
Within this Repo you will find the Dockerfile and the pipeline configuration to build a running container for the motion tool Antragsgrün.

### How do I get set up? ###
Since the docker principles tell you to only run one process within one container, the container provided within this repository, `jugendpresse/antragsgruen`, only provides the php application. You need to setup a MySQL container, too. The `docker-compose.yml` within this repo shows you, how you can set this up.

If you want to reuse an existing database (container), you have to add a database and the credentials manually and remove the database service from `docker-compose.yml`.

Deploying from `docker-compose.yml` works by running the following command:

```sh
docker-compose up -d
```

![docker-compose up -d](https://jugendpresse.cloud/index.php/apps/files_sharing/ajax/publicpreview.php?x=500&y=272&a=true&t=hjNHYoU764vyYpB)

If you've set your Antragsgrün up by `docker-compose.yml`, your container normally is called something like `antragsgruen_antragsgruen_1`.

First start-up (and after updates) will take a while due to installing the used PHP / NodeJS packages. The startup process is done, when the Docker logs show you something like that:

```sh
$ docker logs -f antragsgruen_antragsgruen_1

# [Fri Feb 09 10:00:00.000000 2018] [core:notice] [pid 1] AH00010: Command line: 'apache2 -D FOREGROUND'
```

![docker logs -f antragsgruen_antragsgruen_1](https://jugendpresse.cloud/index.php/apps/files_sharing/ajax/publicpreview.php?x=1315&y=714&a=true&t=aojNWX6rQuTrjDp)

#### Reach the Website ####

Since the default Docker setup via `docker-compose` binds the webservice to port `8080` (`docker-compose.yml`, line 9), you can reach it via http://ip-address:8080 (or if you have bound an URL `domain.tld` to your IP even http://domain.tld:8080).

In my setup, I don't want to use HTTP protocol within public (since it is not encrypted). To use the container via HTTPS protocol, there are two possibilities: you can configure the containers Apache to use certificates and an adjusted Apache2 config file, which all have to be mounted to the container. The alternative is to use a reverse proxy – i.e. [Træfik](https://traefik.io) is able to do what we need and to secure the container with free [Let’s Encrypt](https://letsencrypt.org) Certificates.

#### Database configuration during setup ####

After the boot process of the main container (probably `antragsgruen_antragsgruen_1`), you normally want to set up your instance of Antragsgrün. The main part here is the connection to the database.

The Hostname of the database, you've created is the name of the Docker container of the database. If you used the `docker-compose.yml` file it will normally look like `antragsgruen_database_1`. Also given by `docker-compose.yml` you'll use the `root` user with the given password – please change it within the `docker-compose.yml` before you'll start your containers!

![Database-Setup](https://jugendpresse.cloud/index.php/apps/files_sharing/ajax/publicpreview.php?x=1315&y=753&a=true&t=dWK8cjpFgK28WNl)

### Contribution guidelines ###

This Repo is Creative Commons non Commercial - You can contribute by forking and using pull requests. The team will review them asap.

### environmental variables you should be aware of ###

The following environmental variables you can edit. There are more of them, but there should be no need to update / change the other ones (as the `www-data` username for Apache runner and the `/var/www/html/` folder as working directory).

Since a Docker container defaults the timezone and therefor the time sync to UTC, one can set the `TIMEZONE` environmental variable i.e. as `TIMEZONE="Europe/Berlin"` to change the container time behavior. `docker-compose.yml` file defaults this to `Europe/Berlin`.

On a non-development setup, Antragsgrün wants to communicate with the users – i.e. `A new motion was created` or `You forgot your password? Here's a new one!` and so on. Therefor the serverside sendmail-equivalent `msmtp` has to be configured and all you've to do is to set the following environmental variables:

* `SMTP_HOST` should be set to your smtp host, i.e. `mail.example.com`
* `SMTP_PORT` defaults to `587`
* `SMTP_FROM` should be set to your sending from address, i.e. `motiontool@example.com`
* `SMTP_USER` defaults to `SMTP_FROM` and has to be the user, you are authenticating on the `SMTP_HOST`
* `SMTP_PASS` should be set to your plaintext(!) smtp password, i.e. `I'am very Secr3t!`

If you are running Antragsgrün behind a reverse proxy like Træfik (the `docker-compose.yml` does NOT!), you can ignore the following variable. Else it should be set to the FQDN you'll use for visiting Antragsgrün, i.e. `motiontool.example.com`:

* `APACHE_FQDN`

### installed tools ###

See base repository: https://github.com/jugendpresse/apache
