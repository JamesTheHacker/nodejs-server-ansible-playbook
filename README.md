# NodeJS Server: Digital Ocean Ansible Playbook

![Network Architecutre](https://cdn-images-1.medium.com/max/800/1*TvGvibYBKv3bfMHAUrhJKg.png)

**Deploy a NodeJS application to Digital Ocean with ease!** Don't have a Digital Ocean account? [Get one here and get $10 free](https://m.do.co/c/dde4646baa31) (enough to pay for 2 servers for a month).

This play will set up a single server that is well suited to small/medium NodeJS applications on Digital Ocean. I use this same playbook for setting A/B testing for my marketing campaigns and have used it on servers that recieve large traffic loads without any issues.

The playbook is an extension to a [tutorial I write](https://codeburst.io/building-a-nodejs-web-server-with-haproxy-and-lets-encrypt-on-debian-stretch-2fbf16cfba3a) on setting up a small production ready secure NodeJS server. It performs exactly the same tasks detailed in my tutorial, except it automates the entire process using Ansible.

## Feautres:

* NodeJS 8
* HAProxy
* Automatic application deployment
* Let's Encrypt dns-01 domain verification and certificate renewal
* Daemonized NodeJS application using `systemd`
* Secured Firewall (Only ports 22, 80 and 443 are open)

## How It Works

In this playbook the node application lives under `/var/www`. The default entry point for the application is `/var/www/server.js`. If your entry point differs this can be changed in `group_vars/all.yml`.

I use `haproxy` to act like a revere proxy that load balances 3 instances of `server.createServer`, each running on port `5001`, `5002` and `5003` respectively. Ideally your application should do the same to avoid having to make configuration changes to `haproxy.j2` and `nodeapp.j2`. If you know what you're doing and would like to make modifications for a more advanced architecture you will need to edit `roles/templates/haproxy.j2` and `roles/templates/nodeapp.j2`.

HTTPS is enabled by default. SSL/TLS certificates are provided by Let's Encrypt using `dns-01` and auto certificate renewal.

## Prequisites

* Ansible 2.4
* A Digital Ocean account
* A domain name

This playbook requires Ansible version 2.4. As of writing this is currently the development version. For instructions on installing 2.4 [see here](http://docs.ansible.com/ansible/intro_installation.html#running-from-source).

## Configuring Playbook

Before running the playbook a couple of configuration options need to be set. Configuration options are stored in `group_vars/all.yml`. An example configuration is below:


```
# Domain name for your application
domain: yourdomain.com

# Git repository for your NodeJS application
app_repo: https://github.com/JamesTheHacker/nodebox-testapp

# Application entry point
entry_point: server.js

# Digital Ocean API key
api_token: 308ddfb93a32a22ef222de98496e981ef247d5c1f6fe17d76d8f9db30a7d5f23

# SSH key id (fingerprint) to existing SSH key on Digital Ocean
ssh_key_id: xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx

# Unprivileged username and password
user: web
user_pass: ChangeThisPasswordToSomethingSecure

# Your email address. Required for Let's Encrypt
email: your@email.com

# 2 character country code. Required for Let's Encrypt
country: GB
```

**Variables** 

* `domain:`: Set this to the domain name you want to link to the application. Do not include `www.`. Before running the playbook update the domains nameservers to: `ns1.digitalocean.com`, `ns2.digitalocean.com` and `ns3.digitalocean.com`.
* `app_repo`: The playbook will automatically clone an application from a git repository (Github, Bitbucket etc).
* `entry_point`: Entry point for your NodeJS application
* `api_token`: Set this to your Digital Ocean API token. You can generate a token [here](https://cloud.digitalocean.com/settings/api/tokens).
* `ssh_key_id`: This should contain the SSH fingerprint from Digital Ocean. You can find the fingerprint [here](https://cloud.digitalocean.com/settings/security)
* `user`: Unprivilaged username
* `password`: Unprivilaged user password
* `email`: A valid email address. Required for Let's Encrypt
* `country`: 2 digit country code. Required for Let's Encrypt

**Note:** I recommend using [Vault](http://docs.ansible.com/ansible/playbooks_vault.html) to encrypt `all.yml` to ensure passwords are not stored in plain text.

## Go!

Setting up the server and deploying your application is simple! Once you've set the required configuration variables simple run the following:

    cd nodejs-server-ansible-playbook
    ansible-playbook playbook.yml

If all runs successfully you should now be able to visit your domain in the browser and see your application running :)

## Help and Support

If you have an problems please file an issue. You can also catch me in the `#ansible` channel on freenode (username `jj15`), or tweet me at [@JamesTheHaxor](http://twitter.com/JamesTheHaxor)

## Contributions

I'm human, and mistakes/errors/issues happen. If you would like to fix any issues, or improve this playbook please submit a pull request and I will happily merge :)

## Shoutouts

I'd like to thank my good friend [@Radar](https://twitter.com/MichaelCRaeder) for his help and support. I would also like to thank the amazing people in the `#ansible` channel for answering my numerous questions. You guys are awesome!
