---
title: 'Zelfstudie: De geografische verkeersrouteringsmethode configureren met Azure Traffic Manager'
description: In deze zelfstudie wordt uitgelegd hoe u de methode voor de routering van geografisch verkeer configureert in Azure Traffic Manager
services: traffic-manager
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2020
ms.author: duau
ms.openlocfilehash: 29b3cdde328a994e5806df810db15b529a6da9af
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188663"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Zelfstudie: De geografische verkeersrouteringsmethode configureren met Traffic Manager

Met de geografische verkeersrouteringsmethode kunt u verkeer omleiden naar specifieke eindpunten op basis van de geografische locatie waar de aanvragen vandaan komen. In deze zelfstudie wordt uitgelegd hoe u een Traffic Manager-profiel maakt met deze routeringsmethode en hoe u de eindpunten zo configureert dat verkeer van specifieke geografische locaties wordt ontvangen.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Een Traffic Manager-profiel met geografische routering maken.
> - Een genest eindpunt configureren.
> - Het Traffic Manager-profiel gebruiken.
> - Traffic Manager-profiel verwijderen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

1. Meld u vanuit een browser aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **+ Een resource maken** aan de linkerkant. Zoek naar **Traffic Manager-profiel** en selecteer **Maken**.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager.png" alt-text="Een Traffic Manager-profiel maken":::

1. Definieer op de pagina *Traffic Manager-profiel maken* de volgende instellingen:

    | Instelling         | Waarde                                              |
    | ---             | ---                                                |
    | Naam            | Geef een naam op voor het profiel. Deze naam moet uniek zijn binnen de trafficmanager.net-zone. Voor toegang tot uw Traffic Manager-profiel gebruikt u de DNS-naam `<profilename>.trafficmanager.net`. |    
    | Routeringsmethode  | Selecteer **Geografisch**. |
    | Abonnement    | Selecteer uw abonnement. |
    | Resourcegroep   | Gebruik een bestaande resourcegroep of maak een nieuwe resourcegroep om dit profiel voor te maken. Als u ervoor kiest om een nieuwe resourcegroep te maken, gebruikt u de vervolgkeuzelijst *Locatie van resourcegroep* om de locatie van de resourcegroep op te geven. Deze instelling verwijst naar de locatie van de resourcegroep en heeft geen invloed op het Traffic Manager-profiel dat wereldwijd wordt geïmplementeerd. |

1. Selecteer **Maken** om uw Traffic Manager-profiel te implementeren.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png" alt-text="Pagina Een Traffic Manager-profiel maken":::

## <a name="add-endpoints"></a>Eindpunten toevoegen

1. Selecteer het Traffic Manager-profiel in de lijst.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-list-geographic.png" alt-text="Geografische lijst in Traffic Manager":::

1. Selecteer **Eindpunten** onder *Instellingen* en selecteer **+ Toevoegen** om een nieuw eindpunt toe te voegen.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/add-geographic-endpoint.png" alt-text="Eindpunten toevoegen":::

1. Typ of selecteer de volgende instellingen: 

    | Instelling                | Waarde                                              |
    | ---                    | ---                                                |
    | Type                   | Selecteer het eindpunttype. Voor geografische routeringsprofielen die worden gebruikt in productie, raden we u ten zeerste aan gebruik te maken van geneste eindpunttypen die een onderliggend profiel met meer dan één eindpunt bevatten. Zie [Veelgestelde vragen over geografische verkeersrouteringsmethoden](traffic-manager-FAQs.md) voor meer informatie. |    
    | Naam                   | Geef een naam op om dit eindpunt aan te duiden. |
    | Doelbrontype   | Selecteer het resourcetype voor het doel. |
    | Doelbron        | Selecteer de resource in de lijst. |

    > [!Note]
    > Bepaalde velden op deze pagina zijn afhankelijk van het type eindpunt dat u toevoegt:
    > 1. Als u een Azure-eindpunt toevoegt, selecteert u het **Doelresourcetype** en het **Doel** op basis van de resource waarnaar u verkeer wilt omleiden
    > 1. Als u een **Extern** eindpunt toevoegt, geeft u de **FQDN (Fully Qualified Domain Name)** voor uw eindpunt op.
    > 1. Als u een **Genest eindpunt** toevoegt, selecteert u de **Doelresource** die overeenkomt met het onderliggende profiel dat u wilt gebruiken en geeft u het **Minimale aantal onderliggende eindpunten** op.

1. Gebruik in het gedeelte *Geo-toewijzing* de vervolgkeuzelijst om de regio's toe te voegen van waaruit u wilt dat verkeer naar dit eindpunt wordt gestuurd. Er moet ten minste één regio worden toegevoegd. U kunt meerdere regio's toewijzen.

1. Herhaal de laatste stap voor alle eindpunten die u wilt toevoegen onder dit profiel en selecteer **Opslaan**.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-add-endpoint.png" alt-text="Traffic Manager-eindpunt toevoegen":::

## <a name="use-the-traffic-manager-profile"></a>Het Traffic Manager-profiel gebruiken

1.  Zoek in de zoekbalk van de portal de naam van het **Traffic Manager-profiel** dat u in het vorige gedeelte hebt gemaakt en selecteer het Traffic Manager-profiel in de weergegeven resultaten.
    
    :::image type="content" source="./media/traffic-manager-geographic-routing-method/search-traffic-manager-profile.png" alt-text="Traffic Manager-profiel zoeken":::

1. Het **Traffic Manager-profiel** geeft de DNS-naam weer van het Traffic Manager-profiel dat u zojuist hebt gemaakt. Deze naam kan door clients worden gebruikt (bijvoorbeeld door ernaar te navigeren met een webbrowser) om naar het juiste eindpunt te gaan, zoals wordt bepaald door het routeringstype. Bij geografische routering kijkt Traffic Manager naar de bron-IP van de binnenkomende aanvraag en bepaalt het de regio van oorsprong. Als die regio is toegewezen aan een eindpunt, wordt er verkeer naar gerouteerd. Als deze regio niet aan een eindpunt is toegewezen, retourneert Traffic Manager een NODATA-queryreactie.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-geographic-overview.png" alt-text="Overzicht van geografische Traffic Manager":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u het Traffic Manager-profiel niet meer nodig hebt, zoekt u het profiel op en selecteert u **Profiel verwijderen**.

:::image type="content" source="./media/traffic-manager-geographic-routing-method/delete-traffic-manager-profile.png" alt-text="Traffic Manager-profiel verwijderen":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de geografische verkeersrouteringsmethode:

> [!div class="nextstepaction"]
> [Geografische verkeersrouteringsmethode](traffic-manager-routing-methods.md#geographic)
