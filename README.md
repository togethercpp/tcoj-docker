# CKTOJ Docker

This repository contains the Docker files to run the [CKTOJ](https://github.com/CKTOJ/OJ).

Based on [dmoj-docker](https://github.com/Ninjaclasher/dmoj-docker).

## Installation

Set up Docker's apt repository.
```
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
Install the Docker packages.
```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io 
```
Install the Docker compose.
```
sudo apt-get update
sudo apt-get install docker-compose-plugin
```
Clone the repository:

```sh
$ git clone --recursive https://github.com/CKTOJ/cktoj-docker.git
$ cd cktoj-docker/dmoj
```

From now on, it is assumed you are in the `dmoj` directory.

Initialize the setup by moving the configuration files into the submodule and creating the necessary directories:

```sh
$ ./scripts/initialize
```

Next, configure the environment variables in the files in `dmoj/environment/`. Create the files from the examples:

```sh
$ cp environment/mysql-admin.env.example environment/mysql-admin.env
$ cp environment/mysql.env.example environment/mysql.env
$ cp environment/site.env.example environment/site.env
```

Then, set the MYSQL passwords in `mysql.env` and `mysql-admin.env`, and the host and secret key in `site.env`. Also, configure the `server_name` directive in `dmoj/nginx/conf.d/nginx.conf`.

Next, build the images:

```sh
$ docker compose build
```

Start up the site, so you can perform the initial migrations and generate the static files:

```sh
$ docker compose up -d site db redis celery
```

You will need to generate the schema for the database, since it is currently empty:

```sh
$ ./scripts/migrate
```

You will also need to generate the static files:

```sh
$ ./scripts/copy_static
```

Finally, the VNOJ comes with fixtures so that the initial install is not blank. They can be loaded with the following commands:

```sh
$ ./scripts/manage.py loaddata navbar
$ ./scripts/manage.py loaddata language_small
$ ./scripts/manage.py loaddata demo
```

Keep in mind that the demo fixture creates a superuser account with a username and password of `admin`. You should change the user's password or remove the user entirely.

You can also create a superuser account for yourself:

```sh
$ ./scripts/manage.py createsuperuser
```

## Usage

To start everything:

```sh
$ docker compose up -d
```

To stop everything:

```sh
$ docker compose down
```
