# kxn_dev - Konnexions' Development Image

This image supports the use of a Docker container for the further development of Konnexions' repositories in an Ubuntu environment. 

## 1. Creating a new Konnexions development container

## 1.1 Getting started

    > REM Assumptions:
    > REM   - you want to map the container port 8443 to the host port 443
    > REM   - the name of the Docker container should be: my_kxn_dev
    > REM   - the path the host repository is: //C/projects/my_repro
    > REM   - the directory name for this repository inside the container should be: my_repro_dir
    > REM   - you want to use the latest version of the Konnexions development image
    > docker run -it -p 443:8443 \
                 --name my_kxn_dev \
                 -v //C/projects/my_repro:/my_repro_dir \
                 konnexionsgmbh/kxn_dev:latest
            
    > REM Stopping the container
    > docker stop my_kxn_dev
    
    > REM Restarting the container
    > docker start my_kxn_dev

    > REM Entering a running container
    > docker exec -it my_kxn_dev bash

## 1.2 Detailed Syntax

A new container can be created with the `docker run` command.

##### Syntax:

    docker run -it 
               [-p <port>:8443] \
               [--name <container_name>] \
               [-v <directory_repository>:/dderl] \
               konnexionsgmbh/kxn_dev[:<version>] 
               [<cmd>]
 
##### Parameters:

- **port** - an optional listener port             
- **container_name** - an optional container identification 
- **directory_repository** - an optional host repository directory - the default value is expecting the repository inside the container 
- **version** - an optional version number of the image or the constant `latest`
- **cmd** - an optional command to be executed in the container, default is `bash` for running the `bash` shell

Detailed documentation for the command `docker run` can be found [here](https://docs.docker.com/engine/reference/run/).

##### Examples:

1. Creating a new Docker container named `my_kxn_dev` using a repository inside the Docker container:  

    `docker run -it --name my_kxn_dev konnexionsgmbh/kxn_dev:latest`

2. Creating a new Docker container named `my_kxn_dev` using the host repository of a Windows directory `D:\projects\my_repro`:  

    `docker run -it --name dderl_dev -v //D/projects/my_repro:/my_repro konnexionsgmbh/kxn_dev:latest`

3. Creating a new Docker container named `my_kxn_dev` using the host repository of a Linux directory `/my_repro` and mapping port `8443` to port `8000`:  

    `docker run -it --name my_kxn_dev -p 8000:8443 -v /my_repro:/my_repro konnexionsgmbh/kxn_dev:latest`

## 2 Working with an existing Konnexions development container

### 2.1 Starting a stopped container

A previously stopped container can be started with the `docker start` command.

##### Syntax:

    docker start <container_name>

##### Parameter:

- **container_name** - the mandatory container identification, that is an UUID long identifier, an UUID short identifier or a previously given name 

Detailed documentation for the command `docker start` can be found [here](https://docs.docker.com/engine/reference/commandline/start/).

### 2.2 Entering a running container

A running container can be entered with the `docker exec` command.

##### Syntax:

    docker exec -it <container_name> <cmd>

##### Parameter:

- **container_name** - the mandatory container identification, that is an UUID long identifier, an UUID short identifier or a previously given name 
- **cmd** - the command to be executed in the container, e.g. `bash` for running the `bash` shell

Detailed documentation for the command `docker exec` can be found [here](https://docs.docker.com/engine/reference/commandline/exec/).

## 3 Best practices

### 3.1 Use of a root repository directory on the host computer

If all relevant repositories are located within a common parent directory, then development work in all these repositories can be done within a single Konnexions development container. 

**Example:**

In the following example we assume that the host directory is named `C:\Temp\my_projects` and should be mapped to the `projects` directory in the container.

```
>C:\Temp\my_projects>docker run -it --name kxn_dev -v //C/Temp/my_projects:/projects konnexionsgmbh/kxn_dev:latest
root@35b9310932f1:/# cd projects
root@35b9310932f1:/projects# ls -ll
total 0
drwxrwxrwx 1 root root 4096 May  2 14:05 dderl
```

### 3.2 Use of private GitHub repositories inside the container

To access private repositories in GitHub, you must first create a new personal access token in GitHub and then add it to your git configuration inside the container.

#### 1. Create a new personal access token in GitHub

- With the following URL you can create the access token: https://github.com/settings/tokens

![](.README_images/Personal_Access_Token_01.png)

- Press the button `Generate new token`

![](.README_images/Personal_Access_Token_02.png)

- Name the new token, select the scopes and press the button `Generate token` 

![](.README_images/Personal_Access_Token_03.png)

- Write down the secret code and keep it in a safe place

#### 2. Setting up the Docker container on the host machine

In the following example we assume that the host directory is named `C:\Temp\my_projects` and should be mapped to the `projects` directory in the container.

```
C:\Temp\my_projects\dderl>docker run -it --name kxn_dev -v //C/Temp/my_projects:/projects konnexionsgmbh/kxn_dev:latest
Unable to find image 'konnexionsgmbh/kxn_dev:latest' locally
latest: Pulling from konnexionsgmbh/kxn_dev
d51af753c3d3: Pull complete
...
a6bb30d1a5cf: Pull complete
Digest: sha256:5f6d6afc566ef9142d2d85b85dd331c0558eafaaf286179fd0ae787988c1b89b
Status: Downloaded newer image for konnexionsgmbh/kxn_dev:latest
```

#### 3. Initial configuration of git in the container

```
root@332206c300f1:/# export XDG_CONFIG_HOME=/projects
root@332206c300f1:/# mkdir -p $XDG_CONFIG_HOME/git/
root@332206c300f1:/# touch $XDG_CONFIG_HOME/git/config
root@332206c300f1:/# touch $XDG_CONFIG_HOME/git/credentials
root@332206c300f1:/# git config --file=$XDG_CONFIG_HOME/git/config credential.helper 'store --file=/projects/git/credentials'
root@332206c300f1:/# git config --file=$XDG_CONFIG_HOME/git/config user.name "John Doe"
root@332206c300f1:/# git config --file=$XDG_CONFIG_HOME/git/config user.email "john.doe@company.com"
root@332206c300f1:/# git config --list --show-origin
file:/projects/git/config       credential.helper=store --file=/projects/git/credentials
file:/projects/git/config       user.name=John Doe
file:/projects/git/config       user.email=john.doe@company.com
```

#### 4. Verification of the settings

```
root@332206c300f1:/# cat /projects/git/config
[credential]
        helper = store --file=/projects/git/credentials
[user]
        name = John Doe
[user]
        email = john.doe@company.com
```

#### 5. Clone a repository for the first time

When prompted provide your github user name and the new personal access token from (1).

```
root@332206c300f1:/# cd projects
root@332206c300f1:~# git clone https://github.com/KonnexionsGmbH/docker_images
Cloning into 'docker_images'...
Username for 'https://github.com': John Doe
Password for 'https://john.doe@company.com': abc033c3d4d5220e66d63e60a0c5b2497a2dca9f
remote: Enumerating objects: 78, done.
remote: Counting objects: 100% (78/78), done.
remote: Compressing objects: 100% (49/49), done.
remote: Total 78 (delta 33), reused 68 (delta 23), pack-reused 0
Receiving objects: 100% (78/78), 167.83 KiB | 867.00 KiB/s, done.
Resolving deltas: 100% (33/33), done.
```

#### 6. Verify if the clone completed with success

```
root@332206c300f1:~# cat /projects/git/credentials
https://John Doe:abc033c3d4d5220e66d63e60a0c5b2497a2dca9f@github.com
```

#### 7. Verification after a restart of the Docker container 

```
C:\Temp\my_projects\dderl>docker start kxn_dev
kxn_dev
C:\Temp\my_projects\dderl>docker exec -it kxn_dev bash
root@332206c300f1:/# export XDG_CONFIG_HOME=/projects
root@332206c300f1:/# git config --list --show-origin
file:/projects/git/config       credential.helper=store --file=/projects/git/credentials
file:/projects/git/config       user.name=John Doe
file:/projects/git/config       user.email=john.doe@company.com
```

#### 8. Verification after the removal of the Docker container 

- Deleting the Docker container and image
 
```
C:\Temp\my_projects\dderl>docker stop kxn_dev
kxn_dev

C:\Temp\my_projects\dderl>docker rm kxn_dev
kxn_dev

C:\Temp\my_projects\dderl>docker images
REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
konnexionsgmbh/kxn_dev      latest              51757b5e414e        6 hours ago         3.71GB

C:\Temp\my_projects\dderl>docker rmi 51757b5e414e
Untagged: konnexionsgmbh/kxn_dev:latest
Untagged: konnexionsgmbh/kxn_dev@sha256:5f6d6afc566ef9142d2d85b85dd331c0558eafaaf286179fd0ae787988c1b89b
Deleted: sha256:51757b5e414e5333ace7b163484c06e4685c29312ad09d5d7d648c6936011a60
...
Deleted: sha256:7789f1a3d4e9258fbe5469a8d657deb6aba168d86967063e9b80ac3e1154333f
```

- Recreating the Docker container (and image)

```
C:\Temp\my_projects\dderl>docker run -it --name kxn_dev -v //C/Temp/my_projects:/projects konnexionsgmbh/kxn_dev:latest
Unable to find image 'konnexionsgmbh/kxn_dev:latest' locally
latest: Pulling from konnexionsgmbh/kxn_dev
d51af753c3d3: Pull complete
...
a6bb30d1a5cf: Pull complete
Digest: sha256:5f6d6afc566ef9142d2d85b85dd331c0558eafaaf286179fd0ae787988c1b89b
Status: Downloaded newer image for konnexionsgmbh/kxn_dev:latest
root@ad1f036bbc44:/# export XDG_CONFIG_HOME=/projects
root@ad1f036bbc44:/# git clone https://github.com/KonnexionsGmbH/docker_images
Cloning into 'docker_images'...
remote: Enumerating objects: 78, done.
remote: Counting objects: 100% (78/78), done.
remote: Compressing objects: 100% (49/49), done.
remote: Total 78 (delta 33), reused 68 (delta 23), pack-reused 0
Receiving objects: 100% (78/78), 167.83 KiB | 895.00 KiB/s, done.
Resolving deltas: 100% (33/33), done.
```

- If we use the same path - where `git/config` and `git/credentials` exist - as in Step 3, `git` access (clone/push/pull) doesn't ask for username/password anymore.

## 4 Working inside a running Konnexions development container

### 4.1 `dderl` development

Inside the Docker container you can either clone a `dderl` repository or switch to an existing `dderl` repository. 
If a Docker container with an Oracle database is located on the host computer it can be accessed by using the IP address of the host computer.
Any `dderl` script can be executed inside the Docker container, for example:

    rebar3 compile
    rebar3 as prod release
    ./start.sh 
    
The following port numbers are exposed and can be mapped if necessary:

    1236
    7000-7020
    8125
    8443
    9443    

### 4.2 `ora_bench_dev` development

Inside the Docker container you can either clone a `ora_bench` repository or switch to an existing `ora_bench` repository. 
If a Docker container with an Oracle database is located on the host computer it can be accessed by using the IP address of the host computer.
Any `ora_bench` script can be executed inside the Docker container, for example:

    ./scripts/run_properties_standard.sh > run_properties_standard.log 2>&1
    
**Important:** If the repository was previously used on Windows, then all files in the following directories must also be deleted from Windows first:

- `src_elixir/deps`  
- `src_elixir/mix.lock`  
- `src_erlang/_build` 

### 4.3 [presto](https://prestosql.io)

For an introduction to presto it is best to use the book [Presto - The Definitive Guide](https://prestosql.io/presto-the-definitive-guide.html).

The installation in the `kxn_dev` image includes both the `presto`server and the `presto`command line interface (CLI).
The installation directory is `/presto-server`, of which the `bin` directory is also included in the `path` variable.
The configuration files have the same contents as described in the book.

The `presto` server can be started with the command

    launcher start
     
Then the `presto` CLI is available with the command

    presto

`presto`'s default port `8080`is exposed.

## 5 Installed core components

With the following command you can check in detail which software versions are included in the Docker image:

    apt list --installed

### Version 1.3.0

| Component             | Version          | Remark                      | Status |
|---                    |---               |---                          |---     |
| Ubuntu                | 20.04 LTS        | focal                       |   | 
|                       |                  |                             |   |
| Elixir                | 1.10.4           | compiled with Erlang/OTP 22 |   |
| Erlang/OTP            | 23 [erts-11.0.2] |                             |   |
| gcc                   | 9.3.0            |                             |   |
| go                    | go1.14.6         |                             | upgrade | 
| Java (openjdk)        | 14.0.2           | build 14.0.2-46             |   |
| Node                  | v12.18.3         |                             | upgrade |
| Python                | 3.8.2            |                             |   |
|                       |                  |                             |   |
| OpenSSL               | 1.1.1f           |                             |   | 
| Oracle Instant Client | 19.8.0.0.0       |                             | upgrade | 
|                       |                  |                             |   |
| git                   | 2.27.0           |                             |   | 
| Gradle                | 6.5.1            |                             |   | 
| LCOV                  | 1.14             |                             |   | 
| nginx                 | nginx/1.18.0     |                             |   |
| presto                | CLI 339          |                             | new |
| rebar                 | 3.13.2           |                             |   |
| VIM                   | 8.2              |                             |   |
| yarn                  | 1.22.4           |                             |   | 

### Version 1.2.0

| Component | Version | Remark | Status |
|---|---|---|---|
| Ubuntu | 20.04 LTS | focal | | 
| | | | |
| Elixir | 1.10.4 | compiled with Erlang/OTP 22 | upgrade |
| Erlang/OTP | 23 [erts-11.0.2] | | upgrade |
| gcc | 9.3.0 | | |
| go | go1.14.5 | | upgrade | 
| Java (openjdk) | 14.0.2 | build 14.0.2-46 | upgrade |
| Node | v12.18.2 | | upgrade |
| Python | 3.8.2 | | |
| | | | |
| OpenSSL | 1.1.1f | | | 
| Oracle Instant Client | 19.6.0.0.0 | | | 
| | | | |
| git | 2.27.0 | | upgrade | 
| Gradle | 6.5.1 | | upgrade | 
| LCOV | 1.14 | | | 
| nginx | nginx/1.18.10 | | upgrade |
| rebar | 3.13.2 | | |
| VIM | 8.2 | | |
| yarn | 1.22.4 | | | 

### Version 1.1.0

| Component | Version | Remark | Status |
|---|---|---|---|
| Ubuntu | 20.04 LTS | focal | | 
| | | | |
| Elixir | 1.10.3 | compiled with Erlang/OTP 21 | |
| Erlang/OTP | 22 [erts-10.7.1] | | |
| gcc | 9.3.0 | | |
| go | go1.14.3 | | upgrade | 
| Java (openjdk) | 14.0.1 | build 14.0.1+7 | upgrade |
| Node | v12.16.3 | | |
| Python | 3.8.2 | | |
| | | | |
| OpenSSL | 1.1.1f | | | 
| Oracle Instant Client | 19.6.0.0.0 | | | 
| | | | |
| git | 2.26.2 | | | 
| Gradle | 6.4.1 | | upgrade | 
| LCOV | 1.14 | | | 
| nginx | nginx/1.17.10 | | |
| rebar | 3.13.2 | | |
| VIM | 8.2 | | |
| yarn | 1.22.4 | | | 

### Version 1.0.1

| Component | Version | Remark | Status |
|---|---|---|---|
| Ubuntu | 20.04 LTS | focal | | 
| | | | |
| Elixir | 1.10.3 | compiled with Erlang/OTP 21 | |
| Erlang/OTP | 22 [erts-10.7.1] | | |
| gcc | 9.3.0 | | |
| go | go1.14.2 | | | 
| Java (openjdk) | "14" 2020-03-17 | build 14+36-1461 | |
| Node | v12.16.3 | | |
| Python | 3.8.2 | | |
| | | | |
| OpenSSL | 1.1.1f | | | 
| Oracle Instant Client | 19.6.0.0.0 | | | 
| | | | |
| git | 2.26.2 | | | 
| Gradle | 6.3 | | | 
| LCOV | 1.14 | | new | 
| nginx | nginx/1.17.10 | | |
| rebar | 3.13.2 | | |
| VIM | 8.2 | | |
| yarn | 1.22.4 | | | 

### Version 1.0.0

| Component | Version | Remark |
|---|---|---|
| Ubuntu | 20.04 LTS | focal | 
| | | |
| Elixir | 1.10.3 | compiled with Erlang/OTP 21 | 
| Erlang/OTP | 22 [erts-10.7.1] | |
| gcc | 9.3.0 | | 
| go | go1.14.2 | | 
| Java (openjdk) | "14" 2020-03-17 | build 14+36-1461 | 
| Node | v12.16.3 | | 
| Python | 3.8.2 | | 
| | | |
| OpenSSL | 1.1.1f | | 
| Oracle Instant Client | 19.6.0.0.0 | | 
| | | |
| git | 2.26.2 | | 
| Gradle | 6.3 | | 
| nginx | nginx/1.17.10 | | 
| rebar | 3.13.2 | | 
| VIM | 8.2 | | 
| yarn | 1.22.4 | | 