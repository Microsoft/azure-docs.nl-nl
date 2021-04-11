---
title: De oplossing Connected Factory aanpassen-Azure | Microsoft Docs
description: Een beschrijving van het aanpassen van het gedrag van de Connected Factory Solution Accelerator.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: a092859517ee6ac41e62e55fe95f72b8bfdf87d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646096"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Aanpassen hoe de oplossing Connected Factory gegevens van uw OPC UA-servers weergeeft

> [!IMPORTANT]
> Zie [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) voor de meest recente inhoud terwijl we dit artikel bijwerken.

In de oplossing Connected Factory worden gegevens van de OPC UA-servers die zijn verbonden met de oplossing geaggregeerd en weer gegeven. U kunt bladeren en opdrachten naar de OPC UA-servers in uw oplossing sturen. Zie de [Veelgestelde vragen over Connected Factory](iot-accelerators-faq-cf.md)voor meer informatie over OPC ua.

Voor beelden van geaggregeerde gegevens in de oplossing zijn de algemene efficiëntie van apparatuur (OEE) en Key Performance Indica tors (Kpi's) die u kunt weer geven in het dash board op het niveau van de fabrieks-, lijn-en kantoor. De volgende scherm afbeelding toont de OEE-en KPI-waarden voor het **assemblage** station, op **productie lijn 1**, in de **München** :

![Voor beeld van OEE-en KPI-waarden in de oplossing][img-oee-kpi]

Met deze oplossing kunt u gedetailleerde informatie bekijken van specifieke gegevens items van de OPC UA-servers, genaamd *stations*. De volgende scherm afbeelding toont de grafiek van het aantal gefabriceerde items van een specifiek station:

![Aantal gefabriceerde items][img-manufactured-items]

Als u op een van de grafieken klikt, kunt u de gegevens verder verkennen met behulp van Time Series Insights (TSI):

![Gegevens verkennen met behulp van Time Series Insights][img-tsi]

In dit artikel wordt het volgende beschreven:

- Hoe de gegevens beschikbaar worden gemaakt voor de verschillende weer gaven in de oplossing.
- De manier waarop de gegevens worden weer gegeven, kunt u aanpassen.

## <a name="data-sources"></a>Gegevensbronnen

De oplossing Connected Factory geeft gegevens weer van de OPC UA-servers die zijn verbonden met de oplossing. De standaard installatie omvat verschillende OPC UA-servers waarop een fabrieks simulatie wordt uitgevoerd. U kunt uw eigen OPC UA-servers die [verbinding maken via een gateway] [lnk-Connect-CF], toevoegen aan uw oplossing.

U kunt door de gegevens items bladeren die door een verbonden OPC UA-server naar uw oplossing in het dash board kunnen worden verzonden:

1. Kies **browser** om naar de weer gave **een OPC UA-server selecteren** te navigeren:

    ![Navigeer naar de weer gave een OPC UA-server selecteren][img-select-server]

1. Selecteer een server en klik op **verbinden**. Klik op **door gaan** wanneer de beveiligings waarschuwing wordt weer gegeven.

    > [!NOTE]
    > Deze waarschuwing wordt slechts eenmaal weer gegeven voor elke server en er wordt een vertrouwens relatie tussen het dash board van de oplossing en de server tot stand gebracht.

1. U kunt nu door de gegevens items bladeren die door de server naar de oplossing kunnen worden verzonden. Items die naar de oplossing worden verzonden, hebben een vinkje:

    ![Gepubliceerde items][img-published]

1. Als u een *beheerder* bent in de oplossing, kunt u ervoor kiezen een gegevens item te publiceren om het beschikbaar te maken in de oplossing Connected Factory. Als beheerder kunt u ook de waarde van de gegevens items wijzigen en methoden aanroepen in de OPC UA-server.

## <a name="map-the-data"></a>De gegevens toewijzen

Met de oplossing Connected Factory wordt de gepubliceerde gegevens items van de OPC UA-server gekoppeld aan de verschillende weer gaven in de oplossing. De oplossing Connected Factory wordt geïmplementeerd op uw Azure-account wanneer u de oplossing inricht. Met een JSON-bestand in de oplossing Visual Studio Connected Factory worden deze toewijzings gegevens opgeslagen. U kunt dit JSON-configuratie bestand weer geven en wijzigen in de Connected Factory Visual Studio-oplossing. Nadat u een wijziging hebt aangebracht, kunt u de oplossing opnieuw implementeren.

U kunt het configuratie bestand gebruiken voor het volgende:

- Bewerk de bestaande gesimuleerde fabrieken, productie lijnen en stations.
- Kaart gegevens van echte OPC UA-servers die u met de oplossing verbindt.

Zie [How to configure the Connected Factory Solution Accelerator ](iot-accelerators-connected-factory-configure.md)(Engelstalig) voor meer informatie over het toewijzen en samen voegen van de gegevens om te voldoen aan uw specifieke vereisten.

## <a name="deploy-the-changes"></a>De wijzigingen implementeren

Wanneer u klaar bent met het aanbrengen van wijzigingen in de **ContosoTopologyDescription.jsin** het bestand, moet u de oplossing Connected Factory opnieuw implementeren in uw Azure-account.

De opslag plaats **Azure-IOT-Connected-Factory** bevat een **build.ps1** Power shell-script dat u kunt gebruiken om de oplossing opnieuw te bouwen en te implementeren.

## <a name="next-steps"></a>Volgende stappen

Lees de volgende artikelen voor meer informatie over de Connected Factory Solution Accelerator:

* [Machtigingen op de azureiotsolutions.com-site][lnk-permissions]
* [Veelgestelde vragen over Verbonden Factory](iot-accelerators-faq-cf.md)
* [Veelgestelde vragen][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md