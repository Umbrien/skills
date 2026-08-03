---
name: run-untrusted-project
description: Run, test, build, install, or serve an untrusted project in Microsandbox. Use when project code or dependency scripts must not run on the host.
---

# Run untrusted project

Inspect the project first. Do not run its commands on the host.

Use a named VM, copy the source into it, then run commands with `npx --yes microsandbox`:

```sh
npx --yes microsandbox create --name app --replace \
  --net-default deny --net-rule 'allow@registry.npmjs.org' \
  node:20-bookworm
npx --yes microsandbox copy /absolute/project/path app:/workspace
npx --yes microsandbox exec --no-tty --workdir /workspace app -- npm ci
npx --yes microsandbox exec --no-tty --workdir /workspace app -- npm run dev
```

Pick the image and commands for the project: `python`, `golang`, `rust`, or another OCI image. Dependency installs and their scripts run in the VM.

For a web service, publish and allow only its port, then bind the guest server to `0.0.0.0`:

```sh
npx --yes microsandbox create --name app --replace \
  --port 3000:3000 --net-default deny \
  --net-rule 'allow:ingress@any:tcp:3000' image
```

Keep egress deny-by-default and allow only required hosts. Do not mount the project read-write from the host. Check `npx --yes microsandbox logs app` and `npx --yes microsandbox inspect app`; remove it with `npx --yes microsandbox rm --force app` when finished.
