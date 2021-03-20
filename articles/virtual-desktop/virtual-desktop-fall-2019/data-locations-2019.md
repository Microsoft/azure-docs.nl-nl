---
title: Gegevens locaties voor virtueel bureau blad van Windows (klassiek)-Azure
description: Een beknopt overzicht van de locaties waar Windows-gegevens en meta gegevens worden opgeslagen.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 869defde657c9cb4c8bea6bbacebb9458e5a2b96
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88008811"
---
# <a name="data-locations-for-windows-virtual-desktop-classic"></a>Gegevens locaties voor virtueel bureau blad van Windows (klassiek)

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../data-locations.md) als u Azure Resource Manager Windows Virtual Desktop-objecten probeert te beheren.

Virtueel bureau blad van Windows is momenteel beschikbaar voor alle geografische locaties. In eerste instantie kunnen meta gegevens van de service alleen worden opgeslagen in de Geografie van Verenigde Staten (Verenigde Staten). Beheerders kunnen de locatie kiezen voor het opslaan van gebruikers gegevens wanneer ze de virtuele machines van de hostgroep en gekoppelde services, zoals bestands servers, maken. Meer informatie over Azure-geografi op de [Azure Data Center-kaart](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Micro soft heeft geen invloed op de regio's waar u of uw gebruikers toegang hebben tot uw gebruiker en app-specifieke gegevens.

>[!IMPORTANT]
>In Windows Virtual Desktop worden globale meta gegevens opgeslagen, zoals Tenant namen, namen van hostnamen, namen van app-groepen en UPN-namen in een Data Center in de Verenigde Staten. De opgeslagen meta gegevens worden versleuteld op rest en geo-redundante mirrors worden in de Verenigde Staten bewaard. Alle klant gegevens, zoals app-instellingen en gebruikers gegevens, bevinden zich op de locatie die de klant kiest en wordt niet beheerd door de service.

Meta gegevens van de service worden gerepliceerd in de Verenigde Staten voor nood herstel doeleinden.