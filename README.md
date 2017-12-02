# Autoscaling go applications with OpenShift

As cluster admin, add limits and quotas to the project:
```
oc create -f limits.json  
oc create -f resource-quotas.yaml
```

As a regular user, create the application.
```
oc new-app docker.io/jorgemoralespou/s2i-go~https://github.com/bkoz/gotest.git

oc expose svc gotest --path=/mandelbrot
```
Confirm the app is working then create an hpa object.
```
oc autoscale dc gotest --max=4 --min=1 --cpu-percent=40

oc get hpa -w
```

In a second terminal, busy up the app with requests anmd wait for autoscaling to happen (don't forget the trailing slash):
```
ab -n 20000 -c 16 http://<route>/mandelbrot/
```


