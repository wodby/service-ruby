# Ruby service for Kubernetes on Wodby

Build and run Ruby applications on Kubernetes with Wodby.

This repository defines the Wodby service manifests and operational
configuration for Ruby.

- [Browse Wodby services](https://wodby.com/services)
- [Wodby service documentation](https://wodby.com/docs/2.0/services/)
- [Service manifest reference](https://wodby.com/docs/2.0/services/template/)

## Start with a boilerplate

Use one of the boilerplates exposed by this service to start with compatible
build configuration and Wodby CI:

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
| Application build | Git source connection enabled; Dockerfile: `Dockerfile`; boilerplates: Ruby boilerplate |
| Helm | chart `oci://registry-1.docker.io/wodby/ruby`; version `0.2.2` |
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

## Development workspaces

`workspace-ruby prepare` requires `Gemfile.lock` and runs a frozen Bundler install,
including development dependencies. `workspace-ruby start` runs Puma or the explicit
`WORKSPACE_RUBY_COMMAND`. It sets `RAILS_ENV` and `RACK_ENV` to `development`; `HOST`
and `PORT` default to `0.0.0.0` and `8080`. Generic Ruby requires an application
restart after code changes. Rails applications should configure
`config.file_watcher = ActiveSupport::FileUpdateChecker` in development when using
shared/network storage. A custom evented watcher is not made reliable by placing
pods on the same node. No database migrations or seed commands run automatically.

Requires a runtime image declaring workspace contract version 1. Ordinary and development option tags must use matching revisions.
