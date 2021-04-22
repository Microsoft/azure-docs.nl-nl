---
title: Uw Azure IoT Central uitbreiden met aangepaste | Microsoft Docs
description: Als oplossingsontwikkelaar kunt u een IoT Central configureren voor aangepaste analyses en visualisaties. Deze oplossing maakt gebruik van Azure Databricks.
author: philmea
ms.author: philmea
ms.date: 03/15/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 9ada9947b217944d9aec9f785f4716bfe43315b1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872763"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Azure IoT Central uitbreiden met aangepaste analyse met behulp van Azure Databricks

In deze handleiding ziet u, als ontwikkelaar van oplossingen, hoe u uw IoT Central uitbreidt met aangepaste analyses en visualisaties. In het voorbeeld wordt een [Azure Databricks](/azure/azure-databricks/) gebruikt om de IoT Central telemetriestroom te analyseren en visualisaties te genereren, zoals [boxplots.](https://wikipedia.org/wiki/Box_plot)  

In deze handleiding ziet u hoe u uw IoT Central verder uitbreidt dan wat het al kan doen met [de ingebouwde analysehulpprogramma's](./howto-create-custom-analytics.md).

In deze handleiding leert u het volgende:

* Telemetrie streamen vanuit een IoT Central met behulp van *continue gegevensexport.*
* Maak een Azure Databricks omgeving voor het analyseren en plotten van telemetrie van apparaten.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze handleiding wilt uitvoeren, hebt u een actief Azure-abonnement nodig.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

### <a name="iot-central-application"></a>IoT Central-toepassing

Maak een IoT Central toepassing op de [Azure IoT Central Application Manager-website](https://aka.ms/iotcentral) met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Prijsplan | Standard |
| Toepassingsjabloon | In-store analytics – bewaking van voorwaarden |
| De naam van de toepassing | Accepteer de standaardwaarde of kies uw eigen naam |
| URL | Accepteer de standaardwaarde of kies uw eigen unieke URL-voorvoegsel |
| Directory | Uw Azure Active Directory-tenant |
| Azure-abonnement | Uw Azure-abonnement |
| Region | Uw dichtstbijzijnde regio |

In de voorbeelden en schermafbeeldingen in dit artikel wordt de **Verenigde Staten** gebruikt. Kies een locatie bij u in de buurt en zorg ervoor dat u al uw resources in dezelfde regio maakt.

Deze toepassingssjabloon bevat twee gesimuleerde thermostaatapparaten die telemetrie verzenden.

### <a name="resource-group"></a>Resourcegroep

Gebruik de [Azure Portal om een resourcegroep](https://portal.azure.com/#create/Microsoft.ResourceGroup) met de naam **IoTCentralAnalysis** te maken die de andere resources bevat die u maakt. Maak uw Azure-resources op dezelfde locatie als uw IoT Central toepassing.

### <a name="event-hubs-namespace"></a>Event Hubs-naamruimte

Gebruik de [Azure Portal om een Event Hubs maken met](https://portal.azure.com/#create/Microsoft.EventHub) de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Naam    | Kies de naam van uw naamruimte |
| Prijscategorie | Basic |
| Abonnement | Uw abonnement |
| Resourcegroep | IoTCentralAnalysis |
| Locatie | VS - oost |
| Doorvoereenheden | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks werkruimte

Gebruik de [Azure Portal om een Azure Databricks-service](https://portal.azure.com/#create/Microsoft.Databricks) te maken met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Werkruimtenaam    | De naam van uw werkruimte kiezen |
| Abonnement | Uw abonnement |
| Resourcegroep | IoTCentralAnalysis |
| Locatie | VS - oost |
| Prijscategorie | Standard |

Wanneer u de vereiste resources hebt gemaakt, ziet uw **IoTCentralAnalysis-resourcegroep** eruit zoals in de volgende schermafbeelding:

:::image type="content" source="media/howto-create-custom-analytics/resource-group.png" alt-text="afbeelding van IoT Central analyseresourcegroep.":::

## <a name="create-an-event-hub"></a>Een Event Hub maken

U kunt een toepassing IoT Central om continu telemetrie te exporteren naar een Event Hub. In deze sectie maakt u een Event Hub voor het ontvangen van telemetrie van uw IoT Central toepassing. De Event Hub levert de telemetrie aan uw Stream Analytics voor verwerking.

1. Navigeer Azure Portal de Event Hubs en selecteer **+ Event Hub**.
1. Noem uw Event Hub **centralexport.**
1. Selecteer centralexport in de lijst met Event Hubs in uw **naamruimte.** Kies vervolgens **Beleid voor gedeelde toegang.**
1. Selecteer **+ Toevoegen**. Maak een beleid met de **naam SendListen** met de **claims Send** en **Listen.**
1. Wanneer het beleid gereed is, selecteert u het in de lijst en kopieert u vervolgens de waarde **verbindingsreeks-primaire** sleutel.
1. Noteer deze connection string, u gebruikt deze later wanneer u uw Databricks-notebook configureert om te lezen uit de Event Hub.

Uw Event Hubs-naamruimte ziet er als volgt uit:

:::image type="content" source="media/howto-create-custom-analytics/event-hubs-namespace.png" alt-text="afbeelding van Event Hubs naamruimte.":::

## <a name="configure-export-in-iot-central"></a>Export configureren in IoT Central

In deze sectie configureert u de toepassing voor het streamen van telemetrie van de gesimuleerde apparaten naar uw Event Hub.

Ga op [Azure IoT Central application manager-website](https://aka.ms/iotcentral) naar de IoT Central toepassing die u eerder hebt gemaakt. Als u de export wilt configureren, maakt u eerst een bestemming:

1. Navigeer naar **de pagina Gegevensexport** en selecteer vervolgens **Bestemmingen**.
1. Selecteer **+ Nieuwe bestemming.**
1. Gebruik de waarden in de volgende tabel om een bestemming te maken:

    | Instelling | Waarde |
    | ----- | ----- |
    | Doelnaam | Telemetrie event hub |
    | Doeltype | Azure Event Hubs |
    | Verbindingsreeks | De Event Hub connection string u eerder hebt noteren |

    De **Event Hub** wordt als **centralexport geopend.**

    :::image type="content" source="media/howto-create-custom-analytics/data-export-1.png" alt-text="Schermopname van de bestemming voor het exporteren van gegevens.":::

1. Selecteer **Opslaan**.

De exportdefinitie maken:

1. Navigeer naar **de pagina Gegevensexport** en **selecteer + Nieuw exporteren.**

1. Gebruik de waarden in de volgende tabel om de export te configureren:

    | Instelling | Waarde |
    | ------- | ----- |
    | Exportnaam | Event Hub Exporteren |
    | Ingeschakeld | Uit |
    | Type gegevens dat moet worden geëxporteerd | Telemetrie |
    | Bestemmingen | Selecteer **+ Doel** en selecteer vervolgens **Telemetrie event hub** |

1. Selecteer **Opslaan**.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-2.png" alt-text="Schermopname van de definitie van gegevensexport.":::

Wacht totdat de exportstatus In orde is **op** de **pagina Gegevensexport** voordat u doorgaat.

## <a name="configure-databricks-workspace"></a>Databricks-werkruimte configureren

Navigeer Azure Portal de Azure Databricks service en selecteer **Werkruimte starten.** Er wordt een nieuw tabblad geopend in uw browser en u wordt bij uw werkruimte aangegeven.

### <a name="create-a-cluster"></a>Een cluster maken

Selecteer **op Azure Databricks** pagina Nieuw cluster onder de lijst met algemene **taken.**

Gebruik de informatie in de volgende tabel om uw cluster te maken:

| Instelling | Waarde |
| ------- | ----- |
| Clusternaam | centralanalysis |
| Clustermodus | Standard |
| Databricks Runtime versie | 5.5 LTS (Scala 2.11, Spark 2.4.5) |
| Python-versie | 3 |
| Automatisch schalen inschakelen | No |
| Beëindigen na minuten van inactiviteit | 30 |
| Werktype | Standard_DS3_v2 |
| Werknemers | 1 |
| Stuurprogrammatype | Hetzelfde als werk |

Het maken van een cluster kan enkele minuten duren. Wacht tot het maken van het cluster is voltooid voordat u verder gaat.

### <a name="install-libraries"></a>Bibliotheken installeren

Wacht op **de pagina** Clusters totdat de clustertoestand Wordt **uitgevoerd is.**

De volgende stappen laten zien hoe u de bibliotheek die uw voorbeeld nodig heeft, in het cluster kunt importeren:

1. Wacht op **de pagina** Clusters totdat de status van het interactieve **cluster centralanalysis** **Wordt uitgevoerd is.**

1. Selecteer het cluster en kies vervolgens het **tabblad** Bibliotheken.

1. Kies op **het** tabblad Bibliotheken de optie **Nieuwe installeren.**

1. Kies op **de pagina Bibliotheek** installeren **maven** als de bibliotheekbron.

1. Voer in **het tekstvak** Coördinaten de volgende waarde in: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Kies **Installeren om** de bibliotheek op het cluster te installeren.

1. De status van de bibliotheek is nu **Geïnstalleerd:**

:::image type="content" source="media/howto-create-custom-analytics/cluster-libraries.png" alt-text="Schermopname van Bibliotheek geïnstalleerd.":::

### <a name="import-a-databricks-notebook"></a>Een Databricks-notebook importeren

Gebruik de volgende stappen om een Databricks-notebook te importeren dat de Python-code bevat voor het analyseren en visualiseren van uw IoT Central telemetrie:

1. Navigeer naar **de pagina** Werkruimte in uw Databricks-omgeving. Selecteer de vervolgkeuzekeuze naast uw accountnaam en kies **vervolgens Importeren.**

1. Kies ervoor om te importeren vanuit een URL en voer het volgende adres in: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Als u het notebook wilt importeren, kiest **u Importeren.**

1. Selecteer de **werkruimte om** het geïmporteerde notebook weer te bieden:

:::image type="content" source="media/howto-create-custom-analytics/import-notebook.png" alt-text="Schermopname van Geïmporteerd notebook.":::

5. Bewerk de code in de eerste Python-cel om de code toe te Event Hubs connection string u eerder hebt opgeslagen:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Analyse uitvoeren

Als u de analyse wilt uitvoeren, moet u het notebook aan het cluster koppelen:

1. Selecteer **Losgekoppeld** en selecteer vervolgens het cluster **centralanalysis.**
1. Als het cluster niet wordt uitgevoerd, start u het.
1. Selecteer de knop Uitvoeren om het notebook te starten.

Mogelijk ziet u een fout in de laatste cel. Als dat het zo is, controleert u of de vorige cellen actief zijn, wacht u een minuut totdat sommige gegevens naar de opslag zijn geschreven en vervolgens moet u de laatste cel opnieuw uitvoeren.

### <a name="view-smoothed-data"></a>Vloeiende gegevens weergeven

Schuif in het notebook omlaag naar cel 14 om een grafiek te zien van de rolling gemiddelde vochtigheid per apparaattype. Deze plot wordt continu bijgewerkt wanneer streaming-telemetrie binnenkomt:

:::image type="content" source="media/howto-create-custom-analytics/telemetry-plot.png" alt-text="Schermopname van een vloeiende telemetrieplot.":::

U kunt de grafiek in het notebook wijzigen.

### <a name="view-box-plots"></a>Boxplots weergeven

Schuif in het notebook omlaag naar cel 20 om de vakplots [te zien.](https://en.wikipedia.org/wiki/Box_plot) De boxplots zijn gebaseerd op statische gegevens. Als u ze wilt bijwerken, moet u de cel opnieuw gebruiken:

:::image type="content" source="media/howto-create-custom-analytics/box-plots.png" alt-text="Schermopname van vakplots.":::

U kunt de plots in het notebook het beste desizereren.

## <a name="tidy-up"></a>Opruimen

Verwijder de **resourcegroep IoTCentralAnalysis** in de Azure Portal om deze op te schonen en onnodige kosten te Azure Portal.

U kunt de IoT Central verwijderen van de **pagina Beheer** in de toepassing.

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u het volgende geleerd:

* Telemetrie streamen vanuit een IoT Central met behulp van *continue gegevensexport.*
* Maak een Azure Databricks om telemetriegegevens te analyseren en te plotten.

Nu u weet hoe u aangepaste analyses maakt, is de voorgestelde volgende stap het visualiseren en analyseren van uw [Azure IoT Central-gegevens in](howto-connect-powerbi.md)een Power BI dashboard.
