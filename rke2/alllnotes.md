  sdb:
    pool: lab
    source: k8sw1_lab_disk_2
    type: disk

    admin/kubeadmin123!



  
{
lxc launch ubuntu:20.04 k8sm1 --profile k8sm1 --vm
lxc launch ubuntu:20.04 k8sm2 --profile k8sm2 --vm
lxc launch ubuntu:20.04 k8sm3 --profile k8sm3 --vm
lxc launch ubuntu:20.04 k8sw1 --profile k8sw1 --vm
lxc launch ubuntu:20.04 k8sw2 --profile k8sw2 --vm
lxc launch ubuntu:20.04 k8sw3 --profile k8sw3 --vm
}



cat <<EOF | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list
deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/ /
EOF


cat <<EOF | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable:cri-o:$VERSION.list
deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/$VERSION/$OS/ /
EOF

curl -L https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable:cri-o:$VERSION/$OS/Release.key | sudo apt-key --keyring /etc/apt/trusted.gpg.d/libcontainers.gpg add -
curl -L https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/Release.key | sudo apt-key --keyring /etc/apt/trusted.gpg.d/libcontainers.gpg add -

sudo apt-get update
sudo apt-get install cri-o cri-o-runc



echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list




VERSION=1.23:1.23.0



- path: /etc/apt/sources.list.d/kubernetes.list
  permissions: "0600"
  content: |
    deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main
  owner: root:root


- path: /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list
  permissions: "0600"
  content: |
    deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_20.04/ /
  owner: root:root



- path: /etc/apt/sources.list.d/devel:kubic:libcontainers:stable:cri-o:1.23.list
  permissions: "0600"
  content: |
    deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/1.23:/1.23.0/xUbuntu_20.04/ /
  owner: root:root

        
lxc launch ubuntu:20.04 vanilla --profile vanilla --vm
1.23.3-00


kubeadm=1.23.3-00 kubelet=1.23.3-00 kubectl=1.23.3-00



ETCDCTL_API=3 ./etcdctl member list \
    --cacert /var/lib/rancher/rke2/server/tls/etcd/server-ca.crt \
    --key /var/lib/rancher/rke2/server/tls/etcd/client.key \
    --cert /var/lib/rancher/rke2/server/tls/etcd/client.crt 

43c10f648e47d9f3, started, k8sm1-ba5f3256, https://10.5.100.11:2380, https://10.5.100.11:2379, false
ddaa0a35edf8d629, started, k8sm2-333643b8, https://10.5.100.12:2380, https://10.5.100.12:2379, false



ETCDCTL_API=3 ./etcdctl endpoint status --cluster -w table \
    --cacert /var/lib/rancher/rke2/server/tls/etcd/server-ca.crt \
    --key /var/lib/rancher/rke2/server/tls/etcd/client.key \
    --cert /var/lib/rancher/rke2/server/tls/etcd/client.crt 




alias k='/var/lib/rancher/rke2/bin/kubectl --kubeconfig /etc/rancher/rke2/rke2.yaml'





64.512 – 65.534 – private AS numbers.

      - systemctl enable --now rke2-server
            - curl -sfL https://get.rke2.io | INSTALL_RKE2_CHANNEL=stable  sh -

            curl -sfL https://get.rke2.io |  INSTALL_RKE2_CHANNEL=stable INSTALL_RKE2_TYPE="agent" sh -


      - path: /etc/rancher/rke2/config.yaml 
        owner: root:root
        permission: "0644"
        content: |
          tls-san:
            - rancher-ui.homelab.io

65000 




curl -s https://kube-vip.io/manifests/rbac.yaml > /var/lib/rancher/rke2/server/manifests/kube-vip-rbac.yaml



export VIP=rke2-k8s.homelab.io
export TAG=v0.4.2
export INTERFACE=enp5s0
export CONTAINERD_ADDRESS=/run/k3s/containerd/containerd.sock
export CONTAINER_RUNTIME_ENDPOINT=unix:///run/k3s/containerd/containerd.sock
export PATH=/var/lib/rancher/rke2/bin:$PATH
alias k='/var/lib/rancher/rke2/bin/kubectl --kubeconfig /etc/rancher/rke2/rke2.yaml'
crictl pull  docker.io/plndr/kube-vip:$TAG

alias kube-vip="ctr --namespace k8s.io run --rm --net-host docker.io/plndr/kube-vip:$TAG vip /kube-vip"



export INTERFACE=enp5s0
export VIP rke2-k8s.homelab.io


kube-vip manifest daemonset \
    --interface $INTERFACE \
    --address $VIP \
    --inCluster \
    --taint \
    --controlplane \
    --services \
    --bgp \
    --localAS 65000 \
    --bgpRouterID 192.168.0.2 \
    --bgppeers 10.5.100.11:65000::false,10.5.100.12:65000::false,10.5.100.13:65000::false


apiVersion: apps/v1
kind: DaemonSet
metadata:
  creationTimestamp: null
  labels:
    app.kubernetes.io/name: kube-vip-ds
    app.kubernetes.io/version: v0.4.2
  name: kube-vip-ds
  namespace: kube-system
spec:
  selector:
    matchLabels:
      app.kubernetes.io/name: kube-vip-ds
  template:
    metadata:
      creationTimestamp: null
      labels:
        app.kubernetes.io/name: kube-vip-ds
        app.kubernetes.io/version: v0.4.2
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: node-role.kubernetes.io/master
                operator: Exists
            - matchExpressions:
              - key: node-role.kubernetes.io/control-plane
                operator: Exists
      containers:
      - args:
        - manager
        env:
        - name: vip_arp
          value: "false"
        - name: port
          value: "6443"
        - name: vip_interface
          value: enp5s0
        - name: vip_cidr
          value: "32"
        - name: cp_enable
          value: "true"
        - name: cp_namespace
          value: kube-system
        - name: vip_ddns
          value: "false"
        - name: svc_enable
          value: "true"
        - name: bgp_enable
          value: "true"
        - name: bgp_routerinterface
          value: "enp5s0"
        - name: bgp_as
          value: "65000"
        - name: bgp_peeraddress
        - name: bgp_peerpass
        - name: bgp_peeras
          value: "65000"
        - name: bgp_peers
          value: 10.5.100.11:65000::false,10.5.100.12:65000::false,10.5.100.13:65000::false,10.5.100.254:65300::false
        - name: address
          value: rke2-k8s.homelab.io
        image: ghcr.io/kube-vip/kube-vip:v0.4.2
        imagePullPolicy: Always
        name: kube-vip
        resources: {}
        securityContext:
          capabilities:
            add:
            - NET_ADMIN
            - NET_RAW
      hostNetwork: true
      serviceAccountName: kube-vip
      tolerations:
      - effect: NoSchedule
        operator: Exists
      - effect: NoExecute
        operator: Exists
  updateStrategy: {}
status:
  currentNumberScheduled: 0
  desiredNumberScheduled: 0
  numberMisscheduled: 0
  numberReady: 0



delete protocols bgp 65300 neighbor 10.5.100.13 remote-as 65000


ubnt@ubntr0# commit
[ protocols bgp 65300 ]
Initializing neighbor 10.5.100.11
Initializing neighbor 10.5.100.12
Initializing neighbor 10.5.100.13


configure
set protocols bgp 65200 parameters router-id 10.5.100.254
set protocols bgp 65200 neighbor 10.5.100.11 remote-as 65000
set protocols bgp 65200 neighbor 10.5.100.12 remote-as 65000
set protocols bgp 65200 neighbor 10.5.100.13 remote-as 65000
ubnt@ubntr0# set protocols bgp 65200 maximum-paths ebgp 3
commit
save
exit


ubnt@ubntr0:~$ show ip bgp summary 
BGP router identifier 10.5.100.254, local AS number 65200
BGP table version is 11
1 BGP AS-PATH entries
0 BGP community entries
3  Configured ebgp ECMP multipath: Currently set at 3
1  Configured ibgp ECMP multipath: Currently set at 1

Neighbor                 V   AS   MsgRcv    MsgSen TblVer   InQ   OutQ    Up/Down   State/PfxRcd
10.5.100.11              4 65000  659        651      11      0      0  00:45:06               1
10.5.100.12              4 65000  251        252      11      0      0  00:27:12               1
10.5.100.13              4 65000  276        278      11      0      0  02:16:44               1

Total number of neighbors 3

Total number of Established sessions 3

B    *> 10.5.120.100/32 [20/0] via 10.5.100.13, eth1.100, 00:08:47
     *>                 [20/0] via 10.5.100.12, eth1.100, 00:08:47
     *>                 [20/0] via 10.5.100.11, eth1.100, 00:08:47





apiVersion: v1
kind: ConfigMap
metadata:
  name: kubevip
  namespace: kube-system
data:
  cidr-default: 192.168.0.200/29                      # CIDR-based IP range for use in the default Namespace
  range-development: 192.168.0.210-192.168.0.219      # Range-based IP range for use in the development Namespace
  cidr-finance: 192.168.0.220/29,192.168.0.230/29     # Multiple CIDR-based ranges for use in the finance Namespace
  cidr-global: 192.168.0.240/29                       # CIDR-based range which can be used in any Namespace



Set up a loadbalancer




kubectl apply -f https://raw.githubusercontent.com/kube-vip/kube-vip-cloud-provider/main/manifest/kube-vip-cloud-controller.yaml

kubectl create configmap -n kube-system kubevip --from-literal range-global=10.5.120.150-10.5.120.240










time="2022-03-11T23:43:01Z" level=info msg="Peer Up" Key=10.5.100.254 State=BGP_FSM_OPENCONFIRM Topic=Peer
2022/03/11 23:43:01 conf:<local_as:65000 neighbor_address:"10.5.100.254" peer_as:65300 > state:<local_as:65000 neighbor_address:"10.5.100.254" peer_as:65300 session_state:ESTABLISHED router_id:"10.5.100.254" > transport:<local_address:"10.5.100.11" local_port:34439 remote_port:179 > 


/var/lib/rancher/rke2/server/token  put other nodes.










change 

        - name: bgp_routerid
          value: 192.168.0.2

to

- name: bgp_routerinterface
  value: "enp5s0"











1-10
11-150
151-240
241-254


10.5.120.254
      |
      | 
10.5.100.11
        .12
        .13





kubectl apply -f https://kube-vip.io/manifests/rbac.yaml



https://rancher.com/docs/rke/latest/en/config-options/nodes/


apiVersion: apps/v1
kind: DaemonSet
metadata:
  creationTimestamp: null
  name: kube-vip-ds
  namespace: kube-system
spec:
  selector:
    matchLabels:
      name: kube-vip-ds
  template:
    metadata:
      creationTimestamp: null
      labels:
        name: kube-vip-ds
    spec:
      containers:
      - args:
        - manager
        env:
        - name: vip_arp
          value: "false"
        - name: vip_interface
          value: lo
        - name: port
          value: "6443"
        - name: vip_cidr
          value: "32"
        - name: cp_enable
          value: "true"
        - name: cp_namespace
          value: kube-system
        - name: svc_enable
          value: "true"
        - name: bgp_enable
          value: "true"
        - name: bgp_peers
          value: "10.5.120.254:65000::false,10.5.100.11:65000::false"
        - name: vip_address
          value: 10.5.120.15
        image: ghcr.io/kube-vip/kube-vip:0.3.7
        imagePullPolicy: Always
        name: kube-vip
        resources: {}
        securityContext:
          capabilities:
            add:
            - NET_ADMIN
            - SYS_TIME
      hostNetwork: true
      serviceAccountName: kube-vip
      nodeSelector:
        node-role.kubernetes.io/master: "true"
      tolerations:
      - effect: NoSchedule
        key: node-role.kubernetes.io/master
  updateStrategy: {}

===================================================================================

token: rke2-pre-shared-key
tls-san:
  - k8sm1
  - k8sm1.homelab.io
  - 10.5.100.11
  - rke2-k8s.homelab.io
node-label:
  - node-role.kubernetes.io/control-plane="true"
  - node-role.kubernetes.io/etcd="true"
  - node-role.kubernetes.io/master="true"
disable: rke2-ingress-nginx
cni:
  - cilium

token: rke2-pre-shared-key
server: https://my-kubernetes-domain.com:9345
tls-san:
  - k8sm2
  - k8sm2.homelab.io
  - 10.5.100.12
  - rke2-k8s.homelab.io
node-label:
  - node-role.kubernetes.io/control-plane="true"
  - node-role.kubernetes.io/etcd="true"
  - node-role.kubernetes.io/master="true"
disable: rke2-ingress-nginx
cni:
  - cilium

token: rke2-pre-shared-key
server: https://my-kubernetes-domain.com:9345
tls-san:
  - k8sm3
  - k8sm3.homelab.io
  - 10.5.100.13
  - rke2-k8s.homelab.io
node-label:
  - node-role.kubernetes.io/control-plane="true"
  - node-role.kubernetes.io/etcd="true"
  - node-role.kubernetes.io/master="true"
disable: rke2-ingress-nginx
cni:
  - cilium

Mar  8 21:21:08 k8sm1 rke2[1177]: {"level":"warn","ts":"2022-03-08T21:21:08.903+0100","logger":"etcd-client","caller":"v3@v3.5.1-k3s1/retry_interceptor.go:62","msg":"retrying of unary invoker failed","target":"etcd-endpoints://0xc000458a80/127.0.0.1:2379","attempt":0,"error":"rpc error: code = DeadlineExceeded desc = latest balancer error: last connection error: connection error: desc = \"transport: Error while dialing dial tcp 127.0.0.1:2379: connect: connection refused\""}
Mar  8 21:21:08 k8sm1 rke2[1177]: time="2022-03-08T21:21:08+01:00" level=info msg="Failed to test data store connection: context deadline exceeded"
Mar  8 21:21:23 k8sm1 rke2[1177]: {"level":"warn","ts":"2022-03-08T21:21:23.903+0100","logger":"etcd-client","caller":"v3@v3.5.1-k3s1/retry_interceptor.go:62","msg":"retrying of unary invoker failed","target":"etcd-endpoints://0xc000458a80/127.0.0.1:2379","attempt":0,"error":"rpc error: code = DeadlineExceeded desc = latest balancer error: last connection error: connection error: desc = \"transport: Error while dialing dial tcp 127.0.0.1:2379: connect: connection refused\""}
Mar  8 21:21:23 k8sm1 rke2[1177]: time="2022-03-08T21:21:23+01:00" level=info msg="Failed to test data store connection: context deadline exceeded"
Mar  8 21:21:28 k8sm1 rke2[1177]: time="2022-03-08T21:21:28+01:00" level=info msg="Waiting for API server to become available"
Mar  8 21:21:28 k8sm1 rke2[1177]: time="2022-03-08T21:21:28+01:00" level=info msg="Waiting for etcd server to become available"



apiVersion: kubeadm.k8s.io/v1alpha1
kind: MasterConfiguration
api:
  advertiseAddress: <master-private-ip>
etcd:
  endpoints:
  - http://<master0-ip-address>:2379
  - http://<master1-ip-address>:2379
  - http://<master2-ip-address>:2379
  caFile: /etc/kubernetes/pki/etcd/ca.pem
  certFile: /etc/kubernetes/pki/etcd/client.pem
  keyFile: /etc/kubernetes/pki/etcd/client-key.pem
networking:
  podSubnet: <podCIDR>
apiServerCertSANs:
- <load-balancer-ip>
apiServerExtraArgs:
  endpoint-reconciler-type: lease


helm install rancher rancher-stable/rancher --namespace cattle-system  --set hostname=rancher.homelab.io --set ingress.tls.source=rancher


kubeadmin123!

] Welcome to Rancher
rancher-758ccb55bd-gv74j rancher 2022/03/12 22:47:43 [INFO] A bootstrap password has been generated for your admin user.
rancher-758ccb55bd-gv74j rancher 2022/03/12 22:47:43 [INFO] 
rancher-758ccb55bd-gv74j rancher 2022/03/12 22:47:43 [INFO] Bootstrap Password: b97hpcsscqjvzx9fdhk8rgg4g9vmqbf5698k4g4xkg2f7cd6tfljkk
rancher-758ccb55bd-gv74j rancher 2022/03/12 22:47:43 [INFO] 
rancher-758ccb55bd-gv74j rancher 2022/03/12 22:47:43 [INFO] Use https://10.42.31.4/dashboard/?setup=b97hpcsscqjvzx9fdhk8rgg4g9vmqbf5698k4g4xkg2f7cd6tfljkk to complete setup in the UI
rancher-758ccb55bd-gv74j rancher 2022/03/12 22:47:43 [INFO] -----------------------------------------
rancher-758ccb55bd-gv74j rancher 2022/03/12 22:47:43 [INFO] 








helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

helm install ingress-nginx ingress-nginx/ingress-nginx   --namespace ingress-nginx  -f values.yml 

kubectl taint node k8sm3 node-role.kubernetes.io/master=:NoSchedule


#values.yml

controller:
  kind: DaemonSet
  affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: node-role.kubernetes.io/worker
          operator: Exists
metrics:
  enabled: true



USER-SUPPLIED VALUES:
controller:
  kind: daemonset
  useIngressClassOnly: false
global:
  cattle:
    clusterId: local
    clusterName: local
    rkePathPrefix: ""
    rkeWindowsPathPrefix: ""
    systemDefaultRegistry: ""
    url: https://rancher.homelab.io
  systemDefaultRegistry: ""
imageDefault: true



node-role.kubernetes.io/worker: "true"

{"node-role.kubernetes.io/worker":"true"}


helm install --replace --create-namespace --namespace ingress-nginx ingress-nginx nginx-stable/nginx-ingress   \
      --set rbac.create=true \
      --set controller.healthStatus=true \
      --set prometheus.create=true  \
      --set controller.service.type=NodePort \

      --set controller.service.type=LoadBalancer \
      --set controller.service.loadBalancerIP=10.5.120.150 \




helm install longhorn/longhorn --name longhorn --namespace longhorn-system


helm install longhorn longhorn/longhorn \
  --namespace longhorn-system \
  --create-namespace \
  -f lhvalues.yaml

```yaml

  defaultSettings:
    backupTarget: s3://backupbucket@us-east-1/backupstore
    backupTargetCredentialSecret: minio-secret
    createDefaultDiskLabeledNodes: true
    defaultDataPath: /data/longhorn
    replicaSoftAntiAffinity: false
    storageOverProvisioningPercentage: 600
    storageMinimalAvailablePercentage: 15
    upgradeChecker: false
    defaultReplicaCount: 2
    defaultDataLocality: disabled
    guaranteedEngineCPU:
    defaultLonghornStaticStorageClass: longhorn-static-example
    backupstorePollInterval: 500
    taintToleration: key1=value1:NoSchedule; key2:NoExecute
    systemManagedComponentsNodeSelector: "node-role.kubernetes.io/worker:true"
    priority-class: high-priority
    autoSalvage: false
    disableSchedulingOnCordonedNode: false
    replicaZoneSoftAntiAffinity: false
    volumeAttachmentRecoveryPolicy: never
    nodeDownPodDeletionPolicy: do-nothing
    mkfsExt4Parameters: -O ^64bit,^metadata_csum
    guaranteed-engine-manager-cpu: 15
    guaranteed-replica-manager-cpu: 15

ingress:
  ## Set to true to enable ingress record generation
  enabled: true

  ## Add ingressClassName to the Ingress
  ## Can replace the kubernetes.io/ingress.class annotation on v1.18+
  ingressClassName: nginx

  host: longhorn.homelab.io

```


apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    kubernetes.io/ingress.class: "nginx"
    nginx.ingress.kubernetes.io/auth-type: basic
    nginx.ingress.kubernetes.io/ssl-redirect: 'false'
    nginx.ingress.kubernetes.io/auth-secret: basic-auth
    nginx.ingress.kubernetes.io/auth-realm: 'Authentication Required '
    nginx.ingress.kubernetes.io/proxy-body-size: 10000m
  name: longhorn-ingress
  namespace: longhorn-system
spec:
  rules:
  - host: longhorn-ui.homelab.io
    http:
      paths:
      - backend:
          service:
            name: longhorn-frontend
            port:
              number: 80
        path: /
        pathType: Prefix


```yaml
kubectl get secrets -n longhorn-system minio-secret -oyaml
apiVersion: v1
data:
  AWS_ACCESS_KEY_ID: bG9uZ2hvcm4=
  AWS_CERT: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURrVENDQW5tZ0F3SUJBZ0lVTnpTTjk0L3dEbkljdy8zbzRqek55ZVY3aEM4d0RRWUpLb1pJaHZjTkFRRUwKQlFBd2J6RUxNQWtHQTFVRUJoTUNSRVV4RHpBTkJnTlZCQWdNQmtobGMzTmxiakVTTUJBR0ExVUVCd3dKUkdGeQpiWE4wWVdSME1STXdFUVlEVlFRS0RBcHRZVzVwYm5Sb1pXbDBNUXN3Q1FZRFZRUUxEQUpKVkRFWk1CY0dBMVVFCkF3d1FiV2x1YVc4dWFHOXRaV3hoWWk1cGJ6QWVGdzB5TWpBeE16QXhPVFUzTVRCYUZ3MHlOREF4TXpBeE9UVTMKTVRCYU1HOHhDekFKQmdOVkJBWVRBa1JGTVE4d0RRWURWUVFJREFaSVpYTnpaVzR4RWpBUUJnTlZCQWNNQ1VSaApjbTF6ZEdGa2RERVRNQkVHQTFVRUNnd0tiV0Z1YVc1MGFHVnBkREVMTUFrR0ExVUVDd3dDU1ZReEdUQVhCZ05WCkJBTU1FRzFwYm1sdkxtaHZiV1ZzWVdJdWFXOHdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUsKQW9JQkFRQzJXZWdITFplY3JOS2dZZ2ZtaEVDdnNuTXRpZlB1WWZySkpyU0Rrdjl0U2pkN1RWSzJiNitjMlhxNwpVR3E1K3ZRNWY5N3hHTnB1YWNmejBsR0VjeE55aXdKUFJXNG5zTWhWTlM4b2F1YWhEVEh1cHIxT3c5WHE2cHJlCkNNa2VCVlpYVy91OGJRNEJWRlBJQk9Fd3BBd0JDQkhLSFBNK25kdzBheElrVjJVRmh2VThJVUp3REJhalNaaDMKY0lNTjcyc2Zyb3YxMENrU25QQlFMaTVZaVl2UFVCcm1QOW9VWWpBUzFqZ2JrbGNEeVArN25uem80RFBtdUdISAozOWh2ME5DdURTdHk2aUY0WWZCR3k0b1UxWEFzSXNrdDNqZ0VNc0c1d2JsZmJuU0RrQkJTQ0p0WkdaT0JHb2MxCkxlY3pJVGtDOTI4VFZlUjB3c29BcjNUaCtrMWxBZ01CQUFHakpUQWpNQ0VHQTFVZEVRUWFNQmlIQkFvRlpBR0MKRUcxcGJtbHZMbWh2YldWc1lXSXVhVzh3RFFZSktvWklodmNOQVFFTEJRQURnZ0VCQUdEMjhRUzliT2ZndGtpdQovMGszYzZhcGZDR1lRNjRRRm5ubURxcFovSWU1RDVNZm1zck10aUhWcUVQL3BlMGswcWxrWVdVN00rM0gxNnNXClJQWlFsNVJqWG92a0c2UkNWQ2dVQTdOZDNmSDBBMzg0YkxzckpEUStzUlp4OWZPdTN1ZXZNTmxaSDNuVVVwOFgKV3A1U1RJOGNMSUdDaWVhVHZveGc5ejBYNnRrTm9qemppRWRFdmt2MHAwWGdlRzdiSXpFWHlMTGd3bnJTNmVRaApnOWpnZ2s3bnNIVmR6K2JRcVpXVHJqOWgyWTNRc0lnZVRiOHEyVXNRTThuUkl3ekI0cmxndEtXNklXTkF6WXI3CjJ6UnFFN1FaM2p3TnRBSHlJWExISUxvYTUrY1hWYnh4R2lQbkpMTkcyTmw1bmlSdER1TmRnTittTGYwQVQrK1MKUkMwVVdkcz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
  AWS_ENDPOINTS: aHR0cHM6Ly9taW5pby5ob21lbGFiLmlvOjkwMDA=
  AWS_SECRET_ACCESS_KEY: bG9uZ2hvcm4=
kind: Secret
  name: minio-secret
  namespace: longhorn-system
type: Opaque
```


wordpress /admin/admin




apiVersion: v1
items:
- apiVersion: apps/v1
  kind: DaemonSet
  metadata:
    annotations:
      deprecated.daemonset.template.generation: "23"
      objectset.rio.cattle.io/applied: H4sIAAAAAAAA/7xVwW7jNhD9lWLOslZ25HRXQA9Gk8OiXWOxCdrDwghG5EhmTZEsOVJiBPr3grLjKI6dTXvojeK8eZwZ8j09Ajr1B/mgrIEC0LnwoZtCAhtlJBRwhdRYc0MMCTTEKJERikdAYywjK2tC/LTlXyQ4EKde2VQgs6ZU2Q8qckByNm7vDflJ3W2ggM1FGEW6afLTb8rIXxZSWvNDCoMNRY62pEmn3LvwwaE4JIVtYGqgT0B4Ghq7VQ0FxsZBYVqtE9BYkh7aRefSmOUNMYXIenT+RAZITsC6w6S7LM3T2Rt1rjGsoQBZfZzPc0koso/VvKzKy+yTzD7lP2cSp7M8l1jmOMtnsfKTRbzRaXAkYjuBNAm2Pq4bZLH+/d912vcJMDVOI9PAMXoo/+s0+1FTWFXKKN7GtbGSFqNvT3+3ypO8ar0y9Y1Yk2y1MvXn2tjD9vUDiZYH/h3DzX5Mt+SbAMX3/bCuH5ynEHZS+P4IG9pCMSRMvNV0VHODgcnHe3fkcRg6XD+owAH6VZ/8J05hDXurJ06joXPUqz5OJ0JRGfI7XvR1XECDBmvysEqATDeE9nfQKXeHPgqqQ93GnQp1IIil7iHOeh7FL/P8YhyODNrWmjrSI9j8GKMMk6/iQ30GaXuMEkr6EeBiNgYId0cGSz2mYN/SEeZZEs+wl9J4caSUJrzVf+jEj48t63fUFkHetkz+1DDIuHnIjvE4ru1ynmWvEI7Io5TxQZ0KOTy9f0Q8O008Bk2zdJ5OsyydzfNiyCiKV9N6quQ4bZbFVOhXCagG6xio18LH9/3kA4dFcXDPAfq11fqr1UpEkSz0PW6fjO/lL8FTsK0XFF0nOgWJ1ive/moN0wMPboUOS6UVK9pZk5RRHsvr27vF1ZfPS0iG9bfFnxD1tEpgbQMvie+t30AR7zPy+k4JWghhW8PLV2Ww1VGfB3FTVZFgKGBp91Z0RsTJC+zOn87qvU+gdRKZbtgjUx2Nb/BHRm6H5kTrPRletk1J/ulkCUWWgKQQ/fFUyAx7X1QIJ7a/EcotFFnf/xMAAP//R7ahUlkIAAA
      objectset.rio.cattle.io/id: ""
      objectset.rio.cattle.io/owner-gvk: k3s.cattle.io/v1, Kind=Addon
      objectset.rio.cattle.io/owner-name: kube-vip
      objectset.rio.cattle.io/owner-namespace: kube-system
    creationTimestamp: "2022-03-11T23:42:03Z"
    generation: 23
    labels:
      app.kubernetes.io/name: kube-vip-ds
      app.kubernetes.io/version: v0.4.2
      objectset.rio.cattle.io/hash: df8554deac08f5bfb609d09470da1244dab4a242
    name: kube-vip-ds
    namespace: kube-system
    resourceVersion: "977311"
    uid: eaa81ec4-ef39-4c4b-9968-bdec9fd56127
  spec:
    revisionHistoryLimit: 10
    selector:
      matchLabels:
        app.kubernetes.io/name: kube-vip-ds
    template:
      metadata:
        creationTimestamp: null
        labels:
          app.kubernetes.io/name: kube-vip-ds
          app.kubernetes.io/version: v0.4.2
      spec:
        affinity:
          nodeAffinity:
            requiredDuringSchedulingIgnoredDuringExecution:
              nodeSelectorTerms:
              - matchExpressions:
                - key: node-role.kubernetes.io/master
                  operator: Exists
              - matchExpressions:
                - key: node-role.kubernetes.io/control-plane
                  operator: Exists
        containers:
        - args:
          - manager
          env:
          - name: vip_arp
            value: "false"
          - name: port
            value: "6443"
          - name: vip_loglevel
            value: "5"
          - name: vip_interface
            value: lo
          - name: vip_cidr
            value: "32"
          - name: cp_enable
            value: "true"
          - name: cp_namespace
            value: kube-system
          - name: vip_ddns
            value: "false"
          - name: svc_enable
            value: "true"
          - name: bgp_enable
            value: "true"
          - name: bgp_routerinterface
            value: enp5s0
          - name: bgp_as
            value: "65000"
          - name: bgp_peeraddress
          - name: bgp_peerpass
          - name: bgp_peeras
            value: "65200"
          - name: bgp_peers
            value: 10.5.100.254:65200::false
          - name: address
            value: 10.5.120.100
          image: ghcr.io/kube-vip/kube-vip:v0.4.2
          imagePullPolicy: Always
          name: kube-vip
          resources: {}
          securityContext:
            capabilities:
              add:
              - NET_ADMIN
              - NET_RAW
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
        dnsPolicy: ClusterFirst
        hostNetwork: true
        restartPolicy: Always
        schedulerName: default-scheduler
        securityContext: {}
        serviceAccount: kube-vip
        serviceAccountName: kube-vip
        terminationGracePeriodSeconds: 30
        tolerations:
        - effect: NoSchedule
          operator: Exists
        - effect: NoExecute
          operator: Exists
    updateStrategy:
      rollingUpdate:
        maxSurge: 0
        maxUnavailable: 1
      type: RollingUpdate
  status:
    currentNumberScheduled: 3
    desiredNumberScheduled: 3
    numberAvailable: 3
    numberMisscheduled: 0
    numberReady: 3
    observedGeneration: 23
    updatedNumberScheduled: 3
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""


















apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: longhorn-ingress
  namespace: longhorn-system
  annotations:
    # type of authentication
    nginx.ingress.kubernetes.io/auth-type: basic
    # name of the secret that contains the user/password definitions
    nginx.ingress.kubernetes.io/auth-secret: basic-auth
    # message to display with an appropriate context why the authentication is required
    nginx.ingress.kubernetes.io/auth-realm: 'Authentication Required '
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: longhorn-frontend
          servicePort: 80



apiVersion: v1
metadata:
  name: longhorn-ingress
  namespace: longhorn-system
items:
- apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    annotations:
      nginx.ingress.kubernetes.io/auth-type: basic
      nginx.ingress.kubernetes.io/auth-secret: basic-auth
      nginx.ingress.kubernetes.io/auth-realm: 'Authentication Required '
  spec:
    rules:
    - host: longhorn-frontend.10.5.100.17.nip.io
      http:
        paths:
        - backend:
            service:
              name: longhorn-frontend
              port:
                number: 80
          path: /
          pathType: Prefix






apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
  name: longhorn-sc
parameters:
  fromBackup: ""
  fsType: ext4
  numberOfReplicas: "2"
  staleReplicaTimeout: "30"
  nodeSelector: "storage"
provisioner: driver.longhorn.io
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true


Stop editing your /etc/hosts file
https://nip.io/

10.0.0.1.nip.io maps to 10.0.0.1
192-168-1-250.nip.io maps to 192.168.1.250


kubectl get ingress -n longhorn-system 
longhorn-ingress   public   longhorn-frontend.10.5.100.17.nip.io   127.0.0.1   80      45h

dig +short longhorn-frontend.10.5.100.17.nip.io 
10.5.100.17




#longhorn
--set persistence.defaultClass=false




apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  namespace: wordpress
  namespace: wordpress
spec:
  ingressClassName: public
  rules:
  - host: wordpress-10.5.100.16.nip.io
    http:
      paths:
      - backend:
          service:
            name: wordpress
            port:
              name: http
        path: /
        pathType: Prefix



/	250 MB
/usr	250 MB
/tmp	50 MB
/var	384 MB
/home	100 MB
/boot	250 MB



/ 2 GiB
/usr 2BiB
/var 3GiB
/home 2GiB
/boot 1GiB
/var/lib/docker 5GiB

- install ntp and ntp server
- net.bridge.bridge-nf-call-iptables=1

cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# Setup required sysctl params, these persist across reboots.
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system


curl https://releases.rancher.com/install-docker/20.10.sh | sh



c



error listing backup volume names: Failed to execute: /var/lib/longhorn/engine-binaries/longhornio-longhorn-engine-v1.2.3/longhorn 
[backup ls --volume-only s3://wordpress@homelab], output Invalid URL. Must be either s3://bucket@region/path/, or s3://bucket/path , 
stderr, time="2022-03-20T09:47:58Z" level=error msg="Invalid URL. 
Must be either s3://bucket@region/path/, or s3://bucket/path" , error exit status 1



kubectl label nodes k8sw1 node.longhorn.io/create-default-disk=true


createDefaultDiskLabeledNodes true



helm install longhorn longhorn/longhorn \
  --namespace longhorn-system \
  --values values.yaml


USER=longhorn; PASSWORD=longhorn; echo "${USER}:$(openssl passwd -stdin -apr1 <<< ${PASSWORD})" >> auth
kubectl -n longhorn-system create secret generic basic-auth --from-file=auth


apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: longhorn-ingress
  namespace: longhorn-system
  annotations:
    # type of authentication
    nginx.ingress.kubernetes.io/auth-type: basic
    # prevent the controller from redirecting (308) to HTTPS
    nginx.ingress.kubernetes.io/ssl-redirect: 'false'
    # name of the secret that contains the user/password definitions
    nginx.ingress.kubernetes.io/auth-secret: basic-auth
    # message to display with an appropriate context why the authentication is required
    nginx.ingress.kubernetes.io/auth-realm: 'Authentication Required '
    # custom max body size for file uploading like backing image uploading
    nginx.ingress.kubernetes.io/proxy-body-size: 10000m
spec:
  rules:
  - http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: longhorn-frontend
            port:
              number: 80






velero

kubectl exec -c velero -n velero -it  velero-67554d854-zjbb4  -- /velero backup  create wordpress-20220331-3 --include-namespaces wordpress  --snapshot-volumes=true --storage-location=backups --volume-snapshot-locations=snapshots --default-volumes-to-restic=true


kubectl exec -c velero -n velero -it  velero-67554d854-zjbb4  -- /velero  backup describe  wordpress-20220331-3 --details --insecure-skip-tls-verify






kubectl exec -c velero -n velero -it  velero-67554d854-zjbb4  -- /velero restore  create --from-backup wordpress-20220331-2

sudo ovs-vsctl add-port ovslxd0 enp5s0



create user -> 
configure kube-vip -> 

s3://wordpress@homelab/
minio-secret


cat <<EOF | kubectl apply -f -
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: gokay
spec:
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ2x6Q0NBWDhDQVFBd1VqRUxNQWtHQTFVRUJoTUNXRmd4RlRBVEJnTlZCQWNNREVSbFptRjFiSFFnUTJsMAplVEVjTUJvR0ExVUVDZ3dUUkdWbVlYVnNkQ0JEYjIxd1lXNTVJRXgwWkRFT01Bd0dBMVVFQXd3RloyOXJZWGt3CmdnRWlNQTBHQ1NxR1NJYjNEUUVCQVFVQUE0SUJEd0F3Z2dFS0FvSUJBUURDRGhoOXZnOGFRd0ZlTzRYQ3lyd2gKV1JxWUtIdkJJOThOV2lGdlhPYUVjZFF5UUhYMEpIa1hmVlhoRTkwam1Kd0tGYTU0RHNLYXRZNHA4bkdGM1lLbQo1bzhURldnb3FmSmpPUTF3aU9mc2NVR1g3Ym8wVHNQVnpmeDYxS09NV3ROQk8vSXJSQ08zRkVjVzl2Sm5xZS9CCnhrOXhmRTFuM3hDTkJrRUh6TFhKdlNUeHZUK2Q5UUFmVVBsR0NUSHVNUWFQZGZ4OFQrS2VlTUJaRjN2cXNXbmYKZ0o5YnJSZU00QnBlNXB2VTZrWXliVXN6MWJPTWI5RnBRMjdNRmxBblB6THBxYUdqTUlhZGIrd3N0eHRCS0pESApXQjdjemJNNGVyMnd0c2syL3h4cjIwdStIV1NhVWFMWWdoL01ManNwVWI1VFlBcVM1NmlTNm5CZXduT3ZJVGtwCkFnTUJBQUdnQURBTkJna3Foa2lHOXcwQkFRc0ZBQU9DQVFFQXM5bERvY1crZ0ZUZ29XendWYkV4VVVtbGVLYjIKR0EydGcwaWlPUE5BeEtYN2NYK1ZidEg5QXRIcmhacnlzVFVQSzkrcjFmRTNmM3Q4WjlwaThqSE84ZWF1QzlhdApnSEFZZk5Qc3Y4SWxJZEp5dFQ3eXZSUThLU1pKOWdGMk9UeVZ6ZXpPdFZwRGp3b1Y2djEyckVML1RDWFVGalVsCnhZcWlyMzBoNWpTSyt3aUVKWUFzTUhOLy9LSFJOenk1R1E0WjNqMy8xWW5RWXNGUVBTdnhFa2lvV2R1ekkvL3cKZ3BXR0dSb1QvTlNMVkhERnQ5cHdEcUNNSjk3VkpYc1pscWFWNFUzSkxkckNMZG40WjY2RmwvQVZNcmg3blI1cQpKZXZGVGZxTlRVZEJwZkJSaE1uVTVVODAvWVBIREpQd0tyU2oxTUREUWJ2c2RIUlcwcmVyODFPaWxBPT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUgUkVRVUVTVC0tLS0tCg==
  signerName: kubernetes.io/kube-apiserver-client
  expirationSeconds: 4000
  usages:
  - client auth
EOF


LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ2x6Q0NBWDhDQVFBd1VqRUxNQWtHQTFVRUJoTUNXRmd4RlRBVEJnTlZCQWNNREVSbFptRjFiSFFnUTJsMAplVEVjTUJvR0ExVUVDZ3dUUkdWbVlYVnNkQ0JEYjIxd1lXNTVJRXgwWkRFT01Bd0dBMVVFQXd3RloyOXJZWGt3CmdnRWlNQTBHQ1NxR1NJYjNEUUVCQVFVQUE0SUJEd0F3Z2dFS0FvSUJBUURDRGhoOXZnOGFRd0ZlTzRYQ3lyd2gKV1JxWUtIdkJJOThOV2lGdlhPYUVjZFF5UUhYMEpIa1hmVlhoRTkwam1Kd0tGYTU0RHNLYXRZNHA4bkdGM1lLbQo1bzhURldnb3FmSmpPUTF3aU9mc2NVR1g3Ym8wVHNQVnpmeDYxS09NV3ROQk8vSXJSQ08zRkVjVzl2Sm5xZS9CCnhrOXhmRTFuM3hDTkJrRUh6TFhKdlNUeHZUK2Q5UUFmVVBsR0NUSHVNUWFQZGZ4OFQrS2VlTUJaRjN2cXNXbmYKZ0o5YnJSZU00QnBlNXB2VTZrWXliVXN6MWJPTWI5RnBRMjdNRmxBblB6THBxYUdqTUlhZGIrd3N0eHRCS0pESApXQjdjemJNNGVyMnd0c2syL3h4cjIwdStIV1NhVWFMWWdoL01ManNwVWI1VFlBcVM1NmlTNm5CZXduT3ZJVGtwCkFnTUJBQUdnQURBTkJna3Foa2lHOXcwQkFRc0ZBQU9DQVFFQXM5bERvY1crZ0ZUZ29XendWYkV4VVVtbGVLYjIKR0EydGcwaWlPUE5BeEtYN2NYK1ZidEg5QXRIcmhacnlzVFVQSzkrcjFmRTNmM3Q4WjlwaThqSE84ZWF1QzlhdApnSEFZZk5Qc3Y4SWxJZEp5dFQ3eXZSUThLU1pKOWdGMk9UeVZ6ZXpPdFZwRGp3b1Y2djEyckVML1RDWFVGalVsCnhZcWlyMzBoNWpTSyt3aUVKWUFzTUhOLy9LSFJOenk1R1E0WjNqMy8xWW5RWXNGUVBTdnhFa2lvV2R1ekkvL3cKZ3BXR0dSb1QvTlNMVkhERnQ5cHdEcUNNSjk3VkpYc1pscWFWNFUzSkxkckNMZG40WjY2RmwvQVZNcmg3blI1cQpKZXZGVGZxTlRVZEJwZkJSaE1uVTVVODAvWVBIREpQd0tyU2oxTUREUWJ2c2RIUlcwcmVyODFPaWxBPT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUgUkVRVUVTVC0tLS0tCg==



openssl req -nodes -newkey rsa:2048 -keyout gokay.key -out gokay.csr -subj "/C=/ST=/L=/O=/OU=/CN=gokay"
$ openssl req -nodes -newkey rsa:2048 -keyout example.key -out example.csr -subj "/C=/ST=/L=/O=/OU=/CN=gokay"



    kube-controller:
      extra_args:
        cluster-signing-cert-file: /etc/kubernetes/ssl/kube-ca.pem
        cluster-signing-key-file: /etc/kubernetes/ssl/kube-ca-key.pem

helm repo add rancher-stable  


data:
  AWS_ACCESS_KEY_ID: bG9uZ2hvcm4=
  AWS_CERT: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURrVENDQW5tZ0F3SUJBZ0lVTnpTTjk0L3dEbkljdy8zbzRqek55ZVY3aEM4d0RRWUpLb1pJaHZjTkFRRUwKQlFBd2J6RUxNQWtHQTFVRUJoTUNSRVV4RHpBTkJnTlZCQWdNQmtobGMzTmxiakVTTUJBR0ExVUVCd3dKUkdGeQpiWE4wWVdSME1STXdFUVlEVlFRS0RBcHRZVzVwYm5Sb1pXbDBNUXN3Q1FZRFZRUUxEQUpKVkRFWk1CY0dBMVVFCkF3d1FiV2x1YVc4dWFHOXRaV3hoWWk1cGJ6QWVGdzB5TWpBeE16QXhPVFUzTVRCYUZ3MHlOREF4TXpBeE9UVTMKTVRCYU1HOHhDekFKQmdOVkJBWVRBa1JGTVE4d0RRWURWUVFJREFaSVpYTnpaVzR4RWpBUUJnTlZCQWNNQ1VSaApjbTF6ZEdGa2RERVRNQkVHQTFVRUNnd0tiV0Z1YVc1MGFHVnBkREVMTUFrR0ExVUVDd3dDU1ZReEdUQVhCZ05WCkJBTU1FRzFwYm1sdkxtaHZiV1ZzWVdJdWFXOHdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUsKQW9JQkFRQzJXZWdITFplY3JOS2dZZ2ZtaEVDdnNuTXRpZlB1WWZySkpyU0Rrdjl0U2pkN1RWSzJiNitjMlhxNwpVR3E1K3ZRNWY5N3hHTnB1YWNmejBsR0VjeE55aXdKUFJXNG5zTWhWTlM4b2F1YWhEVEh1cHIxT3c5WHE2cHJlCkNNa2VCVlpYVy91OGJRNEJWRlBJQk9Fd3BBd0JDQkhLSFBNK25kdzBheElrVjJVRmh2VThJVUp3REJhalNaaDMKY0lNTjcyc2Zyb3YxMENrU25QQlFMaTVZaVl2UFVCcm1QOW9VWWpBUzFqZ2JrbGNEeVArN25uem80RFBtdUdISAozOWh2ME5DdURTdHk2aUY0WWZCR3k0b1UxWEFzSXNrdDNqZ0VNc0c1d2JsZmJuU0RrQkJTQ0p0WkdaT0JHb2MxCkxlY3pJVGtDOTI4VFZlUjB3c29BcjNUaCtrMWxBZ01CQUFHakpUQWpNQ0VHQTFVZEVRUWFNQmlIQkFvRlpBR0MKRUcxcGJtbHZMbWh2YldWc1lXSXVhVzh3RFFZSktvWklodmNOQVFFTEJRQURnZ0VCQUdEMjhRUzliT2ZndGtpdQovMGszYzZhcGZDR1lRNjRRRm5ubURxcFovSWU1RDVNZm1zck10aUhWcUVQL3BlMGswcWxrWVdVN00rM0gxNnNXClJQWlFsNVJqWG92a0c2UkNWQ2dVQTdOZDNmSDBBMzg0YkxzckpEUStzUlp4OWZPdTN1ZXZNTmxaSDNuVVVwOFgKV3A1U1RJOGNMSUdDaWVhVHZveGc5ejBYNnRrTm9qemppRWRFdmt2MHAwWGdlRzdiSXpFWHlMTGd3bnJTNmVRaApnOWpnZ2s3bnNIVmR6K2JRcVpXVHJqOWgyWTNRc0lnZVRiOHEyVXNRTThuUkl3ekI0cmxndEtXNklXTkF6WXI3CjJ6UnFFN1FaM2p3TnRBSHlJWExISUxvYTUrY1hWYnh4R2lQbkpMTkcyTmw1bmlSdER1TmRnTittTGYwQVQrK1MKUkMwVVdkcz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
  AWS_ENDPOINTS: aHR0cHM6Ly9taW5pby5ob21lbGFiLmlvOjkwMDA=
  AWS_SECRET_ACCESS_KEY: bG9uZ2hvcm4=
kind: Secret
metadata:





openssl req -nodes -newkey rsa:2048 -keyout devuser.key -out devuser.csr -subj "/C=/ST=/L=/O=/OU=/CN=devuser"



apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: devuser
spec:
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1Z6Q0NBVDhDQVFBd0VqRVFNQTRHQTFVRUF3d0haR1YyZFhObGNqQ0NBU0l3RFFZSktvWklodmNOQVFFQgpCUUFEZ2dFUEFEQ0NBUW9DZ2dFQkFMTU5pOG1XdndQb3RxcEhpeVVmc01nVlJXdzFyYzFXeWRnUVhkanJXTHU5ClJVcFJMdFBDUXN2TXNkRnBIb3pZVnI4OEszSk5XVEZsaXVDbnUrZUVBcVN5YWhLZ2hnUE8rbVRXVjhzM0pWYmQKYXJ1VHdPa0R6ZDN5VkVBU3I2UEpBNE1aaXFJVkNWYi9EeFNRU2xaUSs3YkJVMEYyY3BMaXBPUWxnSTNNd0VvYgpHTC9QNWNKVjhOL1c1dmxnbElrbDlibHROUFpwR2Yza0EzaVlQY1NiZkl6SDE3RzBUeVlWNTdUbHVtbUE1NUswClFYVmV4Uk1hTFJiWW94U3JZeTY5Z0txZFpMd1FxaXVVeTZEMVBLQ3ZsS3VDSlhGMDhmU21tK1hvSnNPVnhVS0cKSFBReHBBY2NrUHBaYnJUMlRwMzZoamZOeGdtT1BjbjJTYXVjaVA0Nk8rMENBd0VBQWFBQU1BMEdDU3FHU0liMwpEUUVCQ3dVQUE0SUJBUUFFT0YrNDNubzRxUnB1bEdEQnc1V091TW95R2F3aFdJcjNmbHAySzZsZjErZ3g1SHoyCmVITEFyTzFEOUFUR010V3dVUVBVaUduRUp3OHZGdFdSQjBGUzlyVXRnTUFJMmJ4a1dqQlA4bkxkallldDZDWVoKczlTY1kxZlU5bTkwUkhxdld6RVBZT1MvZUljTTdOYkRVOHFRZ1FEQWlCYmNMU1dnNFJhcDZzd2gvWGxkekdzaAp1VzRaNm1yUGt6M21ITCt5OEM4L0hNemwrNDZxZEdjZVIwUG1XZjhOa1oxS01ObVdUVmNGaytTUTlRamwxcnFiCnA1c2wwL3lFS1BvMzNrTE9yL1RQa2VmOWlEeTRINFVWU3M0ZlgxQVBnb1A5MjN6STZ0SnhHM3RucDJEeWl4UTYKQm1uMVZJNDhNYTMyYzF2YkJXQzZsNDNMSkhQS3hCcWFKWmxaCi0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=
  signerName: kubernetes.io/kube-apiserver-client
  expirationSeconds: 86400
  usages:
  - client auth


