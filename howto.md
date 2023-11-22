# Elasticsearch cluster monitoring setup with Metricbeat and Kibana  

Sometimes customers will need to be a superuser. You can give them this right. But first make sure that they understand the following:
- They will have full access to the cluster, including managing users
- They can change settings and bring down their instance if they do not know what they are doing
- They can drop the backup policy

Make sure they need it. Very few customers might actually need it. If not sure, ping someone who knows Elasticsearch.

To do so, get the user and password from the env in `API_AUTH_USER` and `INTERNAL_AUTH_PASSWORD`, then:

```bash
curl -XPUT -u "$API_AUTH_USER:$INTERNAL_AUTH_PASSWORD" https://api.clever-cloud.com/v4/addon-providers/es-addon/internal/elasticsearch_xxx/superuser \
  --data '{ "status": "enabled" }' -H 'Content-Type: application/json' -v
```

You should receive a `204 No Content` response.

The user should now have the right. If they use kibana, tell them to logout and login again
