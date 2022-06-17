# install-kubeflow

on a macbook. Taken from the [kubeflow/manifests README](https://github.com/kubeflow/manifests/blob/v1.4-branch/README.md).

Use [kzenv](https://github.com/nlamirault/kzenv) to switch to kustomize 3.2.0 !

```sh
brew tap nlamirault/kzenv https://github.com/nlamirault/kzenv/
# remove "normal" kustomize
brew remove kustomize
brew install kzenv
kzenv install 3.2.0
kzenv use 3.2.0
kustomize version
```

Then use the following commands to install Kubeflow to minikube.

```sh
rm -rf manifests/
# tag of the Kubeflow version
VERSION=v1.5.0 && \
minikube start --kubernetes-version=v1.20.7 --driver=hyperkit --addons=ingress,metrics-server --memory=14g --cpus=8 --disk-size='30000mb' && \
git clone https://github.com/kubeflow/manifests.git --branch $VERSION && \

while ! kustomize build --load_restrictor=none ./kustomization.yaml | kubectl apply -f -; do echo "Retrying to apply resources"; sleep 10; done && \
sleep 600 && \
kubectl port-forward svc/istio-ingressgateway -n istio-system 8080:80
```

## build kustomize.yaml

The above only works as the manifest has been compiled before. This needs to be re-done when a new Kubeflow version is provided. Below the steps taken for the 1.5 release.

```bash
cp ./manifests/example/kustomization.yaml .
gsed -i 
```

Some things take up to 1 hour to work.-.. so be patient.

In my case it was the second service, but you maybe need to try it out.

Login on [http://127.0.0.1:8080](http://127.0.0.1:8080)(or something else, see above) using *user:12341234*.

Tear down:

```sh
minikube delete
cd ..
rm -rf manifests
```