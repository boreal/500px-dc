## 500px DevOps Challenge

### Clone this repo

	git clone https://github.com/boreal/roshi-nginx-via-ansible
	
### Environment initialization

Create a hosts file in the repo root by copying and pasting the following on the command line:

	cd roshi-nginx-via-ansible
	
	cat <<EOF > hosts
	[local]
	localhost
	
	[testing]
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

### Supervising
Monit supervises the nginx, roshi-server and redis services while upstart monitors monit.

### Testing
The "test" role executes the three core functions, insert, select and delete. After the stack has been deployed the tests can be run independently using the --tag option:

	ansible-playbook -i hosts play-fullstack.yml -v -u ubuntu --sudo --private-key ~/.ssh/aws-us-east.pem  -l 500px-dc --tags test

### Configuration Management and Rolling Updates
Ansible provides a great way to implement rolling updates with the --tags option. By labeling commands with unique and common tags it makes it really easy to update very specific areas of the stack. For example, if you have a new firewall rule, add it to the rules file in files/etc/iptables.up.rules and then deploy with:

	ansible-playbook -i hosts play-fullstack.yml -v -u ubuntu --sudo --private-key ~/.ssh/aws-us-east.pem  -l testing --tags firewall

To deploy the latest version of roshi

	ansible-playbook -i hosts play-fullstack.yml -v -u ubuntu --sudo --private-key ~/.ssh/aws-us-east.pem  -l testing --tags roshi

Deploy updates to the nginx configuration

	ansible-playbook -i hosts play-fullstack.yml -v -u ubuntu --sudo --private-key ~/.ssh/aws-us-east.pem  -l testing --tags nginx_config
