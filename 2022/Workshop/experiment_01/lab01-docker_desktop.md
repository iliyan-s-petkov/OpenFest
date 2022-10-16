Един от най-лесните и бързи начини за създаване на личен K8s клъстър за разработване или тестване на приложения  на Mac или PC с Linux или Windows е като използваме някой от следните графични продукти:
* Docker Desktop
* Rancher Desktop
* Podman


## Кратко сравнение между различните варианти
* Както Docker Desktop  така и Rancher изискват привилегирован (root/admin) достъп за инсталация (може да е проблем на служебни компютри с ограничени потребителски права).
* Rancher desktop позволява работа и без привилигерован достъп.
* PodMan използват "rootless" контейнери, не е необходим сървис или демон подобно на Dockerd. Контейнери могат да бъдат създавани като обикновен потребител.

* И трите варианта са  съвместими както с Windows така и с Mac
* Podman и Rancher  са OpenSource и безплатни
*  Docker Desktop e безплатен за
	*  малки компании (до 250 служителия и с годишни приходи по-малко от 10$ милиона) .
	* лична употреба, образоватлни цели, не-комерсиални open-source проекти
* За използване на Docker Desktop в бизнеси с над 250 служителя или годишни приходи над 10$ милиона се изисква платен абонамент.

* Трите се различават по функционалност, реализация и графичен интерфейс.
* И трите поддържат изпозлването на kubectl  за менажиране на K8s клъстри. Dockerd и Rancher (с container engine: dockerd) са съвместими със docker cli клиента
* И трите поддържат съзадаване на единичен K8s клъстър no


![[rancher vs docker.png]]

## Инсталиране на Docker Desktop
### На PC с Windows
#### Минимални изисквания
* Windows 10 64 bit 19042 или по-нов
* разрешена WSL2
* 4 GB ram
* разрешена BIOS ниво хардуерна виртуализация.

#### Процедура за инсталация
* Изтебляне на Docker Desktop от [Doker Hub](https://docs.docker.com/desktop/install/windows-install/)
* Щракнете два-пъти върху файла "**Docker Desktop Installer.exe**" за да започне инсталацията
* Когато инталаторърт попита избете "WSL 2" вместо "Hyper-V" опцията в страницата за кофнигурация.
* Следвайте инструкциите на инсталатора
* Когато инсталаторът завърши, изберете "Close"  за да приключите инсталацията.
* Ако използвате потребителски акаунт различен от Admin акаунта, трябва да го добавите към групата: docker-users

	Run **Computer Management** as an **administrator** and navigate to **Local Users and Groups** > **Groups** > **docker-users**.
	Right-click to add the user to the group.
	Log out and log back in for the changes to take effect.

### На MAC
#### Минимални изисквания
* macOS 10.15 или по-нова
* 4 GB рам

#### Процедура за инсталация
* Изтеглете Docker Desktop от: https://www.docker.com/products/docker-desktop/
* Щракнете два-пъти върху Docker.dmg за да започнете инсталацията
* Изтеглете иконата на Docker в папката: Applications

### На PC с Linux
#### Минимални изисквания
* 64bit процесор поддържащ виртуализация
* поддръжка на KVM
* QEMu 5.2 или по-нова
* systemd
* 4 GB рам

#### Процедура за инсталация
За инструкции за инсталация според вашата дистрибуция моля погледнете [официалната документация](https://docs.docker.com/desktop/install/linux-install/#generic-installation-steps)

Алтернативно можете да  инсталирате  Docker Engine слейдвайки инструкциите в някоя от секциите на документацията според типа на  [вашата дистрибуция](https://docs.docker.com/engine/install/)

## Инсталиране на Rancher Desktop
### На MAC
#### Минимални изисквания
* macOS 10.15 или по-нова
* M1 процесор или  Intel процесор със VT-x
* Internet свързаност
* 8 GB RAM
* 4 CPU Cores

#### Процедура за инсталация
* Изтеглете последната версия ня Rancher Desktop от [GitHub](https://github.com/rancher-sandbox/rancher-desktop/releases)
* Щракнете два-пъти върху изтегления dmg файл за да започнете инсталацията
* Изтеглете иконата на Docker в папката: Applications


### На PC с Windows
#### Минимални изисквания
* Windows 10 1909 или по-нов
* подрръжка на виртуализация
* Интернет свързаност
* 8 GB памет
* 4 ядрен процесор

#### Процедура за инсталация
* Изтеглете последната версия ня Rancher Desktop от [GitHub](https://github.com/rancher-sandbox/rancher-desktop/releases)
* Щракнете двойно върху изтегления .exe файл
* следвайте инструкциит на инсталатора
* Когато инсталацията приключи, щракнете Finish


### На PC с Linux
#### Минимални изисквания
* 64bit процесор поддържащ виртуализация - AMD-V или VT-x
* поддръжка на KVM и R/W достъп до /dev/kvm
* 4 GB рам
* интернет свързаност

#### Процедура за инсталация
За инструкции за инсталация според вашата дистрибуция моля погледнете [официалната документация](https://docs.rancherdesktop.io/getting-started/installation#linux)


## Инсталиране на Podman
#### Процедура за инсталация
За инструкции за инсталация според вашатаоперационна систем, moля погледнете [официалната документация](https://podman-desktop.io/docs/intro)


## Документация
* [Install Docker Desktop on Mac](https://docs.docker.com/desktop/install/mac-install/)
* [Install Docker Desktop on Windows](https://docs.docker.com/desktop/install/windows-install/)
* [Rancher Desktop Installation](https://docs.rancherdesktop.io/getting-started/installation/)
* [Podman Desktop](https://podman-desktop.io/)
* [ The Magic of Docker Desktop is Now Available on Linux](https://www.docker.com/blog/the-magic-of-docker-desktop-is-now-available-on-linux/)
