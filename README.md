# Ruby service for Kubernetes on Wodby

Build and run Ruby applications on Kubernetes with Wodby.

This repository defines the Wodby service manifests and operational
configuration for Ruby.

- [Browse Wodby services](https://wodby.com/services)
- [Wodby service documentation](https://wodby.com/docs/2.0/services/)
- [Service manifest reference](https://wodby.com/docs/2.0/services/template/)

## Start with a template

Use one of the source templates exposed by this service to start with
compatible build configuration and Wodby CI:

- [Ruby boilerplate](https://github.com/wodby/ruby-boilerplate)

## Wodby stacks using this service

- [Ruby application stack](https://github.com/wodby/stack-ruby)

## Service overview

| Property | Manifest configuration |
| --- | --- |
| Service name | `ruby` |
| Type | Application service |
| Versions | `4.0` by default; also available: `3.4`, `3.3` |
| Workloads | `main` (Deployment), primary; scalable |
| Containers | `ruby` using `wodby/ruby`, build target |
| Endpoints | `ruby`: HTTP 8080 (main) |
| Service links | DBMS (`db`), optional, Mail Transfer Agent (`sendmail`), optional, Redis, optional |
| Application build | Git source connection enabled; Dockerfile: `Dockerfile`; starters: Ruby boilerplate |
| Helm | chart `oci://registry-1.docker.io/wodby/ruby`; version `0.1.0` |
| Configuration | 1 integration slots |

## Use this service

Use this service through [Ruby application stack](https://github.com/wodby/stack-ruby), or reference `ruby` from a custom
Wodby stack.

A service is a reusable component and does not deploy by itself. The stack
defines its links, settings, versions, resources, and relationship to the rest
of the application.

## Maintain a custom version

1. Fork this repository.
2. Edit the service manifest and referenced files.
3. Import the repository as a [Git-backed service](https://wodby.com/docs/2.0/services/create/#create-a-git-backed-service).
4. Reference the service from a stack manifest.

Keep service, workload, container, endpoint, link, volume, config, and
derivative names stable unless dependent stacks and app-level overrides are
updated at the same time.

Validate the manifests with:

```bash
wodby service validate-manifest service.yml --org <org-id>
```

See the [service manifest reference](https://wodby.com/docs/2.0/services/template/) and the [managed services index](https://github.com/wodby/services).
