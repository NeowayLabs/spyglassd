# API

There is two main concepts **probes** and **notifiers**.

## Probes

Probes are used to test connectivity (they could probe
anything, but right now it only probes networking stuff).

### Creating Networking Probe

Request:

```
POST /probes/networking
```

With body:

```json
{
    "name": "probe name useful to enrich notifications",
    "protocol": <TCP|UDP>,
    "host":"host",
    "port": 666
}
```

The **name** parameter can be used to enrich notifications with the
name of the service it represents or any other human understandable
name that will help you.

The **protocol** is which transport protocol you want to test
on the probe, allowed values are **TCP** and **UDP**.

The **host** and **port** parameters are the ones that will be
used on probing, like:

```
tcp://<host>:<port>
udp://<host>:<port>
```

If a probe fails, before triggering any notifications it will
try to ping the provided **host**, this way the notification will
already include information about general connectivity between
the two nodes, allowing you to differentiate from a connectivity
issue and a service down issue.

The response will be a 201 created on success, with a Location
header with a relative URI of the probe on it.

### Creating Kubernetes Probe

Request:

```
POST /probes/kubernetes
```

With body:

```json
{
    "name": "probe name useful to enrich notifications",
    "k8sapi": "<schema>://<host>:<port>"
}
```

The **name** parameter can be used to enrich notifications with the
name of the service it represents or any other human understandable
name that will help you.

The **k8sapi** parameter is the full URL to the Kubernetes API
that will be used to perform the probing.

This probe will get all services available on Kubernetes and perform
networking probes (TCP|UDP depending on the service).

It will probe the service according to the protocol/ports declared on
the service and it will probe all the pods that match the selector of
the service for the same protocol/ports.

This is useful to debug two kinds of problems:

* Pods networking issues (flannel or other SDN used, or baremetal)
* Service networking issues (the ClusterIP that uses iptables to do load balancing)

We had problems with both, and the problems only manifested on some hosts.
This way you can get a full notification on networking problems involving
Kubernetes/SDN.

The response will be a 201 created on success, with a Location
header with a relative URI of the probe on it.

### Getting all probes

Request:

```
GET /probes/networking
```

The response will be:

```json

    "probes" : [
        {
            "id": <relative URL>,
            "name": "probe name useful to enrich notifications",
            "protocol": <TCP|UDP>,
            "host":"host",
            "port": 666
        }
    ]
}
```

Basically an array of probes with the same info you passed when
you created each probe, with the addition of an **id** field that is
the relative URI of the individual probe, it can be used to uniquely
identify probes and delete them.

### Deleting probe

Send a request with the DELETE method to the **id** of the probe,
which is the relative URI to the probe resource.

## Notifiers

### Creating notifier

Right now the only kind of notifier available is the **slack** one.

#### Slack

Request:

```
POST /notifiers/slack
```

With body:

```json
{
    "token": "token used to authenticate",
    "channel": "channel name"
}
```

Any probe that fires will notify on all existent notifiers.
This is a very simple model but it is enough for now =).

### Getting all notifiers

Request:

```
GET /notifiers/slack
```

The response will be:

```json
{
    "notifiers" : [
        {
            "id": <relative URL>,
            "channel": ""
        }
    ]
}
```

Basically an array of notifiers with the same info you passed when
you created each notifier, except that it will not include authentication
tokens and with the addition of an **id** field that is
the relative URI of the individual notifier, it can be used to uniquely
identify probes and delete them.

### Deleting notifier

Send a request with the DELETE method to the **id** of the notifier,
which is the relative URI to the probe resource.
