---
title: 'Zelfstudie: Gewogen round robin-verkeersroutering configureren met Azure Traffic Manager'
description: In deze zelfstudie wordt uitgelegd hoe u verkeer in balans kunt brengen in Traffic Manager met behulp van een round robin-methode
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: abcfce43b90c7371d5b38aa5b7a6d478e9d6a0dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92207818"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Zelfstudie: De gewogen verkeersrouteringsmethode configureren in Traffic Manager

Een veelgebruikte methode voor het routeren van verkeer is om een set identieke eindpunten te bieden, waaronder cloudservices en websites, en verkeer gelijkmatig naar beide te verzenden. In de volgende stappen wordt beschreven hoe u dit type verkeersrouteringsmethode kunt configureren.

> [!NOTE]
> De Azure-web-app biedt al round robin-taakverdeling voor websites binnen een Azure-regio (die mogelijk meerdere datacentra omvat). Met Traffic Manager kunt u verkeer distribueren over websites in verschillende datacentra.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Een Traffic Manager-profiel met gewogen routering maken.
> - Het Traffic Manager-profiel gebruiken.
> - Traffic Manager-profiel verwijderen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/)

## <a name="configure-the-weighted-traffic-routing-method"></a>De gewogen verkeersrouteringsmethode configureren

1. Meld u vanuit een browser aan bij de [Azure-portal](https://portal.azure.com).

1. Zoek in de zoekbalk van de portal de naam van het **Traffic Manager-profiel** dat u in het vorige gedeelte hebt gemaakt en selecteer het Traffic Manager-profiel in de weergegeven resultaten.

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/search-traffic-manager-weighted-profile.png" alt-text="Traffic Manager-profiel zoeken":::

1. Selecteer **Configuratie** en selecteer of voer de volgende instellingen in:

    | Instelling         | Waarde                                              |
    | ---             | ---                                                |
    | Routeringsmethode            | Selecteer **Gewogen**. |    
    | DNS time to live (TTL) | Deze waarde bepaalt hoe vaak de lokale cachenaamserver van de client een query op het Traffic Manager-systeem uitvoert naar bijgewerkte DNS-vermeldingen. Dit is de tijd die het duurt voor wijzigingen die zich voordoen met Traffic Manager, zoals het wijzigen van de routeringsmethode van het verkeer of wijzigingen in de beschikbaarheid van toegevoegde eindpunten, in het globale systeem van DNS-servers worden doorgevoerd. |
    | Protocol    | Selecteer een protocol voor eindpuntbewaking. *Opties: HTTP, HTTPS en TCP* |
    | Poort | Geef het poortnummer op. |
    | Pad | Als u eindpunten wilt controleren, moet u een pad en bestandsnaam opgeven. Een slash (/) is een geldige vermelding voor het relatieve pad en impliceert dat het bestand zich in de hoofdmap bevindt (standaard). |
    | Aangepaste headerinstellingen | Configureer de aangepaste headers met de indeling host:contoso.com,nieuweheader:nieuwewaarde. Het maximum aantal ondersteunde paren is 8. Van toepassing op het HTTP- en HTTPS-protocol. Van toepassing op alle eindpunten in het profiel |
    | Verwachte statuscodebereiken (standaard: 200) | Configureer statuscodebereiken in de notatie 200-299,301-301. Het maximum aantal ondersteunde bereiken is 8. Van toepassing op het HTTP- en HTTPS-protocol. Van toepassing op alle eindpunten in het profiel |
    | Testinterval | Het tijdsinterval tussen statustests van eindpunten configureren. U kunt 10 of 30 seconden kiezen. |
    | Aantal fouten tolereren | Configureer het aantal mislukte statustestfouten dat is toegestaan voordat een eindpuntfout wordt getriggerd. Voer een getal tussen 0 en 9 in. | 
    | Time-out voor testen | Configureer de tijd die nodig is voor een time-out van een eindpuntstatustest. Deze waarde moet ten minste 5 zijn en kleiner zijn dan de waarde voor het testinterval. |

1. Selecteer **Opslaan** om de configuratie te voltooien.

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-configuration.png" alt-text="Gewogen configuratie in Traffic Manager"::: 

1. Selecteer **Eindpunt** en configureer het gewicht van elk eindpunt. Het gewicht kan 1-1000 zijn. Hoe hoger het gewicht, hoe hoger de prioriteit.  

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-configure-endpoints-weighted.png" alt-text="Configuratie van gewogen eindpunten in Traffic Manager"::: 

## <a name="use-the-traffic-manager-profile"></a>Het Traffic Manager-profiel gebruiken

Het **Traffic Manager-profiel** geeft de DNS-naam weer van het Traffic Manager-profiel dat u zojuist hebt gemaakt. Deze naam kan door clients worden gebruikt (bijvoorbeeld door ernaar te navigeren met een webbrowser) om naar het juiste eindpunt te gaan, zoals wordt bepaald door het routeringstype. In dit geval worden alle aanvragen naar elk eindpunt gerouteerd op basis van round robin.

:::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-overview.png" alt-text="Overzicht van gewogen routering in Traffic Manager"::: 

## <a name="clean-up-resources"></a>Resources opschonen

Als u het Traffic Manager-profiel niet meer nodig hebt, zoekt u het profiel op en selecteert u **Profiel verwijderen**.

:::image type="content" source="./media/traffic-manager-weighted-routing-method/delete-traffic-manager-weighted-profile.png" alt-text="Gewogen profiel in Traffic Manager verwijderen":::

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de gewogen routeringsmethode:

> [!div class="nextstepaction"]
> [Gewogen verkeersrouteringsmethode](traffic-manager-routing-methods.md#weighted)