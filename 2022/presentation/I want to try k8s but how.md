<!-- slide bg="[[see-ship-612x612.jpg]]" -->

<style>
date {color: white; font-weight: normal; font-size: 30px}
name {color: white; font-weight: bold; font-size: 40px}
header1 {color: white; font-weight: normal; font-size: 50px}
header2 {color: orange; font-weight: bold; font-size: 80px}
header3 {color: orange; font-weight: bold; font-size: 50px}
header5 {color: orange; font-weight: bold; font-size: 30px}
header6 {color: orange; font-weight: bold; font-size: 20px}
</style>

<grid  drag="100 40" drop="top" flow="row" align="center">

<p>
<header1> Искам да пробвам </header1>
</p>
![[k8s.png]]

<p>
<header2>но как ?</header2>
</p>
</grid>

<grid  drag="90 30" drop="bottomleft" flow="col" align="left">

<p> 
<date>OpenFest, София, 16 октомври 2022</date> <br>
<name>Илиян Петков</name>
</p>

</grid>
---
<grid  drag="100 20" drop="top" flow="row" align="left" bg="#004EFF">
<name>&ensp; # whoami </name>
</grid>

<grid  drag="100 80" drop="bottom" flow="row" align="center" bg="white">
<p style="padding-left: 15px; text-align: left"><font size="6">
Илиян Петков <br>
Инженер @VMware България <br>
Twitter: @Iliyan_Petkov <br>
LinkedIn: <a> https://www.linkedin.com/in/iliyan-s-petkov/ </a><br>
</font>
</p>
</grid>

<grid  drag="30 40" drop="topright" flow="row" align="center" bg="transparent">
![[myself.jpg]]
</grid>

---
<!-- slide bg="[[see-ship-612x612.jpg]]" -->
<grid  drag="40 100" drop="left" flow="row" align="left" >
&ensp; Програма
</grid>

<grid  drag="60 100" drop="right" flow="row" align="left" border="thick solid yellow">

&ensp; 01 ~#whoami~ <br>
&ensp; 02  (Малко) Предистория <br>
&ensp; 03 K8s отвътре <br>
&ensp; 04 K8s: "The easy way*" <br>
&ensp; 05 K8s @вкъщи 

</grid>


---
<!-- slide bg="[[see-ship-612x612.jpg]]" -->
<grid  drag="100 30" drop="left" flow="row" align="left" >
<header2> 02 (Малко) Предистория </header2>
</grid>

note: 
* от bear metal през VM  до контейнер
* какво е микросървис, 
* какво е контейнер
* каква е разликата между контейнер и виртуална машина
* какво е K8s и защо има нужда от оркестратори

---
<!-- slide bg="[[ocean_still.jpg]]" -->
<grid  drag="100 20" drop="top" flow="row" align="left" >
<header3> От физически сървъри до контейнери</header3>
</grid>

<grid  drag="100 80" drop="center" flow="row" align="left">
![[from baremetal to containers.png]]
</grid>

---

<!-- slide bg="[[ocean_still.jpg]]" -->
<grid  drag="100 20" drop="top" flow="row" align="left">
<header3>Разлики между контейнер и виртуализация</header3>
</grid>

<grid  drag="100 80" drop="bottom" flow="column" align="left">


![[virtualization-vs-containers.png]]

note:
-   [Virtualization](https://www.redhat.com/en/topics/virtualization) lets your operating systems (Windows or [Linux](https://www.redhat.com/en/topics/linux)) run simultaneously on a single hardware system.
-   Containers share the same operating system kernel and isolate the application processes from the rest of the system. For example: [ARM](https://www.redhat.com/en/topics/linux/what-is-arm-processor) Linux systems run ARM Linux containers, x86 Linux systems run x86 Linux containers, x86 Windows systems run x86 Windows containers. Linux containers are extremely portable, but they [must be compatible](https://www.redhat.com/en/topics/linux/ARM-vs-x86) with the underlying system.

</grid>

---

<!-- slide bg="[[ocean_still.jpg]]" -->
<grid  drag="100 20" drop="top" flow="row" align="left">
<header3>Какво е контейнер ?</header3>
</grid>

<grid  drag="100 80" drop="top" flow="row" align="left" >
<p style="padding-left: 15px; text-align: left; line-height:80%"><font size="4">
* Масово популяризирани  от   <header6>Docker</header6>  но съществуващи още от Unix system 7 през 1979 с въвеждането на <header6>chroot</header6> <br>
* <header6>Енкапсулирани, преносими, изпълними</header6> - дефинират стандартен начин <header6> (OCI) </header6> за пакетиране , разпространение и изпълнение на приложения заедно с техните зависимости<br>
* <header6> Съдържат набор от един или повече процеси </header6>  изолирани от останалата система  <br> 
* Всички файлове необходими за работата на контейнра биват пакетирани в <header6>имиджи (images)</header6> <br>
* <header6>Имиджите се превръщат в контейнери</header6>  след като бъдат изпълнени <br>
* <header6> Linux, MAC OS и дори  Windows поддържат контейнери</header6>  <br>
</font>
</p>
</grid>
<grid  drag="40 30" drop="bottomright" flow="row" align="left" >
![[docker-filesystems-busyboxrw.png]] <!-- element style="object-fit: contain" -->
</grid>


note:
* контейнеризацията съществува от давна но дълго време е била трудна за използване
* Началото е през далечната 1979 - Unix v7 със въвеждането на chroot - изолира приложенията като им дава отделни копия на файловата система
* възприета от BSD през 1982
* BSD Jails - края на 90'те началото на 2000'та
* въведена в линукс през 2001 чрез Linux vServer (inux-VServer provides virtualization for GNU/Linux systems. This is accomplished by kernel level isolation. It allows to run multiple virtual units at once. Those units are sufficiently isolated to guarantee the required security, but utilize available resources efficiently, as they run on the same kernel.)
* linuxcontainers.org is the umbrella project behind LXD, LXC, LXCFS and distrobuilder -> първоначално обявени през 2008 -  използващи cgroups & намеспейсове
* Първите версии на докер са базирани на LXC но зарадимножество проблеми са маханти във версия 1.10.

---

<!-- slide bg="[[ocean_still.jpg]]" -->
<grid  drag="100 20" drop="top" flow="row" align="left">
<header3>Mонолити срещу микросървиси</header3>
</grid>

<grid  drag="100 50" drop="center" flow="row" >

![[monolithic arc.png|200]]

\==>

![[microservices-architecture2.png|400]]

</grid>

---

<!-- slide bg="[[ocean_still.jpg]]" -->
<grid  drag="100 10" drop="top" flow="row" align="left" >
<header3>Какво е микросървис ? </header3>
</grid>

<grid  drag="100 70" drop="top" flow="row" align="left">
<p style="padding-left: 15px; text-align: left"><font size="5">
 * Aрхитектурен и организационен подход при създаването на приложения  <br>
 * Функционалността на приложението е обособена в множество малки и независими компоненти (сървиси)  <br>
 * Сървисите  могат да си общуват през добре-дефинирани интерфейси (<header5>APIs </header5>) <br>
</font>
</p>

</grid>
<grid  drag="60 30" drop="bottomright" flow="row" align="center">

![[Microservices.gif|300]]
</grid>

---

<!-- slide bg="[[ocean_still.jpg]]" -->
<grid  drag="100 20" drop="top" flow="row" align="left" >
<header3> Предимства на микросървисите </header3>
</grid>

<grid  drag="100 80" drop="bottom" flow="row" align="left">
<p style="padding-left: 15px; text-align: left"><font size="5">
* <header5>Автономност</header5> -  Всеки сървис може да бъде разботван, обновяван и мултиплициран независимо от останалите (черна кутия)<br> 
* <header5>Специализираност</header5> -  всеки сървис е проектиран да решава конкретен проблем и да има ограничен набор от функционалности <br>
* <header5>Гъвкавост</header5>  - Малкия размер на сървисите позволява работа по тях от по-малки екипи и по-бърз цъкъл за разработка. <br>
* <header5>Преизползваемст на кода</header5> - подобно на лего блокчетата, отделните сървиси могат да се преизползват в множество проекти <br>
* <header5> Надеждност </header5> - независимостта на отделните сървиси подобрява цялостната надеждност на приложенията. 
</font>
</p>

</grid>

note: 
 * сървистие се като черни кутии
 * имат стандартизиран вход и изход. 
 * един сървис не се интересува от имплементацията на останалите а само от интерфейса му


---

<!-- slide bg="[[ocean_still.jpg]]" -->
<grid  drag="100 10" drop="top" flow="row" align="left" >
<header3>Какво е</header3>
<p>
![[kubernetes-horizontal-color.png]] <!-- element style="object-fit: contain" -->

<header3> &nbsp; ?</header3>
</p></grid>

<grid  drag="100 80" drop="center" flow="row" align="left">
<p style="padding-left: 15px; text-align: left; line-height:70%"><font size="4">
* <header6>Платформа с отворен код</header6> за автоматизиране на разполагането, мултиплицирането и операциите с контейнери върху клъстри от хостове <br>
* Името Kubernetes често се съкращава като "K8s" <br>
* Дарен от Google на CNCF ( Cloud Native Computing Foundation) при нейното основаване през 2015 от: Google, CoreOS, Mesosphere, Red hat, twitter, IBM, Docker Intel... VMware <br>
*  <header6>Kubernetes ([κυβερνήτης]</header6> -  Greek for "[helmsman]," "pilot" or "governor"<br>
* <header6> преносим </header6> - публичен, частен, хибриден или мулти-клауд <br> 
* Прoизлиза от предишни системи разработвани вътрешно в  Google - Borg & Omega <br>
* Кодовото име на пректа е било Project 7 (кръстено на бивш Борг от Стар Трек Seven of Nine) <br>
* За разлика от Borg написан основно на С++, К8S е написан на Go <br>
* Слой за оркестрация позволяващ по-ефективно управление на приложенията използващи контейнери
</font></p></grid>

<grid  drag="100 20" drop="bottom" flow="row" align="left">
<p style="padding-left: 15px; text-align: left; line-height:50%"><font size="4">
Документaлна поредица за създаването на Kubernetes: <br>
  *  <a href="https://youtu.be/BE77h7dmoQU">Kubernetes: The Documentary [Part 1]</a> <br>
  *  <a href="https://youtu.be/318elIq37PE">Kubernetes: The Documentary [Part 2]</a> 
</font></p></grid>

---

<!-- slide bg="[[ocean_still.jpg]]" -->
<grid  drag="100 10" drop="top" flow="row" align="left" >
<header3> По-широк поглед</header3>
</grid>

<grid  drag="100 80" drop="center" flow="row" align="left">

![[kubernetes-iceberg.png]] <!-- element style="object-fit: contain" -->
</grid>

---

<!-- slide bg="[[ocean_still.jpg]]" -->
<grid  drag="100 10" drop="top" flow="row" align="left" >
<header3> Изглед от високо</header3>
</grid>

<grid  drag="100 70" drop="center" flow="row" align="left">
![[cncf landscape.png]] <!-- element style="object-fit: contain" -->
</grid>

<grid  drag="100 10" drop="bottom" flow="row" align="left">
<p style="padding-left: 15px; text-align: left; line-height:50%"><font size="4">
 Карта на <a href="https://landscape.cncf.io/">CNCF Landscape </a>
</font></p>
</grid>

---

<!-- slide bg="[[see-ship-612x612.jpg]]" -->
<grid  drag="100 30" drop="left" flow="row" align="left" >
<header2> &ensp; 03 K8s отвътре  </header2>
</grid>

note:
* Бърз преглед на основните компоненти на K8s
* ще разгледаме някой основни типове ресурси
* Тук ще правим лаба където вдигаме к8с клъстър на един нод и ще пробваме да пуснем Doom на него
* ще пробваме да пуснем KubeDoom за демонстрация

---

<!-- slide bg="[[ocean_still.jpg]]" -->
<grid  drag="100 20" drop="top" flow="row" align="left" >
<header3>Какви компоненти изграждат К8с</header3>
</grid>

<grid  drag="100 80" drop="bottom" flow="row" align="left" >

![[kuberneteds data flow.png]]

</grid>

---

<!-- slide bg="[[ocean_still.jpg]]" -->
<grid  drag="100 20" drop="top" flow="row" align="left">
<header3>Видове Кубернетис API ресурси</header3>
</grid>


<grid  drag="40 70" drop="left" flow="row" align="left" >
<p style="padding-left: 15px; text-align: left; line-height:80%"><font size="5">
* namespace <br>
* pod  <br>
* job <br>
* service <br>
* Ingress  <br>
* Config maps <br>
* Secrets <br>
* Deployments <br>
* Statefullsets <br>
* Replicasets <br>
* PV, PVC <br>
* CRDs <br>
* И още много... <br>

</font></p></grid>

<grid  drag="60 80" drop="right" flow="row" align="left" >
```
$ kc api-resources
NAME                              SHORTNAMES   APIVERSION                             NAMESPACED   KIND
bindings                                       v1                                     true         Binding
componentstatuses                 cs           v1                                     false        ComponentStatus
configmaps                        cm           v1                                     true         ConfigMap
endpoints                         ep           v1                                     true         Endpoints
events                            ev           v1                                     true         Event
limitranges                       limits       v1                                     true         LimitRange
namespaces                        ns           v1                                     false        Namespace
nodes                             no           v1                                     false        Node
persistentvolumeclaims            pvc          v1                                     true         PersistentVolumeClaim
persistentvolumes                 pv           v1                                     false        PersistentVolume
pods                              po           v1                                     true         Pod
podtemplates                                   v1                                     true         PodTemplate
```
</grid>

---

<!-- slide bg="[[ocean_still.jpg]]" -->
<grid  drag="100 20" drop="top" flow="row" align="left">
<header3>Управление на К8s клъстър</header3>
</grid>

<grid  drag="100 70" drop="left" flow="row" align="left" >
<p style="padding-left: 15px; text-align: left; line-height:60%"><font size="4">
Управлението на K8s става през API сървиса. <br>
Съществуват различни методи: <br><br>
Чрез директно обръщение към API сървиса чрез REST клиент: <br>
 * curl, postman, etc. <br>
 * клиентски билбиотеки на GO, Python, Java etc. <br><br>
Клиенти от камндния ред: <br>
* kubectl - референтния CLI клиент за K8s API <br><br>
Графични клиенти: <br>
	* Octant <br>
	* kube-dashboard <br>
	* lens <br>
	* kubenav 
</font></p>
</grid>

---

<!-- slide bg="[[ocean_still.jpg]]" -->
<grid  drag="100 20" drop="top" flow="row" align="left">
<header3> Кубернетис "дистрибуции" </header3>
</grid>

<grid  drag="40 20" drop="-5 10" flow="row" align="right">
<p style="padding-left: 15px; text-align: left; line-height:50%"><font size="3">
<br>"The easiest way to install Kubernetes is to  <br>
         get someone else to do it for you" <br>
Jérôme Petazzoni
</font></p>
</grid>

<grid  drag="100 50" drop="left" flow="row" align="left">
<p style="padding-left: 15px; text-align: left; line-height:60%"><font size="4">
<br><br><header5>Кубернетес като услуга:</header5> <br>
* GKE (Google K8s Engine) <br>
* EKS (Amazon Elastic K8s Sesrvice) <br>
* AKS (MS Azure K8s Service) <br>
* IKE (IBM Cloud K8s Service) <br>
* Linode <br>
* Digital Ocean <br>
* И още много други <br><br>
<header5>Собствен Клъстър:</header5> <br>
 * VMware Tanzu - за продъкшън приложения и за тестови среди  <br>
 * VMware Workstation / Fusion - за тестване (vctl) <br>
* Docker Desktop / Rancher Desktop / Podman  - клъстър от един нод за тестове <br>
* Minikube - единичен клъстър за тестване <br>
* KinD (Kubernetes In Docker) - един или повече К8с клъстри със един или повече нодове <br>
* kubeadm - референтен тул за създаване на K8s клъстри <br>
* k3s, k3d, и други. <br>
</font></p>
</grid>

---

<!-- slide bg="[[see-ship-612x612.jpg]]" -->
<grid  drag="100 30" drop="top" flow="row" align="left" >
<header2> 04 K8s: "The easy way" </header2>
</grid>

<grid  drag="100 30" drop="center" flow="row" align="left" >

"Everybody has a testing environment. Some people are lucky enough enough to have a totally separate environment to run production in." <br>
</grid>

<grid  drag="100 30" drop="bottom" flow="row" align="right" >

@stahnma
</grid>


---

<!-- slide bg="[[ocean_still.jpg]]" -->
<grid  drag="100 20" drop="top" flow="row" align="left">
<header3>Експеримент 01: Инсталиране на K8s </header3>
</grid>

<grid  drag="100 80" drop="bottom" flow="row" align="left">
* Лаб 01 - Docker Desktop/Rancher Desktop 
* Лаб 02 - Kind
* Лаб 03 - MiniKube
* Лаб 04 - KubeAdm
</grid>

---

<!-- slide bg="[[ocean_still.jpg]]" -->
<grid  drag="100 20" drop="top" flow="row" align="left">
<header3>Експеримент 02:  K8s ресурси </header3>
</grid>

<grid  drag="100 80" drop="center" flow="row" align="left">
* Лаб 01 - Hello kubectl 
* Лаб 02 - Зомбита, много зомбита
</grid>

<grid  drag="80 60" drop="-10 75" flow="row" align="left">
*  ![[kubedoom-vnc.png]]
</grid>

---

<!-- slide bg="[[see-ship-612x612.jpg]]" -->
<grid  drag="100 30" drop="left" flow="row" align="left" >
<header2> 05 K8s @вкъщи  </header2>
</grid>

---

<!-- slide bg="[[ocean_still.jpg]]" -->
<grid  drag="100 20" drop="top" flow="row" align="left">
<header3> Приложения на K8s в дома</header3>
</grid>

<grid  drag="100 80" drop="bottom" flow="row" align="left">
 
* Системи за домашна автоматизация - примерно  [Home Assistant](https://www.home-assistant.io/)
* Лични VCS (souce control)- примерно [gogs](https://gogs.io/)
* За локално следене и визаулизация на данните от станции за чистотата на въздуха с Grafana/Influx DB - проект [Luftdaten](https://airbg.info/)
* Всякакви приложения за менажиране на еклектронни книги като [ubooquity](https://vaemendis.net/ubooquity/) , личен домашен клауд [Nextcloud](https://nextcloud.com/) 
* много други :)

</grid>

---

<!-- slide bg="[[ocean_still.jpg]]" -->
<grid  drag="100 20" drop="top" flow="row" align="left">
<header3> Kъде да хоствам K8s клъстър вкъщи ?</header3>
</grid>

<grid  drag="100 80" drop="bottom" flow="row" align="left">
* Можов сторидж - Synology, Netapp, Asustor etc.
* Raspberry PI клъстър с К3s
* на личния лаптоп или компютър
![[raspberry_pi_cluster.JPG|500]]
</grid>

---
<!-- slide bg="[[see-ship-612x612.jpg]]" -->
<grid  drag="100 20" drop="top" flow="row" align="left" >
<header2> Допълнителни ресурси  </header2>
</grid>

<grid  drag="100 60" drop="botom" flow="row" align="left" >
* https://www.udemy.com/user/kodekloud/
* https://www.udemy.com/course/certified-kubernetes-security-specialist
* https://livebook.manning.com/book/kubernetes-in-action-second-edition
* https://www.youtube.com/c/cloudnativefdn
* https://www.youtube.com/c/TechWorldwithNana
* https://nigelpoulton.com/books/
</grid>

---

<!-- slide bg="[[see-ship-612x612.jpg]]" -->
<grid  drag="100 30" drop="topleft" flow="row" align="left" >
<header2>  Благодаря за вниманието!   </header2>

</grid>

<grid  drag="100 30" drop="bottom" flow="row">

Линк за обратна връзка: 

![[feedback-link-qr-code.svg]]
</grid>