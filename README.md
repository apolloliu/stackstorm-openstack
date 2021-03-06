# OpenStack Integration Pack

StackStorm pack for OpenStack integration.

## Configuration

Copy the example configuration in [openstack.yaml.example](./openstack.yaml.example)
to `/opt/stackstorm/configs/openstack.yaml` and edit as required.

**Note** : When modifying the configuration in `/opt/stackstorm/configs/` please
           remember to tell StackStorm to load these new values by running
           `st2ctl reload --register-configs`

Pack configuration requires URLs of the OpenStack APIs and an authentication mechanism. There are
two way to supply this information:

### Token based

Supply an already acquired token and a URL pointing to the service API. Both of these need to be
acquired from the service catalog.

To acquire a token:

```sh
curl -s -X POST $OS_AUTH_URL/tokens -H "Content-Type: application/json" -d '{"auth": {"tenantName": "'"$OS_TENANT_NAME"'", "passwordCredentials": {"username": "'"$OS_USERNAME"'", "password": "'"$OS_PASSWORD"'"}}}' | python -m json.tool
```
See the [OpenStack manual](http://docs.openstack.org/api/quick-start/content/index.html#authenticate) for more information.

```yaml
---
token:
    OS_TOKEN: "xxxxxxxxxxxxxxxxxxxx"
    OS_URL: "http://{API_IP}:{API_PORT}/v2/{TENANT_ID}"
```

### Password based

Tokens are ephemeral and typical expiry is short; in those cases it might be preferred to use the password based approach.

```yaml
---
password:
    OS_AUTH_URL: # authentication url
    OS_TENANT_ID: # id of the tenant
    OS_TENANT_NAME: # name of the tenant
    OS_USERNAME: # username
    OS_PASSWORD: # password
```

## Actions

### Autogenerated actions

All actions in this pack are auto-generated. This is done using the [autogen](/etc/autogen.py)
Python script.

```sh
usage: autogen.py [-h] [--ns NAMESPACE] [--path BASE_PATH] [--debug]

optional arguments:
  -h, --help            show this help message and exit
  --ns NAMESPACE, -n NAMESPACE
  --path BASE_PATH, -p BASE_PATH
  --debug, -d
```

 * NAMESPACE - namespace of the command to generate e.g. 'server' or 'server create'
 * BASE\_PATH - the location where to write the action metadata
 * debug - will spit out extra debug information

## Sensors

This pack contains a sensor for monitoring the [Zaqar](https://wiki.openstack.org/wiki/Zaqar)
messaging service.

This is the relevant `openstack.yaml` configuration section:

```yaml
messaging:
    # The value for service type depends on how the zaqar service is
    # registered under Keystone. Please refer to Keystone for the
    # actual value for the service type.
    service_type: 'messaging'
    claim_ttl: 60
    claim_grace: 60
    queues: []
```

This will dispatch a trigger when it sees messages in the configured queues.