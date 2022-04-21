---
title: "docker inspect query cheatsheet"
date: 2022-04-21T19:10:32+01:00
tags: []
draft: false
---

Anyone who has run `docker inspect` will know there's a ton of information.
Using a tool like jq will help you home in on the information you want.

Here are some useful `jq` queries to remember.

Note that as `docker inspect` can take multiple arguments, it can 
return information on mutiple containers. Therefore, you will
receive an JSON array. This requires you to start your queries with
`.[].`.

## Top level objects

- `AppArmorProfile`
- `Args`
- `Config`
- `Created`
- `Driver`
- `ExecIDs`
- `GraphDriver`
- `HostConfig`
- `HostnamePath`
- `HostsPath`
- `Id`
- `Image`
- `LogPath`
- `MountLabel`
- `Mounts`
- `Name`
- `NetworkSettings`
- `Path`
- `Platform`
- `ProcessLabel`
- `ResolvConfPath`
- `RestartCount`
- `Stat`

These can be found with the following command: `docker inspect <container> | jq '.[] | keys'`

## Useful objects

To summarise the following `jq` queries:

- `.[].NetworkSettings.Ports`
- `.[].Config.Labels`
- `.[].Config.Env`
- `.[].State.Health`

### Find bound ports

```bash
lewisboon@Lewis-PC:~$ docker inspect dd4b | jq .[].NetworkSettings.Ports
{
  "53/tcp": null,
  "53/udp": null,
  "67/udp": null,
  "80/tcp": [
    {
      "HostIp": "0.0.0.0",
      "HostPort": "8080"
    }
  ]
}
```

### Get labels

```bash
lewisboon@Lewis-RyzenPC:~$ docker inspect <container> | jq .[].Config.Labels
{
  "desktop.docker.io/wsl-distro": "Ubuntu-20.04",
  "org.opencontainers.image.created": "2022-01-05T23:05:34.043Z",
  "org.opencontainers.image.description": "Pi-hole in a docker container",
  "org.opencontainers.image.licenses": "",
  "org.opencontainers.image.revision": "4cc3587084800f77beaa96e4d2bde12a3bba7fdb",
  "org.opencontainers.image.source": "https://github.com/pi-hole/docker-pi-hole",
  "org.opencontainers.image.title": "docker-pi-hole",
  "org.opencontainers.image.url": "https://github.com/pi-hole/docker-pi-hole",
  "org.opencontainers.image.version": "2022.01.1"
}
```

### Get environment variables

```bash
lewisboon@Lewis-PC:~$ docker inspect <container> | jq .[].Config.Env
[
  "TZ=Europe/London",
  "PATH=/opt/pihole:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
  "phpver=php",
  "PIHOLE_DOCKER_TAG=2022.01.1",
  "S6_OVERLAY_VERSION=v2.2.0.3",
  "PIHOLE_INSTALL=/etc/.pihole/automated install/basic-install.sh",
  "PHP_ENV_CONFIG=/etc/lighttpd/conf-enabled/15-fastcgi-php.conf",
  "PHP_ERROR_LOG=/var/log/lighttpd/error.log",
  "IPv6=True",
  "S6_LOGGING=0",
  "S6_KEEP_ENV=1",
  "S6_BEHAVIOUR_IF_STAGE2_FAILS=2",
  "ServerIP=0.0.0.0",
  "FTL_CMD=no-daemon",
  "DNSMASQ_USER=pihole"
]
```

### Healthcheck information

```bash
lewisboon@Lewis-PC:~$ docker inspect <container> | jq .[].State.Health
{
  "Status": "healthy",
  "FailingStreak": 0,
  "Log": [
    {
      "Start": "2022-04-21T09:42:28.159586828Z",
      "End": "2022-04-21T09:42:28.263682558Z",
      "ExitCode": 0,
      "Output": "0.0.0.0\n"
    },
    {
      "Start": "2022-04-21T09:42:58.269916211Z",
      "End": "2022-04-21T09:42:58.383608753Z",
      "ExitCode": 0,
      "Output": "0.0.0.0\n"
    },
    {
      "Start": "2022-04-21T09:43:28.389164286Z",
      "End": "2022-04-21T09:43:28.489529779Z",
      "ExitCode": 0,
      "Output": "0.0.0.0\n"
    },
    {
      "Start": "2022-04-21T09:43:58.501466553Z",
      "End": "2022-04-21T09:43:58.578390755Z",
      "ExitCode": 0,
      "Output": "0.0.0.0\n"
    },
    {
      "Start": "2022-04-21T09:44:28.585395222Z",
      "End": "2022-04-21T09:44:28.699168305Z",
      "ExitCode": 0,
      "Output": "0.0.0.0\n"
    }
  ]
}
```