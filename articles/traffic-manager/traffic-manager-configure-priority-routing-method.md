---
title: 'Zelfstudie: Geprioriteerde verkeersroutering configureren met Azure Traffic Manager'
description: In deze zelfstudie wordt uitgelegd hoe u de methode voor de routering van prioriteitsverkeer configureert in Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: duau
ms.openlocfilehash: 1835377f4690097c8390957bf7d897242ba7aace
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92208053"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Zelfstudie: De geprioriteerde verkeersrouteringsmethode configureren in Traffic Manager

In deze zelfstudie wordt beschreven hoe u Azure Traffic Manager kunt gebruiken om gebruikersverkeer re routeren tussen eindpunten met behulp van de methode voor prioriteitsroutering. Met deze routeringsmethode kunt u de volgorde toewijzen van elk eindpunt in de profielconfiguratie van Traffic Manager. Verkeer van gebruikers wordt doorgestuurd naar de eindpunten in de volgorde waarin ze worden weer gegeven. Deze routeringsmethode is nuttig wanneer u wilt configureren voor service-failover. Het primaire eindpunt krijgt het prioriteitsnummer 1 en verwerkt alle inkomende aanvragen. Eindpunten met een lagere prioriteit fungeren als back-up.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Een Traffic Manager-profiel met prioriteitsroutering maken.
> - Eindpunten toevoegen.
> - De prioriteit van eindpunten configureren.
> - Het Traffic Manager-profiel gebruiken.
> - Traffic Manager-profiel verwijderen.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="to-configure-the-priority-traffic-routing-method"></a>Ga als volgt te werk om geprioriteerde verkeersrouteringsmethode te configureren
1. Meld u vanuit een browser aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **+ Een resource maken** aan de linkerkant. Zoek naar **Traffic Manager-profiel** en selecteer **Maken**.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-priority-profile.png" alt-text="Een prioriteitsprofiel maken in Traffic Manager":::

1. Definieer op de pagina *Traffic Manager-profiel maken* de volgende instellingen:

    | Instelling         | Waarde                                              |
    | ---             | ---                                                |
    | Naam            | Geef een naam op voor het profiel. Deze naam moet uniek zijn binnen de trafficmanager.net-zone. Voor toegang tot uw Traffic Manager-profiel gebruikt u de DNS-naam `<profilename>.trafficmanager.net`. |    
    | Routeringsmethode  | Selecteer **Prioriteit**. |
    | Abonnement    | Selecteer uw abonnement. |
    | Resourcegroep   | Gebruik een bestaande resourcegroep of maak een nieuwe resourcegroep om dit profiel voor te maken. Als u ervoor kiest om een nieuwe resourcegroep te maken, gebruikt u de vervolgkeuzelijst *Locatie van resourcegroep* om de locatie van de resourcegroep op te geven. Deze instelling verwijst naar de locatie van de resourcegroep en heeft geen invloed op het Traffic Manager-profiel dat wereldwijd wordt geïmplementeerd. |

1. Selecteer **Maken** om uw Traffic Manager-profiel te implementeren.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-profile-priority.png" alt-text="Een prioriteitsprofiel maken in Traffic Manager":::

## <a name="add-endpoints"></a>Eindpunten toevoegen

1. Selecteer het Traffic Manager-profiel in de lijst.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-list.png" alt-text="Lijst met Traffic Manager-profielen":::

1. Selecteer **Eindpunten** onder *Instellingen* en selecteer **+ Toevoegen** om een nieuw eindpunt toe te voegen.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-add-endpoints.png" alt-text="Eindpunten toevoegen in Traffic Manager":::

1. Typ of selecteer de volgende instellingen: 

    | Instelling                | Waarde                                              |
    | ---                    | ---                                                |
    | Type                   | Selecteer het eindpunttype. |    
    | Naam                   | Geef een naam op om dit eindpunt aan te duiden. |
    | Doelbrontype   | Selecteer het resourcetype voor het doel. |
    | Doelbron        | Selecteer de resource in de lijst. |
    | Prioriteit               | Geef een prioriteitsnummer op voor dit eindpunt. 1 is de hoogste prioriteit. |


1. Selecteer **Toevoegen** om het eindpunt toe te voegen. Herhaal stap 2 en 3 om meer eindpunten toe te voegen. Vergeet niet het juiste prioriteitsnummer in te stellen.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/add-endpoint.png" alt-text="Eindpunt met prioriteit 1 toevoegen":::

1. Controleer op de pagina **Eindpunten** de volgorde van prioriteit voor uw eindpunten. Als u **Prioriteit** als methode voor verkeersroutering hebt geselecteerd, is de volgorde van de geselecteerde eindpunten van belang. Controleer de prioriteitsvolgorde van eindpunten.  Het primaire eindpunt staat bovenaan. Controleer de volgorde waarin het wordt weergegeven. Alle aanvragen worden doorgestuurd naar het eerste eindpunt. Als Traffic Manager detecteert dat dit eindpunt een slechte status heeft, wordt automatisch een failover uitgevoerd naar het volgende eindpunt. 

    :::image type="content" source="./media/traffic-manager-priority-routing-method/endpoints-list.png" alt-text="Lijst met prioriteitseindpunten":::

1. Als u de prioriteitsvolgorde van de eindpunten wilt wijzigen, selecteert u een eindpunt, wijzigt de prioriteitswaarde en selecteert u **Opslaan** om de eindpuntinstellingen op te slaan.

## <a name="use-the-traffic-manager-profile"></a>Het Traffic Manager-profiel gebruiken

1.  Zoek in de zoekbalk van de portal de naam van het **Traffic Manager-profiel** dat u in het vorige gedeelte hebt gemaakt en selecteer het Traffic Manager-profiel in de weergegeven resultaten.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/search-traffic-manager-profile.png" alt-text="Traffic Manager-profiel zoeken":::

1.  De overzichtspagina **Traffic Manager-profiel** geeft de DNS-naam weer van het Traffic Manager-profiel dat u zojuist hebt gemaakt. Dit kan door clients worden gebruikt (bijvoorbeeld door ernaar te navigeren met een webbrowser) om naar het juiste eindpunt te gaan, zoals wordt bepaald door het routeringstype. In dit geval worden alle aanvragen doorgestuurd naar het eerste eindpunt. Als Traffic Manager detecteert dat dit eindpunt een slechte status heeft, wordt automatisch een failover uitgevoerd naar het volgende eindpunt.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-dns-name.png" alt-text="DNS-naam van Traffic Manager":::

1. Als uw Traffic Manager-profiel werkt, kunt u de DNS-record op uw gezaghebbende DNS-server zo bewerken dat de domeinnaam van uw bedrijf verwijst naar de Traffic Manager-domeinnaam.

## <a name="clean-up-resources"></a>Resources opschonen

Als u het Traffic Manager-profiel niet meer nodig hebt, zoekt u het profiel op en selecteert u **Profiel verwijderen**.

:::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-delete-priority-profile.png" alt-text="Prioriteitsprofiel in Traffic Manager verwijderen":::

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de routeringsmethode met prioriteit:

> [!div class="nextstepaction"]
> [Routeringsmethode op basis van prioriteit](traffic-manager-routing-methods.md#priority-traffic-routing-method)
