---
title: Het Connected Factory-dash board gebruiken-Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u functies van het Connected Factory-dash board gebruikt om uw industriële IoT-apparaten te bewaken en te beheren.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: 797bd97940aa49db87b4ca4dd96f140208cd69b6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011214"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>Functies in het Connected Factory Solution Accelerator-dash board gebruiken

> [!IMPORTANT]
> Zie [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) voor de meest recente inhoud terwijl we dit artikel bijwerken.

De Snelstartgids [een Cloud oplossing implementeren voor het beheren van mijn industriële IOT-apparaten](quickstart-connected-factory-deploy.md) Quick Start leert u hoe u door het dash board navigeert en reageert op waarschuwingen. In deze hand leiding worden enkele extra dashboard functies weer gegeven die u kunt gebruiken om uw industriële IoT-apparaten te bewaken en te beheren.

## <a name="apply-filters"></a>Filters toepassen

U kunt de informatie die wordt weer gegeven in het dash board filteren in het deel venster **fabrieks locaties** of in het paneel **waarschuwingen** :

1. Klik op de **trechter** om een lijst met beschikbare filters weer te geven in het paneel met factorylocaties of het paneel Waarschuwingen.

1. Het deel venster filters wordt weer gegeven:

    [![Filters van de oplossingsversneller voor verbonden factory's](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. Kies het filter dat u nodig hebt en klik op **Toep assen**. Het is ook mogelijk om vrije tekst in de filter velden te typen.

1. Vervolgens wordt het filter toegepast. Het pictogram extra trechter geeft aan dat er een filter is toegepast:

    [![Het filter Connected Factory Solution Accelerator is toegepast](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > Een actief filter heeft geen invloed op de weer gegeven OEE-en KPI-waarden, maar de inhoud van de lijst wordt gefilterd.

1. Als u een filter wilt wissen, klikt u op de trechter en klikt u op **wissen** in het deel venster filter.

## <a name="browse-an-opc-ua-server"></a>Door een OPC UA-server bladeren

Wanneer u de oplossings versneller implementeert, voorziet u automatisch een set gesimuleerde OPC UA-servers die u vanuit het dash board kunt bekijken. Gesimuleerde servers maken het eenvoudig om met de oplossings versneller te experimenteren zonder dat u echte servers hoeft te implementeren.

1. Klik op het **browser pictogram** in de navigatie balk van het dash board:

    [![Serverbrowser van de oplossingsversneller Verbonden factory](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. Kies een van de servers in de lijst waarin de servers worden weer gegeven die voor u zijn geïmplementeerd in de oplossings versneller:

    [![Lijst met Connected Factory Solution Accelerator server](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. Klik op **Verbinden**. Er wordt een beveiligingsdialoogvenster weergegeven. Voor de simulatie is het veilig om op **door gaan** te klikken.

1. Klik op een van de knooppunten in de serverstructuur om deze uit te vouwen. Knoop punten die telemetrie publiceren, hebben een vinkje naast het volgende:

    [![Serverstructuur in de oplossingsversneller Verbonden factory](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. Klik met de rechtermuisknop op een item om het knooppunt te lezen, te schrijven, te publiceren of aan te roepen. Welke acties er beschikbaar zijn, is afhankelijk van uw machtigingen en de kenmerken van het knooppunt. Met de optie voor lezen wordt een contextpaneel weergegeven met de waarde van dat knooppunt. Met de optie voor schrijven wordt een contextpaneel weergegeven waarin u een nieuwe waarde kunt invoeren. Met de optie voor aanroepen wordt een knooppunt weergegeven waarin u de parameters voor het aanroepen kunt invoeren.

## <a name="publish-a-node"></a>Een knooppunt publiceren

Wanneer u door een *gesimuleerde OPC UA-server* bladert, kunt u ook nieuwe knooppunten publiceren. Vervolgens kunt u de telemetrie van deze knooppunten in de oplossing analyseren. Deze *gesimuleerde OPC UA-servers* maken het gemakkelijk om met de oplossings versneller te experimenteren zonder echte apparaten te implementeren:

1. Blader naar een knooppunt in de browserstructuur van de OPC UA-server die u wilt publiceren.

1. Klik met de rechtermuisknop op het knooppunt. Klik op **publiceren**:

    [![Publicatie knooppunt van de verbonden Factory-oplossings versneller](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. Er word een contextpaneel weergegeven waarin staat dat het publiceren is voltooid. Het knoop punt wordt weer gegeven in de weer gave op het station met een vinkje ernaast:

    [![Het publiceren van de Connected Factory Solution Accelerator is voltooid](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>Opdracht en controle

Met Verbonden factory kunt u uw industriële apparaten rechtstreeks vanuit de cloud beheren en bedienen. U kunt deze functie gebruiken om te reageren op waarschuwingen die door het apparaat worden gegenereerd. U kunt bijvoorbeeld een opdracht naar het apparaat verzenden om een druk afsluiter te openen. U vindt de beschikbare opdrachten in het knooppunt **StationCommands** in de browserstructuur van de OPC UA-servers. In dit scenario opent u een overdrukventiel op de verzamelplaats van een productielijn in München. Als u de functionaliteit van de opdracht en het besturings element wilt gebruiken, moet u de rol **beheerder** hebben voor de implementatie van oplossings versnelling:

1. Blader naar het knoop punt **StationCommands** in de browser structuur van de OPC UA-server voor de München, productie regel 0, assembly station.

1. Kies de opdracht die u wilt gebruiken. Klik met de rechter muisknop op het knoop punt **OpenPressureReleaseValve** . Klik op **aanroepen**:

    [![Opdracht aanroepen met de oplossingsversneller Verbonden factory](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. Er wordt een context paneel weer gegeven met de melding dat de methode die u gaat aanroepen en eventuele parameter Details. Klik op **aanroepen**:

    [![Call-para meters Connected Factory Solution Accelerator](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. Het contextpaneel wordt bijgewerkt met de melding dat het aanroepen van de methode is voltooid. U kunt controleren of de oproep is voltooid door de waarde van het drukknooppunt te lezen, die na de aanroep is bijgewerkt.

    [![Aanroep van de oplossingsversneller Verbonden factory gelukt](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>Achter de schermen

Wanneer u een oplossingsversneller implementeert, maakt het implementatieproces meerdere resources in het door u geselecteerde Azure-abonnement. U kunt deze resources weergeven in Azure [Portal](https://portal.azure.com). Het implementatieproces maakt een **resourcegroep** met een naam die is gebaseerd op de naam die u voor uw oplossingsversneller hebt gekozen:

[![Resource groep Connected Factory Solution Accelerator](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

U kunt de instellingen van elke resource weergeven door deze te selecteren in de lijst met resources in de resourcegroep.

U kunt ook de bron code voor de oplossings versneller weer geven in de GitHub-opslag plaats [Azure-IOT-Connected-Factory](https://github.com/Azure/azure-iot-connected-factory) .

Wanneer u klaar bent, kunt u de oplossings versneller verwijderen uit uw Azure-abonnement op de [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) -site. Met de site kunt u gemakkelijk alle resources verwijderen die zijn ingericht op het moment dat de oplossingsversneller werd gemaakt.

> [!NOTE]
> Om ervoor te zorgen dat u alles met betrekking tot de oplossings versneller verwijdert, verwijdert u dit op de [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) -site. Verwijder de resourcegroep niet in de portal.

## <a name="next-steps"></a>Volgende stappen

Nu u een werkende oplossingsversneller hebt geïmplementeerd, kunt u gaan werken met de IoT-oplossingsversnellers door de volgende artikelen te lezen:

* [De Connected Factory Solution Accelerator configureren](iot-accelerators-connected-factory-configure.md)
* [Machtigingen op de azureiotsolutions.com-site](iot-accelerators-permissions.md)
