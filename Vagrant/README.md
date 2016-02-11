Vagrant Environment
===================

The Vagrant environment consists of the following nodes  
client - run code from
oscontroller  
oshaproxy  
logstash  


Spin up environment
````
vagrant up
````

After all nodes are spun up connect to the client machine in order to provision all of the nodes.
````
vagrant ssh client
````
