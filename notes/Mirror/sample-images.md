# OpenShift Sample Images

- Import all sample images into cluster 

```bash
IMAGES=`oc get is -o go-template --template="{{range .items}}{{.metadata.name}} {{end}}"`

for i in ${IMAGES}; do
  echo "Pulling: $i"
  ./oc import-image $i --all
done
```
