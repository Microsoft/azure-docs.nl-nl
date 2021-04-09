---
title: Ondersteunings beleid voor Azure Kubernetes service (AKS)
description: Meer informatie over het ondersteunings beleid voor Azure Kubernetes service (AKS), de gedeelde verantwoordelijkheid en functies die in Preview zijn (of alpha of Beta).
services: container-service
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 86b1c0bba30b41a2ee17cfbdf05286c4d2b3fb8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91892707"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Ondersteunings beleid voor de Azure Kubernetes-service

In dit artikel vindt u informatie over het technische ondersteunings beleid en de beperkingen voor Azure Kubernetes service (AKS). Het artikel bevat ook informatie over het beheer van agents, Managed Control plaat-onderdelen, open source-onderdelen van derden en beveiligings-of patch beheer.

## <a name="service-updates-and-releases"></a>Service-updates en-releases

* Zie [AKS Release Notes](https://github.com/Azure/AKS/releases)(Engelstalig) voor informatie over de release.
* Zie [AKS preview-functies en gerelateerde projecten](https://awesomeopensource.com/projects/aks?categoryPage=11)voor meer informatie over de functies van de preview-versie.

## <a name="managed-features-in-aks"></a>Beheerde functies in AKS

Met IaaS-Cloud onderdelen (Infrastructure as a Service), zoals Compute-of netwerk onderdelen, kunt u toegang krijgen tot besturings elementen op laag niveau en aanpassings opties. AKS biedt daarentegen een kant-en-klare Kubernetes-implementatie die u de gemeen schappelijke set configuraties en mogelijkheden biedt voor uw cluster. Als AKS-gebruiker beschikt u over beperkte aanpassings-en implementatie opties. In Exchange hoeft u zich geen zorgen te maken over Kubernetes-clusters of ze rechtstreeks te beheren.

Met AKS krijgt u een volledig beheerd *besturings vlak*. Het besturings vlak bevat alle onderdelen en services die u nodig hebt om te kunnen omgaan en Kubernetes-clusters aan eind gebruikers te bieden. Alle Kubernetes-onderdelen worden onderhouden en beheerd door micro soft.

Micro soft beheert en bewaakt de volgende onderdelen via het deel venster beheer:

* Kubelet-of Kubernetes-API-servers
* Etcd of een compatibele sleutel-value Store, waarmee u Quality of Service (QoS), schaal baarheid en runtime kunt bieden
* DNS-services (bijvoorbeeld uitvoeren-DNS of CoreDNS)
* Kubernetes-proxy of-netwerken
* Een extra invoeg toepassing of systeem onderdeel dat wordt uitgevoerd in de uitvoeren-naam ruimte

AKS is geen PaaS-oplossing (platform-as-a-Service). Sommige onderdelen, zoals agent knooppunten, hebben *gedeelde verantwoordelijkheid*, waar gebruikers het AKS-cluster moeten onderhouden. Gebruikers invoer is vereist, bijvoorbeeld om een beveiligings patch voor het besturings systeem van een agent knooppunt toe te passen.

De services worden *beheerd* in de zin dat micro soft en het AKS-team implementeert, werkt en verantwoordelijk is voor de beschik baarheid en functionaliteit van de service. Klanten kunnen deze beheerde onderdelen niet wijzigen. Micro soft beperkt de aanpassing tot een consistente en schaal bare gebruikers ervaring. Zie de [AKS-engine](https://github.com/Azure/aks-engine)voor een volledig aanpas bare oplossing.

## <a name="shared-responsibility"></a>Gedeelde verantwoordelijkheid

Wanneer een cluster wordt gemaakt, definieert u de Kubernetes-agent knooppunten die AKS maakt. Uw workloads worden op deze knoop punten uitgevoerd.

Omdat uw agent knooppunten persoonlijke code uitvoeren en gevoelige gegevens opslaat, kan Microsoft Ondersteuning deze alleen op een zeer beperkte manier benaderen. Microsoft Ondersteuning kunt zich niet aanmelden bij, opdrachten uitvoeren in of logboeken weer geven voor deze knoop punten zonder uw uitdrukkelijke toestemming of ondersteuning.

Alle wijzigingen die rechtstreeks aan de agent knooppunten worden uitgevoerd via een van de IaaS-Api's, genereren het cluster niet meer. Alle wijzigingen die worden aangebracht aan de agent knooppunten, moeten worden uitgevoerd met behulp van kubernetes-systeem eigen mechanismen, zoals `Daemon Sets` .

En u kunt ook meta gegevens toevoegen aan het cluster en knoop punten, zoals tags en labels, waardoor het cluster niet meer wordt ondersteund als u een van de door het systeem gemaakte meta gegevens wijzigt.

## <a name="aks-support-coverage"></a>AKS-ondersteunings dekking

Micro soft biedt technische ondersteuning voor de volgende voor beelden:

* Connectiviteit met alle Kubernetes-onderdelen die de Kubernetes-service biedt en ondersteunt, zoals de API-server.
* Beheer, uptime, QoS en activiteiten van Kubernetes Control vlak-Services (Kubernetes Control-vlak, API-server, etcd en coreDNS, bijvoorbeeld).
* Etcd-gegevens archief. Ondersteuning omvat geautomatiseerde, transparante back-ups van alle etcd-gegevens om de 30 minuten voor nood planning en herstel van de cluster status. Deze back-ups zijn niet rechtstreeks beschikbaar voor u of voor gebruikers. Ze garanderen de betrouw baarheid en consistentie van gegevens. Etcd. terugdraai acties op aanvraag of herstellen wordt niet ondersteund als onderdeel.
* Alle integratie punten in het stuur programma van de Azure-Cloud provider voor Kubernetes. Dit omvat integraties in andere Azure-Services, zoals load balancers, permanente volumes of netwerken (Kubernetes en Azure CNI).
* Vragen of problemen met betrekking tot het aanpassen van beheer vlak onderdelen, zoals de Kubernetes API-server, etcd en coreDNS.
* Problemen met netwerken, zoals Azure CNI, kubenet of andere problemen met netwerk toegang en-functionaliteit. Problemen zijn onder andere DNS-omzetting, pakket verlies, route ring, enzovoort. Micro soft biedt ondersteuning voor verschillende netwerk scenario's:
  * Kubenet en Azure CNI met behulp van beheerde VNETs of aangepaste subnetten (gebruik uw eigen).
  * Connectiviteit met andere Azure-Services en-toepassingen
  * Inkomend controllers en ingangs-of load balancer configuraties
  * Netwerk prestaties en latentie


> [!NOTE]
> Alle cluster acties die door micro soft/AKS worden uitgevoerd, worden gemaakt met toestemming van de gebruiker onder een ingebouwde Kubernetes-rol `aks-service` en ingebouwde functie binding `aks-service-rolebinding` . Deze rol stelt AKS in staat om problemen met het cluster op te lossen en te diagnosticeren, maar kan geen machtigingen wijzigen of rollen of roltoewijzingen maken, of andere acties met hoge bevoegdheden. Rollen toegang is alleen ingeschakeld onder actieve ondersteunings tickets met Just-in-time-toegang.

Micro soft biedt geen technische ondersteuning voor de volgende voor beelden:

* Vragen over het gebruik van Kubernetes. Microsoft Ondersteuning biedt bijvoorbeeld geen advies over het maken van aangepaste inkomende controllers, het gebruiken van werk belastingen voor toepassingen of het Toep assen van software pakketten of hulpprogram ma's van derden of open source.
  > [!NOTE]
  > Microsoft Ondersteuning kunt advies geven over de functionaliteit, aanpassing en fijnafstelling van AKS-clusters (bijvoorbeeld Kubernetes-bewerkingen en procedures).
* Open-source projecten van derden die niet worden meegeleverd als onderdeel van het Kubernetes-besturings vlak of worden geïmplementeerd met AKS-clusters. Deze projecten kunnen Istio, helm, Envoy of anderen bevatten.
  > [!NOTE]
  > Micro soft kan de beste ondersteuning bieden voor open-source projecten van derden, zoals helm. Wanneer het open source-hulp programma van derden is geïntegreerd met de Kubernetes Azure-Cloud provider of andere AKS, ondersteunt micro soft de voor beelden en toepassingen van micro soft-documentatie.
* Afgesloten bron software van derden. Deze software kan beveiligings scan Programma's en netwerk apparaten of-software omvatten.
* Andere netwerk aanpassingen dan degene die worden vermeld in de [AKS-documentatie](./index.yml).


## <a name="aks-support-coverage-for-agent-nodes"></a>AKS-ondersteunings dekking voor agent knooppunten

### <a name="microsoft-responsibilities-for-aks-agent-nodes"></a>Micro soft-verantwoordelijkheden voor AKS-agent knooppunten

De verantwoordelijkheid van micro soft en gebruikers delen voor Kubernetes-agent knooppunten waar:

* De basis installatie kopie van het besturings systeem heeft vereiste toevoegingen (zoals controle-en netwerk agents).
* De agent knooppunten ontvangen automatisch OS-patches.
* Problemen met de Kubernetes-besturings vlak onderdelen die worden uitgevoerd op de agent knooppunten, worden automatisch hersteld. Deze onderdelen zijn onder andere:
  * `Kube-proxy`
  * Netwerk tunnels die communicatie paden naar de hoofd onderdelen van de Kubernetes bieden
  * `Kubelet`
  * `Moby` of `ContainerD`

> [!NOTE]
> Als een agent knooppunt niet operationeel is, kan AKS afzonderlijke onderdelen of het hele agent-knoop punt opnieuw starten. Deze herstart bewerkingen worden geautomatiseerd en bieden automatisch herstel voor veelvoorkomende problemen. Zie [automatisch herstellen van knoop punten](node-auto-repair.md) als u meer wilt weten over de mechanismen voor automatisch herstel.

### <a name="customer-responsibilities-for-aks-agent-nodes"></a>Verantwoordelijkheden van de klant voor AKS-agent knooppunten

Micro soft biedt patches en nieuwe installatie kopieën voor uw afbeeldings knooppunten wekelijks, maar de standaard patches worden niet automatisch door gerepareerd. Als u wilt dat het besturings systeem en runtime-onderdelen van het agent knooppunt worden gepatchd, moet u een regel matige upgrade planning voor de [knooppunt installatie](node-image-upgrade.md) of deze automatiseren.

Op dezelfde manier geeft AKS regel matig nieuwe kubernetes-patches en secundaire versies vrij. Deze updates kunnen beveiligings-of functionaliteits verbeteringen in Kubernetes bevatten. U bent verantwoordelijk om ervoor te zorgen dat de kubernetes-versie van uw clusters is bijgewerkt en volgens het [AKS kubernetes-ondersteunings versie beleid](supported-kubernetes-versions.md).

#### <a name="user-customization-of-agent-nodes"></a>Gebruikers aanpassing van agent knooppunten
> [!NOTE]
> AKS-agent knooppunten worden weer gegeven in de Azure Portal als normale Azure IaaS-resources. Maar deze virtuele machines worden geïmplementeerd in een aangepaste Azure-resource groep (meestal voorafgegaan door MC_ \* ). U kunt de basis installatie kopie van het besturings systeem niet wijzigen of u kunt geen directe aanpassingen uitvoeren op deze knoop punten met behulp van de IaaS-Api's of-resources. Aangepaste wijzigingen die niet worden uitgevoerd via de AKS-API, blijven niet behouden tijdens een upgrade, schalen, bijwerken of opnieuw opstarten. Vermijd het uitvoeren van wijzigingen aan de agent knooppunten, tenzij Microsoft Ondersteuning u wijzigingen aanbrengt.

AKS beheert de levens cyclus en bewerkingen van agent knooppunten namens u: het wijzigen van de IaaS-resources die zijn gekoppeld aan de agent knooppunten, wordt **niet ondersteund**. Een voor beeld van een niet-ondersteunde bewerking is het aanpassen van een schaalset voor een virtuele-machine groep van een knoop punt door configuraties hand matig te wijzigen via de portal of API voor de schaalset van de virtuele machine.
 
Voor werkbelasting configuraties of-pakketten raadt AKS aan gebruik te maken van [Kubernetes `daemon sets` ](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/).

Met behulp van Kubernetes-privileged `daemon sets` en init-containers kunt u software van derden op cluster agent knooppunten afstemmen/wijzigen of installeren. Voor beelden van dergelijke aanpassingen zijn het toevoegen van aangepaste beveiligings scan software of het bijwerken van sysctl-instellingen.

Hoewel dit pad wordt aanbevolen als bovenstaande vereisten van toepassing zijn, kan AKS engineering en ondersteuning geen hulp bieden bij het oplossen van problemen die het knoop punt niet beschikbaar maken als gevolg van een aangepaste implementatie `daemon set` .

### <a name="security-issues-and-patching"></a>Beveiligings problemen en patches

Als er een beveiligings probleem wordt gevonden in een of meer van de beheerde onderdelen van AKS, worden alle betrokken clusters door het AKS-team bijgewerkt om het probleem te verhelpen. Het team geeft ook hulp bij het upgraden van gebruikers.

Voor agent knooppunten waarop een beveiligings probleem van toepassing is, zal micro soft u op de hoogte stellen van de gevolgen en de stappen voor het oplossen of beperken van het beveiligings probleem (doorgaans een upgrade van een knooppunt installatie kopie of een upgrade van een cluster patch).

### <a name="node-maintenance-and-access"></a>Beheer van knoop punten en toegang

Hoewel u kunt aanmelden bij en wijzigingen aanbrengen in agent knooppunten, wordt het uitvoeren van deze bewerking afgeraden omdat wijzigingen een cluster niet kunnen ondersteunen.

## <a name="network-ports-access-and-nsgs"></a>Netwerk poorten, Access en Nsg's

U kunt de Nsg's alleen aanpassen op aangepaste subnetten. U kunt Nsg's niet aanpassen op beheerde subnetten of op het niveau van de NIC van de agent knooppunten. AKS heeft uitstaande vereisten voor specifieke eind punten, voor het beheren van de uitvoer en het controleren van de benodigde connectiviteit, Zie [Beperk](limit-egress-traffic.md)uitgaand verkeer.

## <a name="stopped-or-de-allocated-clusters"></a>Gestopte of de toegewezen clusters

Zoals eerder aangegeven, wordt door het hand matig opnieuw toewijzen van alle cluster knooppunten via de IaaS-Api's/CLI/Portal het cluster uit de ondersteuning weer gegeven. De enige ondersteunde manier om de toewijzing van alle knoop punten te stoppen/ongedaan te maken, is door [het AKS-cluster te stoppen](start-stop-cluster.md#stop-an-aks-cluster), waarmee de cluster status tot 12 maanden wordt bewaard.

Clusters die meer dan 12 maanden worden gestopt, behouden de status niet langer. 

Clusters die buiten de AKS-Api's zijn verdeeld, hebben geen garantie voor de status. De besturings plannen voor clusters in deze status worden na 30 dagen gearchiveerd en na 12 maanden verwijderd.

AKS behoudt zich het recht voor om besturings plannen te archiveren die zijn geconfigureerd met ondersteunings richtlijnen voor langere Peri Oden van meer dan 30 dagen. AKS onderhoudt back-ups van etcd-meta gegevens van het cluster en kan het cluster eenvoudig opnieuw toewijzen. Deze hertoewijzing kan worden geïnitieerd door een PUT-bewerking, waarbij het cluster weer wordt ondersteund, zoals een upgrade of schalen naar actieve agent knooppunten.

Als uw abonnement is onderbroken of verwijderd, worden het besturings vlak en de status van uw cluster na 90 dagen verwijderd.

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Niet-ondersteunde alpha-en bèta Kubernetes-functies

AKS ondersteunt alleen stabiele en bèta functies binnen het upstream Kubernetes-project. Tenzij anders gedocumenteerd, ondersteunt AKS geen alpha-functies die beschikbaar zijn in het upstream Kubernetes-project.

## <a name="preview-features-or-feature-flags"></a>Preview-functies of functie vlaggen

Voor functies en functionaliteit waarvoor uitgebreide testen en gebruikers feedback vereist is, brengt micro soft nieuwe preview-functies of-onderdelen achter een functie vlag uit. Overweeg deze functies als voorlopige versie of bèta-functies.

Preview-functies of functies van functie vlaggen zijn niet bedoeld voor productie. Voortdurende wijzigingen in Api's en gedrag, fout oplossingen en andere wijzigingen kunnen leiden tot onstabiele clusters en uitval tijd.

Functies in de open bare preview vallen onder de ' Best effort ' ondersteuning, aangezien deze functies in de preview-versie zijn en niet bedoeld voor productie en alleen worden ondersteund door de AKS technische ondersteunings teams tijdens kantoor uren. Zie voor meer informatie:

* [Veelgestelde vragen over ondersteuning voor Azure](https://azure.microsoft.com/support/faq/)

## <a name="upstream-bugs-and-issues"></a>Fouten en problemen upstream

Gezien de snelheid van de ontwikkeling in het upstream-Kubernetes project, ontstaan er fouten invariably. Sommige van deze fouten kunnen niet worden gerepareerd of gewerkt in het AKS-systeem. In plaats daarvan vereisen fout oplossingen grotere patches voor upstream-projecten (zoals Kubernetes, knoop punt-of agent besturingssystemen en kernel). Voor onderdelen die micro soft eigenaar is (zoals de Azure-Cloud provider), zijn AKS en Azure-personeel in de buurt van de voor delen van het oplossen van problemen in de community.

Wanneer een probleem met technische ondersteuning wordt veroorzaakt door een of meer fouten in de upstream, AKS ondersteuning en technische teams:

* Identificeer en koppel de upstream-fouten met alle ondersteunings Details om u te helpen uitleggen waarom dit probleem van invloed is op uw cluster of werk belasting. Klanten ontvangen koppelingen naar de vereiste opslag plaatsen, zodat ze de problemen kunnen bekijken en zien wanneer een nieuwe release oplossingen biedt.
* Geef mogelijke oplossingen of beperkingen op. Als het probleem kan worden verholpen, wordt een [bekend probleem](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) in de AKS-opslag plaats opgeslagen. Het archief met bekende problemen legt het volgende uit:
  * Het probleem, inclusief koppelingen naar Upstream-fouten.
  * De tijdelijke oplossing en informatie over een upgrade of een andere persistentie van de oplossing.
  * Ruwe tijd lijnen voor de opname van het probleem, op basis van de uitgebracht van de upstream-release.
