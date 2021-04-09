---
title: Gegevens locaties voor virtueel bureau blad van Windows-Azure
description: Een kort overzicht van de locaties waar de gegevens en meta gegevens van Windows virtueel bureau blad zijn opgeslagen.
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 12ec71a86a5df5954c14097e6a0ec5c8a5138fc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652425"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Locaties van gegevens en meta gegevens voor Windows virtueel bureau blad

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/data-locations-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

Virtueel bureau blad van Windows is momenteel beschikbaar voor alle geografische locaties. Beheerders kunnen de locatie kiezen voor het opslaan van gebruikers gegevens wanneer ze de virtuele machines van de hostgroep en gekoppelde services, zoals bestands servers, maken. Meer informatie over Azure-geografi op de [Azure Data Center-kaart](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Micro soft heeft geen invloed op de regio's waar u of uw gebruikers toegang hebben tot uw gebruiker en app-specifieke gegevens.

>[!IMPORTANT]
>In Windows Virtual Desktop worden globale meta gegevens opgeslagen, zoals Tenant namen, namen van hostnamen, namen van app-groepen en principal-namen van gebruikers in een Data Center. Wanneer een klant een service object maakt, moet deze een locatie voor het Service object invoeren. De locatie die ze invoeren, bepaalt waar de meta gegevens voor het object worden opgeslagen. De klant kiest een Azure-regio en de meta gegevens worden opgeslagen in de verwante geografie. Zie [Azure-geografi](https://azure.microsoft.com/global-infrastructure/geographies/)voor een lijst met alle Azure-regio's en gerelateerde geografische gebieden.

Momenteel wordt het opslaan van meta gegevens in de volgende geografi ondersteund:

- Verenigde Staten (VS) (algemeen beschikbaar)
- Europa (EU) (open bare preview) 

>[!NOTE]
> Wanneer u een regio selecteert voor het maken van Windows Virtual Desktop service-objecten in, ziet u regio's onder zowel de Verenigde Staten als de EU. Om ervoor te zorgen dat u weet welke regio het meest geschikt is voor uw implementatie, kunt u een kijkje nemen in [onze wereld wijde Azure-infrastructuur kaart](https://azure.microsoft.com/global-infrastructure/geographies/#geographies).

De opgeslagen meta gegevens worden versleuteld op rest en geo-redundante mirrors worden binnen de geografie bewaard. Alle klant gegevens, zoals app-instellingen en gebruikers gegevens, bevinden zich op de locatie die de klant kiest en wordt niet beheerd door de service. Meer geographs worden beschikbaar naarmate de service groeit.

Meta gegevens van de service worden gerepliceerd binnen de Azure-Geografie voor nood herstel doeleinden.