# Cypress Docker Images [![CircleCI](https://circleci.com/gh/cypress-io/cypress-docker-images/tree/master.svg?style=svg)](https://circleci.com/gh/cypress-io/cypress-docker-images/tree/master)

Cypress Docker images are published to [Cypress on Docker Hub](https://hub.docker.com/u/cypress).

These images provide all of the required dependencies for running Cypress in Docker.

We build four images: click on the image name to see the available tags and versions. We provide multiple tags for various operating systems and specific browser versions. These allow you to target specific combinations you need.

| Image Name                                                     | Description                                                                        | Monthly pulls                                                                                                                         |
| -------------------------------------------------------------- | ---------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| [cypress/factory](https://hub.docker.com/r/cypress/factory/)   | A base image template which can be used with ARGs to create a custom docker image. | [![Docker Pulls](https://img.shields.io/docker/pulls/cypress/factory.svg?maxAge=604800)](https://hub.docker.com/r/cypress/factory/)   |
| [cypress/base](https://hub.docker.com/r/cypress/base/)         | All operating system dependencies, no Cypress, and no browsers.                    | [![Docker Pulls](https://img.shields.io/docker/pulls/cypress/base.svg?maxAge=604800)](https://hub.docker.com/r/cypress/base/)         |
| [cypress/browsers](https://hub.docker.com/r/cypress/browsers/) | All operating system dependencies, no Cypress, and some browsers.                  | [![Docker Pulls](https://img.shields.io/docker/pulls/cypress/browsers.svg?maxAge=604800)](https://hub.docker.com/r/cypress/browsers/) |
| [cypress/included](https://hub.docker.com/r/cypress/included/) | All operating system dependencies, Cypress, and some browsers installed globally.  | [![Docker Pulls](https://img.shields.io/docker/pulls/cypress/included.svg?maxAge=604800)](https://hub.docker.com/r/cypress/included/) |

## Tag Selection

If no tag is specified, for example `cypress/included`, then the tag `latest` is used by default: `cypress/included:latest`. It is however recommended to use a specific image tag to avoid breaking changes when new images are released, especially when they include new major versions of Node.js or Cypress.

Some examples with specific tags including an explanation of the tag meanings are:

- [cypress/base:18.16.0](https://hub.docker.com/layers/cypress/base/18.16.0/images/sha256-d00c441748e2f1b79d4002bddafe6628f9f9f5458a8a3c66697e622600dc5ad5)
    Node.js `18.16.0`
- [cypress/browsers:node-18.16.0-chrome-114.0.5735.133-1-ff-114.0.2-edge-114.0.1823.51-1](https://hub.docker.com/layers/cypress/browsers/node-18.16.0-chrome-114.0.5735.133-1-ff-114.0.2-edge-114.0.1823.51-1/images/sha256-e4c1a47c8107c37ca47398d8936743965d871c7285f58b852d5cb2658c400922)
    Node.js `18.16.0`
    Chrome `114.0.5735.133-1`
    Firefox `114.0.2`
    Edge `114.0.1823.51-1`
- [cypress/included:12.17.1](https://hub.docker.com/layers/cypress/included/12.17.1/images/sha256-5d541ff206ed28631e720f8fe98dcadf5c62f8e194c028715fb748e564c8c0cc)
    Cypress `12.17.1`

Once an image with a specific version tag (except `latest`) has been published to [Cypress on Docker Hub](https://hub.docker.com/u/cypress) it is frozen. This prevents accidental changes.

When a new version is published, an image copy with the `latest` tag is also published. This means that the Docker image selected using the `latest` tag (or selected by default if no tag is specified) will also change over time. Specify an explicit version, for example [cypress/base:18.16.0](https://hub.docker.com/layers/cypress/base/18.16.0/images/sha256-d00c441748e2f1b79d4002bddafe6628f9f9f5458a8a3c66697e622600dc5ad5), to access instead a frozen version.

>📍Cypress Docker images are offered as a convenience measure. The goal is to offer Node.js, Browser and Cypress versions to streamline running tests in CI or other non-public, sandboxed environments.
>
> Some preparations and optimizations are not included. For example, given the near infinite permutations, images are not monitored for security vulnerabilities. Additionally, once images are published they are considered immutable and cannot be patched. That means (hypothetically) older images could become more vulnerable over time.
>
> This means they should **not** be used for production deployment and security scans should be performed as-needed by users of these images.

## Docker Hub

All of the images and tags are published to [Cypress on Docker Hub](https://hub.docker.com/u/cypress) under:

- [https://hub.docker.com/r/cypress/factory](https://hub.docker.com/r/cypress/factory)
- [https://hub.docker.com/r/cypress/base](https://hub.docker.com/r/cypress/base)
- [https://hub.docker.com/r/cypress/browsers](https://hub.docker.com/r/cypress/browsers)
- [https://hub.docker.com/r/cypress/included](https://hub.docker.com/r/cypress/included)

## Cypress/Factory

Don't see the exact combination of Cypress, Node.js and browser versions you need for your test environment? Checkout our [cypress/factory](factory). You can use it to generate a custom image to fit your needs.

## Examples

Check out the [README](./included/README.md) document in the [included](./included) directory for examples of how to use `cypress/included` images. (As described above, these images include all operating system dependencies, Cypress, and some browsers installed globally.)

## Common problems

## Firefox not found

By default, the containers run with the root user. However, Firefox by design cannot run with root user, leading to failures such as:

```
Browser: firefox was not found on your system or is not supported by Cypress.
Can't run because you've entered an invalid browser name.
```

To resolve this, the container needs to run with user id `1001`.

One example using the [cypress-io/github-action](https://github.com/cypress-io/github-action)

```yml
name: E2E in custom container
on: push
jobs:
  cypress-run:
    runs-on: ubuntu-22.04
    container:
      image: cypress/browsers:node18.12.0-chrome106-ff106
      options: --user 1001
    steps:
      - uses: actions/checkout@v4
      - uses: cypress-io/github-action@v6
        with:
          browser: firefox
```

Or within a Dockerfile

```Dockerfile
# Use Cypress base image
FROM cypress/browsers:node18.12.0-chrome106-ff106

# Change to a non-root user
USER 1001

#rest of your dockerfile here
```

The [GitHub Actions Runner](https://github.com/actions/runner) creates the `/github/home` (`$HOME`) directory with non-root ownership `1001` (`runner`). Specifying this same user allows Cypress to detect and run Firefox.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md)

## License

See [LICENSE](LICENSE)
