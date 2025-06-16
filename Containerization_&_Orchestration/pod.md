#### Pod

  -  POD is a smallest building block in k8s cluster
  -  In K8S, every container will be created inside POD only
  -  POD always runs inside Node
  -  POD represents running process
  -  POD means group of containers running on a Node
  -  We can create multiple PODS on single node
  -  Every POD will have unique IP address
  -  We can create PODS in 2 ways
  - 1) Interactive Approach
         -  $ kubectl run --name <pod-name> image=<image-name> --generate=pod/v1
  - 2) Declarative Approach ( K8S Manifest YML )

#### POD Life Cycle

- POD is a smallest building block that we can run in k8s cluster
- We are using kubectl to send request to control plane to create POD
- API Server will recieve our request and will store request details in ETCD.
- Schedular will find un-scheduled PODS and it will schedule in worker nodes
- Node Agent (kubelet) will see POD schedule and it will fire Docker Engine
- Docker will engine will run our container
  - Note: PODS are ephemeral (Short Lived Objects)
