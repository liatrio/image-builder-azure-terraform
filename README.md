## What is the image for?
The intended purpose of this image is for it to be used as a Jenkins agent. By using the installed features the user is able to create Jenkins pipelines that can trigger Terraform scripts to deploy to an Azure environment. Additionally, Terragrunt gives the ability to use extra tools for Terraform while TFLint gives us a Terraform linter. An example of using this image as a Jenkins agent via [Kubernetes](https://plugins.jenkins.io/kubernetes/) can be seen below. 

First, an example of configuring the pod template in yaml to create the agent.

```yaml
jenkins:
  clouds:
    - kubernetes:
        name: "kubernetes"
        templates:
          - name: "image-builder-azure-terraform"
            label: "image-builder-azure-terraform"
            nodeUsageMode: NORMAL
            containers:
              - name: "image-azure-terraform"
                image: "ghcr.io/liatrio/image-builder-azure-terraform:${builder_images_version}"
```
And then specifying the agent in the Jenkinsfile for an example step.

```jenkins
stage('Build') {
  agent {
    label "image-builder-azure-terraform"
  }
  steps {
    container('image-azure-terraform') {
      sh "terragrunt plan"
      sh "terragrunt apply"
      sh  'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID'
    }
  }
}
```

## What is installed on this image?
- The [Azure CLI](https://packages.microsoft.com/repos/azure-cli/)
- Version [1.18.2](https://dl.google.com/go/go1.18.2.src.tar.gz) of the Go programming language
- Version [4.5.1](https://github.com/mikefarah/yq/releases/download/v4.5.1/yq_linux_amd64) of yq, a command-line YAML, JSON and XML processor
- Version [0.36.2](https://github.com/terraform-linters/tflint/releases/download/v0.36.2/tflint_linux_amd64.zip) of Terraform linter TFLint
- Version [1.2.5](https://releases.hashicorp.com/terraform/1.2.5/) of infrastructure as code tool Terraform
- Version [0.37.1](https://github.com/gruntwork-io/terragrunt/releases/download/v0.37.1/terragrunt_linux_amd64) of Terraform wrapper Terragrunt 
