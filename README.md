## Value files for having to VPX working in a single tier configuration using ingress
Notes:
1. This assumes you already created the secrets required and the users in VPX are configured with the correct policy.
2. This also assume you created static routes in the VPX to allow health checks

### *Execute the following commands:*
```
helm repo add citrix https://citrix.github.io/citrix-helm-charts/
helm install cic1 citrix/citrix-cloud-native -f cic1/values.yaml
helm install cic2 citrix/citrix-cloud-native -f cic2/values.yaml
```

### *Run a demo app*
```
kubectl create -f https://raw.githubusercontent.com/citrix/cloud-native-getting-started/master/beginners-guide/manifest/guestbook-app.yaml
kubectl get pods -l 'app in (guestbook, redis)'
kubectl apply -f cic1/guestbook-ingress.yaml
kubectl apply -f cic2/guestbook-ingress.yaml
kubectl get ingress
```

### *The result should be:*
```
alexa@k3s1:~/citrix-ingress-config$ kubectl get ingress
NAME                 CLASS   HOSTS                ADDRESS         PORTS   AGE
guestbook-ingress    cic1    www.guestbook.com    192.168.5.191   80      25m
guestbook-ingress2   cic2    www.guestbook2.com   192.168.6.213   80      21m
```

### *Running the following commands should give you the following results:*
```
curl -s -H "Host: www.guestbook.com" http://IP | grep Guestbook
curl -s -H "Host: www.guestbook2.com" http://IP | grep Guestbook
```

### *The result should be:*
```
alexa@k3s1:~/citrix-ingress-config$ curl -s -H "Host: www.guestbook.com" http://192.168.5.191 | grep Guestbook
    <title>Guestbook</title>
      <h2>Guestbook</h2>

alexa@k3s1:~/citrix-ingress-config$ curl -s -H "Host: www.guestbook2.com" http://192.168.6.213 | grep Guestbook
    <title>Guestbook</title>
      <h2>Guestbook</h2>
```










