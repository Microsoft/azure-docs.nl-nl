---
title: Verkeer beheren met Traffic Manager
description: Zoek aanbevolen procedures voor het configureren van Azure Traffic Manager wanneer u deze integreert met Azure App Service.
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.topic: article
ms.date: 02/25/2016
ms.custom: seodec18
ms.openlocfilehash: 040f84288c66f4506919e775b9ea41324b617cfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80437901"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Azure App Service verkeer beheren met Azure Traffic Manager
> [!NOTE]
> In dit artikel vindt u een overzicht van de informatie over de Microsoft Azure Traffic Manager die betrekking heeft op Azure App Service. Meer informatie over Azure Traffic Manager zelf vindt u op de koppelingen aan het einde van dit artikel.
> 
> 

## <a name="introduction"></a>Introductie
U kunt Azure Traffic Manager gebruiken om te bepalen hoe aanvragen van web-clients worden gedistribueerd naar apps in Azure App Service. Wanneer App Service-eindpunten worden toegevoegd aan een Azure Traffic Manager-profiel, houdt Azure Traffic Manager de status van uw App Service-apps bij (actief, gestopt of verwijderd) zodat de service kan bepalen welke van deze eindpunten verkeer moeten ontvangen.

## <a name="routing-methods"></a>Routeringsmethoden
In azure Traffic Manager worden vier verschillende routerings methoden gebruikt. Deze methoden worden beschreven in de volgende lijst die van toepassing zijn op Azure App Service.

* **[Prioriteit](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method):** gebruik een primaire app voor al het verkeer en geef back-ups op als de primaire of de back-upapps niet beschikbaar zijn.
* **[Gewogen](../traffic-manager/traffic-manager-routing-methods.md#weighted):** verkeer distribueren over een set apps, hetzij gelijkmatig als op basis van gewichten, dat u definieert.
* **[Prestaties](../traffic-manager/traffic-manager-routing-methods.md#performance):** wanneer u apps op verschillende geografische locaties hebt, moet u de ' dichtstbijzijnde ' app gebruiken in termen van de laagste netwerk latentie.
* **[Geografisch](../traffic-manager/traffic-manager-routing-methods.md#geographic):** directe gebruikers naar specifieke apps op basis van welke geografische locatie hun DNS-query afkomstig is. 

Zie [Traffic Manager routerings methoden](../traffic-manager/traffic-manager-routing-methods.md)voor meer informatie.

## <a name="app-service-and-traffic-manager-profiles"></a>App Service-en Traffic Manager-profielen
Als u het beheer van App Service app-verkeer wilt configureren, maakt u een profiel in azure Traffic Manager dat gebruikmaakt van een van de vier taakverdelings methoden die eerder zijn beschreven, en voegt u vervolgens de eind punten (in dit geval App Service) toe waarvoor u verkeer naar het profiel wilt beheren. Uw app-status (actief, gestopt of verwijderd) wordt regel matig aan het profiel door gegeven, zodat Azure Traffic Manager verkeer dienovereenkomstig kan omleiden.

Houd bij het gebruik van Azure Traffic Manager met Azure de volgende punten in acht:

* Voor app-implementaties binnen dezelfde regio biedt App Service al een failover-en Round Robin-functionaliteit zonder rekening te houden met de app-modus.
* Voor implementaties in dezelfde regio die gebruikmaken van App Service in combi natie met een andere Azure-Cloud service, kunt u beide typen eind punten combi neren om hybride scenario's in te scha kelen.
* U kunt slechts één App Service eind punt per regio in een profiel opgeven. Wanneer u een app als een eind punt voor één regio selecteert, zijn de resterende apps in die regio niet meer beschikbaar voor selectie voor dat profiel.
* De App Service-eind punten die u opgeeft in een Azure Traffic Manager-profiel, worden weer gegeven onder de sectie **domein namen** op de pagina configureren voor de app in het profiel, maar kan daar niet worden geconfigureerd.
* Nadat u een app aan een profiel hebt toegevoegd, wordt in de **site-URL** op het dash board van de portal pagina van de app de aangepaste domein-URL van de app weer gegeven als u er een hebt ingesteld. Anders wordt de URL van het Traffic Manager profiel weer gegeven (bijvoorbeeld `contoso.trafficmanager.net` ). Zowel de directe domein naam van de app als de URL van de Traffic Manager worden weer gegeven op de pagina configureren van de app in de sectie **domein namen** .
* Uw aangepaste domein namen werken zoals verwacht, maar u kunt ze niet alleen toevoegen aan uw apps als u uw DNS-map zo configureert dat deze verwijst naar de Traffic Manager URL. Zie [een aangepaste domein naam configureren in azure app service met Traffic manager-integratie](configure-domain-traffic-manager.md)voor meer informatie over het instellen van een aangepast domein voor een app service-app.
* U kunt alleen apps in de modus Standard of Premium toevoegen aan een Azure Traffic Manager-profiel.
* Als u een app toevoegt aan een Traffic Manager profiel, wordt de app opnieuw gestart.

## <a name="next-steps"></a>Volgende stappen
Zie [overzicht van Traffic Manager](../traffic-manager/traffic-manager-overview.md)voor een conceptueel en technisch overzicht van Azure Traffic Manager.


