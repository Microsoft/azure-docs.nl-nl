---
title: Reageren op Azure Defender voor Key Vault-waarschuwingen
description: Meer informatie over de stappen die nodig zijn voor het reageren op waarschuwingen van Azure Defender voor Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 67c556e44f07240b1ad1bcde61f40042da46def8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96122202"
---
# <a name="respond-to-azure-defender-for-key-vault-alerts"></a>Reageren op Azure Defender voor Key Vault-waarschuwingen
Wanneer u een waarschuwing ontvangt van Azure Defender voor Key Vault, raden we u aan de waarschuwing te onderzoeken en erop te reageren, zoals hieronder wordt beschreven. Azure Defender voor Key Vault beveiligt toepassingen en referenties, dus zelfs als u vertrouwd bent met de toepassing of gebruiker die de waarschuwing heeft geactiveerd, is het belangrijk dat u de situatie rondom elke waarschuwing controleert.  

Elke waarschuwing van Azure Defender voor Key Vault bevat de volgende elementen:

- Object-id
- User Principal name of IP-adres van de verdachte resource

> [!TIP]
> Op basis van het *type* toegang dat is opgetreden, zijn sommige velden mogelijk niet beschikbaar. Als uw sleutelkluis bijvoorbeeld door een toepassing is geopend, ziet u geen bijbehorende user principal name van de gebruiker. Als het verkeer afkomstig is van buiten Azure, ziet u geen object-id.

## <a name="step-1-contact"></a>Stap 1. Contactpersoon

1. Controleer of het verkeer afkomstig is van binnen uw Azure-Tenant. Als de sleutel kluis firewall is ingeschakeld, is het waarschijnlijk dat u toegang hebt gekregen tot de gebruiker of toepassing die deze waarschuwing heeft geactiveerd.
1. Als u de bron van het verkeer niet kunt controleren, gaat u verder met [stap 2. Onmiddellijke beperking](#step-2-immediate-mitigation).
1. Als u de bron van het verkeer in uw Tenant kunt identificeren, neemt u contact op met de gebruiker of de eigenaar van de toepassing. 

> [!CAUTION]
> Azure Defender voor Key Vault is ontworpen om te helpen verdachte activiteiten te identificeren die worden veroorzaakt door gestolen referenties. Negeer de waarschuwing **niet** alleen omdat u de gebruiker of de toepassing herkent. Neem contact op met de eigenaar van de toepassing of de gebruiker en controleer of de activiteit legitiem is. U kunt zo nodig een onderdrukkings regel maken om ruis te elimineren. Meer informatie over [waarschuwingen van Azure Defender onderdrukken](alerts-suppression-rules.md).


## <a name="step-2-immediate-mitigation"></a>Stap 2. Onmiddellijke beperking 
Als u de gebruiker of toepassing niet herkent, of als u denkt dat de toegang niet mag worden goedgekeurd:

- Als het verkeer afkomstig is van een niet-herkend IP-adres:
    1. Schakel de Azure Key Vault firewall in, zoals beschreven in [Azure Key Vault firewalls en virtuele netwerken configureren](../key-vault/general/network-security.md).
    1. Configureer de firewall met vertrouwde bronnen en virtuele netwerken.

- Als de bron van de waarschuwing een niet-geautoriseerde toepassing of verdachte gebruiker is:
    1. Open de instellingen van het toegangs beleid van de sleutel kluis.
    1. Verwijder de bijbehorende beveiligings-principal of beperk de bewerkingen die de beveiligingsprincipal kan uitvoeren.  

- Als de bron van de waarschuwing een Azure Active Directory rol heeft in uw Tenant:
    1. Neem contact op met de beheerder.
    1. Bepaal of Azure Active Directory machtigingen moeten worden gereduceerd of ingetrokken.

## <a name="step-3-identify-impact"></a>Stap 3. Impact identificeren 
Wanneer de impact is verminderd, onderzoekt u de geheimen in uw sleutel kluis die worden beïnvloed:
1. Open de pagina beveiliging op uw Azure Key Vault en Bekijk de geactiveerde waarschuwing.
1. Selecteer de specifieke waarschuwing die is geactiveerd.
    Bekijk de lijst met de geheimen die zijn geopend en de tijds tempel.
1. Als u Key kluis Diagnostische logboeken hebt ingeschakeld, raadpleegt u de vorige bewerkingen voor het bijbehorende IP-adres, de User Principal of de object-ID.  

## <a name="step-4-take-action"></a>Stap 4. Actie ondernemen 
Wanneer u de lijst met geheimen, sleutels en certificaten hebt opgesteld die door de verdachte gebruiker of toepassing zijn geopend, moet u deze objecten direct draaien.

1. De betreffende geheimen moeten worden uitgeschakeld of verwijderd uit uw sleutel kluis.
1. Als de referenties voor een specifieke toepassing zijn gebruikt:
    1. Neem contact op met de beheerder van de toepassing en vraag hen hun omgeving te controleren op elk gebruik van de aangetaste referenties, omdat dit is aangetast.
    1. Als de aangetaste referenties zijn gebruikt, moet de eigenaar van de toepassing de gegevens identificeren die zijn geopend en de impact verminderen.


## <a name="next-steps"></a>Volgende stappen

Op deze pagina wordt het proces van het reageren op een waarschuwing van Azure Defender voor Key Vault uitgelegd. Raadpleeg de volgende pagina's voor verwante informatie:

- [Inleiding tot Azure Defender voor Key Vault](defender-for-key-vault-introduction.md)
- [Waarschuwingen van Azure Defender onderdrukken](alerts-suppression-rules.md)
- [Security Center-gegevens continu exporteren](continuous-export.md)