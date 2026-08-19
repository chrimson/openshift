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
```
