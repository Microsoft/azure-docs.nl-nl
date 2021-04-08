---
title: Overzicht van de MSIX-app voor Windows Virtual Desktop-Azure
description: Wat is een MSIX-app-koppeling? In dit artikel vindt u meer informatie.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c6bf296b5173a662b1e9dd7b025648e3f16d23c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88556278"
---
# <a name="what-is-msix-app-attach"></a>Wat is een MSIX-app-koppeling?

MSIX is een nieuwe verpakkings indeling die veel functies biedt die zijn gericht op het verbeteren van de ervaring van het pakket voor alle Windows-apps. Zie het [overzicht van MSIX](/windows/msix/overview)voor meer informatie over MSIX.

MSIX app attach is een manier om MSIX-toepassingen te leveren aan zowel fysieke als virtuele machines. MSIX app attach wijkt echter af van gewone MSIX, omdat deze speciaal is gemaakt voor virtuele Windows-Bureau bladen. In dit artikel wordt beschreven wat MSIX app attach is en wat u voor u kunt doen.

## <a name="application-delivery-options-in-windows-virtual-desktop"></a>Opties voor het leveren van toepassingen in het virtuele bureau blad van Windows

U kunt op een van de volgende manieren apps in Windows virtueel bureau blad leveren:

- Apps in een hoofd installatie kopie plaatsen
- Hulpprogram ma's als SCCM of intune voor Centraal beheer gebruiken
- Dynamic app Provisioning (AppV, VMWare AppVolumes of Citrix AppLayering)
- Aangepaste hulpprogram ma's of scripts maken met micro soft en een hulp programma van derden

## <a name="what-does-msix-app-attach-do"></a>Wat doet de MSIX-app voor koppelen?

In een implementatie van een virtueel bureau blad van Windows kan MSIX app attach het volgende doen:

- Maak schei ding tussen gebruikers gegevens, het besturings systeem en apps met behulp van [MSIX-containers](/windows/msix/msix-container).
- Verwijder de nood zaak om opnieuw te verpakken wanneer toepassingen dynamisch worden geleverd.
- Verminder de tijd die de gebruiker nodig heeft om zich aan te melden.
- Verminder de vereisten en kosten voor de infra structuur.

MSIX app attach moet worden toegepast buiten VDI of SBC.

## <a name="traditional-app-layering-compared-to-msix-app-attach"></a>Traditionele app-laag, vergeleken met MSIX app attach

De volgende tabel vergelijkt de belangrijkste functie van de MSIX-app voor koppelen en app-lagen.

| Functie | Traditionele app-laag  | MSIX-app-koppeling  |
|-----|-----------------------------|--------------------|
| Indeling               | Voor verschillende technologieën voor app-lagen zijn verschillende eigen indelingen vereist. | Werkt met de systeem eigen MSIX-verpakkings indeling.        |
| Overhead opnieuw bepakken | Voor eigen indelingen moeten sequentiëren en opnieuw verpakken per update worden vereist.         | Apps die als MSIX zijn gepubliceerd, hoeven niet opnieuw te worden verpakt. Als het MSIX-pakket echter niet beschikbaar is, is de overhead van de verpakking nog steeds van toepassing. |
| Ecosysteem            | N.v.t. (leveranciers leveren bijvoorbeeld geen app-V)  | MSIX is de gang bare technologie van micro soft die belang rijke ISV-partners en interne apps zoals Office doorneemt. U kunt MSIX gebruiken op zowel virtuele Bureau bladen als fysieke Windows-computers. |
| Infrastructuur       | Extra infra structuur vereist (servers, clients, enzovoort) | Alleen opslag   |
| Beheer       | Onderhouds-en update vereist   | Vereenvoudigt app-updates |
| Gebruikerservaring      | Beïnvloedt de aanmeldings tijd van de gebruiker. Er bestaat een grens tussen de status van het besturings systeem, de status van de app en de gebruikers gegevens.  | Geleverde apps kunnen niet worden onderscheiden van lokaal geïnstalleerde toepassingen. |

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over het toevoegen van MSIX-apps, raadpleegt u onze [woorden lijst](app-attach-glossary.md) en [Veelgestelde vragen](app-attach-faq.md). U kunt ook aan de slag met het [instellen van een app-koppeling](app-attach.md).
