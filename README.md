# Resource Quota Testing in Kubenetes
  
# ➤ Create namespace: 
    kubectl.exe create namespace quotatesting ⏎
      -> namespace "quotatesting" created

# ➤ Create resource quota in the previously created namespace: 
    kubectl create -f https://k8s.io/docs/tasks/administer-cluster/quota-mem-cpu.yaml --namespace=quotatesting ⏎
      -> resourcequota "mem-cpu-demo" created

# ➤ Verify the resources Quotas:
   kubectl describe namespaces quotatesting ⏎
   
      Name:         quotatesting
      Labels:       <none>
      Annotations:  <none>
      Status:       Active

      Resource Quotas
       Name:            mem-cpu-demo
       Resource         Used  Hard
       --------         ---   ---
       limits.cpu       0     2
       limits.memory    0     2Gi
       requests.cpu     0     1
       requests.memory  0     1Gi

      No resource limits.
      
  kubectl create -f https://k8s.io/docs/tasks/administer-cluster/quota-mem-cpu-pod.yaml --namespace=quotatesting
pod "quota-mem-cpu-demo" created

# ➤ Verify the resources of the namespace created
			kubectl describe namespaces quotatesting ⏎
				Name:         quotatesting
				Labels:       <none>
				Annotations:  <none>
				Status:       Active

				Resource Quotas
				 Name:            mem-cpu-demo
				 Resource         Used   Hard
				 --------         ---    ---
				 limits.cpu       800m   2
				 limits.memory    800Mi  2Gi
				 requests.cpu     400m   1
				 requests.memory  600Mi  1Gi

				No resource limits.

# ➤ Verify that the pods is running:
	kubectl get resourcequota mem-cpu-demo --namespace=quotatesting ⏎
      NAME           AGE
      mem-cpu-demo   1m

# ➤ Verify the resources:
	kubectl get resourcequota mem-cpu-demo --namespace=quotatesting --output=yaml ⏎
      apiVersion: v1
      kind: ResourceQuota
      metadata:
        creationTimestamp: 2018-03-25T04:26:40Z
        name: mem-cpu-demo
        namespace: quotatesting
        resourceVersion: "82435"
        selfLink: /api/v1/namespaces/quotatesting/resourcequotas/mem-cpu-demo
        uid: b71fe0f2-2fe4-11e8-9ff5-0800270bf8fe
      spec:
        hard:
          limits.cpu: "2"
          limits.memory: 2Gi
          requests.cpu: "1"
          requests.memory: 1Gi
      status:
        hard:
          limits.cpu: "2"
          limits.memory: 2Gi
          requests.cpu: "1"
          requests.memory: 1Gi
        used:
          limits.cpu: 800m
          limits.memory: 800Mi
          requests.cpu: 400m
          requests.memory: 600Mi

# ➤ Create another pod with large resources which is outside the limit:
    kubectl create -f https://k8s.io/docs/tasks/administer-cluster/quota-mem-cpu-pod-2.yaml --namespace=quotatesting ⏎
		
		The output shows that creating the second Pod would cause the memory request total to exceed the memory request quota.
		
				Error from server (Forbidden): error when creating "https://k8s.io/docs/tasks/administer-cluster/quota-mem-cpu-pod-2.yaml": pods "quota-mem-cpu-demo-2" is forbidden: exceeded quota: mem-cpu-demo, requested: requests.memory=700Mi, used: requests.memory=600Mi, limited: requests.memory=1Gi	

# ➤ Clean up
	Delete your namespace:
		kubectl delete namespace quotatesting


  
