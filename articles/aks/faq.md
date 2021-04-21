---
title: Veelgestelde vragen over Azure Kubernetes Service (AKS)
description: Vind antwoorden op enkele veelvoorkomende vragen over Azure Kubernetes Service (AKS).
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: references_regions
ms.openlocfilehash: f13d7a33ce1dc04700932072fe0af80a901c681f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783193"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Veelgestelde vragen over AKS (Azure Kubernetes Service)

In dit artikel worden veelgestelde vragen over Azure Kubernetes Service (AKS) beantwoord.

## <a name="which-azure-regions-currently-provide-aks"></a>Welke Azure-regio's bieden AKS momenteel?

Zie AKS-regio's en -beschikbaarheid voor een volledige lijst met [beschikbare regio's.][aks-regions]

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>Kan ik een AKS-cluster over regio's verspreiden?

Nee. AKS-clusters zijn regionale resources en kunnen geen regio's overspannen. Zie best practices for business continuity and disaster recovery (Best [practices voor bedrijfscontinuïteit][bcdr-bestpractices] en herstel na noodherstel) voor hulp bij het maken van een architectuur die meerdere regio's omvat.

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>Kan ik een AKS-cluster over beschikbaarheidszones verspreiden?

Ja. U kunt een AKS-cluster implementeren in een of meer [beschikbaarheidszones][availability-zones] in [regio's die deze ondersteunen.][az-regions]

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Kan ik beperken wie toegang heeft tot de Kubernetes API-server?

Ja. Er zijn twee opties voor het beperken van de toegang tot de API-server:

- Gebruik [geautoriseerde IP-bereiken][api-server-authorized-ip-ranges] voor API Server als u een openbaar eindpunt voor de API-server wilt onderhouden, maar de toegang tot een set vertrouwde IP-bereiken wilt beperken.
- Gebruik [een privécluster][private-clusters] als u wilt beperken dat de API-server *alleen* toegankelijk is vanuit uw virtuele netwerk.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Kan ik verschillende VM-grootten in één cluster hebben?

Ja, u kunt verschillende grootten voor virtuele machines in uw AKS-cluster gebruiken door meerdere [knooppuntgroepen te maken.][multi-node-pools]

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Worden beveiligingsupdates toegepast op AKS-agentknooppunten?

Azure past automatisch volgens een planning 's nachts beveiligingspatches toe op de Linux-knooppunten in uw cluster. U bent er echter verantwoordelijk voor dat deze Linux-knooppunten indien nodig opnieuw worden opgestart. U hebt verschillende opties voor het opnieuw opstarten van knooppunten:

- Handmatig, via de Azure Portal of de Azure CLI.
- Door uw AKS-cluster bij te upgraden. Het cluster upgradet [knooppunten][cordon-drain] automatisch bij en brengt vervolgens een nieuw knooppunt online met de meest recente Ubuntu-installatie afbeelding en een nieuwe patchversie of een kleine Kubernetes-versie. Zie Een [AKS-cluster upgraden][aks-upgrade]voor meer informatie.
- Met behulp van de [upgrade van de knooppuntafbeelding](node-image-upgrade.md).

### <a name="windows-server-nodes"></a>Windows Server-knooppunten

Voor Windows Server-knooppunten worden Windows Update niet automatisch uitgevoerd en worden de meest recente updates toegepast. Volgens een vast schema rond de Windows Update releasecyclus en uw eigen validatieproces moet u een upgrade uitvoeren op het cluster en de Windows Server-knooppuntgroep(en) in uw AKS-cluster. Met dit upgradeproces worden knooppunten gemaakt die de meest recente Windows Server-afbeelding en patches uitvoeren, waarna de oudere knooppunten worden verwijderd. Zie Een knooppuntgroep upgraden in AKS voor meer informatie [over dit proces.][nodepool-upgrade]

## <a name="why-are-two-resource-groups-created-with-aks"></a>Waarom worden er twee resourcegroepen gemaakt met AKS?

AKS bouwt voort op een aantal Azure-infrastructuurbronnen, waaronder virtuele-machineschaalsets, virtuele netwerken en beheerde schijven. Hierdoor kunt u gebruikmaken van veel van de belangrijkste mogelijkheden van het Azure-platform binnen de beheerde Kubernetes-omgeving die wordt geleverd door AKS. De meeste typen virtuele Azure-machines kunnen bijvoorbeeld rechtstreeks met AKS worden gebruikt en Azure-reserveringen kunnen worden gebruikt om automatisch kortingen op deze resources te ontvangen.

Voor het inschakelen van deze architectuur omvat elke AKS-implementatie twee resourcegroepen:

1. U maakt de eerste resourcegroep. Deze groep bevat alleen de Kubernetes-serviceresource. De AKS-resourceprovider maakt automatisch de tweede resourcegroep tijdens de implementatie. Een voorbeeld van de tweede resourcegroep is *MC_myResourceGroup_myAKSCluster_eastus*. Zie de volgende sectie voor informatie over het opgeven van de naam van deze tweede resourcegroep.
1. De tweede resourcegroep, ook wel de *knooppuntresourcegroep* genoemd, bevat alle infrastructuurresources die aan het cluster zijn gekoppeld. Deze resources omvatten de kubernetes-knooppunt-VM's, virtuele netwerken en opslag. De knooppuntresourcegroep heeft standaard een naam zoals *MC_myResourceGroup_myAKSCluster_eastus.* AKS verwijdert automatisch de knooppuntresource wanneer het cluster wordt verwijderd. Het moet dus alleen worden gebruikt voor resources die de levenscyclus van het cluster delen.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Kan ik mijn eigen naam voor de resourcegroep van het AKS-knooppunt geven?

Ja. Standaard geeft AKS de knooppuntresourcegroep een *MC_resourcegroupname_clustername_location,* maar u kunt ook uw eigen naam geven.

Als u de naam van uw eigen resourcegroep wilt opgeven, installeert u de Azure [CLI-extensie aks-preview][aks-preview-cli] *versie 0.3.2* of hoger. Wanneer u een AKS-cluster maakt met behulp van [de opdracht az aks create,][az-aks-create] gebruikt u de parameter en geeft u een naam `--node-resource-group` op voor de resourcegroep. Als u [een Azure Resource Manager][aks-rm-template] gebruikt om een AKS-cluster te implementeren, kunt u de naam van de resourcegroep definiëren met behulp van de eigenschap *nodeResourceGroup.*

* De secundaire resourcegroep wordt automatisch gemaakt door de Azure-resourceprovider in uw eigen abonnement.
* U kunt alleen een naam voor een aangepaste resourcegroep opgeven wanneer u het cluster maakt.

Wanneer u met de knooppuntresourcegroep werkt, moet u er rekening mee houden dat u het volgende niet kunt doen:

* Geef een bestaande resourcegroep op voor de knooppuntresourcegroep.
* Geef een ander abonnement op voor de knooppuntresourcegroep.
* Wijzig de naam van de knooppuntresourcegroep nadat het cluster is gemaakt.
* Geef namen op voor de beheerde resources in de knooppuntresourcegroep.
* Door Azure gemaakte tags van beheerde resources in de knooppuntresourcegroep wijzigen of verwijderen. (Zie aanvullende informatie in de volgende sectie.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Kan ik tags en andere eigenschappen van de AKS-resources in de knooppuntresourcegroep wijzigen?

Als u door Azure gemaakte tags en andere resource-eigenschappen in de knooppuntresourcegroep wijzigt of verwijdert, kunt u onverwachte resultaten krijgen, zoals fouten met schalen en upgraden. Met AKS kunt u aangepaste tags maken en wijzigen die door eindgebruikers zijn gemaakt. U kunt deze tags toevoegen wanneer u [een knooppuntgroep maakt.](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool) U kunt bijvoorbeeld aangepaste tags maken of wijzigen om een bedrijfseenheid of kostenplaats toe te wijzen. Dit kan ook worden bereikt door Azure-beleidsregels te maken met een bereik voor de beheerde resourcegroep.

Het wijzigen van door Azure gemaakte **tags** voor resources onder de knooppuntresourcegroep in het AKS-cluster is echter een niet-ondersteunde actie die de serviceniveaudoelstelling (SLO) onderbreekt. Zie Biedt AKS een [service level agreement voor meer informatie?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Welke Kubernetes-toegangscontrollers worden ondersteund door AKS? Kunnen toegangscontrollers worden toegevoegd of verwijderd?

AKS ondersteunt de volgende [toegangscontrollers:][admission-controllers]

- *NaamruimteLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *PodNodeSelector*
- *PodTolerationRestriction*
- *ExtendedResourceToleration*

Op dit moment kunt u de lijst met toegangscontrollers in AKS niet wijzigen.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>Kan ik toegangscontrollerwebhooks in AKS gebruiken?

Ja, u kunt toegangscontrollerwebhooks gebruiken in AKS. Het is raadzaam om interne AKS-naamruimten uit te sluiten, die zijn gemarkeerd met het **label van het besturingsvlak.** Bijvoorbeeld door het onderstaande toe te voegen aan de webhookconfiguratie:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

De API-server wordt via AKS gefirewalld zodat uw toegangscontrollerwebhooks toegankelijk moeten zijn vanuit het cluster.

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>Kunnen toegangscontrollerwebhooks invloed hebben op kube-system en interne AKS-naamruimten?

Om de stabiliteit van het systeem te beschermen en te voorkomen dat aangepaste toegangscontrollers van invloed zijn op interne services in het kube-systeem, heeft naamruimte AKS een **toegangsuitdingsuitdingsserver,** waarmee interne naamruimten van kube-system en AKS automatisch worden uitgesloten. Deze service zorgt ervoor dat de aangepaste toegangscontrollers geen invloed hebben op de services die worden uitgevoerd in kube-system.

Als u een kritieke use-case hebt voor het hebben van iets dat is geïmplementeerd op kube-system (niet aanbevolen) waarvoor u moet worden gedekt door uw aangepaste toegangswebhook, kunt u het onderstaande label of de onderstaande aantekening toevoegen, zodat deze door De toegangsuitgever worden genegeerd.

Label: ```"admissions.enforcer/disabled": "true"``` of Aantekening: ```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>Is Azure Key Vault geïntegreerd met AKS?

AKS is momenteel niet systeemeigen geïntegreerd met Azure Key Vault. De provider [Azure Key Vault CSI Secrets Store][csi-driver] maakt directe integratie van Kubernetes-pods naar Key Vault mogelijk.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Kan ik Windows Server-containers uitvoeren op AKS?

Ja, Windows Server-containers zijn beschikbaar in AKS. Als u Windows Server-containers in AKS wilt uitvoeren, maakt u een knooppuntgroep met Windows Server als gastbesturingssysteem. Windows Server-containers kunnen alleen Windows Server 2019 gebruiken. Zie Een [AKS-cluster maken met een Windows Server-knooppuntgroep om][aks-windows-cli]aan de slag te gaan.

Windows Server-ondersteuning voor knooppuntgroep omvat enkele beperkingen die deel uitmaken van het upstream Windows Server in Kubernetes-project. Zie Windows [Server-containers in AKS-beperkingen][aks-windows-limitations]voor meer informatie over deze beperkingen.

## <a name="does-aks-offer-a-service-level-agreement"></a>Biedt AKS een service level agreement?

AKS biedt SLA-garanties als een optionele invoegfunctie met [Uptime SLA][uptime-sla]. 

De gratis SKU die standaard wordt aangeboden, heeft geen bijbehorende Service *Level* *Agreement,* maar heeft een serviceniveaudoelstelling van 99,5%. Het kan gebeuren dat er tijdelijke verbindingsproblemen worden waargenomen in het geval van upgrades, slechte onderlaagknooppunten, platformonderhoud, toepassing die de API-server overstelpen met aanvragen, enzovoort. Als uw workload het opnieuw opstarten van API Server niet tolereert, raden we u aan de SLA voor uptime te gebruiken.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Kan ik Azure-reserveringskortingen toepassen op mijn AKS-agentknooppunten?

AKS-agentknooppunten worden gefactureerd als standaard virtuele Azure-machines. Als u [Azure-reserveringen][reservation-discounts] hebt aangeschaft voor de VM-grootte die u in AKS gebruikt, worden deze kortingen automatisch toegepast.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Kan ik mijn cluster verplaatsen/migreren tussen Azure-tenants?

Het verplaatsen van uw AKS-cluster tussen tenants wordt momenteel niet ondersteund.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Kan ik mijn cluster verplaatsen/migreren tussen abonnementen?

Het verplaatsen van clusters tussen abonnementen wordt momenteel niet ondersteund.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Kan ik mijn AKS-clusters van het huidige Azure-abonnement naar een ander abonnement verplaatsen?

Het verplaatsen van uw AKS-cluster en de bijbehorende resources tussen Azure-abonnementen wordt niet ondersteund.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>Kan ik mijn AKS-cluster of AKS-infrastructuurresources verplaatsen naar andere resourcegroepen of de naam ervan wijzigen?

Het verplaatsen of wijzigen van de naam van uw AKS-cluster en de bijbehorende resources wordt niet ondersteund.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Waarom duurt het verwijderen van mijn cluster zo lang?

De meeste clusters worden verwijderd op verzoek van de gebruiker; In sommige gevallen, met name wanneer klanten hun eigen resourcegroep gebruiken, of wanneer ze taken tussen RG-overschrijdende taken uitvoeren, kan dit extra tijd kosten of mislukken. Als u een probleem hebt met verwijderen, controleert u of de RG niet is vergrendeld, of alle resources buiten de RG zijn losgekoppeld van de RG, en meer.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Als ik pods/implementaties heb met de status 'NodeLost' of 'Onbekend' kan ik mijn cluster nog steeds upgraden?

Dat kan, maar AKS raadt dit niet aan. Upgrades moeten worden uitgevoerd wanneer de status van het cluster bekend en in orde is.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Kan ik een upgrade uitvoeren als ik een cluster heb met een of meer knooppunten met de status Niet in orde of afgesloten?

Nee, verwijder knooppunten met een mislukte status of verwijder ze op een andere manier uit het cluster voordat u een upgrade gaat uitvoeren.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Ik heb een cluster verwijderd, maar zie de fout `[Errno 11001] getaddrinfo failed`

Dit wordt meestal veroorzaakt doordat gebruikers een of meer netwerkbeveiligingsgroepen (NSG's) nog in gebruik hebben en aan het cluster zijn gekoppeld.  Verwijder ze en probeer het verwijderen opnieuw.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Ik heb een upgrade uitgevoerd, maar nu zitten mijn pods in crashlussen en mislukken gereedheidstests?

Controleer of uw service-principal niet is verlopen.  Zie: [AKS-service-principal](./kubernetes-service-principal.md) en [AKS-updatereferenties](./update-credentials.md).

## <a name="my-cluster-was-working-but-suddenly-cant-provision-loadbalancers-mount-pvcs-etc"></a>Mijn cluster werkte, maar kan plotseling geen LoadBalancers inrichten, PVC's monteren, enzovoort?

Controleer of uw service-principal niet is verlopen.  Zie: [AKS-service-principal](./kubernetes-service-principal.md)  en [AKS-updatereferenties](./update-credentials.md).

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>Kan ik mijn AKS-cluster schalen naar nul?
U kunt een [actief AKS-cluster volledig stoppen,](start-stop-cluster.md)waardoor u bespaart op de respectieve rekenkosten. Daarnaast kunt u er [ `User` ](scale-cluster.md#scale-user-node-pools-to-0) ook voor kiezen om alle of specifieke knooppuntgroepen automatisch te schalen naar 0, met behoud van alleen de benodigde clusterconfiguratie.
U kunt systeem-knooppuntgroepen [niet rechtstreeks naar](use-system-pools.md) nul schalen.

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Kan ik de API's van de virtuele-machineschaalset gebruiken om handmatig te schalen?

Nee, schaalbewerkingen met behulp van de API's van de virtuele-machineschaalset worden niet ondersteund. Gebruik de AKS-API's ( `az aks scale` ).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-zero-nodes"></a>Kan ik virtuele-machineschaalsets gebruiken om handmatig naar nul knooppunten te schalen?

Nee, schaalbewerkingen met behulp van de API's van de virtuele-machineschaalset worden niet ondersteund. U kunt de AKS-API gebruiken om te schalen naar nul niet-systeemknooppuntgroepen of om [uw cluster te](start-stop-cluster.md) stoppen.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Kan ik al mijn VM's stoppen of de toewijzing van deze VM's op de lange termijn ingetrokken?

Hoewel AKS tolerantiemechanismen heeft om bestand te zijn tegen een dergelijke configuratie en om hieruit te herstellen, is dit geen ondersteunde configuratie. [Stop in plaats daarvan uw cluster.](start-stop-cluster.md)

## <a name="can-i-use-custom-vm-extensions"></a>Kan ik aangepaste VM-extensies gebruiken?

De Log Analytics-agent wordt ondersteund omdat het een extensie is die wordt beheerd door Microsoft. Anders is AKS een beheerde service en wordt manipulatie van de IaaS-resources niet ondersteund. Als u aangepaste onderdelen wilt installeren, gebruikt u de Kubernetes-API's en -mechanismen. Gebruik bijvoorbeeld DaemonSets om vereiste onderdelen te installeren.

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>Worden in AKS klantgegevens buiten de regio van het cluster opgeslagen?

De functie voor het opslaan van klantgegevens in één regio is momenteel alleen beschikbaar in de regio Azië - zuidoost (Singapore) van de regio Azië en Stille Oceaan Geo and Brazilië - zuid (Sao Paulo State) Region of Brazil Geo. Voor alle andere regio's worden klantgegevens opgeslagen in Geo.

## <a name="are-aks-images-required-to-run-as-root"></a>Zijn AKS-afbeeldingen vereist om te worden uitgevoerd als root?

Met uitzondering van de volgende twee afbeeldingen zijn AKS-afbeeldingen niet vereist om te worden uitgevoerd als root:

- *mcr.microsoft.com/oss/kubernetes/coredns*
- *mcr.microsoft.com/azuremonitor/containerinsights/ciprod*

## <a name="what-is-azure-cni-transparent-mode-vs-bridge-mode"></a>Wat is Azure CNI transparante modus versus brugmodus?

Vanaf v1.2.0 hebben Azure CNI als standaard transparante modus voor Linux CNI-implementaties met één tenancy. In de transparante modus wordt de brugmodus vervangen. In deze sectie bespreken we meer over de verschillen tussen beide modi en wat de voordelen/beperking zijn voor het gebruik van de transparante modus in Azure CNI.

### <a name="bridge-mode"></a>Brugmodus

Zoals de naam al aangeeft, maakt Azure CNI een L2-brug met de naam 'azure0'. Alle interfaces voor `veth` podparen aan de hostzijde worden verbonden met deze brug. Dus Pod-Pod communicatie tussen VM's en het resterende verkeer gaat via deze brug. De brug in kwestie is een virtueel laag 2-apparaat dat op zichzelf niets kan ontvangen of verzenden, tenzij u er een of meer echte apparaten aan verbindt. Daarom moet eth0 van de Linux-VM worden geconverteerd naar een onderliggende azure0-brug. Hiermee maakt u een complexe netwerktopologie binnen de Linux-VM en als een symptoom moest CNI andere netwerkfuncties uitvoeren, zoals het bijwerken van de DNS-server, bijvoorbeeld.

:::image type="content" source="media/faq/bridge-mode.png" alt-text="Topologie van brugmodus":::

Hieronder ziet u een voorbeeld van hoe het instellen van de IP-route er in de bridgemodus uitziet. Ongeacht het aantal pods dat het knooppunt heeft, zijn er altijd maar twee routes. Het eerste bericht: al het verkeer met uitzondering van lokaal op azure0 gaat naar de standaardgateway van het subnet via de interface met ip 'src 10.240.0.4' (dit is het primaire IP-adres van het knooppunt) en de tweede met de naam '10.20.x.x' Podruimte aan kernel om de kernel te laten beslissen.

```bash
default via 10.240.0.1 dev azure0 proto dhcp src 10.240.0.4 metric 100
10.240.0.0/12 dev azure0 proto kernel scope link src 10.240.0.4
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
root@k8s-agentpool1-20465682-1:/#
```

### <a name="transparent-mode"></a>Transparante modus
De transparante modus biedt een heldere benadering voor het instellen van Linux-netwerken. In deze modus worden Azure CNI eigenschappen van de eth0-interface in de Linux-VM niet gewijzigd. Deze minimale benadering van het wijzigen van de Eigenschappen van Linux-netwerken vermindert problemen met complexe hoekcases waar clusters mee te maken kunnen krijgen met de Bridge-modus. In transparante modus maakt Azure CNI en voegt u podpaarinterfaces aan de hostzijde toe die aan het `veth` hostnetwerk worden toegevoegd. Intra-VM-communicatie tussen pods gaat via IP-routes die de CNI toevoegt. Pod-to-Pod-communicatie gaat in wezen via laag 3 en podverkeer wordt gerouteerd door L3-routeringsregels.

:::image type="content" source="media/faq/transparent-mode.png" alt-text="Topologie van transparante modus":::

Hieronder ziet u een voorbeeld van het instellen van de transparante modus via IP-route. De interface van elke pod krijgt een statische route gekoppeld, zodat verkeer met het eerste IP-adres als de Pod rechtstreeks naar de interface van het hostpaar van de pod wordt `veth` verzonden.

```bash
10.240.0.216 dev azv79d05038592 proto static
10.240.0.218 dev azv8184320e2bf proto static
10.240.0.219 dev azvc0339d223b9 proto static
10.240.0.222 dev azv722a6b28449 proto static
10.240.0.223 dev azve7f326f1507 proto static
10.240.0.224 dev azvb3bfccdd75a proto static
168.63.129.16 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
169.254.169.254 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
```

### <a name="benefits-of-transparent-mode"></a>Voordelen van de transparante modus

- Biedt oplossing voor dns-parallelle racevoorwaarde en het vermijden van `conntrack` problemen met 5 sec DNS-latentie zonder dat u lokale DNS voor knooppunten hoeft in te stellen (u kunt om prestatieredenen nog steeds lokale DNS voor knooppunten gebruiken).
- Elimineert de eerste 5 sec DNS-latentie CNI bridge-modus introduceert vandaag vanwege "Just-In-Time" bridge instellen.
- Een van de hoekgevallen in de brugmodus is dat de Azure CNI niet kan blijven bijwerken van de aangepaste DNS-server lijsten die gebruikers toevoegen aan VNET of NIC. Dit resulteert in de CNI ophalen van alleen het eerste exemplaar van de LIJST met DNS-servers. Opgelost in de transparante modus omdat CNI geen eth0-eigenschappen wijzigt. Kijk hier [voor meer informatie.](https://github.com/Azure/azure-container-networking/issues/713)
- Biedt een betere afhandeling van UDP-verkeer en oplossing voor UDP-overstroming storm wanneer ARP een times-out heeft. Wanneer bridge in de brugmodus geen MAC-adres van de doelpod kent in de communicatie tussen VM's en pods naar pods, resulteert dit in storm van het pakket naar alle poorten. Opgelost in de transparante modus omdat er geen L2-apparaten in het pad zijn. Kijk hier [voor meer informatie.](https://github.com/Azure/azure-container-networking/issues/704)
- De transparante modus presteert beter in communicatie tussen pods tussen VM's wat betreft doorvoer en latentie in vergelijking met de brugmodus.

## <a name="how-to-avoid-permission-ownership-setting-slow-issues-when-the-volume-has-a-lot-of-files"></a>Hoe kan ik problemen met het eigendom van machtigingen voorkomen wanneer het volume veel bestanden heeft?

Traditioneel als uw pod wordt uitgevoerd als een niet-hoofdgebruiker (wat u moet doen), moet u een opgeven binnen de beveiligingscontext van de pod, zodat het volume leesbaar en beschrijfbaar is voor de `fsGroup` Pod. Deze vereiste wordt hier [uitvoeriger behandeld.](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)

Maar een neveneffect van de instelling is dat Kubernetes steeds recursief moet worden gebruikt en dat alle bestanden en mappen in het volume recursief moeten worden gebruikt, met enkele uitzonderingen die hieronder worden `fsGroup` `chown()` `chmod()` vermeld. Dit gebeurt zelfs als het groepseigendom van het volume al overeenkomt met de aangevraagde , en kan behoorlijk duur zijn voor grotere volumes met veel kleine bestanden, waardoor het opstarten van pods lang `fsGroup` duurt. Dit scenario is een bekend probleem vóór v1.20 en de tijdelijke oplossing is het instellen van de Pod-run als root:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

Het probleem is opgelost door Kubernetes v1.20. Raadpleeg [Kubernetes 1.20:](https://kubernetes.io/blog/2020/12/14/kubernetes-release-1.20-fsgroupchangepolicy-fsgrouppolicy/) Gedetailleerde controle van wijzigingen in volumemachtigingen voor meer informatie.


<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az_aks_create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: ./windows-faq.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/
