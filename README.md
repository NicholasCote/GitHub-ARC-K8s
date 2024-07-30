# GitHub-ARC-K8s
A repository for deploying the GitHub runner actions controller and scale sets to a K8s cluster

## Permissions required
The Scale Set Controller and the Scale Set itself require elevated K8s API permissions and with RBAC in place users typically 

## Procedure

### ARC Scale Set Controller
The Scale Set controller automates the scaling of self-hosted GitHub Actions runners in a Kubernetes cluster. The scale set controller enables different CRDs and APIs required for the actual scale sets that will be installed next. 

Pull the scale set controller chart down. 
`helm pull oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set-controller`

This can also be installed directly without pulling the chart down. I wanted to keep the carts I used in this repo. This will make it easier to track versions and make changes.

**Install the Chart**
`helm install -n gh-arc --create-namespace scale-set-controller gha-runner-scale-set-controller/.`

### ARC Scale Set
Pull the scale set Helm chart down.
`helm pull oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set`

This can also be installed directly without pulling the chart down. Pulling the scale set chart down allows us to change the image used for the actual runners deployed. 

**Install the Chart**
`helm install -n gh-arc --set githubConfigUrl="${GITHUB_CONFIG_URL}" --set githubConfigSecret.github_token="${GITHUB_PAT}" myrepo-runner-set gha-runner-scale-set/.`

The GITHUB_CONFIG_URL is the HTTPS address for the repository to connect the scale set to. The GITHUB_PAT is an API token that provides authentication and access to the repository. The required scopes are different depending on whether you are registering runners at the repository or organization level. 

The following is the list of required personal access token scopes for ARC runners.

    - Repository runners: repo
    - Organization runners: admin:org

In the example the deployed chart is named **myrepo-runner-set**. When configuring the GitHub actions workflow this is the name that needs to be used to define `runs-on:`
