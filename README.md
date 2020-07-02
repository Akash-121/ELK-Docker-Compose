# ELK-Docker-Compose

At first we have to make docker compose file of ELK stack.

>sudo vim docker-compose.yml

Add these following lines (docker-compose.yml) in the file and then save and exit it.

Lets run the compose file by typing this command,

>sudo docker-compose up -d

Imanges will be pulled, downloaded, extracted and will run as container. We can see the
all container processes by checking this command:

>docker ps

Now we have to pass nginx logs to elasticsearch index from logreader machine by filebeat
and host machines logstash. For this we have to install filebeat in logreader machine by
hitting the command,

>curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.1.1-amd64.deb
>sudo dpkg -i filebeat-7.1.1-amd64.deb

Filebeat will be installed autometically and filebeat.yml file will be found under `/etc/filebeat`
directory.

Lets modify the filebeat.yml file by hitting the following command:

>sudo nano filebeat.yml

Now change the following things:

```
enabled: true
paths:
- /home/appmin/logs/lifemed.test.nginx.log
output.logstash:
# The Logstash hosts
hosts: [&quot;192.168.1.111:5044&quot;]
```
Press ctr+x, Save and close it. Now start the filebeat service by adding this command:

>sudo service filebeat start

Now we have to configure logstatsh of compose file so that it can receive logs from
fillebeat.

We mounted the volum for logstash in the following directory.

./logstash/config/:/etc/logstash/conf.d/

So we will add longstash.conf file in both directory by following this command:

>sudo nano longstash.conf

And add the follwing lines into the conf file………
```
input {
beats {
port =&gt; &quot;5044&quot;
}
}
output {
elasticsearch {
hosts =&gt; [ &quot;192.168.1.111:9200&quot; ]
index =&gt; &quot;logsview&quot;
}
}
```
Now we have to up the compose file again and restart the logstash service by restarting
the container.

>sudo docker-compose up -d

>sudo docker restart CONTAINER ID

Now check kibana under management section in elasticseach create index, One index will
be created and now create a kibana index by adding the elasticsearch index for discover
logs. In the discover section logs will be appeared.
