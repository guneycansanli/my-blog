---
title: "Puppet HA (puppet ca & compilers) in Docker with Nginx Load Balancer"
layout: post
date: 2025-07-20 11:00
image: ../assets/images/puppet-ha-docker/main.jpg
headerImage: true
tag:
    - linux
    - puppet
    - docker
    - nginx
category: blog
author: guneycansanli
description: Puppet HA (puppet ca & compilers) in Docker with Nginx Load Balancer
---

# Introduction

If you’re starting with Puppet and Docker, you might wonder: Should Puppet manage Docker, or run Puppet inside Docker containers? The answer is—you can do both!

Using Puppet in Docker lets you easily package and run Puppet tools like Puppet Server and PuppetDB as containers. This setup is great for quickly building and testing environments, which you can then apply to production.

In this post, I’ll show you how to create containerized Puppet environments to simplify your development and deployment.

---

## 1. Prerequisites

- Docker & Docker Compose installed
- Basic familiarity with Git, Puppet, and Docker

---

## 2. Clone the Puppet HA Sample Repo

You can find the official aricle based on that structure in [How to Run Puppet in Docker](https://www.puppet.com/blog/puppet-docker). 
The **voxpupuli/crafty** repository has good examples with diffrent type of use cases. We will use the repo but there are manual steps that We need to follow. 

```bash
git clone https://github.com/voxpupuli/crafty.git
cd puppet/ha
```

## 3. Analysis of The Repo

We will focus puppet ha subfolder, It includes everything what We need to use. 

```bash
guneycansanli ~/Development/crafty/puppet/ha [main] $ tree
.
├── clean.sh
├── compose.yaml
├── nginx-ssl
├── nginx.conf
├── puppet.conf
└── README.md
```

![puppet-ha][1]


### Puppet Certificate Authority (CA)

- **Image**: `ghcr.io/voxpupuli/puppetserver:8.7.0-latest`
- **Role**: Acts as the Certificate Authority for signing agent certificates.
- **Key Environment Variables**:
  - `PUPPETSERVER_HOSTNAME`: Sets the server's hostname in SSL certificates.
  - `USE_PUPPETDB`: Disables PuppetDB integration (`false`).
  - `CA_ENABLED`: Enables the internal CA (`true`).
  - `CA_ALLOW_SUBJECT_ALT_NAMES`: Allows certificates with Subject Alternative Names (`true`).

Note: In my tests I had issues with mismatch hostname in CA server 

```bash
root@puppet-ca:/# openssl s_client -connect puppet-ca:8140 -showcerts </dev/null 2>/dev/null | openssl x509 -noout -text | grep -A2 "Subject Alternative Name"
            X509v3 Subject Alternative Name: 
                DNS:puppet, DNS:puppet-ca.local
    Signature Algorithm: sha256WithRSAEncryption

root@puppet-ca:/# puppetserver ca clean --certname puppet-ca --config /etc/puppetlabs/puppet/puppet.conf
Fatal error when running action 'clean'
  Error: Failed connecting to https://puppet-ca:8140/puppet-ca/v1/certificate_status/
  Root cause: SSL_connect returned=1 errno=0 peeraddr=127.0.0.1:8140 state=error: certificate verify failed (hostname mismatch)
```

This means that Puppet is trying to connect to https://puppet-ca:8140, but the certificate does not include puppet-ca as a valid hostname in its SAN.
We can maybe edit out **puppet.conf** to fix that or We can add **CERTNAME: puppet-ca** and **DNS_ALT_NAMES: puppet-ca** to our CA server envrionment variables. 

```bash
root@puppet-ca:/# openssl x509 -in /etc/puppetlabs/puppet/ssl/certs/puppet-ca.pem -noout -text | grep -A2 "Subject Alternative Name"
            X509v3 Subject Alternative Name: 
                DNS:puppet-ca, DNS:puppet-ca
    Signature Algorithm: sha256WithRSAEncryption
```

Above output verify DNS Altname is **puppet-ca**


### Puppet Compilers (compiler-001, compiler-002, compiler-003)

- **Image**: `ghcr.io/voxpupuli/puppetserver:8.7.0-latest`
- **Role**: Compile and serve Puppet code to agents.
- **Key Environment Variables**:
  - `PUPPETSERVER_HOSTNAME`: Unique hostname for each compiler.
  - `USE_PUPPETDB`: Disables PuppetDB integration (`false`).
  - `CA_ENABLED`: Disables internal CA (`false`).
  - `CA_HOSTNAME`: Specifies the CA's hostname.
  - `DNS_ALT_NAMES`: Sets additional DNS names for SSL certificates.

### Puppet Load Balancer (puppet-lb)

- **Image**: `nginx:1.29.0`
- **Role**: Load balances traffic to Puppet compilers.
- **Configuration**:
  - Exposes port `8140` for Puppet traffic.
  - Mounts custom `nginx.conf` and SSL certificates for secure communication.

### Testing Service

- **Image**: `ghcr.io/betadots/pdc:latest`
- **Role**: Provides a containerized environment for testing Puppet code.
- **Configuration**:
  - Mounts `puppet.conf` for agent configuration.
  - Mounts `agent-ssl` for SSL certificates.

# Summary Table

| Service        | Image                                             | Role                           | Key Environment Variables                                                       |
|----------------|--------------------------------------------------|--------------------------------|---------------------------------------------------------------------------------|
| Puppet CA      | `ghcr.io/voxpupuli/puppetserver:8.7.0-latest`   | Certificate Authority          | `PUPPETSERVER_HOSTNAME`, `USE_PUPPETDB`, `CA_ENABLED`, `CA_ALLOW_SUBJECT_ALT_NAMES` |
| Puppet Compiler| `ghcr.io/voxpupuli/puppetserver:8.7.0-latest`   | Compile and serve Puppet code  | `PUPPETSERVER_HOSTNAME`, `USE_PUPPETDB`, `CA_ENABLED`, `CA_HOSTNAME`, `DNS_ALT_NAMES` |
| Puppet LB      | `nginx:1.29.0`                                   | Load balancing                 | Exposes port `8140`, mounts `nginx.conf` and SSL certificates                   |
| Crafty HA Test | `ghcr.io/betadots/pdc:latest`                    | Testing Puppet code            | Mounts `puppet.conf` and `agent-ssl`                                           |


---

## 3. Start the Puppet compose

```shell
docker compose --profile puppet up -d

# check if compose is ready
docker compose ps
```

![puppet-ha][2]

![puppet-ha][3]

![puppet-ha][4]



### Generate the certificate for the LB

Once this compose file is up and running execute create CA and also using SSL & CA keys fro nginx:

```shell
docker exec -ti ha-ca-1 puppetserver ca generate --certname puppet-lb --subject-alt-names puppet,localhost

cp server-data/puppet-ca-ssl/certs/ca.pem               nginx-ssl/ca.pem
cp server-data/puppet-ca-ssl/certs/puppet-lb.pem        nginx-ssl/cert_puppet.pem
cp server-data/puppet-ca-ssl/private_keys/puppet-lb.pem nginx-ssl/priv_puppet.pem
```

![puppet-ha][5]

![puppet-ha][6]

## Start the LB compose

```shell
docker compose --profile lb up -d
```

![puppet-ha][7]

## Test an agent

```shell
docker compose --profile test run testing puppet agent -t
```

![puppet-ha][8]

## Clean up

```shell
./clean.sh
```


---

Thanks for reading!

—

**Guneycan Sanli**


[1]: ../assets/images/puppet-ha-docker/puppet-ha-1.jpg
[2]: ../assets/images/puppet-ha-docker/puppet-ha-2.jpg
[3]: ../assets/images/puppet-ha-docker/puppet-ha-3.jpg
[4]: ../assets/images/puppet-ha-docker/puppet-ha-4.jpg
[5]: ../assets/images/puppet-ha-docker/puppet-ha-5.jpg
[6]: ../assets/images/puppet-ha-docker/puppet-ha-6.jpg
[7]: ../assets/images/puppet-ha-docker/puppet-ha-7.jpg
[8]: ../assets/images/puppet-ha-docker/puppet-ha-8.jpg




