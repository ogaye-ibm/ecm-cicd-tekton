

```yaml
kubectl apply -f https://raw.githubusercontent.com/tektoncd/catalog/main/task/kaniko/0.5/kaniko.yaml

oc apply -f resources/workspace-pvc.yaml

oc apply -f resources/git-src-creds-secret.yaml
oc secrets link pipeline webhook-git-src-basic-auth-secret

oc apply -f resources/docker-creds-secret.yaml
# Optional: secret will be bound to kaniko workspace, no need to link to sa
oc secrets link pipeline docker-creds

oc apply -f resources/redhat-pull-secret.yaml
oc secrets link pipeline 13162754-omar.gaye-ibm-pull-secret --for=pull

oc create secret docker-registry registry-dockerconfig-secret \
--docker-server=quay.io \
--docker-username=omar.gaye-ibm \
--docker-password=ZTejas99! \
--docker-email=gayeomar@hotmail.com
oc secrets link pipeline registry-dockerconfig-secret

oc apply -f tasks/common
oc apply -f tasks/quarkus-jvm
oc apply -f pipelines

tkn pipeline start build-push-deploy-kaniko \ 
 -w name=shared-workspace,claimName=webhook-pvc \ 
 -w name=ssh-creds,secret=webhook-git-src-basic-auth-secret \ 
 -w name=docker-reg-creds,secret=docker-creds \ 
 --showlog



gradle build -Dquarkus.package.type=native \ 
-Dquarkus.native.container-build=true \ 
-Dquarkus.native.native-image-xmx=8G \
-x test #optional

gradle build -Dquarkus.package.type=native \
-Dquarkus.native.container-build=true \ 
-Dquarkus.native.native-image-xmx=8G \ 
-x test

docker build -f src/main/docker/Dockerfile.native-micro -t webhook-native:0.0.3 .
docker tag webhook-native:0.0.3 quay.io/omar.gaye-ibm/webhook-native:0.0.3
docker push quay.io/omar.gaye-ibm/webhook-native:0.0.3


tkn pipeline start build-jvm-push-update-manifests \ 
 -w name=shared-workspace,claimName=webhook-pvc \ 
 -w name=ssh-creds,secret=webhook-git-src-basic-auth-secret \ 
 -w name=docker-reg-creds,secret=docker-creds \ 
 -w name=argocd-ssh-creds,secret=git-argocd-basic-auth-secret \ 
 --showlog


```