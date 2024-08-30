## Image build and push to Amazon ECR action

This Github Action builds images and pushes them to Amazon Elastic Container Registry.
It also creates/updates a kubernetes configmap which keeps track of the latest version of the image.
For pushing to a non-AWS container registry, see the [custom build and push action](https://github.com/Vasio-NL/custom-build-and-push-action).

This action is intended to be used with the custom [kubernetes deploy ECR action](https://github.com/Vasio-NL/custom-k8s-deploy-ECR-action).


### Inputs

| Name | Description                                                                     | Required |
| --- |---------------------------------------------------------------------------------| --- |
| `aws-access-key-id` | AWS Access Key ID used to log in to Amazon ECR                                                  | true |
| `aws-secret-access-key` | AWS Secret Access Key used to log in to Amazon ECR                                             | true |
| `container-registry-name` | The name of the container registry                                            | true |
| `dockerfile-path` | Path to the Dockerfile                                                          | true |
| `docker-image-name` | The name of the image                                                           | true |
| `kube-config-base64` | The base64 encoded kubeconfig needed to connect to the cluster                  | true |
| `docker-build-context` | The build context for the docker build. The default is the root directory. ('.') | false |
| `docker-build-args` | Optional additional build arguments for the docker build                        | false |

The container registry name is the name that is prefixed to the image name. An example image:

`1234567890.dkr.ecr.eu-west-1.amazonaws.com/vasio/cool-project:latest`

In this example:
- The <b>registry url</b> is `1234567890.dkr.ecr.eu-west-1.amazonaws.com`. In this action, the registry url is retrieved automatically from AWS.
- The <b>container registry name</b> is `vasio`.
- The <b>image name</b> is `cool-project`.


### Example usage

The following is an example build job:

```
    build-app:
    runs-on: ubuntu-latest
    environment: ${{github.ref_name}}
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Build and push Docker image
        uses: Vasio-NL/custom-build-and-push-ECR-action@v1
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          container-registry-name: vasio
          docker-image-name: cool-project
          dockerfile-path: './docker/Dockerfile'
          kube-config-base64: ${{ secrets.KUBE_CONFIG_B64 }}
```

### For developers: Updating the action
When making changes, make sure to tag new versions so they can be used in Github workflows. The action uses semantic versioning.

To tag the version (v1.0.3 in the example):

`git tag v1.0.3`

also update the major version tag (v1 in the example):

`git tag v1 -f`

When finished tagging, make sure to push the tags:

`git push --tags -f`
