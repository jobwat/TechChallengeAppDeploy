# 2. using Helm to deploy

Date: 2021-06-30

## Status

Accepted

## Context

Require a deployment solution for the TechChallengeApp.

## Decision

We're going to use Helm to deploy the app.

Key elements leading to this decision:
- The app is already [available as a docker container](https://hub.docker.com/r/servian/techchallengeapp)
- The DB dependency can be handled as a helm dependency
- The DB migration can be handled by an helm hook (following [this article](https://itnext.io/database-migrations-on-kubernetes-using-helm-hooks-fb80c0d97805))


## Consequences

- Lean implementation
- Experimenting with Helm
- Faster access to the job interview
- Known caveat on the helm rollback which would constrain the solution to only roll-forward (see [Deployment strategy and rollbacks section](https://itnext.io/database-migrations-on-kubernetes-using-helm-hooks-fb80c0d97805#9128)).
  A good discussion.
