Raspberry Pi (ARM 32bit) compatible Docker base image with InfluxDB, an open source database written in Go specifically to handle time series data with high availability and high performance requirements.


Running your InfluxDB image
---------------------------

Start your image binding the external port `8086` of your containers:

    docker run -d -p 8086:8086 sumglobal/rpi-influxdb

Docker containers are easy to delete. If you are serious about keeping InfluxDB data persistently, then consider adding a volume mapping to the containers `/data` folder:

    docker run -d --volume=/var/influxdb:/data -p 8086:8086 sumglobal/rpi-influxdb

Configuring your InfluxDB
-------------------------

You can use the RESTful API to talk to InfluxDB on port `8086`. Use the new `influx` cli tool to configure the database. While the container is running, you launch the tool with the following command:

  ```
  docker exec -it <influxdb-container-name> /usr/bin/influx
  ```
 which will open up the influx shell 

Initially create Database
-------------------------
Use `-e PRE_CREATE_DB="db1;db2;db3"` to create database named "db1", "db2", and "db3" on the first time the container starts automatically. Each database name is separated by `;`. For example:

```docker run -d -p 8086:8086 -e ADMIN_USER="root" -e INFLUXDB_INIT_PWD="somepassword" -e PRE_CREATE_DB="db1;db2;db3" sumglobal/rpi-influxdb:latest```

Alternatively, create a database and user with the InfluxDB shell:

```
  > CREATE DATABASE db1
  > SHOW DATABASES
  name: databases
  ---------------
  name
  db1
  > USE db1
  > CREATE USER root WITH PASSWORD 'somepassword' WITH ALL PRIVILEGES
  > GRANT ALL PRIVILEGES ON db1 TO root
  > SHOW USERS
  user  admin
  root  true
```


Example Kubernetes pod YAML
---------------------------
```
apiVersion: v1
kind: Pod
metadata:
  labels:
    name: influx
  name: influx
spec:
  containers:
  - image: sumglobal/rpi-influxdb
    name: influx
    env:
    - name: PRE_CREATE_DB
      value: metar
    ports:
    - name: influx
      containerPort: 8086
      hostPort: 8086
    volumeMounts:
      - mountPath: /data
        name: influx-persistent-storage
  volumes:
    - name: influx-persistent-storage
      persistentVolumeClaim:
        claimName: data-claim2
```

Credits
-------
[This docker image was based upon the hypriot-image](https://github.com/hypriot/influxdb)


## License

The MIT License (MIT)

Copyright (c) 2017 SUM Global Technology

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

