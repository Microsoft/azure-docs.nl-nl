---
title: Uw Azure IoT Central-gegevens visualiseren in een Power BI dash board | Microsoft Docs
description: Gebruik de Power BI-oplossing voor Azure IoT Central om uw IoT Central gegevens te visualiseren en te analyseren.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/4/2019
ms.topic: conceptual
ms.openlocfilehash: dd67a4f1a005abc7319723efcc3f2944b18c4f5f
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109230"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Uw Azure IoT Central-gegevens visualiseren en analyseren in een Power BI dash board

*Dit onderwerp is van toepassing op beheerders en ontwikkel aars van oplossingen.*

[!Note] Deze oplossing maakt gebruik van [verouderde functies voor gegevens export](./howto-export-data-legacy.md). Blijf op de hoogte van bijgewerkte richt lijnen voor het maken van verbinding met Power BI met behulp van de meest recente gegevens export.

:::image type="content" source="media/howto-connect-powerbi/iot-continuous-data-export.png" alt-text="Power BI solution-pijp lijn":::

Gebruik de Power BI-oplossing voor Azure IoT Central v3 om een krachtig Power BI dash board te maken voor het bewaken van de prestaties van uw IoT-apparaten. In uw Power BI dash board kunt u het volgende doen:

- Bijhouden hoeveel gegevens uw apparaten na verloop van tijd worden verzonden
- Gegevens volumes vergelijken tussen verschillende telemetrie-stromen
- Filteren op gegevens die worden verzonden door specifieke apparaten
- De meest recente telemetriegegevens in een tabel weer geven

Met deze oplossing wordt een pijp lijn ingesteld waarmee gegevens worden gelezen uit uw [continue data export](./howto-export-data-legacy.md) Azure Blob Storage-account. De pijp lijn gebruikt Azure Functions, Azure Data Factory en Azure SQL Database om de gegevens te verwerken en te transformeren. u kunt de gegevens in een Power BI rapport dat u als een PBIX-bestand hebt gedownload, visualiseren en analyseren. Alle resources worden gemaakt in uw Azure-abonnement, zodat u elk onderdeel kunt aanpassen aan uw behoeften.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze hand leiding wilt uitvoeren, hebt u een actief Azure-abonnement nodig. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Voor het instellen van de oplossing zijn de volgende resources vereist:

- Een IoT Central toepassing van versie 3. Zie [over uw toepassing](./howto-get-app-info.md)voor meer informatie over het controleren van de versie van uw toepassing. Zie [een Azure IOT Central-toepassing maken](./quick-deploy-iot-central.md)voor meer informatie over het maken van een IOT Central-toepassing.
- Continue gegevens export geconfigureerd voor het exporteren van telemetrie, apparaten en apparaatinstellingen naar Azure Blob-opslag. Zie [IOT-gegevens exporteren naar bestemmingen in azure](howto-export-data.md)voor meer informatie.
  - Zorg ervoor dat alleen uw IoT Central-toepassing gegevens exporteert naar de BLOB-container.
  - Uw [apparaten moeten JSON-gecodeerde berichten verzenden](../../iot-hub/iot-hub-devguide-messages-d2c.md). Apparaten moeten `contentType:application/JSON` en `contentEncoding:utf-8` of of worden opgegeven `contentEncoding:utf-16` `contentEncoding:utf-32` in de eigenschappen van het bericht systeem.
- Power BI Desktop (meest recente versie). Zie [Power bi down loads](https://powerbi.microsoft.com/downloads/).
- Power BI Pro (als u het dash board met anderen wilt delen).

> [!NOTE]
> Als u een IoT Central toepassing van versie 2 gebruikt, raadpleegt u [uw Azure IOT Central-gegevens visualiseren en analyseren in een Power bi dash board](/previous-versions/azure/iot-central/core/howto-connect-powerbi) op de documentatie site van eerdere versies.

## <a name="install"></a>Installeren

Als u de pijp lijn wilt instellen, gaat u naar de pagina [Power BI-oplossing voor Azure IOT Central v3](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) op de **Microsoft AppSource** -site. Selecteer **nu downloaden** en volg de instructies.

Wanneer u het PBIX-bestand opent, moet u de instructies lezen en volgen op het voor blad. In deze instructies wordt beschreven hoe u uw rapport verbindt met uw SQL database.

## <a name="report"></a>Rapport

Het PBIX-bestand bevat het rapport **apparaten en telemetrie** toont een historisch overzicht van de telemetrie die is verzonden door apparaten. Het biedt een uitsplitsing van de verschillende typen telemetrie en toont ook de meest recente telemetrie die wordt verzonden door apparaten.

:::image type="content" source="media/howto-connect-powerbi/report.png" alt-text="Power BI apparaten en telemetrie-rapport":::

## <a name="pipeline-resources"></a>Pijplijn resources

U hebt toegang tot alle Azure-resources die de pijp lijn vormen in de Azure Portal. Alle resources bevinden zich in de resource groep die u hebt gemaakt bij het instellen van de pijp lijn.

:::image type="content" source="media/howto-connect-powerbi/azure-deployment.png" alt-text="Azure Portal weer gave van resource groep":::

In de volgende lijst wordt de rol van elke resource in de pijp lijn beschreven:

### <a name="azure-functions"></a>Azure Functions

De functie-app van Azure wordt elke keer geactiveerd IoT Central een nieuw bestand naar de Blob-opslag schrijft. Met de functies worden gegevens geëxtraheerd van de telemetrie, apparaten en apparaatinstellingen blobs om de tussenliggende SQL-tabellen te vullen die Azure Data Factory gebruikt.

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory maakt verbinding met SQL Database als een gekoppelde service. De sjabloon voert opgeslagen procedures uit om de gegevens te verwerken en op te slaan in de analyse tabellen.

Azure Data Factory wordt elke 15 minuten uitgevoerd om de laatste batch met gegevens te transformeren die in de SQL-tabellen moet worden geladen (dit is het huidige minimale nummer voor de trigger van het **tumblingvenstertriggers-venster**).

### <a name="azure-sql-database"></a>Azure SQL Database

Azure Data Factory genereert een set analyse tabellen voor Power BI. U kunt deze schema's verkennen in Power BI en gebruiken om uw eigen visualisaties te maken.

## <a name="estimated-costs"></a>Geschatte kosten

De pagina [Power BI oplossing voor Azure IOT Central v3](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) op de Microsoft AppSource site bevat een koppeling naar een kosten Estimator voor de resources die u implementeert.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u uw gegevens in Power BI kunt visualiseren, is de voorgestelde volgende stap informatie [over het beheren van apparaten](howto-manage-devices.md).
