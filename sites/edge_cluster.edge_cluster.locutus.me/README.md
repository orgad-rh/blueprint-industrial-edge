## How to deploy staging-edge cluster on GCP

First of all you need to have your Google Cloud Platform service account file located in the following path:

`$HOME/.gcp/osServiceAccount.json`

The OpenShift installer binary will read that file if gcp is set as a platform. From the path where the `knictl` binary is located, and in order to pull our staging-edge site and its requirements, please execute:

`knictl fetch_requirements github.com/orgad-rh/blueprint-industrial-edge/sites/edge_cluster.edge_cluster.locutus.me/`

This command will download the site blueprint definition, and all its requirements (oc, openshift-install, kustomize, etc) to the `$HOME/.kni/`. Every site will have a separate directory within that location. The next step involves the actual rendering of the manifests (site + profile + base) into one set of manifests via kustomize that we can pass to the openshift-install binary.

`knictl prepare_manifests edge_cluster.edge_cluster.locutus.me`

If everything goes well, the command will get out some instructions to deploy the cluster. It's basically asking you to run `openshift-install` binary pointing to where the final manifests created by `knictl` are:

`$HOME/.kni/edge_cluster.edge_cluster.locutus.me/requirements/openshift-install create cluster --dir=$HOME/.kni/edge_cluster.edge_cluster.locutus.me/final_manifests --log-level debug`

Wait until the deployment is completed, and you will information about console endpoint, kubeadmin password and kubeconfig path.

If you have manifests that you want to deploy as Day 2 operations located in any of the 02_cluster-addons or 03_services directories, you can deploy them running the following command:

`knictl apply_workloads edge_cluster.edge_cluster.locutus.me`

This is basically running kustomize to build and render all the manifests enabling alpha plugins, and apply them via oc/kubectl.
