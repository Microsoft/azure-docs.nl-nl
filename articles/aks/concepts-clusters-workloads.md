---
title: Concepten-Kubernetes-basis beginselen voor Azure Kubernetes Services (AKS)
description: Meer informatie over de basis onderdelen van het cluster en de workload van Kubernetes en hoe deze zijn gerelateerd aan functies in azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 5e505ed44d221b20178ea5ffb1d9125fb2bddd4c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105932"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Kubernetes-kernconcepten voor Azure Kubernetes Service (AKS)

Het ontwikkelen van toepassingen gaat verder naar een op een container gebaseerde benadering, waardoor de nood zaak om resources te organiseren en te beheren. Als toonaangevend platform biedt Kubernetes een betrouw bare planning van werk belastingen voor fout tolerante toepassingen. Azure Kubernetes service (AKS), een beheerde Kubernetes-aanbieding, vereenvoudigt de implementatie en het beheer van toepassingen op basis van containers.

Dit artikel bevat het volgende:
* Kern onderdelen van de Kubernetes-infra structuur:
    * *besturings vlak*
    * *punt*
    * *knooppunt groepen*
* Werkbelasting resources: 
    * *gehele*
    * *implementaties*
    * *groepen* 
* Resources groeperen in *naam ruimten*.

## <a name="what-is-kubernetes"></a>Wat is Kubernetes?

Kubernetes is een snel evoluerend platform dat toepassingen op basis van containers en de bijbehorende netwerk-en opslag onderdelen beheert. Kubernetes richt zich op de werk belasting van de toepassing, niet op de onderliggende onderdelen van de infra structuur. Kubernetes biedt een declaratieve aanpak van implementaties, ondersteund door een robuuste set Api's voor beheer bewerkingen.

U kunt moderne, draag bare, micro Services gebaseerde toepassingen bouwen en uitvoeren met behulp van Kubernetes om de beschik baarheid van de toepassings onderdelen te organiseren en te beheren. Kubernetes ondersteunt zowel stateless als stateful toepassingen als teams voortgang door de aanneming van op micro Services gebaseerde toepassingen.

Als een open platform kunt u met Kubernetes uw toepassingen bouwen met uw favoriete programmeer taal, besturings systeem, bibliotheken of Messa ging-bus. Bestaande hulpprogram ma's voor continue integratie en continue levering (CI/CD) kunnen worden geïntegreerd met Kubernetes voor het plannen en implementeren van releases.

AKS biedt een beheerde Kubernetes-service waarmee de complexiteit van implementatie-en kern beheer taken, zoals upgrade coördinatie, wordt gereduceerd. Het Azure-platform beheert het AKS-besturings vlak en u betaalt alleen voor de AKS-knoop punten waarop uw toepassingen worden uitgevoerd. AKS is gebaseerd op de open-source Azure Kubernetes service-Engine: [AKS-engine][aks-engine].

## <a name="kubernetes-cluster-architecture"></a>Kubernetes-cluster architectuur

Een Kubernetes-cluster is onderverdeeld in twee onderdelen:

- *Besturings vlak*: voorziet in de kern Kubernetes Services en de indeling van werk belastingen van toepassingen.
- *Knoop punten*: Voer de werk belasting van uw toepassingen uit.

![Kubernetes en knooppunt onderdelen](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Besturingsvlak

Wanneer u een AKS-cluster maakt, wordt automatisch een besturings vlak gemaakt en geconfigureerd. Dit besturings element heeft geen kosten als een beheerde Azure-resource die is afgeleid van de gebruiker. U betaalt alleen voor de knoop punten die zijn gekoppeld aan het AKS-cluster. Het besturings vlak en de bijbehorende resources bevinden zich alleen in de regio waar u het cluster hebt gemaakt.

Het besturings vlak bevat de volgende kern Kubernetes-onderdelen:

| Onderdeel | Beschrijving |  
| ----------------- | ------------- |  
| *uitvoeren-apiserver*                                                                                 | De API-server is hoe de onderliggende Kubernetes-Api's worden weer gegeven. Dit onderdeel biedt de interactie voor beheer hulpprogramma's, zoals `kubectl` of het Kubernetes-dash board.                                                        |  
| *etcd* | Om de status van uw Kubernetes-cluster en-configuratie te behouden, is de Maxi maal beschik bare *etcd* een sleutel waarde Store in Kubernetes.                                      |  
| *uitvoeren-scheduler*                                                                            | Wanneer u toepassingen maakt of schaalt, bepaalt de scheduler welke knoop punten de werk belasting kunnen uitvoeren en worden gestart.                                                                                    |  
| *uitvoeren-Controller-Manager*                                                                            | De controller beheerder ziet een aantal kleinere controllers die acties uitvoeren, zoals het repliceren van peulen en het verwerken van knooppunt bewerkingen.                                                                  |  

AKS biedt een besturings vlak voor één Tenant, met een speciale API-server, scheduler, enzovoort. U definieert het aantal en de grootte van de knoop punten en het Azure-platform configureert de beveiligde communicatie tussen het besturings vlak en knoop punten. Interactie met het besturings vlak vindt plaats via Kubernetes Api's, zoals `kubectl` of het Kubernetes-dash board.

Hoewel u geen onderdelen (zoals een Maxi maal beschik bare *etcd* -archief) hoeft te configureren met dit beheerde besturings vlak, hebt u geen toegang tot het besturings vlak. Kubernetes en knooppunt upgrades worden beheerd via de Azure CLI of Azure Portal. Om mogelijke problemen op te lossen, kunt u de logboeken van het controle vlak door Azure Monitor logboeken bekijken.

Als u een besturings vlak wilt configureren of rechtstreeks wilt openen, implementeert u uw eigen Kubernetes-cluster met behulp van [AKS-engine][aks-engine].

Zie [Aanbevolen procedures voor cluster beveiliging en upgrades in AKS][operator-best-practices-cluster-security]voor de bijbehorende aanbevolen procedures.

## <a name="nodes-and-node-pools"></a>Knoop punten en knooppunt groepen

Als u uw toepassingen en ondersteunende services wilt uitvoeren, hebt u een Kubernetes- *knoop punt* nodig. Een AKS-cluster heeft ten minste één knoop punt, een virtuele Azure-machine (VM) waarop de Kubernetes-knooppunt onderdelen en de container runtime worden uitgevoerd.

| Onderdeel | Beschrijving |  
| ----------------- | ------------- |  
| `kubelet`                                                                                 | De Kubernetes-agent die de Orchestration-aanvragen verwerkt vanuit het besturings vlak en de planning van het uitvoeren van de aangevraagde containers.                                                        |  
| *uitvoeren-proxy* | Hiermee verwerkt u virtuele netwerken op elk knoop punt. De proxy routeert netwerk verkeer en beheert IP-adres sering voor services en peulen.                                      |  
| *container runtime*                                                                            | Staat toe dat toepassingen in containers kunnen worden uitgevoerd en met aanvullende bronnen kunnen communiceren, zoals het virtuele netwerk en de opslag. AKS-clusters die gebruikmaken van Kubernetes-versie 1.19 +-knooppunt groepen, worden gebruikt `containerd` als container runtime. AKS-clusters die gebruikmaken van Kubernetes vóór de knooppunt groep versie 1,19, gebruiken [Moby](https://mobyproject.org/) (upstream docker) als container runtime.                                                                                    |  


![Virtuele Azure-machine en ondersteunende bronnen voor een Kubernetes-knoop punt](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

De Azure VM-grootte voor uw knoop punten definieert de opslag-Cpu's, het geheugen, de grootte en het beschik bare type (zoals SSD met hoge prestaties of normale HDD). Plan de grootte van het knoop punt om te bepalen of uw toepassingen grote hoeveel heden CPU en geheugen of hoge prestaties nodig hebben. Uitschalen van het aantal knoop punten in uw AKS-cluster om te voldoen aan de vraag.

In AKS is de VM-installatie kopie voor de knoop punten van uw cluster gebaseerd op Ubuntu Linux of Windows Server 2019. Wanneer u een AKS-cluster maakt of het aantal knoop punten uitbreidt, wordt het aangevraagde aantal Vm's automatisch door het Azure-platform gemaakt en geconfigureerd. Agent knooppunten worden gefactureerd als standaard-Vm's, waardoor eventuele VM-grootte kortingen (inclusief [Azure-reserve ringen][reservation-discounts]) automatisch worden toegepast.

Implementeer uw eigen Kubernetes-cluster met [AKS-engine][aks-engine] als u gebruikmaakt van een ander host-besturings systeem, container runtime of met inbegrip van verschillende aangepaste pakketten. De functies van de upstream- `aks-engine` releases en bieden configuratie opties die voor de ondersteuning van AKS-clusters worden ondersteund. Als u dus een andere container-runtime dan `containerd` of [Moby](https://mobyproject.org/)wilt gebruiken, kunt u uitvoeren `aks-engine` om een Kubernetes-cluster te configureren en implementeren dat voldoet aan uw huidige behoeften.

### <a name="resource-reservations"></a>Resource reserveringen

AKS maakt gebruik van knooppunt bronnen om de knooppunt functie als onderdeel van uw cluster te helpen. Dit gebruik kan een verschil maken tussen het totale aantal resources van het knoop punt en de toewijs bare resources in AKS. Onthoud deze informatie bij het instellen van aanvragen en limieten voor de door de gebruiker geïmplementeerde peulen.

Voer de volgende opdracht uit om de toewijs bare bronnen van een knoop punt te vinden:
```kubectl
kubectl describe node [NODE_NAME]
```

AKS reserveert bronnen op elk knoop punt om de prestaties en functionaliteit van het knoop punt te behouden. Naarmate een knoop punt groter wordt in resources, neemt de resource reservering toe als gevolg van een hogere behoefte aan het beheer van de door de gebruiker geïmplementeerde peulen.

>[!NOTE]
> Het gebruik van AKS-invoeg toepassingen zoals container Insights (OMS) neemt extra knooppunt bronnen in beslag.

Er zijn twee soorten resources gereserveerd:

- **CPU**  
    De gereserveerde CPU is afhankelijk van het knooppunt type en de cluster configuratie. Dit kan leiden tot een minder toewijs bare CPU vanwege het uitvoeren van extra functies.

   | CPU-kernen op de host | 1    | 2    | 4    | 8    | 16 | 32|64|
   |---|---|---|---|---|---|---|---|
   |Uitvoeren-gereserveerd (millicores)|60|100|140|180|260|420|740|

- **Geheugen**  
    Het geheugen dat wordt gebruikt door AKS omvat de som van twee waarden.

   1. **`kubelet` daemon**   
       De `kubelet` daemon wordt geïnstalleerd op alle Kubernetes-agent knooppunten om het maken en beëindigen van containers te beheren. 
   
        AKS is standaard ingesteld op `kubelet` het *geheugen. beschik bare<750Mi* -verwijderings regel, zodat een knoop punt altijd ten minste 750 mi te allen tijde kan hebben. Wanneer een host lager is dan de drempel waarde voor beschik bare geheugen, `kubelet` wordt de trigger geactiveerd om een van de uitgevoerde peulen te beëindigen en geheugen vrij te maken op de hostmachine.

   2. **Een redegressieve hoeveelheid geheugen reserveringen** voor de kubelet-daemon goed te laten functioneren (*uitvoeren-gereserveerd*).
      - 25% van de eerste 4 GB geheugen
      - 20% van de volgende 4 GB geheugen (Maxi maal 8 GB)
      - 10% van de volgende 8 GB geheugen (Maxi maal 16 GB)
      - 6% van de volgende 112 GB geheugen (Maxi maal 128 GB)
      - 2% van de geheugens boven 128 GB

Regels voor geheugen en CPU-toewijzing:
* Bewaar agent knooppunten in orde, met inbegrip van enige hosting systeem die van cruciaal belang is voor de cluster status. 
* Ervoor zorgen dat het knoop punt minder toewijs bare geheugen en CPU rapporteert dan wanneer het geen deel uitmaakt van een Kubernetes-cluster. 

De bovenstaande resource reserveringen kunnen niet worden gewijzigd.

Als een knoop punt bijvoorbeeld 7 GB biedt, wordt er 34% van het geheugen gerapporteerd die niet kan worden verplaatst, inclusief de 750Mi-drempel waarde voor harde verwijdering.

`0.75 + (0.25*4) + (0.20*3) = 0.75GB + 1GB + 0.6GB = 2.35GB / 7GB = 33.57% reserved`

Naast reserve ringen voor Kubernetes, reserveert het onderliggende knooppunt besturingssysteem ook een hoeveelheid CPU-en geheugen bronnen om besturings systemen te onderhouden.

Zie [Best Practices for Basic scheduler-functies in AKS][operator-best-practices-scheduler]voor gekoppelde aanbevolen procedures.

### <a name="node-pools"></a>Knooppuntpools

Knoop punten van dezelfde configuratie worden samen in *knooppunt groepen* gegroepeerd. Een Kubernetes-cluster bevat ten minste één knooppunt groep. Het eerste aantal knoop punten en grootte worden gedefinieerd wanneer u een AKS-cluster maakt, waarmee een *standaard knooppunt groep* wordt gemaakt. Deze standaard knooppunt groep in AKS bevat de onderliggende virtuele machines waarop de agent knooppunten worden uitgevoerd.

> [!NOTE]
> Om ervoor te zorgen dat uw cluster betrouwbaar werkt, moet u ten minste twee (2) knoop punten uitvoeren in de standaard knooppunt groep.

U kunt een AKS-cluster schalen of bijwerken op basis van de standaard knooppunt groep. U kunt ervoor kiezen om een specifieke knooppunt groep te schalen of bij te werken. Voor upgrade bewerkingen worden actieve containers gepland op andere knoop punten in de knooppunt groep totdat alle knoop punten zijn bijgewerkt.

Zie [meerdere knooppunt groepen maken en beheren voor een cluster in AKS][use-multiple-node-pools]voor meer informatie over het gebruik van meerdere knooppunt groepen in AKS.

### <a name="node-selectors"></a>Knooppunt selecties

In een AKS-cluster met meerdere knooppunt groepen moet u mogelijk de Kubernetes-planner laten weten welke knooppunt groep voor een bepaalde resource moet worden gebruikt. Ingangs controllers mogen bijvoorbeeld niet worden uitgevoerd op Windows Server-knoop punten. 

Met knooppunt selecties kunt u verschillende para meters definiëren, zoals het besturings systeem van het knoop punt, om te bepalen waar een pod moet worden gepland.

In het volgende eenvoudige voor beeld wordt een NGINX-exemplaar gepland op een Linux-knoop punt met behulp van de knooppunt kiezer *' Beta.kubernetes.io/OS ': Linux*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.12-alpine
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

Zie [Aanbevolen procedures voor geavanceerde functies van scheduler in AKS][operator-best-practices-advanced-scheduler]voor meer informatie over het bepalen van de planning.

## <a name="pods"></a>Gehele

Kubernetes maakt gebruik van *peul* om een exemplaar van uw toepassing uit te voeren. Een pod vertegenwoordigt één exemplaar van uw toepassing. 

Er is doorgaans een 1:1-toewijzing met een container. In geavanceerde scenario's kan een pod meerdere containers bevatten. Meerdere containers worden op hetzelfde knoop punt gepland en kunnen containers gerelateerde resources delen.

Wanneer u een pod maakt, kunt u *resource aanvragen* definiëren om een bepaalde hoeveelheid CPU-of geheugen bronnen aan te vragen. De Kubernetes scheduler probeert aan de aanvraag te voldoen door de peuling te plannen om uit te voeren op een knoop punt met beschik bare resources. U kunt ook maximale resource limieten opgeven om te voor komen dat een pod te veel Compute-bronnen van het onderliggende knoop punt verbruikt. U kunt het beste resource limieten voor alle peulen gebruiken om de Kubernetes scheduler te helpen bij het identificeren van de benodigde, toegestane resources.

Zie [Kubernetes peul][kubernetes-pods] en [Kubernetes pod Lifecycle][kubernetes-pod-lifecycle]voor meer informatie.

Een Pod is een logische resource, maar de werk belasting van de toepassing wordt uitgevoerd op de containers. Een Peul is doorgaans tijdelijke, wegwerp bronnen. Een individueel gepland totaal van de hoge Beschik baarheid en redundantie Kubernetes-functies missen. In plaats daarvan worden er peulen geïmplementeerd en beheerd door Kubernetes- *controllers*, zoals de implementatie controller.

## <a name="deployments-and-yaml-manifests"></a>Implementaties en YAML-manifesten

Een *implementatie* vertegenwoordigt hetzelfde peul dat wordt beheerd door de Kubernetes-implementatie controller. Een implementatie definieert het aantal pod- *replica's* dat moet worden gemaakt. De Kubernetes scheduler zorgt ervoor dat extra peulen worden gepland op gezonde knoop punten als er bij het meren aantal of knoop punten problemen optreden.

U kunt implementaties bijwerken om de configuratie van een Peul, container installatie kopie of gekoppelde opslag te wijzigen. De implementatie controller:
* Hiermee wordt een gegeven aantal replica's verwerkt en beëindigd.
* Maakt replica's van de nieuwe implementatie definitie.
* Het proces wordt voortgezet totdat alle replica's in de implementatie zijn bijgewerkt.

Voor de meeste stateless toepassingen in AKS moet het implementatie model worden gebruikt in plaats van dat er afzonderlijke peulen worden gepland. Kubernetes kan de status en status van de implementatie controleren om ervoor te zorgen dat het vereiste aantal replica's binnen het cluster wordt uitgevoerd. Als afzonderlijk wordt gepland, worden er geen peulen opnieuw gestart als er een probleem optreedt en worden ze niet opnieuw gepland op gezonde knoop punten als het huidige knoop punt een probleem aantreft.

Als voor uw toepassing mini maal beschik bare instanties zijn vereist, wilt u geen beheer beslissingen met een update proces storen. *Pod-onderbrekingen budgetten* bepalen hoeveel replica's in een implementatie kunnen worden uitgevoerd tijdens een update of upgrade van een knoop punt. Als u bijvoorbeeld *vijf (5)* replica's in uw implementatie hebt, kunt u een pod-onderbreking van *4 (vier)* definiëren, zodat er slechts één replica tegelijk kan worden verwijderd of opnieuw moet worden gepland. Net als bij pod-resource limieten moet best practice pod-verstoringen definiëren voor toepassingen waarvoor een minimum aantal replica's vereist is om altijd aanwezig te zijn.

Implementaties worden doorgaans gemaakt en beheerd met `kubectl create` of `kubectl apply` . Maak een implementatie door een manifest bestand te definiëren in de YAML-indeling. 

In het volgende voor beeld wordt een basis implementatie van de NGINX-webserver gemaakt. De implementatie specificeert *drie (3)* replica's die moeten worden gemaakt en vereist dat poort *80* is geopend in de container. Resource aanvragen en-limieten worden ook gedefinieerd voor de CPU en het geheugen.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: mcr.microsoft.com/oss/nginx/nginx:1.15.2-alpine
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

Complexere toepassingen kunnen worden gemaakt door services, zoals load balancers, op te nemen in het YAML-manifest.

Zie [Kubernetes-implementaties][kubernetes-deployments]voor meer informatie.

### <a name="package-management-with-helm"></a>Pakket beheer met helm

[Helm][helm] wordt vaak gebruikt voor het beheren van toepassingen in Kubernetes. U kunt resources implementeren door bestaande open bare helm- *grafieken* te bouwen en gebruiken die een verpakte versie van de toepassings code en Kubernetes yaml-manifesten bevatten. U kunt helm-grafieken lokaal of in een externe opslag plaats opslaan, zoals een [Azure container Registry helm-grafiek opslag plaats][acr-helm].

Als u helm wilt gebruiken, installeert u de helm-client op uw computer of gebruikt u de helm-client in de [Azure Cloud shell][azure-cloud-shell]. Zoek of maak helm-grafieken en installeer ze vervolgens op uw Kubernetes-cluster. Zie [bestaande toepassingen met helm in AKS installeren][aks-helm]voor meer informatie.

## <a name="statefulsets-and-daemonsets"></a>StatefulSets en DaemonSets

Met de Kubernetes scheduler voert de implementatie controller replica's uit op alle beschik bare knoop punten met beschik bare resources. Hoewel deze benadering voldoende is voor stateless toepassingen, is de implementatie controller niet ideaal voor toepassingen die het volgende vereisen:
* Een permanente naam Conventie of opslag. 
* Er moet een replica aanwezig zijn op elk geselecteerd knoop punt in een cluster.

Met twee Kubernetes-resources kunt u echter deze typen toepassingen beheren:

- *StatefulSets* behouden de status van toepassingen buiten een individuele levens cyclus van Pod, zoals opslag.
- *DaemonSets* zorg dat een actief exemplaar op elk knoop punt in de Kubernetes-Boots trap proces wordt uitgevoerd.

### <a name="statefulsets"></a>StatefulSets

Ontwikkeling van moderne toepassingen is vaak gericht op stateless toepassingen. Voor stateful toepassingen, zoals die database onderdelen bevatten, kunt u *StatefulSets* gebruiken. Net als bij implementaties wordt met een StatefulSet ten minste één identieke pod gemaakt en beheerd. Replica's in een StatefulSet volgen een gepaste, sequentiële benadering van implementatie, schaal, upgrade en beëindiging. De naam Conventie, netwerk namen en opslag blijven behouden, omdat replica's opnieuw worden gepland met een StatefulSet.

Definieer de toepassing in YAML-indeling met behulp van `kind: StatefulSet` . Vanaf daar verwerkt de StatefulSet-controller de implementatie en het beheer van de vereiste replica's. Gegevens worden naar permanente opslag geschreven, die wordt verschaft door Azure Managed Disks of Azure Files. Met StatefulSets blijft de onderliggende permanente opslag behouden, zelfs wanneer de StatefulSet is verwijderd.

Zie [Kubernetes StatefulSets][kubernetes-statefulsets]voor meer informatie.

Replica's in een StatefulSet worden gepland en uitgevoerd op alle beschik bare knoop punten in een AKS-cluster. Als u er zeker van wilt zijn dat er ten minste één pod in uw set wordt uitgevoerd op een knoop punt, gebruikt u in plaats daarvan een Daemonset.

### <a name="daemonsets"></a>DaemonSets

Voor specifieke logboek verzameling of-bewaking moet u mogelijk een pod uitvoeren op alle, of geselecteerd, knoop punten. U kunt de *daemonset* implementeren een of meer identieke peulen, maar de daemonset-controller zorgt ervoor dat elk opgegeven knoop punt een exemplaar van de pod uitvoert.

De controller van de Daemonset kan in het proces voor het opstarten van het cluster in een vroeg stadium plannen voordat de standaard Kubernetes scheduler is gestart. Op deze manier zorgt u ervoor dat het Peul in een Daemonset wordt gestart voordat het traditionele peul in een implementatie of StatefulSet wordt gepland.

Net als StatefulSets wordt een Daemonset gedefinieerd als onderdeel van een YAML-definitie met behulp van `kind: DaemonSet` .

Zie [Kubernetes DaemonSets][kubernetes-daemonset]voor meer informatie.

> [!NOTE]
> Als de [add-on van de virtuele knoop punten](virtual-nodes-cli.md#enable-virtual-nodes-addon)wordt gebruikt, maakt DaemonSets geen peul op het virtuele knoop punt.

## <a name="namespaces"></a>Naamruimten

Kubernetes-resources, zoals peulen en implementaties, worden logisch gegroepeerd in een *naam ruimte* om een AKS-cluster te verdelen en de toegang tot bronnen te beperken, te bekijken of te beheren. U kunt bijvoorbeeld naam ruimten maken om bedrijfs groepen te scheiden. Gebruikers kunnen alleen communiceren met resources binnen hun toegewezen naam ruimten.

![Kubernetes-naam ruimten om resources en toepassingen logisch te verdelen](media/concepts-clusters-workloads/namespaces.png)

Wanneer u een AKS-cluster maakt, zijn de volgende naam ruimten beschikbaar:

| Naamruimte | Description |  
| ----------------- | ------------- |  
| *standaardinstelling*                                                                                 | Wanneer er een Peul en implementaties standaard worden gemaakt wanneer er geen wordt gegeven. In kleinere omgevingen kunt u toepassingen rechtstreeks in de standaard naam ruimte implementeren zonder dat u extra logische schei dingen hoeft te maken. Wanneer u communiceert met de Kubernetes-API, zoals met `kubectl get pods` , wordt de standaard naam ruimte gebruikt wanneer er geen is opgegeven.                                                        |  
| *kube-system* | Waar kern bronnen bestaan, zoals netwerk functies, zoals DNS en proxy, of het Kubernetes-dash board. Normaal gesp roken implementeert u uw eigen toepassingen niet in deze naam ruimte.                                      |  
| *kube-public*                                                                            | Doorgaans niet gebruikt, maar kan worden gebruikt voor bronnen die zichtbaar zijn in het hele cluster, en kunnen worden weer gegeven door elke wille keurige gebruiker.                                                                                    |  


Zie [Kubernetes-naam ruimten][kubernetes-namespaces]voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

In dit artikel worden enkele van de belangrijkste Kubernetes-onderdelen beschreven en hoe deze van toepassing zijn op AKS-clusters. Raadpleeg de volgende artikelen voor meer informatie over de belangrijkste Kubernetes-en AKS-concepten:

- [Kubernetes/AKS-toegang en-identiteit][aks-concepts-identity]
- [Kubernetes/AKS-beveiliging][aks-concepts-security]
- [Kubernetes/AKS virtuele netwerken][aks-concepts-network]
- [Kubernetes/AKS-opslag][aks-concepts-storage]
- [Kubernetes/AKS-schaal][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
