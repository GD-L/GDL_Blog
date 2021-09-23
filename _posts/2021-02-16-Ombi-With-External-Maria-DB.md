---
layout: post
title:  "Using Ombi with an External DB"
date:   2021-02-16
tags: [Docker, Ombi]
category: [Homelab]
---

The other day the SQLITE database in my [Ombi](https://ombi.io/) setup got locked out, which seems to be a somewhat common occurrence. And because I wasn't paying attention to where I had initially stored my database I wasn't able to do a proper [database migration](https://docs.ombi.app/guides/migrating-databases/)
<!--more-->
According to the [docs](https://docs.ombi.app/info/alternate-databases/) it's pretty straight forward. I copied the `database.json` config below from [Alternate Databse Options](https://docs.ombi.app/info/alternate-databases/) and pointed the internal config to the file I created.

```json
{
  "OmbiDatabase": {
    "Type": "MySQL",
    "ConnectionString": "Server=localhost;Port=3306;Database=Ombi;User=ombi;Password=ombi"
  },
  "SettingsDatabase": {
    "Type": "MySQL",
    "ConnectionString": "Server=localhost;Port=3306;Database=Ombi;User=ombi;Password=ombi"
  },
  "ExternalDatabase": {
    "Type": "MySQL",
    "ConnectionString": "Server=localhost;Port=3306;Database=Ombi;User=ombi;Password=ombi"
  }
}
```

```yaml
version: '3.1'
services:
  ombi:
    image: linuxserver/ombi:development
    network_mode: host
    container_name: 'ombi'
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/New_York
      #- BASE_URL=/ombi #optional
    volumes:
      - ./data:/config
      - ./data/mariadb/database.json:/config/database.json
    ports:
      - "3579:3579"
    restart: unless-stopped
    depends_on: mariadb
  
  mariadb:
    image: mariadb:latest
    container_name: ombi-mariadb
    restart: unless-stopped
    volumes:
      - ./data/mariadb:/config
    ports:
      - '3306:3306'
    env_file: ./data/mariadb/maria_envs.env
```


MariaDB Envs:
```env
MYSQL_ROOT_PASSWORD = PASSWORD
MYSQL_DATABASE=Ombi
MYSQL_USER=ombi
MYSQL_PASSWORD=ombi
PGID=1000
PUID=1000
TZ=America/New_York
```

After bringing up the stack I continued executing the **Single Database Permissions** listed in the steps.
```sql
CREATE DATABASE IF NOT EXISTS `Ombi` DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
CREATE USER 'ombi'@'%' IDENTIFIED BY 'ombi';
GRANT ALL PRIVILEGES ON `Ombi`.* TO 'ombi'@'%' WITH GRANT OPTION;
```

After this I had a nice new clean install of Ombi using MariaDB.
