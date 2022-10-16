Kind  e инструмент позволяващ създаването на локални K8s клъстри използвайки Docker контейнери като нодове.

Kind първоначално е бил замислен като инструмент за разработването и тестването на самия K8s но може да бъде използван и за създаване на локална дев/тест среда за K8s или CI.


Kind се състой от:
* GO пакети реализиращи създаването на клъстър, изграждането на имиджи и др.
* Клиент за командния ред - kind използващ Go пакетите
* Docker имиджи съдържащи systemd, K8s, етц.

Предимства нa Kind:
* Kind се поддържа на Mac, Linux и Windows
* позволява паралелното създаване на множество кубернетис клъстри, всеки със желаната версия на K8s
* позволява docker клиента да се извиква директно от Mac/Windows шел 
* Позволява размера на всеки клъстър да бъде посочен - брой на Control Plane и Worker нодове може да бъде лесно посочван в конфигурационен файл. 
* по-бърз е от Docker/Rancher desktop понеже не използва виртуални  машини (техничеки изисква докер engine който може при стартиране да създава виртуални машини - Mac & Windows)

## Инсталиране на Kind

### На MAC
чрез brew
```bash
brew install kind
```

чрез MacPorts
```bash
sudo port selfupdate && sudo port install kind
```

### На PC с Linux

```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.16.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```
където v0.16.0  е желаната версия на Kind

### На PC с Windows
Чрез Chocolatey:
```powershell
choco install kind
```

чрез PowerShell:
```powershell
curl.exe -Lo kind-windows-amd64.exe https://kind.sigs.k8s.io/dl/v0.16.0/kind-windows-amd64
Move-Item .\kind-windows-amd64.exe c:\some-dir-in-your-PATH\kind.exe
```

## Създаване на клъстър 

Управлението на клъстрите ставата чрез командата: kind
```shell
kind create cluster
```
По подразбиране kind ще създаде клъстър наречен... kind 
Можем давидим създадените контейнери в Docker чрез:
```shell
docker container ls
или
docker ps
```

можем да създадем клъстър с различно име като го посочим при създаването:
```shell
kind create cluster --name=[cluster-name]
```

Желаната версия на K8s за клъстъра изибраме чрез опцията: --image
```
kind create cluster --image=[image]
```

пример:
```shell
kind create cluster --name test-125 --image kindest/node:v1.25.2@sha256:9be91e9e9cdf116809841fc77ebdb8845443c4c72fe5218f3ae9eb57fdb4bace

или

kind create cluster --name test --image kindest/node:v1.25.2
```

Според това каква версия на Kind е инсталирана определени версии на K8s се поддържат.  Списъкът със тях е наличен във [github](https://github.com/kubernetes-sigs/kind/releases)

## Документация
* [Kind Quick Start Guide - Installation](https://kind.sigs.k8s.io/docs/user/quick-start#installation)
