Steps to Add a New SSH Agent in Jenkins

1. Create a user named jenkins on the master and agent machines.

2. Add the public key of the Jenkins master to: /home/jenkins/.ssh/authorized_keys on the agent.

3. Add the host public key (/etc/ssh/<key.pub>) of the agent to: /home/jenkins/.ssh/known_hosts on the master machine.
    Usually this is done using:
	`ssh-keyscan -H <agent-ip>`

4. In Jenkins Console: Manage Jenkins → Credentials
	Create credentials of type: SSH Username with private key
	using:
	the agent username (jenkins)
	and the private key of the master
	Create a new node in Jenkins and configure:
	Agent IP
	Remote directory
	Credentials
	Launch method = SSH