# [ Vagrant + Ansible + Docker + Spring Boot 3 + Spring Security 6.1.0 + Spring Authorization server 1.1.0 ] Playground

# Featuring:

## Spring Boot Spring Secured Docker Swarm services:

- auth-server (Spring Authorization Server implementation)

- auth-client (The entry point to authenticate & authorize users)

- auth-message-resource (Secured resource of messages)

## Other:

- nginx (Used to dispatch external requests to swarm services)

## Based on Spring Authorization Server samples

See:

- [Spring Authorization
  Server](https://github.com/spring-projects/spring-authorization-server)

- [Spring Authorization Server
  Samples](https://github.com/spring-projects/spring-authorization-server/tree/main/samples#demo-sample)

- [Spring Authorization Server Samples local
  adoc-file](file://./SpringAuthServer.adoc)

# Implementation notes to mention

There are some known Docker Swarm related bugs. Some special steps where
taken to cope with those problems:

- Swarm containers having published ports "hide" those ports from other
  nodes, participating in swarm, within user-defined overlay network.
  That is a known bug, introduced in some docker swarm versions range.
  Nginx is used to overcome this problem by publishing those ports by
  himself and proxying request to actual services.

- Nginx service has a known bug: nginx container becomes broken after
  some container based manipulations in the context of a swarm and
  replies with errors while trying to proxy a request. It becomes
  operational after restart (docker service update nginx quote=0 …
  docker service update nginx quote=1). To overcome that problem the
  ansible module \[pause\] is used after base services and before nginx
  service deploy to freeze nginx deploy for 30 seconds to allow other
  services become initialized within a swarm overlay network.

# Instruction

## Prerequisites

- VirtualBox

- Vagrant

- Ansible

- /etc/hosts lines:

<!-- -->

    127.0.0.1 auth-server
    127.0.0.1 auth-client
    127.0.0.1 auth-message-resource

## Run

    vagrant up

## Browse

    http://auth-client:8080

## Login:

user: user1

password: password