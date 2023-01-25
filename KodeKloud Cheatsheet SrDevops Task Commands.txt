Task 01 - Task 36 : Kodekloud Cheatsheat SysAdmin Task Commands.txt
Task 37 - Task 87 : Kodekloud Cheatsheat DevOps Task Commands.txt
--------------------------------------------------------------------------------------------------------------------------------
Task 88: 11/Oct/2022

Init Containers in Kubernetes

There are some applications that need to be deployed on Kubernetes cluster and these apps have some pre-requisites where some configurations need to be changed before deploying the app container. Some of these changes cannot be made inside the images so the DevOps team has come up with a solution to use init containers to perform these tasks during deployment. Below is a sample scenario that the team is going to test first.

    Create a Deployment named as ic-deploy-devops.

    Configure spec as replicas should be 1, labels app should be ic-devops, template's metadata lables app should be the same ic-devops.

    The initContainers should be named as ic-msg-devops, use image debian, preferably with latest tag and use command '/bin/bash', '-c' and 'echo Init Done - Welcome to xFusionCorp Industries > /ic/news'. The volume mount should be named as ic-volume-devops and mount path should be /ic.

    Main container should be named as ic-main-devops, use image debian, preferably with latest tag and use command '/bin/bash', '-c' and 'while true; do cat /ic/news; sleep 5; done'. The volume mount should be named as ic-volume-devops and mount path should be /ic.

    Volume to be named as ic-volume-devops and it should be an emptyDir type.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.


1. Check existing running services,deployment and pods
thor@jump_host ~$ kubectl get all
	NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
	service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   59m

thor@jump_host ~$ kubectl get services
	NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
	kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   59m

thor@jump_host ~$ kubectl get deploy
	No resources found in default namespace.

thor@jump_host ~$ kubectl get pods
	No resources found in default namespace.

2. Create yaml file as per requirement.
thor@jump_host ~$ vi /tmp/init.yml
 
thor@jump_host ~$ cat /tmp/init.yml 
		apiVersion: apps/v1

		kind: Deployment

		metadata:

		  name: ic-deploy-devops

		  labels:

		    app: ic-devops

		spec:

		  replicas: 1

		  selector:

		    matchLabels:

		      app: ic-devops

		  template:

		    metadata:

		      labels:

		        app: ic-devops

		    spec:

		      volumes:

		        - name: ic-volume-devops

		          emptyDir: {}

		      initContainers:

		        - name: ic-msg-devops

		          image: debian:latest

		          command:

		            [

		              "/bin/bash",

		              "-c",

		              "echo Init Done - Welcome to xFusionCorp Industries > /ic/news",

		            ]

		          volumeMounts:

		            - name: ic-volume-devops

		              mountPath: /ic

		 

		      containers:

		        - name: ic-main-devops

		          image: debian:latest

		          command:

		            [

		              "/bin/bash",

		              "-c",

		              "while true; do cat /ic/news; sleep 5; done",

		            ]

		          volumeMounts:

		            - name: ic-volume-devops

		              mountPath: /ic

3. Create pod and deployment 
thor@jump_host ~$ kubectl create -f /tmp/init.yml 
	deployment.apps/ic-deploy-devops created

4. Wait for pod to get to running status
thor@jump_host ~$ kubectl get pods -w
	NAME                                READY   STATUS    RESTARTS   AGE
	ic-deploy-devops-68f4dcbdfb-grg6v   1/1     Running   0          23s
	^C

thor@jump_host ~$ kubectl get deploy
	NAME               READY   UP-TO-DATE   AVAILABLE   AGE
	ic-deploy-devops   1/1     1            1           60s

thor@jump_host ~$ kubectl get pods
	NAME                                READY   STATUS    RESTARTS   AGE
	ic-deploy-devops-68f4dcbdfb-grg6v   1/1     Running   0          66s

5. Validate the task by checking logs on created pod and cat on created file
thor@jump_host ~$ kubectl logs -f ic-deploy-devops-68f4dcbdfb-grg6v
		Init Done - Welcome to xFusionCorp Industries
		Init Done - Welcome to xFusionCorp Industries
		Init Done - Welcome to xFusionCorp Industries
		Init Done - Welcome to xFusionCorp Industries
		Init Done - Welcome to xFusionCorp Industries
		Init Done - Welcome to xFusionCorp Industries
		Init Done - Welcome to xFusionCorp Industries
		^C

thor@jump_host ~$ kubectl exec ic-deploy-devops-68f4dcbdfb-grg6v -- cat /ic/news
		Init Done - Welcome to xFusionCorp Industries

--------------------------------------------------------------------------------------------------------------------------------
Task 89: 13/Oct/2022

Troubleshoot Issue With Pods

One of the junior DevOps team members was working on to deploy a stack on Kubernetes cluster. Somehow the pod is not coming up and its failing with some errors. We need to fix this as soon as possible. Please look into it.

    There is a pod named webserver and the container under it is named as nginx-container. It is using image nginx:latest

    There is a sidecar container as well named sidecar-container which is using ubuntu:latest image.

Look into the issue and fix it, make sure pod is in running state and you are able to access the app.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.



Name:         webserver
Namespace:    default
Priority:     0
Node:         kodekloud-control-plane/172.17.0.2
Start Time:   Thu, 13 Oct 2022 12:03:02 +0000
Labels:       app=web-app
Annotations:  <none>
Status:       Pending
IP:           10.244.0.5
IPs:
  IP:  10.244.0.5
Containers:
  nginx-container:
    Container ID:   
    Image:          nginx:latests
    Image ID:       
    Port:           <none>
    Host Port:      <none>
    State:          Waiting
      Reason:       ImagePullBackOff
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/log/nginx from shared-logs (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-2dgf4 (ro)
  sidecar-container:
    Container ID:  containerd://f186ca761ed3228d82850c85ff4fbf29b954ee664214699ac609288f80cfe75c
    Image:         ubuntu:latest
    Image ID:      docker.io/library/ubuntu@sha256:35fb073f9e56eb84041b0745cb714eff0f7b225ea9e024f703cab56aaa5c7720
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log; sleep 30; done
    State:          Running
      Started:      Thu, 13 Oct 2022 12:03:10 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/log/nginx from shared-logs (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-2dgf4 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             False 
  ContainersReady   False 
  PodScheduled      True 
Volumes:
  shared-logs:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     
    SizeLimit:  <unset>
  default-token-2dgf4:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-2dgf4
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age                From               Message
  ----     ------     ----               ----               -------
  Normal   Scheduled  61s                default-scheduler  Successfully assigned default/webserver to kodekloud-control-plane
  Normal   Pulling    60s                kubelet            Pulling image "ubuntu:latest"
  Normal   Started    53s                kubelet            Started container sidecar-container
  Normal   Pulled     53s                kubelet            Successfully pulled image "ubuntu:latest" in 6.384820926s
  Normal   Created    53s                kubelet            Created container sidecar-container
  Normal   BackOff    26s (x3 over 53s)  kubelet            Back-off pulling image "nginx:latests"
  Warning  Failed     26s (x3 over 53s)  kubelet            Error: ImagePullBackOff
  Normal   Pulling    12s (x3 over 60s)  kubelet            Pulling image "nginx:latests"
  Warning  Failed     11s (x3 over 60s)  kubelet            Failed to pull image "nginx:latests": rpc error: code = NotFound desc = failed to pull and unpack image "docker.io/library/nginx:latests": failed to resolve reference "docker.io/library/nginx:latests": docker.io/library/nginx:latests: not found
  Warning  Failed     11s (x3 over 60s)  kubelet            Error: ErrImagePull


1. Check pod running status using kubectl utility
thor@jump_host ~$ kubectl get pods
	NAME        READY   STATUS             RESTARTS   AGE
	webserver   1/2     ImagePullBackOff   0          30s

thor@jump_host ~$ kubectl get pods webserver
	NAME        READY   STATUS         RESTARTS   AGE
	webserver   1/2     ErrImagePull   0          39s

2. Check pod configuration and try to indentify error
thor@jump_host ~$ kubectl describe pod webserver
		Name:         webserver
		Namespace:    default
		Priority:     0
		Node:         kodekloud-control-plane/172.17.0.2
		Start Time:   Thu, 13 Oct 2022 12:03:02 +0000
		Labels:       app=web-app
		Annotations:  <none>
		Status:       Pending
		IP:           10.244.0.5
		IPs:
		  IP:  10.244.0.5
		Containers:
		|------------------------------------------------------------------------------------------------------------------------
		| nginx-container:
		|   Container ID:   
		|   Image:          nginx:latests											<-------- Error in container image name
		|   Image ID:       
		|   Port:           <none>
		|   Host Port:      <none>
		|   State:          Waiting
		|     Reason:       ImagePullBackOff
		|   Ready:          False
		|   Restart Count:  0
		|   Environment:    <none>
		|   Mounts:
		|     /var/log/nginx from shared-logs (rw)
		|     /var/run/secrets/kubernetes.io/serviceaccount from default-token-2dgf4 (ro)
		|------------------------------------------------------------------------------------------------------------------------      
		  sidecar-container:
		    Container ID:  containerd://f186ca761ed3228d82850c85ff4fbf29b954ee664214699ac609288f80cfe75c
		    Image:         ubuntu:latest
		    Image ID:      docker.io/library/ubuntu@sha256:35fb073f9e56eb84041b0745cb714eff0f7b225ea9e024f703cab56aaa5c7720
		    Port:          <none>
		    Host Port:     <none>
		    Command:
		      sh
		      -c
		      while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log; sleep 30; done
		    State:          Running
		      Started:      Thu, 13 Oct 2022 12:03:10 +0000
		    Ready:          True
		    Restart Count:  0
		    Environment:    <none>
		    Mounts:
		      /var/log/nginx from shared-logs (rw)
		      /var/run/secrets/kubernetes.io/serviceaccount from default-token-2dgf4 (ro)
		Conditions:
		  Type              Status
		  Initialized       True 
		  Ready             False 
		  ContainersReady   False 
		  PodScheduled      True 
		Volumes:
		  shared-logs:
		    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
		    Medium:     
		    SizeLimit:  <unset>
		  default-token-2dgf4:
		    Type:        Secret (a volume populated by a Secret)
		    SecretName:  default-token-2dgf4
		    Optional:    false
		QoS Class:       BestEffort
		Node-Selectors:  <none>
		Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
		                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
		Events:
		  Type     Reason     Age                From               Message
		  ----     ------     ----               ----               -------
		  Normal   Scheduled  61s                default-scheduler  Successfully assigned default/webserver to kodekloud-control-plane
		  Normal   Pulling    60s                kubelet            Pulling image "ubuntu:latest"
		  Normal   Started    53s                kubelet            Started container sidecar-container
		  Normal   Pulled     53s                kubelet            Successfully pulled image "ubuntu:latest" in 6.384820926s
		  Normal   Created    53s                kubelet            Created container sidecar-container
		  Normal   BackOff    26s (x3 over 53s)  kubelet            Back-off pulling image "nginx:latests"
		  Warning  Failed     26s (x3 over 53s)  kubelet            Error: ImagePullBackOff
		  Normal   Pulling    12s (x3 over 60s)  kubelet            Pulling image "nginx:latests"
		  Warning  Failed     11s (x3 over 60s)  kubelet            Failed to pull image "nginx:latests": rpc error: code = NotFound desc = failed to pull and unpack image "docker.io/library/nginx:latests": failed to resolve reference "docker.io/library/nginx:latests": docker.io/library/nginx:latests: not found
		  Warning  Failed     11s (x3 over 60s)  kubelet            Error: ErrImagePull


3. Edit the pod to change the container image name (using vi editor or default editor)
thor@jump_host ~$ kubectl edit pod webserver
		pod/webserver edited

4. Wait for pod to running status
thor@jump_host ~$ kubectl get pod webserver
		NAME        READY   STATUS    RESTARTS   AGE
		webserver   2/2     Running   0          2m22s

thor@jump_host ~$ kubectl describe pod webserver
		Name:         webserver
		Namespace:    default
		Priority:     0
		Node:         kodekloud-control-plane/172.17.0.2
		Start Time:   Thu, 13 Oct 2022 12:03:02 +0000
		Labels:       app=web-app
		Annotations:  <none>
		Status:       Running
		IP:           10.244.0.5
		IPs:
		  IP:  10.244.0.5
		Containers:
		|------------------------------------------------------------------------------------------------------------------------
		| nginx-container:																										
		|   Container ID:   containerd://96e18a2d97d0dcd78289713a2656e1150b4615dd6139fc56c293bc283e147616							
		|   Image:          nginx:latest
		|   Image ID:       docker.io/library/nginx@sha256:2f770d2fe27bc85f68fd7fe6a63900ef7076bc703022fe81b980377fe3d27b70
		|   Port:           <none>
		|   Host Port:      <none>
		|   State:          Running																		<------------Error resolved, pod running
		|     Started:      Thu, 13 Oct 2022 12:05:22 +0000
		|   Ready:          True
		|   Restart Count:  0
		|   Environment:    <none>
		|   Mounts:
		|     /var/log/nginx from shared-logs (rw)
		|     /var/run/secrets/kubernetes.io/serviceaccount from default-token-2dgf4 (ro)
		|------------------------------------------------------------------------------------------------------------------------|
		  sidecar-container:
		    Container ID:  containerd://f186ca761ed3228d82850c85ff4fbf29b954ee664214699ac609288f80cfe75c
		    Image:         ubuntu:latest
		    Image ID:      docker.io/library/ubuntu@sha256:35fb073f9e56eb84041b0745cb714eff0f7b225ea9e024f703cab56aaa5c7720
		    Port:          <none>
		    Host Port:     <none>
		    Command:
		      sh
		      -c
		      while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log; sleep 30; done
		    State:          Running
		      Started:      Thu, 13 Oct 2022 12:03:10 +0000
		    Ready:          True
		    Restart Count:  0
		    Environment:    <none>
		    Mounts:
		      /var/log/nginx from shared-logs (rw)
		      /var/run/secrets/kubernetes.io/serviceaccount from default-token-2dgf4 (ro)
		Conditions:
		  Type              Status
		  Initialized       True 
		  Ready             True 
		  ContainersReady   True 
		  PodScheduled      True 
		Volumes:
		  shared-logs:
		    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
		    Medium:     
		    SizeLimit:  <unset>
		  default-token-2dgf4:
		    Type:        Secret (a volume populated by a Secret)
		    SecretName:  default-token-2dgf4
		    Optional:    false
		QoS Class:       BestEffort
		Node-Selectors:  <none>
		Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
		                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
		Events:
		  Type     Reason     Age                  From               Message
		  ----     ------     ----                 ----               -------
		  Normal   Scheduled  2m28s                default-scheduler  Successfully assigned default/webserver to kodekloud-control-plane
		  Normal   Pulling    2m27s                kubelet            Pulling image "ubuntu:latest"
		  Normal   Started    2m20s                kubelet            Started container sidecar-container
		  Normal   Pulled     2m20s                kubelet            Successfully pulled image "ubuntu:latest" in 6.384820926s
		  Normal   Created    2m20s                kubelet            Created container sidecar-container
		  Normal   Pulling    99s (x3 over 2m27s)  kubelet            Pulling image "nginx:latests"
		  Warning  Failed     98s (x3 over 2m27s)  kubelet            Failed to pull image "nginx:latests": rpc error: code = NotFound desc = failed to pull and unpack image "docker.io/library/nginx:latests": failed to resolve reference "docker.io/library/nginx:latests": docker.io/library/nginx:latests: not found
		  Warning  Failed     98s (x3 over 2m27s)  kubelet            Error: ErrImagePull
		  Normal   BackOff    62s (x6 over 2m20s)  kubelet            Back-off pulling image "nginx:latests"
		  Warning  Failed     62s (x6 over 2m20s)  kubelet            Error: ImagePullBackOff

thor@jump_host ~$ kubectl get pods
	NAME        READY   STATUS    RESTARTS   AGE
	webserver   2/2     Running   0          3m46s

--------------------------------------------------------------------------------------------------------------------------------
Task 90: 15/Oct/2022

Persistent Volumes in Kubernetes HTTPD

The Nautilus DevOps team is working on a Kubernetes template to deploy a web application on the cluster. There are some requirements to create/use persistent volumes to store the application code, and the template needs to be designed accordingly. Please find more details below:

    Create a PersistentVolume named as pv-datacenter. Configure the spec as storage class should be manual, set capacity to 5Gi, set access mode to ReadWriteOnce, volume type should be hostPath and set path to /mnt/itadmin (this directory is already created, you might not be able to access it directly, so you need not to worry about it).

    Create a PersistentVolumeClaim named as pvc-datacenter. Configure the spec as storage class should be manual, request 1Gi of the storage, set access mode to ReadWriteOnce.

    Create a pod named as pod-datacenter, mount the persistent volume you created with claim name pvc-datacenter at document root of the web server, the container within the pod should be named as container-datacenter using image httpd with latest tag only (remember to mention the tag i.e httpd:latest).

    Create a node port type service named web-datacenter using node port 30008 to expose the web server running within the pod.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

1. Check kubectl utility for currently running services and pods
thor@jump_host ~$ kubectl get all
	NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
	service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   65m
 
thor@jump_host ~$ kubectl get pvc
	No resources found in default namespace.

2. Create YAML file as per requirements 
thor@jump_host ~$ vi /tmp/pvc_httpd.yml

thor@jump_host ~$ cat /tmp/pvc_httpd.yml 
		---
		apiVersion: v1
		kind: PersistentVolume
		metadata:
		  name: pv-datacenter
		spec:
		  capacity:
		    storage: 5Gi
		  accessModes:
		    - ReadWriteOnce
		  storageClassName: manual
		  hostPath:
		    path: /mnt/itadmin
		---
		apiVersion: v1
		kind: PersistentVolumeClaim
		metadata:
		  name: pvc-datacenter
		spec:
		  accessModes:
		    - ReadWriteOnce
		  storageClassName: manual
		  resources:
		    requests:
		      storage: 1Gi
		---
		apiVersion: v1
		kind: Pod
		metadata:
		  name: pod-datacenter
		  labels:
		     app: httpd
		spec:
		  volumes:
		    - name: storage-datacenter
		      persistentVolumeClaim:
		        claimName: pvc-datacenter
		  containers:
		    - name: container-datacenter
		      image: httpd:latest
		      ports:
		        - containerPort: 80
		      volumeMounts:
		        - name: storage-datacenter
		          mountPath:  /usr/local/apache2/htdocs/
		---                                                                                                           
		apiVersion: v1                                                                                                
		kind: Service                                                                                                 
		metadata:                                                                                                     
		  name: web-datacenter                                                                                         
		spec:                                                                                                         
		   type: NodePort                                                                                             
		   selector:                                                                                                  
		     app: httpd                                                                                     
		   ports:                                                                                                     
		     - port: 80                                                                                               
		       targetPort: 80                                                                                         
		       nodePort: 30008

3. Create the deployment , pods and persistent volumes
thor@jump_host ~$ kubectl create -f /tmp/pvc_httpd.yml 
	persistentvolume/pv-datacenter created
	persistentvolumeclaim/pvc-datacenter created
	pod/pod-datacenter created
	service/web-datacenter created

4. Wait for pods and services to running status
thor@jump_host ~$ kubectl get all
		NAME                 READY   STATUS    RESTARTS   AGE
		pod/pod-datacenter   0/1     Pending   0          6s

		NAME                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
		service/kubernetes       ClusterIP   10.96.0.1       <none>        443/TCP        72m
		service/web-datacenter   NodePort    10.96.147.100   <none>        80:30008/TCP   6s

 
thor@jump_host ~$ kubectl get all
	NAME                 READY   STATUS    RESTARTS   AGE
	pod/pod-datacenter   1/1     Running   0          30s

	NAME                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
	service/kubernetes       ClusterIP   10.96.0.1       <none>        443/TCP        73m
	service/web-datacenter   NodePort    10.96.147.100   <none>        80:30008/TCP   30s

5. Validate Psersistent Volume Claim and Peristent volume 
thor@jump_host ~$ kubectl get pvc
	NAME             STATUS   VOLUME          CAPACITY   ACCESS MODES   STORAGECLASS   AGE
	pvc-datacenter   Bound    pv-datacenter   5Gi        RWO            manual         40s
 
thor@jump_host ~$ kubectl get pv
	NAME            CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                    STORAGECLASS   REASON   AGE
	pv-datacenter   5Gi        RWO            Retain           Bound    default/pvc-datacenter   manual                  45s

6. Validate task by View port on website

--------------------------------------------------------------------------------------------------------------------------------
Task 91: 17/Oct/2022

Resolve Git Merge Conflicts

Sarah and Max were working on writting some stories which they have pushed to the repository. Max has recently added some new changes and is trying to push them to the repository but he is facing some issues. Below you can find more details:

SSH into storage server using user max and password Max_pass123. Under /home/max you will find the story-blog repository. Try to push the changes to the origin repo and fix the issues. The story-index.txt must have titles for all 4 stories. Additionally, there is a typo in The Lion and the Mooose line where Mooose should be Mouse.

Click on the Gitea UI button on the top bar. You should be able to access the Gitea page. You can login to Gitea server from UI using username sarah and password Sarah_pass123 or username max and password Max_pass123.

Note: For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

1. SSH to storage server with user max
thor@jump_host ~$ ssh max@ststor01
		The authenticity of host 'ststor01 (172.16.238.15)' can't be established.
		ECDSA key fingerprint is SHA256:0z85j/k+4Nf8WKbHJzxo1AOv4FeRA8LPET2N3BEkYyo.
		ECDSA key fingerprint is MD5:74:e6:4d:c4:b3:80:07:be:03:30:0a:bf:1e:eb:e6:82.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'ststor01,172.16.238.15' (ECDSA) to the list of known hosts.
max@ststor01's password: 
		Welcome to xFusionCorp Storage server.

2. Go to local git repo and check git status
max $ cd /home/max/story-blog/

max (master)$ ls -ahl
		total 32
		drwxr-sr-x    3 max      max         4.0K Oct 17 14:06 .
		drwxr-sr-x    1 max      max         4.0K Oct 17 14:06 ..
		drwxr-sr-x    8 max      max         4.0K Oct 17 14:06 .git
		-rw-r--r--    1 max      max          807 Oct 17 14:06 fox-and-grapes.txt
		-rw-r--r--    1 max      max          792 Oct 17 14:06 frogs-and-ox.txt
		-rw-r--r--    1 max      max         1.1K Oct 17 14:06 lion-and-mouse.txt
		-rw-r--r--    1 max      max          102 Oct 17 14:06 story-index.txt

max (master)$ git status
		On branch master
		Your branch is ahead of 'origin/master' by 1 commit.
		  (use "git push" to publish your local commits)
		nothing to commit, working directory clean
 
3.Check story-index.txt file
max (master)$ cat story-index.txt 
		1. The Lion and the Mooose
		2. The Frogs and the Ox
		3. The Fox and the Grapes
		4. The Donkey and the Dog

4. Try to push the changes to know the error		
max (master)$ git push
		Username for 'http://git.stratos.xfusioncorp.com': max
		Password for 'http://max@git.stratos.xfusioncorp.com': 
		To http://git.stratos.xfusioncorp.com/sarah/story-blog.git
		 ! [rejected]        master -> master (fetch first)
		error: failed to push some refs to 'http://git.stratos.xfusioncorp.com/sarah/story-blog.git'
		hint: Updates were rejected because the remote contains work that you do
		hint: not have locally. This is usually caused by another repository pushing
		hint: to the same ref. You may want to first integrate the remote changes
		hint: (e.g., 'git pull ...') before pushing again.
		hint: See the 'Note about fast-forwards' in 'git push --help' for details.

5. Conflict in between local repo and Gitex repo. Setglobal variables 

max (master)$ git config --global --add user.email max@stratos.xfusioncorp.com

max (master)$ git config --global --add user.name max

max (master)$ git config -l
	user.email=max@stratos.xfusioncorp.com
	user.name=max
	core.repositoryformatversion=0
	core.filemode=true
	core.bare=false
	core.logallrefupdates=true
	remote.origin.url=http://git.stratos.xfusioncorp.com/sarah/story-blog.git
	remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
	branch.master.remote=origin
	branch.master.merge=refs/heads/master

6. Pull the changes from gitex to local 
max (master)$ git  pull origin master
	remote: Enumerating objects: 4, done.
	remote: Counting objects: 100% (4/4), done.
	remote: Compressing objects: 100% (3/3), done.
	remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
	Unpacking objects: 100% (3/3), done.
	From http://git.stratos.xfusioncorp.com/sarah/story-blog
	 * branch            master     -> FETCH_HEAD
	   9f02757..7edc0ea  master     -> origin/master
	Auto-merging story-index.txt
	CONFLICT (add/add): Merge conflict in story-index.txt
	Automatic merge failed; fix conflicts and then commit the result.

7. Conflict while merging story-index.txt. Edit and clear those conflict

max (master)$ vi story-index.txt

max (master)$ cat story-index.txt 
	1. The Lion and the Mouse
	2. The Frogs and the Ox
	3. The Fox and the Grapes
	4. The Donkey and the Dog

8. Push the changes back to remote repository

max (master)$ git add story-index.txt 

max (master)$ git commit -m "Fixed the issue"
	[master cf96f89] Fixed the issue
	 1 file changed, 6 deletions(-)

max (master)$ git push origin  master
	Username for 'http://git.stratos.xfusioncorp.com': max
	Password for 'http://max@git.stratos.xfusioncorp.com': 
	Counting objects: 3, done.
	Delta compression using up to 36 threads.
	Compressing objects: 100% (3/3), done.
	Writing objects: 100% (3/3), 280 bytes | 0 bytes/s, done.
	Total 3 (delta 2), reused 0 (delta 0)
	remote: . Processing 1 references
	remote: Processed 1 references in total
	To http://git.stratos.xfusioncorp.com/sarah/story-blog.git
	   acefab2..cf96f89  master -> master
 
max (master)$ git status
	On branch master
	Your branch is up-to-date with 'origin/master'.
	nothing to commit, working directory clean

max (master)$ git pull origin  master
	From http://git.stratos.xfusioncorp.com/sarah/story-blog
	 * branch            master     -> FETCH_HEAD
	Already up-to-date.
 
max (master)$ git status
	On branch master
	Your branch is up-to-date with 'origin/master'.
	nothing to commit, working directory clean

max (master)$ 

--------------------------------------------------------------------------------------------------------------------------------
Task 92: 19/Oct/2022

Managing Jinja2 Templates Using Ansible

One of the Nautilus DevOps team members is working on to develop a role for httpd installation and configuration. Work is almost completed, however there is a requirement to add a jinja2 template for index.html file. Additionally, the relevant task needs to be added inside the role. The inventory file ~/ansible/inventory is already present on jump host that can be used. Complete the task as per details mentioned below:

a. Update ~/ansible/playbook.yml playbook to run the httpd role on App Server 1.

b. Create a jinja2 template index.html.j2 under /home/thor/ansible/role/httpd/templates/ directory and add a line This file was created using Ansible on <respective server> (for example This file was created using Ansible on stapp01 in case of App Server 1). Also please make sure not to hard code the server name inside the template. Instead, use inventory_hostname variable to fetch the correct value.

c. Add a task inside /home/thor/ansible/role/httpd/tasks/main.yml to copy this template on App Server 1 under /var/www/html/index.html. Also make sure that /var/www/html/index.html file's permissions are 0777.

d. The user/group owner of /var/www/html/index.html file must be respective sudo user of the server (for example tony in case of stapp01).

Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure the playbook works this way without passing any extra arguments.


1. Go to mentioned folder and verify playbook and inventory

thor@jump_host ~$ cd /home/thor/ansible/

thor@jump_host ~/ansible$ ls -ahl
		total 20K
		drwxr-xr-x 3 thor thor 4.0K Oct 19 05:55 .
		drwxr----- 1 thor thor 4.0K Oct 19 05:55 ..
		-rw-r--r-- 1 thor thor  237 Oct 19 05:55 inventory
		-rw-r--r-- 1 thor thor   73 Oct 19 05:55 playbook.yml
		drwxr-xr-x 3 thor thor 4.0K Oct 19 05:55 role

thor@jump_host ~/ansible$ cat inventory 
		stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
		stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
		stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n

 
thor@jump_host ~/ansible$ cat playbook.yml 
		---
		- hosts: 
		  become: yes
		  become_user: root
		  roles:
		    - role/httpd

2. Edit the playbook to add the mentioned host (stapp01)

thor@jump_host ~/ansible$ vi playbook.yml 

thor@jump_host ~/ansible$ cat playbook.yml 
		---
		- hosts: stapp01						<--------------------------
		  become: yes
		  become_user: root
		  roles:
		    - role/httpd

3. Create Jinja2 template with mentioned content

thor@jump_host ~/ansible$ vi /home/thor/ansible/role/httpd/templates/index.html.j2

thor@jump_host ~/ansible$ cat /home/thor/ansible/role/httpd/templates/index.html.j2
		This file was created using Ansible on {{ ansible_hostname }}


4. Edit the mian.yml file to add a task to copy the Jinja2 template  

thor@jump_host ~/ansible$ vi /home/thor/ansible/role/httpd/tasks/main.yml

thor@jump_host ~/ansible$ cat /home/thor/ansible/role/httpd/tasks/main.yml
		---
		# tasks file for role/test

		- name: install the latest version of HTTPD
		  yum:
		    name: httpd
		    state: latest

		- name: Start service httpd
		  service:
		    name: httpd
		    state: started

		- name: Use Jinja2 template to generate index.html
		  template:
		    src: /home/thor/ansible/role/httpd/templates/index.html.j2
		    dest: /var/www/html/index.html
		    mode: "0777"
		    owner: "{{ ansible_user }}"
		    group: "{{ ansible_user }}"


5. Run the playbook

thor@jump_host ~/ansible$ ansible-playbook -i inventory playbook.yml 

		PLAY [stapp01] ******************************************************************************************************************************

		TASK [Gathering Facts] **********************************************************************************************************************
		ok: [stapp01]

		TASK [role/httpd : install the latest version of HTTPD] *************************************************************************************
		changed: [stapp01]

		TASK [role/httpd : Start service httpd] *****************************************************************************************************
		changed: [stapp01]

		TASK [role/httpd : Use Jinja2 template to generate index.html] ******************************************************************************
		changed: [stapp01]

		PLAY RECAP **********************************************************************************************************************************
		stapp01                    : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   


6. Validate the changes on app server

thor@jump_host ~/ansible$ ansible stapp01 -i inventory  -a "cat /var/www/html/index.html"
		stapp01 | CHANGED | rc=0 >>
		This file was created using Ansible on stapp01

thor@jump_host ~/ansible$ ansible stapp01 -i inventory  -a "ls -ahl /var/www/html/index.html"
		stapp01 | CHANGED | rc=0 >>
		-rwxrwxrwx 1 tony tony 47 Oct 19 06:03 /var/www/html/index.html

thor@jump_host ~/ansible$ ansible stapp02 -i inventory  -a "cat /var/www/html/index.html"
		stapp02 | FAILED | rc=1 >>
		cat: /var/www/html/index.html: No such file or directorynon-zero return code

thor@jump_host ~/ansible$ ansible stapp03 -i inventory  -a "cat /var/www/html/index.html"
		stapp03 | FAILED | rc=1 >>
		cat: /var/www/html/index.html: No such file or directorynon-zero return code

--------------------------------------------------------------------------------------------------------------------------------
Task 93: 20/Oct/2022

Ansible Inventory Update

The Nautilus DevOps team has started testing their Ansible playbooks on different servers within the stack. They have placed some playbooks under /home/thor/playbook/ directory on jump host which they want to test. Some of these playbooks have already been tested on different servers, but now they want to test them on app server 3 in Stratos DC. However, they first need to create an inventory file so that Ansible can connect to the respective app. Below are some requirements:

a. Create an ini type Ansible inventory file /home/thor/playbook/inventory on jump host.

b. Add App Server 3 in this inventory along with required variables that are needed to make it work.

c. The inventory hostname of the host should be the server name as per the wiki, for example stapp01 for app server 1 in Stratos DC.

Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure the playbook works this way without passing any extra arguments.


1. Verify mentioned playbook and other files 

thor@jump_host ~$ cd /home/thor/playbook/

thor@jump_host ~/playbook$ ls -ahl
		total 16K
		drwxr-xr-x 2 thor thor 4.0K Oct 20 09:36 .
		drwxr----- 1 thor thor 4.0K Oct 20 09:36 ..
		-rw-r--r-- 1 thor thor   36 Oct 20 09:36 ansible.cfg
		-rw-r--r-- 1 thor thor  250 Oct 20 09:36 playbook.yml

thor@jump_host ~/playbook$ cat playbook.yml 
		---
		- hosts: all
		  become: yes
		  become_user: root
		  tasks:
		    - name: Install httpd package    
		      yum: 
		        name: httpd 
		        state: installed
		    
		    - name: Start service httpd
		      service:
		        name: httpd
		        state: started

2. Create the mentioned inventory file with App Server 3 details

thor@jump_host ~/playbook$ vi /home/thor/playbook/inventory

thor@jump_host ~/playbook$ cat /home/thor/playbook/inventory
		stapp03 ansible_host=172.16.238.12 ansible_ssh_pass=BigGr33n  ansible_user=banner

3. Exectue the playbook and verify.

thor@jump_host ~/playbook$ ansible-playbook -i inventory playbook.yml

		PLAY [all] **********************************************************************************************************************************

		TASK [Gathering Facts] **********************************************************************************************************************
		ok: [stapp03]

		TASK [Install httpd package] ****************************************************************************************************************
		changed: [stapp03]

		TASK [Start service httpd] ******************************************************************************************************************
		changed: [stapp03]

		PLAY RECAP **********************************************************************************************************************************
		stapp03                    : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

thor@jump_host ~/playbook$

--------------------------------------------------------------------------------------------------------------------------------
Task 94: 22/Oct/2022

Git Fork a Repository


There is a Git server used by the Nautilus project teams. Recently a new developer Jon joined the team and needs to start working on a project. To do so, he needs to fork an existing Git repository. Below you can find more details:

    Click on the Gitea UI button on the top bar. You should be able to access the Gitea page.

    Login to Gitea server using username jon and password Jon_pass123.

    There you will see a Git repository sarah/story-blog, fork it under jon user.

Note: For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.


1. As mentioned open Gitea UI

2. Login to Gitea server using username jon and password Jon_pass123

3. Go to repo sarah/story-blog

4. Right top corner option for fork the repo

5. The new forked repo will be under jon/story-blog

--------------------------------------------------------------------------------------------------------------------------------
Task 95: 23/Oct/2022

Create a Docker Image From Container

One of the Nautilus developer was working to test new changes on a container. He wants to keep a backup of his changes to the container. A new request has been raised for the DevOps team to create a new image from this container. Below are more details about it:

a. Create an image apps:xfusion on Application Server 2 from a container ubuntu_latest that is running on same server.


1. 1. Login on app server  & Switch to  root user

thor@jump_host ~$ ssh steve@stapp02
		The authenticity of host 'stapp02 (172.16.238.11)' can't be established.
		ECDSA key fingerprint is SHA256:LmeMEQk6Mx7vqZWW6o6Knsvsgqwb4FlOk7e/cSvtfms.
		ECDSA key fingerprint is MD5:b8:e8:31:0f:29:8b:c7:be:26:6e:42:aa:56:51:29:8c.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'stapp02,172.16.238.11' (ECDSA) to the list of known hosts.
		steve@stapp02's password: 

[steve@stapp02 ~]$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for steve: 


2. Check existing docker container ubuntu_latest running status 

[root@stapp02 ~]# docker ps 
		CONTAINER ID   IMAGE     COMMAND   CREATED              STATUS              PORTS     NAMES
		e2d2e33ed57c   ubuntu    "bash"    About a minute ago   Up About a minute             ubuntu_latest

3. Check current docker images on your system

[root@stapp02 ~]# docker images
		REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
		ubuntu       latest    216c552ea5ba   2 weeks ago   77.8MB

4. Create new image from given contianer as per task

[root@stapp02 ~]# docker commit ubuntu_latest apps:xfusion
		sha256:2158e1542b17e48950a0c1fd45ab50729fbd9dd344210b5771b0113955100168

5. Verify and validate the new  docker image created on your system

[root@stapp02 ~]# docker images
		REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
		apps         xfusion   2158e1542b17   6 seconds ago   116MB
		ubuntu       latest    216c552ea5ba   2 weeks ago     77.8MB

--------------------------------------------------------------------------------------------------------------------------------
Task 96: 25/Oct/2022

Puppet Multi-Packages Installation

Some new changes need to be made on some of the app servers in Stratos Datacenter. There are some packages that need to be installed on the app server 2. We want to install these packages using puppet only.

    Puppet master is already installed on Jump Server.

    Create a puppet programming file blog.pp under /etc/puppetlabs/code/environments/production/manifests on master node i.e on Jump Server and perform below mentioned tasks using the same.

    Define a class multi_package_node for agent node 2 i.e app server 2. Install net-tools and unzip packages on the agent node 2.

Notes: :- Please make sure to run the puppet agent test using sudo on agent nodes, otherwise you can face certificate issues. In that case you will have to clean the certificates first and then you will be able to run the puppet agent test.

:- Before clicking on the Check button please make sure to verify puppet server and puppet agent services are up and running on the respective servers, also please make sure to run puppet agent test to apply/test the changes manually first.

:- Please note that once lab is loaded, the puppet server service should start automatically on puppet master server, however it can take upto 2-3 minutes to start.


1. Switch to root user on jump host to create puppet programming file

thor@jump_host ~$ sudo su - 

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for thor: 

2. Go to mentione folder location and create blog.pp  file with given requirements

root@jump_host ~# cd /etc/puppetlabs/code/environments/production/manifests/
 
root@jump_host /etc/puppetlabs/code/environments/production/manifests# ls -ahl
		total 8.0K
		drwxr-xr-x 1 puppet puppet 4.0K Jul 13  2021 .
		drwxr-xr-x 1 puppet puppet 4.0K Aug  9  2021 ..
 
root@jump_host /etc/puppetlabs/code/environments/production/manifests# vi blog.pp

root@jump_host /etc/puppetlabs/code/environments/production/manifests# cat blog.pp 
		class multi_package_node {
		$multi_package = [ 'net-tools', 'unzip']
		    package { $multi_package: ensure => 'installed' }
		}

		node 'stapp02.stratos.xfusioncorp.com' {
		  include multi_package_node
		}

3. Login to App Server 2 and switch to root user

root@jump_host /etc/puppetlabs/code/environments/production/manifests# ssh steve@stapp02
		The authenticity of host 'stapp02 (172.16.238.11)' can't be established.
		ECDSA key fingerprint is SHA256:JGrKQGk9+m0eDIxj8Ttwk2oL5abYgUcCazjXSp36dxs.
		ECDSA key fingerprint is MD5:ec:b0:32:f5:c4:6d:68:07:03:f0:ad:7a:a6:a7:f9:47.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'stapp02,172.16.238.11' (ECDSA) to the list of known hosts.
		steve@stapp02's password: 

[steve@stapp02 ~]$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for steve: 

4. Check if given packages are already  installed

[root@stapp02 ~]# rpm -qa|grep -e net-tools -e unzip

5. Run the puppet agent to pull configuration from puppet server 

[root@stapp02 ~]# puppet agent -tv
		Info: Using configured environment 'production'
		Info: Retrieving pluginfacts
		Info: Retrieving plugin
		Info: Retrieving locales
		Info: Caching catalog for stapp02.stratos.xfusioncorp.com
		Info: Applying configuration version '1666708349'
		Notice: /Stage[main]/Multi_package_node/Package[net-tools]/ensure: created
		Notice: /Stage[main]/Multi_package_node/Package[unzip]/ensure: created
		Notice: Applied catalog in 14.67 seconds

6. Validate the task by checking required packages installed

[root@stapp02 ~]# rpm -qa|grep -e net-tools -e unzip
		unzip-6.0-24.el7_9.x86_64
		net-tools-2.0-0.25.20131004git.el7.x86_64

[root@stapp02 ~]#

--------------------------------------------------------------------------------------------------------------------------------
Task 97: 27/Oct/2022

Puppet Add Users


A new teammate has joined the Nautilus application development team, the application development team has asked the DevOps team to create a new user account for the new teammate on application server 1 in Stratos Datacenter. The task needs to be performed using Puppet only. You can find more details below about the task.

Create a Puppet programming file official.pp under /etc/puppetlabs/code/environments/production/manifests directory on master node i.e Jump Server, and using Puppet user resource add a user on all app servers as mentioned below:

    Create a user ravi and set its UID to 1879 on Puppet agent nodes 1 i.e App Servers 1.

Notes: :- Please make sure to run the puppet agent test using sudo on agent nodes, otherwise you can face certificate issues. In that case you will have to clean the certificates first and then you will be able to run the puppet agent test.

:- Before clicking on the Check button please make sure to verify puppet server and puppet agent services are up and running on the respective servers, also please make sure to run puppet agent test to apply/test the changes manually first.

:- Please note that once lab is loaded, the puppet server service should start automatically on puppet master server, however it can take upto 2-3 minutes to start.


1. Switch to root user and go to mentioned folder

thor@jump_host ~$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for thor: 
 
root@jump_host ~# cd /etc/puppetlabs/code/environments/production/manifests/

root@jump_host /etc/puppetlabs/code/environments/production/manifests# ls -ahl
		total 8.0K
		drwxr-xr-x 1 puppet puppet 4.0K Jul 13  2021 .
		drwxr-xr-x 1 puppet puppet 4.0K Aug  9  2021 ..

2. Create the mentioned puppet programming file with given requirements 

root@jump_host /etc/puppetlabs/code/environments/production/manifests# vi official.pp

root@jump_host /etc/puppetlabs/code/environments/production/manifests# cat official.pp
		class user_creator {
			user { 'ravi':
						ensure   => present,
						uid => 1879,
		  }
		}

		node 'stapp01.stratos.xfusioncorp.com' {
			include user_creator
		}

3. Validate the puppet programming file 

root@jump_host /etc/puppetlabs/code/environments/production/manifests# puppet parser validate official.pp 

4. Login to app server 01 and switch to root 

root@jump_host /etc/puppetlabs/code/environments/production/manifests# ssh tony@stapp01
		The authenticity of host 'stapp01 (172.16.238.10)' can't be established.
		ECDSA key fingerprint is SHA256:CxGn3WKmroQOoXAC5aufEdVzoDTknATLtoQHDcP0WEI.
		ECDSA key fingerprint is MD5:f1:cb:d0:b8:e6:b4:74:04:70:d2:ba:4f:12:ce:a3:23.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'stapp01,172.16.238.10' (ECDSA) to the list of known hosts.
		tony@stapp01's password: 

[tony@stapp01 ~]$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for tony: 

5. Run puppet agent to pull latest configuration from puppet server

[root@stapp01 ~]# puppet agent -tv
		Info: Using configured environment 'production'
		Info: Retrieving pluginfacts
		Info: Retrieving plugin
		Info: Retrieving locales
		Info: Caching catalog for stapp01.stratos.xfusioncorp.com
		Info: Applying configuration version '1666854162'
		Notice: /Stage[main]/User_creator/User[ravi]/ensure: created
		Notice: Applied catalog in 0.06 seconds

6. Verify the new user created 

[root@stapp01 ~]# cat /etc/passwd |grep ravi
	ravi:x:1879:1879::/home/ravi:/bin/bash

--------------------------------------------------------------------------------------------------------------------------------
Task 98: 28/Oct/2022

Kubernetes Time Check Pod

The Nautilus DevOps team want to create a time check pod in a particular Kubernetes namespace and record the logs. This might be initially used only for testing purposes, but later can be implemented in an existing cluster. Please find more details below about the task and perform it.

    Create a pod called time-check in the datacenter namespace. This pod should run a container called time-check, container should use the busybox image with latest tag only and remember to mention tag i.e busybox:latest.

    Create a config map called time-config with the data TIME_FREQ=12 in the same namespace.

    The time-check container should run the command: while true; do date; sleep $TIME_FREQ;done and should write the result to the location /opt/dba/time/time-check.log. Remember you will also need to add an environmental variable TIME_FREQ in the container, which should pick value from the config map TIME_FREQ key.

    Create a volume log-volume and mount the same on /opt/dba/time within the container.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.


1. Check kubectl utility config on jump server 

thor@jump_host ~$ kubectl get namespace
		NAME                 STATUS   AGE
		default              Active   115m
		kube-node-lease      Active   115m
		kube-public          Active   115m
		kube-system          Active   115m
		local-path-storage   Active   115m

thor@jump_host ~$ kubectl get pods
	No resources found in default namespace.

2. Create new namespace 

thor@jump_host ~$ kubectl create namespace datacenter
	namespace/datacenter created

thor@jump_host ~$ kubectl get namespace
		NAME                 STATUS   AGE
		datacenter           Active   4s
		default              Active   116m
		kube-node-lease      Active   116m
		kube-public          Active   116m
		kube-system          Active   116m
		local-path-storage   Active   115m

3. Create YAML file as per given requirements 

thor@jump_host ~$ vi /tmp/time_check.yaml
 
thor@jump_host ~$ cat /tmp/time_check.yaml 

		apiVersion: v1
		kind: Namespace
		metadata:
		  name: datacenter

		---
		apiVersion: v1
		kind: ConfigMap
		metadata:
		  name: time-config
		  namespace: datacenter
		data:
		    TIME_FREQ: "12"

		---    
		apiVersion: v1
		kind: Pod
		metadata:
		  name: time-check
		  namespace: datacenter
		spec:
		  containers:
		    - name: time-check
		      image: busybox:latest
		      command: [ "sh", "-c", "while true; do date; sleep $TIME_FREQ;done >> /opt/dba/time/time-check.log" ]
		      env:
		        - name: TIME_FREQ
		          valueFrom:
		            configMapKeyRef:
		              name: time-config
		              key: TIME_FREQ
		      volumeMounts:
		      - name: log-volume
		        mountPath: /opt/dba/time
		  volumes:
		    - name: log-volume
		      emptyDir : {}
		  restartPolicy: Never

4. Create Pod using the YAML file

thor@jump_host ~$ kubectl create -f  /tmp/time_check.yaml 
		configmap/time-config created
		pod/time-check created
		Error from server (AlreadyExists): error when creating "/tmp/time_check.yaml": namespaces "datacenter" already exists

5. Verify the pod running status 

thor@jump_host ~$ kubectl get pods -n datacenter
		NAME         READY   STATUS    RESTARTS   AGE
		time-check   1/1     Running   0          29s

thor@jump_host ~$ kubectl get pods -n datacenter
		NAME         READY   STATUS    RESTARTS   AGE
		time-check   1/1     Running   0          32s

--------------------------------------------------------------------------------------------------------------------------------
Task 99: 04/Nov/2022

Deploy MySQL on Kubernetes



A new MySQL server needs to be deployed on Kubernetes cluster. The Nautilus DevOps team was working on to gather the requirements. Recently they were able to finalize the requirements and shared them with the team members to start working on it. Below you can find the details:

1.) Create a PersistentVolume mysql-pv, its capacity should be 250Mi, set other parameters as per your preference.

2.) Create a PersistentVolumeClaim to request this PersistentVolume storage. Name it as mysql-pv-claim and request a 250Mi of storage. Set other parameters as per your preference.

3.) Create a deployment named mysql-deployment, use any mysql image as per your preference. Mount the PersistentVolume at mount path /var/lib/mysql.

4.) Create a NodePort type service named mysql and set nodePort to 30007.

5.) Create a secret named mysql-root-pass having a key pair value, where key is password and its value is YUIidhb667, create another secret named mysql-user-pass having some key pair values, where frist key is username and its value is kodekloud_pop, second key is password and value is GyQkFRVNr3, create one more secret named mysql-db-url, key name is database and value is kodekloud_db7

6.) Define some Environment variables within the container:

a) name: MYSQL_ROOT_PASSWORD, should pick value from secretKeyRef name: mysql-root-pass and key: password

b) name: MYSQL_DATABASE, should pick value from secretKeyRef name: mysql-db-url and key: database

c) name: MYSQL_USER, should pick value from secretKeyRef name: mysql-user-pass key key: username

d) name: MYSQL_PASSWORD, should pick value from secretKeyRef name: mysql-user-pass and key: password

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.


1. Check Kubectl service status and config

thor@jump_host ~$ kubectl get all
		NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
		service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   38m

thor@jump_host ~$ kubectl get service
		NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
		kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   38m

thor@jump_host ~$ kubectl get pv
		No resources found

thor@jump_host ~$ kubectl get pvc
		No resources found in default namespace.

2. Create Kubectl Secrets as per given task

thor@jump_host ~$ kubectl create secret generic mysql-root-pass --from-literal=password=YUIidhb667
		secret/mysql-root-pass created
 
thor@jump_host ~$ kubectl create secret generic mysql-user-pass --from-literal=username=kodekloud_pop --from-literal=password=GyQkFRVNr3
		secret/mysql-user-pass created

thor@jump_host ~$ kubectl create secret generic mysql-db-url --from-literal=database=kodekloud_db7
		secret/mysql-db-url created

3. Verify Secrets creation

thor@jump_host ~$ kubectl get all
		NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
		service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   40m

thor@jump_host ~$ kubectl get secrets
		NAME                  TYPE                                  DATA   AGE
		default-token-d62nx   kubernetes.io/service-account-token   3      40m
		mysql-db-url          Opaque                                1      16s
		mysql-root-pass       Opaque                                1      47s
		mysql-user-pass       Opaque                                2      28s

4. Create YAML file as per requirement
 
thor@jump_host ~$ vi /tmp/mysql_deploy.yaml
 
thor@jump_host ~$ cat /tmp/mysql_deploy.yaml 
		---                                                                                           
		apiVersion: v1                                                                                
		kind: PersistentVolume                                                                        
		metadata:                                                                                     
		  name: mysql-pv                                                                              
		spec:                                                                                         
		  capacity:                                                                                   
		    storage: 250Mi     
		  accessModes:                                                                                
		    - ReadWriteOnce                                                                           
		  hostPath:                                                                                   
		    path: "/var/lib/mysql"                                                                    
		---                                                                                           
		apiVersion: v1                                                                                
		kind: PersistentVolumeClaim                                                                   
		metadata:                                                                                     
		  name: mysql-pv-claim                                                                        
		spec:                                                                                         
		  accessModes:                                                                                
		    - ReadWriteOnce                                                                           
		  resources:                                                                                  
		    requests:                                                                                 
		      storage: 250Mi                                                                          
		---
		apiVersion: v1
		kind: Service
		metadata:
		  name: mysql
		spec:
		  type: NodePort
		  selector:
		    app: mysql
		  ports:
		    - port: 3306
		      targetPort: 3306
		      nodePort: 30007
		---       
		apiVersion: apps/v1
		kind: Deployment
		metadata:
		  name: mysql-deployment
		  labels:
		    app: mysql
		spec:
		  replicas: 1
		  selector:
		    matchLabels:
		      app: mysql
		  template:
		    metadata:
		      labels:
		        app: mysql
		    spec:
		      volumes:
		      - name: mysql-pv
		        persistentVolumeClaim:
		          claimName: mysql-pv-claim
		      containers:
		      - name: mysql
		        image: mysql:latest
		        env:
		        - name: MYSQL_ROOT_PASSWORD
		          valueFrom:
		            secretKeyRef:
		              name: mysql-root-pass
		              key: password
		        - name: MYSQL_DATABASE
		          valueFrom:
		            secretKeyRef:
		              name: mysql-db-url
		              key: database
		        - name: MYSQL_USER
		          valueFrom:
		            secretKeyRef:
		              name: mysql-user-pass
		              key: username
		        - name: MYSQL_PASSWORD
		          valueFrom:
		            secretKeyRef:
		              name: mysql-user-pass
		              key: password
		        volumeMounts:
		        - name: mysql-pv
		          mountPath: /var/lib/mysql
		        ports:
		        - containerPort: 3306
		          name: mysql


5. Create Pods  using the YAML file

thor@jump_host ~$ kubectl create -f  /tmp/mysql_deploy.yaml 
		persistentvolume/mysql-pv created
		persistentvolumeclaim/mysql-pv-claim created
		service/mysql created
		deployment.apps/mysql-deployment created

6. Verify the components created 

thor@jump_host ~$ kubectl get pv
		NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                    STORAGECLASS   REASON   AGE
		mysql-pv                                   250Mi      RWO            Retain           Available                                                    13s
		pvc-c497c610-d100-4616-b073-56b04050cfa3   250Mi      RWO            Delete           Bound       default/mysql-pv-claim   standard                9s

thor@jump_host ~$ kubectl get pvc
		NAME             STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
		mysql-pv-claim   Bound    pvc-c497c610-d100-4616-b073-56b04050cfa3   250Mi      RWO            standard       19s

7. Wait for Pods to get into running status and check the ENV for MYSQL env variables in the pod created

thor@jump_host ~$ kubectl get all
		NAME                                    READY   STATUS              RESTARTS   AGE
		pod/mysql-deployment-6ccf56667c-dmrw5   0/1     ContainerCreating   0          32s

		NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
		service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP          42m
		service/mysql        NodePort    10.96.210.211   <none>        3306:30007/TCP   32s

		NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
		deployment.apps/mysql-deployment   0/1     1            0           32s

		NAME                                          DESIRED   CURRENT   READY   AGE
		replicaset.apps/mysql-deployment-6ccf56667c   1         1         0       32s

thor@jump_host ~$ kubectl get all
		NAME                                    READY   STATUS    RESTARTS   AGE
		pod/mysql-deployment-6ccf56667c-dmrw5   1/1     Running   0          45s

		NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
		service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP          42m
		service/mysql        NodePort    10.96.210.211   <none>        3306:30007/TCP   45s

		NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
		deployment.apps/mysql-deployment   1/1     1            1           45s

		NAME                                          DESIRED   CURRENT   READY   AGE
		replicaset.apps/mysql-deployment-6ccf56667c   1         1         1       45s


thor@jump_host ~$ kubectl exec -it mysql-deployment-6ccf56667c-dmrw5 -- /bin/bash

bash-4.4# printenv
		MYSQL_PASSWORD=GyQkFRVNr3
		MYSQL_PORT_3306_TCP_PROTO=tcp
		HOSTNAME=mysql-deployment-6ccf56667c-dmrw5
		MYSQL_DATABASE=kodekloud_db7
		KUBERNETES_PORT_443_TCP_PROTO=tcp
		MYSQL_SERVICE_PORT=3306
		KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
		MYSQL_ROOT_PASSWORD=YUIidhb667
		MYSQL_PORT=tcp://10.96.210.211:3306
		KUBERNETES_PORT=tcp://10.96.0.1:443
		MYSQL_PORT_3306_TCP=tcp://10.96.210.211:3306
		PWD=/
		HOME=/root
		MYSQL_MAJOR=8.0
		GOSU_VERSION=1.14
		MYSQL_USER=kodekloud_pop
		MYSQL_PORT_3306_TCP_PORT=3306
		KUBERNETES_SERVICE_PORT_HTTPS=443
		MYSQL_PORT_3306_TCP_ADDR=10.96.210.211
		KUBERNETES_PORT_443_TCP_PORT=443
		MYSQL_VERSION=8.0.31-1.el8
		KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
		TERM=xterm
		SHLVL=1
		KUBERNETES_SERVICE_PORT=443
		PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
		KUBERNETES_SERVICE_HOST=10.96.0.1
		MYSQL_SHELL_VERSION=8.0.31-1.el8
		MYSQL_SERVICE_HOST=10.96.210.211
		_=/usr/bin/printenv
bash-4.4# 

--------------------------------------------------------------------------------------------------------------------------------
Task 100: 05/Nov/2022

Git Install and Create Bare Repository

The Nautilus development team shared requirements with the DevOps team regarding new application development.—specifically, they want to set up a Git repository for that project. Create a Git repository on Storage server in Stratos DC as per details given below:

    Install git package using yum on Storage server.

    After that create a bare repository /opt/news.git (make sure to use exact name).


1. Login on storage server and switch to root user

thor@jump_host ~$ ssh natasha@ststor01
		The authenticity of host 'ststor01 (172.16.238.15)' can't be established.
		ECDSA key fingerprint is SHA256:IEbMfpjmum1MkMYwoKYJ6cm72/aRpYpB9G787u7jPMM.
		ECDSA key fingerprint is MD5:55:d5:fc:19:3e:f2:bb:47:77:9d:7d:a7:ea:cc:e7:61.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'ststor01,172.16.238.15' (ECDSA) to the list of known hosts.
		natasha@ststor01's password: 

[natasha@ststor01 ~]$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for natasha: 

2. Install git package using yum 

[root@ststor01 ~]# rpm -qa |grep git

[root@ststor01 ~]# yum install -y git
		Loaded plugins: fastestmirror, ovl
		Determining fastest mirrors
		 * base: ftpmirror.your.org
		 * extras: mirror.genesishosting.com
		 * updates: tx-mirror.tier.net
		base                                                                                                                  | 3.6 kB  00:00:00     
		extras                                                                                                                | 2.9 kB  00:00:00     
		updates                                                                                                               | 2.9 kB  00:00:00     
		(1/4): base/7/x86_64/group_gz                                                                                         | 153 kB  00:00:00     
		(2/4): extras/7/x86_64/primary_db                                                                                     | 249 kB  00:00:00     
		(3/4): base/7/x86_64/primary_db                                                                                       | 6.1 MB  00:00:00     
		(4/4): updates/7/x86_64/primary_db                                                                                    |  17 MB  00:00:00     
		Resolving Dependencies
		--> Running transaction check
		---> Package git.x86_64 0:1.8.3.1-23.el7_8 will be installed
		--> Processing Dependency: perl-Git = 1.8.3.1-23.el7_8 for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl >= 5.008 for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: rsync for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(warnings) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(vars) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(strict) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(lib) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(Term::ReadKey) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(Git) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(Getopt::Long) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::stat) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::Temp) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::Spec) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::Path) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::Find) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::Copy) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::Basename) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(Exporter) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(Error) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: openssh-clients for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: less for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: /usr/bin/perl for package: git-1.8.3.1-23.el7_8.x86_64
		--> Running transaction check
		---> Package less.x86_64 0:458-9.el7 will be installed
		--> Processing Dependency: groff-base for package: less-458-9.el7.x86_64
		---> Package openssh-clients.x86_64 0:7.4p1-22.el7_9 will be installed
		--> Processing Dependency: openssh = 7.4p1-22.el7_9 for package: openssh-clients-7.4p1-22.el7_9.x86_64
		--> Processing Dependency: libedit.so.0()(64bit) for package: openssh-clients-7.4p1-22.el7_9.x86_64
		---> Package perl.x86_64 4:5.16.3-299.el7_9 will be installed
		--> Processing Dependency: perl-libs = 4:5.16.3-299.el7_9 for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Socket) >= 1.3 for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Scalar::Util) >= 1.10 for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl-macros for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl-libs for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(threads::shared) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(threads) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(constant) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Time::Local) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Time::HiRes) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Storable) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Socket) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Scalar::Util) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Pod::Simple::XHTML) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Pod::Simple::Search) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Filter::Util::Call) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Carp) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: libperl.so()(64bit) for package: 4:perl-5.16.3-299.el7_9.x86_64
		---> Package perl-Error.noarch 1:0.17020-2.el7 will be installed
		---> Package perl-Exporter.noarch 0:5.68-3.el7 will be installed
		---> Package perl-File-Path.noarch 0:2.09-2.el7 will be installed
		---> Package perl-File-Temp.noarch 0:0.23.01-3.el7 will be installed
		---> Package perl-Getopt-Long.noarch 0:2.40-3.el7 will be installed
		--> Processing Dependency: perl(Pod::Usage) >= 1.14 for package: perl-Getopt-Long-2.40-3.el7.noarch
		--> Processing Dependency: perl(Text::ParseWords) for package: perl-Getopt-Long-2.40-3.el7.noarch
		---> Package perl-Git.noarch 0:1.8.3.1-23.el7_8 will be installed
		---> Package perl-PathTools.x86_64 0:3.40-5.el7 will be installed
		---> Package perl-TermReadKey.x86_64 0:2.30-20.el7 will be installed
		---> Package rsync.x86_64 0:3.1.2-11.el7_9 will be installed
		--> Running transaction check
		---> Package groff-base.x86_64 0:1.22.2-8.el7 will be installed
		---> Package libedit.x86_64 0:3.0-12.20121213cvs.el7 will be installed
		---> Package openssh.x86_64 0:7.4p1-21.el7 will be updated
		--> Processing Dependency: openssh = 7.4p1-21.el7 for package: openssh-server-7.4p1-21.el7.x86_64
		---> Package openssh.x86_64 0:7.4p1-22.el7_9 will be an update
		---> Package perl-Carp.noarch 0:1.26-244.el7 will be installed
		---> Package perl-Filter.x86_64 0:1.49-3.el7 will be installed
		---> Package perl-Pod-Simple.noarch 1:3.28-4.el7 will be installed
		--> Processing Dependency: perl(Pod::Escapes) >= 1.04 for package: 1:perl-Pod-Simple-3.28-4.el7.noarch
		--> Processing Dependency: perl(Encode) for package: 1:perl-Pod-Simple-3.28-4.el7.noarch
		---> Package perl-Pod-Usage.noarch 0:1.63-3.el7 will be installed
		--> Processing Dependency: perl(Pod::Text) >= 3.15 for package: perl-Pod-Usage-1.63-3.el7.noarch
		--> Processing Dependency: perl-Pod-Perldoc for package: perl-Pod-Usage-1.63-3.el7.noarch
		---> Package perl-Scalar-List-Utils.x86_64 0:1.27-248.el7 will be installed
		---> Package perl-Socket.x86_64 0:2.010-5.el7 will be installed
		---> Package perl-Storable.x86_64 0:2.45-3.el7 will be installed
		---> Package perl-Text-ParseWords.noarch 0:3.29-4.el7 will be installed
		---> Package perl-Time-HiRes.x86_64 4:1.9725-3.el7 will be installed
		---> Package perl-Time-Local.noarch 0:1.2300-2.el7 will be installed
		---> Package perl-constant.noarch 0:1.27-2.el7 will be installed
		---> Package perl-libs.x86_64 4:5.16.3-299.el7_9 will be installed
		---> Package perl-macros.x86_64 4:5.16.3-299.el7_9 will be installed
		---> Package perl-threads.x86_64 0:1.87-4.el7 will be installed
		---> Package perl-threads-shared.x86_64 0:1.43-6.el7 will be installed
		--> Running transaction check
		---> Package openssh-server.x86_64 0:7.4p1-21.el7 will be updated
		---> Package openssh-server.x86_64 0:7.4p1-22.el7_9 will be an update
		---> Package perl-Encode.x86_64 0:2.51-7.el7 will be installed
		---> Package perl-Pod-Escapes.noarch 1:1.04-299.el7_9 will be installed
		---> Package perl-Pod-Perldoc.noarch 0:3.20-4.el7 will be installed
		--> Processing Dependency: perl(parent) for package: perl-Pod-Perldoc-3.20-4.el7.noarch
		--> Processing Dependency: perl(HTTP::Tiny) for package: perl-Pod-Perldoc-3.20-4.el7.noarch
		---> Package perl-podlators.noarch 0:2.5.1-3.el7 will be installed
		--> Running transaction check
		---> Package perl-HTTP-Tiny.noarch 0:0.033-3.el7 will be installed
		---> Package perl-parent.noarch 1:0.225-244.el7 will be installed
		--> Finished Dependency Resolution

		Dependencies Resolved

		=============================================================================================================================================
		 Package                                  Arch                     Version                                   Repository                 Size
		=============================================================================================================================================
		Installing:
		 git                                      x86_64                   1.8.3.1-23.el7_8                          base                      4.4 M
		Installing for dependencies:
		 groff-base                               x86_64                   1.22.2-8.el7                              base                      942 k
		 less                                     x86_64                   458-9.el7                                 base                      120 k
		 libedit                                  x86_64                   3.0-12.20121213cvs.el7                    base                       92 k
		 openssh-clients                          x86_64                   7.4p1-22.el7_9                            updates                   655 k
		 perl                                     x86_64                   4:5.16.3-299.el7_9                        updates                   8.0 M
		 perl-Carp                                noarch                   1.26-244.el7                              base                       19 k
		 perl-Encode                              x86_64                   2.51-7.el7                                base                      1.5 M
		 perl-Error                               noarch                   1:0.17020-2.el7                           base                       32 k
		 perl-Exporter                            noarch                   5.68-3.el7                                base                       28 k
		 perl-File-Path                           noarch                   2.09-2.el7                                base                       26 k
		 perl-File-Temp                           noarch                   0.23.01-3.el7                             base                       56 k
		 perl-Filter                              x86_64                   1.49-3.el7                                base                       76 k
		 perl-Getopt-Long                         noarch                   2.40-3.el7                                base                       56 k
		 perl-Git                                 noarch                   1.8.3.1-23.el7_8                          base                       56 k
		 perl-HTTP-Tiny                           noarch                   0.033-3.el7                               base                       38 k
		 perl-PathTools                           x86_64                   3.40-5.el7                                base                       82 k
		 perl-Pod-Escapes                         noarch                   1:1.04-299.el7_9                          updates                    52 k
		 perl-Pod-Perldoc                         noarch                   3.20-4.el7                                base                       87 k
		 perl-Pod-Simple                          noarch                   1:3.28-4.el7                              base                      216 k
		 perl-Pod-Usage                           noarch                   1.63-3.el7                                base                       27 k
		 perl-Scalar-List-Utils                   x86_64                   1.27-248.el7                              base                       36 k
		 perl-Socket                              x86_64                   2.010-5.el7                               base                       49 k
		 perl-Storable                            x86_64                   2.45-3.el7                                base                       77 k
		 perl-TermReadKey                         x86_64                   2.30-20.el7                               base                       31 k
		 perl-Text-ParseWords                     noarch                   3.29-4.el7                                base                       14 k
		 perl-Time-HiRes                          x86_64                   4:1.9725-3.el7                            base                       45 k
		 perl-Time-Local                          noarch                   1.2300-2.el7                              base                       24 k
		 perl-constant                            noarch                   1.27-2.el7                                base                       19 k
		 perl-libs                                x86_64                   4:5.16.3-299.el7_9                        updates                   690 k
		 perl-macros                              x86_64                   4:5.16.3-299.el7_9                        updates                    44 k
		 perl-parent                              noarch                   1:0.225-244.el7                           base                       12 k
		 perl-podlators                           noarch                   2.5.1-3.el7                               base                      112 k
		 perl-threads                             x86_64                   1.87-4.el7                                base                       49 k
		 perl-threads-shared                      x86_64                   1.43-6.el7                                base                       39 k
		 rsync                                    x86_64                   3.1.2-11.el7_9                            updates                   408 k
		Updating for dependencies:
		 openssh                                  x86_64                   7.4p1-22.el7_9                            updates                   510 k
		 openssh-server                           x86_64                   7.4p1-22.el7_9                            updates                   459 k

		Transaction Summary
		=============================================================================================================================================
		Install  1 Package  (+35 Dependent packages)
		Upgrade             (  2 Dependent packages)

		Total download size: 19 M
		Downloading packages:
		Delta RPMs disabled because /usr/bin/applydeltarpm not installed.
		(1/38): groff-base-1.22.2-8.el7.x86_64.rpm                                                                            | 942 kB  00:00:00     
		(2/38): git-1.8.3.1-23.el7_8.x86_64.rpm                                                                               | 4.4 MB  00:00:00     
		(3/38): less-458-9.el7.x86_64.rpm                                                                                     | 120 kB  00:00:00     
		(4/38): libedit-3.0-12.20121213cvs.el7.x86_64.rpm                                                                     |  92 kB  00:00:00     
		(5/38): openssh-7.4p1-22.el7_9.x86_64.rpm                                                                             | 510 kB  00:00:00     
		(6/38): openssh-clients-7.4p1-22.el7_9.x86_64.rpm                                                                     | 655 kB  00:00:00     
		(7/38): openssh-server-7.4p1-22.el7_9.x86_64.rpm                                                                      | 459 kB  00:00:00     
		(8/38): perl-Carp-1.26-244.el7.noarch.rpm                                                                             |  19 kB  00:00:00     
		(9/38): perl-Error-0.17020-2.el7.noarch.rpm                                                                           |  32 kB  00:00:00     
		(10/38): perl-Exporter-5.68-3.el7.noarch.rpm                                                                          |  28 kB  00:00:00     
		(11/38): perl-File-Path-2.09-2.el7.noarch.rpm                                                                         |  26 kB  00:00:00     
		(12/38): perl-File-Temp-0.23.01-3.el7.noarch.rpm                                                                      |  56 kB  00:00:00     
		(13/38): perl-Filter-1.49-3.el7.x86_64.rpm                                                                            |  76 kB  00:00:00     
		(14/38): perl-Getopt-Long-2.40-3.el7.noarch.rpm                                                                       |  56 kB  00:00:00     
		(15/38): perl-Git-1.8.3.1-23.el7_8.noarch.rpm                                                                         |  56 kB  00:00:00     
		(16/38): perl-HTTP-Tiny-0.033-3.el7.noarch.rpm                                                                        |  38 kB  00:00:00     
		(17/38): perl-PathTools-3.40-5.el7.x86_64.rpm                                                                         |  82 kB  00:00:00     
		(18/38): perl-Pod-Escapes-1.04-299.el7_9.noarch.rpm                                                                   |  52 kB  00:00:00     
		(19/38): perl-Encode-2.51-7.el7.x86_64.rpm                                                                            | 1.5 MB  00:00:00     
		(20/38): perl-Pod-Perldoc-3.20-4.el7.noarch.rpm                                                                       |  87 kB  00:00:00     
		(21/38): perl-5.16.3-299.el7_9.x86_64.rpm                                                                             | 8.0 MB  00:00:00     
		(22/38): perl-Pod-Usage-1.63-3.el7.noarch.rpm                                                                         |  27 kB  00:00:00     
		(23/38): perl-Pod-Simple-3.28-4.el7.noarch.rpm                                                                        | 216 kB  00:00:00     
		(24/38): perl-Scalar-List-Utils-1.27-248.el7.x86_64.rpm                                                               |  36 kB  00:00:00     
		(25/38): perl-Storable-2.45-3.el7.x86_64.rpm                                                                          |  77 kB  00:00:00     
		(26/38): perl-TermReadKey-2.30-20.el7.x86_64.rpm                                                                      |  31 kB  00:00:00     
		(27/38): perl-Text-ParseWords-3.29-4.el7.noarch.rpm                                                                   |  14 kB  00:00:00     
		(28/38): perl-Time-HiRes-1.9725-3.el7.x86_64.rpm                                                                      |  45 kB  00:00:00     
		(29/38): perl-Time-Local-1.2300-2.el7.noarch.rpm                                                                      |  24 kB  00:00:00     
		(30/38): perl-Socket-2.010-5.el7.x86_64.rpm                                                                           |  49 kB  00:00:00     
		(31/38): perl-constant-1.27-2.el7.noarch.rpm                                                                          |  19 kB  00:00:00     
		(32/38): perl-parent-0.225-244.el7.noarch.rpm                                                                         |  12 kB  00:00:00     
		(33/38): perl-podlators-2.5.1-3.el7.noarch.rpm                                                                        | 112 kB  00:00:00     
		(34/38): perl-libs-5.16.3-299.el7_9.x86_64.rpm                                                                        | 690 kB  00:00:00     
		(35/38): perl-threads-shared-1.43-6.el7.x86_64.rpm                                                                    |  39 kB  00:00:00     
		(36/38): perl-threads-1.87-4.el7.x86_64.rpm                                                                           |  49 kB  00:00:00     
		(37/38): rsync-3.1.2-11.el7_9.x86_64.rpm                                                                              | 408 kB  00:00:00     
		(38/38): perl-macros-5.16.3-299.el7_9.x86_64.rpm                                                                      |  44 kB  00:00:00     
		---------------------------------------------------------------------------------------------------------------------------------------------
		Total                                                                                                         18 MB/s |  19 MB  00:00:01     
		Running transaction check
		Running transaction test
		Transaction test succeeded
		Running transaction
		  Installing : groff-base-1.22.2-8.el7.x86_64                                                                                           1/40 
		  Updating   : openssh-7.4p1-22.el7_9.x86_64                                                                                            2/40 
		  Installing : 1:perl-parent-0.225-244.el7.noarch                                                                                       3/40 
		  Installing : perl-HTTP-Tiny-0.033-3.el7.noarch                                                                                        4/40 
		  Installing : perl-podlators-2.5.1-3.el7.noarch                                                                                        5/40 
		  Installing : perl-Pod-Perldoc-3.20-4.el7.noarch                                                                                       6/40 
		  Installing : 1:perl-Pod-Escapes-1.04-299.el7_9.noarch                                                                                 7/40 
		  Installing : perl-Encode-2.51-7.el7.x86_64                                                                                            8/40 
		  Installing : perl-Text-ParseWords-3.29-4.el7.noarch                                                                                   9/40 
		  Installing : perl-Pod-Usage-1.63-3.el7.noarch                                                                                        10/40 
		  Installing : 4:perl-macros-5.16.3-299.el7_9.x86_64                                                                                   11/40 
		  Installing : 4:perl-Time-HiRes-1.9725-3.el7.x86_64                                                                                   12/40 
		  Installing : perl-Exporter-5.68-3.el7.noarch                                                                                         13/40 
		  Installing : perl-constant-1.27-2.el7.noarch                                                                                         14/40 
		  Installing : perl-Socket-2.010-5.el7.x86_64                                                                                          15/40 
		  Installing : perl-Time-Local-1.2300-2.el7.noarch                                                                                     16/40 
		  Installing : perl-Carp-1.26-244.el7.noarch                                                                                           17/40 
		  Installing : perl-Storable-2.45-3.el7.x86_64                                                                                         18/40 
		  Installing : perl-PathTools-3.40-5.el7.x86_64                                                                                        19/40 
		  Installing : perl-Scalar-List-Utils-1.27-248.el7.x86_64                                                                              20/40 
		  Installing : 1:perl-Pod-Simple-3.28-4.el7.noarch                                                                                     21/40 
		  Installing : perl-File-Temp-0.23.01-3.el7.noarch                                                                                     22/40 
		  Installing : perl-File-Path-2.09-2.el7.noarch                                                                                        23/40 
		  Installing : perl-threads-shared-1.43-6.el7.x86_64                                                                                   24/40 
		  Installing : perl-threads-1.87-4.el7.x86_64                                                                                          25/40 
		  Installing : perl-Filter-1.49-3.el7.x86_64                                                                                           26/40 
		  Installing : 4:perl-libs-5.16.3-299.el7_9.x86_64                                                                                     27/40 
		  Installing : perl-Getopt-Long-2.40-3.el7.noarch                                                                                      28/40 
		  Installing : 4:perl-5.16.3-299.el7_9.x86_64                                                                                          29/40 
		  Installing : 1:perl-Error-0.17020-2.el7.noarch                                                                                       30/40 
		  Installing : perl-TermReadKey-2.30-20.el7.x86_64                                                                                     31/40 
		  Installing : less-458-9.el7.x86_64                                                                                                   32/40 
		  Installing : libedit-3.0-12.20121213cvs.el7.x86_64                                                                                   33/40 
		  Installing : openssh-clients-7.4p1-22.el7_9.x86_64                                                                                   34/40 
		  Installing : rsync-3.1.2-11.el7_9.x86_64                                                                                             35/40 
		  Installing : perl-Git-1.8.3.1-23.el7_8.noarch                                                                                        36/40 
		  Installing : git-1.8.3.1-23.el7_8.x86_64                                                                                             37/40 
		  Updating   : openssh-server-7.4p1-22.el7_9.x86_64                                                                                    38/40 
		  Cleanup    : openssh-server-7.4p1-21.el7.x86_64                                                                                      39/40 
		  Cleanup    : openssh-7.4p1-21.el7.x86_64                                                                                             40/40 
		  Verifying  : perl-HTTP-Tiny-0.033-3.el7.noarch                                                                                        1/40 
		  Verifying  : perl-threads-shared-1.43-6.el7.x86_64                                                                                    2/40 
		  Verifying  : 4:perl-Time-HiRes-1.9725-3.el7.x86_64                                                                                    3/40 
		  Verifying  : openssh-clients-7.4p1-22.el7_9.x86_64                                                                                    4/40 
		  Verifying  : perl-Exporter-5.68-3.el7.noarch                                                                                          5/40 
		  Verifying  : perl-constant-1.27-2.el7.noarch                                                                                          6/40 
		  Verifying  : perl-PathTools-3.40-5.el7.x86_64                                                                                         7/40 
		  Verifying  : openssh-7.4p1-22.el7_9.x86_64                                                                                            8/40 
		  Verifying  : 4:perl-macros-5.16.3-299.el7_9.x86_64                                                                                    9/40 
		  Verifying  : git-1.8.3.1-23.el7_8.x86_64                                                                                             10/40 
		  Verifying  : 1:perl-parent-0.225-244.el7.noarch                                                                                      11/40 
		  Verifying  : perl-Socket-2.010-5.el7.x86_64                                                                                          12/40 
		  Verifying  : rsync-3.1.2-11.el7_9.x86_64                                                                                             13/40 
		  Verifying  : perl-TermReadKey-2.30-20.el7.x86_64                                                                                     14/40 
		  Verifying  : groff-base-1.22.2-8.el7.x86_64                                                                                          15/40 
		  Verifying  : perl-File-Temp-0.23.01-3.el7.noarch                                                                                     16/40 
		  Verifying  : 1:perl-Pod-Simple-3.28-4.el7.noarch                                                                                     17/40 
		  Verifying  : perl-Time-Local-1.2300-2.el7.noarch                                                                                     18/40 
		  Verifying  : 1:perl-Pod-Escapes-1.04-299.el7_9.noarch                                                                                19/40 
		  Verifying  : perl-Git-1.8.3.1-23.el7_8.noarch                                                                                        20/40 
		  Verifying  : perl-Carp-1.26-244.el7.noarch                                                                                           21/40 
		  Verifying  : 1:perl-Error-0.17020-2.el7.noarch                                                                                       22/40 
		  Verifying  : perl-Storable-2.45-3.el7.x86_64                                                                                         23/40 
		  Verifying  : perl-Scalar-List-Utils-1.27-248.el7.x86_64                                                                              24/40 
		  Verifying  : perl-Pod-Usage-1.63-3.el7.noarch                                                                                        25/40 
		  Verifying  : perl-Encode-2.51-7.el7.x86_64                                                                                           26/40 
		  Verifying  : perl-Pod-Perldoc-3.20-4.el7.noarch                                                                                      27/40 
		  Verifying  : perl-podlators-2.5.1-3.el7.noarch                                                                                       28/40 
		  Verifying  : 4:perl-5.16.3-299.el7_9.x86_64                                                                                          29/40 
		  Verifying  : perl-File-Path-2.09-2.el7.noarch                                                                                        30/40 
		  Verifying  : libedit-3.0-12.20121213cvs.el7.x86_64                                                                                   31/40 
		  Verifying  : perl-threads-1.87-4.el7.x86_64                                                                                          32/40 
		  Verifying  : openssh-server-7.4p1-22.el7_9.x86_64                                                                                    33/40 
		  Verifying  : perl-Filter-1.49-3.el7.x86_64                                                                                           34/40 
		  Verifying  : perl-Getopt-Long-2.40-3.el7.noarch                                                                                      35/40 
		  Verifying  : perl-Text-ParseWords-3.29-4.el7.noarch                                                                                  36/40 
		  Verifying  : 4:perl-libs-5.16.3-299.el7_9.x86_64                                                                                     37/40 
		  Verifying  : less-458-9.el7.x86_64                                                                                                   38/40 
		  Verifying  : openssh-7.4p1-21.el7.x86_64                                                                                             39/40 
		  Verifying  : openssh-server-7.4p1-21.el7.x86_64                                                                                      40/40 

		Installed:
		  git.x86_64 0:1.8.3.1-23.el7_8                                                                                                              

		Dependency Installed:
		  groff-base.x86_64 0:1.22.2-8.el7             less.x86_64 0:458-9.el7                      libedit.x86_64 0:3.0-12.20121213cvs.el7         
		  openssh-clients.x86_64 0:7.4p1-22.el7_9      perl.x86_64 4:5.16.3-299.el7_9               perl-Carp.noarch 0:1.26-244.el7                 
		  perl-Encode.x86_64 0:2.51-7.el7              perl-Error.noarch 1:0.17020-2.el7            perl-Exporter.noarch 0:5.68-3.el7               
		  perl-File-Path.noarch 0:2.09-2.el7           perl-File-Temp.noarch 0:0.23.01-3.el7        perl-Filter.x86_64 0:1.49-3.el7                 
		  perl-Getopt-Long.noarch 0:2.40-3.el7         perl-Git.noarch 0:1.8.3.1-23.el7_8           perl-HTTP-Tiny.noarch 0:0.033-3.el7             
		  perl-PathTools.x86_64 0:3.40-5.el7           perl-Pod-Escapes.noarch 1:1.04-299.el7_9     perl-Pod-Perldoc.noarch 0:3.20-4.el7            
		  perl-Pod-Simple.noarch 1:3.28-4.el7          perl-Pod-Usage.noarch 0:1.63-3.el7           perl-Scalar-List-Utils.x86_64 0:1.27-248.el7    
		  perl-Socket.x86_64 0:2.010-5.el7             perl-Storable.x86_64 0:2.45-3.el7            perl-TermReadKey.x86_64 0:2.30-20.el7           
		  perl-Text-ParseWords.noarch 0:3.29-4.el7     perl-Time-HiRes.x86_64 4:1.9725-3.el7        perl-Time-Local.noarch 0:1.2300-2.el7           
		  perl-constant.noarch 0:1.27-2.el7            perl-libs.x86_64 4:5.16.3-299.el7_9          perl-macros.x86_64 4:5.16.3-299.el7_9           
		  perl-parent.noarch 1:0.225-244.el7           perl-podlators.noarch 0:2.5.1-3.el7          perl-threads.x86_64 0:1.87-4.el7                
		  perl-threads-shared.x86_64 0:1.43-6.el7      rsync.x86_64 0:3.1.2-11.el7_9               

		Dependency Updated:
		  openssh.x86_64 0:7.4p1-22.el7_9                                   openssh-server.x86_64 0:7.4p1-22.el7_9                                  

		Complete!

[root@ststor01 ~]# rpm -qa |grep git
		git-1.8.3.1-23.el7_8.x86_64

3. Go to mentioned folder and init a Bare git repo 

[root@ststor01 ~]# cd /opt

[root@ststor01 opt]# ls -ahl
		total 8.0K
		drwxr-xr-x 1 root root 4.0K Apr 11  2018 .
		drwxr-xr-x 1 root root 4.0K Nov  5 15:29 ..
 
[root@ststor01 opt]# git init --bare news.git
		Initialized empty Git repository in /opt/news.git/

[root@ststor01 opt]# ls -ahl
		total 12K
		drwxr-xr-x 1 root root 4.0K Nov  5 15:32 .
		drwxr-xr-x 1 root root 4.0K Nov  5 15:29 ..
		drwxr-xr-x 7 root root 4.0K Nov  5 15:32 news.git

[root@ststor01 opt]# cd news.git/

[root@ststor01 news.git]# git status
		fatal: This operation must be run in a work tree

[root@ststor01 news.git]# ls -ahl
		total 40K
		drwxr-xr-x 7 root root 4.0K Nov  5 15:32 .
		drwxr-xr-x 1 root root 4.0K Nov  5 15:32 ..
		drwxr-xr-x 2 root root 4.0K Nov  5 15:32 branches
		-rw-r--r-- 1 root root   66 Nov  5 15:32 config
		-rw-r--r-- 1 root root   73 Nov  5 15:32 description
		-rw-r--r-- 1 root root   23 Nov  5 15:32 HEAD
		drwxr-xr-x 2 root root 4.0K Nov  5 15:32 hooks
		drwxr-xr-x 2 root root 4.0K Nov  5 15:32 info
		drwxr-xr-x 4 root root 4.0K Nov  5 15:32 objects
		drwxr-xr-x 4 root root 4.0K Nov  5 15:32 refs

[root@ststor01 news.git]# 

--------------------------------------------------------------------------------------------------------------------------------
Task 101: 16/Nov/2022

Deploy Nginx and Phpfpm on Kubernetes

The Nautilus Application Development team is planning to deploy one of the php-based application on Kubernetes cluster. As per discussion with DevOps team they have decided to use nginx and phpfpm. Additionally, they shared some custom configuration requirements. Below you can find more details. Please complete the task as per requirements mentioned below:

1) Create a service to expose this app, the service type must be NodePort, nodePort should be 30012.

2.) Create a config map nginx-config for nginx.conf as we want to add some custom settings for nginx.conf.

a) Change default port 80 to 8091 in nginx.conf.

b) Change default document root /usr/share/nginx to /var/www/html in nginx.conf.

c) Update directory index to index index.html index.htm index.php in nginx.conf.

3.) Create a pod named nginx-phpfpm .

b) Create a shared volume shared-files that will be used by both containers (nginx and phpfpm) also it should be a emptyDir volume.

c) Map the ConfigMap we declared above as a volume for nginx container. Name the volume as nginx-config-volume, mount path should be /etc/nginx/nginx.conf and subPath should be nginx.conf

d) Nginx container should be named as nginx-container and it should use nginx:latest image. PhpFPM container should be named as php-fpm-container and it should use php:7.0-fpm image.

e) The shared volume shared-files should be mounted at /var/www/html location in both containers. Copy /opt/index.php from jump host to the nginx document root inside nginx container, once done you can access the app using App button on the top bar.

Before clicking on finish button always make sure to check if all pods are in running state.

You can use any labels as per your choice.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

1. Check kubectl utility and current configuration

thor@jump_host ~$ kubectl get all
		NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
		service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   5h9m

2. Create the config YAML file as per the requirements 

thor@jump_host ~$ vi /tmp/nginx_phpfpm.yml

thor@jump_host ~$ cat /tmp/nginx_phpfpm.yml 
		#ConfigMap Configuration
		---
		apiVersion: v1
		kind: ConfigMap
		metadata:
		  name: nginx-config
		data:
		  nginx.conf: |
		    events {} 
		    http {
		      server {
		        listen 8091;
		        index index.html index.htm index.php;
		        root  /var/www/html;
		        location ~ \.php$ {
		          include fastcgi_params;
		          fastcgi_param REQUEST_METHOD $request_method;
		          fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
		          fastcgi_pass 127.0.0.1:9000;
		        }
		      }
		    }


		#Pod Configuration    
		---
		apiVersion: v1
		kind: Pod
		metadata:
		  name: nginx-phpfpm
		  labels:
		     app: nginx-phpfpm
		     tier: back-end
		spec:
		  volumes:
		    - name: shared-files
		      emptyDir: {}
		    - name: nginx-config-volume
		      configMap:
		        name: nginx-config
		  containers:
		    - name: nginx-container
		      image: nginx:latest
		      volumeMounts:
		        - name: shared-files
		          mountPath: /var/www/html
		        - name: nginx-config-volume
		          mountPath: /etc/nginx/nginx.conf
		          subPath: nginx.conf
		    - name: php-fpm-container
		      image: php:7.3-fpm
		      volumeMounts:
		        - name: shared-files
		          mountPath: /var/www/html

		#Service configuration
		---
		apiVersion: v1
		kind: Service
		metadata:
		  name: nginx-phpfpm
		spec:
		  type: NodePort
		  selector:
		    app: nginx-phpfpm
		    tier: back-end
		  ports:
		    - port: 8091
		      targetPort: 8091
		      nodePort: 30012

3. Create the service and pods

thor@jump_host ~$ kubectl create -f /tmp/nginx_phpfpm.yml 
		configmap/nginx-config created
		pod/nginx-phpfpm created
		service/nginx-phpfpm created

4. Check the configuration applied and wait for pod to get running  

thor@jump_host ~$ kubectl get all
		NAME               READY   STATUS              RESTARTS   AGE
		pod/nginx-phpfpm   0/2     ContainerCreating   0          10s

		NAME                   TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)          AGE
		service/kubernetes     ClusterIP   10.96.0.1     <none>        443/TCP          5h11m
		service/nginx-phpfpm   NodePort    10.96.77.59   <none>        8091:30012/TCP   10s
 
thor@jump_host ~$ kubectl get all
		NAME               READY   STATUS    RESTARTS   AGE
		pod/nginx-phpfpm   2/2     Running   0          60s

		NAME                   TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)          AGE
		service/kubernetes     ClusterIP   10.96.0.1     <none>        443/TCP          5h12m
		service/nginx-phpfpm   NodePort    10.96.77.59   <none>        8091:30012/TCP   60s

5. Copy the mentioned file in the given pod

thor@jump_host ~$ kubectl cp /opt/index.php nginx-phpfpm:/var/www/html --container=nginx-container

6. Validate by clicking on the App Button top right side side of the terminal. If the app is running then the task is done.

--------------------------------------------------------------------------------------------------------------------------------
Task 102: 18/Nov/2022

Run a Docker Container


Nautilus DevOps team is testing some applications deployment on some of the application servers. They need to deploy a nginx container on Application Server 3. Please complete the task as per details given below:

    On Application Server 3 create a container named nginx_3 using image nginx with alpine tag and make sure container is in running state.

1.Login to app server 3 and switch to root user 

thor@jump_host ~$ ssh banner@stapp03
		The authenticity of host 'stapp03 (172.16.238.12)' can't be established.
		ECDSA key fingerprint is SHA256:Zf7WuEVcvpXAR1ITq9DoENoxEcCaCyV0Y4JyuAJXgFc.
		ECDSA key fingerprint is MD5:48:49:21:90:b7:aa:00:ed:df:0f:a2:fb:b0:5d:92:8f.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'stapp03,172.16.238.12' (ECDSA) to the list of known hosts.
		banner@stapp03's password: 

[banner@stapp03 ~]$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for banner: 

2. Check existing running docker images and containers

[root@stapp03 ~]# docker ps
		CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

[root@stapp03 ~]# docker images
		REPOSITORY   TAG       IMAGE ID   CREATED   SIZE

3. Run the docker container as per requirement using the given image and name  

[root@stapp03 ~]# docker run -d --name nginx_3 -p 8080:80 nginx:alpine
		Unable to find image 'nginx:alpine' locally
		alpine: Pulling from library/nginx
		ca7dd9ec2225: Pull complete 
		76a48b0f5898: Pull complete 
		2f12a0e7c01d: Pull complete 
		1a7b9b9bbef6: Pull complete 
		b704883c57af: Pull complete 
		4342b1ab302e: Pull complete 
		Digest: sha256:455c39afebd4d98ef26dd70284aa86e6810b0485af5f4f222b19b89758cabf1e
		Status: Downloaded newer image for nginx:alpine
		d022a38a3abd331cea55f3b89f1da16373a3b58d34f9f6d08d554c37545554a5

4. Check the docker running status

[root@stapp03 ~]# docker ps
		CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                  NAMES
		d022a38a3abd   nginx:alpine   "/docker-entrypoint.…"   8 seconds ago   Up 5 seconds   0.0.0.0:8080->80/tcp   nginx_3

[root@stapp03 ~]# docker images
		REPOSITORY   TAG       IMAGE ID       CREATED      SIZE
		nginx        alpine    19dd4d73108a   6 days ago   23.5MB

5. Validate by clicking on the view port top right side side of the terminal and select port 80. If the container is running then we see nginx page and the task is done.

--------------------------------------------------------------------------------------------------------------------------------
Task 103: 20/Nov/2022

Git Create Branches

Nautilus developers are actively working on one of the project repositories, /usr/src/kodekloudrepos/apps. They recently decided to implement some new features in the application, and they want to maintain those new changes in a separate branch. Below are the requirements that have been shared with the DevOps team:

    On Storage server in Stratos DC create a new branch xfusioncorp_apps from master branch in /usr/src/kodekloudrepos/apps git repo.

    Please do not try to make any changes in code.


1. Login to Storage server and switch to root user 

thor@jump_host ~$ ssh natasha@ststor01
		The authenticity of host 'ststor01 (172.16.238.15)' can't be established.
		ECDSA key fingerprint is SHA256:bNwXFd+JrQ3QZwtPJNbAxE+nIRPwx+rAC8iwQL2UNUs.
		ECDSA key fingerprint is MD5:a1:23:7e:da:b3:40:86:fc:22:47:b7:f1:15:7d:ab:b3.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'ststor01,172.16.238.15' (ECDSA) to the list of known hosts.
		natasha@ststor01's password: 

[natasha@ststor01 ~]$ sudo su - 

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for natasha: 

2. Go to mentioned repository
[root@ststor01 ~]# cd /usr/src/kodekloudrepos/apps/

3. List current branches

[root@ststor01 apps]# git branch -a
		* kodekloud_apps
		  master
		  remotes/origin/master

4. Switch to master branch as we are required to create new branch from master 

[root@ststor01 apps]# git checkout master
		Switched to branch 'master'

[root@ststor01 apps]# git branch -a
		  kodekloud_apps
		* master
		  remotes/origin/master

[root@ststor01 apps]# git status
		# On branch master
		nothing to commit, working directory clean

5. Create the mentioned branch and checkout to it .

[root@ststor01 apps]# git branch xfusioncorp_apps
 
[root@ststor01 apps]# git checkout xfusioncorp_apps
		Switched to branch 'xfusioncorp_apps'

6. Validate the task 

[root@ststor01 apps]# git branch -a
		  kodekloud_apps
		  master
		* xfusioncorp_apps
		  remotes/origin/master

[root@ststor01 apps]# git status
		# On branch xfusioncorp_apps
		nothing to commit, working directory clean

--------------------------------------------------------------------------------------------------------------------------------
Task 104: 28/Nov/2022

Kubernetes Troubleshooting

One of the Nautilus DevOps team members was working on to update an existing Kubernetes template. Somehow, he made some mistakes in the template and it is failing while applying. We need to fix this as soon as possible, so take a look into it and make sure you are able to apply it without any issues. Also, do not remove any component from the template like pods/deployments/volumes etc.

/home/thor/mysql_deployment.yml is the template that needs to be fixed.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.


thor@jump_host ~$ pwd 
		/home/thor
thor@jump_host ~$ ls -ahl
		total 36K
		drwxr----- 1 thor thor 4.0K Nov 28 02:58 .
		drwxr-xr-x 1 root root 4.0K Oct 31  2020 ..
		-rwxrwx--- 1 thor thor   18 Oct 30  2018 .bash_logout
		-rwxrwx--- 1 thor thor  193 Oct 30  2018 .bash_profile
		-rwxrwx--- 1 thor thor  510 Nov 28 02:58 .bashrc
		drwxrwx--- 1 thor thor 4.0K Oct 31  2020 .config
		drwxrwx--- 2 thor thor 4.0K Nov 28 02:58 .kube
		-rw-r--r-- 1 thor thor 2.3K Nov 28 02:58 mysql_deployment.yml
		drwx------ 2 thor thor 4.0K Nov 28 02:58 .ssh

thor@jump_host ~$ kubectl get all
		NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
		service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   6h6m
 
thor@jump_host ~$ kubectl get secrets
		NAME                  TYPE                                  DATA   AGE
		default-token-z8mrm   kubernetes.io/service-account-token   3      6h6m
		mysql-db-url          Opaque                                1      37s
		mysql-root-pass       Opaque                                1      38s
		mysql-user-pass       Opaque                                2      37s
 
thor@jump_host ~$ cat mysql_deployment.yml 
		apiVersion: apps/v1 
		kind: PersistentVolume            
		metadata:
		  name: mysql-pv
		  labels: 
		  type: local 
		spec:
		  storageClassName: standard      
		  capacity:
		    storage: 250Mi
		  accessModes: 
		    - ReadWriteOnce
		  hostPath:                       
		  path: "/mnt/data" 
		  persistentVolumeReclaimPolicy:  
		  -  Retain  
		---    
		apiVersion: apps/v1 
		kind: PersistentVolumeClaim 
		metadata:                          
		  name: mysql-pv-claim
		  labels:
		  app: mysql-app 
		spec:                              
		  storageClassName: standard       
		  accessModes:
		    - ReadWriteOnce                
		  resources:
		    requests: 
		      storage: 250MB 
		---
		apiVersion: v1                    
		kind: Service                      
		metadata:
		  name: mysql         
		  labels:             
		    app: mysql-app
		spec:
		  type: NodePort
		  ports:
		    - targetPort: 3306
		      port: 3306
		      nodePort: 30011
		  selector:    
		    app: mysql-app
		  tier: mysql
		---
		apiVersion: v1 
		kind: Deployment            
		metadata:
		  name: mysql-deployment       
		  labels:                       
		    app: mysql-app 
		spec:
		  selector:
		    matchLabels:
		      app: mysql-app
		    tier: mysql 
		  strategy:
		    type: Recreate
		  template:                    
		    metadata:
		      labels:                  
		        app: mysql-app
		      tier: mysql 
		    spec:                       
		      containers: 
		      - images: mysql:5.6 
		        name: mysql
		        env:                        
		        - name: MYSQL_ROOT_PASSWORD 
		          valueFrom:                
		          secretKeyRef: 
		            name: mysql-root-pass 
		              key: password 
		        - name: MYSQL_DATABASE
		          valueFrom:
		          secretKeyRef: 
		            name: mysql-db-url 
		              key: database 
		        - name: MYSQL_USER
		          valueFrom:
		            secretKeyRef:
		              name: mysql-user-pass
		              key: username
		        - name: MYSQL_PASSWORD
		          valueFrom:
		            secretKeyRef:
		              name: mysql-user-pass
		              key: password
		        ports:
		        - containerPort: 3306        
		          name: mysql
		        volumeMounts:
		        - name: mysql-persistent-storage 
		          mountPath: /var/lib/mysql
		      volumes:                       
		      - name: mysql-persistent-storage
		          persistentVolumeClaim:
		          claimName: mysql-pv-claim

thor@jump_host ~$ kubectl create -f mysql_deployment.yml 
		unable to recognize "mysql_deployment.yml": no matches for kind "PersistentVolume" in version "apps/v1"
		unable to recognize "mysql_deployment.yml": no matches for kind "PersistentVolumeClaim" in version "apps/v1"
		error validating "mysql_deployment.yml": error validating data: ValidationError(Service.spec): unknown field "tier" in io.k8s.api.core.v1.ServiceSpec; if you choose to ignore these errors, turn validation off with --validate=false


thor@jump_host ~$ vi mysql_pv.yml
thor@jump_host ~$ cat mysql_pv.yml
		---
		apiVersion: apps/v1 
		kind: PersistentVolume            
		metadata:
		  name: mysql-pv
		  labels: 
		  type: local 
		spec:
		  storageClassName: standard      
		  capacity:
		    storage: 250Mi
		  accessModes: 
		    - ReadWriteOnce
		  hostPath:                       
		  path: "/mnt/data" 
		  persistentVolumeReclaimPolicy:  
		  -  Retain  

thor@jump_host ~$ kubectl create -f mysql_pv.yml 
		error: unable to recognize "mysql_pv.yml": no matches for kind "PersistentVolume" in version "apps/v1"

thor@jump_host ~$ vi mysql_pv.yml
thor@jump_host ~$ cat mysql_pv.yml
		---
		apiVersion: v1 
		kind: PersistentVolume            
		metadata:
		  name: mysql-pv
		  labels: 
		  type: local 
		spec:
		  storageClassName: standard      
		  capacity:
		    storage: 250Mi
		  accessModes: 
		  - ReadWriteOnce
		  hostPath:                       
		    path: "/mnt/data" 
		  persistentVolumeReclaimPolicy: Retain  

thor@jump_host ~$ kubectl create -f mysql_pv.yml 
		error: error validating "mysql_pv.yml": error validating data: ValidationError(PersistentVolume.metadata): unknown field "type" in io.k8s.apimachinery.pkg.apis.meta.v1.ObjectMeta; if you choose to ignore these errors, turn validation off with --validate=false

thor@jump_host ~$ vi mysql_pv.yml
thor@jump_host ~$ cat mysql_pv.yml
		---
		apiVersion: v1 																																										<---------------
		kind: PersistentVolume            
		metadata:
		  name: mysql-pv
		  labels: 
		    type: local 																																									<---------------
		spec:
		  storageClassName: standard      
		  capacity:
		    storage: 250Mi
		  accessModes: 
		  - ReadWriteOnce																																									<---------------
		  hostPath:                       
		    path: "/mnt/data" 
		  persistentVolumeReclaimPolicy: Retain 																													<--------------- 

thor@jump_host ~$ kubectl create -f mysql_pv.yml 
		persistentvolume/mysql-pv created
 
thor@jump_host ~$ vi mysql_pvc.yml
thor@jump_host ~$ cat mysql_pvc.yml
		---    
		apiVersion: apps/v1 
		kind: PersistentVolumeClaim 
		metadata:                          
		  name: mysql-pv-claim
		  labels:
		  app: mysql-app 
		spec:                              
		  storageClassName: standard       
		  accessModes:
		    - ReadWriteOnce                
		  resources:
		    requests: 
		      storage: 250MB 

thor@jump_host ~$ kubectl create -f  mysql_pvc.yml
		error: unable to recognize "mysql_pvc.yml": no matches for kind "PersistentVolumeClaim" in version "apps/v1"

thor@jump_host ~$ vi mysql_pvc.yml
thor@jump_host ~$ cat mysql_pvc.yml
		---    
		apiVersion: v1 																																									<---------------
		kind: PersistentVolumeClaim 
		metadata:                          
		  name: mysql-pv-claim
		  labels:
		    app: mysql-app 																																							<---------------
		spec:                              
		  storageClassName: standard       
		  accessModes:
		  - ReadWriteOnce 																																							<---------------               
		  resources:
		    requests: 
		      storage: 250Mi 																																						<---------------

thor@jump_host ~$ kubectl create -f  mysql_pvc.yml
		persistentvolumeclaim/mysql-pv-claim created
 
thor@jump_host ~$ vi mysql_service.yml
thor@jump_host ~$ cat mysql_service.yml
		---
		apiVersion: v1                    
		kind: Service                      
		metadata:
		  name: mysql         
		  labels:             
		    app: mysql-app
		spec:
		  type: NodePort
		  ports:
		    - targetPort: 3306
		      port: 3306
		      nodePort: 30011
		  selector:    
		    app: mysql-app
		  tier: mysql

thor@jump_host ~$ kubectl create -f mysql_service.yml
		error: error validating "mysql_service.yml": error validating data: ValidationError(Service.spec): unknown field "tier" in io.k8s.api.core.v1.ServiceSpec; if you choose to ignore these errors, turn validation off with --validate=false

thor@jump_host ~$ vi mysql_service.yml
thor@jump_host ~$ cat mysql_service.yml
		---
		apiVersion: v1                    
		kind: Service                      
		metadata:
		  name: mysql         
		  labels:             
		    app: mysql-app
		spec:
		  type: NodePort
		  ports:
		    - targetPort: 3306
		      port: 3306
		      nodePort: 30011
		  selector:    
		    app: mysql-app
		    tier: mysql 																																							<---------------																						
thor@jump_host ~$ kubectl create -f mysql_service.yml
		service/mysql created

thor@jump_host ~$ vi mysql_deploy.yml
thor@jump_host ~$ cat mysql_deploy.yml
		---
		apiVersion: v1 
		kind: Deployment            
		metadata:
		  name: mysql-deployment       
		  labels:                       
		    app: mysql-app 
		spec:
		  selector:
		    matchLabels:
		      app: mysql-app
		    tier: mysql 
		  strategy:
		    type: Recreate
		  template:                    
		    metadata:
		      labels:                  
		        app: mysql-app
		      tier: mysql 
		    spec:                       
		      containers: 
		      - images: mysql:5.6 
		        name: mysql
		        env:                        
		        - name: MYSQL_ROOT_PASSWORD 
		          valueFrom:                
		          secretKeyRef: 
		            name: mysql-root-pass 
		              key: password 
		        - name: MYSQL_DATABASE
		          valueFrom:
		          secretKeyRef: 
		            name: mysql-db-url 
		              key: database 
		        - name: MYSQL_USER
		          valueFrom:
		            secretKeyRef:
		              name: mysql-user-pass
		              key: username
		        - name: MYSQL_PASSWORD
		          valueFrom:
		            secretKeyRef:
		              name: mysql-user-pass
		              key: password
		        ports:
		        - containerPort: 3306        
		          name: mysql
		        volumeMounts:
		        - name: mysql-persistent-storage 
		          mountPath: /var/lib/mysql
		      volumes:                       
		      - name: mysql-persistent-storage
		          persistentVolumeClaim:
		          claimName: mysql-pv-claim

thor@jump_host ~$ kubectl create -f mysql_deploy.yml
		error: error parsing mysql_deploy.yml: error converting YAML to JSON: yaml: line 29: mapping values are not allowed in this context

thor@jump_host ~$ vi mysql_deploy.yml
thor@jump_host ~$ cat mysql_deploy.yml
		---
		apiVersion: v1 
		kind: Deployment            
		metadata:
		  name: mysql-deployment       
		  labels:                       
		    app: mysql-app 
		spec:
		  selector:
		    matchLabels:
		      app: mysql-app
		      tier: mysql 
		  strategy:
		    type: Recreate
		  template:                    
		    metadata:
		      labels:                  
		        app: mysql-app
		        tier: mysql 
		    spec:                       
		      containers: 
		      - images: mysql:5.6 
		        name: mysql
		        env:                        
		        - name: MYSQL_ROOT_PASSWORD 
		          valueFrom:                
		            secretKeyRef: 
		              name: mysql-root-pass 
		              key: password 
		        - name: MYSQL_DATABASE
		          valueFrom:
		            secretKeyRef: 
		              name: mysql-db-url 
		              key: database 
		        - name: MYSQL_USER
		          valueFrom:
		            secretKeyRef:
		              name: mysql-user-pass
		              key: username
		        - name: MYSQL_PASSWORD
		          valueFrom:
		            secretKeyRef:
		              name: mysql-user-pass
		              key: password
		        ports:
		        - containerPort: 3306        
		          name: mysql
		        volumeMounts:
		        - name: mysql-persistent-storage 
		          mountPath: /var/lib/mysql
		      volumes:                       
		      - name: mysql-persistent-storage
		        persistentVolumeClaim:
		          claimName: mysql-pv-claim

thor@jump_host ~$ kubectl create -f mysql_deploy.yml
		error: unable to recognize "mysql_deploy.yml": no matches for kind "Deployment" in version "v1"

thor@jump_host ~$ vi mysql_deploy.yml

thor@jump_host ~$ kubectl create -f mysql_deploy.yml
		error: error validating "mysql_deploy.yml": error validating data: ValidationError(Deployment.spec.template.spec.containers[0]): unknown field "images" in io.k8s.api.core.v1.Container; if you choose to ignore these errors, turn validation off with --validate=false

thor@jump_host ~$ vi mysql_deploy.yml
thor@jump_host ~$ cat mysql_deploy.yml
		---
		apiVersion: apps/v1 																																	<---------------				
		kind: Deployment            
		metadata:
		  name: mysql-deployment       
		  labels:                       
		    app: mysql-app 
		spec:
		  selector:
		    matchLabels:
		      app: mysql-app
		      tier: mysql 																																		<---------------					
		  strategy:
		    type: Recreate
		  template:                    
		    metadata:
		      labels:                  
		        app: mysql-app
		        tier: mysql 
		    spec:                       
		      containers: 
		      - image: mysql:5.6 																															<---------------		
		        name: mysql
		        env:                        
		        - name: MYSQL_ROOT_PASSWORD 
		          valueFrom:                
		            secretKeyRef: 																														<---------------
		              name: mysql-root-pass 																									<---------------		
		              key: password 
		        - name: MYSQL_DATABASE
		          valueFrom:
		            secretKeyRef: 																														<---------------				
		              name: mysql-db-url 																											<---------------
		              key: database 
		        - name: MYSQL_USER
		          valueFrom:
		            secretKeyRef:
		              name: mysql-user-pass
		              key: username
		        - name: MYSQL_PASSWORD
		          valueFrom:
		            secretKeyRef:
		              name: mysql-user-pass
		              key: password
		        ports:
		        - containerPort: 3306        
		          name: mysql
		        volumeMounts:
		        - name: mysql-persistent-storage 
		          mountPath: /var/lib/mysql
		      volumes:                       
		      - name: mysql-persistent-storage
		        persistentVolumeClaim:																												<---------------			
		          claimName: mysql-pv-claim

thor@jump_host ~$ kubectl create -f mysql_deploy.yml
		deployment.apps/mysql-deployment created

 
thor@jump_host ~$ kubectl get all
		NAME                                    READY   STATUS              RESTARTS   AGE
		pod/mysql-deployment-74f5dd5cdf-gq5nd   0/1     ContainerCreating   0          13s

		NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
		service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP          6h23m
		service/mysql        NodePort    10.96.153.164   <none>        3306:30011/TCP   10m

		NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
		deployment.apps/mysql-deployment   0/1     1            0           13s

		NAME                                          DESIRED   CURRENT   READY   AGE
		replicaset.apps/mysql-deployment-74f5dd5cdf   1         1         0       13s

thor@jump_host ~$ kubectl get pv
		NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                    STORAGECLASS   REASON   AGE
		mysql-pv   250Mi      RWO            Retain           Bound    default/mysql-pv-claim   standard                13m

thor@jump_host ~$ kubectl get pvc
		NAME             STATUS   VOLUME     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
		mysql-pv-claim   Bound    mysql-pv   250Mi      RWO            standard       12m
 
thor@jump_host ~$ kubectl get all
		NAME                                    READY   STATUS    RESTARTS   AGE
		pod/mysql-deployment-74f5dd5cdf-gq5nd   1/1     Running   0          36s

		NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
		service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP          6h24m
		service/mysql        NodePort    10.96.153.164   <none>        3306:30011/TCP   10m

		NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
		deployment.apps/mysql-deployment   1/1     1            1           36s

		NAME                                          DESIRED   CURRENT   READY   AGE
		replicaset.apps/mysql-deployment-74f5dd5cdf   1         1         1       36s
 
thor@jump_host ~$ kubectl delete service mysql
		service "mysql" deleted

thor@jump_host ~$ kubectl get all
		NAME                                    READY   STATUS    RESTARTS   AGE
		pod/mysql-deployment-74f5dd5cdf-gq5nd   1/1     Running   0          69s

		NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
		service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   6h24m

		NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
		deployment.apps/mysql-deployment   1/1     1            1           69s

		NAME                                          DESIRED   CURRENT   READY   AGE
		replicaset.apps/mysql-deployment-74f5dd5cdf   1         1         1       69s

thor@jump_host ~$ kubectl delete deployment mysql-deployment
		deployment.apps "mysql-deployment" deleted

thor@jump_host ~$ kubectl get all
		NAME                                    READY   STATUS        RESTARTS   AGE
		pod/mysql-deployment-74f5dd5cdf-gq5nd   0/1     Terminating   0          106s

		NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
		service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   6h25m

thor@jump_host ~$ kubectl delete pods mysql-deployment-74f5dd5cdf-gq5nd
		Error from server (NotFound): pods "mysql-deployment-74f5dd5cdf-gq5nd" not found
thor@jump_host ~$ kubectl get all
		NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
		service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   6h26m
 
thor@jump_host ~$ kubectl delete pvc mysql-pv-claim
		persistentvolumeclaim "mysql-pv-claim" deleted

thor@jump_host ~$ kubectl delete pv mysql-pv
		persistentvolume "mysql-pv" deleted
 
thor@jump_host ~$ kubectl get pv 
		No resources found
thor@jump_host ~$ kubectl get pvc
		No resources found in default namespace.

thor@jump_host ~$ cp mysql_deployment.yml mysql_deployment_bkp.yml
thor@jump_host ~$ vi mysql_deployment.yml 
thor@jump_host ~$ cat mysql_pv.yml mysql_pvc.yml mysql_service.yml mysql_deploy.yml >> mysql_deployment.yml 
thor@jump_host ~$ cat mysql_deployment.yml 
---
apiVersion: v1 
kind: PersistentVolume            
metadata:
  name: mysql-pv
  labels: 
    type: local 
spec:
  storageClassName: standard      
  capacity:
    storage: 250Mi
  accessModes: 
  - ReadWriteOnce
  hostPath:                       
    path: "/mnt/data" 
  persistentVolumeReclaimPolicy: Retain  
---    
apiVersion: v1 
kind: PersistentVolumeClaim 
metadata:                          
  name: mysql-pv-claim
  labels:
    app: mysql-app 
spec:                              
  storageClassName: standard       
  accessModes:
  - ReadWriteOnce                
  resources:
    requests: 
      storage: 250Mi 
---
apiVersion: v1                    
kind: Service                      
metadata:
  name: mysql         
  labels:             
    app: mysql-app
spec:
  type: NodePort
  ports:
    - targetPort: 3306
      port: 3306
      nodePort: 30011
  selector:    
    app: mysql-app
    tier: mysql
---
apiVersion: apps/v1 
kind: Deployment            
metadata:
  name: mysql-deployment       
  labels:                       
    app: mysql-app 
spec:
  selector:
    matchLabels:
      app: mysql-app
      tier: mysql 
  strategy:
    type: Recreate
  template:                    
    metadata:
      labels:                  
        app: mysql-app
        tier: mysql 
    spec:                       
      containers: 
      - image: mysql:5.6 
        name: mysql
        env:                        
        - name: MYSQL_ROOT_PASSWORD 
          valueFrom:                
            secretKeyRef: 
              name: mysql-root-pass 
              key: password 
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef: 
              name: mysql-db-url 
              key: database 
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: mysql-user-pass
              key: username
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-user-pass
              key: password
        ports:
        - containerPort: 3306        
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage 
          mountPath: /var/lib/mysql
      volumes:                       
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim

thor@jump_host ~$ ls -ahl
total 56K
		drwxr----- 1 thor thor 4.0K Nov 28 03:21 .
		drwxr-xr-x 1 root root 4.0K Oct 31  2020 ..
		-rwxrwx--- 1 thor thor   18 Oct 30  2018 .bash_logout
		-rwxrwx--- 1 thor thor  193 Oct 30  2018 .bash_profile
		-rwxrwx--- 1 thor thor  510 Nov 28 02:58 .bashrc
		drwxrwx--- 1 thor thor 4.0K Oct 31  2020 .config
		drwxrwx--- 3 thor thor 4.0K Nov 28 02:59 .kube
		-rw-r--r-- 1 thor thor 2.3K Nov 28 03:20 mysql_deployment_bkp.yml
		-rw-r--r-- 1 thor thor 2.3K Nov 28 03:21 mysql_deployment.yml
		-rw-rw-r-- 1 thor thor 1.4K Nov 28 03:16 mysql_deploy.yml
		-rw-rw-r-- 1 thor thor  313 Nov 28 03:04 mysql_pvc.yml
		-rw-rw-r-- 1 thor thor  316 Nov 28 03:02 mysql_pv.yml
		-rw-rw-r-- 1 thor thor  295 Nov 28 03:06 mysql_service.yml
		drwx------ 2 thor thor 4.0K Nov 28 02:58 .ssh

thor@jump_host ~$ kubectl create -f mysql_deployment.yml 
		persistentvolume/mysql-pv created
		persistentvolumeclaim/mysql-pv-claim created
		service/mysql created
		deployment.apps/mysql-deployment created
 
thor@jump_host ~$ kubectl get all
		NAME                                    READY   STATUS    RESTARTS   AGE
		pod/mysql-deployment-74f5dd5cdf-cqwt6   1/1     Running   0          7s

		NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
		service/kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP          6h29m
		service/mysql        NodePort    10.96.176.41   <none>        3306:30011/TCP   7s

		NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
		deployment.apps/mysql-deployment   1/1     1            1           7s

		NAME                                          DESIRED   CURRENT   READY   AGE
		replicaset.apps/mysql-deployment-74f5dd5cdf   1         1         1       7s

thor@jump_host ~$ kubectl get pv
		NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                    STORAGECLASS   REASON   AGE
		mysql-pv   250Mi      RWO            Retain           Bound    default/mysql-pv-claim   standard                16s

thor@jump_host ~$ kubectl get pvc
		NAME             STATUS   VOLUME     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
		mysql-pv-claim   Bound    mysql-pv   250Mi      RWO            standard       19s
thor@jump_host ~$ 

--------------------------------------------------------------------------------------------------------------------------------
Task 105: 29/Nov/2022

Save, Load and Transfer Docker Image

One of the DevOps team members was working on to create a new custom docker image on App Server 1 in Stratos DC. He is done with his changes and image is saved on same server with name demo:nautilus. Recently a requirement has been raised by a team to use that image for testing, but the team wants to test the same on App Server 3. So we need to provide them that image on App Server 3 in Stratos DC.

a. On App Server 1 save the image demo:nautilus in an archive.

b. Transfer the image archive to App Server 3.

c. Load that image archive on App Server 3 with same name and tag which was used on App Server 1.

Note: Docker is already installed on both servers; however, if its service is down please make sure to start it.

1. Login to App Server and switch to root

thor@jump_host ~$ ssh tony@stapp01
		The authenticity of host 'stapp01 (172.16.238.10)' can't be established.
		ECDSA key fingerprint is SHA256:7/VTPQ2CABk+3zUB7pJzVmT/d4XiNBJuNZ0bl5GCobQ.
		ECDSA key fingerprint is MD5:df:68:3b:89:1a:b0:fd:1f:79:ec:d4:b7:89:e3:e9:13.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'stapp01,172.16.238.10' (ECDSA) to the list of known hosts.
		tony@stapp01's password: 

[tony@stapp01 ~]$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for tony: 

2. List the existing images in docker which need to archive

[root@stapp01 ~]# docker images
		REPOSITORY   TAG        IMAGE ID       CREATED              SIZE
		demo         nautilus   a0358bd5cf49   About a minute ago   118MB
		ubuntu       latest     a8780b506fa4   3 weeks ago          77.8MB

3. Save the image in a TAR archive

[root@stapp01 ~]# docker save -o /tmp/demo.tar demo:nautilus

[root@stapp01 ~]# ls -ahl /tmp
		total 115M
		drwxrwxrwt 1 root root 4.0K Nov 29 04:40 .
		drwxr-xr-x 1 root root 4.0K Nov 29 04:36 ..
		-rw------- 1 root root 115M Nov 29 04:40 demo.tar
		-rwxr-xr-x 1 root root  239 Nov 29 04:37 docker_container.sh
		drwxrwxrwt 1 root root 4.0K Aug  1  2019 .font-unix
		drwxrwxrwt 1 root root 4.0K Aug  1  2019 .ICE-unix
		-rwx------ 1 root root  836 Aug  1  2019 ks-script-rnBCJB
		drwxrwxrwt 1 root root 4.0K Aug  1  2019 .Test-unix
		drwxrwxrwt 1 root root 4.0K Aug  1  2019 .X11-unix
		drwxrwxrwt 1 root root 4.0K Aug  1  2019 .XIM-unix
		-rw------- 1 root root    0 Aug  1  2019 yum.log

4. Copy(SCP) the archive file to mentioned server

[root@stapp01 ~]# scp /tmp/demo.tar banner@stapp03:/tmp
		The authenticity of host 'stapp03 (172.16.238.12)' can't be established.
		ECDSA key fingerprint is SHA256:RveOEHTizfGCH1Dwm9qbs1/OPlyAGQuSYS3JGfpEwL0.
		ECDSA key fingerprint is MD5:13:5d:0c:a2:b4:72:84:a9:15:41:8e:35:a6:f3:7f:22.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'stapp03,172.16.238.12' (ECDSA) to the list of known hosts.
		banner@stapp03's password: 
		demo.tar                                                                                                   100%  115MB  45.8MB/s   00:02    

5. Login to mentioned App server and switch to root user

[root@stapp01 ~]# ssh banner@stapp03
		banner@stapp03's password: 
 
[banner@stapp03 ~]$ sudo su  -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for banner: 

6. Verify the copied archive file

[root@stapp03 ~]# ls -ahl /tmp/
		total 115M
		drwxrwxrwt 1 root   root   4.0K Nov 29 04:41 .
		drwxr-xr-x 1 root   root   4.0K Nov 29 04:36 ..
		-rw------- 1 banner banner 115M Nov 29 04:41 demo.tar
		drwxrwxrwt 1 root   root   4.0K Aug  1  2019 .font-unix
		drwxrwxrwt 1 root   root   4.0K Aug  1  2019 .ICE-unix
		-rwx------ 1 root   root    836 Aug  1  2019 ks-script-rnBCJB
		drwxrwxrwt 1 root   root   4.0K Aug  1  2019 .Test-unix
		drwxrwxrwt 1 root   root   4.0K Aug  1  2019 .X11-unix
		drwxrwxrwt 1 root   root   4.0K Aug  1  2019 .XIM-unix
		-rw------- 1 root   root      0 Aug  1  2019 yum.log

7. Check current docker images
[root@stapp03 ~]# docker images
		Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?

8. Start docker daemon if required 

[root@stapp03 ~]# systemctl start docker

[root@stapp03 ~]# systemctl status docker
		● docker.service - Docker Application Container Engine
		   Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; vendor preset: disabled)
		   Active: active (running) since Tue 2022-11-29 04:42:59 UTC; 7s ago
		     Docs: https://docs.docker.com
		 Main PID: 583 (dockerd)
		    Tasks: 23
		   Memory: 49.2M
		   CGroup: /docker/dcc4ae04d8c1008c63da5958d22da0d318642b6438e34301f6634af45e159a3c/system.slice/docker.service
		           └─583 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock

		Nov 29 04:42:57 stapp03.stratos.xfusioncorp.com dockerd[583]: time="2022-11-29T04:42:57.326955764Z" level=warning msg="Your kernel do...imit"
		Nov 29 04:42:57 stapp03.stratos.xfusioncorp.com dockerd[583]: time="2022-11-29T04:42:57.327031003Z" level=warning msg="Your kernel do...uler"
		Nov 29 04:42:57 stapp03.stratos.xfusioncorp.com dockerd[583]: time="2022-11-29T04:42:57.327039173Z" level=warning msg="Your kernel do...ight"
		Nov 29 04:42:57 stapp03.stratos.xfusioncorp.com dockerd[583]: time="2022-11-29T04:42:57.327046428Z" level=warning msg="Your kernel do...vice"
		Nov 29 04:42:57 stapp03.stratos.xfusioncorp.com dockerd[583]: time="2022-11-29T04:42:57.330263079Z" level=info msg="Loading container...art."
		Nov 29 04:42:58 stapp03.stratos.xfusioncorp.com dockerd[583]: time="2022-11-29T04:42:58.293270350Z" level=info msg="Loading container...one."
		Nov 29 04:42:58 stapp03.stratos.xfusioncorp.com dockerd[583]: time="2022-11-29T04:42:58.986622869Z" level=info msg="Docker daemon" co....10.7
		Nov 29 04:42:58 stapp03.stratos.xfusioncorp.com dockerd[583]: time="2022-11-29T04:42:58.986895666Z" level=info msg="Daemon has comple...tion"
		Nov 29 04:42:59 stapp03.stratos.xfusioncorp.com systemd[1]: Started Docker Application Container Engine.
		Nov 29 04:42:59 stapp03.stratos.xfusioncorp.com dockerd[583]: time="2022-11-29T04:42:59.681252769Z" level=info msg="API listen on /va...sock"
		Hint: Some lines were ellipsized, use -l to show in full.
 
[root@stapp03 ~]# docker images
		REPOSITORY   TAG       IMAGE ID   CREATED   SIZE

9. Load the image archive 

[root@stapp03 ~]# docker load -i /tmp/demo.tar 
		f4a670ac65b6: Loading layer [==================================================>]  80.31MB/80.31MB
		f1e0d34b5503: Loading layer [==================================================>]   39.8MB/39.8MB
		Loaded image: demo:nautilus

10. Validate the task by checking docker images

[root@stapp03 ~]# docker images
		REPOSITORY   TAG        IMAGE ID       CREATED         SIZE
		demo         nautilus   a0358bd5cf49   4 minutes ago   118MB

--------------------------------------------------------------------------------------------------------------------------------
Task 106: 30/Nov/2022

Create Deployments in Kubernetes Cluster

The Nautilus DevOps team has started practicing some pods, and services deployment on Kubernetes platform as they are planning to migrate most of their applications on Kubernetes platform. Recently one of the team members has been assigned a task to create a deploymnt as per details mentioned below:

Create a deployment named httpd to deploy the application httpd using the image httpd: latest (remember to mention the tag as well)

1. Check current kubectl utility and configuration

thor@jump_host ~$ kubectl get all
		NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
		service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   71m

thor@jump_host ~$ kubectl get namespace
		NAME                 STATUS   AGE
		default              Active   71m
		kube-node-lease      Active   71m
		kube-public          Active   71m
		kube-system          Active   71m
		local-path-storage   Active   71m

thor@jump_host ~$ kubectl get deploy
		No resources found in default namespace.

2. Create the deploy as per given rrequirements

thor@jump_host ~$ kubectl create deploy httpd --image httpd:latest
		deployment.apps/httpd created

3. Validate the task by checking running pod and its description 

thor@jump_host ~$ kubectl get deploy
		NAME    READY   UP-TO-DATE   AVAILABLE   AGE
		httpd   0/1     1            0           4s

thor@jump_host ~$ kubectl get pods
		NAME                    READY   STATUS              RESTARTS   AGE
		httpd-84898796c-cxk8c   0/1     ContainerCreating   0          11s
 
thor@jump_host ~$ kubectl get pods
		NAME                    READY   STATUS    RESTARTS   AGE
		httpd-84898796c-cxk8c   1/1     Running   0          15s

thor@jump_host ~$ kubectl describe pod httpd-84898796c-cxk8c
		Name:         httpd-84898796c-cxk8c
		Namespace:    default
		Priority:     0
		Node:         kodekloud-control-plane/172.17.0.2
		Start Time:   Wed, 30 Nov 2022 14:49:07 +0000
		Labels:       app=httpd
		              pod-template-hash=84898796c
		Annotations:  <none>
		Status:       Running
		IP:           10.244.0.5
		IPs:
		  IP:           10.244.0.5
		Controlled By:  ReplicaSet/httpd-84898796c
		Containers:
		  httpd:
		    Container ID:   containerd://bd2e7069563795ec06f923fb9ade47569a6b88fc12e65a671c6df382c1c4770f
		    Image:          httpd:latest 																														<-------------------
		    Image ID:       docker.io/library/httpd@sha256:f2e89def4c032b02c83e162c1819ccfcbd4ea6bdbc5ff784bbc68cba940a9046
		    Port:           <none>
		    Host Port:      <none>
		    State:          Running
		      Started:      Wed, 30 Nov 2022 14:49:21 +0000
		    Ready:          True
		    Restart Count:  0
		    Environment:    <none>
		    Mounts:
		      /var/run/secrets/kubernetes.io/serviceaccount from default-token-btvbx (ro)
		Conditions:
		  Type              Status
		  Initialized       True 
		  Ready             True 
		  ContainersReady   True 
		  PodScheduled      True 
		Volumes:
		  default-token-btvbx:
		    Type:        Secret (a volume populated by a Secret)
		    SecretName:  default-token-btvbx
		    Optional:    false
		QoS Class:       BestEffort
		Node-Selectors:  <none>
		Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
		                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
		Events:
		  Type    Reason     Age   From               Message
		  ----    ------     ----  ----               -------
		  Normal  Scheduled  42s   default-scheduler  Successfully assigned default/httpd-84898796c-cxk8c to kodekloud-control-plane
		  Normal  Pulling    41s   kubelet            Pulling image "httpd:latest"
		  Normal  Pulled     28s   kubelet            Successfully pulled image "httpd:latest" in 12.615345253s
		  Normal  Created    28s   kubelet            Created container httpd
		  Normal  Started    28s   kubelet            Started container httpd
thor@jump_host ~$

--------------------------------------------------------------------------------------------------------------------------------
Task 107: 02/Dec/2022

Manage Git Repositories

A new developer just joined the Nautilus development team and has been assigned a new project for which he needs to create a new repository under his account on Gitea server. Additionally, there is some existing data that need to be added to the repo. Below you can find more details about the task:

Click on the Gitea UI button on the top bar. You should be able to access the Gitea UI. Login to Gitea server using username max and password Max_pass123.

a. Create a new git repository story_ecommerce under max user.

b. SSH into storage server using user max and password Max_pass123 and clone this newly created repository under user max home directory i.e /home/max.

c. Copy all files from location /usr/sysops to the repository and commit/push your changes to the master branch. The commit message must be "add stories" (must be done in single commit).

d. Create a new branch max_apps from master.

e. Copy a file story-index-max.txt from location /tmp/stories/ to the repository. This file has a typo, which you can fix by changing the word Mooose to Mouse. Commit and push the changes to the newly created branch. Commit message must be "typo fixed for Mooose" (must be done in single commit).

1. Login to Gitea UI using given credentials .

2. Create a new repository 

3. SSH to storage server 

thor@jump_host ~$ ssh max@ststor01
		The authenticity of host 'ststor01 (172.16.238.15)' can't be established.
		ECDSA key fingerprint is SHA256:0z85j/k+4Nf8WKbHJzxo1AOv4FeRA8LPET2N3BEkYyo.
		ECDSA key fingerprint is MD5:74:e6:4d:c4:b3:80:07:be:03:30:0a:bf:1e:eb:e6:82.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'ststor01,172.16.238.15' (ECDSA) to the list of known hosts.
		max@ststor01's password: 
		Welcome to xFusionCorp Storage server.

4. clone the newly created repository

max $ git clone http://git.stratos.xfusioncorp.com/max/story_ecommerce.git
		Cloning into 'story_ecommerce'...
		warning: You appear to have cloned an empty repository.
		Checking connectivity... done.

max $ ls -ahl
		total 32
		drwxr-sr-x    1 max      max         4.0K Dec  2 12:07 .
		drwxr-xr-x    1 root     root        4.0K Oct 26  2020 ..
		-rw-r--r--    1 max      max          202 Oct 26  2020 .bash_profile
		-rw-r--r--    1 max      max          202 Oct 26  2020 .bashrc
		-rw-r--r--    1 max      max           50 Oct 26  2020 .vimrc
		drwxr-sr-x    3 max      max         4.0K Dec  2 12:07 story_ecommerce

 
max $ cd story_ecommerce/

max $ ls -ahl
		total 16
		drwxr-sr-x    3 max      max         4.0K Dec  2 12:07 .
		drwxr-sr-x    1 max      max         4.0K Dec  2 12:07 ..
		drwxr-sr-x    7 max      max         4.0K Dec  2 12:07 .git
5. Copy the file 

max $ cp /usr/sysops/*.* ./

max $ ls -ahl
		total 24
		drwxr-sr-x    3 max      max         4.0K Dec  2 12:08 .
		drwxr-sr-x    1 max      max         4.0K Dec  2 12:07 ..
		drwxr-sr-x    7 max      max         4.0K Dec  2 12:07 .git
		-rw-r--r--    1 max      max          792 Dec  2 12:08 frogs-and-ox.txt
		-rw-r--r--    1 max      max         1.1K Dec  2 12:08 lion-and-mouse.txt

6. Add the files to git , commit the changes and then push the master branch

max $ git add .

max $ git commit "add stories"^C

max $ git status
		On branch master

		Initial commit

		Changes to be committed:
		  (use "git rm --cached <file>..." to unstage)

		        new file:   frogs-and-ox.txt
		        new file:   lion-and-mouse.txt

max $ git commit -m "add stories"
		[master (root-commit) 66c4fcd] add stories
		 Committer: Linux User <max@ststor01.stratos.xfusioncorp.com>
		Your name and email address were configured automatically based
		on your username and hostname. Please check that they are accurate.
		You can suppress this message by setting them explicitly. Run the
		following command and follow the instructions in your editor to edit
		your configuration file:

		    git config --global --edit

		After doing this, you may fix the identity used for this commit with:

		    git commit --amend --reset-author

		 2 files changed, 42 insertions(+)
		 create mode 100644 frogs-and-ox.txt
		 create mode 100644 lion-and-mouse.txt

max (master)$ git status
		On branch master
		Your branch is based on 'origin/master', but the upstream is gone.
		  (use "git branch --unset-upstream" to fixup)
		nothing to commit, working directory clean

max (master)$ git push origin master
		Username for 'http://git.stratos.xfusioncorp.com': max
		Password for 'http://max@git.stratos.xfusioncorp.com': 
		Counting objects: 4, done.
		Delta compression using up to 36 threads.
		Compressing objects: 100% (4/4), done.
		Writing objects: 100% (4/4), 1.19 KiB | 0 bytes/s, done.
		Total 4 (delta 0), reused 0 (delta 0)
		remote: . Processing 1 references
		remote: Processed 1 references in total
		To http://git.stratos.xfusioncorp.com/max/story_ecommerce.git
		 * [new branch]      master -> master

7. Create the new branch and checkout to newly created branch 

max (master)$ git branch max_apps
 
max (master)$ git branch -a
		* master
		  max_apps
		  remotes/origin/master

max (master)$ git checkout max_apps
		Switched to branch 'max_apps'
		max (max_apps)$ git branch -a
		  master
		* max_apps
		  remotes/origin/master

max (max_apps)$ ls -ahl
		total 24
		drwxr-sr-x    3 max      max         4.0K Dec  2 12:08 .
		drwxr-sr-x    1 max      max         4.0K Dec  2 12:07 ..
		drwxr-sr-x    8 max      max         4.0K Dec  2 12:10 .git
		-rw-r--r--    1 max      max          792 Dec  2 12:08 frogs-and-ox.txt
		-rw-r--r--    1 max      max         1.1K Dec  2 12:08 lion-and-mouse.txt

8. copy the new file make the changes 

max (max_apps)$ cp /tmp/stories/story-index-max.txt ./

max (max_apps)$ ls -ahl
		total 28
		drwxr-sr-x    3 max      max         4.0K Dec  2 12:11 .
		drwxr-sr-x    1 max      max         4.0K Dec  2 12:07 ..
		drwxr-sr-x    8 max      max         4.0K Dec  2 12:10 .git
		-rw-r--r--    1 max      max          792 Dec  2 12:08 frogs-and-ox.txt
		-rw-r--r--    1 max      max         1.1K Dec  2 12:08 lion-and-mouse.txt
		-rw-r--r--    1 max      max          102 Dec  2 12:11 story-index-max.txt

max (max_apps)$ vi story-index-max.txt 

max (max_apps)$ cat story-index-max.txt 
		1. The Lion and the Mouse
		2. The Frogs and the Ox
		3. The Fox and the Grapes
		4. The Donkey and the Dog


max (max_apps)$ git status
		On branch max_apps
		Untracked files:
		  (use "git add <file>..." to include in what will be committed)

		        story-index-max.txt

		nothing added to commit but untracked files present (use "git add" to track)

9. Add the files to git , commit the changes and then push the max_apps branch

max (max_apps)$ git add .

max (max_apps)$ git  commit -m "typo fixed for Mooose"
		[max_apps 54d2209] typo fixed for Mooose
		 Committer: Linux User <max@ststor01.stratos.xfusioncorp.com>
		Your name and email address were configured automatically based
		on your username and hostname. Please check that they are accurate.
		You can suppress this message by setting them explicitly. Run the
		following command and follow the instructions in your editor to edit
		your configuration file:

		    git config --global --edit

		After doing this, you may fix the identity used for this commit with:

		    git commit --amend --reset-author

		 1 file changed, 4 insertions(+)
		 create mode 100644 story-index-max.txt

max (max_apps)$ git status
		On branch max_apps
		nothing to commit, working directory clean

max (max_apps)$ git push origin max_apps
		Username for 'http://git.stratos.xfusioncorp.com': max
		Password for 'http://max@git.stratos.xfusioncorp.com': 
		Counting objects: 3, done.
		Delta compression using up to 36 threads.
		Compressing objects: 100% (3/3), done.
		Writing objects: 100% (3/3), 412 bytes | 0 bytes/s, done.
		Total 3 (delta 0), reused 0 (delta 0)
		remote: 
		remote: Create a new pull request for 'max_apps':
		remote:   http://git.stratos.xfusioncorp.com/max/story_ecommerce/compare/master...max_apps
		remote: 
		remote: . Processing 1 references
		remote: Processed 1 references in total
		To http://git.stratos.xfusioncorp.com/max/story_ecommerce.git
		 * [new branch]      max_apps -> max_apps

max (max_apps)$ git status
		On branch max_apps
		nothing to commit, working directory clean

--------------------------------------------------------------------------------------------------------------------------------
Task 108: 03/Dec/2022

Puppet Create a File

The Puppet master and Puppet agent nodes have been set up by the Nautilus DevOps team so they can perform testing. In Stratos DC all app servers have been configured as Puppet agent nodes. Below are details about the testing scenario they want to proceed with.

Use Puppet file resource and perform the below given task:

    Create a Puppet programming file apps.pp under /etc/puppetlabs/code/environments/production/manifests directory on master node i.e Jump Server.

    Using /etc/puppetlabs/code/environments/production/manifests/apps.pp create a file news.txt under /opt/sysops directory on App Server 1.



1. Switch to root user on jump server / puppet master and got to mentione folder location

thor@jump_host ~$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for thor: 
 
root@jump_host ~# cd /etc/puppetlabs/code/environments/production/manifests/

root@jump_host /etc/puppetlabs/code/environments/production/manifests# ls -ahl
		total 8.0K
		drwxr-xr-x 1 puppet puppet 4.0K Jul 13  2021 .
		drwxr-xr-x 1 puppet puppet 4.0K Aug  9  2021 ..

2. Create puppet programming file as per given requirement

root@jump_host /etc/puppetlabs/code/environments/production/manifests# vi apps.pp

root@jump_host /etc/puppetlabs/code/environments/production/manifests# cat apps.pp
		class file_creator {

		  # Now create news.txt under /opt/sysops

		  file { '/opt/sysops/news.txt':

		    ensure => 'present',

		  }

		}

		node 'stapp01.stratos.xfusioncorp.com' {

		  include file_creator

		}

root@jump_host /etc/puppetlabs/code/environments/production/manifests# ls -ahl
		total 12K
		drwxr-xr-x 1 puppet puppet 4.0K Dec  3 16:13 .
		drwxr-xr-x 1 puppet puppet 4.0K Aug  9  2021 ..
		-rw-r--r-- 1 root   root    201 Dec  3 16:13 apps.pp

3. Validate the puppet file

root@jump_host /etc/puppetlabs/code/environments/production/manifests# puppet parser validate apps.pp 

4. Login to App server 1 and swithc to root user 

root@jump_host /etc/puppetlabs/code/environments/production/manifests# ssh tony@stapp01
		The authenticity of host 'stapp01 (172.16.238.10)' can't be established.
		ECDSA key fingerprint is SHA256:NLXvXSL0mV+6PF2+l8fyhaVp40+uZEWYpT6hSzNh8fw.
		ECDSA key fingerprint is MD5:04:0d:f9:32:97:24:7d:cf:63:ce:a3:c4:0b:a9:0c:40.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'stapp01,172.16.238.10' (ECDSA) to the list of known hosts.
		tony@stapp01's password: 
 
[tony@stapp01 ~]$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for tony:

5. Run puppet agent to pull the configuration from puppet server

[root@stapp01 ~]# puppet agent -tv
		Info: Using configured environment 'production'
		Info: Retrieving pluginfacts
		Info: Retrieving plugin
		Info: Retrieving locales
		Info: Caching catalog for stapp01.stratos.xfusioncorp.com
		Info: Applying configuration version '1670084110'
		Notice: /Stage[main]/File_creator/File[/opt/sysops/news.txt]/ensure: created
		Notice: Applied catalog in 0.01 seconds

6. Validate the task by checking file created 

[root@stapp01 ~]# cd /opt/sysops/

[root@stapp01 sysops]# ls -ahl
		total 8.0K
		drwxr-xr-x 2 root root 4.0K Dec  3 16:15 .
		drwxr-xr-x 1 root root 4.0K Dec  3 16:10 ..
		-rw-r--r-- 1 root root    0 Dec  3 16:15 news.txt

--------------------------------------------------------------------------------------------------------------------------------
Task 109: 05/Dec/2022

Ansible Blockinfile Module

The Nautilus DevOps team wants to install and set up a simple httpd web server on all app servers in Stratos DC. Additionally, they want to deploy a sample web page for now using Ansible only. Therefore, write the required playbook to complete this task. Find more details about the task below.

We already have an inventory file under /home/thor/ansible directory on jump host. Create a playbook.yml under /home/thor/ansible directory on jump host itself.

    Using the playbook, install httpd web server on all app servers. Additionally, make sure its service should up and running.

    Using blockinfile Ansible module add some content in /var/www/html/index.html file. Below is the content:

    Welcome to XfusionCorp!

    This is Nautilus sample file, created using Ansible!

    Please do not modify this file manually!

    The /var/www/html/index.html file's user and group owner should be apache on all app servers.

    The /var/www/html/index.html file's permissions should be 0644 on all app servers.

Note:

i. Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure the playbook works this way without passing any extra arguments.

ii. Do not use any custom or empty marker for blockinfile module.


1. Go to mentioned folder and check inventory file.

thor@jump_host ~$ cd /home/thor/ansible/

thor@jump_host ~/ansible$ ls -ahl
		total 16K
		drwxr-xr-x 2 thor thor 4.0K Dec  5 16:26 .
		drwxr----- 1 thor thor 4.0K Dec  5 16:26 ..
		-rw-r--r-- 1 thor thor   36 Dec  5 16:26 ansible.cfg
		-rw-r--r-- 1 thor thor  237 Dec  5 16:26 inventory

thor@jump_host ~/ansible$ cat inventory 
		stapp01 ansible_host=172.16.238.10 ansible_ssh_pass=Ir0nM@n ansible_user=tony
		stapp02 ansible_host=172.16.238.11 ansible_ssh_pass=Am3ric@ ansible_user=steve
		stapp03 ansible_host=172.16.238.12 ansible_ssh_pass=BigGr33n ansible_user=banner

thor@jump_host ~/ansible$ cat  ansible.cfg 
		[defaults]
		host_key_checking = False

2. Create playbook as per given requirements		

thor@jump_host ~/ansible$ vi playbook.yml
thor@jump_host ~/ansible$ cat playbook.yml 
		---
		- name: Install httpd and setup index.html
		  hosts: stapp01, stapp02, stapp03
		  become: yes

		  tasks:
		    - name: Install httpd
		      package:
		        name: httpd
		        state: present
		    - name: Start service httpd, if not started
		      service:
		        name: httpd
		        state: started
		    - name: Add content block in index.html and set permissions
		      blockinfile:
		        path: /var/www/html/index.html
		        create: yes
		        block: 
		          Welcome to XfusionCorp!
		          This is Nautilus sample file, created using Ansible!
		          Please do not modify this file manually!
		        owner: apache
		        group: apache
		        mode: "0644"

3. Execute the playbook

thor@jump_host ~/ansible$ ansible-playbook -i inventory playbook.yml 

		PLAY [Install httpd and setup index.html] ***************************************************************************************************

		TASK [Gathering Facts] **********************************************************************************************************************
		ok: [stapp02]
		ok: [stapp03]
		ok: [stapp01]

		TASK [Install httpd] ************************************************************************************************************************
		changed: [stapp02]
		changed: [stapp01]
		changed: [stapp03]

		TASK [Start service httpd, if not started] **************************************************************************************************
		changed: [stapp02]
		changed: [stapp03]
		changed: [stapp01]

		TASK [Add content block in index.html and set permissions] **********************************************************************************
		changed: [stapp03]
		changed: [stapp01]
		changed: [stapp02]

		PLAY RECAP **********************************************************************************************************************************
		stapp01                    : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
		stapp02                    : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
		stapp03                    : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

4. Validate the task using inventory file and url

thor@jump_host ~/ansible$ ansible -i inventory all -a 'ls -ahl /var/www/html/'
		stapp02 | CHANGED | rc=0 >>
		total 12K
		drwxr-xr-x 2 root   root   4.0K Dec  5 16:31 .
		drwxr-xr-x 4 root   root   4.0K Dec  5 16:31 ..
		-rw-r--r-- 1 apache apache  176 Dec  5 16:31 index.html
		stapp03 | CHANGED | rc=0 >>
		total 12K
		drwxr-xr-x 2 root   root   4.0K Dec  5 16:31 .
		drwxr-xr-x 4 root   root   4.0K Dec  5 16:31 ..
		-rw-r--r-- 1 apache apache  176 Dec  5 16:31 index.html
		stapp01 | CHANGED | rc=0 >>
		total 12K
		drwxr-xr-x 2 root   root   4.0K Dec  5 16:31 .
		drwxr-xr-x 4 root   root   4.0K Dec  5 16:31 ..
		-rw-r--r-- 1 apache apache  176 Dec  5 16:31 index.html

thor@jump_host ~/ansible$ curl http://stapp01
		# BEGIN ANSIBLE MANAGED BLOCK
		Welcome to XfusionCorp! This is Nautilus sample file, created using Ansible! Please do not modify this file manually!
		# END ANSIBLE MANAGED BLOCK

thor@jump_host ~/ansible$ curl http://stapp02
		# BEGIN ANSIBLE MANAGED BLOCK
		Welcome to XfusionCorp! This is Nautilus sample file, created using Ansible! Please do not modify this file manually!
		# END ANSIBLE MANAGED BLOCK

thor@jump_host ~/ansible$ curl http://stapp03
		# BEGIN ANSIBLE MANAGED BLOCK
		Welcome to XfusionCorp! This is Nautilus sample file, created using Ansible! Please do not modify this file manually!
		# END ANSIBLE MANAGED BLOCK
thor@jump_host ~/ansible$

--------------------------------------------------------------------------------------------------------------------------------
Task 110: 07/Dec/2022

Docker Volumes Mapping

The Nautilus DevOps team is testing applications containerization, which issupposed to be migrated on docker container-based environments soon. In today's stand-up meeting one of the team members has been assigned a task to create and test a docker container with certain requirements. Below are more details:

a. On App Server 2 in Stratos DC pull nginx image (preferably latest tag but others should work too).

b. Create a new container with name demo from the image you just pulled.

c. Map the host volume /opt/dba with container volume /tmp. There is an sample.txt file present on same server under /tmp; copy that file to /opt/dba. Also please keep the container in running state.

1. Login to App Server and switch to root user

thor@jump_host ~$ ssh steve@stapp02
		The authenticity of host 'stapp02 (172.16.238.11)' can't be established.
		ECDSA key fingerprint is SHA256:7rKUo4xCK3UkRzI5NbGbEjCT2Czto4RqXcEi60iyGBs.
		ECDSA key fingerprint is MD5:72:4b:e2:d6:29:d3:c6:e2:4b:23:04:ac:4f:5d:84:90.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'stapp02,172.16.238.11' (ECDSA) to the list of known hosts.
		steve@stapp02's password: 

[steve@stapp02 ~]$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for steve: 

2. check existing docker images

[root@stapp02 ~]# docker images 
		REPOSITORY   TAG       IMAGE ID   CREATED   SIZE

3. Pull the mentioned image from server 

[root@stapp02 ~]# docker pull nginx:latest
		latest: Pulling from library/nginx
		025c56f98b67: Pull complete 
		ca9c7f45d396: Pull complete 
		ed6bd111fc08: Pull complete 
		e25b13a5f70d: Pull complete 
		9bbabac55ab6: Pull complete 
		e5c9ba265ded: Pull complete 
		Digest: sha256:ab589a3c466e347b1c0573be23356676df90cd7ce2dbf6ec332a5f0a8b5e59db
		Status: Downloaded newer image for nginx:latest
		docker.io/library/nginx:latest
 
[root@stapp02 ~]# docker images 
		REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
		nginx        latest    ac8efec875ce   30 hours ago   142MB

4. Copy mentioned file to given folder  

[root@stapp02 ~]# cp /tmp/sample.txt /opt/dba

[root@stapp02 ~]# ls -ahl /opt/dba/
		total 12K
		drwxr-xr-x 2 root root 4.0K Dec  7 11:05 .
		drwxr-xr-x 1 root root 4.0K Dec  7 11:02 ..
		-rw-r--r-- 1 root root   23 Dec  7 11:05 sample.txt

5. Run the docker image with mentioned volume mount

[root@stapp02 ~]# docker run --name demo  -v /opt/dba:/tmp -d -it  nginx:latest
		695a584a84cecf83f4b50ad70d803ef41b8ef91a6f35dffa79e85d04cffc5c0e

[root@stapp02 ~]# docker ps
		CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS     NAMES
		695a584a84ce   nginx:latest   "/docker-entrypoint.…"   16 seconds ago   Up 13 seconds   80/tcp    demo

6. Verify the mount by login to container and ls on /tmp

[root@stapp02 ~]# docker exec -it 695a584a84ce /bin/bash

root@695a584a84ce:/# ls -ahl /tmp/
		total 12K
		drwxr-xr-x  2 root root 4.0K Dec  7 11:05 .
		drwxr-xr-x 22 root root 4.0K Dec  7 11:06 ..
		-rw-r--r--  1 root root   23 Dec  7 11:05 sample.txt

root@695a584a84ce:/# 

--------------------------------------------------------------------------------------------------------------------------------
Task 111: 08/Dec/2022

Ansible Replace Module

There is some data on all app servers in Stratos DC. The Nautilus development team shared some requirement with the DevOps team to alter some of the data as per recent changes they made. The DevOps team is working to prepare an Ansible playbook to accomplish the same. Below you can find more details about the task.

Write a playbook.yml under /home/thor/ansible on jump host, an inventory is already present under /home/thor/ansible directory on Jump host itself. Perform below given tasks using this playbook:

    We have a file /opt/devops/blog.txt on app server 1. Using Ansible replace module replace string xFusionCorp to Nautilus in that file.

    We have a file /opt/devops/story.txt on app server 2. Using Ansiblereplace module replace the string Nautilus to KodeKloud in that file.

    We have a file /opt/devops/media.txt on app server 3. Using Ansible replace module replace string KodeKloud to xFusionCorp Industries in that file.

Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure the playbook works this way without passing any extra arguments.

1. Go to mentioned folder and check ansible inventory file 

thor@jump_host ~$ cd /home/thor/ansible/

thor@jump_host ~/ansible$ ls -ahl
		total 16K
		drwxr-xr-x 2 thor thor 4.0K Dec  8 14:18 .
		drwxr----- 1 thor thor 4.0K Dec  8 14:18 ..
		-rw-r--r-- 1 thor thor   36 Dec  8 14:18 ansible.cfg
		-rw-r--r-- 1 thor thor  237 Dec  8 14:18 inventory

thor@jump_host ~/ansible$ cat inventory 
		stapp01 ansible_host=172.16.238.10 ansible_ssh_pass=Ir0nM@n ansible_user=tony
		stapp02 ansible_host=172.16.238.11 ansible_ssh_pass=Am3ric@ ansible_user=steve
		stapp03 ansible_host=172.16.238.12 ansible_ssh_pass=BigGr33n ansible_user=banner

thor@jump_host ~/ansible$ cat ansible.cfg 
		[defaults]
		host_key_checking = False

2.Create playbook.yml as per given requirements 

thor@jump_host ~/ansible$ vi playbook.yml
 
thor@jump_host ~/ansible$ cat playbook.yml 
		- name: Ansible replace
		  hosts: stapp01,stapp02,stapp03
		  become: yes
		  tasks:
		    - name: blog.txt replacement
		      replace:
		        path: /opt/devops/blog.txt
		        regexp: "xFusionCorp"
		        replace: "Nautilus"
		      when: inventory_hostname == "stapp01"
		    - name: story.txt replacement
		      replace:
		        path: /opt/devops/story.txt
		        regexp: "Nautilus"
		        replace: "KodeKloud"
		      when: inventory_hostname == "stapp02"
		    - name: media.txt replacement
		      replace:
		        path: /opt/devops/media.txt
		        regexp: "KodeKloud"
		        replace: "xFusionCorp Industries"
		      when: inventory_hostname == "stapp03"

3. Run playbook to make the changes  

thor@jump_host ~/ansible$ ansible-playbook -i inventory playbook.yml 

		PLAY [Ansible replace] **********************************************************************************************************************

		TASK [Gathering Facts] **********************************************************************************************************************
		ok: [stapp01]
		ok: [stapp02]
		ok: [stapp03]

		TASK [blog.txt replacement] *****************************************************************************************************************
		skipping: [stapp03]
		skipping: [stapp02]
		changed: [stapp01]

		TASK [story.txt replacement] ****************************************************************************************************************
		skipping: [stapp01]
		skipping: [stapp03]
		changed: [stapp02]

		TASK [media.txt replacement] ****************************************************************************************************************
		skipping: [stapp01]
		skipping: [stapp02]
		changed: [stapp03]

		PLAY RECAP **********************************************************************************************************************************
		stapp01                    : ok=2    changed=1    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   
		stapp02                    : ok=2    changed=1    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   
		stapp03                    : ok=2    changed=1    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   

4. Validate the task by checking the files on each server 

thor@jump_host ~/ansible$ ansible -i inventory stapp01 -a 'cat /opt/devops/blog.txt'
		stapp01 | CHANGED | rc=0 >>
		Welcome to Nautilus Industries !

thor@jump_host ~/ansible$ ansible -i inventory stapp02 -a 'cat /opt/devops/story.txt'
		stapp02 | CHANGED | rc=0 >>
		Welcome to KodeKloud Group !

thor@jump_host ~/ansible$ ansible -i inventory stapp03 -a 'cat /opt/devops/media.txt'
		stapp03 | CHANGED | rc=0 >>
		Welcome to xFusionCorp Industries !

--------------------------------------------------------------------------------------------------------------------------------
Task 112: 10/Dec/2022

Pull Docker Image

Nautilus project developers are planning to start testing on a new project. As per their meeting with the DevOps team, they want to test containerized environment application features. As per details shared with DevOps team, we need to accomplish the following task:

a. Pull busybox:musl image on App Server 1 in Stratos DC and re-tag (create new tag) this image as busybox:news


1. Login to App Server and switch to root user 

thor@jump_host ~$ ssh tony@stapp01
		The authenticity of host 'stapp01 (172.16.238.10)' can't be established.
		ECDSA key fingerprint is SHA256:JJuCHauappFu+vIIL627WKXRst9vHaF4I+p/4JibJ8E.
		ECDSA key fingerprint is MD5:ca:3c:ea:4e:7c:90:2f:27:c0:40:66:0e:7c:ff:a0:b7.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'stapp01,172.16.238.10' (ECDSA) to the list of known hosts.
		tony@stapp01's password: 
 
[tony@stapp01 ~]$ sudo su - 

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for tony: 

2. Check current docker images on server

[root@stapp01 ~]# docker images
		REPOSITORY   TAG       IMAGE ID   CREATED   SIZE

3. Pull the mentioned docker image

[root@stapp01 ~]# docker pull busybox:musl
		musl: Pulling from library/busybox
		4c50f639dbf8: Pull complete 
		Digest: sha256:7d1702cfd71b9eb2ceea92bcadc293cd1ac17e321542ef9551d58f77e8b798e4
		Status: Downloaded newer image for busybox:musl
		docker.io/library/busybox:musl
 
[root@stapp01 ~]# docker images
		REPOSITORY   TAG       IMAGE ID       CREATED      SIZE
		busybox      musl      24d4c1fb43e8   3 days ago   1.41MB

4. Create new tag/ re-tag the pulled docker image and verify

[root@stapp01 ~]# docker image tag busybox:musl busybox:news
 
[root@stapp01 ~]# docker images
		REPOSITORY   TAG       IMAGE ID       CREATED      SIZE
		busybox      musl      24d4c1fb43e8   3 days ago   1.41MB
		busybox      news      24d4c1fb43e8   3 days ago   1.41MB

--------------------------------------------------------------------------------------------------------------------------------
Task 113: 11/Dec/2022

Managing ACLs Using Ansible

There are some files that need to be created on all app servers in Stratos DC. The Nautilus DevOps team want these files to be owned by user root only however, they also want that the app specific user to have a set of permissions on these files. All tasks must be done using Ansible only, so they need to create a playbook. Below you can find more information about the task.

Create a playbook.yml under /home/thor/ansible on jump host, an inventory file is already present under /home/thor/ansible directory on Jump Server itself.

    Create an empty file blog.txt under /opt/sysops/ directory on app server 1. Set some acl properties for this file. Using acl provide read '(r)' permissions to group tony (i.e entity is tony and etype is group).

    Create an empty file story.txt under /opt/sysops/ directory on app server 2. Set some acl properties for this file. Using acl provide read + write '(rw)' permissions to user steve (i.e entity is steve and etype is user).

    Create an empty file media.txt under /opt/sysops/ on app server 3. Set some acl properties for this file. Using acl provide read + write '(rw)' permissions to group banner (i.e entity is banner and etype is group).

Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure the playbook works this way, without passing any extra arguments.

1. Go to mentioned folder and check ansible inventory file 

thor@jump_host ~$ cd /home/thor/ansible/

thor@jump_host ~/ansible$ ls -ahl
		total 16K
		drwxr-xr-x 2 thor thor 4.0K Dec 11 05:22 .
		drwxr----- 1 thor thor 4.0K Dec 11 05:22 ..
		-rw-r--r-- 1 thor thor   36 Dec 11 05:22 ansible.cfg
		-rw-r--r-- 1 thor thor  237 Dec 11 05:22 inventory

thor@jump_host ~/ansible$ cat inventory 
		stapp01 ansible_host=172.16.238.10 ansible_ssh_pass=Ir0nM@n ansible_user=tony
		stapp02 ansible_host=172.16.238.11 ansible_ssh_pass=Am3ric@ ansible_user=steve
		stapp03 ansible_host=172.16.238.12 ansible_ssh_pass=BigGr33n ansible_user=banner

thor@jump_host ~/ansible$ cat ansible.cfg 
		[defaults]
		host_key_checking = False

thor@jump_host ~/ansible$ ansible -i inventory all -a "ls -ahl /opt/sysops/"
		stapp02 | CHANGED | rc=0 >>
		total 8.0K
		drwxr-xr-x 2 root root 4.0K Dec 11 05:22 .
		drwxr-xr-x 1 root root 4.0K Dec 11 05:22 ..
		stapp03 | CHANGED | rc=0 >>
		total 8.0K
		drwxr-xr-x 2 root root 4.0K Dec 11 05:22 .
		drwxr-xr-x 1 root root 4.0K Dec 11 05:22 ..
		stapp01 | CHANGED | rc=0 >>
		total 8.0K
		drwxr-xr-x 2 root root 4.0K Dec 11 05:22 .
		drwxr-xr-x 1 root root 4.0K Dec 11 05:22 ..

2.Create playbook.yml as per given requirements

thor@jump_host ~/ansible$ vi playbook.yml

thor@jump_host ~/ansible$ cat playbook.yml 
		- name: Create file and set ACL in Host 1
		  hosts: stapp01
		  become: yes
		  tasks:
		    - name: Create the blog.txt on stapp01
		      file:
		        path: /opt/sysops/blog.txt
		        state: touch
		    - name: Set ACL for blog.txt
		      acl:
		        path: /opt/sysops/blog.txt
		        entity: tony
		        etype: group
		        permissions: r
		        state: present
		- name: Create file and set ACL in Host 2
		  hosts: stapp02
		  become: yes
		  tasks:
		    - name: Create the story.txt on stapp02
		      file:
		        path: /opt/sysops/story.txt
		        state: touch
		    - name: Set ACL for story.txt
		      acl:
		        path: /opt/sysops/story.txt
		        entity: steve
		        etype: user
		        permissions: rw
		        state: present
		- name: Create file and set ACL in Host 3
		  hosts: stapp03
		  become: yes
		  tasks:
		    - name: Create the media.txt on stapp03
		      file:
		        path: /opt/sysops/media.txt
		        state: touch
		    - name: Set ACL for media.txt
		      acl:
		        path: /opt/sysops/media.txt
		        entity: banner
		        etype: group
		        permissions: rw
		        state: present

3. Run playbook to make the changes

thor@jump_host ~/ansible$ ansible-playbook -i inventory playbook.yml 

		PLAY [Create file and set ACL in Host 1] ****************************************************************************************************

		TASK [Gathering Facts] **********************************************************************************************************************
		ok: [stapp01]

		TASK [Create the blog.txt on stapp01] *******************************************************************************************************
		changed: [stapp01]

		TASK [Set ACL for blog.txt] *****************************************************************************************************************
		changed: [stapp01]

		PLAY [Create file and set ACL in Host 2] ****************************************************************************************************

		TASK [Gathering Facts] **********************************************************************************************************************
		ok: [stapp02]

		TASK [Create the story.txt on stapp02] ******************************************************************************************************
		changed: [stapp02]

		TASK [Set ACL for story.txt] ****************************************************************************************************************
		changed: [stapp02]

		PLAY [Create file and set ACL in Host 3] ****************************************************************************************************

		TASK [Gathering Facts] **********************************************************************************************************************
		ok: [stapp03]

		TASK [Create the media.txt on stapp03] ******************************************************************************************************
		changed: [stapp03]

		TASK [Set ACL for media.txt] ****************************************************************************************************************
		changed: [stapp03]

		PLAY RECAP **********************************************************************************************************************************
		stapp01                    : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
		stapp02                    : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
		stapp03                    : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   


4. Validate the task by checking the files on each server 

thor@jump_host ~/ansible$ ansible -i inventory all -a "ls -ahl /opt/sysops/"
		stapp02 | CHANGED | rc=0 >>
		total 8.0K
		drwxr-xr-x  2 root root 4.0K Dec 11 05:25 .
		drwxr-xr-x  1 root root 4.0K Dec 11 05:22 ..
		-rw-rw-r--+ 1 root root    0 Dec 11 05:25 story.txt
		stapp01 | CHANGED | rc=0 >>
		total 8.0K
		drwxr-xr-x  2 root root 4.0K Dec 11 05:25 .
		drwxr-xr-x  1 root root 4.0K Dec 11 05:22 ..
		-rw-r--r--+ 1 root root    0 Dec 11 05:25 blog.txt
		stapp03 | CHANGED | rc=0 >>
		total 8.0K
		drwxr-xr-x  2 root root 4.0K Dec 11 05:25 .
		drwxr-xr-x  1 root root 4.0K Dec 11 05:22 ..
		-rw-rw-r--+ 1 root root    0 Dec 11 05:25 media.txt

--------------------------------------------------------------------------------------------------------------------------------
Task 114: 12/Dec/2022

Rollback a Deployment in Kubernetes

This morning the Nautilus DevOps team rolled out a new release for one of the applications. Recently one of the customers logged a complaint which seems to be about a bug related to the recent release. Therefore, the team wants to rollback the recent release.

There is a deployment named nginx-deployment; roll it back to the previous revision.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

1. check existing deployment and pods running status

thor@jump_host ~$ kubectl  get all
		NAME                                    READY   STATUS    RESTARTS   AGE
		pod/nginx-deployment-594c94667b-mvkmr   1/1     Running   0          37s
		pod/nginx-deployment-594c94667b-r7b6k   1/1     Running   0          16s
		pod/nginx-deployment-594c94667b-sfxns   1/1     Running   0          20s

		NAME                    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
		service/kubernetes      ClusterIP   10.96.0.1      <none>        443/TCP        135m
		service/nginx-service   NodePort    10.96.37.132   <none>        80:30008/TCP   49s

		NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
		deployment.apps/nginx-deployment   3/3     3            3           49s

		NAME                                          DESIRED   CURRENT   READY   AGE
		replicaset.apps/nginx-deployment-594c94667b   3         3         3       38s
		replicaset.apps/nginx-deployment-74fb588559   0         0         0       49s

thor@jump_host ~$ kubectl  get deploy
		NAME               READY   UP-TO-DATE   AVAILABLE   AGE
		nginx-deployment   3/3     3            3           60s

thor@jump_host ~$ kubectl  get pods
		NAME                                READY   STATUS    RESTARTS   AGE
		nginx-deployment-594c94667b-mvkmr   1/1     Running   0          54s
		nginx-deployment-594c94667b-r7b6k   1/1     Running   0          33s
		nginx-deployment-594c94667b-sfxns   1/1     Running   0          37s

2. Rollback/revert last deployment

thor@jump_host ~$ kubectl rollout undo deployment nginx-deployment
		deployment.apps/nginx-deployment rolled back

3. Wait for all pods to get back to running status

thor@jump_host ~$ kubectl  get deploy
		NAME               READY   UP-TO-DATE   AVAILABLE   AGE
		nginx-deployment   3/3     2            3           112s

thor@jump_host ~$ kubectl  get pods
		NAME                                READY   STATUS        RESTARTS   AGE
		nginx-deployment-594c94667b-mvkmr   1/1     Terminating   0          105s
		nginx-deployment-594c94667b-sfxns   0/1     Terminating   0          88s
		nginx-deployment-74fb588559-h7hpg   1/1     Running       0          9s
		nginx-deployment-74fb588559-whhpb   1/1     Running       0          7s
		nginx-deployment-74fb588559-zvjz7   1/1     Running       0          4s

thor@jump_host ~$ kubectl  get pods
		NAME                                READY   STATUS        RESTARTS   AGE
		nginx-deployment-594c94667b-sfxns   0/1     Terminating   0          92s
		nginx-deployment-74fb588559-h7hpg   1/1     Running       0          13s
		nginx-deployment-74fb588559-whhpb   1/1     Running       0          11s
		nginx-deployment-74fb588559-zvjz7   1/1     Running       0          8s

thor@jump_host ~$ kubectl  get pods
		NAME                                READY   STATUS    RESTARTS   AGE
		nginx-deployment-74fb588559-h7hpg   1/1     Running   0          16s
		nginx-deployment-74fb588559-whhpb   1/1     Running   0          14s
		nginx-deployment-74fb588559-zvjz7   1/1     Running   0          11s

4. Validate the task

thor@jump_host ~$ kubectl rollout status deployment nginx-deployment
		deployment "nginx-deployment" successfully rolled out

thor@jump_host ~$ kubectl  get all
		NAME                                    READY   STATUS    RESTARTS   AGE
		pod/nginx-deployment-74fb588559-h7hpg   1/1     Running   0          40s
		pod/nginx-deployment-74fb588559-whhpb   1/1     Running   0          38s
		pod/nginx-deployment-74fb588559-zvjz7   1/1     Running   0          35s

		NAME                    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
		service/kubernetes      ClusterIP   10.96.0.1      <none>        443/TCP        137m
		service/nginx-service   NodePort    10.96.37.132   <none>        80:30008/TCP   2m28s

		NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
		deployment.apps/nginx-deployment   3/3     3            3           2m28s

		NAME                                          DESIRED   CURRENT   READY   AGE
		replicaset.apps/nginx-deployment-594c94667b   0         0         0       2m17s
		replicaset.apps/nginx-deployment-74fb588559   3         3         3       2m28s
thor@jump_host ~$

--------------------------------------------------------------------------------------------------------------------------------
Task 115: 13/Dec/2022

Ansible Config File Update

To manage all servers within the stack using Ansible, the Nautilus DevOps team is planning to use a common sudo user among all servers. Ansible will be able to use this to perform different tasks on each server. This is not finalized yet, but the team has decided to first perform testing. The DevOps team has already installed Ansible on jump host using yum, and they now have the following requirement:

On jump host make appropriate changes so that Ansible can use kareem as a default ssh user for all hosts. Make changes in Ansible's default configuration only —please do not try to create a new config.


1. Switch to root user on jump_host

		thor@jump_host ~$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for thor: 

2. check current configuration in ansible config file(/etc/ansible/ansible.cfg) for default ssh user (remote_user) 

[root@jump_host ~]# cat /etc/ansible/ansible.cfg |grep remote_user
		#remote_user = root

3. Change the value of remote_user in config file as per given requirements

[root@jump_host ~]# vi /etc/ansible/ansible.cfg

[root@jump_host ~]# cat /etc/ansible/ansible.cfg |grep remote_user
		remote_user = kareem

[root@jump_host ~]#

--------------------------------------------------------------------------------------------------------------------------------
Task 116: 14/Dec/2022

Git Install and Create Repository

The Nautilus development team shared with the DevOps team requirements for new application development, setting up a Git repository for that project. Create a Git repository on Storage server in Stratos DC as per details given below:

    Install git package using yum on Storage server.

    After that create/init a git repository /opt/apps.git (use the exact name as asked and make sure not to create a bare repository).

1. Login to Storage server and switch to root user

thor@jump_host ~$ ssh natasha@ststor01
		The authenticity of host 'ststor01 (172.16.238.15)' can't be established.
		ECDSA key fingerprint is SHA256:uWWoehA80FSH1JuFHkpfSS/WsONvW8V0Pk5krQQP86Y.
		ECDSA key fingerprint is MD5:a9:cf:a5:29:d8:fa:df:6e:c3:f6:4b:71:8a:c2:dd:cc.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'ststor01,172.16.238.15' (ECDSA) to the list of known hosts.
		natasha@ststor01's password: 
 
[natasha@ststor01 ~]$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for natasha: 

2. Install git and verify 

[root@ststor01 ~]# rpm -qa|grep git

[root@ststor01 ~]# yum install -y git
		Loaded plugins: fastestmirror, ovl
		Determining fastest mirrors
		 * base: mirror.grid.uchicago.edu
		 * extras: mirror.team-cymru.com
		 * updates: mirror.vacares.com
		base                                                                                                                  | 3.6 kB  00:00:00     
		extras                                                                                                                | 2.9 kB  00:00:00     
		updates                                                                                                               | 2.9 kB  00:00:00     
		(1/4): base/7/x86_64/group_gz                                                                                         | 153 kB  00:00:00     
		(2/4): extras/7/x86_64/primary_db                                                                                     | 249 kB  00:00:00     
		(3/4): base/7/x86_64/primary_db                                                                                       | 6.1 MB  00:00:00     
		(4/4): updates/7/x86_64/primary_db                                                                                    |  18 MB  00:00:05     
		Resolving Dependencies
		--> Running transaction check
		---> Package git.x86_64 0:1.8.3.1-23.el7_8 will be installed
		--> Processing Dependency: perl-Git = 1.8.3.1-23.el7_8 for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl >= 5.008 for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: rsync for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(warnings) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(vars) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(strict) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(lib) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(Term::ReadKey) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(Git) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(Getopt::Long) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::stat) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::Temp) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::Spec) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::Path) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::Find) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::Copy) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::Basename) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(Exporter) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(Error) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: openssh-clients for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: less for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: /usr/bin/perl for package: git-1.8.3.1-23.el7_8.x86_64
		--> Running transaction check
		---> Package less.x86_64 0:458-9.el7 will be installed
		--> Processing Dependency: groff-base for package: less-458-9.el7.x86_64
		---> Package openssh-clients.x86_64 0:7.4p1-22.el7_9 will be installed
		--> Processing Dependency: openssh = 7.4p1-22.el7_9 for package: openssh-clients-7.4p1-22.el7_9.x86_64
		--> Processing Dependency: libedit.so.0()(64bit) for package: openssh-clients-7.4p1-22.el7_9.x86_64
		---> Package perl.x86_64 4:5.16.3-299.el7_9 will be installed
		--> Processing Dependency: perl-libs = 4:5.16.3-299.el7_9 for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Socket) >= 1.3 for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Scalar::Util) >= 1.10 for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl-macros for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl-libs for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(threads::shared) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(threads) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(constant) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Time::Local) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Time::HiRes) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Storable) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Socket) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Scalar::Util) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Pod::Simple::XHTML) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Pod::Simple::Search) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Filter::Util::Call) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Carp) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: libperl.so()(64bit) for package: 4:perl-5.16.3-299.el7_9.x86_64
		---> Package perl-Error.noarch 1:0.17020-2.el7 will be installed
		---> Package perl-Exporter.noarch 0:5.68-3.el7 will be installed
		---> Package perl-File-Path.noarch 0:2.09-2.el7 will be installed
		---> Package perl-File-Temp.noarch 0:0.23.01-3.el7 will be installed
		---> Package perl-Getopt-Long.noarch 0:2.40-3.el7 will be installed
		--> Processing Dependency: perl(Pod::Usage) >= 1.14 for package: perl-Getopt-Long-2.40-3.el7.noarch
		--> Processing Dependency: perl(Text::ParseWords) for package: perl-Getopt-Long-2.40-3.el7.noarch
		---> Package perl-Git.noarch 0:1.8.3.1-23.el7_8 will be installed
		---> Package perl-PathTools.x86_64 0:3.40-5.el7 will be installed
		---> Package perl-TermReadKey.x86_64 0:2.30-20.el7 will be installed
		---> Package rsync.x86_64 0:3.1.2-11.el7_9 will be installed
		--> Running transaction check
		---> Package groff-base.x86_64 0:1.22.2-8.el7 will be installed
		---> Package libedit.x86_64 0:3.0-12.20121213cvs.el7 will be installed
		---> Package openssh.x86_64 0:7.4p1-21.el7 will be updated
		--> Processing Dependency: openssh = 7.4p1-21.el7 for package: openssh-server-7.4p1-21.el7.x86_64
		---> Package openssh.x86_64 0:7.4p1-22.el7_9 will be an update
		---> Package perl-Carp.noarch 0:1.26-244.el7 will be installed
		---> Package perl-Filter.x86_64 0:1.49-3.el7 will be installed
		---> Package perl-Pod-Simple.noarch 1:3.28-4.el7 will be installed
		--> Processing Dependency: perl(Pod::Escapes) >= 1.04 for package: 1:perl-Pod-Simple-3.28-4.el7.noarch
		--> Processing Dependency: perl(Encode) for package: 1:perl-Pod-Simple-3.28-4.el7.noarch
		---> Package perl-Pod-Usage.noarch 0:1.63-3.el7 will be installed
		--> Processing Dependency: perl(Pod::Text) >= 3.15 for package: perl-Pod-Usage-1.63-3.el7.noarch
		--> Processing Dependency: perl-Pod-Perldoc for package: perl-Pod-Usage-1.63-3.el7.noarch
		---> Package perl-Scalar-List-Utils.x86_64 0:1.27-248.el7 will be installed
		---> Package perl-Socket.x86_64 0:2.010-5.el7 will be installed
		---> Package perl-Storable.x86_64 0:2.45-3.el7 will be installed
		---> Package perl-Text-ParseWords.noarch 0:3.29-4.el7 will be installed
		---> Package perl-Time-HiRes.x86_64 4:1.9725-3.el7 will be installed
		---> Package perl-Time-Local.noarch 0:1.2300-2.el7 will be installed
		---> Package perl-constant.noarch 0:1.27-2.el7 will be installed
		---> Package perl-libs.x86_64 4:5.16.3-299.el7_9 will be installed
		---> Package perl-macros.x86_64 4:5.16.3-299.el7_9 will be installed
		---> Package perl-threads.x86_64 0:1.87-4.el7 will be installed
		---> Package perl-threads-shared.x86_64 0:1.43-6.el7 will be installed
		--> Running transaction check
		---> Package openssh-server.x86_64 0:7.4p1-21.el7 will be updated
		---> Package openssh-server.x86_64 0:7.4p1-22.el7_9 will be an update
		---> Package perl-Encode.x86_64 0:2.51-7.el7 will be installed
		---> Package perl-Pod-Escapes.noarch 1:1.04-299.el7_9 will be installed
		---> Package perl-Pod-Perldoc.noarch 0:3.20-4.el7 will be installed
		--> Processing Dependency: perl(parent) for package: perl-Pod-Perldoc-3.20-4.el7.noarch
		--> Processing Dependency: perl(HTTP::Tiny) for package: perl-Pod-Perldoc-3.20-4.el7.noarch
		---> Package perl-podlators.noarch 0:2.5.1-3.el7 will be installed
		--> Running transaction check
		---> Package perl-HTTP-Tiny.noarch 0:0.033-3.el7 will be installed
		---> Package perl-parent.noarch 1:0.225-244.el7 will be installed
		--> Finished Dependency Resolution

		Dependencies Resolved

		=============================================================================================================================================
		 Package                                  Arch                     Version                                   Repository                 Size
		=============================================================================================================================================
		Installing:
		 git                                      x86_64                   1.8.3.1-23.el7_8                          base                      4.4 M
		Installing for dependencies:
		 groff-base                               x86_64                   1.22.2-8.el7                              base                      942 k
		 less                                     x86_64                   458-9.el7                                 base                      120 k
		 libedit                                  x86_64                   3.0-12.20121213cvs.el7                    base                       92 k
		 openssh-clients                          x86_64                   7.4p1-22.el7_9                            updates                   655 k
		 perl                                     x86_64                   4:5.16.3-299.el7_9                        updates                   8.0 M
		 perl-Carp                                noarch                   1.26-244.el7                              base                       19 k
		 perl-Encode                              x86_64                   2.51-7.el7                                base                      1.5 M
		 perl-Error                               noarch                   1:0.17020-2.el7                           base                       32 k
		 perl-Exporter                            noarch                   5.68-3.el7                                base                       28 k
		 perl-File-Path                           noarch                   2.09-2.el7                                base                       26 k
		 perl-File-Temp                           noarch                   0.23.01-3.el7                             base                       56 k
		 perl-Filter                              x86_64                   1.49-3.el7                                base                       76 k
		 perl-Getopt-Long                         noarch                   2.40-3.el7                                base                       56 k
		 perl-Git                                 noarch                   1.8.3.1-23.el7_8                          base                       56 k
		 perl-HTTP-Tiny                           noarch                   0.033-3.el7                               base                       38 k
		 perl-PathTools                           x86_64                   3.40-5.el7                                base                       82 k
		 perl-Pod-Escapes                         noarch                   1:1.04-299.el7_9                          updates                    52 k
		 perl-Pod-Perldoc                         noarch                   3.20-4.el7                                base                       87 k
		 perl-Pod-Simple                          noarch                   1:3.28-4.el7                              base                      216 k
		 perl-Pod-Usage                           noarch                   1.63-3.el7                                base                       27 k
		 perl-Scalar-List-Utils                   x86_64                   1.27-248.el7                              base                       36 k
		 perl-Socket                              x86_64                   2.010-5.el7                               base                       49 k
		 perl-Storable                            x86_64                   2.45-3.el7                                base                       77 k
		 perl-TermReadKey                         x86_64                   2.30-20.el7                               base                       31 k
		 perl-Text-ParseWords                     noarch                   3.29-4.el7                                base                       14 k
		 perl-Time-HiRes                          x86_64                   4:1.9725-3.el7                            base                       45 k
		 perl-Time-Local                          noarch                   1.2300-2.el7                              base                       24 k
		 perl-constant                            noarch                   1.27-2.el7                                base                       19 k
		 perl-libs                                x86_64                   4:5.16.3-299.el7_9                        updates                   690 k
		 perl-macros                              x86_64                   4:5.16.3-299.el7_9                        updates                    44 k
		 perl-parent                              noarch                   1:0.225-244.el7                           base                       12 k
		 perl-podlators                           noarch                   2.5.1-3.el7                               base                      112 k
		 perl-threads                             x86_64                   1.87-4.el7                                base                       49 k
		 perl-threads-shared                      x86_64                   1.43-6.el7                                base                       39 k
		 rsync                                    x86_64                   3.1.2-11.el7_9                            updates                   408 k
		Updating for dependencies:
		 openssh                                  x86_64                   7.4p1-22.el7_9                            updates                   510 k
		 openssh-server                           x86_64                   7.4p1-22.el7_9                            updates                   459 k

		Transaction Summary
		=============================================================================================================================================
		Install  1 Package  (+35 Dependent packages)
		Upgrade             (  2 Dependent packages)

		Total download size: 19 M
		Downloading packages:
		Delta RPMs disabled because /usr/bin/applydeltarpm not installed.
		(1/38): groff-base-1.22.2-8.el7.x86_64.rpm                                                                            | 942 kB  00:00:00     
		(2/38): less-458-9.el7.x86_64.rpm                                                                                     | 120 kB  00:00:00     
		(3/38): libedit-3.0-12.20121213cvs.el7.x86_64.rpm                                                                     |  92 kB  00:00:00     
		(4/38): git-1.8.3.1-23.el7_8.x86_64.rpm                                                                               | 4.4 MB  00:00:00     
		(5/38): openssh-7.4p1-22.el7_9.x86_64.rpm                                                                             | 510 kB  00:00:00     
		(6/38): openssh-clients-7.4p1-22.el7_9.x86_64.rpm                                                                     | 655 kB  00:00:00     
		(7/38): perl-Carp-1.26-244.el7.noarch.rpm                                                                             |  19 kB  00:00:00     
		(8/38): perl-Error-0.17020-2.el7.noarch.rpm                                                                           |  32 kB  00:00:00     
		(9/38): perl-Exporter-5.68-3.el7.noarch.rpm                                                                           |  28 kB  00:00:00     
		(10/38): perl-File-Path-2.09-2.el7.noarch.rpm                                                                         |  26 kB  00:00:00     
		(11/38): perl-File-Temp-0.23.01-3.el7.noarch.rpm                                                                      |  56 kB  00:00:00     
		(12/38): perl-Filter-1.49-3.el7.x86_64.rpm                                                                            |  76 kB  00:00:00     
		(13/38): openssh-server-7.4p1-22.el7_9.x86_64.rpm                                                                     | 459 kB  00:00:00     
		(14/38): perl-Getopt-Long-2.40-3.el7.noarch.rpm                                                                       |  56 kB  00:00:00     
		(15/38): perl-Git-1.8.3.1-23.el7_8.noarch.rpm                                                                         |  56 kB  00:00:00     
		(16/38): perl-HTTP-Tiny-0.033-3.el7.noarch.rpm                                                                        |  38 kB  00:00:00     
		(17/38): perl-PathTools-3.40-5.el7.x86_64.rpm                                                                         |  82 kB  00:00:00     
		(18/38): perl-Pod-Perldoc-3.20-4.el7.noarch.rpm                                                                       |  87 kB  00:00:00     
		(19/38): perl-Pod-Escapes-1.04-299.el7_9.noarch.rpm                                                                   |  52 kB  00:00:00     
		(20/38): perl-Pod-Simple-3.28-4.el7.noarch.rpm                                                                        | 216 kB  00:00:00     
		(21/38): perl-Pod-Usage-1.63-3.el7.noarch.rpm                                                                         |  27 kB  00:00:00     
		(22/38): perl-Scalar-List-Utils-1.27-248.el7.x86_64.rpm                                                               |  36 kB  00:00:00     
		(23/38): perl-Socket-2.010-5.el7.x86_64.rpm                                                                           |  49 kB  00:00:00     
		(24/38): perl-Storable-2.45-3.el7.x86_64.rpm                                                                          |  77 kB  00:00:00     
		(25/38): perl-TermReadKey-2.30-20.el7.x86_64.rpm                                                                      |  31 kB  00:00:00     
		(26/38): perl-Encode-2.51-7.el7.x86_64.rpm                                                                            | 1.5 MB  00:00:00     
		(27/38): perl-Text-ParseWords-3.29-4.el7.noarch.rpm                                                                   |  14 kB  00:00:00     
		(28/38): perl-Time-HiRes-1.9725-3.el7.x86_64.rpm                                                                      |  45 kB  00:00:00     
		(29/38): perl-Time-Local-1.2300-2.el7.noarch.rpm                                                                      |  24 kB  00:00:00     
		(30/38): perl-constant-1.27-2.el7.noarch.rpm                                                                          |  19 kB  00:00:00     
		(31/38): perl-libs-5.16.3-299.el7_9.x86_64.rpm                                                                        | 690 kB  00:00:00     
		(32/38): perl-parent-0.225-244.el7.noarch.rpm                                                                         |  12 kB  00:00:00     
		(33/38): perl-threads-1.87-4.el7.x86_64.rpm                                                                           |  49 kB  00:00:00     
		(34/38): perl-macros-5.16.3-299.el7_9.x86_64.rpm                                                                      |  44 kB  00:00:00     
		(35/38): perl-threads-shared-1.43-6.el7.x86_64.rpm                                                                    |  39 kB  00:00:00     
		(36/38): perl-podlators-2.5.1-3.el7.noarch.rpm                                                                        | 112 kB  00:00:00     
		(37/38): rsync-3.1.2-11.el7_9.x86_64.rpm                                                                              | 408 kB  00:00:00     
		(38/38): perl-5.16.3-299.el7_9.x86_64.rpm                                                                             | 8.0 MB  00:00:04     
		---------------------------------------------------------------------------------------------------------------------------------------------
		Total                                                                                                        3.2 MB/s |  19 MB  00:00:05     
		Running transaction check
		Running transaction test
		Transaction test succeeded
		Running transaction
		  Installing : groff-base-1.22.2-8.el7.x86_64                                                                                           1/40 
		  Updating   : openssh-7.4p1-22.el7_9.x86_64                                                                                            2/40 
		  Installing : 1:perl-parent-0.225-244.el7.noarch                                                                                       3/40 
		  Installing : perl-HTTP-Tiny-0.033-3.el7.noarch                                                                                        4/40 
		  Installing : perl-podlators-2.5.1-3.el7.noarch                                                                                        5/40 
		  Installing : perl-Pod-Perldoc-3.20-4.el7.noarch                                                                                       6/40 
		  Installing : 1:perl-Pod-Escapes-1.04-299.el7_9.noarch                                                                                 7/40 
		  Installing : perl-Encode-2.51-7.el7.x86_64                                                                                            8/40 
		  Installing : perl-Text-ParseWords-3.29-4.el7.noarch                                                                                   9/40 
		  Installing : perl-Pod-Usage-1.63-3.el7.noarch                                                                                        10/40 
		  Installing : 4:perl-macros-5.16.3-299.el7_9.x86_64                                                                                   11/40 
		  Installing : 4:perl-Time-HiRes-1.9725-3.el7.x86_64                                                                                   12/40 
		  Installing : perl-Exporter-5.68-3.el7.noarch                                                                                         13/40 
		  Installing : perl-constant-1.27-2.el7.noarch                                                                                         14/40 
		  Installing : perl-Socket-2.010-5.el7.x86_64                                                                                          15/40 
		  Installing : perl-Time-Local-1.2300-2.el7.noarch                                                                                     16/40 
		  Installing : perl-Carp-1.26-244.el7.noarch                                                                                           17/40 
		  Installing : perl-Storable-2.45-3.el7.x86_64                                                                                         18/40 
		  Installing : perl-PathTools-3.40-5.el7.x86_64                                                                                        19/40 
		  Installing : perl-Scalar-List-Utils-1.27-248.el7.x86_64                                                                              20/40 
		  Installing : 1:perl-Pod-Simple-3.28-4.el7.noarch                                                                                     21/40 
		  Installing : perl-File-Temp-0.23.01-3.el7.noarch                                                                                     22/40 
		  Installing : perl-File-Path-2.09-2.el7.noarch                                                                                        23/40 
		  Installing : perl-threads-shared-1.43-6.el7.x86_64                                                                                   24/40 
		  Installing : perl-threads-1.87-4.el7.x86_64                                                                                          25/40 
		  Installing : perl-Filter-1.49-3.el7.x86_64                                                                                           26/40 
		  Installing : 4:perl-libs-5.16.3-299.el7_9.x86_64                                                                                     27/40 
		  Installing : perl-Getopt-Long-2.40-3.el7.noarch                                                                                      28/40 
		  Installing : 4:perl-5.16.3-299.el7_9.x86_64                                                                                          29/40 
		  Installing : 1:perl-Error-0.17020-2.el7.noarch                                                                                       30/40 
		  Installing : perl-TermReadKey-2.30-20.el7.x86_64                                                                                     31/40 
		  Installing : less-458-9.el7.x86_64                                                                                                   32/40 
		  Installing : libedit-3.0-12.20121213cvs.el7.x86_64                                                                                   33/40 
		  Installing : openssh-clients-7.4p1-22.el7_9.x86_64                                                                                   34/40 
		  Installing : rsync-3.1.2-11.el7_9.x86_64                                                                                             35/40 
		  Installing : perl-Git-1.8.3.1-23.el7_8.noarch                                                                                        36/40 
		  Installing : git-1.8.3.1-23.el7_8.x86_64                                                                                             37/40 
		  Updating   : openssh-server-7.4p1-22.el7_9.x86_64                                                                                    38/40 
		  Cleanup    : openssh-server-7.4p1-21.el7.x86_64                                                                                      39/40 
		  Cleanup    : openssh-7.4p1-21.el7.x86_64                                                                                             40/40 
		  Verifying  : perl-HTTP-Tiny-0.033-3.el7.noarch                                                                                        1/40 
		  Verifying  : perl-threads-shared-1.43-6.el7.x86_64                                                                                    2/40 
		  Verifying  : 4:perl-Time-HiRes-1.9725-3.el7.x86_64                                                                                    3/40 
		  Verifying  : openssh-clients-7.4p1-22.el7_9.x86_64                                                                                    4/40 
		  Verifying  : perl-Exporter-5.68-3.el7.noarch                                                                                          5/40 
		  Verifying  : perl-constant-1.27-2.el7.noarch                                                                                          6/40 
		  Verifying  : perl-PathTools-3.40-5.el7.x86_64                                                                                         7/40 
		  Verifying  : openssh-7.4p1-22.el7_9.x86_64                                                                                            8/40 
		  Verifying  : 4:perl-macros-5.16.3-299.el7_9.x86_64                                                                                    9/40 
		  Verifying  : git-1.8.3.1-23.el7_8.x86_64                                                                                             10/40 
		  Verifying  : 1:perl-parent-0.225-244.el7.noarch                                                                                      11/40 
		  Verifying  : perl-Socket-2.010-5.el7.x86_64                                                                                          12/40 
		  Verifying  : rsync-3.1.2-11.el7_9.x86_64                                                                                             13/40 
		  Verifying  : perl-TermReadKey-2.30-20.el7.x86_64                                                                                     14/40 
		  Verifying  : groff-base-1.22.2-8.el7.x86_64                                                                                          15/40 
		  Verifying  : perl-File-Temp-0.23.01-3.el7.noarch                                                                                     16/40 
		  Verifying  : 1:perl-Pod-Simple-3.28-4.el7.noarch                                                                                     17/40 
		  Verifying  : perl-Time-Local-1.2300-2.el7.noarch                                                                                     18/40 
		  Verifying  : 1:perl-Pod-Escapes-1.04-299.el7_9.noarch                                                                                19/40 
		  Verifying  : perl-Git-1.8.3.1-23.el7_8.noarch                                                                                        20/40 
		  Verifying  : perl-Carp-1.26-244.el7.noarch                                                                                           21/40 
		  Verifying  : 1:perl-Error-0.17020-2.el7.noarch                                                                                       22/40 
		  Verifying  : perl-Storable-2.45-3.el7.x86_64                                                                                         23/40 
		  Verifying  : perl-Scalar-List-Utils-1.27-248.el7.x86_64                                                                              24/40 
		  Verifying  : perl-Pod-Usage-1.63-3.el7.noarch                                                                                        25/40 
		  Verifying  : perl-Encode-2.51-7.el7.x86_64                                                                                           26/40 
		  Verifying  : perl-Pod-Perldoc-3.20-4.el7.noarch                                                                                      27/40 
		  Verifying  : perl-podlators-2.5.1-3.el7.noarch                                                                                       28/40 
		  Verifying  : 4:perl-5.16.3-299.el7_9.x86_64                                                                                          29/40 
		  Verifying  : perl-File-Path-2.09-2.el7.noarch                                                                                        30/40 
		  Verifying  : libedit-3.0-12.20121213cvs.el7.x86_64                                                                                   31/40 
		  Verifying  : perl-threads-1.87-4.el7.x86_64                                                                                          32/40 
		  Verifying  : openssh-server-7.4p1-22.el7_9.x86_64                                                                                    33/40 
		  Verifying  : perl-Filter-1.49-3.el7.x86_64                                                                                           34/40 
		  Verifying  : perl-Getopt-Long-2.40-3.el7.noarch                                                                                      35/40 
		  Verifying  : perl-Text-ParseWords-3.29-4.el7.noarch                                                                                  36/40 
		  Verifying  : 4:perl-libs-5.16.3-299.el7_9.x86_64                                                                                     37/40 
		  Verifying  : less-458-9.el7.x86_64                                                                                                   38/40 
		  Verifying  : openssh-7.4p1-21.el7.x86_64                                                                                             39/40 
		  Verifying  : openssh-server-7.4p1-21.el7.x86_64                                                                                      40/40 

		Installed:
		  git.x86_64 0:1.8.3.1-23.el7_8                                                                                                              

		Dependency Installed:
		  groff-base.x86_64 0:1.22.2-8.el7             less.x86_64 0:458-9.el7                      libedit.x86_64 0:3.0-12.20121213cvs.el7         
		  openssh-clients.x86_64 0:7.4p1-22.el7_9      perl.x86_64 4:5.16.3-299.el7_9               perl-Carp.noarch 0:1.26-244.el7                 
		  perl-Encode.x86_64 0:2.51-7.el7              perl-Error.noarch 1:0.17020-2.el7            perl-Exporter.noarch 0:5.68-3.el7               
		  perl-File-Path.noarch 0:2.09-2.el7           perl-File-Temp.noarch 0:0.23.01-3.el7        perl-Filter.x86_64 0:1.49-3.el7                 
		  perl-Getopt-Long.noarch 0:2.40-3.el7         perl-Git.noarch 0:1.8.3.1-23.el7_8           perl-HTTP-Tiny.noarch 0:0.033-3.el7             
		  perl-PathTools.x86_64 0:3.40-5.el7           perl-Pod-Escapes.noarch 1:1.04-299.el7_9     perl-Pod-Perldoc.noarch 0:3.20-4.el7            
		  perl-Pod-Simple.noarch 1:3.28-4.el7          perl-Pod-Usage.noarch 0:1.63-3.el7           perl-Scalar-List-Utils.x86_64 0:1.27-248.el7    
		  perl-Socket.x86_64 0:2.010-5.el7             perl-Storable.x86_64 0:2.45-3.el7            perl-TermReadKey.x86_64 0:2.30-20.el7           
		  perl-Text-ParseWords.noarch 0:3.29-4.el7     perl-Time-HiRes.x86_64 4:1.9725-3.el7        perl-Time-Local.noarch 0:1.2300-2.el7           
		  perl-constant.noarch 0:1.27-2.el7            perl-libs.x86_64 4:5.16.3-299.el7_9          perl-macros.x86_64 4:5.16.3-299.el7_9           
		  perl-parent.noarch 1:0.225-244.el7           perl-podlators.noarch 0:2.5.1-3.el7          perl-threads.x86_64 0:1.87-4.el7                
		  perl-threads-shared.x86_64 0:1.43-6.el7      rsync.x86_64 0:3.1.2-11.el7_9               

		Dependency Updated:
		  openssh.x86_64 0:7.4p1-22.el7_9                                   openssh-server.x86_64 0:7.4p1-22.el7_9                                  

		Complete!

[root@ststor01 ~]# rpm -qa|grep git
		git-1.8.3.1-23.el7_8.x86_64

3. Go to mentioned folder and init/create the required git repository 

[root@ststor01 ~]# cd /opt/

[root@ststor01 opt]# git init apps.git
		Initialized empty Git repository in /opt/apps.git/.git/

4. Verify the task  

[root@ststor01 opt]# ls -ahl
		total 12K
		drwxr-xr-x 1 root root 4.0K Dec 14 14:12 .
		drwxr-xr-x 1 root root 4.0K Dec 14 14:09 ..
		drwxr-xr-x 3 root root 4.0K Dec 14 14:12 apps.git

[root@ststor01 opt]# cd apps.git/

[root@ststor01 apps.git]# ls -ahl
		total 12K
		drwxr-xr-x 3 root root 4.0K Dec 14 14:12 .
		drwxr-xr-x 1 root root 4.0K Dec 14 14:12 ..
		drwxr-xr-x 7 root root 4.0K Dec 14 14:12 .git
[root@ststor01 apps.git]#

--------------------------------------------------------------------------------------------------------------------------------
Task 117: 18/Dec/2022

Ansible Lineinfile Module

The Nautilus DevOps team want to install and set up a simple httpd web server on all app servers in Stratos DC. They also want to deploy a sample web page using Ansible. Therefore, write the required playbook to complete this task as per details mentioned below.

We already have an inventory file under /home/thor/ansible directory on jump host. Write a playbook playbook.yml under /home/thor/ansible directory on jump host itself. Using the playbook perform below given tasks:

    Install httpd web server on all app servers, and make sure its service is up and running.

    Create a file /var/www/html/index.html with content:

		This is a Nautilus sample file, created using Ansible!

    Using lineinfile Ansible module add some more content in /var/www/html/index.html file. Below is the content:

		Welcome to Nautilus Group!

Also make sure this new line is added at the top of the file.

    The /var/www/html/index.html file's user and group owner should be apache on all app servers.

    The /var/www/html/index.html file's permissions should be 0744 on all app servers.

Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure the playbook works this way without passing any extra arguments.

1. Go to mentioned  ansible folder and check inventory and confing files

thor@jump_host ~$ cd /home/thor/ansible/

thor@jump_host ~/ansible$ ls -ahl
		total 16K
		drwxr-xr-x 2 thor thor 4.0K Dec 18 14:54 .
		drwxr----- 1 thor thor 4.0K Dec 18 14:54 ..
		-rw-r--r-- 1 thor thor   36 Dec 18 14:54 ansible.cfg
		-rw-r--r-- 1 thor thor  237 Dec 18 14:54 inventory

thor@jump_host ~/ansible$ cat inventory 
		stapp01 ansible_host=172.16.238.10 ansible_ssh_pass=Ir0nM@n ansible_user=tony
		stapp02 ansible_host=172.16.238.11 ansible_ssh_pass=Am3ric@ ansible_user=steve
		stapp03 ansible_host=172.16.238.12 ansible_ssh_pass=BigGr33n ansible_user=banner

thor@jump_host ~/ansible$ cat ansible.cfg 
		[defaults]
		host_key_checking = False

2. Create playbook.yml as per given requirement

thor@jump_host ~/ansible$ vi playbook.yml

thor@jump_host ~/ansible$ cat playbook.yml 
		- name: Install httpd and setup index.html
		  hosts: stapp01, stapp02, stapp03
		  become: yes
		  tasks:
		  - name: Install httpd
		    package:
		      name: httpd
		      state: present
		  - name: Start service httpd, if not started
		    service:
		      name: httpd
		      state: started
		  - name: Add content in index.html. Create file if it does not exist and set file attributes
		    copy:
		      dest: /var/www/html/index.html
		      content: This is a Nautilus sample file, created using Ansible!
		      mode: "0744"
		      owner: apache
		      group: apache
		  - name: Update content in index.html
		    lineinfile:
		      path: /var/www/html/index.html
		      insertbefore: BOF
		      line: Welcome to Nautilus Group!

3. Check current status on app server before running playbook

thor@jump_host ~/ansible$ ansible -i inventory  all -a "cat /var/www/html/index.html"
		stapp03 | CHANGED | rc=0 >>
		This is KodeKloud Ansible Lab !
		stapp01 | CHANGED | rc=0 >>
		This is KodeKloud Ansible Lab !
		stapp02 | CHANGED | rc=0 >>
		This is KodeKloud Ansible Lab !

4. Run the ansible playbook

thor@jump_host ~/ansible$ ansible-playbook -i inventory playbook.yml 

		PLAY [Install httpd and setup index.html] ***************************************************************************************************

		TASK [Gathering Facts] **********************************************************************************************************************
		ok: [stapp01]
		ok: [stapp03]
		ok: [stapp02]

		TASK [Install httpd] ************************************************************************************************************************
		changed: [stapp01]
		changed: [stapp02]
		changed: [stapp03]

		TASK [Start service httpd, if not started] **************************************************************************************************
		changed: [stapp01]
		changed: [stapp03]
		changed: [stapp02]

		TASK [Add content in index.html. Create file if it does not exist and set file attributes] **************************************************
		changed: [stapp03]
		changed: [stapp01]
		changed: [stapp02]

		TASK [Update content in index.html] *********************************************************************************************************
		changed: [stapp03]
		changed: [stapp01]
		changed: [stapp02]

		PLAY RECAP **********************************************************************************************************************************
		stapp01                    : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
		stapp02                    : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
		stapp03                    : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   


5. Validate the task by checking changed index file and curl on each app server

thor@jump_host ~/ansible$ ansible -i inventory  all -a "cat /var/www/html/index.html"
		stapp02 | CHANGED | rc=0 >>
		Welcome to Nautilus Group!
		This is a Nautilus sample file, created using Ansible!
		stapp03 | CHANGED | rc=0 >>
		Welcome to Nautilus Group!
		This is a Nautilus sample file, created using Ansible!
		stapp01 | CHANGED | rc=0 >>
		Welcome to Nautilus Group!
		This is a Nautilus sample file, created using Ansible!

thor@jump_host ~/ansible$ curl http://stapp01
		Welcome to Nautilus Group!
		This is a Nautilus sample file, created using Ansible!

thor@jump_host ~/ansible$ curl http://stapp02
		Welcome to Nautilus Group!
		This is a Nautilus sample file, created using Ansible!

thor@jump_host ~/ansible$ curl http://stapp03
		Welcome to Nautilus Group!
		This is a Nautilus sample file, created using Ansible!
thor@jump_host ~/ansible$ 

--------------------------------------------------------------------------------------------------------------------------------
Task 118: 20/Dec/2022

 Git Setup from Scratch

 Some new developers have joined xFusionCorp Industries and have been assigned Nautilus project. They are going to start development on a new application, and some pre-requisites have been shared with the DevOps team to proceed with. Please note that all tasks need to be performed on storage server in Stratos DC.

a. Install git, set up any values for user.email and user.name globally and create a bare repository /opt/beta.git.

b. There is an update hook (to block direct pushes to master branch) under /tmp on storage server itself; use the same to block direct pushes to master branch in /opt/beta.git repo.

c. Clone /opt/beta.git repo in /usr/src/kodekloudrepos/beta directory.

d. Create a new branch xfusioncorp_beta in repo that you cloned in /usr/src/kodekloudrepos.

e. There is a readme.md file in /tmp on storage server itself; copy that to repo, add/commit in the new branch you created, and finally push your branch to origin.

f. Also create master branch from your branch and remember you should not be able to push to master as per hook you have set up.

1. Login to Storage server and swithc to root user 

thor@jump_host ~$ ssh natasha@ststor01
			The authenticity of host 'ststor01 (172.16.238.15)' can't be established.
			ECDSA key fingerprint is SHA256:xI9jm4UPQLaCbWnPSdAYxPs5mhviGmA6fT/Xvk5TFAQ.
			ECDSA key fingerprint is MD5:fb:41:2b:5d:c6:59:f6:f5:09:9f:48:c4:73:fc:60:49.
			Are you sure you want to continue connecting (yes/no)? yes
			Warning: Permanently added 'ststor01,172.16.238.15' (ECDSA) to the list of known hosts.
			natasha@ststor01's password: 

[natasha@ststor01 ~]$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for natasha: 

2. Install git 

[root@ststor01 ~]# rpm -qa|grep git 

[root@ststor01 ~]# yum install -y git
		Loaded plugins: fastestmirror, ovl
		Determining fastest mirrors
		 * base: repos.forethought.net
		 * extras: mirror.us.oneandone.net
		 * updates: mirror.dal.nexril.net
		base                                                                                                                  | 3.6 kB  00:00:00     
		extras                                                                                                                | 2.9 kB  00:00:00     
		updates                                                                                                               | 2.9 kB  00:00:00     
		(1/4): base/7/x86_64/group_gz                                                                                         | 153 kB  00:00:00     
		(2/4): extras/7/x86_64/primary_db                                                                                     | 249 kB  00:00:00     
		(3/4): base/7/x86_64/primary_db                                                                                       | 6.1 MB  00:00:00     
		(4/4): updates/7/x86_64/primary_db                                                                                    |  19 MB  00:00:00     
		Resolving Dependencies
		--> Running transaction check
		---> Package git.x86_64 0:1.8.3.1-23.el7_8 will be installed
		--> Processing Dependency: perl-Git = 1.8.3.1-23.el7_8 for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl >= 5.008 for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: rsync for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(warnings) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(vars) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(strict) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(lib) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(Term::ReadKey) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(Git) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(Getopt::Long) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::stat) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::Temp) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::Spec) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::Path) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::Find) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::Copy) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(File::Basename) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(Exporter) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: perl(Error) for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: openssh-clients for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: less for package: git-1.8.3.1-23.el7_8.x86_64
		--> Processing Dependency: /usr/bin/perl for package: git-1.8.3.1-23.el7_8.x86_64
		--> Running transaction check
		---> Package less.x86_64 0:458-9.el7 will be installed
		--> Processing Dependency: groff-base for package: less-458-9.el7.x86_64
		---> Package openssh-clients.x86_64 0:7.4p1-22.el7_9 will be installed
		--> Processing Dependency: openssh = 7.4p1-22.el7_9 for package: openssh-clients-7.4p1-22.el7_9.x86_64
		--> Processing Dependency: libedit.so.0()(64bit) for package: openssh-clients-7.4p1-22.el7_9.x86_64
		---> Package perl.x86_64 4:5.16.3-299.el7_9 will be installed
		--> Processing Dependency: perl-libs = 4:5.16.3-299.el7_9 for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Socket) >= 1.3 for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Scalar::Util) >= 1.10 for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl-macros for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl-libs for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(threads::shared) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(threads) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(constant) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Time::Local) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Time::HiRes) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Storable) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Socket) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Scalar::Util) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Pod::Simple::XHTML) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Pod::Simple::Search) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Filter::Util::Call) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: perl(Carp) for package: 4:perl-5.16.3-299.el7_9.x86_64
		--> Processing Dependency: libperl.so()(64bit) for package: 4:perl-5.16.3-299.el7_9.x86_64
		---> Package perl-Error.noarch 1:0.17020-2.el7 will be installed
		---> Package perl-Exporter.noarch 0:5.68-3.el7 will be installed
		---> Package perl-File-Path.noarch 0:2.09-2.el7 will be installed
		---> Package perl-File-Temp.noarch 0:0.23.01-3.el7 will be installed
		---> Package perl-Getopt-Long.noarch 0:2.40-3.el7 will be installed
		--> Processing Dependency: perl(Pod::Usage) >= 1.14 for package: perl-Getopt-Long-2.40-3.el7.noarch
		--> Processing Dependency: perl(Text::ParseWords) for package: perl-Getopt-Long-2.40-3.el7.noarch
		---> Package perl-Git.noarch 0:1.8.3.1-23.el7_8 will be installed
		---> Package perl-PathTools.x86_64 0:3.40-5.el7 will be installed
		---> Package perl-TermReadKey.x86_64 0:2.30-20.el7 will be installed
		---> Package rsync.x86_64 0:3.1.2-12.el7_9 will be installed
		--> Running transaction check
		---> Package groff-base.x86_64 0:1.22.2-8.el7 will be installed
		---> Package libedit.x86_64 0:3.0-12.20121213cvs.el7 will be installed
		---> Package openssh.x86_64 0:7.4p1-21.el7 will be updated
		--> Processing Dependency: openssh = 7.4p1-21.el7 for package: openssh-server-7.4p1-21.el7.x86_64
		---> Package openssh.x86_64 0:7.4p1-22.el7_9 will be an update
		---> Package perl-Carp.noarch 0:1.26-244.el7 will be installed
		---> Package perl-Filter.x86_64 0:1.49-3.el7 will be installed
		---> Package perl-Pod-Simple.noarch 1:3.28-4.el7 will be installed
		--> Processing Dependency: perl(Pod::Escapes) >= 1.04 for package: 1:perl-Pod-Simple-3.28-4.el7.noarch
		--> Processing Dependency: perl(Encode) for package: 1:perl-Pod-Simple-3.28-4.el7.noarch
		---> Package perl-Pod-Usage.noarch 0:1.63-3.el7 will be installed
		--> Processing Dependency: perl(Pod::Text) >= 3.15 for package: perl-Pod-Usage-1.63-3.el7.noarch
		--> Processing Dependency: perl-Pod-Perldoc for package: perl-Pod-Usage-1.63-3.el7.noarch
		---> Package perl-Scalar-List-Utils.x86_64 0:1.27-248.el7 will be installed
		---> Package perl-Socket.x86_64 0:2.010-5.el7 will be installed
		---> Package perl-Storable.x86_64 0:2.45-3.el7 will be installed
		---> Package perl-Text-ParseWords.noarch 0:3.29-4.el7 will be installed
		---> Package perl-Time-HiRes.x86_64 4:1.9725-3.el7 will be installed
		---> Package perl-Time-Local.noarch 0:1.2300-2.el7 will be installed
		---> Package perl-constant.noarch 0:1.27-2.el7 will be installed
		---> Package perl-libs.x86_64 4:5.16.3-299.el7_9 will be installed
		---> Package perl-macros.x86_64 4:5.16.3-299.el7_9 will be installed
		---> Package perl-threads.x86_64 0:1.87-4.el7 will be installed
		---> Package perl-threads-shared.x86_64 0:1.43-6.el7 will be installed
		--> Running transaction check
		---> Package openssh-server.x86_64 0:7.4p1-21.el7 will be updated
		---> Package openssh-server.x86_64 0:7.4p1-22.el7_9 will be an update
		---> Package perl-Encode.x86_64 0:2.51-7.el7 will be installed
		---> Package perl-Pod-Escapes.noarch 1:1.04-299.el7_9 will be installed
		---> Package perl-Pod-Perldoc.noarch 0:3.20-4.el7 will be installed
		--> Processing Dependency: perl(parent) for package: perl-Pod-Perldoc-3.20-4.el7.noarch
		--> Processing Dependency: perl(HTTP::Tiny) for package: perl-Pod-Perldoc-3.20-4.el7.noarch
		---> Package perl-podlators.noarch 0:2.5.1-3.el7 will be installed
		--> Running transaction check
		---> Package perl-HTTP-Tiny.noarch 0:0.033-3.el7 will be installed
		---> Package perl-parent.noarch 1:0.225-244.el7 will be installed
		--> Finished Dependency Resolution

		Dependencies Resolved

		=============================================================================================================================================
		 Package                                  Arch                     Version                                   Repository                 Size
		=============================================================================================================================================
		Installing:
		 git                                      x86_64                   1.8.3.1-23.el7_8                          base                      4.4 M
		Installing for dependencies:
		 groff-base                               x86_64                   1.22.2-8.el7                              base                      942 k
		 less                                     x86_64                   458-9.el7                                 base                      120 k
		 libedit                                  x86_64                   3.0-12.20121213cvs.el7                    base                       92 k
		 openssh-clients                          x86_64                   7.4p1-22.el7_9                            updates                   655 k
		 perl                                     x86_64                   4:5.16.3-299.el7_9                        updates                   8.0 M
		 perl-Carp                                noarch                   1.26-244.el7                              base                       19 k
		 perl-Encode                              x86_64                   2.51-7.el7                                base                      1.5 M
		 perl-Error                               noarch                   1:0.17020-2.el7                           base                       32 k
		 perl-Exporter                            noarch                   5.68-3.el7                                base                       28 k
		 perl-File-Path                           noarch                   2.09-2.el7                                base                       26 k
		 perl-File-Temp                           noarch                   0.23.01-3.el7                             base                       56 k
		 perl-Filter                              x86_64                   1.49-3.el7                                base                       76 k
		 perl-Getopt-Long                         noarch                   2.40-3.el7                                base                       56 k
		 perl-Git                                 noarch                   1.8.3.1-23.el7_8                          base                       56 k
		 perl-HTTP-Tiny                           noarch                   0.033-3.el7                               base                       38 k
		 perl-PathTools                           x86_64                   3.40-5.el7                                base                       82 k
		 perl-Pod-Escapes                         noarch                   1:1.04-299.el7_9                          updates                    52 k
		 perl-Pod-Perldoc                         noarch                   3.20-4.el7                                base                       87 k
		 perl-Pod-Simple                          noarch                   1:3.28-4.el7                              base                      216 k
		 perl-Pod-Usage                           noarch                   1.63-3.el7                                base                       27 k
		 perl-Scalar-List-Utils                   x86_64                   1.27-248.el7                              base                       36 k
		 perl-Socket                              x86_64                   2.010-5.el7                               base                       49 k
		 perl-Storable                            x86_64                   2.45-3.el7                                base                       77 k
		 perl-TermReadKey                         x86_64                   2.30-20.el7                               base                       31 k
		 perl-Text-ParseWords                     noarch                   3.29-4.el7                                base                       14 k
		 perl-Time-HiRes                          x86_64                   4:1.9725-3.el7                            base                       45 k
		 perl-Time-Local                          noarch                   1.2300-2.el7                              base                       24 k
		 perl-constant                            noarch                   1.27-2.el7                                base                       19 k
		 perl-libs                                x86_64                   4:5.16.3-299.el7_9                        updates                   690 k
		 perl-macros                              x86_64                   4:5.16.3-299.el7_9                        updates                    44 k
		 perl-parent                              noarch                   1:0.225-244.el7                           base                       12 k
		 perl-podlators                           noarch                   2.5.1-3.el7                               base                      112 k
		 perl-threads                             x86_64                   1.87-4.el7                                base                       49 k
		 perl-threads-shared                      x86_64                   1.43-6.el7                                base                       39 k
		 rsync                                    x86_64                   3.1.2-12.el7_9                            updates                   408 k
		Updating for dependencies:
		 openssh                                  x86_64                   7.4p1-22.el7_9                            updates                   510 k
		 openssh-server                           x86_64                   7.4p1-22.el7_9                            updates                   459 k

		Transaction Summary
		=============================================================================================================================================
		Install  1 Package  (+35 Dependent packages)
		Upgrade             (  2 Dependent packages)

		Total download size: 19 M
		Downloading packages:
		Delta RPMs disabled because /usr/bin/applydeltarpm not installed.
		(1/38): groff-base-1.22.2-8.el7.x86_64.rpm                                                                            | 942 kB  00:00:00     
		(2/38): less-458-9.el7.x86_64.rpm                                                                                     | 120 kB  00:00:00     
		(3/38): git-1.8.3.1-23.el7_8.x86_64.rpm                                                                               | 4.4 MB  00:00:00     
		(4/38): libedit-3.0-12.20121213cvs.el7.x86_64.rpm                                                                     |  92 kB  00:00:00     
		(5/38): openssh-clients-7.4p1-22.el7_9.x86_64.rpm                                                                     | 655 kB  00:00:00     
		(6/38): openssh-7.4p1-22.el7_9.x86_64.rpm                                                                             | 510 kB  00:00:00     
		(7/38): openssh-server-7.4p1-22.el7_9.x86_64.rpm                                                                      | 459 kB  00:00:00     
		(8/38): perl-Carp-1.26-244.el7.noarch.rpm                                                                             |  19 kB  00:00:00     
		(9/38): perl-Error-0.17020-2.el7.noarch.rpm                                                                           |  32 kB  00:00:00     
		(10/38): perl-Exporter-5.68-3.el7.noarch.rpm                                                                          |  28 kB  00:00:00     
		(11/38): perl-File-Path-2.09-2.el7.noarch.rpm                                                                         |  26 kB  00:00:00     
		(12/38): perl-File-Temp-0.23.01-3.el7.noarch.rpm                                                                      |  56 kB  00:00:00     
		(13/38): perl-Filter-1.49-3.el7.x86_64.rpm                                                                            |  76 kB  00:00:00     
		(14/38): perl-Getopt-Long-2.40-3.el7.noarch.rpm                                                                       |  56 kB  00:00:00     
		(15/38): perl-Git-1.8.3.1-23.el7_8.noarch.rpm                                                                         |  56 kB  00:00:00     
		(16/38): perl-HTTP-Tiny-0.033-3.el7.noarch.rpm                                                                        |  38 kB  00:00:00     
		(17/38): perl-Pod-Escapes-1.04-299.el7_9.noarch.rpm                                                                   |  52 kB  00:00:00     
		(18/38): perl-Encode-2.51-7.el7.x86_64.rpm                                                                            | 1.5 MB  00:00:00     
		(19/38): perl-PathTools-3.40-5.el7.x86_64.rpm                                                                         |  82 kB  00:00:00     
		(20/38): perl-Pod-Perldoc-3.20-4.el7.noarch.rpm                                                                       |  87 kB  00:00:00     
		(21/38): perl-Pod-Simple-3.28-4.el7.noarch.rpm                                                                        | 216 kB  00:00:00     
		(22/38): perl-Pod-Usage-1.63-3.el7.noarch.rpm                                                                         |  27 kB  00:00:00     
		(23/38): perl-Scalar-List-Utils-1.27-248.el7.x86_64.rpm                                                               |  36 kB  00:00:00     
		(24/38): perl-Socket-2.010-5.el7.x86_64.rpm                                                                           |  49 kB  00:00:00     
		(25/38): perl-Storable-2.45-3.el7.x86_64.rpm                                                                          |  77 kB  00:00:00     
		(26/38): perl-TermReadKey-2.30-20.el7.x86_64.rpm                                                                      |  31 kB  00:00:00     
		(27/38): perl-Text-ParseWords-3.29-4.el7.noarch.rpm                                                                   |  14 kB  00:00:00     
		(28/38): perl-Time-HiRes-1.9725-3.el7.x86_64.rpm                                                                      |  45 kB  00:00:00     
		(29/38): perl-Time-Local-1.2300-2.el7.noarch.rpm                                                                      |  24 kB  00:00:00     
		(30/38): perl-constant-1.27-2.el7.noarch.rpm                                                                          |  19 kB  00:00:00     
		(31/38): perl-libs-5.16.3-299.el7_9.x86_64.rpm                                                                        | 690 kB  00:00:00     
		(32/38): perl-macros-5.16.3-299.el7_9.x86_64.rpm                                                                      |  44 kB  00:00:00     
		(33/38): perl-parent-0.225-244.el7.noarch.rpm                                                                         |  12 kB  00:00:00     
		(34/38): perl-threads-1.87-4.el7.x86_64.rpm                                                                           |  49 kB  00:00:00     
		(35/38): perl-threads-shared-1.43-6.el7.x86_64.rpm                                                                    |  39 kB  00:00:00     
		(36/38): rsync-3.1.2-12.el7_9.x86_64.rpm                                                                              | 408 kB  00:00:00     
		(37/38): perl-podlators-2.5.1-3.el7.noarch.rpm                                                                        | 112 kB  00:00:00     
		(38/38): perl-5.16.3-299.el7_9.x86_64.rpm                                                                             | 8.0 MB  00:00:00     
		---------------------------------------------------------------------------------------------------------------------------------------------
		Total                                                                                                         15 MB/s |  19 MB  00:00:01     
		Running transaction check
		Running transaction test
		Transaction test succeeded
		Running transaction
		  Installing : groff-base-1.22.2-8.el7.x86_64                                                                                           1/40 
		  Updating   : openssh-7.4p1-22.el7_9.x86_64                                                                                            2/40 
		  Installing : 1:perl-parent-0.225-244.el7.noarch                                                                                       3/40 
		  Installing : perl-HTTP-Tiny-0.033-3.el7.noarch                                                                                        4/40 
		  Installing : perl-podlators-2.5.1-3.el7.noarch                                                                                        5/40 
		  Installing : perl-Pod-Perldoc-3.20-4.el7.noarch                                                                                       6/40 
		  Installing : 1:perl-Pod-Escapes-1.04-299.el7_9.noarch                                                                                 7/40 
		  Installing : perl-Encode-2.51-7.el7.x86_64                                                                                            8/40 
		  Installing : perl-Text-ParseWords-3.29-4.el7.noarch                                                                                   9/40 
		  Installing : perl-Pod-Usage-1.63-3.el7.noarch                                                                                        10/40 
		  Installing : 4:perl-macros-5.16.3-299.el7_9.x86_64                                                                                   11/40 
		  Installing : 4:perl-Time-HiRes-1.9725-3.el7.x86_64                                                                                   12/40 
		  Installing : perl-Exporter-5.68-3.el7.noarch                                                                                         13/40 
		  Installing : perl-constant-1.27-2.el7.noarch                                                                                         14/40 
		  Installing : perl-Socket-2.010-5.el7.x86_64                                                                                          15/40 
		  Installing : perl-Time-Local-1.2300-2.el7.noarch                                                                                     16/40 
		  Installing : perl-Carp-1.26-244.el7.noarch                                                                                           17/40 
		  Installing : perl-Storable-2.45-3.el7.x86_64                                                                                         18/40 
		  Installing : perl-PathTools-3.40-5.el7.x86_64                                                                                        19/40 
		  Installing : perl-Scalar-List-Utils-1.27-248.el7.x86_64                                                                              20/40 
		  Installing : 1:perl-Pod-Simple-3.28-4.el7.noarch                                                                                     21/40 
		  Installing : perl-File-Temp-0.23.01-3.el7.noarch                                                                                     22/40 
		  Installing : perl-File-Path-2.09-2.el7.noarch                                                                                        23/40 
		  Installing : perl-threads-shared-1.43-6.el7.x86_64                                                                                   24/40 
		  Installing : perl-threads-1.87-4.el7.x86_64                                                                                          25/40 
		  Installing : perl-Filter-1.49-3.el7.x86_64                                                                                           26/40 
		  Installing : 4:perl-libs-5.16.3-299.el7_9.x86_64                                                                                     27/40 
		  Installing : perl-Getopt-Long-2.40-3.el7.noarch                                                                                      28/40 
		  Installing : 4:perl-5.16.3-299.el7_9.x86_64                                                                                          29/40 
		  Installing : 1:perl-Error-0.17020-2.el7.noarch                                                                                       30/40 
		  Installing : perl-TermReadKey-2.30-20.el7.x86_64                                                                                     31/40 
		  Installing : less-458-9.el7.x86_64                                                                                                   32/40 
		  Installing : libedit-3.0-12.20121213cvs.el7.x86_64                                                                                   33/40 
		  Installing : openssh-clients-7.4p1-22.el7_9.x86_64                                                                                   34/40 
		  Installing : rsync-3.1.2-12.el7_9.x86_64                                                                                             35/40 
		  Installing : perl-Git-1.8.3.1-23.el7_8.noarch                                                                                        36/40 
		  Installing : git-1.8.3.1-23.el7_8.x86_64                                                                                             37/40 
		  Updating   : openssh-server-7.4p1-22.el7_9.x86_64                                                                                    38/40 
		  Cleanup    : openssh-server-7.4p1-21.el7.x86_64                                                                                      39/40 
		  Cleanup    : openssh-7.4p1-21.el7.x86_64                                                                                             40/40 
		  Verifying  : perl-HTTP-Tiny-0.033-3.el7.noarch                                                                                        1/40 
		  Verifying  : rsync-3.1.2-12.el7_9.x86_64                                                                                              2/40 
		  Verifying  : perl-threads-shared-1.43-6.el7.x86_64                                                                                    3/40 
		  Verifying  : 4:perl-Time-HiRes-1.9725-3.el7.x86_64                                                                                    4/40 
		  Verifying  : openssh-clients-7.4p1-22.el7_9.x86_64                                                                                    5/40 
		  Verifying  : perl-Exporter-5.68-3.el7.noarch                                                                                          6/40 
		  Verifying  : perl-constant-1.27-2.el7.noarch                                                                                          7/40 
		  Verifying  : perl-PathTools-3.40-5.el7.x86_64                                                                                         8/40 
		  Verifying  : openssh-7.4p1-22.el7_9.x86_64                                                                                            9/40 
		  Verifying  : 4:perl-macros-5.16.3-299.el7_9.x86_64                                                                                   10/40 
		  Verifying  : git-1.8.3.1-23.el7_8.x86_64                                                                                             11/40 
		  Verifying  : 1:perl-parent-0.225-244.el7.noarch                                                                                      12/40 
		  Verifying  : perl-Socket-2.010-5.el7.x86_64                                                                                          13/40 
		  Verifying  : perl-TermReadKey-2.30-20.el7.x86_64                                                                                     14/40 
		  Verifying  : groff-base-1.22.2-8.el7.x86_64                                                                                          15/40 
		  Verifying  : perl-File-Temp-0.23.01-3.el7.noarch                                                                                     16/40 
		  Verifying  : 1:perl-Pod-Simple-3.28-4.el7.noarch                                                                                     17/40 
		  Verifying  : perl-Time-Local-1.2300-2.el7.noarch                                                                                     18/40 
		  Verifying  : 1:perl-Pod-Escapes-1.04-299.el7_9.noarch                                                                                19/40 
		  Verifying  : perl-Git-1.8.3.1-23.el7_8.noarch                                                                                        20/40 
		  Verifying  : perl-Carp-1.26-244.el7.noarch                                                                                           21/40 
		  Verifying  : 1:perl-Error-0.17020-2.el7.noarch                                                                                       22/40 
		  Verifying  : perl-Storable-2.45-3.el7.x86_64                                                                                         23/40 
		  Verifying  : perl-Scalar-List-Utils-1.27-248.el7.x86_64                                                                              24/40 
		  Verifying  : perl-Pod-Usage-1.63-3.el7.noarch                                                                                        25/40 
		  Verifying  : perl-Encode-2.51-7.el7.x86_64                                                                                           26/40 
		  Verifying  : perl-Pod-Perldoc-3.20-4.el7.noarch                                                                                      27/40 
		  Verifying  : perl-podlators-2.5.1-3.el7.noarch                                                                                       28/40 
		  Verifying  : 4:perl-5.16.3-299.el7_9.x86_64                                                                                          29/40 
		  Verifying  : perl-File-Path-2.09-2.el7.noarch                                                                                        30/40 
		  Verifying  : libedit-3.0-12.20121213cvs.el7.x86_64                                                                                   31/40 
		  Verifying  : perl-threads-1.87-4.el7.x86_64                                                                                          32/40 
		  Verifying  : openssh-server-7.4p1-22.el7_9.x86_64                                                                                    33/40 
		  Verifying  : perl-Filter-1.49-3.el7.x86_64                                                                                           34/40 
		  Verifying  : perl-Getopt-Long-2.40-3.el7.noarch                                                                                      35/40 
		  Verifying  : perl-Text-ParseWords-3.29-4.el7.noarch                                                                                  36/40 
		  Verifying  : 4:perl-libs-5.16.3-299.el7_9.x86_64                                                                                     37/40 
		  Verifying  : less-458-9.el7.x86_64                                                                                                   38/40 
		  Verifying  : openssh-7.4p1-21.el7.x86_64                                                                                             39/40 
		  Verifying  : openssh-server-7.4p1-21.el7.x86_64                                                                                      40/40 

		Installed:
		  git.x86_64 0:1.8.3.1-23.el7_8                                                                                                              

		Dependency Installed:
		  groff-base.x86_64 0:1.22.2-8.el7             less.x86_64 0:458-9.el7                      libedit.x86_64 0:3.0-12.20121213cvs.el7         
		  openssh-clients.x86_64 0:7.4p1-22.el7_9      perl.x86_64 4:5.16.3-299.el7_9               perl-Carp.noarch 0:1.26-244.el7                 
		  perl-Encode.x86_64 0:2.51-7.el7              perl-Error.noarch 1:0.17020-2.el7            perl-Exporter.noarch 0:5.68-3.el7               
		  perl-File-Path.noarch 0:2.09-2.el7           perl-File-Temp.noarch 0:0.23.01-3.el7        perl-Filter.x86_64 0:1.49-3.el7                 
		  perl-Getopt-Long.noarch 0:2.40-3.el7         perl-Git.noarch 0:1.8.3.1-23.el7_8           perl-HTTP-Tiny.noarch 0:0.033-3.el7             
		  perl-PathTools.x86_64 0:3.40-5.el7           perl-Pod-Escapes.noarch 1:1.04-299.el7_9     perl-Pod-Perldoc.noarch 0:3.20-4.el7            
		  perl-Pod-Simple.noarch 1:3.28-4.el7          perl-Pod-Usage.noarch 0:1.63-3.el7           perl-Scalar-List-Utils.x86_64 0:1.27-248.el7    
		  perl-Socket.x86_64 0:2.010-5.el7             perl-Storable.x86_64 0:2.45-3.el7            perl-TermReadKey.x86_64 0:2.30-20.el7           
		  perl-Text-ParseWords.noarch 0:3.29-4.el7     perl-Time-HiRes.x86_64 4:1.9725-3.el7        perl-Time-Local.noarch 0:1.2300-2.el7           
		  perl-constant.noarch 0:1.27-2.el7            perl-libs.x86_64 4:5.16.3-299.el7_9          perl-macros.x86_64 4:5.16.3-299.el7_9           
		  perl-parent.noarch 1:0.225-244.el7           perl-podlators.noarch 0:2.5.1-3.el7          perl-threads.x86_64 0:1.87-4.el7                
		  perl-threads-shared.x86_64 0:1.43-6.el7      rsync.x86_64 0:3.1.2-12.el7_9               

		Dependency Updated:
		  openssh.x86_64 0:7.4p1-22.el7_9                                   openssh-server.x86_64 0:7.4p1-22.el7_9                                  

		Complete!

[root@ststor01 ~]# rpm -qa|grep git 
		git-1.8.3.1-23.el7_8.x86_64

3. Add git global variables values 

[root@ststor01 ~]# git config --global --add user.name natasha

[root@ststor01 ~]# git config --global --add user.email natasha@stratos.xfusioncorp.com

[root@ststor01 ~]# git config --global -l
		user.name=natasha
		user.email=natasha@stratos.xfusioncorp.com

4. Create git bare repo

[root@ststor01 ~]# git init --bare /opt/beta.git
		Initialized empty Git repository in /opt/beta.git/

[root@ststor01 ~]# ls -ahl /opt/beta.git/
		total 40K
		drwxr-xr-x 7 root root 4.0K Dec 21 04:05 .
		drwxr-xr-x 1 root root 4.0K Dec 21 04:05 ..
		drwxr-xr-x 2 root root 4.0K Dec 21 04:05 branches
		-rw-r--r-- 1 root root   66 Dec 21 04:05 config
		-rw-r--r-- 1 root root   73 Dec 21 04:05 description
		-rw-r--r-- 1 root root   23 Dec 21 04:05 HEAD
		drwxr-xr-x 2 root root 4.0K Dec 21 04:05 hooks
		drwxr-xr-x 2 root root 4.0K Dec 21 04:05 info
		drwxr-xr-x 4 root root 4.0K Dec 21 04:05 objects
		drwxr-xr-x 4 root root 4.0K Dec 21 04:05 refs

5. copy the hook file from given location to hhoks folder under git repo 

[root@ststor01 ~]# cat /tmp/update 
		#!/bin/sh
		if [ "$1" == refs/heads/master ];
		then
		  echo "Manual pushing to this repo's master branch is restricted"
		  exit 1
		fi

[root@ststor01 ~]# cp /tmp/update /opt/beta.git/hooks/

[root@ststor01 ~]# cat /opt/beta.git/hooks/update
		#!/bin/sh
		if [ "$1" == refs/heads/master ];
		then
		  echo "Manual pushing to this repo's master branch is restricted"
		  exit 1
		fi

6. Clone the repo under given directory location

[root@ststor01 ~]# cd /usr/src/kodekloudrepos

[root@ststor01 kodekloudrepos]# git clone /opt/beta.git
		Cloning into 'beta'...
		warning: You appear to have cloned an empty repository.
		done.
[root@ststor01 kodekloudrepos]# ls -ahl
		total 12K
		drwxr-xr-x 3 root root 4.0K Dec 21 04:07 .
		drwxr-xr-x 1 root root 4.0K Dec 21 04:02 ..
		drwxr-xr-x 3 root root 4.0K Dec 21 04:07 beta

[root@ststor01 kodekloudrepos]# cd beta/

[root@ststor01 beta]# ls -ahl
		total 12K
		drwxr-xr-x 3 root root 4.0K Dec 21 04:07 .
		drwxr-xr-x 3 root root 4.0K Dec 21 04:07 ..
		drwxr-xr-x 7 root root 4.0K Dec 21 04:07 .git

7. Create new branch

[root@ststor01 beta]# git checkout -b xfusioncorp_beta
		Switched to a new branch 'xfusioncorp_beta'

8. Copy readme.md file

[root@ststor01 beta]# cp /tmp/readme.md ./

[root@ststor01 beta]# ls -ahl
		total 16K
		drwxr-xr-x 3 root root 4.0K Dec 21 04:08 .
		drwxr-xr-x 3 root root 4.0K Dec 21 04:07 ..
		drwxr-xr-x 7 root root 4.0K Dec 21 04:08 .git
		-rw-r--r-- 1 root root   33 Dec 21 04:08 readme.md

9. Commit and push the changes in ementioned branch

[root@ststor01 beta]# git status
		# On branch xfusioncorp_beta
		#
		# Initial commit
		#
		# Untracked files:
		#   (use "git add <file>..." to include in what will be committed)
		#
		#       readme.md
		nothing added to commit but untracked files present (use "git add" to track)

[root@ststor01 beta]# git add readme.md 

[root@ststor01 beta]# git commit -m "Added readme.md files"
		[xfusioncorp_beta (root-commit) 24839e3] Added readme.md files
		 1 file changed, 1 insertion(+)
		 create mode 100644 readme.md

[root@ststor01 beta]# git push origin xfusioncorp_beta
		Counting objects: 3, done.
		Writing objects: 100% (3/3), 256 bytes | 0 bytes/s, done.
		Total 3 (delta 0), reused 0 (delta 0)
		To /opt/beta.git
		 * [new branch]      xfusioncorp_beta -> xfusioncorp_beta

[root@ststor01 beta]# git status
		# On branch xfusioncorp_beta
		nothing to commit, working directory clean

10. Move to master branch and try to push changes (push should fail as per added hook)

[root@ststor01 beta]# git checkout -b master
		Switched to a new branch 'master'

[root@ststor01 beta]# git branch -a
		* master
		  xfusioncorp_beta
		  remotes/origin/xfusioncorp_beta

[root@ststor01 beta]# git push origin master
		Total 0 (delta 0), reused 0 (delta 0)
		remote: Manual pushing to this repo's master branch is restricted
		remote: error: hook declined to update refs/heads/master
		To /opt/beta.git
		 ! [remote rejected] master -> master (hook declined)
		error: failed to push some refs to '/opt/beta.git'
[root@ststor01 beta]#

--------------------------------------------------------------------------------------------------------------------------------
Task 119: 23/Dec/2022

Puppet Install a Package


Some new packages need to be installed on app server 3 in Stratos Datacenter. The Nautilus DevOps team has decided to install the same using Puppet. Since jump host is already configured to run as Puppet master server and all app servers are already configured to work as the puppet agent nodes, we need to create required manifests on the Puppet master server so that the same can be applied on all Puppet agent nodes. Please find more details about the task below.

Create a Puppet programming file apps.pp under /etc/puppetlabs/code/environments/production/manifests directory on master node i.e Jump Server and using puppet package resource perform the tasks given below.

    Install package vsftpd through Puppet package resource only on App server 3i.epuppet agent node 3`.

Notes: :- Please make sure to run the puppet agent test using sudo on agent nodes, otherwise you can face certificate issues. In that case you will have to clean the certificates first and then you will be able to run the puppet agent test.

:- Before clicking on the Check button please make sure to verify puppet server and puppet agent services are up and running on the respective servers, also please make sure to run puppet agent test to apply/test the changes manually first.

:- Please note that once lab is loaded, the puppet server service should start automatically on puppet master server, however it can take upto 2-3 minutes to start.


1. Switch to root user on puppet master / jump host server

thor@jump_host ~$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for thor: 

2. Go to Puppet folder and create mentioned puppet file as per requirements 

root@jump_host ~# cd /etc/puppetlabs/code/environments/production/manifests

root@jump_host /etc/puppetlabs/code/environments/production/manifests# ls -ahl
		total 8.0K
		drwxr-xr-x 1 puppet puppet 4.0K Jul 13  2021 .
		drwxr-xr-x 1 puppet puppet 4.0K Aug  9  2021 ..

root@jump_host /etc/puppetlabs/code/environments/production/manifests# vi apps.pp

root@jump_host /etc/puppetlabs/code/environments/production/manifests# cat apps.pp 
		class vsftpd_installer {
		    package {'vsftpd':
		        ensure => installed
		    }
		}

		node 'stapp03.stratos.xfusioncorp.com' {
		  include vsftpd_installer
		}

3. Validate the puppet file created  

root@jump_host /etc/puppetlabs/code/environments/production/manifests# puppet parser validate apps.pp 

4. Login to App Server 3 and switch to root 

root@jump_host /etc/puppetlabs/code/environments/production/manifests# ssh banner@stapp03
		The authenticity of host 'stapp03 (172.16.238.12)' can't be established.
		ECDSA key fingerprint is SHA256:FH25URE1iUQRs9xBcOo91DcHsaLqxniAcWvLCFxadwk.
		ECDSA key fingerprint is MD5:6d:62:95:a8:cb:d3:73:41:13:f0:99:1b:4a:7b:ec:64.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'stapp03,172.16.238.12' (ECDSA) to the list of known hosts.
		banner@stapp03's password: 

[banner@stapp03 ~]$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for banner: 

[root@stapp03 ~]# rpm -qa |grep vsftpd

5. Run Puppet agent to pull the configuration from puppet server

[root@stapp03 ~]# puppet agent -tv
		Info: Using configured environment 'production'
		Info: Retrieving pluginfacts
		Info: Retrieving plugin
		Info: Retrieving locales
		Info: Caching catalog for stapp03.stratos.xfusioncorp.com
		Info: Applying configuration version '1671769126'
		Notice: Applied catalog in 0.12 seconds

6. Validate the task by checking installed package

[root@stapp03 ~]# rpm -qa |grep vsftpd
		vsftpd-3.0.2-29.el7_9.x86_64

[root@stapp03 ~]# 

--------------------------------------------------------------------------------------------------------------------------------
Task 120: 24/Dec/2022

Fix Issue with VolumeMounts in Kubernetes

We deployed a Nginx and PHPFPM based setup on Kubernetes cluster last week and it had been working fine. This morning one of the team members made a change somewhere which caused some issues, and it stopped working. Please look into the issue and fix it:

The pod name is nginx-phpfpm and configmap name is nginx-config. Figure out the issue and fix the same.

Once issue is fixed, copy /home/thor/index.php file from jump host into nginx-container under nginx document root and you should be able to access the website using Website button on top bar.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

1. Check existing running pods

thor@jump_host ~$ kubectl get pods
		NAME           READY   STATUS    RESTARTS   AGE
		nginx-phpfpm   2/2     Running   0          87s

2. Check existing running configmap for shared volume map

thor@jump_host ~$ kubectl get configmap
		NAME               DATA   AGE
		kube-root-ca.crt   1      26m
		nginx-config       1      96s

thor@jump_host ~$ kubectl describe configmap nginx-config
		Name:         nginx-config
		Namespace:    default
		Labels:       <none>
		Annotations:  <none>

		Data
		====
		nginx.conf:
		----
		events {
		}
		http {
		  server {
		    listen 8099 default_server;
		    listen [::]:8099 default_server;

		    # Set nginx to serve files from the shared volume!
		    root /var/www/html;                                                               <--------------------------------------
		    index  index.html index.htm index.php;
		    server_name _;
		    location / {
		      try_files $uri $uri/ =404;
		    }
		    location ~ \.php$ {
		      include fastcgi_params;
		      fastcgi_param REQUEST_METHOD $request_method;
		      fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
		      fastcgi_pass 127.0.0.1:9000;
		    }
		  }
		}

		Events:  <none>

3. Get running pod configuration into a YAML file and look for mountPath value

thor@jump_host ~$ kubectl get pod nginx-phpfpm -o yaml  > /tmp/nginx.yaml

thor@jump_host ~$ cat /tmp/nginx.yaml |grep mountPath
      {"apiVersion":"v1","kind":"Pod","metadata":{"annotations":{},"labels":{"app":"php-app"},"name":"nginx-phpfpm","namespace":"default"},"spec":{"containers":[{"image":"php:7.2-fpm","name":"php-fpm-container","volumeMounts":[{"mountPath":"/var/www/html","name":"shared-files"}]},{"image":"nginx:latest","name":"nginx-container","volumeMounts":[{"mountPath":"/usr/share/nginx/html","name":"shared-files"},{"mountPath":"/etc/nginx/nginx.conf","name":"nginx-config-volume","subPath":"nginx.conf"}]}],"volumes":[{"emptyDir":{},"name":"shared-files"},{"configMap":{"name":"nginx-config"},"name":"nginx-config-volume"}]}}
              k:{"mountPath":"/etc/nginx/nginx.conf"}:
                f:mountPath: {}
              k:{"mountPath":"/usr/share/nginx/html"}:
                f:mountPath: {}
              k:{"mountPath":"/var/www/html"}:
                f:mountPath: {}
    - mountPath: /var/www/html
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
    - mountPath: /usr/share/nginx/html
    - mountPath: /etc/nginx/nginx.conf
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount

4. Edit the nginx.yaml file and change mountPath value from ‘/usr/share/nginx/html’ to ‘/var/www/html’ 

thor@jump_host ~$ vi /tmp/nginx.yaml 

thor@jump_host ~$ cat /tmp/nginx.yaml |grep mountPath
      {"apiVersion":"v1","kind":"Pod","metadata":{"annotations":{},"labels":{"app":"php-app"},"name":"nginx-phpfpm","namespace":"default"},"spec":{"containers":[{"image":"php:7.2-fpm","name":"php-fpm-container","volumeMounts":[{"mountPath":"/var/www/html","name":"shared-files"}]},{"image":"nginx:latest","name":"nginx-container","volumeMounts":[{"mountPath":"/var/www/html","name":"shared-files"},{"mountPath":"/etc/nginx/nginx.conf","name":"nginx-config-volume","subPath":"nginx.conf"}]}],"volumes":[{"emptyDir":{},"name":"shared-files"},{"configMap":{"name":"nginx-config"},"name":"nginx-config-volume"}]}}
              k:{"mountPath":"/etc/nginx/nginx.conf"}:
                f:mountPath: {}
              k:{"mountPath":"/var/www/html"}:
                f:mountPath: {}
              k:{"mountPath":"/var/www/html"}:
                f:mountPath: {}
    - mountPath: /var/www/html
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
    - mountPath: /var/www/html
    - mountPath: /etc/nginx/nginx.conf
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount

5. Replace the running pods with new edited/changed config

thor@jump_host ~$ kubectl replace -f /tmp/nginx.yaml --force
		pod "nginx-phpfpm" deleted
		pod/nginx-phpfpm replaced

thor@jump_host ~$ kubectl get pods
		NAME           READY   STATUS    RESTARTS   AGE
		nginx-phpfpm   2/2     Running   0          10s

6. Copy the mentioned index.php file into pods

thor@jump_host ~$ kubectl cp  /home/thor/index.php  nginx-phpfpm:/var/www/html -c nginx-container

7. Validate the the task by curl on running nginx instance / check Website as mentioned in task

thor@jump_host ~$ kubectl exec -it nginx-phpfpm -c nginx-container  -- curl -I  http://localhost:8099
		HTTP/1.1 200 OK
		Server: nginx/1.23.3
		Date: Sat, 24 Dec 2022 13:34:46 GMT
		Content-Type: text/html; charset=UTF-8
		Connection: keep-alive
		X-Powered-By: PHP/7.2.34

thor@jump_host ~$

--------------------------------------------------------------------------------------------------------------------------------
Task 121: 25/Dec/2022

Create Pods in Kubernetes Cluster

The Nautilus DevOps team has started practicing some pods and services deployment on Kubernetes platform as they are planning to migrate most of their applications on Kubernetes platform. Recently one of the team members has been assigned a task to create a pod as per details mentioned below:

    Create a pod named pod-httpd using httpd image with latest tag only and remember to mention the tag i.e httpd:latest.

    Labels app should be set to httpd_app, also container should be named as httpd-container.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.


1. Check kubectl utility config

thor@jump_host ~$ kubectl get all
		NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
		service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   25m

thor@jump_host ~$ kubectl get services
		NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
		kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   25m

thor@jump_host ~$ kubectl get namespaces
		NAME                 STATUS   AGE
		default              Active   25m
		kube-node-lease      Active   25m
		kube-public          Active   25m
		kube-system          Active   25m
		local-path-storage   Active   25m

thor@jump_host ~$ kubectl get pods
	No resources found in default namespace.

2. Create YAML file as per requirements

thor@jump_host ~$ vi /tmp/pods.yml

thor@jump_host ~$ cat /tmp/pods.yml
		apiVersion: v1
		kind: Pod
		metadata:
		    name: pod-httpd
		    labels:
		      app: httpd_app
		spec:
		    containers:
		    - name: httpd-container
		      image: httpd:latest

3. Create pods by using the yaml file created

thor@jump_host ~$ kubectl create -f /tmp/pods.yml 
		pod/pod-httpd created

4. Check the status and wait for pods to get running

thor@jump_host ~$ kubectl get all
		NAME            READY   STATUS              RESTARTS   AGE
		pod/pod-httpd   0/1     ContainerCreating   0          8s

		NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
		service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   27m

thor@jump_host ~$ kubectl get pods
		NAME        READY   STATUS    RESTARTS   AGE
		pod-httpd   1/1     Running   0          15s

thor@jump_host ~$ kubectl get pods -o wide
		NAME        READY   STATUS    RESTARTS   AGE   IP           NODE                      NOMINATED NODE   READINESS GATES
		pod-httpd   1/1     Running   0          22s   10.244.0.5   kodekloud-control-plane   <none>           <none>
thor@jump_host ~$

--------------------------------------------------------------------------------------------------------------------------------
Task 122: 26/Dec/2022

Git Repository Update

The Nautilus development team started with new project development. They have created different Git repositories to manage respective project's source code. One of the repo /opt/blog.git was created recently. The team has given us a sample index.html file that is currently present on jump host under /tmp. The repository has been cloned at /usr/src/kodekloudrepos on storage server in Stratos DC.

Copy sample index.html file from jump host to storage server under cloned repository at /usr/src/kodekloudrepos, add/commit the file and push to master branch.


1. Copy/SCP the index.html file from jump host to storage server

thor@jump_host ~$ sudo scp /tmp/index.html natasha@ststor01:/tmp/

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for thor: 

		The authenticity of host 'ststor01 (172.16.238.15)' can't be established.
		ECDSA key fingerprint is SHA256:NTUGOk5/tEHb4NSstyQtMlCVIii+1RuDwMw14+CgndM.
		ECDSA key fingerprint is MD5:a7:27:52:36:c5:db:6f:16:47:31:ed:2f:10:6c:b7:70.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'ststor01,172.16.238.15' (ECDSA) to the list of known hosts.
		natasha@ststor01's password: 
		
		index.html                                                                                                 100%   27    82.1KB/s   00:00    

2. Login/SSH to storage server and switch to root user 

thor@jump_host ~$ ssh natasha@ststor01
		The authenticity of host 'ststor01 (172.16.238.15)' can't be established.
		ECDSA key fingerprint is SHA256:NTUGOk5/tEHb4NSstyQtMlCVIii+1RuDwMw14+CgndM.
		ECDSA key fingerprint is MD5:a7:27:52:36:c5:db:6f:16:47:31:ed:2f:10:6c:b7:70.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'ststor01,172.16.238.15' (ECDSA) to the list of known hosts.
		natasha@ststor01's password: 

[natasha@ststor01 ~]$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for natasha: 

3. Move to local git repo and copy the index.html file 

[root@ststor01 ~]# cd /usr/src/kodekloudrepos

[root@ststor01 kodekloudrepos]# ls -ahl
		total 12K
		drwxr-xr-x 3 root root 4.0K Dec 26 16:22 .
		drwxr-xr-x 1 root root 4.0K Dec 26 16:22 ..
		drwxr-xr-x 3 root root 4.0K Dec 26 16:22 blog

[root@ststor01 kodekloudrepos]# cd blog/

[root@ststor01 blog]# ls -ahl
		total 20K
		drwxr-xr-x 3 root root 4.0K Dec 26 16:22 .
		drwxr-xr-x 3 root root 4.0K Dec 26 16:22 ..
		drwxr-xr-x 8 root root 4.0K Dec 26 16:22 .git
		-rw-r--r-- 1 root root   34 Dec 26 16:22 info.txt
		-rw-r--r-- 1 root root   26 Dec 26 16:22 welcome.txt

[root@ststor01 blog]# git status
		# On branch master
		nothing to commit, working directory clean

[root@ststor01 blog]# cp /tmp/index.html ./

[root@ststor01 blog]# ls -ahl
		total 24K
		drwxr-xr-x 3 root root 4.0K Dec 26 16:25 .
		drwxr-xr-x 3 root root 4.0K Dec 26 16:22 ..
		drwxr-xr-x 8 root root 4.0K Dec 26 16:25 .git
		-rw-r--r-- 1 root root   27 Dec 26 16:25 index.html
		-rw-r--r-- 1 root root   34 Dec 26 16:22 info.txt
		-rw-r--r-- 1 root root   26 Dec 26 16:22 welcome.txt

4. Add and commit the index.html file

[root@ststor01 blog]# git status
		# On branch master
		# Untracked files:
		#   (use "git add <file>..." to include in what will be committed)
		#
		#       index.html
		nothing added to commit but untracked files present (use "git add" to track)

[root@ststor01 blog]# git add index.html 

[root@ststor01 blog]# git commit -m "Added index.html"
		[master 0b92f5c] Added index.html
		 1 file changed, 1 insertion(+)
		 create mode 100644 index.html

5. Push the master branch to origin

[root@ststor01 blog]# git push -u origin master
		Counting objects: 4, done.
		Delta compression using up to 36 threads.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (3/3), 332 bytes | 0 bytes/s, done.
		Total 3 (delta 0), reused 0 (delta 0)
		To /opt/blog.git
		   f9b5024..0b92f5c  master -> master
		Branch master set up to track remote branch master from origin.

[root@ststor01 blog]# git status
		# On branch master
		nothing to commit, working directory clean

[root@ststor01 blog]# git remote -v
		origin  /opt/blog.git (fetch)
		origin  /opt/blog.git (push)

[root@ststor01 blog]#

--------------------------------------------------------------------------------------------------------------------------------
Task 123: 28/Dec/2022

Ansible Unarchive Module

One of the DevOps team members has created an ZIP archive on jump host in Stratos DC that needs to be extracted and copied over to all app servers in Stratos DC itself. Because this is a routine task, the Nautilus DevOps team has suggested automating it. We can use Ansible since we have been using it for other automation tasks. Below you can find more details about the task:

We have an inventory file under /home/thor/ansible directory on jump host, which should have all the app servers added already.

There is a ZIP archive /usr/src/devops/xfusion.zip on jump host.

Create a playbook.yml under /home/thor/ansible/ directory on jump host itself to perform the below given tasks.

    Unzip /usr/src/devops/xfusion.zip archive in /opt/devops/ location on all app servers.

    Make sure the extracted data must has the respective sudo user as their user and group owner, i.e tony for app server 1, steve for app server 2, banner for app server 3.

    The extracted data permissions must be 0755

Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure playbook works this way, without passing any extra arguments.


1. Check current config and inventory file on jump host

thor@jump_host ~$ cd /home/thor/ansible/

thor@jump_host ~/ansible$ ls -ahl
		total 16K
		drwxr-xr-x 2 thor thor 4.0K Dec 28 03:24 .
		drwxr----- 1 thor thor 4.0K Dec 28 03:24 ..
		-rw-r--r-- 1 thor thor   36 Dec 28 03:24 ansible.cfg
		-rw-r--r-- 1 thor thor  237 Dec 28 03:24 inventory

thor@jump_host ~/ansible$ cat inventory 
		stapp01 ansible_host=172.16.238.10 ansible_ssh_pass=Ir0nM@n ansible_user=tony
		stapp02 ansible_host=172.16.238.11 ansible_ssh_pass=Am3ric@ ansible_user=steve
		stapp03 ansible_host=172.16.238.12 ansible_ssh_pass=BigGr33n ansible_user=banner

thor@jump_host ~/ansible$ cat ansible.cfg 
		[defaults]
		host_key_checking = False

2. Check existing files on app servers as well as jump host and verify inventory file 

thor@jump_host ~/ansible$ ansible -i inventory all -a "ls -ahl /opt/devops/"
		stapp02 | CHANGED | rc=0 >>
		total 8.0K
		drwxr-xr-x 2 root root 4.0K Dec 28 03:26 .
		drwxr-xr-x 1 root root 4.0K Dec 28 03:26 ..
		stapp03 | CHANGED | rc=0 >>
		total 8.0K
		drwxr-xr-x 2 root root 4.0K Dec 28 03:26 .
		drwxr-xr-x 1 root root 4.0K Dec 28 03:26 ..
		stapp01 | CHANGED | rc=0 >>
		total 8.0K
		drwxr-xr-x 2 root root 4.0K Dec 28 03:26 .
		drwxr-xr-x 1 root root 4.0K Dec 28 03:26 ..

thor@jump_host ~/ansible$ ls -ahl /usr/src/devops
		total 12K
		drwxr-xr-x 2 root root 4.0K Dec 28 03:26 .
		drwxr-xr-x 1 root root 4.0K Dec 28 03:26 ..
		-rw-r--r-- 1 root root  367 Dec 28 03:26 xfusion.zip

3. Create playbook as per requirements 

thor@jump_host ~/ansible$ vi playbook.yml

thor@jump_host ~/ansible$ cat playbook.yml
		- name: Extract archive
		  hosts: stapp01, stapp02, stapp03
		  become: yes
		  tasks:
		    - name: Extract the archive and set the owner/permissions
		      unarchive:
		        src: /usr/src/devops/xfusion.zip
		        dest: /opt/devops/
		        owner: "{{ ansible_user }}"
		        group: "{{ ansible_user }}"
		        mode: "0755"

4. Run the playbook
thor@jump_host ~/ansible$ ansible-playbook -i inventory playbook.yml 

		PLAY [Extract archive] **********************************************************************************************************************

		TASK [Gathering Facts] **********************************************************************************************************************
		ok: [stapp02]
		ok: [stapp03]
		ok: [stapp01]

		TASK [Extract the archive and set the owner/permissions] ************************************************************************************
		changed: [stapp02]
		changed: [stapp01]
		changed: [stapp03]

		PLAY RECAP **********************************************************************************************************************************
		stapp01                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
		stapp02                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
		stapp03                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

5. Validate the task by checking files on app servers

thor@jump_host ~/ansible$ ansible -i inventory all -a "ls -ahl /opt/devops/"
		stapp03 | CHANGED | rc=0 >>
		total 12K
		drwxr-xr-x 3 root   root   4.0K Dec 28 03:31 .
		drwxr-xr-x 1 root   root   4.0K Dec 28 03:26 ..
		drwxr-xr-x 2 banner banner 4.0K Dec 28 03:26 unarchive
		stapp01 | CHANGED | rc=0 >>
		total 12K
		drwxr-xr-x 3 root root 4.0K Dec 28 03:31 .
		drwxr-xr-x 1 root root 4.0K Dec 28 03:26 ..
		drwxr-xr-x 2 tony tony 4.0K Dec 28 03:26 unarchive
		stapp02 | CHANGED | rc=0 >>
		total 12K
		drwxr-xr-x 3 root  root  4.0K Dec 28 03:31 .
		drwxr-xr-x 1 root  root  4.0K Dec 28 03:26 ..
		drwxr-xr-x 2 steve steve 4.0K Dec 28 03:26 unarchive

--------------------------------------------------------------------------------------------------------------------------------
Task 124: 29/Dec/2022

Update an Existing Deployment in Kubernetes

There is an application deployed on Kubernetes cluster. Recently, the Nautilus application development team developed a new version of the application that needs to be deployed now. As per new updates some new changes need to be made in this existing setup. So update the deployment and service as per details mentioned below:

We already have a deployment named nginx-deployment and service named nginx-service. Some changes need to be made in this deployment and service, make sure not to delete the deployment and service.

1.) Change the service nodeport from 30008 to 32165

2.) Change the replicas count from 1 to 5

3.) Change the image from nginx:1.17 to nginx:latest

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

1. Check current runnning / existing setup

thor@jump_host ~$ kubectl get all
		NAME                                    READY   STATUS    RESTARTS   AGE
		pod/nginx-deployment-576b8f48fb-9prm8   1/1     Running   0          24s

		NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
		service/kubernetes      ClusterIP   10.96.0.1       <none>        443/TCP        92m
		service/nginx-service   NodePort    10.96.249.254   <none>        80:30008/TCP   25s

		NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
		deployment.apps/nginx-deployment   1/1     1            1           25s

		NAME                                          DESIRED   CURRENT   READY   AGE
		replicaset.apps/nginx-deployment-576b8f48fb   1         1         1       25s

thor@jump_host ~$ kubectl get services 
		NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
		kubernetes      ClusterIP   10.96.0.1       <none>        443/TCP        92m
		nginx-service   NodePort    10.96.249.254   <none>        80:30008/TCP   39s

thor@jump_host ~$ kubectl get deployment
		NAME               READY   UP-TO-DATE   AVAILABLE   AGE
		nginx-deployment   1/1     1            1           49s

2. Edit the service to change the node port 

thor@jump_host ~$ kubectl edit service nginx-service
		service/nginx-service edited

thor@jump_host ~$ kubectl get services 
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
		kubernetes      ClusterIP   10.96.0.1       <none>        443/TCP        94m
		nginx-service   NodePort    10.96.249.254   <none>        80:32165/TCP   2m21s

3. Edit the deployment to change number of replicas and container image 

thor@jump_host ~$ kubectl get deployment
		NAME               READY   UP-TO-DATE   AVAILABLE   AGE
		nginx-deployment   1/1     1            1           2m33s

thor@jump_host ~$ kubectl edit deployment nginx-deployment
		deployment.apps/nginx-deployment edited

4. Wait for all pods to get to running status and deployment get available

thor@jump_host ~$ kubectl get deployment 
		NAME               READY   UP-TO-DATE   AVAILABLE   AGE
		nginx-deployment   4/5     3            4           4m1s

thor@jump_host ~$ kubectl get pods
		NAME                                READY   STATUS              RESTARTS   AGE
		nginx-deployment-576b8f48fb-2m4j2   0/1     Terminating         0          24s
		nginx-deployment-576b8f48fb-9prm8   1/1     Running             0          4m11s
		nginx-deployment-576b8f48fb-f8gl7   1/1     Terminating         0          24s
		nginx-deployment-576b8f48fb-nbxdz   1/1     Terminating         0          24s
		nginx-deployment-c7ff8fb84-2btjm    0/1     ContainerCreating   0          6s
		nginx-deployment-c7ff8fb84-62wqb    1/1     Running             0          24s
		nginx-deployment-c7ff8fb84-n4t6s    0/1     ContainerCreating   0          5s
		nginx-deployment-c7ff8fb84-vwmps    1/1     Running             0          24s
		nginx-deployment-c7ff8fb84-zjbjf    1/1     Running             0          23s

thor@jump_host ~$ kubectl get pods
		NAME                                READY   STATUS        RESTARTS   AGE
		nginx-deployment-576b8f48fb-9prm8   0/1     Terminating   0          4m26s
		nginx-deployment-c7ff8fb84-2btjm    1/1     Running       0          21s
		nginx-deployment-c7ff8fb84-62wqb    1/1     Running       0          39s
		nginx-deployment-c7ff8fb84-n4t6s    1/1     Running       0          20s
		nginx-deployment-c7ff8fb84-vwmps    1/1     Running       0          39s
		nginx-deployment-c7ff8fb84-zjbjf    1/1     Running       0          38s

thor@jump_host ~$ kubectl get pods
		NAME                               READY   STATUS    RESTARTS   AGE
		nginx-deployment-c7ff8fb84-2btjm   1/1     Running   0          27s
		nginx-deployment-c7ff8fb84-62wqb   1/1     Running   0          45s
		nginx-deployment-c7ff8fb84-n4t6s   1/1     Running   0          26s
		nginx-deployment-c7ff8fb84-vwmps   1/1     Running   0          45s
		nginx-deployment-c7ff8fb84-zjbjf   1/1     Running   0          44s

thor@jump_host ~$ kubectl get deployment 
		NAME               READY   UP-TO-DATE   AVAILABLE   AGE
		nginx-deployment   5/5     5            5           4m37s
thor@jump_host ~$

--------------------------------------------------------------------------------------------------------------------------------
Task 125: 30/Dec/2022

Puppet Setup File Permissions

The Nautilus DevOps team has put some data on all app servers in Stratos DC. jump host is configured as Puppet master server, and all app servers are already been configured as Puppet agent nodes. The team needs to update the content of some of the exiting files, as well as need to update their permissions etc. Please find below more details about the task:

Create a Puppet programming file cluster.pp under /etc/puppetlabs/code/environments/production/manifests directory on the master node i.e Jump Server. Using puppet file resource, perform the below mentioned tasks.

    A file named beta.txt already exists under /opt/devops directory on App Server 2.

    Add content Welcome to xFusionCorp Industries! in beta.txt file on App Server 2.

    Set its permissions to 0777.

Notes: :- Please make sure to run the puppet agent test using sudo on agent nodes, otherwise you can face certificate issues. In that case you will have to clean the certificates first and then you will be able to run the puppet agent test.

:- Before clicking on the Check button please make sure to verify puppet server and puppet agent services are up and running on the respective servers, also please make sure to run puppet agent test to apply/test the changes manually first.

:- Please note that once lab is loaded, the puppet server service should start automatically on puppet master server, however it can take upto 2-3 minutes to start.


1. Switch to root on jump host/ puppet master server

thor@jump_host ~$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for thor: 

2. Create puppet programming file as per requirements

root@jump_host ~# cd /etc/puppetlabs/code/environments/production/manifests

root@jump_host /etc/puppetlabs/code/environments/production/manifests# ls -ahl
		total 8.0K
		drwxr-xr-x 1 puppet puppet 4.0K Jul 13  2021 .
		drwxr-xr-x 1 puppet puppet 4.0K Aug  9  2021 ..

root@jump_host /etc/puppetlabs/code/environments/production/manifests# vi cluster.pp

root@jump_host /etc/puppetlabs/code/environments/production/manifests# cat cluster.pp 
		class file_updater {
		 # Update beta.txt under /opt/devops
		 file { '/opt/devops/beta.txt':
		   ensure => 'present',
		   content => 'Welcome to xFusionCorp Industries!',
		   mode => '0777',
		 }
		}

		node 'stapp02.stratos.xfusioncorp.com' {
		 include file_updater
		}

3. Parse the cluster.pp file created

root@jump_host /etc/puppetlabs/code/environments/production/manifests# puppet parser validate cluster.pp 

4. Login to app server 2 and switch to root user

root@jump_host /etc/puppetlabs/code/environments/production/manifests# ssh steve@stapp02
		The authenticity of host 'stapp02 (172.16.238.11)' can't be established.
		ECDSA key fingerprint is SHA256:y+jxiyrcebtFq5E1tWUCPBvqr4JIpQzsMbyOE6GTxE4.
		ECDSA key fingerprint is MD5:b4:dd:d5:ca:7d:77:7c:be:6d:ec:33:e6:15:73:9a:0c.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'stapp02,172.16.238.11' (ECDSA) to the list of known hosts.
		steve@stapp02's password: 

[steve@stapp02 ~]$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for steve: 

5. Run puppet agent to pull the changes  

[root@stapp02 ~]# puppet agent -tv
		Info: Using configured environment 'production'
		Info: Retrieving pluginfacts
		Info: Retrieving plugin
		Info: Retrieving locales
		Info: Caching catalog for stapp02.stratos.xfusioncorp.com
		Info: Applying configuration version '1672396253'
		Notice: Applied catalog in 0.05 seconds

6. Validate the task by checing file content and permissions

[root@stapp02 ~]# cat /opt/devops/beta.txt 
		Welcome to xFusionCorp Industries!

[root@stapp02 ~]# ls -ahl /opt/devops/
		total 12K
		drwxr-xr-x 2 root root 4.0K Dec 30 10:30 .
		drwxr-xr-x 1 root root 4.0K Dec 30 10:26 ..
		-rwxrwxrwx 1 root root   34 Dec 30 10:30 beta.txt
[root@stapp02 ~]#

--------------------------------------------------------------------------------------------------------------------------------
Task 126: 31/Dec/2022

Fix Python App Deployed on Kubernetes Cluster


One of the DevOps engineers was trying to deploy a python app on Kubernetes cluster. Unfortunately, due to some mis-configuration, the application is not coming up. Please take a look into it and fix the issues. Application should be accessible on the specified nodePort.

The deployment name is python-deployment-nautilus, its using poroko/flask-demo-appimage. The deployment and service of this app is already deployed.

    nodePort should be 32345 and targetPort should be python flask app's default port.

Note: The kubectl on jump_host has been configured to work with the kubernetes cluster.


1. Get current running status and configuration

thor@jump_host ~$ kubectl get all
		NAME                                              READY   STATUS             RESTARTS   AGE
		pod/python-deployment-nautilus-5dbcd5f8b4-dflt7   0/1     ImagePullBackOff   0          28s

		NAME                              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
		service/kubernetes                ClusterIP   10.96.0.1       <none>        443/TCP          71m
		service/python-service-nautilus   NodePort    10.96.200.222   <none>        8080:32345/TCP   28s

		NAME                                         READY   UP-TO-DATE   AVAILABLE   AGE
		deployment.apps/python-deployment-nautilus   0/1     1            0           28s

		NAME                                                    DESIRED   CURRENT   READY   AGE
		replicaset.apps/python-deployment-nautilus-5dbcd5f8b4   1         1         0       28s

2. Check the pod logs to know the error

thor@jump_host ~$ kubectl logs python-deployment-nautilus-5dbcd5f8b4-dflt7
		Error from server (BadRequest): container "python-container-nautilus" in pod "python-deployment-nautilus-5dbcd5f8b4-dflt7" is waiting to start: trying and failing to pull image

3. The error is that its uable to pull image, so lets check the deploy configured

thor@jump_host ~$ kubectl get deploy
		NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
		python-deployment-nautilus   0/1     1            0           64s

thor@jump_host ~$ kubectl describe deploy python-deployment-nautilus
			Name:                   python-deployment-nautilus
			Namespace:              default
			CreationTimestamp:      Sat, 31 Dec 2022 14:12:25 +0000
			Labels:                 <none>
			Annotations:            deployment.kubernetes.io/revision: 1
			Selector:               app=python_app
			Replicas:               1 desired | 1 updated | 1 total | 0 available | 1 unavailable
			StrategyType:           RollingUpdate
			MinReadySeconds:        0
			RollingUpdateStrategy:  25% max unavailable, 25% max surge
			Pod Template:
			  Labels:  app=python_app
			  Containers:
			   python-container-nautilus:
			    Image:        poroko/flask-app-demo                                 <--------Wrong name of image
			    Port:         5000/TCP                                              <--------Also image running on port 5000 which is flask default port
			    Host Port:    0/TCP
			    Environment:  <none>
			    Mounts:       <none>
			  Volumes:        <none>
			Conditions:
			  Type           Status  Reason
			  ----           ------  ------
			  Available      False   MinimumReplicasUnavailable
			  Progressing    True    ReplicaSetUpdated
			OldReplicaSets:  <none>
			NewReplicaSet:   python-deployment-nautilus-5dbcd5f8b4 (1/1 replicas created)
			Events:
			  Type    Reason             Age   From                   Message
			  ----    ------             ----  ----                   -------
			  Normal  ScalingReplicaSet  93s   deployment-controller  Scaled up replica set python-deployment-nautilus-5dbcd5f8b4 to 1

4. Edit deployment to update the image name. 

thor@jump_host ~$ kubectl edit deploy python-deployment-nautilus
		deployment.apps/python-deployment-nautilus edited

5. Wait for pod to get to runnning status 

thor@jump_host ~$ kubectl get all
			NAME                                              READY   STATUS              RESTARTS   AGE
			pod/python-deployment-nautilus-5dbcd5f8b4-dflt7   0/1     ImagePullBackOff    0          3m
			pod/python-deployment-nautilus-767b6d4c9b-t4jzx   0/1     ContainerCreating   0          9s

			NAME                              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
			service/kubernetes                ClusterIP   10.96.0.1       <none>        443/TCP          74m
			service/python-service-nautilus   NodePort    10.96.200.222   <none>        8080:32345/TCP   3m

			NAME                                         READY   UP-TO-DATE   AVAILABLE   AGE
			deployment.apps/python-deployment-nautilus   0/1     1            0           3m

			NAME                                                    DESIRED   CURRENT   READY   AGE
			replicaset.apps/python-deployment-nautilus-5dbcd5f8b4   1         1         0       3m
			replicaset.apps/python-deployment-nautilus-767b6d4c9b   1         1         0       9s

thor@jump_host ~$ kubectl get all
		NAME                                              READY   STATUS              RESTARTS   AGE
		pod/python-deployment-nautilus-5dbcd5f8b4-dflt7   0/1     ImagePullBackOff    0          3m7s
		pod/python-deployment-nautilus-767b6d4c9b-t4jzx   0/1     ContainerCreating   0          16s

		NAME                              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
		service/kubernetes                ClusterIP   10.96.0.1       <none>        443/TCP          74m
		service/python-service-nautilus   NodePort    10.96.200.222   <none>        8080:32345/TCP   3m7s

		NAME                                         READY   UP-TO-DATE   AVAILABLE   AGE
		deployment.apps/python-deployment-nautilus   0/1     1            0           3m7s

		NAME                                                    DESIRED   CURRENT   READY   AGE
		replicaset.apps/python-deployment-nautilus-5dbcd5f8b4   1         1         0       3m7s
		replicaset.apps/python-deployment-nautilus-767b6d4c9b   1         1         0       16s

6. Check service configuration as it is running on port 8080 where as it should run on flask default port 5000

thor@jump_host ~$ kubectl get service
NAME                      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
kubernetes                ClusterIP   10.96.0.1       <none>        443/TCP          74m
python-service-nautilus   NodePort    10.96.200.222   <none>        8080:32345/TCP   3m19s

thor@jump_host ~$ kubectl describe service python-service-nautilus
		Name:                     python-service-nautilus
		Namespace:                default
		Labels:                   <none>
		Annotations:              <none>
		Selector:                 app=python_app
		Type:                     NodePort
		IP:                       10.96.200.222
		Port:                     <unset>  8080/TCP
		TargetPort:               8080/TCP
		NodePort:                 <unset>  32345/TCP
		Endpoints:                10.244.0.6:8080
		Session Affinity:         None
		External Traffic Policy:  Cluster
		Events:                   <none>

7. Edit serive configuration to update port to 5000

thor@jump_host ~$ kubectl edit service python-service-nautilus
		service/python-service-nautilus edited

thor@jump_host ~$ kubectl get service
		NAME                      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
		kubernetes                ClusterIP   10.96.0.1       <none>        443/TCP          76m
		python-service-nautilus   NodePort    10.96.200.222   <none>        5000:32345/TCP   4m55s

8. Validate the task by checking updated config and logs of pod. Also run Application from top right corner

thor@jump_host ~$ kubectl get all
		NAME                                              READY   STATUS    RESTARTS   AGE
		pod/python-deployment-nautilus-767b6d4c9b-t4jzx   1/1     Running   0          2m11s

		NAME                              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
		service/kubernetes                ClusterIP   10.96.0.1       <none>        443/TCP          76m
		service/python-service-nautilus   NodePort    10.96.200.222   <none>        5000:32345/TCP   5m2s

		NAME                                         READY   UP-TO-DATE   AVAILABLE   AGE
		deployment.apps/python-deployment-nautilus   1/1     1            1           5m2s

		NAME                                                    DESIRED   CURRENT   READY   AGE
		replicaset.apps/python-deployment-nautilus-5dbcd5f8b4   0         0         0       5m2s
		replicaset.apps/python-deployment-nautilus-767b6d4c9b   1         1         1       2m11s

thor@jump_host ~$ kubectl logs python-deployment-nautilus-767b6d4c9b-t4jzx
		 * Serving Flask app "app.py"
		 * Environment: production
		   WARNING: Do not use the development server in a production environment.
		   Use a production WSGI server instead.
		 * Debug mode: off
		 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
thor@jump_host ~$

--------------------------------------------------------------------------------------------------------------------------------
Task 127: 02/Jan/2023

Fix issue with PhpFpm Application Deployed on Kubernetes

We deployed a Nginx and PHPFPM based application on Kubernetes cluster last week and it had been working fine. This morning one of the team members was troubleshooting an issue with this stack and he was supposed to run Nginx welcome page for now on this stack till issue with phpfpm is fixed but he made a change somewhere which caused some issue and the application stopped working. Please look into the issue and fix the same:

The deployment name is nginx-phpfpm-dp and service name is nginx-service. Figure out the issues and fix them. FYI Nginx is configured to use default http port, node port is 30008 and copy index.php under /tmp/index.php to deployment under /var/www/html. Please do not try to delete/modify any other existing components like deployment name, service name etc.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

1. Get current running status and config

thor@jump_host ~$ kubectl get all
			NAME                                   READY   STATUS    RESTARTS   AGE
			pod/nginx-phpfpm-dp-5cccd45499-52sfq   2/2     Running   0          72s

			NAME                    TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)          AGE
			service/kubernetes      ClusterIP      10.96.0.1     <none>        443/TCP          43m
			service/nginx-service   LoadBalancer   10.96.21.86   <pending>     8094:30008/TCP   72s

			NAME                              READY   UP-TO-DATE   AVAILABLE   AGE
			deployment.apps/nginx-phpfpm-dp   1/1     1            1           72s

			NAME                                         DESIRED   CURRENT   READY   AGE
			replicaset.apps/nginx-phpfpm-dp-5cccd45499   1         1         1       72s

thor@jump_host ~$ kubectl get deploy
		NAME              READY   UP-TO-DATE   AVAILABLE   AGE
		nginx-phpfpm-dp   1/1     1            1           83s

thor@jump_host ~$ kubectl describe deploy nginx-phpfpm-dp
			Name:               nginx-phpfpm-dp
			Namespace:          default
			CreationTimestamp:  Mon, 02 Jan 2023 07:57:43 +0000
			Labels:             app=nginx-fpm
			Annotations:        deployment.kubernetes.io/revision: 1
			Selector:           app=nginx-fpm,tier=frontend
			Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
			StrategyType:       Recreate
			MinReadySeconds:    0
			Pod Template:
			  Labels:  app=nginx-fpm
			           tier=frontend
			  Containers:
			   nginx-container:
			    Image:        nginx:latest
			    Port:         <none>
			    Host Port:    <none>
			    Environment:  <none>
			    Mounts:
			      /etc/nginx/nginx.conf from nginx-config-volume (rw,path="nginx.conf")
			      /var/www/html from shared-files (rw)
			   php-fpm-container:
			    Image:        php:7.3-fpm
			    Port:         <none>
			    Host Port:    <none>
			    Environment:  <none>
			    Mounts:
			      /var/www/html from shared-files (rw)
			  Volumes:
			   nginx-persistent-storage:
			    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
			    ClaimName:  nginx-pv-claim
			    ReadOnly:   false
			   shared-files:
			    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
			    Medium:     
			    SizeLimit:  <unset>
			   nginx-config-volume:
			    Type:      ConfigMap (a volume populated by a ConfigMap)
			    Name:      nginx-config
			    Optional:  false
			Conditions:
			  Type           Status  Reason
			  ----           ------  ------
			  Available      True    MinimumReplicasAvailable
			  Progressing    True    NewReplicaSetAvailable
			OldReplicaSets:  nginx-phpfpm-dp-5cccd45499 (1/1 replicas created)
			NewReplicaSet:   <none>
			Events:
			  Type    Reason             Age   From                   Message
			  ----    ------             ----  ----                   -------
			  Normal  ScalingReplicaSet  104s  deployment-controller  Scaled up replica set nginx-phpfpm-dp-5cccd45499 to 1

thor@jump_host ~$ kubectl get pods
		NAME                               READY   STATUS    RESTARTS   AGE
		nginx-phpfpm-dp-5cccd45499-52sfq   2/2     Running   0          2m14s

thor@jump_host ~$ kubectl describe pod nginx-phpfpm-dp-5cccd45499-52sfq
		Name:         nginx-phpfpm-dp-5cccd45499-52sfq
		Namespace:    default
		Priority:     0
		Node:         kodekloud-control-plane/172.17.0.2
		Start Time:   Mon, 02 Jan 2023 07:57:44 +0000
		Labels:       app=nginx-fpm
		              pod-template-hash=5cccd45499
		              tier=frontend
		Annotations:  <none>
		Status:       Running
		IP:           10.244.0.5
		IPs:
		  IP:           10.244.0.5
		Controlled By:  ReplicaSet/nginx-phpfpm-dp-5cccd45499
		Containers:
		  nginx-container:
		    Container ID:   containerd://e1f25836dc6bae575137ffc0a4bc333ee8ee6814f9781d07312c6a076a1aac0b
		    Image:          nginx:latest
		    Image ID:       docker.io/library/nginx@sha256:0047b729188a15da49380d9506d65959cce6d40291ccfb4e039f5dc7efd33286
		    Port:           <none>
		    Host Port:      <none>
		    State:          Running
		      Started:      Mon, 02 Jan 2023 07:57:56 +0000
		    Ready:          True
		    Restart Count:  0
		    Environment:    <none>
		    Mounts:
		      /etc/nginx/nginx.conf from nginx-config-volume (rw,path="nginx.conf")
		      /var/run/secrets/kubernetes.io/serviceaccount from default-token-lx7b4 (ro)
		      /var/www/html from shared-files (rw)
		  php-fpm-container:
		    Container ID:   containerd://949818ca41eff60942027eba6b89522df41a2702e05cab4b19bf7f887604be87
		    Image:          php:7.3-fpm
		    Image ID:       docker.io/library/php@sha256:2d68e401d2d3b9f8a6572791cd7a25062450c43ff52e58391146809741ad0885
		    Port:           <none>
		    Host Port:      <none>
		    State:          Running
		      Started:      Mon, 02 Jan 2023 07:58:25 +0000
		    Ready:          True
		    Restart Count:  0
		    Environment:    <none>
		    Mounts:
		      /var/run/secrets/kubernetes.io/serviceaccount from default-token-lx7b4 (ro)
		      /var/www/html from shared-files (rw)
		Conditions:
		  Type              Status
		  Initialized       True 
		  Ready             True 
		  ContainersReady   True 
		  PodScheduled      True 
		Volumes:
		  nginx-persistent-storage:
		    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
		    ClaimName:  nginx-pv-claim
		    ReadOnly:   false
		  shared-files:
		    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
		    Medium:     
		    SizeLimit:  <unset>
		  nginx-config-volume:
		    Type:      ConfigMap (a volume populated by a ConfigMap)
		    Name:      nginx-config
		    Optional:  false
		  default-token-lx7b4:
		    Type:        Secret (a volume populated by a Secret)
		    SecretName:  default-token-lx7b4
		    Optional:    false
		QoS Class:       BestEffort
		Node-Selectors:  <none>
		Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
		                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
		Events:
		  Type    Reason     Age    From               Message
		  ----    ------     ----   ----               -------
		  Normal  Scheduled  2m40s  default-scheduler  Successfully assigned default/nginx-phpfpm-dp-5cccd45499-52sfq to kodekloud-control-plane
		  Normal  Pulling    2m40s  kubelet            Pulling image "nginx:latest"
		  Normal  Pulled     2m29s  kubelet            Successfully pulled image "nginx:latest" in 10.902914584s
		  Normal  Created    2m29s  kubelet            Created container nginx-container
		  Normal  Started    2m29s  kubelet            Started container nginx-container
		  Normal  Pulling    2m29s  kubelet            Pulling image "php:7.3-fpm"
		  Normal  Pulled     2m1s   kubelet            Successfully pulled image "php:7.3-fpm" in 28.293999265s
		  Normal  Created    2m     kubelet            Created container php-fpm-container
		  Normal  Started    2m     kubelet            Started container php-fpm-container

2. Check pod logs for error

thor@jump_host ~$ kubectl logs nginx-phpfpm-dp-5cccd45499-52sfq
	error: a container name must be specified for pod nginx-phpfpm-dp-5cccd45499-52sfq, choose one of: [nginx-container php-fpm-container]

thor@jump_host ~$ kubectl logs nginx-phpfpm-dp-5cccd45499-52sfq -c nginx-container
		/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
		/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
		/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
		10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
		10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
		/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
		/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
		/docker-entrypoint.sh: Configuration complete; ready for start up

thor@jump_host ~$ kubectl logs nginx-phpfpm-dp-5cccd45499-52sfq -c php-fpm-container
		[02-Jan-2023 07:58:25] NOTICE: fpm is running, pid 1
		[02-Jan-2023 07:58:25] NOTICE: ready to handle connections

3. Check the configmap for port used 
 
thor@jump_host ~$ kubectl get configmap
NAME               DATA   AGE
kube-root-ca.crt   1      45m
nginx-config       1      3m47s
thor@jump_host ~$ kubectl describe configmap nginx-config
Name:         nginx-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
nginx.conf:
----
events {
}
http {
  server {
    listen 80 default_server;                                                      <----- Default nginx port 80
    listen [::]:80 default_server;

    # Set nginx to serve files from the shared volume!
    root /var/www/html;
    index  index.html index.ph p index.htm;                                         <------- error in index file name
    server_name _;
    location / {
      try_files $uri $uri/ =404;
    }
    location ~ \.php$ {
      include fastcgi_params;
      fastcgi_param REQUEST_METHOD $request_method;
      fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
      fastcgi_pass 127.0.0.1:9000;
    }
  }
}

Events:  <none>

4. Edit the index file name in configmap (2 changes)

thor@jump_host ~$ kubectl edit configmap nginx-config
		configmap/nginx-config edited

thor@jump_host ~$ kubectl describe configmap nginx-config
			Name:         nginx-config
			Namespace:    default
			Labels:       <none>
			Annotations:  <none>

			Data
			====
			nginx.conf:
			----
			events {
			}
			http {
			  server {
			    listen 80 default_server;
			    listen [::]:80 default_server;

			    # Set nginx to serve files from the shared volume!
			    root /var/www/html;
			    index  index.html index.php index.htm;
			    server_name _;
			    location / {
			      try_files $uri $uri/ =404;
			    }
			    location ~ \.php$ {
			      include fastcgi_params;
			      fastcgi_param REQUEST_METHOD $request_method;
			      fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
			      fastcgi_pass 127.0.0.1:9000;
			    }
			  }
			}

			Events:  <none>

5. Check the service			

thor@jump_host ~$ kubectl get service
		NAME            TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)          AGE
		kubernetes      ClusterIP      10.96.0.1     <none>        443/TCP          47m
		nginx-service   LoadBalancer   10.96.21.86   <pending>     8094:30008/TCP   5m32s                <----Port is different than 80

thor@jump_host ~$ kubectl describe service nginx-service
			Name:                     nginx-service
			Namespace:                default
			Labels:                   app=nginx-fpm
			Annotations:              <none>
			Selector:                 app=nginx-fpm,tier=frontend
			Type:                     LoadBalancer
			IP:                       10.96.21.86
			Port:                     <unset>  8094/TCP                                                     <------Port is different than 80
			TargetPort:               8094/TCP
			NodePort:                 <unset>  30008/TCP
			Endpoints:                10.244.0.5:8094
			Session Affinity:         None
			External Traffic Policy:  Cluster
			Events:                   <none>

6. Edit service to configure port to 80 (3 changes)

thor@jump_host ~$ kubectl edit service nginx-service
		service/nginx-service edited
 
thor@jump_host ~$ kubectl get service
		NAME            TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
		kubernetes      ClusterIP      10.96.0.1     <none>        443/TCP        48m
		nginx-service   LoadBalancer   10.96.21.86   <pending>     80:30008/TCP   6m35s                  <-----Port Changed

7. Restart deployment in order to restart pods (if pods dont restart on own)

thor@jump_host ~$ kubectl rollout restart deploy nginx-phpfpm-dp
		deployment.apps/nginx-phpfpm-dp restarted

thor@jump_host ~$ kubectl get deploy
		NAME              READY   UP-TO-DATE   AVAILABLE   AGE
		nginx-phpfpm-dp   0/1     1            0           13m

thor@jump_host ~$ kubectl get deploy
		NAME              READY   UP-TO-DATE   AVAILABLE   AGE
		nginx-phpfpm-dp   1/1     1            1           13m

thor@jump_host ~$ kubectl get pods
		NAME                               READY   STATUS    RESTARTS   AGE
		nginx-phpfpm-dp-6b5c45c6c7-zq6nr   2/2     Running   0          12s

thor@jump_host ~$ kubectl get pods
		NAME                               READY   STATUS    RESTARTS   AGE
		nginx-phpfpm-dp-6b5c45c6c7-zq6nr   2/2     Running   0          57s

8. Copy index.php from jump host server to nginx_container  in the running pod and verify

thor@jump_host ~$ kubectl cp /tmp/index.php nginx-phpfpm-dp-6b5c45c6c7-zq6nr:/var/www/html -c nginx-container

thor@jump_host ~$ kubectl exec -it nginx-phpfpm-dp-6b5c45c6c7-zq6nr -c nginx-container -- bash

root@nginx-phpfpm-dp-6b5c45c6c7-zq6nr:/# ls -ahl /var/www/html
		total 12K
		drwxrwxrwx 2 root root 4.0K Jan  2 08:12 .
		drwxr-xr-x 3 root root 4.0K Jan  2 08:10 ..
		-rw-r--r-- 1 root root  168 Jan  2 08:12 index.php
root@nginx-phpfpm-dp-6b5c45c6c7-zq6nr:/# 

9. Validate by clicking on the view port top right side side of the terminal and select port 30008. If the container is running then we see nginx page and the task is done.

--------------------------------------------------------------------------------------------------------------------------------
Task 128: 03/Jan/2023

Rolling Updates And Rolling Back Deployments in Kubernetes


There is a production deployment planned for next week. The Nautilus DevOps team wants to test the deployment update and rollback on Dev environment first so that they can identify the risks in advance. Below you can find more details about the plan they want to execute.

    Create a namespace nautilus. Create a deployment called httpd-deploy under this new namespace, It should have one container called httpd, use httpd:2.4.27 image and 2 replicas. The deployment should use RollingUpdate strategy with maxSurge=1, and maxUnavailable=2. Also create a NodePort type service named httpd-service and expose the deployment on nodePort: 30008.

    Now upgrade the deployment to version httpd:2.4.43 using a rolling update.

    Finally, once all pods are updated undo the recent update and roll back to the previous/original version.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

1. Get current configuration

thor@jump_host ~$ kubectl get namespace
	NAME                 STATUS   AGE
	default              Active   27m
	kube-node-lease      Active   27m
	kube-public          Active   27m
	kube-system          Active   27m
	local-path-storage   Active   27m

2. Create mentioned namespace and verify

thor@jump_host ~$ kubectl create namespace nautilus
	namespace/nautilus created

thor@jump_host ~$ kubectl get namespace
		NAME                 STATUS   AGE
		default              Active   28m
		kube-node-lease      Active   28m
		kube-public          Active   28m
		kube-system          Active   28m
		local-path-storage   Active   27m
		nautilus             Active   3s

3. Create YAML file to implement the requirements of deployment and service

thor@jump_host ~$ vi /tmp/httpd_deploy.yaml

thor@jump_host ~$ cat /tmp/httpd_deploy.yaml
		---
		apiVersion: apps/v1
		kind: Deployment
		metadata:
		  name: httpd-deploy
		  namespace: nautilus
		spec:
		  replicas: 2
		  strategy:
		    type: RollingUpdate
		    rollingUpdate:
		      maxSurge: 1
		      maxUnavailable: 2
		  selector:
		    matchLabels:
		      app: httpd
		  template:
		    metadata:
		      labels:
		        app: httpd
		    spec:
		      containers:
		        - name: httpd
		          image: httpd:2.4.27
		---                                                                                                           
		apiVersion: v1                                                                                                
		kind: Service                                                                                                 
		metadata:                                                                                                     
		  name: httpd-service                                                                                         
		spec:                                                                                                         
		  type: NodePort                                                                                             
		  selector:                                                                                                  
		    app: httpd                                                                                     
		  ports:                                                                                                     
		    - port: 80                                                                                               
		      targetPort: 80                                                                                         
		      nodePort: 30008

4. Apply the YAML file created 
thor@jump_host ~$ kubectl apply -f /tmp/httpd_deploy.yaml --namespace=nautilus
		deployment.apps/httpd-deploy created
		service/httpd-service created

5. Verify the changes/creation of deployment, pods and service. Wait till pods get into running status.

thor@jump_host ~$ kubectl get all
		NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
		service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   29m

thor@jump_host ~$ kubectl get all -n nautilus
		NAME                                READY   STATUS    RESTARTS   AGE
		pod/httpd-deploy-5c68f9f7b7-bbczb   1/1     Running   0          38s
		pod/httpd-deploy-5c68f9f7b7-txl6h   1/1     Running   0          38s

		NAME                    TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
		service/httpd-service   NodePort   10.96.225.85   <none>        80:30008/TCP   38s

		NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
		deployment.apps/httpd-deploy   2/2     2            2           38s

		NAME                                      DESIRED   CURRENT   READY   AGE
		replicaset.apps/httpd-deploy-5c68f9f7b7   2         2         2       38s

thor@jump_host ~$ kubectl get deployment -n nautilus -o wide
		NAME           READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES         SELECTOR
		httpd-deploy   2/2     2            2           60s   httpd        httpd:2.4.27   app=httpd

thor@jump_host ~$ kubectl get pods -n nautilus
		NAME                            READY   STATUS    RESTARTS   AGE
		httpd-deploy-5c68f9f7b7-bbczb   1/1     Running   0          73s
		httpd-deploy-5c68f9f7b7-txl6h   1/1     Running   0          73s

6. Update the container image as mentioned and wait till pods get into running status 

thor@jump_host ~$ kubectl set image deployment/httpd-deploy httpd=httpd:2.4.43 --namespace=nautilus --record=true
		deployment.apps/httpd-deploy image updated

thor@jump_host ~$ kubectl get deployment -n nautilus -o wide
		NAME           READY   UP-TO-DATE   AVAILABLE   AGE    CONTAINERS   IMAGES         SELECTOR
		httpd-deploy   0/2     2            0           101s   httpd        httpd:2.4.43   app=httpd

thor@jump_host ~$ kubectl get pods -n nautilus
		NAME                            READY   STATUS              RESTARTS   AGE
		httpd-deploy-7bb4d96457-7pg4l   0/1     ContainerCreating   0          19s
		httpd-deploy-7bb4d96457-kjbn4   0/1     ContainerCreating   0          18s

thor@jump_host ~$ kubectl get pods -n nautilus
		NAME                            READY   STATUS    RESTARTS   AGE
		httpd-deploy-7bb4d96457-7pg4l   1/1     Running   0          28s
		httpd-deploy-7bb4d96457-kjbn4   1/1     Running   0          27s

thor@jump_host ~$ kubectl get deployment -n nautilus -o wide
		NAME           READY   UP-TO-DATE   AVAILABLE   AGE    CONTAINERS   IMAGES         SELECTOR
		httpd-deploy   2/2     2            2           119s   httpd        httpd:2.4.43   app=httpd


7. Check rollout status and history for deployment

thor@jump_host ~$ kubectl rollout status deployment httpd-deploy -n nautilus
		deployment "httpd-deploy" successfully rolled out
 
thor@jump_host ~$ kubectl rollout history deployment httpd-deploy -n nautilus
		deployment.apps/httpd-deploy 
		REVISION  CHANGE-CAUSE
		1         <none>
		2         kubectl set image deployment/httpd-deploy httpd=httpd:2.4.43 --namespace=nautilus --record=true

8. Undo latest rollout to revert back the changes and wait for pods to get back to running status again. Check status and history of deployment

thor@jump_host ~$ kubectl rollout undo deployment httpd-deploy -n nautilus
		deployment.apps/httpd-deploy rolled back

thor@jump_host ~$ kubectl rollout status deployment httpd-deploy -n nautilus
		Waiting for deployment "httpd-deploy" rollout to finish: 1 of 2 updated replicas are available...
		deployment "httpd-deploy" successfully rolled out

thor@jump_host ~$ kubectl get pods -n nautilus
		NAME                            READY   STATUS    RESTARTS   AGE
		httpd-deploy-5c68f9f7b7-9sgks   1/1     Running   0          16s
		httpd-deploy-5c68f9f7b7-rnv6l   1/1     Running   0          15s

thor@jump_host ~$ kubectl get deployment -n nautilus -o wide
		NAME           READY   UP-TO-DATE   AVAILABLE   AGE    CONTAINERS   IMAGES         SELECTOR
		httpd-deploy   2/2     2            2           3m2s   httpd        httpd:2.4.27   app=httpd

thor@jump_host ~$ kubectl rollout history deployment httpd-deploy -n nautilus
		deployment.apps/httpd-deploy 
		REVISION  CHANGE-CAUSE
		2         kubectl set image deployment/httpd-deploy httpd=httpd:2.4.43 --namespace=nautilus --record=true
		3         <none>

thor@jump_host ~$
--------------------------------------------------------------------------------------------------------------------------------
Task 129: 04/Jan/2023

Managing Jinja2 Templates Using Ansible

One of the Nautilus DevOps team members is working on to develop a role for httpd installation and configuration. Work is almost completed, however there is a requirement to add a jinja2 template for index.html file. Additionally, the relevant task needs to be added inside the role. The inventory file ~/ansible/inventory is already present on jump host that can be used. Complete the task as per details mentioned below:

a. Update ~/ansible/playbook.yml playbook to run the httpd role on App Server 1.

b. Create a jinja2 template index.html.j2 under /home/thor/ansible/role/httpd/templates/ directory and add a line This file was created using Ansible on <respective server> (for example This file was created using Ansible on stapp01 in case of App Server 1). Also please make sure not to hard code the server name inside the template. Instead, use inventory_hostname variable to fetch the correct value.

c. Add a task inside /home/thor/ansible/role/httpd/tasks/main.yml to copy this template on App Server 1 under /var/www/html/index.html. Also make sure that /var/www/html/index.html file's permissions are 0655.

d. The user/group owner of /var/www/html/index.html file must be respective sudo user of the server (for example tony in case of stapp01).

Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure the playbook works this way without passing any extra arguments.


1. Check current inventory and playbook

thor@jump_host ~$ cd /home/thor/ansible/

thor@jump_host ~/ansible$ ls -ahl
		total 20K
		drwxr-xr-x 3 thor thor 4.0K Jan  4 14:10 .
		drwxr----- 1 thor thor 4.0K Jan  4 14:10 ..
		-rw-r--r-- 1 thor thor  237 Jan  4 14:10 inventory
		-rw-r--r-- 1 thor thor   73 Jan  4 14:10 playbook.yml
		drwxr-xr-x 3 thor thor 4.0K Jan  4 14:10 role

thor@jump_host ~/ansible$ cat inventory 
		stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
		stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
		stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n

thor@jump_host ~/ansible$ cat playbook.yml 
		---
		- hosts: 
		  become: yes
		  become_user: root
		  roles:
		    - role/httpd

2. Edit playbook to add host to just App Server 1

thor@jump_host ~/ansible$ vi playbook.yml 

thor@jump_host ~/ansible$ cat playbook.yml 
		---
		- hosts: stapp01 
		  become: yes
		  become_user: root
		  roles:
		    - role/httpd

3. Create Jinja template as per given requirement

thor@jump_host ~/ansible$ vi /home/thor/ansible/role/httpd/templates/index.html.j2

thor@jump_host ~/ansible$ cat /home/thor/ansible/role/httpd/templates/index.html.j2
		This file was created using Ansible on {{ ansible_hostname }}

4. Edit role file as per given requirement to add task to create index.html on App Server

thor@jump_host ~/ansible$ vi /home/thor/ansible/role/httpd/tasks/main.yml

thor@jump_host ~/ansible$ cat /home/thor/ansible/role/httpd/tasks/main.yml
		---
		# tasks file for role/test

		- name: install the latest version of HTTPD
		  yum:
		    name: httpd
		    state: latest

		- name: Start service httpd
		  service:
		    name: httpd
		    state: started

		- name: Use Jinja2 template to generate index.html
		  template:
		    src: /home/thor/ansible/role/httpd/templates/index.html.j2
		    dest: /var/www/html/index.html
		    mode: "0655"
		    owner: "{{ ansible_user }}"
		    group: "{{ ansible_user }}"

5. Run playbook

thor@jump_host ~/ansible$ ansible-playbook -i inventory playbook.yml 

		PLAY [stapp01] ******************************************************************************************************************************

		TASK [Gathering Facts] **********************************************************************************************************************
		ok: [stapp01]

		TASK [role/httpd : install the latest version of HTTPD] *************************************************************************************
		changed: [stapp01]

		TASK [role/httpd : Start service httpd] *****************************************************************************************************
		changed: [stapp01]

		TASK [role/httpd : Use Jinja2 template to generate index.html] ******************************************************************************
		changed: [stapp01]

		PLAY RECAP **********************************************************************************************************************************
		stapp01                    : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

6. Verify and validate task by checking index.html on App server 1

thor@jump_host ~/ansible$ ansible -i inventory stapp01 -a "ls -ahl /var/www/html"
		stapp01 | CHANGED | rc=0 >>
		total 12K
		drwxr-xr-x 2 root root 4.0K Jan  4 14:20 .
		drwxr-xr-x 4 root root 4.0K Jan  4 14:20 ..
		-rw-r-xr-x 1 tony tony   47 Jan  4 14:20 index.html

thor@jump_host ~/ansible$ ansible -i inventory stapp01 -a "cat /var/www/html/index.html"
		stapp01 | CHANGED | rc=0 >>
		This file was created using Ansible on stapp01

thor@jump_host ~/ansible$

---------------------------------------------------------------------------------------------------------------------------------
Task 130: 06/Jan/2023

 Ansible Create Users and Groups


Several new developers and DevOps engineers just joined the xFusionCorp industries. They have been assigned the Nautilus project, and as per the onboarding process we need to create user accounts for new joinees on at least one of the app servers in Stratos DC. We also need to create groups and make new users members of those groups. We need to accomplish this task using Ansible. Below you can find more information about the task.

There is already an inventory file ~/playbooks/inventory on jump host.

On jump host itself there is a list of users in ~/playbooks/data/users.yml file and there are two groups — admins and developers —that have list of different users. Create a playbook ~/playbooks/add_users.yml on jump host to perform the following tasks on app server 2 in Stratos DC.

a. Add all users given in the users.yml file on app server 2.

b. Also add developers and admins groups on the same server.

c. As per the list given in the users.yml file, make each user member of the respective group they are listed under.

d. Make sure home directory for all of the users under developers group is /var/www (not the default i.e /var/www/{USER}). Users under admins group should use the default home directory (i.e /home/devid for user devid).

e. Set password BruCStnMT5 for all of the users under developers group and LQfKeWWxWD for of the users under admins group. Make sure to use the password given in the ~/playbooks/secrets/vault.txt file as Ansible vault password to encrypt the original password strings. You can use ~/playbooks/secrets/vault.txt file as a vault secret file while running the playbook (make necessary changes in ~/playbooks/ansible.cfg file).

f. All users under admins group must be added as sudo users. To do so, simply make them member of the wheel group as well.

Note: Validation will try to run the playbook using command ansible-playbook -i inventory add_users.yml so please make sure playbook works this way, without passing any extra arguments.


1. Check inventory file , users file and password vault file

thor@jump_host ~$ cd /home/thor/playbooks/

thor@jump_host ~/playbooks$ ls -ahl
		total 24K
		drwxr-xr-x 4 thor thor 4.0K Jan  6 07:34 .
		drwxr----- 1 thor thor 4.0K Jan  6 07:34 ..
		-rw-r--r-- 1 thor thor   36 Jan  6 07:34 ansible.cfg
		drwxr-xr-x 2 thor thor 4.0K Jan  6 04:21 data
		-rw-r--r-- 1 thor thor  237 Jan  6 07:34 inventory
		drwxr-xr-x 2 thor thor 4.0K Jan  6 07:34 secrets

thor@jump_host ~/playbooks$ cat inventory 
		stapp01 ansible_host=172.16.238.10 ansible_ssh_pass=Ir0nM@n ansible_user=tony
		stapp02 ansible_host=172.16.238.11 ansible_ssh_pass=Am3ric@ ansible_user=steve
		stapp03 ansible_host=172.16.238.12 ansible_ssh_pass=BigGr33n ansible_user=banner

thor@jump_host ~/playbooks$ cat data/users.yml 
		admins:
		  - rob
		  - david
		  - joy

		developers:
		  - tim
		  - ray
		  - jim
		  - mark

thor@jump_host ~/playbooks$ cat secrets/vault.txt 
		P@ss3or432

2. Add vault file location to ansible config file

thor@jump_host ~/playbooks$ cat ansible.cfg 
		[defaults]
		host_key_checking = False

thor@jump_host ~/playbooks$ vi ansible.cfg 

thor@jump_host ~/playbooks$ cat ansible.cfg 
		[defaults]
		host_key_checking = False
		vault_password_file = /home/thor/playbooks/secrets/vault.txt

3. Create playbook add_users.yml as per given requirements to add users and groups

thor@jump_host ~/playbooks$ vi add_users.yml

thor@jump_host ~/playbooks$ cat add_users.yml
		---                                                                                                              
		- name: Ansbile Add User & Group                                                                       
		  hosts: stapp02                                                                                                
		  become: yes                                                                                                    
		  tasks:                                                                                                         
		  - name: Creating Admin Groups                                                                                  
		    group:                                                                                                       
		     name:                                                                                                       
		      admins                                                                                                     
		     state: present                                                                                              
		  - name: Creating Dev Groups                                                                                    
		    group:                                                                                                       
		     name:                                                                                                       
		      developers                                                                                                 
		     state: present                                                                                              
		  - name: Creating Admins Group Users                                                                            
		    user:                                                                                                        
		     name: "{{ item }}"                                                                                          
		     password: "{{ 'LQfKeWWxWD' | password_hash ('sha512') }}"                                                   
		     groups: admins,wheel
		     state: present                                                                                              
		    loop:                                                                                                        
		    - rob                                                                                                        
		    - joy                                                                                                        
		    - david                                                                                                      
		  - name: Creating Developers Group Users                                                                        
		    user:                                                                                                        
		     name: "{{ item }}"                                                                                          
		     password: "{{ 'BruCStnMT5' | password_hash ('sha512') }}"                                                   
		     home: "/var/www/{{ item }}"                                                                                             
		     group: developers                                                                                           
		     state: present                                                                                              
		    loop:                                                                                                        
		    - tim                                                                                                        
		    - jim                                                                                                        
		    - mark                                                                                                       
		    - ray

4. Check connection by getting current users list on app server 2

thor@jump_host ~/playbooks$ ansible -i inventory stapp02 -a "cat /etc/passwd"
		stapp02 | CHANGED | rc=0 >>
		root:x:0:0:root:/root:/bin/bash
		bin:x:1:1:bin:/bin:/sbin/nologin
		daemon:x:2:2:daemon:/sbin:/sbin/nologin
		adm:x:3:4:adm:/var/adm:/sbin/nologin
		lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
		sync:x:5:0:sync:/sbin:/bin/sync
		shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
		halt:x:7:0:halt:/sbin:/sbin/halt
		mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
		operator:x:11:0:operator:/root:/sbin/nologin
		games:x:12:100:games:/usr/games:/sbin/nologin
		ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
		nobody:x:99:99:Nobody:/:/sbin/nologin
		systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
		dbus:x:81:81:System message bus:/:/sbin/nologin
		sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
		ansible:x:1000:1000::/home/ansible:/bin/bash
		steve:x:1001:1001::/home/steve:/bin/bash

5. Run playbook to create users/groups on App Server 2

thor@jump_host ~/playbooks$ ansible-playbook -i inventory add_users.yml 

		PLAY [Ansbile Add User & Group] *************************************************************************************************************

		TASK [Gathering Facts] **********************************************************************************************************************
		ok: [stapp02]

		TASK [Creating Admin Groups] ****************************************************************************************************************
		changed: [stapp02]

		TASK [Creating Dev Groups] ******************************************************************************************************************
		changed: [stapp02]

		TASK [Creating Admins Group Users] **********************************************************************************************************
		changed: [stapp02] => (item=rob)
		changed: [stapp02] => (item=joy)
		changed: [stapp02] => (item=david)

		TASK [Creating Developers Group Users] ******************************************************************************************************
		changed: [stapp02] => (item=tim)
		changed: [stapp02] => (item=jim)
		changed: [stapp02] => (item=mark)
		changed: [stapp02] => (item=ray)

		PLAY RECAP **********************************************************************************************************************************
		stapp02                    : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   


6. Verify and validate task

thor@jump_host ~/playbooks$ ansible -i inventory stapp02 -a "cat /etc/passwd"
		stapp02 | CHANGED | rc=0 >>
		root:x:0:0:root:/root:/bin/bash
		bin:x:1:1:bin:/bin:/sbin/nologin
		daemon:x:2:2:daemon:/sbin:/sbin/nologin
		adm:x:3:4:adm:/var/adm:/sbin/nologin
		lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
		sync:x:5:0:sync:/sbin:/bin/sync
		shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
		halt:x:7:0:halt:/sbin:/sbin/halt
		mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
		operator:x:11:0:operator:/root:/sbin/nologin
		games:x:12:100:games:/usr/games:/sbin/nologin
		ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
		nobody:x:99:99:Nobody:/:/sbin/nologin
		systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
		dbus:x:81:81:System message bus:/:/sbin/nologin
		sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
		ansible:x:1000:1000::/home/ansible:/bin/bash
		steve:x:1001:1001::/home/steve:/bin/bash
		rob:x:1002:1004::/home/rob:/bin/bash
		joy:x:1003:1005::/home/joy:/bin/bash
		david:x:1004:1006::/home/david:/bin/bash
		tim:x:1005:1003::/var/www/tim:/bin/bash
		jim:x:1006:1003::/var/www/jim:/bin/bash
		mark:x:1007:1003::/var/www/mark:/bin/bash
		ray:x:1008:1003::/var/www/ray:/bin/bash

thor@jump_host ~/playbooks$ ssh rob@stapp02
		The authenticity of host 'stapp02 (172.16.238.11)' can't be established.
		ECDSA key fingerprint is SHA256:RfzwP0l5UhLkHTrsf3Aiiib98V9zGk8SLR64bTwIwBg.
		ECDSA key fingerprint is MD5:c3:cb:fd:91:90:a5:b8:56:e6:28:80:85:23:c6:aa:85.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'stapp02' (ECDSA) to the list of known hosts.
		rob@stapp02's password: 

[rob@stapp02 ~]$ sudo su -

[root@stapp02 ~]# id
		uid=0(root) gid=0(root) groups=0(root)

[root@stapp02 ~]# id david
		uid=1004(david) gid=1006(david) groups=1006(david),10(wheel),1002(admins)

[root@stapp02 ~]# id joy
		uid=1003(joy) gid=1005(joy) groups=1005(joy),10(wheel),1002(admins)

[root@stapp02 ~]# id mark
		uid=1007(mark) gid=1003(developers) groups=1003(developers)

[root@stapp02 ~]# exit
logout

[rob@stapp02 ~]$ exit
		logout
		Connection to stapp02 closed.
thor@jump_host ~/playbooks$

---------------------------------------------------------------------------------------------------------------------------------
Task 131: 07/Jan/2023

Puppet Add Users

A new teammate has joined the Nautilus application development team, the application development team has asked the DevOps team to create a new user account for the new teammate on application server 3 in Stratos Datacenter. The task needs to be performed using Puppet only. You can find more details below about the task.

Create a Puppet programming file news.pp under /etc/puppetlabs/code/environments/production/manifests directory on master node i.e Jump Server, and using Puppet user resource add a user on all app servers as mentioned below:

    Create a user siva and set its UID to 1644 on Puppet agent nodes 3 i.e App Servers 3.

Notes: :- Please make sure to run the puppet agent test using sudo on agent nodes, otherwise you can face certificate issues. In that case you will have to clean the certificates first and then you will be able to run the puppet agent test.

:- Before clicking on the Check button please make sure to verify puppet server and puppet agent services are up and running on the respective servers, also please make sure to run puppet agent test to apply/test the changes manually first.

:- Please note that once lab is loaded, the puppet server service should start automatically on puppet master server, however it can take upto 2-3 minutes to start.


1. Login to root user on jump host

thor@jump_host ~$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for thor: 

2. Create puppet programming file news.pp on given directory location as per give nrequirements

root@jump_host ~# cd /etc/puppetlabs/code/environments/production/manifests

root@jump_host /etc/puppetlabs/code/environments/production/manifests# vi news.pp

root@jump_host /etc/puppetlabs/code/environments/production/manifests# cat news.pp
		class user_create {
		  user { 
		   'siva':
		   ensure   => present,
		   uid => 1644,
		  }
		}

		node 'stapp03.stratos.xfusioncorp.com' {
		  include user_create
		}

3. Parse and validate news.pp file

root@jump_host /etc/puppetlabs/code/environments/production/manifests# puppet parser validate news.pp

4. Login to App Server 3 and sitch to root user

root@jump_host /etc/puppetlabs/code/environments/production/manifests# ssh banner@stapp03
		The authenticity of host 'stapp03 (172.16.238.12)' can't be established.
		ECDSA key fingerprint is SHA256:FRLWcj5X07yxcJggr/l4cU9nSphy2E6WFO+cW4WgnqQ.
		ECDSA key fingerprint is MD5:47:a3:ee:4c:19:d1:8c:47:d7:0f:0e:43:a0:6f:45:89.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'stapp03,172.16.238.12' (ECDSA) to the list of known hosts.
		banner@stapp03's password: 

[banner@stapp03 ~]$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for banner: 

5. Run puppet agent to pull the changes from master server

[root@stapp03 ~]# puppet agent -tv
		Info: Using configured environment 'production'
		Info: Retrieving pluginfacts
		Info: Retrieving plugin
		Info: Retrieving locales
		Info: Caching catalog for stapp03.stratos.xfusioncorp.com
		Info: Applying configuration version '1673086141'
		Notice: /Stage[main]/User_create/User[siva]/ensure: created
		Notice: Applied catalog in 0.05 seconds

6. Validate the task by checking user created

[root@stapp03 ~]# cat /etc/passwd|grep siva
		siva:x:1644:1644::/home/siva:/bin/bash

[root@stapp03 ~]# id siva
		uid=1644(siva) gid=1644(siva) groups=1644(siva)

---------------------------------------------------------------------------------------------------------------------------------
Task 132: 08/Jan/2023

Ansible Copy Module


There is data on jump host that needs to be copied on all application servers in Stratos DC. Nautilus DevOps team want to perform this task using Ansible. Perform the task as per details mentioned below:

a. On jump host create an inventory file /home/thor/ansible/inventory and add all application servers as managed nodes.

b. On jump host create a playbook /home/thor/ansible/playbook.yml to copy /usr/src/devops/index.html file to all application servers at location /opt/devops.

Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure the playbook works this way without passing any extra arguments.


1. Go to mentioned folder and create inventory file with credentials for all app servers 

thor@jump_host ~$ cd /home/thor/ansible/

thor@jump_host ~/ansible$ ls -ahl
		total 8.0K
		drwxr-xr-x 2 thor thor 4.0K Jan  8 14:04 .
		drwxr----- 1 thor thor 4.0K Jan  8 14:04 ..

thor@jump_host ~/ansible$ vi /home/thor/ansible/inventory

thor@jump_host ~/ansible$ cat /home/thor/ansible/inventory
		stapp01 ansible_host=172.16.238.10 ansible_ssh_pass=Ir0nM@n  ansible_user=tony

		stapp02 ansible_host=172.16.238.11 ansible_ssh_pass=Am3ric@  ansible_user=steve

		stapp03 ansible_host=172.16.238.12 ansible_ssh_pass=BigGr33n  ansible_user=banner

2. Verify the inventory file by running command via ansible

thor@jump_host ~/ansible$ ansible -i inventory all -a "ls -ahl /opt/devops"
		stapp03 | CHANGED | rc=0 >>
		total 8.0K
		drwxr-xr-x 2 root root 4.0K Jan  8 14:05 .
		drwxr-xr-x 1 root root 4.0K Jan  8 14:05 ..
		stapp02 | CHANGED | rc=0 >>
		total 8.0K
		drwxr-xr-x 2 root root 4.0K Jan  8 14:05 .
		drwxr-xr-x 1 root root 4.0K Jan  8 14:05 ..
		stapp01 | CHANGED | rc=0 >>
		total 8.0K
		drwxr-xr-x 2 root root 4.0K Jan  8 14:05 .
		drwxr-xr-x 1 root root 4.0K Jan  8 14:05 ..

3. Create Playbook.yml to copy mentioned file 

thor@jump_host ~/ansible$ vi playbook.yml

thor@jump_host ~/ansible$ cat playbook.yml
		- name: Ansible copy

		  hosts: all

		  become: yes

		  tasks:

		    - name: copy index.html to sysops folder

		      copy: src=/usr/src/devops/index.html dest=/opt/devops

4. Run the playbook

thor@jump_host ~/ansible$ ansible-playbook -i inventory playbook.yml 

		PLAY [Ansible copy] *************************************************************************************************************************

		TASK [Gathering Facts] **********************************************************************************************************************
		ok: [stapp03]
		ok: [stapp01]
		ok: [stapp02]

		TASK [copy index.html to sysops folder] *****************************************************************************************************
		changed: [stapp02]
		changed: [stapp03]
		changed: [stapp01]

		PLAY RECAP **********************************************************************************************************************************
		stapp01                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
		stapp02                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
		stapp03                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

5. Validate the task by checking destination folder on each server by ansible

thor@jump_host ~/ansible$ ansible -i inventory all -a "ls -ahl /opt/devops"
		stapp02 | CHANGED | rc=0 >>
		total 12K
		drwxr-xr-x 2 root root 4.0K Jan  8 14:08 .
		drwxr-xr-x 1 root root 4.0K Jan  8 14:05 ..
		-rw-r--r-- 1 root root   35 Jan  8 14:08 index.html
		stapp03 | CHANGED | rc=0 >>
		total 12K
		drwxr-xr-x 2 root root 4.0K Jan  8 14:08 .
		drwxr-xr-x 1 root root 4.0K Jan  8 14:05 ..
		-rw-r--r-- 1 root root   35 Jan  8 14:08 index.html
		stapp01 | CHANGED | rc=0 >>
		total 12K
		drwxr-xr-x 2 root root 4.0K Jan  8 14:08 .
		drwxr-xr-x 1 root root 4.0K Jan  8 14:05 ..
		-rw-r--r-- 1 root root   35 Jan  8 14:08 index.html

thor@jump_host ~/ansible$

---------------------------------------------------------------------------------------------------------------------------------
Task 133: 10/Jan/2023

Ansible Unarchive Module

One of the DevOps team members has created an ZIP archive on jump host in Stratos DC that needs to be extracted and copied over to all app servers in Stratos DC itself. Because this is a routine task, the Nautilus DevOps team has suggested automating it. We can use Ansible since we have been using it for other automation tasks. Below you can find more details about the task:

We have an inventory file under /home/thor/ansible directory on jump host, which should have all the app servers added already.

There is a ZIP archive /usr/src/itadmin/nautilus.zip on jump host.

Create a playbook.yml under /home/thor/ansible/ directory on jump host itself to perform the below given tasks.

    Unzip /usr/src/itadmin/nautilus.zip archive in /opt/itadmin/ location on all app servers.

    Make sure the extracted data must has the respective sudo user as their user and group owner, i.e tony for app server 1, steve for app server 2, banner for app server 3.

    The extracted data permissions must be 0755

Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure playbook works this way, without passing any extra arguments.

1. Check ansible configuration and connection to all app servers

thor@jump_host ~$ cd /home/thor/ansible/

thor@jump_host ~/ansible$ ls -ahl
		total 16K
		drwxr-xr-x 2 thor thor 4.0K Jan 10 04:42 .
		drwxr----- 1 thor thor 4.0K Jan 10 04:42 ..
		-rw-r--r-- 1 thor thor   36 Jan 10 04:42 ansible.cfg
		-rw-r--r-- 1 thor thor  237 Jan 10 04:42 inventory

thor@jump_host ~/ansible$ cat inventory 
		stapp01 ansible_host=172.16.238.10 ansible_ssh_pass=Ir0nM@n ansible_user=tony
		stapp02 ansible_host=172.16.238.11 ansible_ssh_pass=Am3ric@ ansible_user=steve
		stapp03 ansible_host=172.16.238.12 ansible_ssh_pass=BigGr33n ansible_user=banner


thor@jump_host ~/ansible$ ansible -i inventory all -a "ls -ahl /opt/itadmin/"
		stapp02 | CHANGED | rc=0 >>
		total 8.0K
		drwxr-xr-x 2 root root 4.0K Jan 10 04:43 .
		drwxr-xr-x 1 root root 4.0K Jan 10 04:43 ..
		stapp03 | CHANGED | rc=0 >>
		total 8.0K
		drwxr-xr-x 2 root root 4.0K Jan 10 04:43 .
		drwxr-xr-x 1 root root 4.0K Jan 10 04:43 ..
		stapp01 | CHANGED | rc=0 >>
		total 8.0K
		drwxr-xr-x 2 root root 4.0K Jan 10 04:43 .
		drwxr-xr-x 1 root root 4.0K Jan 10 04:43 ..

2. Create playbook as per given requirement

thor@jump_host ~/ansible$ vi playbook.yml

thor@jump_host ~/ansible$ cat playbook.yml
		- name: Extract archive

		  hosts: stapp01, stapp02, stapp03

		  become: yes

		  tasks:

		    - name: Extract the archive and set the owner/permissions

		      unarchive:

		        src: /usr/src/itadmin/nautilus.zip

		        dest: /opt/itadmin/

		        owner: "{{ ansible_user }}"

		        group: "{{ ansible_user }}"

		        mode: "0755"

3. Run ansible playbook

thor@jump_host ~/ansible$ ansible-playbook -i inventory playbook.yml 

		PLAY [Extract archive] **********************************************************************************************************************

		TASK [Gathering Facts] **********************************************************************************************************************
		ok: [stapp03]
		ok: [stapp01]
		ok: [stapp02]

		TASK [Extract the archive and set the owner/permissions] ************************************************************************************
		changed: [stapp02]
		changed: [stapp01]
		changed: [stapp03]

		PLAY RECAP **********************************************************************************************************************************
		stapp01                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
		stapp02                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
		stapp03                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

4. Validate task by checking unarchived file on all app servers

thor@jump_host ~/ansible$ ansible -i inventory all -a "ls -ahl /opt/itadmin/"
		stapp01 | CHANGED | rc=0 >>
		total 12K
		drwxr-xr-x 3 root root 4.0K Jan 10 04:46 .
		drwxr-xr-x 1 root root 4.0K Jan 10 04:43 ..
		drwxr-xr-x 2 tony tony 4.0K Jan 10 04:43 unarchive
		stapp02 | CHANGED | rc=0 >>
		total 12K
		drwxr-xr-x 3 root  root  4.0K Jan 10 04:46 .
		drwxr-xr-x 1 root  root  4.0K Jan 10 04:43 ..
		drwxr-xr-x 2 steve steve 4.0K Jan 10 04:43 unarchive
		stapp03 | CHANGED | rc=0 >>
		total 12K
		drwxr-xr-x 3 root   root   4.0K Jan 10 04:46 .
		drwxr-xr-x 1 root   root   4.0K Jan 10 04:43 ..
		drwxr-xr-x 2 banner banner 4.0K Jan 10 04:43 unarchive
thor@jump_host ~/ansible$

---------------------------------------------------------------------------------------------------------------------------------
Task 134: 11/Jan/2023

Persistent Volumes in Kubernetes HTTPD (Task Repeated)

The Nautilus DevOps team is working on a Kubernetes template to deploy a web application on the cluster. There are some requirements to create/use persistent volumes to store the application code, and the template needs to be designed accordingly. Please find more details below:

    Create a PersistentVolume named as pv-xfusion. Configure the spec as storage class should be manual, set capacity to 5Gi, set access mode to ReadWriteOnce, volume type should be hostPath and set path to /mnt/finance (this directory is already created, you might not be able to access it directly, so you need not to worry about it).

    Create a PersistentVolumeClaim named as pvc-xfusion. Configure the spec as storage class should be manual, request 1Gi of the storage, set access mode to ReadWriteOnce.

    Create a pod named as pod-xfusion, mount the persistent volume you created with claim name pvc-xfusion at document root of the web server, the container within the pod should be named as container-xfusion using image httpd with latest tag only (remember to mention the tag i.e httpd:latest).

    Create a node port type service named web-xfusion using node port 30008 to expose the web server running within the pod.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

1. Check kubectl utility for currently running services and pods

thor@jump_host ~$ kubectl get all
		NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
		service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   36m

thor@jump_host ~$ kubectl get pv
		No resources found

thor@jump_host ~$ kubectl get pvc
		No resources found in default namespace.

2. Create YAML file as per requirements 

thor@jump_host ~$ vi /tmp/pvc_httpd.yml

thor@jump_host ~$ cat /tmp/pvc_httpd.yml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-xfusion
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  storageClassName: manual
  hostPath:
    path: /mnt/finance
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-xfusion
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: manual
  resources:
    requests:
      storage: 1Gi
---
apiVersion: v1
kind: Pod
metadata:
  name: pod-xfusion
  labels:
     app: httpd
spec:
  volumes:
    - name: storage-datacenter
      persistentVolumeClaim:
        claimName: pvc-xfusion
  containers:
    - name: container-xfusion
      image: httpd:latest
      ports:
        - containerPort: 80
      volumeMounts:
        - name: storage-datacenter
          mountPath:  /usr/local/apache2/htdocs/
---                                                                                                           
apiVersion: v1                                                                                                
kind: Service                                                                                                 
metadata:                                                                                                     
  name: web-xfusion                                                                                         
spec:                                                                                                         
   type: NodePort                                                                                             
   selector:                                                                                                  
     app: httpd                                                                                     
   ports:                                                                                                     
     - port: 80                                                                                               
       targetPort: 80                                                                                         
       nodePort: 30008
 
3. Create the deployment , pods and persistent volumes

thor@jump_host ~$ kubectl create -f /tmp/pvc_httpd.yml 
		persistentvolume/pv-xfusion created
		persistentvolumeclaim/pvc-xfusion created
		pod/pod-xfusion created
		service/web-xfusion created

4. Wait for pods and services to running status

thor@jump_host ~$ kubectl get all
		NAME              READY   STATUS    RESTARTS   AGE
		pod/pod-xfusion   1/1     Running   0          16s

		NAME                  TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
		service/kubernetes    ClusterIP   10.96.0.1      <none>        443/TCP        38m
		service/web-xfusion   NodePort    10.96.61.213   <none>        80:30008/TCP   16s

thor@jump_host ~$ kubectl get all
		NAME              READY   STATUS    RESTARTS   AGE
		pod/pod-xfusion   1/1     Running   0          21s

		NAME                  TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
		service/kubernetes    ClusterIP   10.96.0.1      <none>        443/TCP        38m
		service/web-xfusion   NodePort    10.96.61.213   <none>        80:30008/TCP   21s

5. Validate Psersistent Volume Claim and Peristent volume 

thor@jump_host ~$ kubectl get pvc
		NAME          STATUS   VOLUME       CAPACITY   ACCESS MODES   STORAGECLASS   AGE
		pvc-xfusion   Bound    pv-xfusion   5Gi        RWO            manual         31s

thor@jump_host ~$ kubectl get pv
		NAME         CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                 STORAGECLASS   REASON   AGE
		pv-xfusion   5Gi        RWO            Retain           Bound    default/pvc-xfusion   manual                  34s

6. Validate task by View Website on right top corner

---------------------------------------------------------------------------------------------------------------------------------
Task 135: 13/Jan/2023

Deploy Apache Web Server on Kubernetes CLuster (Task repeated)

There is an application that needs to be deployed on Kubernetes cluster under Apache web server. The Nautilus application development team has asked the DevOps team to deploy it. We need to develop a template as per requirements mentioned below:

    Create a namespace named as httpd-namespace-devops.

    Create a deployment named as httpd-deployment-devops under newly created namespace. For the deployment use httpd image with latest tag only and remember to mention the tag i.e httpd:latest, and make sure replica counts are 2.

    Create a service named as httpd-service-devops under same namespace to expose the deployment, nodePort should be 30004.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.


1. Check current kubernetes namespace

thor@jump_host ~$ kubectl get namespace
		NAME                 STATUS   AGE
		default              Active   81m
		kube-node-lease      Active   81m
		kube-public          Active   81m
		kube-system          Active   81m
		local-path-storage   Active   81m

2. Create required namespace

thor@jump_host ~$ kubectl create namespace httpd-namespace-devops
		namespace/httpd-namespace-devops created

thor@jump_host ~$ kubectl get namespace
		NAME                     STATUS   AGE
		default                  Active   82m
		httpd-namespace-devops   Active   18s
		kube-node-lease          Active   82m
		kube-public              Active   82m
		kube-system              Active   82m
		local-path-storage       Active   82m

3. Check running pods and services for newly created namespace

thor@jump_host ~$ kubectl get pods -n  httpd-namespace-devops
		No resources found in httpd-namespace-devops namespace.

thor@jump_host ~$ kubectl get service -n  httpd-namespace-devops
		No resources found in httpd-namespace-devops namespace.

4. Create YAML file as pre requirement with configuration for deployment, service and pods

thor@jump_host ~$ vi /tmp/httpd.yaml

thor@jump_host ~$ cat /tmp/httpd.yaml
		---
		apiVersion: v1
		kind: Service
		metadata:
		  name: httpd-service-devops
		  namespace: httpd-namespace-devops
		spec:
		  type: NodePort
		  selector:
		    app: httpd_app_devops
		  ports:
		    - port: 80
		      targetPort: 80
		      nodePort: 30004
		---
		apiVersion: apps/v1
		kind: Deployment
		metadata:
		  name: httpd-deployment-devops
		  namespace: httpd-namespace-devops
		  labels:
		    app: httpd_app_devops
		spec:
		  replicas: 2
		  selector:
		    matchLabels:
		      app: httpd_app_devops
		  template:
		    metadata:
		      labels:
		        app: httpd_app_devops
		    spec:
		      containers:
		        - name: httpd-container-devops
		          image: httpd:latest
		          ports:
		            - containerPort: 80

5. Run the YAML file configuration

thor@jump_host ~$ kubectl create -f /tmp/httpd.yaml
		service/httpd-service-devops created
		deployment.apps/httpd-deployment-devops created

6. Verify service, deployment and pods created and wait for them to get to running status 

thor@jump_host ~$ kubectl get service -n  httpd-namespace-devops
		NAME                   TYPE       CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
		httpd-service-devops   NodePort   10.96.58.40   <none>        80:30004/TCP   5s

thor@jump_host ~$ kubectl get pods -n  httpd-namespace-devops
		NAME                                      READY   STATUS    RESTARTS   AGE
		httpd-deployment-devops-867b499f4-99kpp   1/1     Running   0          13s
		httpd-deployment-devops-867b499f4-dgnfz   1/1     Running   0          13s
thor@jump_host ~$

7. Validate the task by checking website on top right corner

---------------------------------------------------------------------------------------------------------------------------------
Task 136: 14/Jan/2023

Puppet Multi-Packages Installation (Task Repeated)

Some new changes need to be made on some of the app servers in Stratos Datacenter. There are some packages that need to be installed on the app server 3. We want to install these packages using puppet only.

    Puppet master is already installed on Jump Server.

    Create a puppet programming file beta.pp under /etc/puppetlabs/code/environments/production/manifests on master node i.e on Jump Server and perform below mentioned tasks using the same.

    Define a class multi_package_node for agent node 3 i.e app server 3. Install net-tools and unzip packages on the agent node 3.

Notes: :- Please make sure to run the puppet agent test using sudo on agent nodes, otherwise you can face certificate issues. In that case you will have to clean the certificates first and then you will be able to run the puppet agent test.

:- Before clicking on the Check button please make sure to verify puppet server and puppet agent services are up and running on the respective servers, also please make sure to run puppet agent test to apply/test the changes manually first.

1. 1. Switch to root user on jump host to create puppet programming file

thor@jump_host ~$ sudo su - 

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for thor: 

2. Go to mentione folder location and create blog.pp  file with given requirements

root@jump_host ~# cd /etc/puppetlabs/code/environments/production/manifests

root@jump_host /etc/puppetlabs/code/environments/production/manifests# ls -ahl
		total 8.0K
		drwxr-xr-x 1 puppet puppet 4.0K Jul 13  2021 .
		drwxr-xr-x 1 puppet puppet 4.0K Aug  9  2021 ..

root@jump_host /etc/puppetlabs/code/environments/production/manifests# vi beta.pp

root@jump_host /etc/puppetlabs/code/environments/production/manifests# cat beta.pp
		class multi_package_node {
		$multi_package = [ 'net-tools', 'unzip']
		    package { $multi_package: ensure => 'installed' }
		}

		node 'stapp03.stratos.xfusioncorp.com' {
		  include multi_package_node
		}

3. Validate the puppet file
 
root@jump_host /etc/puppetlabs/code/environments/production/manifests# puppet parser validate beta.pp 

4. Login to App Server 3 and switch to root user 
root@jump_host /etc/puppetlabs/code/environments/production/manifests# ssh banner@stapp03
		The authenticity of host 'stapp03 (172.16.238.12)' can't be established.
		ECDSA key fingerprint is SHA256:no5E3rkS5jFnKkqTqKOdwYwHJz71GjgolocMxJ+KsNM.
		ECDSA key fingerprint is MD5:02:15:a7:52:b8:38:e0:78:e8:4a:3a:c7:d3:85:57:a5.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added 'stapp03,172.16.238.12' (ECDSA) to the list of known hosts.
		banner@stapp03's password: 

[banner@stapp03 ~]$ sudo su -

		We trust you have received the usual lecture from the local System
		Administrator. It usually boils down to these three things:

		    #1) Respect the privacy of others.
		    #2) Think before you type.
		    #3) With great power comes great responsibility.

		[sudo] password for banner: 

5.. Check if given packages are already  installed 

[root@stapp03 ~]# rpm -qa|grep -e net-tools -e unzip

6. Run the puppet agent to pull configuration from puppet server

[root@stapp03 ~]# puppet agent -tv
Notice: Run of Puppet configuration client already in progress; skipping  (/opt/puppetlabs/puppet/cache/state/agent_catalog_run.lock exists)

7. Validate the task by checking required packages installed
[root@stapp03 ~]# rpm -qa|grep -e net-tools -e unzip
unzip-6.0-24.el7_9.x86_64
net-tools-2.0-0.25.20131004git.el7.x86_64
[root@stapp03 ~]# 

---------------------------------------------------------------------------------------------------------------------------------
Task 137: 16/Jan/2023

Puppet Setup SSH Keys (Task Repeated)

The Puppet master and Puppet agent nodes have been set up by the Nautilus DevOps team to perform some testing. In Stratos DC all app servers have been configured as Puppet agent nodes. They want to setup a password less SSH connection between Puppet master and Puppet agent nodes and this task needs to be done using Puppet itself. Below are details about the task:

Create a Puppet programming file beta.pp under /etc/puppetlabs/code/environments/production/manifests directory on the Puppet master node i.e on Jump Server. Define a class ssh_node1 for agent node 1 i.e App Server 1, ssh_node2 for agent node 2 i.e App Server 2, ssh_node3 for agent node3 i.e App Server 3. You will need to generate a new ssh key for thor user on Jump Server, that needs to be added on all App Servers.

Configure a password less SSH connection from puppet master i.e jump host to all App Servers. However, please make sure the key is added to the authorized_keys file of each app's sudo user (i.e tony for App Server 1).

Notes: :- Before clicking on the Check button please make sure to verify puppet server and puppet agent services are up and running on the respective servers, also please make sure to run puppet agent test to apply/test the changes manually first.

:- Please note that once lab is loaded, the puppet server service should start automatically on puppet master server, however it can take upto 2-3 minutes to start.



Kindly Check Task 83 for solution

---------------------------------------------------------------------------------------------------------------------------------
Task 138: 18/Jan/2023

Resolve Git Merge Conflicts (Task Repeated)

Sarah and Max were working on writting some stories which they have pushed to the repository. Max has recently added some new changes and is trying to push them to the repository but he is facing some issues. Below you can find more details:

SSH into storage server using user max and password Max_pass123. Under /home/max you will find the story-blog repository. Try to push the changes to the origin repo and fix the issues. The story-index.txt must have titles for all 4 stories. Additionally, there is a typo in The Lion and the Mooose line where Mooose should be Mouse.

Click on the Gitea UI button on the top bar. You should be able to access the Gitea page. You can login to Gitea server from UI using username sarah and password Sarah_pass123 or username max and password Max_pass123.

Note: For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.


Kindly Check Task 91 for solution

---------------------------------------------------------------------------------------------------------------------------------
Task 139: 19/Jan/2023

Ansible Unarchive Module (Task Repeated)

One of the DevOps team members has created an ZIP archive on jump host in Stratos DC that needs to be extracted and copied over to all app servers in Stratos DC itself. Because this is a routine task, the Nautilus DevOps team has suggested automating it. We can use Ansible since we have been using it for other automation tasks. Below you can find more details about the task:

We have an inventory file under /home/thor/ansible directory on jump host, which should have all the app servers added already.

There is a ZIP archive /usr/src/sysops/datacenter.zip on jump host.

Create a playbook.yml under /home/thor/ansible/ directory on jump host itself to perform the below given tasks.

    Unzip /usr/src/sysops/datacenter.zip archive in /opt/sysops/ location on all app servers.

    Make sure the extracted data must has the respective sudo user as their user and group owner, i.e tony for app server 1, steve for app server 2, banner for app server 3.

    The extracted data permissions must be 0644

Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure playbook works this way, without passing any extra arguments.

playbook.yml

- name: Extract archive
  hosts: stapp01, stapp02, stapp03
  become: yes
  tasks:
    - name: Extract the archive and set the owner/permissions
      unarchive:
        src: /usr/src/sysops/datacenter.zip
        dest: /opt/sysops/
        owner: "{{ ansible_user }}"
        group: "{{ ansible_user }}"
        mode: "0644"


Kindly Check Task 123 for solution

---------------------------------------------------------------------------------------------------------------------------------
Task 140: 21/Jan/2023

Pull Docker Image (Task Repeated)

Nautilus project developers are planning to start testing on a new project. As per their meeting with the DevOps team, they want to test containerized environment application features. As per details shared with DevOps team, we need to accomplish the following task:

a. Pull busybox:musl image on App Server 2 in Stratos DC and re-tag (create new tag) this image as busybox:local

Kindly Check Task 112 for solution
---------------------------------------------------------------------------------------------------------------------------------
Task 141: 22/Jan/2023

Run a Docker Container (Task Repeated)

Nautilus DevOps team is testing some applications deployment on some of the application servers. They need to deploy a nginx container on Application Server 2. Please complete the task as per details given below:

    On Application Server 2 create a container named nginx_2 using image nginx with alpine tag and make sure container is in running state.


Kindly Check Task 102 for solution

---------------------------------------------------------------------------------------------------------------------------------
Task 142: 23/Jan/2023

Puppet Create Symlinks (Task Repeated)


Some directory structure in the Stratos Datacenter needs to be changed, there is a directory that needs to be linked to the default Apache document root. We need to accomplish this task using Puppet, as per the instructions given below:

Create a puppet programming file apps.pp under /etc/puppetlabs/code/environments/production/manifests directory on puppet master node i.e on Jump Server. Within that define a class symlink and perform below mentioned tasks:

    Create a symbolic link through puppet programming code. The source path should be /opt/itadmin and destination path should be /var/www/html on Puppet agents 3 i.e on App Servers 3.

    Create a blank file media.txt under /opt/itadmin directory on puppet agent 3 nodes i.e on App Servers 3.

Notes: :- Please make sure to run the puppet agent test using sudo on agent nodes, otherwise you can face certificate issues. In that case you will have to clean the certificates first and then you will be able to run the puppet agent test.

:- Before clicking on the Check button please make sure to verify puppet server and puppet agent services are up and running on the respective servers, also please make sure to run puppet agent test to apply/test the changes manually first.

:- Please note that once lab is loaded, the puppet server service should start automatically on puppet master server, however it can take upto 2-3 minutes to start.

apps.pp

class symlink {
  # First create a symlink to /var/www/html
  file { '/opt/itadmin':
    ensure => 'link',
    target => '/var/www/html',
  }
   # Now create media.txt under /opt/itadmin
  file { '/opt/itadmin/media.txt':
    ensure => 'present',
  }
}

node 'stapp03.stratos.xfusioncorp.com' {
  include symlink
}

Kindly Check Task 53 for solution

---------------------------------------------------------------------------------------------------------------------------------
Task 143: 25/Jan/2023

Deploy Guest Book App on Kubernetes (Task Repeated)

The Nautilus Application development team has finished development of one of the applications and it is ready for deployment. It is a guestbook application that will be used to manage entries for guests/visitors. As per discussion with the DevOps team, they have finalized the infrastructure that will be deployed on Kubernetes cluster. Below you can find more details about it.

BACK-END TIER

    Create a deployment named redis-master for Redis master.

    a.) Replicas count should be 1.

    b.) Container name should be master-redis-devops and it should use image redis.

    c.) Request resources as CPU should be 100m and Memory should be 100Mi.

    d.) Container port should be redis default port i.e 6379.

    Create a service named redis-master for Redis master. Port and targetPort should be Redis default port i.e 6379.

    Create another deployment named redis-slave for Redis slave.

    a.) Replicas count should be 2.

    b.) Container name should be slave-redis-devops and it should use gcr.io/google_samples/gb-redisslave:v3 image.

    c.) Requests resources as CPU should be 100m and Memory should be 100Mi.

    d.) Define an environment variable named GET_HOSTS_FROM and its value should be dns.

    e.) Container port should be Redis default port i.e 6379.

    Create another service named redis-slave. It should use Redis default port i.e 6379.

FRONT END TIER

    Create a deployment named frontend.

    a.) Replicas count should be 3.

    b.) Container name should be php-redis-devops and it should use gcr.io/google-samples/gb-frontend:v4 image.

    c.) Request resources as CPU should be 100m and Memory should be 100Mi.

    d.) Define an environment variable named as GET_HOSTS_FROM and its value should be dns.

    e.) Container port should be 80.

    Create a service named frontend. Its type should be NodePort, port should be 80 and its nodePort should be 30009.

Finally, you can check the guestbook app by clicking on + button in the top left corner and Select port to view on Host 1 then enter your nodePort.

You can use any labels as per your choice.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.


Kindly Check Task 51 for solution

---------------------------------------------------------------------------------------------------------------------------------
Task 144 Onwards : Kodekloud Cheatsheat DevOps Architect Task Commands.txt

---------------------------------------------------------------------------------------------------------------------------------
---------------------------------------------------------------------------------------------------------------------------------
Task 145:

---------------------------------------------------------------------------------------------------------------------------------
Task 146:

---------------------------------------------------------------------------------------------------------------------------------
Task 147:

---------------------------------------------------------------------------------------------------------------------------------
Task 148:

---------------------------------------------------------------------------------------------------------------------------------
Task 149:

---------------------------------------------------------------------------------------------------------------------------------
Task 150:

---------------------------------------------------------------------------------------------------------------------------------
Task 151:
