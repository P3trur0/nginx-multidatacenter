# Nginx active/passive in a multi-datacenter/geo-replicated scenario.

More details about the demo can be found on my Medium blog: https://medium.com/@davidecerbo/deploy-a-multi-datacenter-web-application-with-an-active-passive-setup-e95ca1270e22

The demo provides an Nginx setup to serve a passive/active service in a multi-datacenter/geo-replicated scenario.

Using this simple setup we can route our clients to the closest datacenter, depending on their location, and if a datacenter, or a
container, will not be available anymore, we can use a backup server on another datacenter/geographical location.
Obviously the responsibility to replicate data will be on charge of the other systems.


In this demo, we will start an Nginx load balancer that redirects the client that performs requests without headers to a datacenter
and the client that uses the header "X-SERVER-SELECT" with value "two" to another datacenter.

When a container on a datacenter, on entire datacenter, will not be available anymore, the load balancer will redirect the service
invocation to the backup node.


To run the demo execute:

	$ docker-compose up

and then browsing http://localhost:9090 you'll read "**Test ONE**" string.

Then try to contact using the header "**X-SERVER-SELECT**" with value "two":

	$ curl --header "X-Server-Select:two" http://localhost:9090

The result will be: "Test TWO".

Now try to stop the main server that refers to the backend "TWO"

	$ docker stop backendTwo

And re-execute the curl above:
	
	$ curl --header "X-Server-Select:two" http://localhost:9090

The result will be "Test TWO Backup". Now re-start the container:

	$ docker start backendTWO

and retry the curl:

	$ curl --header "X-Server-Select:two" http://localhost:9090

The result will be again: "Test TWO".
