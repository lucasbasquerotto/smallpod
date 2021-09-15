# Minimal Pod Repository

This repository is an example of a minimal pod layer repository.

It has a `docker-compose` file with an `nginx` service that runs on port `8080` in the host and loads the static HTML files from the `html` directory.

Run `docker-compose up -d` and access `localhost:8080`. There, you can see links to other pages.

To access the page `dynamic.html`, it first must be generated using the pod context file `ctx.yml`.

An example of a [project environment base file](http://github.com/lucasbasquerotto/ctl#project-environment-base-file) to deploy this service (both locally as well as remotely) can be seen at https://github.com/lucasbasquerotto/env-base/blob/master/examples/minimal.yml

The configuration about this pod in the environment base file is defined as:

```yaml
# This is an excerpt of the complete file
pods:
  minimal:
    repo: "smallpod"
    ctx: "ctx.yml"
    root: true
    fast_prepare: true
    params:
      dynamic_page_title: "My Dynamic Page"
repos:
  smallpod:
    src: "https://github.com/lucasbasquerotto/smallpod.git"
    version: "main"
```

Deploy the project defining a [project environment file](http://github.com/lucasbasquerotto/ctl#project-environment-file) as:

```yaml
name: "<< project_name >>"
ctxs: ["<<cloud_context>>"]
env:
  repo:
    src: "https://github.com/lucasbasquerotto/env-base.git"
    version: "master"
  repo_dir: "env-base"
  file: "examples/minimal.yml"
params:
  domain: "<<your_dns_zone>>"
  dns_record: "<<dns_subdomain>>"
credentials:
  digital_ocean_api_token: "<<digital_ocean_api_token>>"
  cloudflare_email: "<<cloudflare_email>>"
  cloudflare_token: "<<cloudflare_token>>"
```

Replace the variables between `<<` and `>>` with the data specific to your environment.

Example for a local deployment:

```yaml
name: "my-local-demo"
ctxs: ["local"]
env:
  repo:
    src: "https://github.com/lucasbasquerotto/env-base.git"
    version: "master"
  repo_dir: "env-base"
  file: "examples/minimal.yml"
```

If deploying locally (using the `local` cloud context as defined in the project environment base file), you can go to the pod repository directory (in your local machine) and run `docker-compose up -d`. Then, you can access the website at `localhost:8080`.

Example for a remote deployment:

```yaml
name: "my-remote-demo"
ctxs: ["remote"]
env:
  repo:
    src: "https://github.com/lucasbasquerotto/env-base.git"
    version: "master"
  repo_dir: "env-base"
  file: "examples/minimal.yml"
params:
  domain: "mydomain.com"
  dns_record: "subdomain"
credentials:
  digital_ocean_api_token: "digital_ocean-t0k3n"
  cloudflare_email: "email@mydomain.com"
  cloudflare_token: "cloudflare-t0k3n"
```

When deploying remotely (using the `remote` cloud context as defined in the project environment base file), a Droplet will be created in Digital Ocean with the port 8080 opened to everyone, and `A` and `AAAA` DNS records will be created in Cloudflare pointing to the public IP of the Droplet created. After the deployment, you can access the website at `subdomain.mydomain.com:8080`.
