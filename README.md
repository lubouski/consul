# Basic HashiCorp Consul installation on Vagrant with some keynotes

## HOW TO

- [Vagrantfile](Vagrantfile)

- [Starting the agents](https://www.consul.io/intro/getting-started/join.html)

```
vagrant@n1:~$ consul agent -server -bootstrap-expect=1 \
    -data-dir=/tmp/consul -node=agent-one -bind=172.20.20.10 \
    -enable-script-checks=true -config-dir=/etc/consul.d

...

vagrant@n2:~$ consul agent -data-dir=/tmp/consul -node=agent-two \
    -bind=172.20.20.11 -enable-script-checks=true -config-dir=/etc/consul.d
```
## Joining a Cluster

```
vagrant@n1:~$ consul join 172.20.20.11
Successfully joined cluster by contacting 1 nodes.

...

vagrant@n2:~$ consul members
Node       Address            Status  Type    Build  Protocol
agent-two  172.20.20.11:8301  alive   client  0.5.0  2
agent-one  172.20.20.10:8301  alive   server  0.5.0  2
```

- [Defining a Service](https://www.consul.io/intro/getting-started/services.html)
- [Registering a Health Checks](https://www.consul.io/intro/getting-started/checks.html)

```
vagrant@n2:~$ echo '{"check": {"name": "ping",
  "args": ["ping", "-c1", "google.com"], "interval": "30s"}}' \
  >/etc/consul.d/ping.json

vagrant@n2:~$ echo '{"service": {"name": "web", "tags": ["rails"], "port": 80,
  "check": {"args": ["curl", "localhost"], "interval": "10s"}}}' \
  >/etc/consul.d/web.json

...

vagrant@n2:~$ consul reload

vagrant@n1:~$ curl http://localhost:8500/v1/health/state/critical
[{"Node":"agent-two","CheckID":"service:web","Name":"Service 'web' check","Status":"critical","Notes":"","ServiceID":"web","ServiceName":"web","ServiceTags":["rails"]}]

...
```

### Additional 

- [ ] [watches](https://www.consul.io/docs/agent/watches.html)
- [ ] [event](https://www.consul.io/docs/commands/event.html)
- [ ] [exec](https://www.consul.io/docs/commands/exec.html)
- [x] [KV data](https://www.consul.io/intro/getting-started/kv.html) 


