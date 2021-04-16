---
title: Azure Red Hat OpenShift-toewijzingsmatrix voor verantwoordelijkheid
description: Meer informatie over het eigendom van verantwoordelijkheden voor de werking van een Azure Red Hat OpenShift cluster
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 4/12/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: aro, openshift, az aro, red hat, cli, RACI, support
ms.openlocfilehash: 4bb00cb533d0065a992831f09ed8280c96efcdee
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537009"
---
# <a name="overview-of-responsibilities-for-azure-red-hat-openshift"></a>Overzicht van verantwoordelijkheden voor Azure Red Hat OpenShift

Dit document bevat een overzicht van de verantwoordelijkheden van Microsoft, Red Hat en klanten voor Azure Red Hat OpenShift clusters. Zie de Azure Red Hat OpenShift servicedefinitie voor meer informatie Azure Red Hat OpenShift over Azure Red Hat OpenShift en de onderdelen ervan.

Terwijl Microsoft en Red Hat de Azure Red Hat OpenShift beheren, deelt de klant de verantwoordelijkheid voor de functionaliteit van het cluster. Hoewel Azure Red Hat OpenShift clusters worden gehost op Azure-resources in Azure-abonnementen van klanten, zijn ze extern toegankelijk. Het onderliggende platform en de gegevensbeveiliging zijn het eigendom van Microsoft en Red Hat.

## <a name="overview"></a>Overzicht
<table>
  <tr>
   <td><strong>Resource</strong>
   </td>
   <td><strong><a href="#incident-and-operations-management">Incident- en operationeel beheer</a></strong>
   </td>
   <td><strong><a href="#change-management">Wijzigingsbeheer</a></strong>
   </td>
   <td><strong><a href="#identity-and-access-management">Identiteits- en toegangsbeheer</a></strong>
   </td>
   <td><strong><a href="#security-and-regulation-compliance">Naleving van beveiliging en regelgeving</a></strong>
   </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Klantgegevens</a>
   </td>
   <td>Klant </td>
   <td>Klant </td>
   <td>Klant </td>
   <td>Klant </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Klanttoepassingen</a>
   </td>
   <td>Klant </td>
   <td>Klant </td>
   <td>Klant </td>
   <td>Klant </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Ontwikkelaarsservices </a>
   </td>
   <td>Klant </td>
   <td>Klant </td>
   <td>Klant </td>
   <td>Klant </td>
  </tr>
  <tr>
   <td>Platformbewaking </td>
   <td>Microsoft en Red Hat </td>
   <td>Microsoft en Red Hat </td>
   <td>Microsoft en Red Hat </td>
   <td>Microsoft en Red Hat </td>
  </tr>
  <tr>
   <td>Logboekregistratie </td>
   <td>Microsoft en Red Hat </td>
   <td>Gedeeld </td>
   <td>Gedeeld </td>
   <td>Gedeeld </td>
  </tr>
  <tr>
   <td>Toepassingsnetwerken </td>
   <td>Gedeeld </td>
   <td>Gedeeld </td>
   <td>Gedeeld </td>
   <td>Microsoft en Red Hat </td>
  </tr>
  <tr>
   <td>Clusternetwerken </td>
   <td>Microsoft en Red Hat </td>
   <td>Gedeeld </td>
   <td>Gedeeld </td>
   <td>Microsoft en Red Hat </td>
  </tr>
  <tr>
   <td>Virtueel netwerk </td>
   <td>Gedeeld </td>
   <td>Gedeeld </td>
   <td>Gedeeld </td>
   <td>Gedeeld </td>
  </tr>
  <tr>
   <td>Besturingsvlakknooppunten </td>
   <td>Microsoft en Red Hat </td>
   <td>Microsoft en Red Hat </td>
   <td>Microsoft en Red Hat </td>
   <td>Microsoft en Red Hat </td>
  </tr>
  <tr>
   <td>Werkknooppunten </td>
   <td>Microsoft en Red Hat </td>
   <td>Microsoft en Red Hat </td>
   <td>Microsoft en Red Hat </td>
   <td>Microsoft en Red Hat </td>
  </tr>
  <tr>
   <td>Clusterversie </td>
   <td>Microsoft en Red Hat </td>
   <td>Gedeeld </td>
   <td>Microsoft en Red Hat </td>
   <td>Microsoft en Red Hat </td>
  </tr>
  <tr>
   <td>Capaciteitsbeheer </td>
   <td>Microsoft en Red Hat </td>
   <td>Gedeeld </td>
   <td>Microsoft en Red Hat </td>
   <td>Microsoft en Red Hat </td>
  </tr>
  <tr>
   <td>Virtuele opslag </td>
   <td>Microsoft en Red Hat </td>
   <td>Microsoft en Red Hat </td>
   <td>Microsoft en Red Hat </td>
   <td>Microsoft en Red Hat </td>
  </tr>
  <tr>
   <td>Fysieke infrastructuur en beveiliging </td>
   <td>Microsoft en Red Hat </td>
   <td>Microsoft en Red Hat </td>
   <td>Microsoft en Red Hat </td>
   <td>Microsoft en Red Hat </td>
  </tr>
</table>


Tabel 1. Verantwoordelijkheden per resource


## <a name="tasks-for-shared-responsibilities-by-area"></a>Taken voor gedeelde verantwoordelijkheden per gebied 

### <a name="incident-and-operations-management"></a>Incident- en operationeel beheer 

De klant en Microsoft en Red Hat delen de verantwoordelijkheid voor de bewaking en het onderhoud van een Azure Red Hat OpenShift cluster. De klant is verantwoordelijk voor [](#customer-data-and-applications) incident- en operationeel beheer van klanttoepassingsgegevens en aangepaste netwerken die de klant mogelijk heeft geconfigureerd.

<table>
  <tr>
   <td><strong>Resource</strong>
   </td>
   <td><strong>Verantwoordelijkheden van Microsoft en Red Hat</strong>
   </td>
   <td><strong>Verantwoordelijkheden van de klant</strong>
   </td>
  </tr>
  <tr>
   <td>Toepassingsnetwerken </td>
   <td>
<ul>

<li>Cloudservices load balancer en native OpenShift-routerservice bewaken en reageren op waarschuwingen.
</li>
</ul>
   </td>
   <td>
<ul>

<li>De status van service load balancer eindpunten bewaken.

<li>Controleer de status van toepassingsroutes en de eindpunten erachter.

<li>Rapportuitval naar Microsoft en Red Hat.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Virtueel netwerk
   </td>
   <td>
<ul>

<li>Controleer cloud load balancers, subnetten en Azure-cloudonderdelen die nodig zijn voor standaardplatformnetwerken en reageer op waarschuwingen.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Beveilig netwerkverkeer dat optioneel is geconfigureerd via VNet-naar-VNet-verbinding, VPN-verbinding of Private Link-verbinding voor potentiële problemen of beveiligingsrisico's.
</li>
</ul>
   </td>
  </tr>
</table>


Tabel 2. Gedeelde verantwoordelijkheden voor incident- en operationeel beheer


### <a name="change-management"></a>Wijzigingsbeheer

Microsoft en Red Hat zijn verantwoordelijk voor het inschakelen van wijzigingen in de clusterinfrastructuur en -services die de klant gaat beheren, evenals het onderhouden van versies die beschikbaar zijn voor de hoofdknooppunten, infrastructuurservices en werkknooppunten. De klant is verantwoordelijk voor het initiëren van infrastructuurwijzigingen en het installeren en onderhouden van optionele services en netwerkconfiguraties op het cluster, evenals alle wijzigingen in klantgegevens en klanttoepassingen.


<table>
  <tr>
   <td><strong>Resource</strong>
   </td>
   <td><strong>Verantwoordelijkheden van Microsoft en Red Hat</strong>
   </td>
   <td><strong>Verantwoordelijkheden van de klant</strong>
   </td>
  </tr>
  <tr>
   <td>Logboekregistratie </td>
   <td>
<ul>

<li>Platformcontrolelogboeken centraal aggregeren en bewaken.

<li>Bied de klant documentatie voor het inschakelen van toepassingslogboekregistratie met behulp van Log Analytics via Azure Monitor voor containers.

<li>Geef auditlogboeken op wanneer de klant hier om vraagt.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Installeer de optionele standaardoperator voor toepassingslogregistratie op het cluster.

<li>Optionele oplossingen voor app-logboekregistratie installeren, configureren en onderhouden, zoals sidecar-containers voor logboekregistratie of toepassingen voor logboekregistratie van derden.

<li>Stem de grootte en frequentie af van toepassingslogboeken die worden geproduceerd door klanttoepassingen als deze van invloed zijn op de stabiliteit van het cluster.

<li>Aanvraag platform auditlogboeken via een ondersteuningsaanvraag voor het onderzoeken van specifieke incidenten.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Toepassingsnetwerken
   </td>
   <td>
<ul>

<li>Load balancers voor openbare cloud instellen

<li>Stel de native OpenShift-routerservice in. Bieden de mogelijkheid om de router in te stellen als privé en maximaal één extra router-shard toe te voegen.

<li>Installeer, configureer en onderhoud OpenShift SDN-onderdelen voor standaard intern podverkeer.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Configureer niet-standaard podnetwerkmachtigingen voor project- en podnetwerken, pod-ingress en pod-egress met behulp van NetworkPolicy-objecten.

<li>Aanvullende service load balancers aanvragen en configureren voor specifieke services.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Clusternetwerken
   </td>
   <td>
<ul>

<li>Stel clusterbeheeronderdelen in, zoals openbare of privé-service-eindpunten en de benodigde integratie met onderdelen van virtuele netwerken.

<li>Interne netwerkonderdelen instellen die vereist zijn voor interne clustercommunicatie tussen werkknooppunten en hoofdknooppunten.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Geef indien nodig optionele niet-standaard IP-adresbereiken op voor de CIDR van de machine, service-CIDR en pod-CIDR via OpenShift Cluster Manager wanneer het cluster wordt ingericht.

<li>Vraag het API-service-eindpunt openbaar of privé te maken bij het maken van het cluster of nadat het cluster is gemaakt via Azure CLI.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Virtueel netwerk
   </td>
   <td>
<ul>

<li>Onderdelen van virtuele netwerken instellen en configureren die vereist zijn voor het inrichten van het cluster, waaronder virtuele privécloud, subnetten, load balancers, internetgateways, NAT-gateways, enzovoort.

<li>Bied de klant de mogelijkheid om VPN-connectiviteit met on-premises resources, VNet-naar-VNet-connectiviteit en Private Link beheren zoals vereist via OpenShift Cluster Manager.

<li>Klanten in staat stellen load balancers voor openbare cloud te maken en te implementeren voor gebruik met service load balancers.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Optionele onderdelen van openbare cloudnetwerken instellen en onderhouden, zoals VNet-naar-VNet-verbinding, VPN-verbinding of Private Link verbinding.

<li>Aanvragen en configureren van extra service load balancers voor specifieke services.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Clusterversie
   </td>
   <td>
<ul>

<li>Planning en status van upgrades voor secundaire versies en onderhoudsversies communiceren

<li>Changelogs en releasenotities publiceren voor kleine upgrades en onderhoudsupgrades
</li>
</ul>
   </td>
   <td>
<ul>

<li>Upgrade van cluster initiëren

<li>Test klanttoepassingen op secundaire versies en onderhoudsversies om compatibiliteit te garanderen
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Capaciteitsbeheer
   </td>
   <td>
<ul>

<li>Het gebruik van het besturingsvlak bewaken (hoofdknooppunten)

<li>Beheervlakknooppunten schalen en/of het beheervlak het schalen en/of het schalen om de kwaliteit van de service te behouden

<li>Het gebruik van klantbronnen bewaken, inclusief netwerk-, opslag- en rekencapaciteit. Wanneer functies voor automatisch schalen niet zijn ingeschakeld, wordt de klant gewaarschuwd voor wijzigingen die zijn vereist voor clusterresources (bijvoorbeeld nieuwe rekenknooppunten om te schalen, extra opslag, enzovoort)
</li>
</ul>
   </td>
   <td>
<ul>

<li>Gebruik de opgegeven OpenShift Cluster Manager-besturingselementen om indien nodig extra werkknooppunten toe te voegen of te verwijderen.

<li>Reageren op meldingen van Microsoft en Red Hat met betrekking tot clusterresourcevereisten.
</li>
</ul>
   </td>
  </tr>
</table>


Tabel 3. Gedeelde verantwoordelijkheden voor wijzigingsbeheer


### <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer 

Identiteits- en toegangsbeheer omvat alle verantwoordelijkheden om ervoor te zorgen dat alleen de juiste personen toegang hebben tot cluster-, toepassings- en infrastructuurbronnen. Dit omvat taken zoals het bieden van mechanismen voor toegangsbeheer, verificatie, autorisatie en het beheren van toegang tot resources.


<table>
  <tr>
   <td><strong>Resource</strong>
   </td>
   <td><strong>Verantwoordelijkheden van Microsoft en Red Hat</strong>
   </td>
   <td><strong>Verantwoordelijkheden van de klant</strong>
   </td>
  </tr>
  <tr>
   <td>Logboekregistratie </td>
   <td>
<ul>

<li>Voldoe aan een op industriestandaarden gebaseerd gelaagd intern toegangsproces voor platformcontrolelogboeken.

<li>Biedt native OpenShift RBAC-mogelijkheden.
</li>
</ul>
   </td>
   <td>
<ul>

<li>OpenShift RBAC configureren om de toegang tot projecten en de toepassingslogboeken van een project te bepalen.

<li>Voor oplossingen van derden of aangepaste toepassingen voor logboekregistratie is de klant verantwoordelijk voor toegangsbeheer.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Toepassingsnetwerken
   </td>
   <td>
<ul>

<li>Biedt native OpenShift RBAC-mogelijkheden.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Configureer OpenShift RBAC om de toegang tot de routeconfiguratie zo nodig te bepalen.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Clusternetwerken
   </td>
   <td>
<ul>

<li>Biedt native OpenShift RBAC-mogelijkheden.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Red Hat-organisatielidmaatschap van Red Hat-accounts beheren.

<li>Beheer organisatiebeheerders voor Red Hat om toegang te verlenen tot OpenShift Cluster Manager.

<li>Configureer OpenShift RBAC om de toegang tot de routeconfiguratie zo nodig te bepalen.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Virtueel netwerk
   </td>
   <td>
<ul>

<li>Besturingselementen voor klanttoegang bieden via OpenShift Cluster Manager.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Optionele gebruikerstoegang tot openbare cloudonderdelen beheren via OpenShift Cluster Manager.
</li>
</ul>
   </td>
  </tr>
</table>


Tabel 4. Gedeelde verantwoordelijkheden voor identiteits- en toegangsbeheer


### <a name="security-and-regulation-compliance"></a>Naleving van beveiliging en regelgeving 

Beveiliging en naleving omvatten alle verantwoordelijkheden en controles die zorgen voor naleving van relevante wetten, beleidsregels en voorschriften.


<table>
  <tr>
   <td><strong>Resource</strong>
   </td>
   <td><strong>Verantwoordelijkheden van Microsoft en Red Hat</strong>
   </td>
   <td><strong>Verantwoordelijkheden van de klant</strong>
   </td>
  </tr>
  <tr>
   <td>Logboekregistratie </td>
   <td>
<ul>

<li>Clustercontrolelogboeken verzenden naar een Microsoft- en Red Hat SIEM om te analyseren op beveiligingsgebeurtenissen. Behoudt auditlogboeken voor een bepaalde periode ter ondersteuning van forensische analyse.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Analyseer toepassingslogboeken op beveiligingsgebeurtenissen. Verzend toepassingslogboeken naar een extern eindpunt via sidecar-containers voor logboekregistratie of toepassingen voor logboekregistratie van derden als er een langere retentie is vereist dan wordt aangeboden door de standaardstack voor logboekregistratie.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Virtueel netwerk
   </td>
   <td>
<ul>

<li>Controleer de onderdelen van virtuele netwerken op mogelijke problemen en beveiligingsrisico's.

<li>Gebruik aanvullende openbare Microsoft- en Red Hat Azure-hulpprogramma's voor extra bewaking en beveiliging.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Beveilig optioneel geconfigureerde onderdelen van virtuele netwerken op mogelijke problemen en beveiligingsrisico's.

<li>Configureer indien nodig de benodigde firewallregels of datacenterbeveiligingen.
</li>
</ul>
   </td>
  </tr>
</table>


Tabel 5. Gedeelde verantwoordelijkheden voor naleving van beveiliging en regelgeving


## <a name="customer-responsibilities-when-using-azure-red-hat-openshift"></a>Verantwoordelijkheden van de klant bij het gebruik van Azure Red Hat OpenShift 


### <a name="customer-data-and-applications"></a>Klantgegevens en -toepassingen

De klant is verantwoordelijk voor de toepassingen, workloads en gegevens die in de Azure Red Hat OpenShift. Microsoft en Red Hat bieden echter verschillende hulpprogramma's waarmee de klant gegevens en toepassingen op het platform kan beheren.


<table>
  <tr>
   <td><strong>Resource</strong>
   </td>
   <td><strong>Hoe Microsoft en Red Hat u helpen</strong>
   </td>
   <td><strong>Verantwoordelijkheden van de klant</strong>
   </td>
  </tr>
  <tr>
   <td>Klantgegevens </td>
   <td>
<ul>

<li>Behoud standaarden op platformniveau voor gegevensversleuteling zoals gedefinieerd door beveiligings- en nalevingsstandaarden van de branche. 

<li>OpenShift-onderdelen bieden voor het beheren van toepassingsgegevens, zoals geheimen.

<li>Schakel integratie met externe gegevensservices (zoals Azure SQL) in voor het opslaan en beheren van gegevens buiten het cluster en/of Microsoft en Red Hat Azure.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Behoudt de verantwoordelijkheid voor alle klantgegevens die zijn opgeslagen op het platform en hoe klanttoepassingen deze gegevens gebruiken en beschikbaar maken.

<li>Etcd-versleuteling
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Klanttoepassingen
   </td>
   <td>
<ul>

<li>Clusters inrichten met OpenShift-onderdelen die zijn geïnstalleerd, zodat klanten toegang hebben tot de OpenShift- en Kubernetes-API's voor het implementeren en beheren van toepassingen in containers.

<li>Toegang verlenen tot OpenShift-API's die een klant kan gebruiken om operators in te stellen om community-, externe, Microsoft- en Red Hat-services en Red Hat-services toe te voegen aan het cluster. 

<li>Bied opslagklassen en in plug-ins ter ondersteuning van permanente volumes voor gebruik met klanttoepassingen.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Houd de verantwoordelijkheid voor toepassingen, gegevens en de volledige levenscyclus van klanten en derden.

<li>Als een klant Red Hat, de community, derden, hun eigen of andere services aan het cluster toevoegt met operators of externe afbeeldingen, is de klant verantwoordelijk voor deze services en voor het werken met de juiste provider (inclusief Red Hat) om eventuele problemen op te lossen.

<li>Gebruik de geleverde hulpprogramma's en functies om <a href="https://docs.openshift.com/aro/4/architecture/understanding-development.html#application-types">te configureren en te implementeren;</a> <a href="https://docs.openshift.com/aro/4/applications/deployments/deployment-strategies.html">up-to-date houden;</a> <a href="https://docs.openshift.com/aro/4/applications/working-with-quotas.html">resourceaanvragen en -limieten instellen;</a> <a href="https://docs.openshift.com/aro/4/getting_started/scaling-your-cluster.html">het cluster zo groot maken dat het voldoende resources heeft om apps uit te voeren;</a> <a href="https://docs.openshift.com/aro/4/administering_a_cluster/">machtigingen instellen;</a> integreren met andere services; <a href="https://docs.openshift.com/aro/4/openshift_images/images-understand.html">afbeeldingsstreams of sjablonen beheren die de klant implementeert;</a> <a href="https://docs.openshift.com/aro/4/cloud_infrastructure_access">extern dienen;</a> gegevens opslaan, er een back-up van maken en gegevens herstellen; en op andere wijze hun zeer beschikbare en flexibele workloads beheren.

<li>Behoudt de verantwoordelijkheid voor het bewaken van de toepassingen die worden uitgevoerd op Azure Red Hat OpenShift; inclusief het installeren en beheren van software voor het verzamelen van metrische gegevens en het maken van waarschuwingen.
</li>
</ul>
   </td>
  </tr>
</table>


Tabel 7. Verantwoordelijkheden van de klant voor klantgegevens, klanttoepassingen en services
