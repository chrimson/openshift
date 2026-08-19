# OpenShift

## Fresh install
```
./crc stop -f
./crc delete -f
./crc cleanup
rm -rf ~/.crc

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

## ArgoCD
```
oc create route passthrough argocd-external \
  --service=argocd-server \
  --port=https \
  --hostname=argocd.chrimson.net \
  -n argocd

oc extract secret/argocd-initial-admin-secret -n argocd --to=- --keys=password
```
