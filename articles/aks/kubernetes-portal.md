---
title: Toegang tot Kubernetes-resources via de Azure Portal
description: Meer informatie over hoe u met Kubernetes-resources een Azure Kubernetes service-cluster (AKS) kunt beheren vanuit de Azure Portal.
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: ce5dc74dc3625b2b1fed447c4e6480308267d32a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578678"
---
# <a name="access-kubernetes-resources-from-the-azure-portal"></a>Toegang tot Kubernetes-resources via de Azure Portal

De Azure Portal bevat een Kubernetes resource weergave voor eenvoudige toegang tot de Kubernetes-resources in uw Azure Kubernetes service-cluster (AKS). Het weer geven van Kubernetes-resources in de Azure Portal reduceert de context wisseling tussen de Azure Portal en het `kubectl` opdracht regel programma, waarmee u de ervaring voor het weer geven en bewerken van uw Kubernetes-resources kunt stroom lijnen. De resource Viewer bevat momenteel meerdere resource typen, zoals implementaties, peulen en replica sets.

De resource weergave Kubernetes van de Azure Portal vervangt de [AKS dashboard-invoeg toepassing][kubernetes-dashboard], die is afgeschaft.

## <a name="prerequisites"></a>Vereisten

Als u Kubernetes-resources wilt weer geven in de Azure Portal, hebt u een AKS-cluster nodig. Elk cluster wordt ondersteund, maar als de integratie van Azure Active Directory (Azure AD) wordt gebruikt, moet uw cluster gebruikmaken van [Azure AD-integratie met AKS-beheer][aks-managed-aad]. Als uw cluster verouderde Azure AD gebruikt, kunt u uw cluster bijwerken in de portal of met de [Azure cli][cli-aad-upgrade]. U kunt ook [de Azure Portal gebruiken][portal-cluster] om een nieuw AKS-cluster te maken.

## <a name="view-kubernetes-resources"></a>Kubernetes-resources weer geven

Als u de Kubernetes-resources wilt bekijken, gaat u naar uw AKS-cluster in de Azure Portal. Het navigatie deel venster aan de linkerkant wordt gebruikt om toegang te krijgen tot uw resources. De resources zijn onder andere:

- In **naam ruimten** worden de naam ruimten van het cluster weer gegeven. Het filter aan de bovenkant van de lijst met naam ruimten biedt een snelle manier om uw naam ruimte bronnen te filteren en weer te geven.
- **Werk belastingen** toont informatie over implementaties, peulen, replica sets, stateful sets, daemon-sets, taken en cron-taken die zijn geïmplementeerd in uw cluster. In de onderstaande scherm afbeelding ziet u het standaard systeem-peul in een voor beeld van een AKS-cluster.
- **Services en ingresses** toont alle service-en ingangs bronnen van uw cluster.
- In **opslag** worden uw Azure Storage-klassen en permanente volume gegevens weer gegeven.
- In de **configuratie** worden de configuratie kaarten en geheimen van uw cluster weer gegeven.

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="Kubernetes pod-informatie die wordt weer gegeven in de Azure Portal." lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>Een app implementeren

In dit voor beeld gebruiken we onze voor beeld-AKS-cluster om de Azure stem-toepassing te implementeren vanuit de [AKS Quick][portal-quickstart]start.

1. Selecteer **toevoegen** vanuit een van de resource weergaven (naam ruimte, werk belastingen, services en Ingresses, opslag of configuratie).
1. Plak het YAML voor de Azure stem-toepassing vanuit de [AKS Quick][portal-quickstart]start.
1. Selecteer **toevoegen** aan de onderkant van de yaml-editor om de toepassing te implementeren. 

Zodra het YAML-bestand is toegevoegd, worden in de resource Viewer zowel Kubernetes-services weer gegeven die zijn gemaakt: de interne service (Azure-stem) als de externe service (Azure-stemmen) om toegang te krijgen tot de Azure stem-toepassing. De externe service bevat een gekoppeld extern IP-adres, zodat u de toepassing eenvoudig kunt bekijken in uw browser.

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Informatie over Azure stem toepassingen die worden weer gegeven in de Azure Portal." lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>Implementatie inzichten bewaken

AKS-clusters met [Azure monitor voor][enable-monitor] ingeschakelde containers kunnen snel implementatie en andere inzichten weer geven. In de weer gave Kubernetes resources kunnen gebruikers de live status van afzonderlijke implementaties zien, met inbegrip van CPU-en geheugen gebruik, evenals overgang naar Azure monitor voor uitgebreidere informatie over specifieke knoop punten en containers. Hier volgt een voor beeld van implementatie Insights van een voor beeld van een AKS-cluster:

:::image type="content" source="media/kubernetes-portal/deployment-insights.png" alt-text="Implementatie Insights wordt weer gegeven in de Azure Portal." lightbox="media/kubernetes-portal/deployment-insights.png":::

## <a name="edit-yaml"></a>YAML bewerken

De resource weergave Kubernetes bevat ook een YAML-editor. Met een ingebouwde YAML-editor kunt u services en implementaties bijwerken of maken vanuit de portal en wijzigingen direct Toep assen.

:::image type="content" source="media/kubernetes-portal/service-editor.png" alt-text="YAML-editor voor een Kubernetes-service die wordt weer gegeven in de Azure Portal.":::

Nadat u de YAML hebt bewerkt, worden de wijzigingen toegepast door **revisie + opslaan** te selecteren, de wijzigingen te bevestigen en vervolgens opnieuw op te slaan.

>[!WARNING]
> Het uitvoeren van directe productie wijzigingen via de gebruikers interface of CLI wordt niet aanbevolen. u moet gebruikmaken van [doorlopende integratie (CI) en doorlopende implementatie (cd) best practices](kubernetes-action.md). De Azure Portal Kubernetes-beheer mogelijkheden en de YAML-editor zijn gebouwd voor het leren en testen van nieuwe implementaties in een ontwikkelings-en test instelling.

## <a name="troubleshooting"></a>Problemen oplossen

Deze sectie heeft betrekking op veelvoorkomende problemen en stappen voor probleem oplossing.

### <a name="unauthorized-access"></a>Onbevoegde toegang

Om toegang te krijgen tot de Kubernetes-resources, moet u toegang hebben tot het AKS-cluster, de Kubernetes-API en de Kubernetes-objecten. Zorg ervoor dat u een cluster beheerder of een gebruiker met de juiste machtigingen hebt voor toegang tot het AKS-cluster. Zie [toegangs-en identiteits opties voor AKS][concepts-identity]voor meer informatie over de beveiliging van het cluster.

>[!NOTE]
> De resource weergave kubernetes in azure portal wordt alleen ondersteund door [beheerde Aad-clusters](managed-aad.md) of clusters die niet zijn ingeschakeld voor Aad. Als u gebruikmaakt van een cluster waarvoor Managed AAD is ingeschakeld, moet uw AAD-gebruiker of-identiteit de respectieve rollen/rollen bindingen hebben om toegang te krijgen tot de kubernetes-API, naast de machtiging voor het ophalen van de [gebruiker `kubeconfig` ](control-kubeconfig-access.md).

### <a name="enable-resource-view"></a>Resource weergave inschakelen

Voor bestaande clusters moet u mogelijk de resource weergave Kubernetes inschakelen. Als u de resource weergave wilt inschakelen, volgt u de aanwijzingen in de portal voor uw cluster.

> [!TIP]
> De AKS-functie voor door de [**API server geautoriseerde IP-adresbereiken**](api-server-authorized-ip-ranges.md) kan worden toegevoegd om de API-server toegang alleen te beperken tot het open bare eind punt van de firewall. Een andere optie voor dergelijke clusters is het bijwerken `--api-server-authorized-ip-ranges` om toegang te bieden voor een lokale client computer of een IP-adres bereik (van waaruit de portal wordt gebladerd). U hebt het open bare IPv4-adres van de computer nodig om deze toegang toe te staan. U kunt dit adres vinden met de onderstaande opdracht of door te zoeken in ' wat is mijn IP-adres ' in een Internet browser.
```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u toegang krijgt tot Kubernetes-resources voor uw AKS-cluster. Zie [implementaties en yaml-manifesten][deployments] voor een dieper inzicht in cluster bronnen en de YAML-bestanden die worden gebruikt met de Kubernetes resource viewer.

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/containers/container-insights-enable-existing-clusters.md
[portal-cluster]: kubernetes-walkthrough-portal.md