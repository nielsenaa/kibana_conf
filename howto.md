# Elasticsearch cluster monitoring setup with Metricbeat and Kibana  

Say you have 3 ES hosts/nodes defined as below and you want to setup metricbeat to monitor each of them, and visualize the data through a single Kibana console: 

- aaaaa-elasticsearch.services.clever-cloud.com
- bbbbb-elasticsearch.services.clever-cloud.com
- ccccc-elasticsearch.services.clever-cloud.com

First, create a simple bash file with the content below, name it something relevant like "kibana_custom_conf" and host it somewhere so it is accessible (we strongly recommend Cellar.)

kibana_custom_conf file content:

```bash
#!/bin/bash -l

cat <<EOF >config/kibana.yml
server.port: 8080
server.host: 0.0.0.0
server.name: "kibana-${ES_ADDON_HOST}"

server.publicBaseUrl: "https://kibana-${ES_ADDON_HOST}"

elasticsearch.hosts: ["https://${ES_ADDON_HOST}"]
elasticsearch.username: "${ES_ADDON_KIBANA_USER}"
elasticsearch.password: "${ES_ADDON_KIBANA_PASSWORD}"

xpack.security.authc.providers:
  basic.basic1:
    order: 0
    description: "For Basic Auth reqs"
  saml.saml1:
    order: 1
    realm: saml1
    description: "Login in with Clever Cloud"

xpack.security.public:
        protocol: https
        hostname: "kibana-${ES_ADDON_HOST}"
        port: 443

xpack.reporting.enabled: true
xpack.security.secureCookies: true
telemetry.enabled: false
telemetry.optIn: false
EOF

if [[ -n "${CC_KIBANA_SECURITY_ENCRYPTION_KEY}" ]]; then
    echo "xpack.security.encryptionKey: \"${CC_KIBANA_SECURITY_ENCRYPTION_KEY}\"" >> config/kibana.yml
fi

if [[ -n "${CC_KIBANA_REPORTING_ENCRYPTION_KEY}" ]]; then
    echo "xpack.reporting.encryptionKey: \"${CC_KIBANA_REPORTING_ENCRYPTION_KEY}\"" >> config/kibana.yml
fi

if [[ -n "${CC_KIBANA_OBJECTS_ENCRYPTION_KEY}" ]]; then
    echo "xpack.encryptedSavedObjects.encryptionKey: \"${CC_KIBANA_OBJECTS_ENCRYPTION_KEY}\"" >> config/kibana.yml
fi

# Kibana doesn't create it automatically
# -p to ignore errors if it already exists
mkdir -p data
```

Now create a new Kibana app on a new ES addon. This will be the ES and Kibana that will collect all data coming from metricbeat, and will also be the access point and interface where you will be able to do all your monitoring and enjoy all your data discovery, trnasformation etc...

Before starting your Kibana app, set the CC_PRE_RUN_HOOK env value in the configuration of your Kibana app to :

```bash
https://path_to_your_custom_config_file/kibana_custom_conf
```

Now that your Kibana is ready 

On each node
```bash
curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-8.6.1-linux-x86_64.tar.gz
mkdir metricbeat
tar xzvf metricbeat-8.6.1-linux-x86_64.tar.gz  --strip-components=1 -C metricbeat
cd metricbeat
./metricbeat modules enable elasticsearch
vim metricbeat.yml
```

Update metricbeat.yml with the following content :

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

Update elasticsearch.yml with the following content :

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
