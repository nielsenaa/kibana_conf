# Elasticsearch cluster monitoring setup with Metricbeat and Kibana  

Sometimes customers will need to be a superuser. You can give them this right. But first make sure that they understand the following:
- They will have full access to the cluster, including managing users
- They can change settings and bring down their instance if they do not know what they are doing
- They can drop the backup policy

Make sure they need it. Very few customers might actually need it. If not sure, ping someone who knows Elasticsearch.

To do so, get the user and password from the env in `API_AUTH_USER` and `INTERNAL_AUTH_PASSWORD`, then:

```bash
curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-8.6.1-linux-x86_64.tar.gz
mkdir metricbeat
tar xzvf metricbeat-8.6.1-linux-x86_64.tar.gz  --strip-components=1 -C metricbeat
cd metricbeat
./metricbeat modules enable elasticsearch
vim metricbeat.yml

setup.kibana:

  host: "https://kibana-bo33yt4r5zq0j7va5gpr-elasticsearch.services.clever-cloud.com:443"
  username: "kibana_custom"
  password: "testpassword"
  ...
  ...
  ...
output.elasticsearch:
  hosts: ["bo33yt4r5zq0j7va5gpr-elasticsearch.services.clever-cloud.com:443"]

  protocol: "https"
  username: "uaDvkfqeZmZBNwrYO9Cd"
  password: "V4vrvItoS7gHwajj3OG0"


./metricbeat setup -e
cd module.d
vim elasticsearch.yml

  hosts: ["bzzl7d0tnzvdntx0opfz-elasticsearch.services.clever-cloud.com:8080"]
  username: "uEgOllpY8aqADM0Ux3Ej"
  password: "CBjrUxKt3LaenKLafsNP"

cd ..
chown root metricbeat.yml
./metricbeat -e
```

You should receive a `204 No Content` response.

The user should now have the right. If they use kibana, tell them to logout and login again
