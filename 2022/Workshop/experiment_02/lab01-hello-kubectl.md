
kubeclt е шел клиент за kubernetes REST API, чрез който се свързваме и изпълняваме операции от командния ред. 

## Инсталиране на kubectl

###  На PC базирани на Linux
Повече информация как да се инсталира kubectl от binary или през пакет мениджър можете да видите на wiki страницата: [Install and Set Up kubectl on Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)

###  На PC базирани на Windowds 
Клиента може да се изтегли директно от: https://dl.k8s.io/release/v1.25.0/bin/windows/amd64/kubectl.exe 

или през Chocolatery:
```powershell
choco install kubernetes-cli
```

###  На MAC 
 чрез BREW:
```bash
brew install kubectl
```

или чрез Macports
```bash
sudo port selfupdate
sudo port install kubectl
```

## Как се конфигурира kubectl
За да използваме kubectl, трябва да предоставим информация как да се свържем със API сървиса на k8s клъстъра с окйто искаме да работим

По подразбиране kubectl очаква конфигурационният и файл: `config` да е разположен във: `$HOME/.kube`

В случай че файлът е разположен на друго място или искаме да раобтим със няколко различни файла има няколко начина по които  да ги посочим:
* като създадем променлива на средата: `KUBECONFIG` с пътя до конфиг файла
* като го посочим изрично на командния ред като опция: `--kubeconfig=/path/kube_config`
пеример: `kubectl --kubeconfig=/tmp/my_config get no`

Във един kubectlconfig файл може да се съдържа конфигурация а множество k8s клъстри. Всяка една от тях наричаме контекст (context)

За да видим какви контекстви вече съществуват в нашия файл можем да използваме командата:
```shell
$ kubectl config get-contexts
CURRENT   NAME                          CLUSTER                       AUTHINFO                                NAMESPACE
          kind-cl-nginx                 kind-cl-nginx                 kind-cl-nginx
          kind-kubedoom                 kind-kubedoom                 kind-kubedoom                           doomedpods
          kind-test-125                 kind-test-125                 kind-test-125
          rancher-desktop               rancher-desktop               rancher-desktop
```

за да изберем контекста с който искаме да работим, в случай че имаме множество можем да използваме командата:
```shell
$ kubectl config use-context kind-kubedoom
Switched to context "kind-kubedoom".
```

за да видим кой е текущият контекст с който работим:
```shell
$ kubectl config current-context
kind-kubedoom
```
за повече подробности моля погледнете:  [ Configure Access to Multiple Clusters](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)

## изпозлване на kubectl
Полезни съвети как да се използва kubectl могат да бъдат намерение на wiki  страницата: [kubectl cheat sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

Синтаксиса за изпълнение на kubectl команди от командния ред е:
```shell
kubectl [command] [TYPE] [NAME] [flags]
```

където `command`, `TYPE`, `NAME`, and `flags` са:
* `command` - показва операцията, глагола, действието което искаме да изпълним върхи един или повече ресурси. Пример: create, get, delete, describe
* TYPE - показва типа на API ресурса. типовете на ресурсите на не са чувствителни към главни/малки букви. Те могат да бъдат посочвани в единствено (pod), множествено (pods) число или чрез абревиетура (po)
	```shell
kubectl get pod pod1
kubectl get pods pod1
kubectl get po pod1
```
* NAME - показва името на конкретния ресурс. имената на ресурсите не са чувствителни към главни/малки букви. Ако не се посочи име на ресурс, информация за всички ресурси от този тип се показва. Например: `kc get pods`

* flags - избира специални опции. Например за да сериализираме изхода от командата като json или yaml можем да използваме опцията: '-o json', '-о yaml', или ако искаме видим повече детайли можем да изберем: '-o wide'
```shell
monster@elmo:~$ kc get po
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          46m

```
```shell
monster@elmo:~$ kc get po -o wide
NAME    READY   STATUS    RESTARTS   AGE   IP          NODE   NOMINATED NODE   READINESS GATES
nginx   1/1     Running   0          46m   10.32.0.4   elmo   <none>           <none>

```

## Как да разширим Kubectl чрез плъгини (plugins)
един удобен вариант е да се използва плъгин мениджър като [krew](https://krew.sigs.k8s.io/).
съществуват над 206 плъгина които в момента са налични за инсталация, като за всеки един има рейтинг (популярност) и описание. 
Примернен лист:
```shell
$ kc krew list
PLUGIN             VERSION
allctx             v1.3.0
cert-manager       v1.9.0
ctx                v0.9.4
deprecations       v1.4.0
graph              v0.5.0
konfig             v0.2.6
neat               v2.0.3
ns                 v0.9.4
resource-capacity  v0.7.0
score              v1.10.0
slice              v1.2.3
starboard          v0.15.8
status             v0.7.4
stern              v1.22.0
tree               v0.4.2
view-secret        v0.10.0
whoami             v0.0.44
```

Информация как да се инсталира krew може да намерите тук: https://krew.sigs.k8s.io/docs/user-guide/setup/install/

в [quickstart](https://krew.sigs.k8s.io/docs/user-guide/quickstart/). гайда има повече информация за някой често използвани операции като  добавяне или обновяване на версията на плъгин

## Документация
* [Install tools](https://kubernetes.io/docs/tasks/tools/)
* [kubectl](https://kubernetes.io/docs/reference/kubectl/kubectl/)
* [Command line tool (kubectl)](https://kubernetes.io/docs/reference/kubectl/)
*  [kubectl cheat sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
* [ Configure Access to Multiple Clusters](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)
* [What is Krew?](https://krew.sigs.k8s.io/)