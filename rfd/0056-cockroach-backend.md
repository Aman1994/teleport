---
authors: Jim Bishopp (jim@goteleport.com)
state: draft
---

# RFD 56 - CockroachDB Backend

## What

A CockroachDB backend extends Teleport with the ability to store data
in a self-hosted CockroachDB cluster.

## Why

Supporting additional backends such as CockroachDB reduces onboarding time
for Teleport customers by allowing them to use existing infrastructure.

## Scope

This RFD focuses on implementing a backend for a self-hosted CockroachDB cluster.

Requirements:

- Prioritize self-hosted CockroachDB.
- Implementation should support CockroachDB node failures by automatically
  reconnecting to another node when a connected node becomes unavailable.
- Design should consider extensibility to support other SQL databases 
  such as PostgreSQL and MySQL.
- Design should utilize a dialer/connector abstraction when connecting to
  the database to allow the implementation to be extended to support SQL
  databases hosted by a cloud provider.

## Authentication

The backend will require mutual authentication when connecting to the CockroachDB 
cluster. This requires users to generate client certificates and configure
Teleport to use them. Users can generate client certificates by following 
the steps outlined in the [CockroachDB Authentication Guide](https://www.cockroachlabs.com/docs/stable/authentication.html#client-authentication).
Once generated, paths to the certificates can be added to the Teleport
configuration file's storage section.

## UX

Teleport users enable the CockroachDB Backend by setting the storage
type in the Teleport configuration file to `cockroach`, setting a list 
of accessible CockroachDB nodes, and adding paths to the location of 
CockroachDB client certificates.

```yaml
teleport:
  storage:
    type: cockroach

    # List of CockroachDB nodes to connect to:
    nodes: ["172.17.0.1:26257", "172.17.0.2:26257", "172.17.0.3:26257"]

	 # Set database where Teleport will store its data.
	 database: "teleport"

    # Required path to TLS CA, client certificate, and client key files 
    # to connect to CockroachDB.
    #
    # To create these, follow
    # https://www.cockroachlabs.com/docs/stable/authentication.html#client-authentication
    tls_cert_file: /var/lib/teleport/cockroach.root.crt
    tls_key_file: /var/lib/teleport/cockroach.root.key
    tls_ca_file: /var/lib/teleport/cockroach.ca.crt
```


