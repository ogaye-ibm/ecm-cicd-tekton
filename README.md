

```yaml
oc apply -f resources/workspace-pvc.yaml

oc apply -f resources/git-src-creds-secret.yaml
oc secrets link pipeline webhook-git-src-basic-auth-secret

oc apply -f resources/docker-creds.yaml
# Optional, secret will be bound to kaniko workspace
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




```