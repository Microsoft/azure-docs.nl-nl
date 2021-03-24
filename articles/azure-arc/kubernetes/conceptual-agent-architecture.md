---
title: Architectuur van Azure Arc enabled Kubernetes-agent
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Dit artikel bevat een overzicht van de architectuur van Azure Arc enabled Kubernetes-agents
keywords: Kubernetes, Arc, Azure, containers
ms.openlocfilehash: ec95efdfef871777e7f53617b057529e301739dd
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953065"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Architectuur van Azure Arc enabled Kubernetes-agent

Op zijn eigen wijze kunnen [Kubernetes](https://kubernetes.io/) werk belasting consistent implementeren op hybride en omgevingen met meerdere clouds. Azure Arc enabled Kubernetes werkt echter als een gecentraliseerd, consistent beheergebied dat beleid, governance en beveiliging in heterogene omgevingen beheert. Dit artikel bevat:

* Een architectuur overzicht van het verbinden van een cluster met Azure Arc.
* Het verbindings patroon gevolgd door agents.
* Een beschrijving van de gegevens die worden uitgewisseld tussen de cluster omgeving en Azure.

## <a name="deploy-agents-to-your-cluster"></a>Agents implementeren in uw cluster

De meeste on-premises data centers dwingen strikte netwerk regels af waarmee binnenkomende communicatie op de netwerk grens firewall wordt voor komen. Azure Arc enabled Kubernetes werkt met deze beperkingen door niet de binnenkomende poorten in de firewall te vereisen en alleen selectieve eind punten voor uitgaande communicatie in te scha kelen. Azure Arc enabled Kubernetes-agents starten deze uitgaande communicatie. 

![Architectuuroverzicht](./media/architectural-overview.png)

### <a name="connect-a-cluster-to-azure-arc"></a>Een cluster verbinden met Azure Arc

1. Maak een Kubernetes-cluster op uw door u gewenste infra structuur (VMware vSphere, Amazon Web Services, Google Cloud Platform, enz.). 

    > [!NOTE]
    > Omdat Azure Arc enabled Kubernetes momenteel alleen ondersteuning biedt voor het koppelen van bestaande Kubernetes-clusters aan Azure Arc, moeten klanten de levens cyclus van het Kubernetes-cluster zelf maken en beheren.  

1. Start de Azure-Arc-registratie voor uw cluster met behulp van Azure CLI.
    * Azure CLI gebruikt helm om de helm-grafiek van de agent op het cluster te implementeren.
    * De cluster knooppunten initiëren een uitgaande communicatie met de [micro soft-container Registry](https://github.com/microsoft/containerregistry) en halen de installatie kopieën op die nodig zijn om de volgende agents in de `azure-arc` naam ruimte te maken:

        | Agent | Description |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Azure Arc enabled Kubernetes ondersteunt momenteel alleen door het [systeem toegewezen identiteiten](../../active-directory/managed-identities-azure-resources/overview.md). `clusteridentityoperator` initieert de eerste uitgaande communicatie. Deze eerste communicatie haalt het Managed Service Identity (MSI)-certificaat op dat door andere agents wordt gebruikt voor communicatie met Azure. |
        | `deployment.apps/config-agent` | Controleert het verbonden cluster op configuratie bronnen voor broncode beheer die op het cluster zijn toegepast. Hiermee wordt de nalevings status bijgewerkt. |
        | `deployment.apps/controller-manager` | Een operator van Opera tors die de interacties tussen onderdelen van Azure Arc indeelt. |    
        | `deployment.apps/metrics-agent` | Hiermee worden metrische gegevens van andere Arc-agents verzameld om de optimale prestaties te controleren. |
        | `deployment.apps/cluster-metadata-operator` | Verzamelt de clustermetagegevens, inclusief clusterversie, het aantal knooppunten, en de versie van de Azure Arc-agent. |
        | `deployment.apps/resource-sync-agent` | Synchroniseert de bovenvermelde meta gegevens van het cluster naar Azure. |
        | `deployment.apps/flux-logs-agent` | Hiermee worden logboeken van de stroom-Opera tors verzameld die zijn geïmplementeerd als onderdeel van de configuratie van broncode beheer. |
    
1. Als alle Azure Arc enabled Kubernetes-agent peul de `Running` status heeft, controleert u of uw cluster is verbonden met Azure Arc. U ziet het volgende:
    * Een Kubernetes-resource van Azure arc in [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Deze resource wordt door Azure getraceerd als een projectie van het door de klant beheerde Kubernetes-cluster, niet het daad werkelijke Kubernetes-cluster zelf.
    * De meta gegevens van het cluster (zoals de Kubernetes-versie, de agent versie en het aantal knoop punten) worden weer gegeven op de Azure Arc enabled Kubernetes-resource als meta gegevens.

## <a name="data-exchange-between-cluster-environment-and-azure"></a>Gegevens uitwisseling tussen cluster omgevingen en Azure

| Gegevenstype | Scenario | Communicatie modus |
| --------- | -------- | ------------------ |
| Kubernetes-clusterversie | Meta gegevens van cluster | Agent pushes naar Azure |
| Aantal knoop punten in het cluster | Meta gegevens van cluster | Agent pushes naar Azure |
| Agent versie | Meta gegevens van cluster | Agent pushes naar Azure |
| Kubernetes-distributie type | Meta gegevens van cluster | Azure CLI-pushes naar Azure |
| Infrastructuur type (AWS/GCP/vSphere/...) | Meta gegevens van cluster | Azure CLI-pushes naar Azure |
| vCPU aantal knoop punten in het cluster | Billing | Azure CLI-pushes naar Azure |
| Heartbeat van agent | Status van resources | Agent pushes naar Azure |
| Resource verbruik (geheugen/CPU) door agents | Diagnose en ondersteuning | Agent pushes naar Azure |
| Logboeken van alle agent containers | Diagnose en ondersteuning | Agent pushes naar Azure |
| Upgrade Beschik baarheid agent | Upgrade van agent | Agent-Pulles van Azure |
| Gewenste status van configuratie: git-opslag plaats-URL, stroom operator parameters, persoonlijke sleutel, bekende hosts-inhoud, HTTPS-gebruikers naam, Token of wacht woord | Configuratie | Agent-Pulles van Azure |
| Status van de installatie van de stroom operator | Configuratie | Agent pushes naar Azure |
| Azure Policy toewijzingen die gate keeper-afdwinging in het cluster nodig hebben | Azure Policy | Agent-Pulles van Azure |
| Controle-en nalevings status van beleids afdwingingen in het cluster | Azure Policy | Agent pushes naar Azure |
| Metrische gegevens en logboeken van klant werkbelastingen | Azure Monitor | Agent pusht naar Log Analytics werkruimte resource in de Tenant en het abonnement van de klant |

## <a name="connectivity-status"></a>Connectiviteits status

| Status | Beschrijving |
| ------ | ----------- |
| Verbinding maken | De Azure Arc enabled Kubernetes-resource wordt gemaakt in Azure Resource Manager, maar de service is nog niet in de agent ontvangen. |
| Verbonden | De Kubernetes-service van Azure Arc heeft binnen de afgelopen 15 minuten een heartbeat van de agent ontvangen. |
| Offline | De Kubernetes-resource van Azure Arc is eerder verbonden, maar de service heeft 15 minuten geen agent-heartbeat ontvangen. |
| Verlopen | Het MSI-certificaat bevat een verloop venster van 90 dagen nadat het is uitgegeven. Zodra dit certificaat is verlopen, wordt de resource beschouwd als `Expired` alle functies, zoals configuratie, bewaking en beleid, werken niet meer op dit cluster. Meer informatie over het adresseren van verlopen Azure Arc Kubernetes-bronnen vindt u [in het artikel Veelgestelde vragen](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources). |

## <a name="understand-connectivity-modes"></a>Connectiviteits modi begrijpen

| Connectiviteits modus | Beschrijving |
| ----------------- | ----------- |
| Volledig verbonden | Agents kunnen consistent communiceren met Azure met weinig vertraging bij het door geven van GitOps-configuraties, het afdwingen van Azure Policy-en gate keeper-beleid en het verzamelen van metrische gegevens over werk belasting en Logboeken in Azure Monitor. |
| Semi-verbonden | Het MSI-certificaat dat door de `clusteridentityoperator` is opgehaald, is Maxi maal 90 dagen geldig voordat het certificaat verloopt. Na verloop van tijd werkt de Azure Arc-Kubernetes-resource niet meer. Als u alle functies van Azure-Arc op het cluster opnieuw wilt activeren, verwijdert u de Azure-Kubernetes-resource en-agents en maakt u deze opnieuw. Sluit het cluster gedurende de 90 dagen ten minste één keer per 30 dagen aan. |
| Ontkoppeld | Kubernetes-clusters in omgevingen zonder verbinding kunnen geen toegang krijgen tot Azure. dit wordt momenteel niet ondersteund door Azure Arc enabled Kubernetes. Als deze mogelijkheid van belang is voor u, kunt u een idee doen op [het UserVoice-forum van Azure-Arc](https://feedback.azure.com/forums/925690-azure-arc).

## <a name="next-steps"></a>Volgende stappen

* Door loop onze Snelstartgids om [een Kubernetes-cluster te verbinden met Azure Arc](./quickstart-connect-cluster.md).
* Meer informatie over het maken van verbindingen tussen uw cluster en een Git-opslag plaats als een [configuratie bron met Azure Arc enabled Kubernetes](./conceptual-configurations.md).