---
title: bestand opnemen
description: bestand opnemen
services: signalr
author: wesmc7777
ms.service: signalr
ms.topic: include
ms.date: 04/17/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: e5cfc9beb5473917a76f822862ce3d61675d6493
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93406669"
---
1. Als u een Azure SignalR Service-resource wilt maken, meldt u zich eerst aan bij de [Azure-portal](https://portal.azure.com). Selecteer in de linkerbovenhoek van de pagina **+ Een resource maken**. Voer **Signalr-service** in het tekstvak **Zoeken in Marketplace** in.

2. Selecteer **Signalr service** in de resultaten en selecteer **Maken**.

3. Op de nieuwe pagina met **SignalR**-instellingen voegt u de volgende instellingen toe voor uw nieuwe SignalR-resource:

    | Naam | Aanbevolen waarde | Beschrijving |
    | ---- | ----------------- | ----------- |
    | Resourcenaam | *testsignalr* | Voer een unieke resourcenaam in voor de SignalR-resource. De naam moet een tekenreeks zijn van 1 tot 63 tekens die alleen cijfers, letters en het koppelteken (`-`) mag bevatten. De naam mag niet beginnen of eindigen met het koppelteken en opeenvolgende koppeltekens zijn niet toegestaan.|
    | Abonnement | Kies uw abonnement |  Selecteer het Azure-abonnement dat u wilt gebruiken om SignalR te testen. Als uw account maar één abonnement heeft, wordt dit automatisch geselecteerd en wordt de vervolgkeuzelijst **Abonnement** niet weergegeven.|
    | Resourcegroep | Maak een resourcegroep met de naam *SignalRTestResources*| Selecteer of maak een resourcegroep voor uw SignalR-resource. Deze groep is handig voor het ordenen van meerdere resources die u mogelijk op een bepaald moment wilt verwijderen door resourcegroep te verwijderen. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../articles/azure-resource-manager/management/overview.md) voor meer informatie. |
    | Locatie | *VS - oost* | Gebruik **Locatie** om de geografische locatie op te geven waar SignalR-resource wordt gehost. Voor de beste prestaties wordt u aangeraden om de resource te maken in dezelfde regio als de andere onderdelen van uw toepassing. |
    | Prijscategorie | *Gratis* | Momenteel zijn de opties **Gratis** en **Standaard** beschikbaar. |
    | Vastmaken aan dashboard | ✔ | Selecteer dit vakje als u wilt dat de resource wordt vastgemaakt aan uw dashboard, zodat u deze eenvoudiger kunt vinden. |

4. Selecteer **Controleren + maken**. Wacht tot de validatie is voltooid. 

5. Selecteer **Maken**. De implementatie kan enkele minuten duren.

6. Wanneer de implementatie is voltooid, selecteert u onder **INSTELLINGEN** de optie **Sleutels**. Kopieer uw verbindingsreeks voor de primaire sleutel. U gebruikt deze reeks later om uw app te configureren voor het gebruik van de Azure SignalR Service-resource.

    Deze verbindingsreeks heeft de volgende vorm:
    
    `Endpoint=<service_endpoint>;AccessKey=<access_key>;`
