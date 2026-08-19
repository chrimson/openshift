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
```
