## 500px DevOps Challenge

### Environment initialization

In order to configure the server with a working mail system we need to ask for account details for either Mandrill or Sendgrid. Both offfer free accounts. Optionally you can leave the values empty, but it's nice to have a working mail system for monitoring and alerting. To inititalized the ansible environment run the following command:

	ansible-playbook -i hosts play-initialize-ansible-env.yml -v

### Deploying the service stack

	ansible-playbook -i hosts play-fullstack.yml -v

