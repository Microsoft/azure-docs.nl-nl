---
title: Een Azure-Arc-gegevens controller maken in de Azure Portal
description: Een Azure-Arc-gegevens controller maken in de Azure Portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 04/07/2021
ms.topic: how-to
ms.openlocfilehash: 12d0997e677bcca423f32951e99a6202855104ad
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030863"
---
# <a name="create-an-azure-arc-data-controller-in-the-azure-portal"></a>Een Azure-Arc-gegevens controller maken in de Azure Portal

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="introduction"></a>Introductie

U kunt de Azure Portal gebruiken om een Azure-Arc-gegevens controller te maken.

Veel van de mogelijkheden voor het maken van Azure Arc worden gestart in de Azure Portal zelfs als de resource die moet worden gemaakt of beheerd, zich buiten de Azure-infra structuur bevindt. Het patroon van de gebruikers ervaring in deze gevallen, met name wanneer er geen rechtstreekse verbinding tussen Azure en uw omgeving is, is het gebruik van de Azure Portal voor het genereren van een script dat vervolgens kan worden gedownload en uitgevoerd in uw omgeving om een beveiligde verbinding te maken met Azure. Servers met Azure-Arc-functionaliteit volgen dit patroon bijvoorbeeld om [servers met Arc-functionaliteit te maken](../servers/onboard-portal.md).

Wanneer u de modus voor indirecte verbinding van gegevens services van Azure Arc gebruikt, kunt u de Azure Portal gebruiken om een notitie blok voor u te genereren dat vervolgens kan worden gedownload en uitgevoerd in Azure Data Studio voor uw Kubernetes-cluster. 

Wanneer u de modus directe verbinding gebruikt, kunt u de gegevens controller rechtstreeks vanuit de Azure Portal inrichten. U kunt meer lezen over [connectiviteits modi](connectivity.md).

## <a name="use-the-azure-portal-to-create-an-azure-arc-data-controller"></a>De Azure Portal gebruiken om een Azure-Arc-gegevens controller te maken

Volg de onderstaande stappen om een Azure-Arc-gegevens controller te maken met behulp van de Azure Portal en Azure Data Studio.

1. Meld u eerst aan bij de [Azure Portal Marketplace](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/azure%20arc%20data%20controller).  De zoek resultaten voor Marketplace worden gefilterd om u de ' Azure Arc data controller ' weer te geven.
1. Als de eerste stap de zoek criteria niet heeft opgegeven. Voer in de zoek resultaten in en klik op Azure Arc data controller.
1. Selecteer de tegel Azure data controller in de Marketplace.
1. Klik op de knop **Create**.
1. Selecteer de modus voor indirecte connectiviteit. Meer informatie over [connectiviteits modi en vereisten](./connectivity.md). 
1. Bekijk de vereisten voor het maken van een Azure Arc-gegevens controller en installeer eventuele ontbrekende vereiste software, zoals Azure Data Studio en kubectl.
1. Klik op de **volgende knop: Details van gegevens controller** .
1. Kies een abonnement, resource groep en Azure-locatie, net zoals u zou doen voor andere resources die u in de Azure Portal zou maken. In dit geval wordt de Azure-locatie die u selecteert, waar de meta gegevens van de bron worden opgeslagen.  De resource zelf wordt gemaakt op de infra structuur die u kiest. Het hoeft niet in de Azure-infra structuur te zijn.
1. Voer een naam in voor de gegevens controller.

1. Selecteer een configuratie profiel voor de implementatie.
1. Klik op de knop **openen in azure Studio** .
1. In het volgende scherm ziet u een samen vatting van uw selecties en een notitie blok dat wordt gegenereerd.  U kunt op de knop **inrichtings notitieblok downloaden** klikken om het notitie blok te downloaden.

   > [!IMPORTANT]
   > Op Azure Red Hat open Shift of Red Hat open Shift container platform moet u de beveiligings context beperking Toep assen voordat u de gegevens controller maakt. Volg de instructies in [een beveiligings context beperking Toep assen voor Azure Arc enabled Data Services op open Shift](how-to-apply-security-context-constraint.md).

1. Open het notitie blok in Azure Data Studio en klik bovenaan op de knop **alles uitvoeren** .
1. Volg de aanwijzingen en instructies in het notitie blok om het maken van de gegevens controller te volt ooien.

## <a name="monitoring-the-creation-status"></a>De aanmaak status bewaken

Het duurt enkele minuten voordat de controller is gemaakt. U kunt de voortgang in een ander Terminal venster bewaken met de volgende opdrachten:

> [!NOTE]
>  In de onderstaande voor beelden wordt ervan uitgegaan dat u een gegevens controller en Kubernetes-naam ruimte hebt gemaakt met de naam ' Arc '.  Als u de naam van een andere naam ruimte/gegevens controller hebt gebruikt, kunt u ' Arc ' vervangen door uw naam.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

U kunt ook de status van de aanmaak van een bepaalde pod controleren door een opdracht als hieronder uit te voeren.  Dit is met name nuttig voor het oplossen van problemen.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Problemen bij het maken oplossen

Raadpleeg de [hand leiding](troubleshoot-guide.md)voor het oplossen van problemen als u problemen ondervindt bij het maken.