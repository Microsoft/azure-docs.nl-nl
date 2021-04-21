---
title: Gegevenslocaties voor Windows Virtual Desktop - Azure
description: Een kort overzicht van de locaties Windows Virtual Desktop gegevens en metagegevens worden opgeslagen.
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: eeba3cb579c6ef9158379403a3206f99a2cfb060
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830624"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Gegevens- en metagegevenslocaties voor Windows Virtual Desktop

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/data-locations-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

Windows Virtual Desktop is momenteel beschikbaar voor alle geografische locaties. Beheerders kunnen de locatie kiezen voor het opslaan van gebruikersgegevens wanneer ze de virtuele machines van de hostgroep en de bijbehorende services, zoals bestandsservers, maken. Meer informatie over Azure-geografische gebieden kunt u vinden op de [kaart met Azure-datacenters.](https://azuredatacentermap.azurewebsites.net/)

>[!NOTE]
>Microsoft controleert of beperkt de regio's waar u of uw gebruikers toegang hebben tot uw gebruikers- en app-specifieke gegevens niet.

>[!IMPORTANT]
>Windows Virtual Desktop slaat algemene metagegevensinformatie, zoals werkruimtenamen, hostgroepnamen, app-groepsnamen en user principal names, op in een datacenter. Wanneer een klant een serviceobject maakt, moet deze een locatie voor het serviceobject invoeren. De locatie die ze invoeren, bepaalt waar de metagegevens voor het object worden opgeslagen. De klant kiest een Azure-regio en de metagegevens worden opgeslagen in de gerelateerde geografie. Zie Azure-geografieën voor een lijst met alle [Azure-regio's en](https://azure.microsoft.com/global-infrastructure/geographies/)gerelateerde geografische gebieden.

We ondersteunen momenteel het opslaan van metagegevens in de volgende geografische gebieden:

- Verenigde Staten (VS) (algemeen beschikbaar)
- Europa (EU) (openbare preview) 

>[!NOTE]
> Wanneer u een regio selecteert waarin u Windows Virtual Desktop serviceobjecten wilt maken, ziet u regio's onder de geografische regio's van de VS en de EU. Bekijk onze globale infrastructuurkaart van Azure om te zien welke regio het beste werkt voor [uw implementatie.](https://azure.microsoft.com/global-infrastructure/geographies/#geographies)

De opgeslagen metagegevens worden 'at rest' versleuteld en geografisch redundante spiegels worden binnen de geografie onderhouden. Alle klantgegevens, zoals app-instellingen en gebruikersgegevens, bevinden zich op de locatie die de klant kiest en worden niet beheerd door de service. Naarmate de service groeit, komen er meer geografische gebieden beschikbaar.

Servicemetagegevens worden voor noodhersteldoeleinden gerepliceerd binnen de Azure-geografie.
