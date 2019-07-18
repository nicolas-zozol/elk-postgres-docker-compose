Install Docker on Ubuntu
====

docker and docker.io are bad or old projects. Use docker-ce

    sudo apt-get update
    sudo apt-get install \
        apt-transport-https \
        ca-certificates \
        curl \
        gnupg-agent \
        software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo add-apt-repository \
       "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
       $(lsb_release -cs) \
       stable"
    sudo apt-get update
    sudo apt-get install docker-ce docker-ce-cli containerd.io
    
    # Check groups
    sudo groupadd docker
    sudo usermod -aG docker $USER
    newgrp docker  # change groups without logout/login
    groups  # check you have docker in your groups
    

Now install docker compose

    sudo apt  install docker-compose
    
    # sudo apt install python-pip
    # pip install docker-compose
                   
Configure Docker to start on boot

    sudo systemctl enable docker
    # sudo systemctl disable docker # for disable     

And now please reboot.


Quickstart
====

TL;DR

    # add memory
    sudo sysctl -w vm.max_map_count=262144
    
    # download image from the web
    docker-compose pull     
    
    # initiate security
    # CHECK the password
    docker-compose -f setup.yml up
    
    # start services  
    docker-compose -d up 
    # and wait quite a long time.... 
    # TIP: Remove -d to check errors on start
    
    # stop : DO NOT use docker-compose down or it destroys everything
    docker-compose stop
        


Linux settings
=====

Elastic has user : `elastic` and a password like: N7CZ3hfFU/1xRjfpkZS7aA==
Kibana is accessible at [http://localhost:5601/app/kibana]()
Postgres has user/password defined on .env file. Database is `postgres` and port 5432
Adminer web app is accessible with [http://localhost:8080]()


.env file exemple:

    TAG=6.6.0
    ELASTIC_VERSION=6.6.0
    POSTGRES_USER=nicolas
    POSTGRES_PASSWORD=pass

`.env` file **SHOULD NOT** be on your git repository


WARNING !!!!
-----

* Linux Users must set the following configuration as `root`:
* Using `docker-compose up` without `--daemeon` flag, you may see:


        [1]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
        max virtual memory areas vm.max_map_count [65530] i


So change it in any terminal you start, or in `.bashrc` file if needed

        sudo sysctl -w vm.max_map_count=262144

But lol, you can't easily sudo in .bash_rc. So do it every time and create a shortcut

        alias mem='sudo sysctl -w vm.max_map_count=262144'        
        

By default, the amount of Virtual Memory [is not enough](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html).


Setup
=====


First we need to:

1. set default password
2. create keystores to store passwords
3. install dashboards, index patterns, etc.. for beats and apm

This is accomplished using the setup.yml file:
```
docker-compose -f setup.yml up
```

Please take note after the setup completes it will output the password
that is used for the `elastic` login.

Now we can launch the stack with `docker-compose up -d` to create a demonstration Elastic Stack with
Elasticsearch, Kibana, Logstash, Auditbeat, Metricbeat, Filebeat, Packetbeat,
and Heartbeat.

** After success**, Kibana is there at [`http://localhost:5601`](http://localhost:5601) to see the results.
> *NOTE*: Elasticsearch is now setup with self-signed certs.

Log in with `elastic` and what ever your auto generated elastic password is from the
setup.

ex: `Your 'elastic' user password is: J73E9VwG7sjuI2C7rsAj2g==`


Install docker and docker-compose (on ubuntu)
=====

- Docker and Docker Compose.
  * Windows and Mac users get Compose installed automatically
with Docker for Windows/Mac.

  * Linux users can read the [install instructions](https://docs.docker.com/compose/install/#install-compose


Or if python and pip:

```
pip install docker-compose
``` 



Windows Settings
=====


* Windows Users must set the following 2 ENV vars:
  * `COMPOSE_CONVERT_WINDOWS_PATHS=1`
  * `PWD=/path/to/checkout/for/stack-docker`
    * for example I use the path: `/c/Users/nick/elastic/stack-docker`
    * Note: you're paths must be in the form of `/c/path/to/place` using `C:\path\to\place` will not work
  * You can set these two ways:
    1. Temporarily add an env var in powershell use: `$Env:COMPOSE_CONVERT_WINDOWS_PATHS=1`
    2. Permanently add an env var in powershell use: `[Environment]::SetEnvironmentVariable("COMPOSE_CONVERT_WINDOWS_PATHS", "1", "Machine")`
      > Note: you will need to refresh or create a new powershell for this env var to take effect
    3. in System Properties add the environment variables.


* At least 4GiB of RAM for the containers. Windows and Mac users _must_
configure their Docker virtual machine to have more than the default 2 GiB of
RAM:

![Docker VM memory settings](screenshots/docker-vm-memory-settings.png)
