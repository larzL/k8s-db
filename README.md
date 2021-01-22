# Stack.io challenge

This program is the part 2 tech challenge for stack.io.


## Getting Started

Clone the repository.

```
git clone https://github.com/larzL/k8s-db.git
```


## Run deployment

Once cloned the repository, run below command to create all resources and deploy the services.

```
kubectl apply -k ./
```


## Test the APP and DB

Use `kubectl get pod` to make sure all pods are runniing. You can also check `hpa`, configmap`, `sevice`, etc as well. Once confirmed things are running properly, you can access the app to verify. A wordpress image is installed for testing purpose. If you're using minikube, run below command to get the app URL. 

```
minikube service k8s-app
```

To test the connection from app to db, you can use `root` and the password created by secret generator to access the DB. As for the db endpoint, you can get it by running `kubectl get service`.

To test persistent storage on the db, simply create any oject on the DB, then `kubectl delete pod "DB_pod" to recreate the db. Then login to the db again to make sure the oject still exist.


## Notes

* Have a persistent storage. In the case a database pod would to restart the database needs to keep all the data.
  * A persistent volume has been created and mounted on the DB. I already verified reboot the DB won't lose any data.
* Have a way for an external application in the cluster to talk to this database.
  * DB service is configured to be accessible by other service from the same cluster.
* Accept environment variables that contain database hostname, user to use, and a password. Pick a better way to inject those to your application container.
  * I duplicated env vars for demonstration. We can use configmap or value.yaml to manage env vars. I used configmap in this case. You can use `printenv` on the pod to verify.
* Needs to be able to autoscale based on cpu usage.
  * HPA has been configured for autoscale base on CPU usage. You can run `kubectl describe hpa` to verify.
* Have a way to restart the application pods in a seamless way without causing downtime.
  * I set deplyment to recreate when deletion occured. Replicaset also been configured to ensure 0 downtime. In addional, you can also use the `rollout` when restarting the pod(s).
* Have a way to access the application from outside the cluster. No need to manage certificates for this.
  * App service is configured to be accessible from outside. I verified locally when running on minikube.

## Authors

* **Larry Li** - [larzL](https://github.com/larzL)
