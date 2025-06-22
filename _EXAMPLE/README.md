# mongodb-ansible

This Ansible role is used to provision and manage mongodb cluster.

This project can operate in isolation, but is designed to operate using [clusterverse](https://github.com/dseeley/clusterverse) to manage the base infrastructure.  Please see the [README.md](https://github.com/dseeley/clusterverse/blob/master/README.md) there for instructions on deployment.


## EXAMPLE:

### Configuration
+ Cluster configuration is stored in `cluster_defs/**/cluster_vars[*].yml` files.
+ Application configuration is stored in `cluster_defs/**/app_vars[*].yml` files.

### Playbook execution
+ `cluster.yml` is used to create a new cluser, or repair an existing one.
+ `redeploy.yml` is used to replace all the nodes of a cluster, e.g. to upgrade the version of MongoDB, or the underlying OS.
+ `backup_create.yml` is used to create a backup of the cluster in S3
+ `backup_restore.yml` is used to restore a backup of the cluster from S3


#### Options
+ `mongo_version`: The version of MongoDB to install/redeploy, e.g. `3.4.24`

### Invocation
To create a cluster:
+ `ansible-playbook cluster.yml -e cloud_type=aws -e region=eu-west-1 -e buildenv=dev`

To redeploy a cluster:
+ `ansible-playbook redeploy.yml -e cloud_type=aws -e region=eu-west-1 -e buildenv=dev -e canary=none`

To delete a cluster:
+ `ansible-playbook cluster.yml -e cloud_type=aws -e region=eu-west-1 -e buildenv=dev --tags=clusterverse_clean -e clean=_all_`

To create a backup:
+ `ansible-playbook backup_create.yml -e cloud_type=aws -e region=eu-west-1 -e buildenv=dev`

To restore a backup:
+ `ansible-playbook backup_restore.yml -e cloud_type=aws -e region=eu-west-1 -e buildenv=dev`


### Invocation via Docker
For CI/CD, run within the included Docker container
+ `docker build -t ansibuild .`
+ `docker run --rm --name ansibuilder_monogodb -e VAULT_PASSWORD_BUILDENV=$VAULT_PASSWORD ansibuild ansible-playbook cluster.yml -e cloud_type=aws -e region=eu-west-1 -e buildenv=dev -vvv`