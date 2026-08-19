# OpenShift

## Fresh install
```
./crc -f stop
./crc -f delete
./crc cleanup
rm -rf ~/.crc

./crc setup
./crc start
eval $(./crc oc-env)

sudo certbot certonly --nginx -d openshift.chrimson.net -d oauth-openshift.chrimson.net
```
