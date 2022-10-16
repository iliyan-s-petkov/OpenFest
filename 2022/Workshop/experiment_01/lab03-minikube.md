Minkube  е инструмент позволяващ създаването на локален K8s клъстър подходящ за разработване на приложения, тестване и експерименти.

* Създава К8s клъстър във Виртуална машина или докер контейнер (след Minikube 1.7.0)
* Поддържа множество хипервайзъри - VMware Fusion/Workstation, VirtialBox, kvm 
* Поддържа кубернетис клъстри с повече от един нод
* Поддържа създаване на много кубернетис клъстри (чрез профили)
* има вградена поддръжка за [Kubernetes Dashboard UI](https://github.com/kubernetes/dashboard).

## Инсталиране на Minikube
### Минимални изисквания
* 2 ядра или повече  на процесора
* 2GB свободна памет
* 20GB свободно дисково пространство
* Интернет свързаност
* Менажер на контерйнери или виртуални машини - Docker, Hyperkit, Hyper-V, KVM, VMware Fusion/Workstation, VirtualBox


### На MAC
чрез BREW:
```shell
brew install minikube
```
чрез изпълним файл:
```shell
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64
sudo install minikube-darwin-amd64 /usr/local/bin/minikube
```

### На PC с Linux
Debian:
```shell
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
sudo dpkg -i minikube_latest_amd64.deb
```
RPM:
```shell
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-latest.x86_64.rpm
sudo rpm -Uvh minikube-latest.x86_64.rpm
```

### На PC с Windows
*  изтеглете инсталатора за [последната версия](https://storage.googleapis.com/minikube/releases/latest/minikube-installer.exe)
* щракнете двойно върху изпълнимия файл или инсталирайте през PowerShell:
  ```powershell
New-Item -Path 'c:\' -Name 'minikube' -ItemType Directory -Force
Invoke-WebRequest -OutFile 'c:\minikube\minikube.exe' -Uri 'https://github.com/kubernetes/minikube/releases/latest/download/minikube-windows-amd64.exe' -UseBasicParsing
```
* Добавете MiniKube към променливата със пътищата: PATH
```powershell
$oldPath = [Environment]::GetEnvironmentVariable('Path', [EnvironmentVariableTarget]::Machine)
if ($oldPath.Split(';') -inotcontains 'C:\minikube'){ `
  [Environment]::SetEnvironmentVariable('Path', $('{0};C:\minikube' -f $oldPath), [EnvironmentVariableTarget]::Machine) `
}
```

Забележка: ако сте инсталирали MiniKube през PowerShell, затворете го и го отворете наново преди да започнете да го използвате 

## Създаване на клъстър 

От терминал със администраторски права изпълнете:
```shell
minikube start --driver=docker
или
minikube start --driver=vmware
```

При стартиране на клъстъра изберете желания от вас драйвер: [Minikube drivers](https://minikube.sigs.k8s.io/docs/drivers/)

За да направите предпочитания драйвер подразбиращ се:
```shell
minikube config set driver docker 
или
minikube config set driver vmware
```

по подразбиране minikube създава K8s клъстър наречен: minikube. Ако желате да създадете друг клъстър или да промените името, можете да иползвате опицята: ---profile или (-p)

За да видите списък със всички свой профили изпълнете:
```shell
minikube profile list
```

## Документация
* [Minikube start](https://minikube.sigs.k8s.io/docs/start/)
 * [Minikube drivers](https://minikube.sigs.k8s.io/docs/drivers/)
