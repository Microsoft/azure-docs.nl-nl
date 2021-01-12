---
title: Veelgestelde vragen over Azure Kubernetes service (AKS)
description: Vind antwoorden op enkele veelgestelde vragen over Azure Kubernetes service (AKS).
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 7fc348ae7b3edb79e75aa1acd08941fec447da6f
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127631"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Veelgestelde vragen over AKS (Azure Kubernetes Service)

Dit artikel behandelt Veelgestelde vragen over Azure Kubernetes service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Welke Azure-regio's bieden momenteel AKS?

Zie [AKS-regio's en beschik baarheid][aks-regions]voor een volledige lijst met beschik bare regio's.

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>Kan ik een AKS-cluster spreiden over regio's?

Nee. AKS-clusters zijn regionale bronnen en kunnen geen regio's omvatten. Zie [Aanbevolen procedures voor bedrijfs continuïteit en herstel na nood gevallen][bcdr-bestpractices] voor meer informatie over het maken van een architectuur met meerdere regio's.

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>Kan ik een AKS-cluster spreiden over beschikbaarheids zones?

Ja. U kunt een AKS-cluster implementeren in een of meer [beschikbaarheids zones][availability-zones] in [regio's die ze ondersteunen][az-regions].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Kan ik de toegang tot de Kubernetes-API-server beperken?

Ja. Er zijn twee opties voor het beperken van de toegang tot de API-server:

- Gebruik [API server Authorized IP Ranges][api-server-authorized-ip-ranges] als u een openbaar eind punt voor de API-server wilt behouden, maar de toegang tot een set vertrouwde IP-bereiken wilt beperken.
- Gebruik [een persoonlijk cluster][private-clusters] als u wilt beperken dat de API-server *alleen* toegankelijk is vanuit uw virtuele netwerk.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Kan ik verschillende VM-grootten in één cluster hebben?

Ja, u kunt verschillende groottes van virtuele machines in uw AKS-cluster gebruiken door [meerdere knooppunt groepen][multi-node-pools]te maken.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Worden beveiligings updates toegepast op agent knooppunten van AKS?

Azure past automatisch beveiligings patches toe op de Linux-knoop punten in uw cluster volgens een nacht planning. U bent echter zelf verantwoordelijk om ervoor te zorgen dat deze Linux-knoop punten zo nodig opnieuw worden opgestart. U hebt verschillende opties voor het opnieuw opstarten van knoop punten:

- Hand matig, via de Azure Portal of de Azure CLI.
- Door uw AKS-cluster bij te werken. Met het cluster worden automatisch de [Cordon-en afvoer knooppunten][cordon-drain] bijgewerkt en vervolgens een nieuw knoop punt online met de meest recente Ubuntu-installatie kopie en een nieuwe patch versie of een secundaire Kubernetes-versie. Zie [een AKS-cluster upgraden][aks-upgrade]voor meer informatie.
- Door een [upgrade van de knooppunt installatie kopie](node-image-upgrade.md)te gebruiken.

### <a name="windows-server-nodes"></a>Windows Server-knoop punten

Voor Windows Server-knoop punten wordt Windows Update niet automatisch uitgevoerd en worden de nieuwste updates toegepast. U moet een upgrade uitvoeren op het cluster en de Windows Server-knooppunt groep (en) in uw AKS-cluster, volgens een regel matige planning rond de Windows Update release cyclus en uw eigen validatie proces. Dit upgrade proces maakt knoop punten waarop de nieuwste installatie kopie en patches van Windows Server worden uitgevoerd, waarna de oudere knoop punten worden verwijderd. Zie [een knooppunt groep bijwerken in AKS][nodepool-upgrade]voor meer informatie over dit proces.

## <a name="why-are-two-resource-groups-created-with-aks"></a>Waarom zijn er twee resource groepen gemaakt met AKS?

AKS bouwt voort op een aantal Azure-infrastructuur resources, zoals schaal sets voor virtuele machines, virtuele netwerken en beheerde schijven. Op die manier kunt u veel van de kern mogelijkheden van het Azure-platform gebruiken binnen de beheerde Kubernetes-omgeving van AKS. De meeste typen van virtuele machines van Azure kunnen bijvoorbeeld rechtstreeks worden gebruikt met AKS en Azure Reservations kunnen worden gebruikt voor het automatisch ontvangen van kortingen op deze resources.

Om deze architectuur in te scha kelen, omvat elke AKS-implementatie twee resource groepen:

1. U maakt de eerste resource groep. Deze groep bevat alleen de Kubernetes-service resource. De resource provider AKS maakt automatisch de tweede resource groep tijdens de implementatie. Een voor beeld van de tweede resource groep is *MC_myResourceGroup_myAKSCluster_eastus*. Zie de volgende sectie voor meer informatie over het opgeven van de naam van deze tweede resource groep.
1. De tweede resource groep, ook wel de *resource groep knoop punt* genoemd, bevat alle infrastructuur resources die zijn gekoppeld aan het cluster. Deze resources omvatten de Kubernetes-knoop punt-Vm's, virtuele netwerken en opslag. De resource groep van het knoop punt heeft standaard een naam als *MC_myResourceGroup_myAKSCluster_eastus*. AKS verwijdert automatisch de knooppunt resource wanneer het cluster wordt verwijderd. dit moet daarom alleen worden gebruikt voor resources die de levens cyclus van het cluster delen.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Kan ik mijn eigen naam opgeven voor de resource groep van het AKS-knoop punt?

Ja. AKS krijgt standaard de naam van de knooppunt resource groep *MC_resourcegroupname_clustername_location*, maar u kunt ook uw eigen naam opgeven.

Als u de naam van uw eigen resource groep wilt opgeven, installeert u de [AKS-preview][aks-preview-cli] Azure cli-extensie versie *0.3.2* of hoger. Wanneer u een AKS-cluster maakt met behulp van de opdracht [AZ AKS Create][az-aks-create] , gebruikt u de `--node-resource-group` para meter en geeft u een naam op voor de resource groep. Als u [een Azure Resource Manager-sjabloon gebruikt][aks-rm-template] om een AKS-cluster te implementeren, kunt u de naam van de resource groep definiëren met behulp van de eigenschap *nodeResourceGroup* .

* De secundaire resource groep wordt automatisch gemaakt door de Azure-resource provider in uw eigen abonnement.
* U kunt alleen een aangepaste resource groeps naam opgeven wanneer u het cluster maakt.

Houd er bij het werken met de knooppunt resource groep voor dat u niet:

* Geef een bestaande resource groep op voor de knooppunt resource groep.
* Geef een ander abonnement op voor de knooppunt resource groep.
* Wijzig de naam van de resource groep van het knoop punt nadat het cluster is gemaakt.
* Geef namen voor de beheerde resources op in de resource groep van het knoop punt.
* Door Azure gemaakte Tags van beheerde resources wijzigen of verwijderen in de resource groep van het knoop punt. (Zie de volgende sectie voor meer informatie.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Kan ik tags en andere eigenschappen van de AKS-resources in de knooppunt resource groep wijzigen?

Als u door Azure gemaakte Tags en andere bron eigenschappen in de knooppunt resource groep wijzigt of verwijdert, kunt u onverwachte resultaten krijgen, zoals het schalen en upgraden van fouten. Met AKS kunt u aangepaste tags maken en wijzigen die zijn gemaakt door eind gebruikers, en kunt u deze Tags toevoegen wanneer u [een groep van knoop punten maakt](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool). Het is raadzaam om aangepaste labels te maken of te wijzigen, bijvoorbeeld om een bedrijfs eenheid of kosten plaats toe te wijzen. Dit kan ook worden bereikt door Azure-beleids regels te maken met een bereik voor de beheerde resource groep.

Het wijzigen van door **Azure gemaakte Tags** op resources onder de knooppunt resource groep in het AKS-cluster is echter een niet-ondersteunde actie, waardoor de serviceniveau doelstelling (SLO) wordt verbroken. Zie voor meer informatie [AKS biedt een service overeenkomst?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Welke Kubernetes-toegangs controllers ondersteunt AKS? Kunnen Admission controllers worden toegevoegd of verwijderd?

AKS ondersteunt de volgende [toegangs controllers][admission-controllers]:

- *NamespaceLifecycle*
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

Op dit moment kunt u de lijst met toegangs controllers in AKS niet wijzigen.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>Kan ik de webhooks van de toegangs controller gebruiken op AKS?

Ja, u kunt toegangs beheer-webhooks gebruiken op AKS. Het is raadzaam om interne AKS-naam ruimten, die zijn gemarkeerd met het **label van het besturings element** , uit te sluiten. U kunt bijvoorbeeld het onderstaande toevoegen aan de configuratie van de webhook:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

AKS firewallt de API-server uit, zodat de toegangs beheer-webhooks toegankelijk moeten zijn vanuit het cluster.

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>Kan de toegangs beheer-webhooks invloed hebben op uitvoeren-System en interne AKS-naam ruimten?

Om de stabiliteit van het systeem te beschermen en te voor komen dat aangepaste toegangs controllers van invloed zijn op interne services in het uitvoeren-systeem, is naam ruimte AKS een **toegangs Afdwinger** die automatisch uitvoeren-systeem en AKS interne naam ruimten uitsluit. Deze service zorgt ervoor dat de aangepaste toegangs controllers niet van invloed zijn op de services die worden uitgevoerd in uitvoeren-System.

Als u een belang rijke gebruiks situatie hebt om iets te implementeren op een uitvoeren-systeem (niet aanbevolen), wat u nodig hebt om te worden gedekt door uw aangepaste Admission-webhook, kunt u het onderstaande label of de aantekening toevoegen, zodat de toegangs afdwingt deze negeert.

Label: ```"admissions.enforcer/disabled": "true"``` of aantekening: ```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>Is Azure Key Vault geïntegreerd met AKS?

AKS is momenteel niet ingebouwd met Azure Key Vault. De [Azure Key Vault provider voor CSI Secrets Store][csi-driver] maakt echter directe integratie van Kubernetes van peul tot Key Vault geheimen.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Kan ik Windows Server-containers op AKS uitvoeren?

Ja, Windows Server-containers zijn beschikbaar op AKS. Als u Windows Server-containers in AKS wilt uitvoeren, maakt u een knooppunt groep die Windows Server als het gast besturingssysteem uitvoert. Windows Server-containers kunnen alleen Windows Server 2019 gebruiken. Zie [een AKS-cluster maken met een Windows Server-knooppunt groep][aks-windows-cli]om aan de slag te gaan.

Windows Server-ondersteuning voor de knooppunt groep bevat enkele beperkingen die deel uitmaken van de upstream-Windows-Server in Kubernetes-project. Zie [Windows Server-containers in AKS-beperkingen][aks-windows-limitations]voor meer informatie over deze beperkingen.

## <a name="does-aks-offer-a-service-level-agreement"></a>Biedt AKS een service overeenkomst?

AKS biedt SLA-garanties als een optionele invoeg functie met [Sla voor uptime][uptime-sla].

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Kan ik Azure-reserverings kortingen Toep assen op mijn AKS-agent knooppunten?

AKS-agent knooppunten worden gefactureerd als standaard virtuele machines van Azure, dus als u [Azure-reserve ringen][reservation-discounts] hebt aangeschaft voor de VM-grootte die u in AKS gebruikt, worden deze kortingen automatisch toegepast.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Kan ik mijn cluster verplaatsen/migreren tussen Azure-tenants?

Het verplaatsen van uw AKS-cluster tussen tenants wordt momenteel niet ondersteund.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Kan ik mijn cluster verplaatsen/migreren tussen abonnementen?

Het verplaatsen van clusters tussen abonnementen wordt momenteel niet ondersteund.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Kan ik mijn AKS-clusters van het huidige Azure-abonnement naar een andere verplaatsen?

Het verplaatsen van uw AKS-cluster en de bijbehorende resources tussen Azure-abonnementen wordt niet ondersteund.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>Kan ik mijn AKS-cluster of AKS-infrastructuur resources verplaatsen naar andere resource groepen of de naam ervan wijzigen?

Het verplaatsen of hernoemen van uw AKS-cluster en de bijbehorende resources worden niet ondersteund.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Waarom duurt het verwijderen van een cluster?

De meeste clusters worden verwijderd bij de gebruikers aanvraag; in sommige gevallen, met name waarbij klanten hun eigen resource groep halen of het verwijderen van cross-RG taken, kan het langer duren of mislukken. Als u een probleem met verwijderingen ondervindt, controleert u of u geen vergren delingen op de RG hebt, of bronnen buiten de RG zijn ontkoppeling van de RG, enzovoort.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Als ik pod/Deployments heb in de status NodeLost of Unknown, kan ik mijn cluster nog steeds bijwerken?

Dit kan, maar AKS wordt dit niet aanbevolen. Upgrades moeten worden uitgevoerd wanneer de status van het cluster bekend en in orde is.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Als ik een cluster met een of meer knoop punten met een slechte status of computer heb afgesloten, kan ik dan een upgrade uitvoeren?

Nee, verwijderen/verwijderen van knoop punten met een mislukte status of anderszins verwijderd uit het cluster vóór de upgrade.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Ik heb een cluster verwijderd, maar zie de fout `[Errno 11001] getaddrinfo failed`

Dit wordt meestal veroorzaakt door gebruikers die een of meer netwerk beveiligings groepen (Nsg's) nog in gebruik hebben en aan het cluster zijn gekoppeld.  Verwijder ze en probeer opnieuw te verwijderen.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Ik heb een upgrade uitgevoerd, maar nu zijn mijn peulen in de loop van een crash, en mislukt de gereedheids tests?

Bevestig dat uw Service-Principal niet is verlopen.  Zie: [AKS Service Principal](./kubernetes-service-principal.md) and [AKS update credentials](./update-credentials.md).

## <a name="my-cluster-was-working-but-suddenly-cant-provision-loadbalancers-mount-pvcs-etc"></a>Mijn cluster werkte, maar kan plotseling geen LoadBalancers, Mount-Pvc's, enzovoort inrichten?

Bevestig dat uw Service-Principal niet is verlopen.  Zie: [AKS Service Principal](./kubernetes-service-principal.md)  and [AKS update credentials](./update-credentials.md).

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>Kan ik mijn AKS-cluster op nul schalen?
U kunt [een actief AKS-cluster volledig stoppen](start-stop-cluster.md)en besparen op de respectieve reken kosten. Daarnaast kunt u ook kiezen om [alle of specifieke `User` knooppunt groepen te schalen of automatisch te schalen](scale-cluster.md#scale-user-node-pools-to-0) op 0, zodat alleen de benodigde cluster configuratie behouden blijft.
U kunt [systeem knooppunt groepen](use-system-pools.md) niet rechtstreeks schalen naar nul.

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Kan ik de Api's voor de schaalset van de virtuele machine gebruiken om hand matig te schalen?

Nee, schaal bewerkingen met behulp van de virtuele-machine Scale set-Api's worden niet ondersteund. Gebruik de AKS-Api's ( `az aks scale` ).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-zero-nodes"></a>Kan ik schaal sets voor virtuele machines gebruiken om hand matig te schalen naar nul knoop punten?

Nee, schaal bewerkingen met behulp van de virtuele-machine Scale set-Api's worden niet ondersteund. U kunt de AKS-API gebruiken om te schalen naar nul niet-systeem knooppunt groepen of [het cluster stoppen](start-stop-cluster.md) .

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Kan ik al mijn Vm's stoppen of de toewijzing ervan ongedaan maken?

Hoewel AKS de mechanismen voor flexibiliteit heeft om een dergelijke configuratie te verkrijgen en hiervan te herstellen, is dit geen ondersteunde configuratie. [Stop het cluster](start-stop-cluster.md) .

## <a name="can-i-use-custom-vm-extensions"></a>Kan ik aangepaste VM-extensies gebruiken?

De Log Analytics-agent wordt ondersteund omdat het een extensie is die door micro soft wordt beheerd. Anders Nee, AKS is een beheerde service, en manipulatie van de IaaS-resources wordt niet ondersteund. Als u aangepaste onderdelen wilt installeren, gebruikt u de Kubernetes-Api's en-mechanismen. Gebruik bijvoorbeeld DaemonSets om de vereiste onderdelen te installeren.

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>Slaat AKS klant gegevens buiten de regio van het cluster op?

De functie voor het opslaan van klant gegevens in één regio is momenteel alleen beschikbaar in de regio Zuidoost-Azië (Singapore) van de Azië en Stille Oceaan geo. Voor alle andere regio's worden klantgegevens opgeslagen in Geo.

## <a name="are-aks-images-required-to-run-as-root"></a>Moeten AKS-installatie kopieën worden uitgevoerd als root?

Met uitzonde ring van de volgende twee installatie kopieën hoeven AKS-installatie kopieën niet te worden uitgevoerd als root:

- *mcr.microsoft.com/oss/kubernetes/coredns*
- *mcr.microsoft.com/azuremonitor/containerinsights/ciprod*

## <a name="what-is-azure-cni-transparent-mode-vs-bridge-mode"></a>Wat is de transparante modus van Azure CNI versus de modus brug?

Van v 1.2.0 Azure CNI heeft de transparante modus als standaard voor de implementaties van één pacht Linux-CNI. In de transparante modus wordt de brug modus vervangen. In deze sectie bespreken we meer over de verschillen in beide modi en wat zijn de voor delen/beperking voor het gebruik van de transparante modus in azure CNI.

### <a name="bridge-mode"></a>Brug modus

Zoals de naam suggereert, maakt de brug modus van Azure CNI, in een ' just-in-time ' mode, een L2-brug met de naam ' azure0 '. Alle interfaces van de pod-koppeling aan de host zijde `veth` worden verbonden met deze brug. Pod-Pod intra VM-communicatie en het resterende verkeer passeren deze brug. De desbetreffende Bridge is een virtueel apparaat van laag 2 dat op zichzelf niets kan ontvangen of verzenden, tenzij u een of meer echte apparaten verbindt. Daarom moet eth0 van de virtuele Linux-machine worden geconverteerd naar een ondergeschikte Bridge-brug (' azure0 '). Hiermee maakt u een complexe netwerk topologie in de Linux-VM en als symptoom CNI moet u zorgen maken voor andere netwerk functies zoals DNS-Server Update, enzovoort.

:::image type="content" source="media/faq/bridge-mode.png" alt-text="Topologie van de brug modus":::

Hieronder ziet u een voor beeld van hoe de configuratie van de IP-route eruitziet in de modus brug. Ongeacht het aantal peulen dat het knoop punt heeft, is er slechts twee routes. De eerste keer dat alle verkeer met uitzonde ring van lokaal op azure0 via de interface met het IP-adres 10.240.0.4 (de primaire IP van het knoop punt) en de tweede met de waarde "10.20. x. x" Pod ruimte voor de kernel wordt beslist.

```bash
default via 10.240.0.1 dev azure0 proto dhcp src 10.240.0.4 metric 100
10.240.0.0/12 dev azure0 proto kernel scope link src 10.240.0.4
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
root@k8s-agentpool1-20465682-1:/#
```

### <a name="transparent-mode"></a>Transparante modus
De transparante modus heeft een rechte benadering voor het instellen van Linux-netwerken. In deze modus wijzigt Azure CNI geen eigenschappen van de eth0-interface in de Linux-VM. Deze minimale benadering van het wijzigen van de Linux-netwerk eigenschappen vermindert het aantal complexe hoek problemen die clusters kunnen oplopen met de brug modus. In de transparante modus maakt Azure CNI de pod- `veth` koppel interfaces aan de host zijde die worden toegevoegd aan het hostnetwerkadapter. De intra VM Pod-to-pod-communicatie is via IP-routes die de CNI gaat toevoegen. In wezen Pod-to-pod-communicatie is meer dan laag 3 en pod verkeer wordt gerouteerd door N3-routerings regels.

:::image type="content" source="media/faq/transparent-mode.png" alt-text="Topologie van transparante modus":::

Hieronder ziet u een voor beeld van een configuratie van een IP-route van de transparante modus. elke pod-interface krijgt een statische route die is gekoppeld zodat verkeer met het doel-IP-adres als pod rechtstreeks naar de interface van de host-Side van het Pod wordt verzonden `veth` .

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

### <a name="benefits-of-transparent-mode"></a>Voor delen van transparante modus

- Voorziet in een oplossing voor de `conntrack` parallelle race voorwaarde en het vermijden van 5 sec. DNS-latentie problemen zonder dat u lokaal knoop punt-DNS hoeft in te stellen (u kunt nog steeds lokale DNS gebruiken om prestatie redenen).
- Elimineert de eerste 5-sec. CNI Bridge-modus van de DNS-latentie wordt vandaag geïntroduceerd door de brug installatie van ' just-in-time '.
- Een van de hoek gevallen in de brug modus is dat de Azure CNI de aangepaste DNS-Server lijsten die gebruikers toevoegen aan VNET of NIC niet kunnen blijven bijwerken. Dit leidt ertoe dat de CNI alleen de eerste instantie van de lijst met DNS-servers ophaalt. Opgelost in de transparante modus als CNI geen eth0-eigenschappen wijzigt. Meer informatie vindt u [hier](https://github.com/Azure/azure-container-networking/issues/713).
- Biedt een betere afhandeling van UDP-verkeer en-beperking voor UDP-flood Storm wanneer er een time-out optreedt voor ARP. Als Bridge in de brug modus geen MAC-adres van de doel-pod in de intra-VM-Pod-to-pod-communicatie kent, resulteert dit in een ontwerp van het pakket naar alle poorten. Opgelost in de transparante modus omdat er geen L2-apparaten aanwezig zijn in het pad. Meer informatie vindt u [hier](https://github.com/Azure/azure-container-networking/issues/704).
- De transparante modus voert betere Pod-to-pod-communicatie binnen de virtuele machine uit met betrekking tot de door Voer en latentie in vergelijking met de brug modus.

## <a name="how-to-avoid-permission-ownership-setting-slow-issues-when-the-volume-has-a-lot-of-files"></a>Hoe voorkom ik dat machtigingen eigendom trage problemen instellen wanneer het volume veel bestanden heeft?

Traditioneel als uw Pod wordt uitgevoerd als een niet-hoofd gebruiker (wat u moet doen), moet u een `fsGroup` in de beveiligings context van de pod opgeven, zodat het volume kan worden gelezen en schrijfbaar door de pod. Deze vereiste wordt verderop in dit [onderwerp](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)beschreven.

Maar een neven effect van de instelling `fsGroup` is dat elke keer dat er een volume wordt gekoppeld, Kubernetes recursief `chown()` en `chmod()` alle bestanden en mappen in het volume zijn, met enkele uitzonde ringen die hieronder zijn vermeld. Dit gebeurt zelfs als het groeps eigendom van het volume al overeenkomt met het aangevraagde `fsGroup` , en kan een aanzienlijke duur zijn voor grotere volumes met veel kleine bestanden, waardoor het opstarten van de pod lange tijd in beslag neemt. Dit scenario is een bekend probleem vóór v 1.20 en de tijdelijke oplossing is het instellen van de pod run as root:

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

Het probleem is opgelost door Kubernetes v 1.20, raadpleegt u [Kubernetes 1,20: nauw keurigheid van volume machtigingen wijzigen](https://kubernetes.io/blog/2020/12/14/kubernetes-release-1.20-fsgroupchangepolicy-fsgrouppolicy/) voor meer informatie.


<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
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
