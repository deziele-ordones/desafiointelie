# Intelie Challenge


# Proposed Problem

![alt text](https://github.com/intelie/challenge-remote-access/raw/master/example%20network%20infrastructure.png "Example network infrastructure")
The only allowed connection between Client A and Server A is via SSH. The only allowed connection between Server A and Server B is via SSH.

We need to access, from Client A and using HTTP, a service running on port 8000 of Server B.

Notes and restrictions:

- There is no direct route from Client A to Server B and there is no practical way to create one. Imagine the following scenario as an example: Server B belongs to a client's internal datacenter and we receive a VPN that allows access only to server A's SSH port.
- There is another service running on port 8000 of Server A, we should not impact this.


# Proposed solution


The suggestion would be to create SSH tunnels, the ssh protocol can encapsulate other traffic between two hosts. The ssh tunnel reaches inaccessible networks and systems.
SSH tunneling is a method of transporting arbitrary network data over an encrypted SSH connection. Can be used to add encryption to legacy applications. It can also be used to deploy virtual private networks (VPNs) and access intranet services through firewalls.

Below is an example of an SSH tunnel implementation:

![alt text](https://github.com/deziele-ordones/desafiointelie/blob/master/ssh-local2.png "Exemple network infrastructure")


In the image above, the blue host cannot reach http://192.168.0.3 but can connect via ssh to 192.168.0.2. The following ssh command executed on the blue host will allow the blue host to reach the red host.


- ssh -L 8080:192.168.0.3:80 reduser@192.168.0.2

The blue host can now open a browser, access http: // localhost: 8080, and be presented to the web page hosted at 192.168.0.3.

![alt text](https://github.com/deziele-ordones/desafiointelie/blob/master/ssh-local1.png "Exemple Network Infra")

In the image above, the blue host wants to connect to the red host on port 80, but there is a firewall in the middle that denies that. Because the blue host can connect via ssh to the red host, we can create a local port forwarding ssh tunnel to access this port.

The command on the blue host will be:

- ssh -L 8080:192.168.0.2:80 reduser@192.168.0.2

Now when the blue host opens a browser and accesses http: // localhost: 8080 they can see what the red server has on port 80.

Local Port Forwarding Syntax
This syntax for creating a local ssh port forwarding tunnel is as follows:

- ssh -L LPORT:RHOST:RPORT GATEWAY 

## Remote SSH Port Forwarding

In this scenario, we are creating a reverse ssh tunnel. Here we can start an ssh tunnel in one direction and use it to create an ssh tunnel back to the other side. This can be useful when you drop a drone computer into a network and want it to "call home." Then, when you call home, you can connect to it through the established ssh tunnel.

![alt text](https://github.com/deziele-ordones/desafiointelie/blob/master/ssh-remote.png "infra")

We are on the green host and we want ssh on the blue host. However, the firewall blocks this connection directly. Since the blue host can ssh to the green host, we can connect using this, and when the green host wants to go back to the blue host, he can go through this previously established tunnel.

The blue host starts the ssh tunnel like this:

- ssh -R 2222:localhost:22 greenuser@192.168.0.2

This opens port 2222 on the green host, which is port forwarding to port 22 on the blue host. Therefore, if the green host were ssh to itself on port 2222, it would reach the blue host.

Now the green host can ssh to blue host like this:

- ssh -p 2222 blueuser@localhost

## Using the -N Option

When using ssh, you can specify the -Nsignal that tells ssh that you do not need to send any commands over the ssh connection when it is established. This option is often used when creating tunnels, as we often do not need to be prompted.

## Autossh
The autossh command is used to add persistence to your tunnels. His job is to make sure his ssh connection is active, and if not, create it.

Here is an autossh command that you can recognize.
autossh -N -i /home/blueuser/.ssh/id_rsa -R 2222: localhost: 22 greenuser@192.168.0.3

Now when your tunnel goes down, it will automatically try to reconnect and keep trying until it succeeds. To make it persistent during a reboot, add the ssh command as a cron job.

