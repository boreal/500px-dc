## 500px DevOps Challenge

### Environment initialization

Create a hosts file in the repo root by copying and pasting the following on the command line:

	cat <<EOF > hosts
	[local]
	localhost
	
	[500px-dc]
	dc.example.com
	EOF

Replace dc.example.com with a hostname that you can reach.

The initialization playbook creates the host_var and group_var directories along with the global variables file (all.yml).

In order to configure the server with a working mail system we need to ask for account details for either Mandrill or Sendgrid. Both offer free accounts. Optionally you can leave the values empty, but it's nice to have a working mail system for monitoring and alerting. To inititalized the ansible environment run the following command and just hit enter to accept the default values if you don't have the credentials:

	ansible-playbook -i hosts play-initialize-ansible-env.yml -v

### Deploying the service stack

To build the full service stack run the play-fullstack.yml playbook. Here's what my command looks like:

	ansible-playbook -i hosts play-fullstack.yml -v -u ubuntu --sudo --private-key ~/.ssh/aws-us-east.pem  -l 500px-dc

Reboot the server after the deployment completes error free.

### Monitoring
Monit monitors the services (nginx, roshi-server and redis) and upstart monitors monit.