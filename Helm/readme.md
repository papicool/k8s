# HELM - 

Helm is the package manager for kubernetes
## What is Helm?

package manager means:
- single command installation
- Dependencies are resolved
- Doesn't require deep understanding of software
- Easier update, upgrate and removal

### How Helm manages packages?
- Helm Chart

This is the definition of a k8s application. Helm use this information along with a config to instantiate a released object

- least invasive change 

in the event that there is a change to release, helm will only change what has been updated since last released

- Running vs Desired state 

if a helm chart has been released, Helm can determine what the current state of the environnement is vs the desired state and make change as needed

- Release Tracking 

Helm versions Releases. This means that if something goes wrong. The release can be rolled back to a previous version

### What Helm can do ?
`helm install`: Single command install

`helm status` : provide insight for releases

`helm upgrade` : perform simple updates/upgrades

`helm rollback` : provide ability to rollback

simple deployment with a single command

`helm uninstall` : single command uninstall