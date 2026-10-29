<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
    <img src="assets/logo.svg" alt="Limen" width="300">
  </picture>
</p>

<h3 align="center">Access that expires. An audit trail you can prove.</h3>

<p align="center">
  Identity-aware access to SSH, databases, Kubernetes, message brokers and internal apps.<br>
  Open source, written in Rust, and run on your own infrastructure.
</p>

<p align="center">
  <img alt="License: Apache-2.0" src="https://img.shields.io/badge/license-Apache--2.0-14B8A6">
  <img alt="Written in Rust" src="https://img.shields.io/badge/written%20in-Rust-DEA584?logo=rust&logoColor=white">
  <img alt="Status: pre-release" src="https://img.shields.io/badge/status-pre--release-F59E0B">
  <a href="https://github.com/limen-dev/.github/blob/main/SECURITY.md"><img alt="Security: private reporting" src="https://img.shields.io/badge/security-private%20reporting-1F2A44"></a>
</p>

<br>

<table>
  <tr>
    <td width="33%" valign="top">
      <img src="assets/icons/key-round.svg" width="36" alt=""><br>
      <b>Nothing long-lived</b><br>
      <sub>Each session gets a certificate that expires in an hour, or sooner if you choose. CI jobs sign in with their own OIDC token, so no person or pipeline keeps a key or a password.</sub>
    </td>
    <td width="33%" valign="top">
      <img src="assets/icons/scroll-text.svg" width="36" alt=""><br>
      <b>An audit you can verify</b><br>
      <sub>Every session is recorded or audited into signed, hash-chained batches. One command proves nothing was edited, reordered or removed. If audit can't be written, new sessions are refused.</sub>
    </td>
    <td width="33%" valign="top">
      <img src="assets/icons/network.svg" width="36" alt=""><br>
      <b>No inbound ports</b><br>
      <sub>Workers run next to your targets and dial out. Your servers and databases stay private, and only the worker ever holds their credentials.</sub>
    </td>
  </tr>
</table>

## What it looks like

```console
$ limen login                     # a browser opens on your identity provider
$ limen ls
NAME             KIND        ACCESS AS                LABELS
orders-staging   ● postgres  limen_read, limen_write  env=staging team=payments
web-staging-1    ● ssh       ec2-user, ubuntu         env=staging

$ limen ssh web-staging-1         # a recorded session, with a one-hour certificate
$ limen db connect orders-staging # psql through Limen, no password to copy
$ limen explain orders-prod       # which rule stops me?
$ limen request orders-prod --for 30m --reason "INC-123"
```

## How it works

```mermaid
flowchart LR
  user["👤 Engineers<br>🤖 CI jobs"]
  idp["Your identity<br>provider"]
  subgraph limen ["Limen"]
    direction TB
    proxy["Proxy"]
    cp["Control plane"]
  end
  subgraph net ["Your private network"]
    direction TB
    worker["Worker"]
    targets[("Servers, databases,<br>clusters, brokers")]
  end
  s3[("Audit in S3<br>signed, chained")]
  user -- "1 · sign in" --> idp
  user == "2 · ssh, psql, kubectl" ==> proxy
  worker == "dials out" ==> proxy
  worker --> targets
  cp -. "checks policy,<br>issues certificates" .- proxy
  cp -. "writes" .-> s3
```

<p align="center"><sub>Nothing inside your network opens a port: the worker connects out to the proxy, and only the worker holds target credentials.</sub></p>

## What it reaches

<p align="center">
  <img src="assets/icons/terminal.svg" height="34" alt="SSH" title="SSH">&nbsp;&nbsp;&nbsp;
  <img src="https://cdn.simpleicons.org/postgresql" height="34" alt="PostgreSQL" title="PostgreSQL">&nbsp;&nbsp;&nbsp;
  <img src="https://cdn.simpleicons.org/mysql" height="34" alt="MySQL" title="MySQL">&nbsp;&nbsp;&nbsp;
  <img src="https://cdn.simpleicons.org/redis" height="34" alt="Redis and Valkey" title="Redis and Valkey">&nbsp;&nbsp;&nbsp;
  <img src="https://cdn.simpleicons.org/kubernetes" height="34" alt="Kubernetes" title="Kubernetes">&nbsp;&nbsp;&nbsp;
  <img src="https://cdn.simpleicons.org/mongodb" height="34" alt="MongoDB" title="MongoDB">&nbsp;&nbsp;&nbsp;
  <img src="https://cdn.simpleicons.org/opensearch" height="34" alt="OpenSearch" title="OpenSearch">&nbsp;&nbsp;&nbsp;
  <picture><source media="(prefers-color-scheme: dark)" srcset="https://cdn.simpleicons.org/elasticsearch/white"><img src="https://cdn.simpleicons.org/elasticsearch" height="34" alt="Elasticsearch" title="Elasticsearch"></picture>&nbsp;&nbsp;&nbsp;
  <img src="https://cdn.simpleicons.org/rabbitmq" height="34" alt="RabbitMQ" title="RabbitMQ">&nbsp;&nbsp;&nbsp;
  <picture><source media="(prefers-color-scheme: dark)" srcset="https://cdn.simpleicons.org/apachekafka/white"><img src="https://cdn.simpleicons.org/apachekafka" height="34" alt="Kafka" title="Kafka"></picture>&nbsp;&nbsp;&nbsp;
  <img src="assets/icons/globe.svg" height="34" alt="Internal web apps" title="Internal web apps">
</p>

<p align="center"><sub>SSH · PostgreSQL · MySQL · Redis and Valkey · Kubernetes · MongoDB · OpenSearch · Elasticsearch · RabbitMQ · Kafka · internal web apps · anything else over TCP<br>Works with your own servers and with the AWS managed services (RDS, Aurora, ElastiCache, EKS, MSK, Amazon MQ)</sub></p>

## Repositories

| Repository | What it is |
|---|---|
| **[limen](https://github.com/limen-dev/limen)** | The control plane, proxy, worker and `limen` CLI, the web console, and the docs |
| **[terraform-provider-limen](https://github.com/limen-dev/terraform-provider-limen)** | Manage resources, roles and bots from Terraform or OpenTofu, through the management API |
| **[homebrew-tap](https://github.com/limen-dev/homebrew-tap)** | `brew install limen-dev/tap/limen` |

## Where it stands

> [!IMPORTANT]
> **Limen is pre-release.** There is no tagged release yet. Everything is tested end to end, but against local stand-ins: Dex in place of a real identity provider, databases and brokers in containers, a local S3. Nothing has been verified against real AWS, Okta or Datadog. Evaluate it; don't put it in front of production systems yet.

<p align="center"><sub>
  <i>Limen</i> is Latin for threshold: the line you cross to get in.<br>
  Found a security problem? Please <a href="https://github.com/limen-dev/.github/blob/main/SECURITY.md">report it privately</a>.
</sub></p>
