# CKAD
## POD
1. create namespace ckad
2. create a pod named pod1 with image 2.3.5 and expose port 80 - create this pod in namespace ckad
3. verify pod is running
4. if not fix, retrieve the ip of the pod
5. run a temporary pod using busybox, shell into it and run a wget against pod1
6. view logs of pod1
7. delete pod1 and ckad namespace
## RBAC 
1. Inspect the environment and identify the authorization modes configured on the cluster.

    <details>
      <summary>show</summary>
      <p> 

    ```kubectl describe pod kube-apiserver-controlplane -n kube-system```

    </p> </details>

2. How many roles exist in the default namespace?

    <details>
      <summary>show</summary>
      <p>

    ```kubectl get roles```

    </p></details>
3. How many roles exist in all namespaces together?

    <details>
      <summary>show</summary>
      <p>

    ```kubectl get roles --all-namespaces```

    </p></details>

4. What are the resources the kube-proxy role in the kube-system namespace is given access to?

    <details>
      <summary>show</summary>
      <p> 

    ```kubectl describe roles kube-proxy -n kube-system```

    </p> </details>

5. Which account is the kube-proxy role assigned to?

    <details>
      <summary>show</summary>
      <p> 

    ```kubectl describe rolebinding kube-proxy -n kube-system```

    </p></details>

6. A user dev-user is created. User's details have been added to the kubeconfig file. Inspect the permissions granted to the user. Check if the user can 
list pods in the default namespace.

    <details>
      <summary>show</summary>
      <p>

      ```kubectl get pod --as dev-user``` 

      </p>
    </details>


7. Create the necessary roles and role bindings required for the dev-user to create, list and delete pods in the default namespace. Use the given spec:
    Role: developer
    Role Resources: pods
    Role Actions: list
    Role Actions: create
    Role Actions: delete
    RoleBinding: dev-user-binding
    RoleBinding: Bound to dev-user
    
    <details>
      <summary>show</summary>
      <p>

      To create a Role:-  ```kubectl create role developer --namespace=default --verb=list,create,delete --resource=pods```

      To create a RoleBinding:- ```kubectl create rolebinding dev-user-binding --namespace=default --role=developer --user=dev-user```


      </p>


      or, 

      ```
      kind: Role
    apiVersion: rbac.authorization.k8s.io/v1
    metadata:
      namespace: default
      name: developer
    rules:
    - apiGroups: [""]
      resources: ["pods"]
      verbs: ["list", "create","delete"]

    ---
    kind: RoleBinding
    apiVersion: rbac.authorization.k8s.io/v1
    metadata:
      name: dev-user-binding
    subjects:
    - kind: User
      name: dev-user
      apiGroup: rbac.authorization.k8s.io
    roleRef:
      kind: Role
      name: developer
      apiGroup: rbac.authorization.k8s.io
      ```

    </details>


8. A set of new roles and role-bindings are created in the blue namespace for the dev-user. However, the dev-user is unable to get details of the dark-blue-app pod in the blue namespace. Investigate and fix the issue.

    <details>
    <summary>show</summary>
    <p>

    ```kubectl edit role developer -n blue```

    and correct the resourceNames field

    </p>
    </details>

9. Add a new rule in the existing role developer to grant the dev-user permissions to create deployments in the blue namespace.

    <details>
    <summary>show</summary>
    <p>
    Edit the developer role in the blue namespace to add a new rule under the rules section. Append the below rule to the end of the file

    ```
    kubectl edit role developer -n blue
    - apiGroups:
      - apps
      resources:
      - deployments
      verbs:
      - create
      ```

    </p>
    /details>

    
## Cluster Role
    
10. How many ClusterRoles do you see defined in the cluster?
    <details>
    <summary>show</summary>
    <p>

    ```k get clusterroles --no-headers | wc -l```

    </p>
    </details>
    
