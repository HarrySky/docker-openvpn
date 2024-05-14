# Docker-OpenVPN

Dockerized OpenVPN deployment automated with Ansible

## Requirements

On host:
- `Ansible` (https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html)
- Python package `netaddr` (https://pypi.org/project/netaddr/)

On server (Linux server is expected):
- `OpenVPN` (needed to generate TLS key, on Ubuntu run `apt install openvpn`)
- `Docker` and `Docker Compose` **v2** (https://docs.docker.com/engine/install/)

## Configuration

In order to deploy OpenVPN server and configure clients - you need to create your configuration in `inventories`.

There are example files `inventories/server` and `inventories/host_vars/server.yml`.

### Inventory file

Copy `inventories/server` file to `inventories` with descriptive name, like `my_eu_vps`.

Default file looks like this:
```
server ansible_host=vpn.example.org ansible_user=root
```

First value (`server`) is the name of YAML file in `inventories/host_vars` that should be used (without `.yml` extension).

We will create it later, I recommend to name your inventory file the same as YAML file (if you only need to configure one OpenVPN server).

Now to keyworded arguments:
- put your server IP or domain as `ansible_host` value
- put SSH user for your server as `ansible_user` value
- (optional) if you use non-standard port for SSH - put your SSH port as `ansible_port` value

### Host variables

Copy `inventories/host_vars/server.yml` file to `inventories/host_vars` with descriptive name, like `my_eu_vps.yml`.

All configuration lines have comment above them explaining what they do, change it to suit your case.

## Deployment

When configuration is created - you can deploy your OpenVPN server using `setup_server.yml` playbook.

**NB!** If you did not set `root` as `ansible_user` in inventory file for your server - you need to add `-K` flag to command below (it will ask server user sudo password before running playbook)

Run following command (replace SERVER with your inventory file name in `inventories` folder):
```bash
ansible-playbook -i inventories/SERVER -e "HOSTS=all" setup_server.yml -vv
```

If everything went well - you now have running OpenVPN server, but no clients yet...

### Configuring clients

To add clients to OpenVPN server you need to add them in host variables file to `openvpn_clients` array.

When configured - you can add clients to server and create files for them using `gen_client_conf.yml` playbook.

**NB!** If you did not set `root` as `ansible_user` in inventory file for your server - you need to add `-K` flag to command below (it will ask server user sudo password before running playbook)

Run following command (replace SERVER with your inventory file name in `inventories` folder):
```bash
ansible-playbook -i inventories/SERVER -e "HOSTS=all" gen_client_conf.yml -vv
```

It should check all your clients and generate certificates and configs for new ones.

If everything went well you can get configuration from server using SCP (**replace UPPERCASED values**):
- inline `.ovpn` config: `scp ANSIBLE_USER@ANSIBLE_HOST:/opt/docker_openvpn/OPENVPN_SERVER_NAME/clients/CLIENT_NAME.ovpn ~/`
- all files `.tar.gz` archive: `scp ANSIBLE_USER@ANSIBLE_HOST:/opt/docker_openvpn/OPENVPN_SERVER_NAME/clients/CLIENT_NAME.tar.gz ~/`
