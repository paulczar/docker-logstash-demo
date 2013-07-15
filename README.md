# Logstash Demo
_powered by docker_

## Requirements

* Docker
* Internet

## Launch the Demo


```
$ git clone https://github.com/paulczar/docker-logstash-demo.git
$ cd docker-logstash-demo
$ IFACE=wlan0 ./run_demo
```

_set IFACE to be the interface you wish to expose services to. will default to eth0_

## Output should look like

_the first run can take some time while all the docker containers are downloaded.   After the first run the only thing that takes time is the downloading of the sample data_


```
$ IFACE=wlan0 ./run_demo
ElasticSearch: a8d19d1044ae http://172.16.42.8:9200/_plugin/head/
ElasticSearch2: 40d9db4e5c68 http://172.16.42.9:9200/_plugin/head/
Uploading context 102400 bytes
Logstash Syslog: 8bdbca2a67cb tcp://192.168.50.129:49278
Logstash Apache: 8bdbca2a67cb tcp://192.168.50.129:49277
Kibana: d7f3fe184438 http://192.168.50.129:49279
send some sample syslog data:
tail -100 /var/log/syslog | nc 192.168.50.129 49278
send some sample apache data:
tail -100 /var/log/apache2/apache.log | nc 192.168.50.129 49277
```

_note Kibana and Logstash expose ports via your IP to the outside world_

## Using the Demo

It will take a few moments for logstash to spin up and start processing logs.   Logstash will grab the $plunk sample apache logs from [here](http://docs.splunk.com/Documentation/Storm/Storm/User/Adddatatoyourproject).  They may be up to a few weeks old,  so you may need to go hunting in kibana to find them.  

Note it also gives you a sample command to push the most recent 100 lines of your syslog file.  This can be fun for a live demo as you can ask people in the room to send their own syslogs at you.

You can explore the ElasticSearch and Kibana URLs given to you in the output.   Note that ElasticSearch is not exposed ... the is good security practice.   Instead nginx which is running kibana proxies the queries through to it.

You can upload the file `apache.json` to kibana to give you a more interesting dashboard for the apache logs.    

## What is going on??

If you're curious as to the workings of this  ...  explore the following files

* `Dockerfile`
* `run_demo`
* `logstash.conf.template`
* `patterns/splunkfix`

## Finished?  Clean up

This will kill and delete any docker containers ...  

```
docker kill `docker ps -a | awk '{print $1}' | xargs`
docker rm `docker ps -a | awk '{print $1}' | xargs`
```

## lolsplunkwat?

despite the splunk sample apache logs not being NCSA common log format compliant ( no timezone in the timestamp field), and having occasional lines starting with `i` like some n00b intern hand copy and pasting logs in `vim` and forgetting he's already hit the `i` for insert, they're a pretty good source of reasonably frequently updated logs.

The misformatting log the logs actually gives us reason to show off the power of the `grok` and `date` filters ( see `patterns/splunkfix`) which is cool.

# Authors

Paul Czarkowski - paul at paulcz dot net

# License

Copyright [2013] [Paul Czarkowski]

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.