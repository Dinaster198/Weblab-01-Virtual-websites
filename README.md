# Web Lab 1: Virtual Hosts

## Usage

Run:

   vagrant up

There will be two virtual machines:

- `dns`

- `web`

## Contents

Contains the following files:

- `README.md`, this file

- `Vagrantfile`, Vagrant project

- `apache2-provision.sh` with the web server provisioning

- `dns-provision.sh` with the DNS server provisioning

Contains the following subdirectories:

- `files/apolo.olimpo.test` document root for this website.

- `files/atenea.olimpo.test` document root for this website.

- `files/mercurio.olimpo.test` web server configuration

- `files/ns1.olimpo.test` DNS server configuration

## How to Start the Project

### 1. Requirements

Make sure the following software is installed:
- VirtualBox
- Vagrant

### 2. Start the Virtual Machines

Open a terminal in the project directory, where the `Vagrantfile` is located.

Start both virtual machines:

```bash
vagrant up
```

This command starts the following machines:

| Machine | Hostname | IP Address | Service |
|---|---|---|---|
| dns | ns1.olimpo.test | 192.168.57.10 | Bind9 |
| web | mercurio.olimpo.test | 192.168.57.11 | Apache2 |

Vagrant automatically runs the provisioning scripts to configure the services.

### 3. Check the Virtual Machines

To check their current status:

```bash
vagrant status
```

Both machines should be running.

### 4. Access the Virtual Machines

To access the DNS server:

```bash
vagrant ssh dns
```

To access the web server:

```bash
vagrant ssh web
```

To leave a virtual machine:

```bash
exit
```

### 5. Access the Websites

Open the following URLs in your browser:

- http://apolo.olimpo.test
- http://atenea.olimpo.test

Make sure the following entries are present in the host machine's `hosts` file:

```text
192.168.57.11 apolo.olimpo.test
192.168.57.11 atenea.olimpo.test
```

### 6. Stop the Project

To shut down both virtual machines:

```bash
vagrant halt
```

To start them again later, run:

```bash
vagrant up
```

**Note:** If you modify the provisioning scripts or need to run them again, use:

```bash
vagrant provision
```
