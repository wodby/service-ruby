# Ruby on Wodby

What Wodby sets up for an application that runs on this service. Check it before adding a server configuration or connection settings to the code.

## How the application is started

The container runs Puma with a configuration file the image writes on every start:

```
puma -C /usr/local/etc/puma.rb
```

- Puma loads the Rack application from `/usr/src/app/config.ru` (`PUMA_RACKUP`), in the directory `/usr/src/app` (`PUMA_DIRECTORY`). The repository must have a `config.ru` at its root, or the variable must point to it.
- Puma listens on `0.0.0.0:8080`. The port is fixed in the generated configuration and is the port of the service's HTTP endpoint.
- The image does not include Puma. It must be in the application's `Gemfile`.
- A `config/puma.rb` in the repository is not loaded by this command. Set these variables on the service instead: `PUMA_WORKERS`, `PUMA_THREADS`, `PUMA_WORKER_TIMEOUT`, `PUMA_WORKER_BOOT_TIMEOUT`, `PUMA_PRELOAD_APP`, `PUMA_PRUNE_BUNDLER`, `PUMA_QUIET`, `PUMA_TAG`. A change applies with the next deployment of the service.
- The generated configuration sets Puma's environment from `PUMA_ENVIRONMENT`, and the image's default for it is `development`. The service does not set `PUMA_ENVIRONMENT` or `RACK_ENV`. An application that depends on them sets them on the service or in its Dockerfile.

## Linked services

Links to other services reach the application as environment variables. Nothing in the image reads them: the application reads them itself. Do not hardcode hosts or credentials.

| Link | Variables |
| --- | --- |
| Database (MariaDB, MySQL or PostgreSQL) | `DB_HOST`, `DB_PORT`, `DB_NAME` (also `DB_DATABASE`), `DB_USERNAME`, `DB_PASSWORD`, `DB_DRIVER` (also `DB_CONNECTION`) |
| Mail | `SMTP_HOST`, `SMTP_PORT` |
| Redis or Valkey | `REDIS_HOST`, `REDIS_PORT`, `REDIS_PASSWORD` |

All three links are optional. A variable is present only while its link exists and the linked service is enabled. The mail link carries no credentials.

## Environment

- `WODBY_HOSTS` is a JSON array of the environment's hosts, not a comma-separated string. `WODBY_PRIMARY_HOST` and `WODBY_PRIMARY_URL` are the canonical ones for links generated outside a request.
- `WODBY_APP_SERVICE_NAME` is this service's host name inside the environment. Other services reach the application at that name on port 8080.
- `WODBY_ENV_TYPE` tells a development environment from a production-like one.

## Build

The code is copied to `/usr/src/app`, the working directory. The service's own Dockerfile then runs `bundle install` when a `Gemfile` exists, and nothing else. A pipeline that passes a Dockerfile from the repository (`wodby ci build ruby -f Dockerfile`) uses that file instead, and the repository's Dockerfile then owns gem installation.

The service declares no volume of its own: files written inside the container are lost when it is replaced.

## In a development workspace

- The checkout is mounted at `/usr/src/app` and served as it is on disk.
- Workspace setup installs gems with `workspace-ruby prepare`: a frozen `bundle install`, development and test groups included. `Gemfile.lock` must be committed; preparation fails without it and never updates it. Run it again after changing dependencies.
- Gems are installed under `.wodby-workspace/` in the checkout, which is kept out of Git status without touching `.gitignore`.
- The application is started with `bundle exec puma --bind "tcp://$HOST:$PORT"`, with `HOST` and `PORT` defaulting to `0.0.0.0` and `8080`. The generated Puma configuration and the `PUMA_*` variables are not used here. `RACK_ENV` and `RAILS_ENV` are `development`.
- `WORKSPACE_RUBY_COMMAND` on the service replaces that start command.
- Puma does not watch files: a code change needs a restart of the service.
- A change to variables or linked services still needs a deployment of the environment.

## Check the result

- `curl -s -o /dev/null -w '%{http_code}' localhost:8080` from the container shows whether the application answers on the expected port.
- `printenv | grep -E '^(DB|SMTP|REDIS)_' | cut -d= -f1` lists the link variables that are present.
