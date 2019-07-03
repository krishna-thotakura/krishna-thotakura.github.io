## Utilizing localstack for testing your microservice without using AWS resources

Here are my notes from trying to use Localstack

The localstack docker container starts up okay, but with a bunch of errors. We have been able to run it using docker compose. The other container in the docker compose is used to wait for localstack to be up, and then use terraform to initialize localstack with the mock s3, dynamodb tables, sqs queues, etc needed by our microservice.

I had some problem with getting docker compose to use a pre-defined network. Hence, i let docker compose create a bridge network(by specifying a project name) and then let the microservice and test runner containers join that network.

Using custom bridge network allowed our test runner container to hit the http endpoints of the microservice container using http://containername. But note that when the microservice was trying to talk to the containers created by docker compose, it needed to use service names as opposed to container names specified in the docker compose file.

Also, note that while all these containers can talk to each other when connected to the same custom bridge network, you still need to make note of the proxy settings specified in the container(at the unix layer or at the java application layer). These can still prevent connectivity unless you specify the docker container name or docker compose service in the no_proxy or -Dhttp.nonProxyHosts variables.

At the end, the microservice was able to use the Dynamodb port on localstack but couldnt connect to sqs port on localstack. This may be related to the startup errors seen in the localstack startup log.

 ![Running multiple containers on single host](../images/multiple-containers.png)
