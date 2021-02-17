---
title: Architectuur van Azure Arc enabled Kubernetes-agent
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Dit artikel bevat een overzicht van de architectuur van Azure Arc enabled Kubernetes-agents
keywords: Kubernetes, Arc, Azure, containers
ms.openlocfilehash: e1f51f066598b59501b30704cb1475dd5332160e
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561468"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Architectuur van Azure Arc enabled Kubernetes-agent

[Kubernetes](https://kubernetes.io/) kan worden gebruikt voor het implementeren van container werkbelastingen op hybride en omgevingen met meerdere clouds op een consistente manier. Azure Arc enabled Kubernetes kan worden gebruikt als een gecentraliseerd besturings vlak om beleid, governance en beveiliging consistent in deze heterogene-omgevingen te beheren. Dit artikel bevat:

* Een architectuur overzicht van het verbinden van een cluster met Azure Arc.
* Het verbindings patroon gevolgd door agents.
* Een beschrijving van de gegevens die worden uitgewisseld tussen de cluster omgeving en Azure.

## <a name="deploy-agents-to-your-cluster"></a>Agents implementeren in uw cluster

De meeste on-premises data centers dwingen strikte netwerk regels af waarmee binnenkomende communicatie op de firewall die wordt gebruikt op de netwerk grens wordt voor komen. Azure Arc enabled Kubernetes werkt met deze beperkingen door alleen selectieve eind punten voor uitgaande communicatie in te scha kelen en geen binnenkomende poorten op de firewall te vereisen. De uitgaande verbindingen worden geïnitieerd door Azure Arc ingeschakelde Kubernetes-agents.

![Architectuuroverzicht](./media/architectural-overview.png)

Met de volgende stappen een cluster verbinden met Azure Arc:

1. Maak een Kubernetes-cluster op uw door u gewenste infra structuur (VMware vSphere, Amazon Web Services, Google Cloud Platform, enz.). 

    > [!NOTE]
    > Klanten zijn verplicht om de levens cyclus van het Kubernetes-cluster zelf te maken en te beheren omdat Azure Arc enabled Kubernetes momenteel alleen ondersteuning biedt voor het koppelen van bestaande Kubernetes-clusters aan Azure Arc.  

1. Start de Azure-Arc-registratie voor uw cluster met behulp van Azure CLI.
    * Azure CLI gebruikt helm om de helm-grafiek van de agent op het cluster te implementeren.
    * De cluster knooppunten initiëren een uitgaande communicatie met de [micro soft-container Registry](https://github.com/microsoft/containerregistry) en halen de installatie kopieën op die nodig zijn om de volgende agents in de `azure-arc` naam ruimte te maken:

        | Agent | Description |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Azure Arc enabled Kubernetes ondersteunt momenteel alleen door het [systeem toegewezen identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). clusteridentityoperator maakt de eerste uitgaande communicatie nodig om het door andere agents gebruikte MSI-certificaat (Managed Service Identity) op te halen voor communicatie met Azure. |
        | `deployment.apps/config-agent` | Hiermee wordt het verbonden cluster gewatcheerd voor configuratie bronnen voor broncode beheer die zijn toegepast op het cluster en de nalevings status van updates |
        | `deployment.apps/controller-manager` | Een operator van Opera tors die de interacties tussen onderdelen van Azure Arc indeelt |    
        | `deployment.apps/metrics-agent` | Verzamelt metrische gegevens van andere Arc-agents om ervoor te zorgen dat deze agents optimale prestaties vertonen |
        | `deployment.apps/cluster-metadata-operator` | Verzamelt de meta gegevens van het cluster, de Cluster versie, het aantal knoop punten en de versie van de Azure Arc-agent |
        | `deployment.apps/resource-sync-agent` | Synchroniseert de hierboven vermelde meta gegevens van het cluster naar Azure |
        | `deployment.apps/flux-logs-agent` | Hiermee worden logboeken van de stroom-Opera tors verzameld die zijn geïmplementeerd als onderdeel van de configuratie van broncode beheer |
    
1. Als alle Azure Arc enabled Kubernetes-agent in de `Running` staat van de status, controleert u of uw cluster is verbonden met Azure Arc. U ziet het volgende:
    * Een Kubernetes-resource van Azure arc in [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Deze resource wordt bijgehouden in azure als een projectie van het door de klant beheerde Kubernetes-cluster, niet het daad werkelijke Kubernetes-cluster zelf.
    * Meta gegevens van het cluster, zoals de Kubernetes-versie, de versie van de agent en het aantal knoop punten, worden weer gegeven op de Azure-Arc Kubernetes-resource als meta gegevens.

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
| Gewenste status van configuratie-Git opslag plaats URL, stroom operator parameters, persoonlijke sleutel, bekende hosts-inhoud, HTTPS-gebruikers naam, token/wacht woord | Configuratie | Agent-Pulles van Azure |
| Status van de installatie van de stroom operator | Configuratie | Agent pushes naar Azure |
| Azure Policy toewijzingen die gate keeper-afdwinging in het cluster nodig hebben | Azure Policy | Agent-Pulles van Azure |
| Controle-en nalevings status van beleids afdwingingen in het cluster | Azure Policy | Agent pushes naar Azure |
| Metrische gegevens en logboeken van klant werkbelastingen | Azure Monitor | Agent pusht naar Log Analytics werkruimte resource in de Tenant en het abonnement van de klant |

## <a name="connectivity-status"></a>Connectiviteits status

| Status | Beschrijving |
| ------ | ----------- |
| Verbinding maken | De Kubernetes-resource van Azure Arc is gemaakt in Azure Resource Manager, maar de service heeft nog geen agent-heartbeat ontvangen. |
| Verbonden | De Kubernetes-service van Azure Arc heeft binnen de afgelopen 15 minuten een heartbeat van de agent ontvangen. |
| Offline | De Kubernetes-resource van Azure Arc is eerder verbonden, maar de service heeft 15 minuten geen agent-heartbeat ontvangen. |
| Verlopen | Het MSI-certificaat (Managed Service Identity) heeft een verloop venster van 90 dagen nadat het is uitgegeven. Zodra dit certificaat is verlopen, wordt de resource beschouwd als `Expired` alle functies, zoals configuratie, bewaking en beleid, werken niet meer op dit cluster. Meer informatie over het adresseren van verlopen Azure Arc-Kubernetes-resources vindt u [hier](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources) |

## <a name="understand-connectivity-modes"></a>Connectiviteits modi begrijpen

| Connectiviteits modus | Description |
| ----------------- | ----------- |
| Volledig verbonden | Agents kunnen altijd contact met Azure maken. De ervaring is in dit geval ideaal omdat er weinig vertraging is opgetreden bij het door geven van configuraties (voor GitOps), het afdwingen van beleid (in Azure Policy en gate keeper) en het verzamelen van metrische gegevens en logboeken van werk belastingen (in Azure Monitor) |
| Semi-verbonden | Het MSI-certificaat dat is opgehaald door de `clusteridentityoperator` is geldig gedurende 90 dagen voordat het certificaat verloopt. Zodra het certificaat is verlopen, werkt de Azure Arc enabled Kubernetes-resource niet meer. Verwijder de Azure-Kubernetes-resource en-agents en maak deze opnieuw om alle Arc-functies te laten werken aan het cluster. Tijdens de 90 dagen wordt aanbevolen om het cluster ten minste eenmaal per 30 dagen aan te sluiten. |
| Ontkoppeld | Kubernetes-clusters in niet-verbonden omgevingen zonder toegang tot Azure worden momenteel niet ondersteund door Azure Arc enabled Kubernetes. Als deze mogelijkheid van belang is voor u, kunt u een idee doen op [het UserVoice-forum van Azure-Arc](https://feedback.azure.com/forums/925690-azure-arc).

## <a name="next-steps"></a>Volgende stappen

* [Een cluster verbinden met Azure Arc](./connect-cluster.md)
* [Conceptueel overzicht van configuraties](./conceptual-configurations.md)