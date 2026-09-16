# mongod

This Ansible role is used to provision and manage a mongodb cluster.

This project is designed to operate using [**clusterverse**](https://github.com/clusterverse/clusterverse) to manage the base infrastructure.  Please see the [README.md](https://github.com/clusterverse/clusterverse/blob/master/README.md) there for detailed instructions on its usage.

## Requirements / Compatibility
+ This role works with MongoDB 3.4 and above, including redeploying from one version to the next.  Doing so requires some compromises with the Ansible libraries: Most of `community.mongodb.*` (except `mongodb_shell`) are designed for MongoDB 4.2 and above, and do not work with lower versions.  Where possible, both the `.shell` and modern library are available, with a `when:` condition to select based on the version of MongoDB being deployed.  **_NOTE:_**  because a redeploy from `4.0` to `4.2` will be running on both `4.0` and `4.2` clusters, we must use the `shell` library when configuring clusters of `>4.2`, not `>=4.2`.
+ ansible-core >= 2.17.4 (pypi >= 10.4.0)
+ clusterverse.clusterverse >= 6.1.2
+ See [docs/EXAMPLE/Dockerfile_nonroot](https://github.com/clusterverse/clusterverse/blob/master/docs/EXAMPLE/Dockerfile_nonroot) for a full list of dependencies.

## Example
Please see the [_EXAMPLE](https://github.com/clusterverse/mongodb/tree/master/_EXAMPLE) directory in this repository for some basic configuration.  This can be copied in the root directory, and used as a starting point for your own configuration.

### Configuration
+ Cluster configuration is stored in `cluster_defs/**/cluster_vars[*].yml` files.
+ Application configuration is stored in `cluster_defs/**/app_vars[*].yml` files.
+ Regions are defined per hosttype in `hosttype_vars.<hosttype>.region_vars.<region>` (a cluster may span multiple regions).  Playbooks no longer take `-e region=...`.

### Playbook invocation
+ [deploy.yml](https://github.com/clusterverse/mongodb/tree/master/_EXAMPLE/deploy.yml) - Deploys a cluster from scratch, or repairs a cluster, or scales it up (note: not _down_).
    + e.g. `ansible-playbook -e buildenv=dev -e cloud_type=aws deploy.yml`
+ [redeploy.yml](https://github.com/clusterverse/mongodb/blob/master/_EXAMPLE/redeploy.yml) - Redeploys the cluster, replacing all the nodes entirely.
    + e.g. `ansible-playbook -e buildenv=dev -e cloud_type=aws redeploy.yml`
+ [destroy.yml](https://github.com/clusterverse/mongodb/tree/master/_EXAMPLE/destroy.yml) - Destroys the cluster,
    + e.g. `ansible-playbook -e buildenv=dev -e cloud_type=aws destroy.yml`
+ [backup_create.yml](https://github.com/clusterverse/mongodb/tree/master/_EXAMPLE/backup_create.yml) - Creates a backup of the cluster in S3,
    + e.g. `ansible-playbook -e buildenv=dev -e cloud_type=aws backup_create.yml`
+ [backup_restore.yml](https://github.com/clusterverse/mongodb/tree/master/_EXAMPLE/backup_restore.yml) - Restores a backup of the cluster from S3,
    + e.g. `ansible-playbook -e buildenv=dev -e cloud_type=aws backup_restore.yml`

### Invocation via Docker
For CI/CD, run within the included Docker container
+ `docker build -t ansibuild -f Dockerfile_nonroot .`
+ `docker run --rm --name ansibuilder_mongodb -e VAULT_PASSWORD_BUILDENV=$VAULT_PASSWORD ansibuild ansible-playbook deploy.yml -e cloud_type=aws -e buildenv=dev -vvv`

#### Options
+ `mongo_version`: The version of MongoDB to install/redeploy, e.g. `8.0.18`
