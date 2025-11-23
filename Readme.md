# Sprawozdanie - laboratorium 7

### 1. Utworzenie przestrzeni nazw
Użyto polecenia:
```
kubectl create ns remote
```

### 2. Uruchomienie poda remoteweb
Do stworzenia [manifestu](remoteweb.yaml) dla poda użyto polecenia:
```
kubectl run remoteweb --image=nginx -n remote --dry-run=client -o yaml > remoteweb.yaml
```

Do stworzenia [manifestu](remoteweb-service.yaml) dla serwisu użyto polecenia:
```
kubectl expose pod remoteweb --port=80 --target-port=80 --name=remoteweb-service --type=NodePort --namespace=remote --dry-run=client -o yaml > remoteweb-service.yaml
```
Po czym przy użyciu polecenia <i>kubectl edit</i> ustawiono port węzła na 31999.

Weryfikacja konfiguracji serwisu:
```
kubectl get svc -n remote
NAME                TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
remoteweb-service   NodePort   10.96.103.138   <none>        80:31999/TCP   23s
```

### 3. Uruchomienie poda testpod
Użyto polecenia:
```
kubectl run testpod --image=busybox --restart=Never -- sleep infinity
```

### 4. Weryfikacja dostępu do domowej strony internetowej remoteweb
Użyto polecenia:
```
kubectl exec -it testpod -- wget --spider --timeout=1 http://remoteweb-service.remote.svc.cluster.local
```

Otrzymano wynik:
```
Connecting to remoteweb-service.remote.svc.cluster.local (10.96.103.138:80)
remote file exists
```

Potwierdza to, że można uzyskać dostęp.

### 5. Weryfikacja dostępu do strony na węźle 31999
W celu uzyskania adresu url użyto polecenia:
```
minikube service remoteweb-service -n remote --url &
```

Otrzymany adres wykorzystano w poleceniu:
```
curl http://127.0.0.1:55046 > webpage.html
```

Polecenie zwróciło [kod HTML](webpage.html) domyślnej strony internetowej aktywnej w podzie remoteweb.
