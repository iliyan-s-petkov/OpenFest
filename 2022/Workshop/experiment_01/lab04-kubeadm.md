Чрез `kubeadm` може да се създаде работещ K8s клъстър следвайки най-добрите практики.

kubeadm е подходящ за:
* изпробване на K8s дори и за пръв път
* автоматизиране на създаване на нови клъстри и тестване на приложения в тях 
* използване като градивен блок в други екосистеми и/или инсталационен инструмент в по-големи среди

## Създаване на K8s клъстър със kubeadm

### Mинимални изисквания
* Една или повече машини работещи със dev/rpm съвместима операционнна система - например Ubuntu ili CentOS
* 2GB или повече RAM
* 2 или повече ядра на машините изполвани като част от control-plane 
* Пълна мрежова свързаност между машинитe в клъстъра
* SWAP трябва да е забранен на машините
* всяка машина трябва да има уникален hosname & mac

### Подготовка на машините
* изключване на swap. Ако swap е разрешен на машината на която ще инсталираме  К8s, това би повлияло на рабоата на K8s scheduler 

Проверка дали има разрешен swap:
```shell
 monster@elmo:~$ swapon
NAME      TYPE SIZE USED PRIO
/swap.img file 1.4G   0B   -2

```
Изключване на swap:
```shell
monster@elmo:~$ sudo swapoff -a
monster@elmo:~$
```
Можем да потвърдим:
```shell
monster@elmo:~$ cat /proc/swaps
Filename				Type		Size		Used		Priority
```
 За да предотвратим разрешаването на swap при следващо стартиране на машината, трябва да редактираме: /etc/fstab (може и ръчно чрез vim)
```shell
sudo sed -i '/\/swap.img/ s/^#*/#/' /etc/fstab
``` 

Преди да започнем с инсталирацията, нека се убедим че имаме последните версии на пакетите:
```shell
sudo apt update
sudo apt upgrade -y
```
Инсталиране на необходими пакети:
```shell
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg lsb-release
```

Инсталиране на Google Cloud public signing key
```shell
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
```

Добавяме K8s APT репозиторито 
```shell
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
обновяваме индекса на пакетите, инсталираме kublet, kubeadm и kubectl:
```shell
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```
 ako сега проверим ще видим че kublet процеса непрекъснато се рестартира:
```shell
monster@elmo:~$ systemctl status kubelet -l
● kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /etc/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: activating (auto-restart) (Result: exit-code) since Sat 2022-10-15 20:34:47 UTC; 9s ago
       Docs: https://kubernetes.io/docs/home/
    Process: 13686 ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXT
RA_ARGS (code=exited, status=1/FAILURE)
   Main PID: 13686 (code=exited, status=1/FAILURE)
        CPU: 62ms

Oct 15 20:34:47 elmo systemd[1]: kubelet.service: Main process exited, code=exited, status=1/FAILURE

Oct 15 20:34:47 elmo systemd[1]: kubelet.service: Failed with result 'exit-code'.
```
```shell
monster@elmo:~$ journalctl -u kubelet
Oct 15 20:33:04 elmo systemd[1]: Started kubelet: The Kubernetes Node Agent.
Oct 15 20:33:04 elmo systemd[1]: kubelet.service: Current command vanished from the unit f
ile, execution of the command list won't be resumed.
Oct 15 20:33:04 elmo systemd[1]: Stopping kubelet: The Kubernetes Node Agent...
Oct 15 20:33:04 elmo systemd[1]: kubelet.service: Deactivated successfully.
Oct 15 20:33:04 elmo systemd[1]: Stopped kubelet: The Kubernetes Node Agent.
Oct 15 20:33:04 elmo systemd[1]: Started kubelet: The Kubernetes Node Agent.
Oct 15 20:33:04 elmo kubelet[13538]: E1015 20:33:04.491669   13538 run.go:74] "command failed" err="failed to load kubelet con
fig file, error: failed to load Kubelet config file /var/lib/kubelet/config.yaml, error failed to read kubelet config file \"/
var/lib/kubelet/config.yaml\", error: open /var/lib/kubelet/config.yaml: no such file or directory, path: /var/lib/kubelet/con
fig.yaml"
Oct 15 20:33:04 elmo systemd[1]: kubelet.service: Main process exited, code=exited, status=1/FAILURE
...
```

Разрешаваме kubelt сървиса за да се стартира при следващото рестартиране на машината:
```shell
sudo systemctl enable --now kubelet
```
Проверяваме че br_netfilter модула е зареден и го зареждаме ако не е:
```shell
lsmod | grep br_netfilter
sudo modprobe br_netfilter
```
За да може Linux нода да може да вижда правилно трафика през bridge, параметъра: `net.bridge.bridge-nf-call-iptables`  трябва да е настроен на: 1
Модулите: overlay и br_netfilter са необходими на containerd
```shell
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter
```

някой sysctl параметри небодхоими за инсталацията трябва запазим и след рестарт:
```shell
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system
```

Инсталиране на Container Runtime: containerd
Добавяне на gpg ключа на репозиторито:
```shell
 sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```
Добавяне на репозиторито с необходимите пакети:
```shell
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
Инсталиране на containerd:
```shell
sudo apt update
sudo apt install containerd.io -y
```
След това създаваме конфигурационния файл на containerd:
```shell
sudo su
containerd config default>/etc/containerd/config.toml
exit
```

Конфигуриране на containerd cgroup драйвера
To use the systemd cgroup driver in /etc/containerd/config.toml with runc, set
```
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
  ...
  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
    SystemdCgroup = true
```
The systemd cgroup driver is recommended if you use cgroup v2.

рестартираме containerd за да приложим новата конфигурация
```shell
sudo systemctl restart containerd
```
Разрешаваме containerd да се стартира заедно със системата
```shell
sudo systemctl enable containerd
```
изтегляне на необходимите имиджи от  kubeadm
```shell
root@elmo:/home/monster# sudo kubeadm config images pull
[config/images] Pulled registry.k8s.io/kube-apiserver:v1.25.3
[config/images] Pulled registry.k8s.io/kube-controller-manager:v1.25.3
[config/images] Pulled registry.k8s.io/kube-scheduler:v1.25.3
[config/images] Pulled registry.k8s.io/kube-proxy:v1.25.3
[config/images] Pulled registry.k8s.io/pause:3.8
[config/images] Pulled registry.k8s.io/etcd:3.5.4-0
[config/images] Pulled registry.k8s.io/coredns/coredns:v1.9.3
```
Инициализиране на control plane със kubeadm
```shell
monster@elmo:~$  sudo kubeadm init
[init] Using Kubernetes version: v1.25.3
[preflight] Running pre-flight checks
	[WARNING SystemVerification]: missing optional cgroups: blkio
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [elmo kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 192.168.224.149]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [elmo localhost] and IPs [192.168.224.149 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [elmo localhost] and IPs [192.168.224.149 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Starting the kubelet
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[apiclient] All control plane components are healthy after 8.502481 seconds
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config" in namespace kube-system with the configuration for the kubelets in the cluster
[upload-certs] Skipping phase. Please see --upload-certs
[mark-control-plane] Marking the node elmo as control-plane by adding the labels: [node-role.kubernetes.io/control-plane node.kubernetes.io/exclude-from-external-load-balancers]
[mark-control-plane] Marking the node elmo as control-plane by adding the taints [node-role.kubernetes.io/control-plane:NoSchedule]
[bootstrap-token] Using token: xcnc1w.ok1t1bgsk296gzfa
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to get nodes
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] Configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] Configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.224.149:6443 --token xcnc1w.ok1t1bgsk296gzfa \
	--discovery-token-ca-cert-hash sha256:2702f8943e320d59de098fb5578b507997d82e5591eb5f887167709f2f2767ba
```
Моля не изпълнявайте никоя от командите предложени от: kubeadm!

Следващата стъпка е да настройм kubectl клиента за да може да се свърже със сървъра:
```shell
monster@elmo:~$ kubectl config view
apiVersion: v1
clusters: null
contexts: null
current-context: ""
kind: Config
preferences: {}
users: null
```
по подразбиране конфигурацията на файла е празна. Подразбиращото се място където kubectl търси за конфигурационен файл е във: $HOME/.kube
```shell
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

```shell
sudo cat /etc/crictl.yaml
sudo crictl config --set runtime-endpoint=unix:///run/containerd/containerd.sock --set image-endpoint=unix:///run/containerd/containerd.sock
```
Рестартирайте машината:
```
sudo reboot
```
Да видим дали нашия клъстър вече работи:
```shell
monster@elmo:~$ kubectl get no -o wide
NAME   STATUS     ROLES           AGE   VERSION   INTERNAL-IP       EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
elmo   NotReady   control-plane   76m   v1.25.3   192.168.224.149   <none>        Ubuntu 22.04.1 LTS   5.15.0-50-generic   containerd://1.6.8

```
Статуса му показва, че не е готов. Да се опитаме да разберем защо:
```shell
monster@elmo:~$ kubectl describe no/elmo
Name:               elmo
Roles:              control-plane
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=elmo
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/control-plane=
                    node.kubernetes.io/exclude-from-external-load-balancers=
Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: unix:///var/run/containerd/containerd.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Sat, 15 Oct 2022 21:20:56 +0000
Taints:             node-role.kubernetes.io/control-plane:NoSchedule
                    node.kubernetes.io/not-ready:NoSchedule

`
```shell
monster@elmo:~$ kubectl describe no/elmo
Name:               elmo
Roles:              control-plane
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=elmo
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/control-plane=
                    node.kubernetes.io/exclude-from-external-load-balancers=
Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: unix:///var/run/containerd/containerd.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Sat, 15 Oct 2022 21:20:56 +0000
Taints:             node-role.kubernetes.io/control-plane:NoSchedule
                    node.kubernetes.io/not-ready:NoSchedule

`
```shell
monster@elmo:~$ kubectl describe no/elmo
Name:               elmo
Roles:              control-plane
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=elmo
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/control-plane=
                    node.kubernetes.io/exclude-from-external-load-balancers=
Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: unix:///var/run/containerd/containerd.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Sat, 15 Oct 2022 21:20:56 +0000
Taints:             node-role.kubernetes.io/control-plane:NoSchedule
                    node.kubernetes.io/not-ready:NoSchedule
...
```
виждаме че на нода има настроени Taints. Това е механизмът чрез който кубернетис предотвратява стартиране на подобве, освен ако изрично те нямат толреанс към ограниченията.
По подразбиране kubeadm ограничава  стартирането на не-системни подове върху мениджмънт нодовете. За да може да използваме клъстър само с един ноде, ще трябва да премахнем ограниченията.

```shell
kubectl taint node --all node-role.kubernetes.io/control-plane-
```

последната стъпка е да конфигурираме CNI:
```shell
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
```

сега вече нашият нод от един хост би трябвало да е готов за създаване на нови подове
```shell
monster@elmo:~$ kc get no
NAME   STATUS   ROLES           AGE   VERSION
elmo   Ready    control-plane   10h   v1.25.3
```

```shell
monster@elmo:~$ kc get po -A
NAMESPACE     NAME                           READY   STATUS    RESTARTS       AGE
kube-system   coredns-565d847f94-6jns4       1/1     Running   0              10h
kube-system   coredns-565d847f94-smtq5       1/1     Running   0              10h
kube-system   etcd-elmo                      1/1     Running   43 (8h ago)    9h
kube-system   kube-apiserver-elmo            1/1     Running   3 (8h ago)     8h
kube-system   kube-controller-manager-elmo   1/1     Running   45 (8h ago)    10h
kube-system   kube-proxy-hx9tz               1/1     Running   45 (8h ago)    10h
kube-system   kube-scheduler-elmo            1/1     Running   45 (8h ago)    10h
kube-system   weave-net-w4nz8                2/2     Running   1 (100s ago)   111s
```

```shell
monster@elmo:~$ kc run --image nginx nginx
pod/nginx created
```

```shell
monster@elmo:~$ kc get po
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          12s
```

```shell
monster@elmo:~$ kc get po -o wide
NAME    READY   STATUS    RESTARTS   AGE   IP          NODE   NOMINATED NODE   READINESS GATES
nginx   1/1     Running   0          67s   10.32.0.4   elmo   <none>           <none>
```

```shell
monster@elmo:~$ ping 10.32.0.4
PING 10.32.0.4 (10.32.0.4) 56(84) bytes of data.
64 bytes from 10.32.0.4: icmp_seq=1 ttl=64 time=0.144 ms
```

## Документация
* [Installing kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
* [Creating a cluster with kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)
* [Container Runtimes](https://kubernetes.io/docs/setup/production-environment/container-runtimes/)
* [Network Plugin Requirements](https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#network-plugin-requirements)]
* [weve net kube-addon install](https://www.weave.works/docs/net/latest/kubernetes/kube-addon/#install)
* [containerd installation](https://docs.docker.com/engine/install/ubuntu/)
* [kubeadm init](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-init/)
*[ Debugging Kubernetes nodes with crictl](https://kubernetes.io/docs/tasks/debug/debug-cluster/crictl/)
