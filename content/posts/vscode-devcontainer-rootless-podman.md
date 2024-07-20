+++
title = 'Vscode DevContainers with Rootless Podman'
date = 2024-07-20T17:51:11-04:00
draft = false
+++

# VSCode DevContainers with Rootless Podman on Fedora Linux
I recently spent some time getting VSCode DevContainers working with rootless Podman. I'm not going to go through the process of getting Podman installed and configured on your system, or installing VSCode and the DevContainers extension. The key issues are in the `devcontainer.json` which requires some hoop-jumping to get the settings to match up right, resulting in the configuration here:

```json
// devcontainer.json
...
"remoteUser": "vscode",
"workspaceMount":"", // fixed below in the --volume argument  - enables rootless podman
"runArgs": [
    "--userns=keep-id", // retain the user GID/PID
    "--memory=0", // optional, but I found issues with running out of heap memory while building the container
    "--cpus=0", // optional, but will enable the container access to all your cores
    "--volume=${localWorkspaceFolder}:/workspaces/${localWorkspaceFolderBasename}:Z" // explicitly mount the workspace folder
],
"containerEnv": {
    "HOME": "/home/vscode"
}
```

I also edited my VSCode `settings.json` to use `podman` and `podman-compose` instead of docker:

```json
"dev.containers.dockerComposePath": "podman-compose"
"dev.containers.dockerPath": "podman"
```