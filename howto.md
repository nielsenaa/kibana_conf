# Elasticsearch cluster monitoring setup with Metricbeat and Kibana  

To do so, get the user and password from the env in `API_AUTH_USER` and `INTERNAL_AUTH_PASSWORD`, then:

```bash
curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-8.6.1-linux-x86_64.tar.gz
mkdir metricbeat
tar xzvf metricbeat-8.6.1-linux-x86_64.tar.gz  --strip-components=1 -C metricbeat
cd metricbeat
./metricbeat modules enable elasticsearch
vim metricbeat.yml
```

```bash
setup.kibana:

  host: "https://kibana-bo33yt4r5zq0j7va5gpr-elasticsearch.services.clever-cloud.com:443"
  username: "kibana_custom_user"
  password: "kibana_custom_password"
  ...
  ...
  ...
output.elasticsearch:
  hosts: ["bo33yt4r5zq0j7va5gpr-elasticsearch.services.clever-cloud.com:443"]

  protocol: "https"
  username: "xxxxx"
  password: "yyyyy"
```

```bash
./metricbeat setup -e
cd module.d
vim elasticsearch.yml
```

```bash
  hosts: ["bzzl7d0tnzvdntx0opfz-elasticsearch.services.clever-cloud.com:8080"]
  username: "aaaaa"
  password: "bbbbb"
```

```bash
cd ..
chown root metricbeat.yml
./metricbeat -e
```

Now go to your Kibana interface, you should have data flowing in
