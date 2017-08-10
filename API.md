# API

There is two main concepts **probes** and **notifiers**.

## Probes

Probes are used to test connectivity (they could probe
anything, but right now it only probes networking stuff).

### Creating probe

Request:

```
POST /networking/probes
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

### Getting all probes

### Deleting probe

## Notifiers

### Creating notifier

### Getting all notifiers

### Deleting notifier
