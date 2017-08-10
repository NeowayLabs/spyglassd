# spyglassd

Spy your nodes from far far away =)

## Why ?

You another agent/daemon running in our cluster ?
Our use case is very simple, sometimes we have some networking problems
on cloud environments, usually caused by:

* Some routing that has been changed
* Firewalls or security groups changed
* Nothing changed, just the wrath of the gods upon you

On these cases we usually starting getting inside machines
and performing the ritual of "ncat'ing" or "telnet'ing" or
whatever you use to make a simple test to check if basic
connectivity is ok between node A that is running the service
having trouble and node B which is running the service that
node A is trying to reach.

Then we thought, why isn't computers doing this for us ?
By now, you must be thinking, but what about all those
awesome monitoring services ?

What we are aiming here is at a much more basic bootstrap
level. Sometimes we are debugging changes on networking
even before any monitoring solution has been deployed,
at these stages it would be really useful to have a really
simple piece of code checking required connectivity between nodes.

Also there is health verification performed by some cluster
orchestrator's like Kubernetes and load balancers, but these only
proves that the Kubernetes/Load Balancer is able to reach your services,
it does not proves that services that may be running on a different
cloud provider are being able to reach it (and we have some complex
cases where this is done internally through cross cloud peering).

We also have a demand on not coupling to cloud providers,
so you we can't cheat with awesome cloud dependent stuff.

So the idea is to run a very simple agent that will help us
monitor the health of our whole infrastructure at a transport
layer level.

Feel free to think that this is stupid, it probably is, but it
is the fun kind of stupid =).

## How

The solution is based on go get'ing spyglassd or running it with
docker if you are feeling lazy or using CoreOS.

spyglassd will export a very simple REST API that will allow you to
configure probes and notifications (like an alert, but simpler and
more limited). This will be the basis for all the magic.

There is no cluster awareness on the daemons, each one is a
standalone instance with no shared state and no dependency at all.

Feel free to deploy it and use as you wish, but we have a recommendation
on how you can do this:

* Add spyglassd to your node cloud bootstrap script (customdata for Azure, userdata for AWS).
* If you have automation to create nodes, automate with it the configuration of probes and notifications
* To update probes and notification, just change the previous script and run it again
* Done =)

Don't have automation ? You are already screwed, good luck =).

## Installation

TODO

## Running

There is two parameters to spyglassd:

```sh
spyglassd --storage-dir <storage_dir> --name <name>
```

The **--storage-dir** parameter is a directory where all state
will be saved. If the directory does not exist it will be created.

The **--name** parameter is the name that will be used to identify
this agent on notifications, aiding debug with a more human understandable
name instead of just an IP address.

## API

The API is [documented here](API.md)
