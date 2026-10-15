# OpenShift

## Install
```
./crc config set disk-size 100
./crc setup
./crc start
eval $(./crc oc-env)

sudo certbot certonly --nginx -d openshift.chrimson.net -d oauth-openshift.chrimson.net

loginctl enable-linger chrimson
loginctl show-user chrimson | grep Linger

oc login -u kubeadmin -p <your-kubeadmin-password> https://api.crc.testing:6443
oc get secret htpass-secret -n openshift-config -o jsonpath='{.data.htpasswd}' | base64 -d > htpasswd
htpasswd -b htpasswd developer <YOUR_NEW_STRONG_PASSWORD>
oc create secret generic htpass-secret --from-file=htpasswd=htpasswd -n openshift-config --dry-run=client -o yaml | oc replace -f -
```

## Uninstall
```
./crc stop -f
./crc delete -f
./crc cleanup
rm -rf ~/.crc
```

# ArgoCD

## Install
```
oc new-project argocd

oc adm policy add-scc-to-user privileged -z argocd-application-controller -n argocd
oc adm policy add-scc-to-user privileged -z argocd-applicationset-controller -n argocd
oc adm policy add-scc-to-user privileged -z argocd-dex-server -n argocd
oc adm policy add-scc-to-user privileged -z argocd-notifications-controller -n argocd
oc adm policy add-scc-to-user privileged -z argocd-redis -n argocd
oc adm policy add-scc-to-user privileged -z argocd-repo-server -n argocd
oc adm policy add-scc-to-user privileged -z argocd-server -n argocd

oc create secret generic argocd-redis -n argocd \
  --from-literal=auth="" \
  --dry-run=client -o yaml | oc apply -f -

oc create route passthrough argocd-server \
  --service=argocd-server \
  --port=https \
  -n argocd

oc create route passthrough argocd-external \
  --service=argocd-server \
  --port=https \
  --hostname=argocd.chrimson.net \
  -n argocd

oc apply --server-side -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

oc set image deployment/argocd-redis redis=redis:7.2-alpine -n argocd
oc create secret generic argocd-redis \
  --from-literal=auth=argocd-redis-password \
  -n argocd \
  --dry-run=client -o yaml | oc apply -f -
oc rollout restart deployment -n argocd argocd-redis

oc extract secret/argocd-initial-admin-secret -n argocd --to=- --keys=password

oc patch deployment argocd-repo-server -n argocd --type='json' -p='[
  {
    "op": "add",
    "path": "/spec/template/spec/containers/0/resources",
    "value": {
      "limits": {
        "cpu": "500m",
        "memory": "1Gi",
        "ephemeral-storage": "1Gi"
      },
      "requests": {
        "cpu": "50m",
        "memory": "256Mi",
        "ephemeral-storage": "256Mi"
      }
    }
  }
]'
```

## Uninstall
```
oc delete -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
oc delete namespace argocd
oc delete clusterrolebinding argocd-application-controller argocd-applicationset-controller argocd-server
```
