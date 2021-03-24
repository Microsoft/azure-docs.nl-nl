---
title: Azure IoT Central uitbreiden met aangepaste analyses | Microsoft Docs
description: Configureer als ontwikkelaar van oplossingen een IoT Central-toepassing om aangepaste analyses en visualisaties uit te voeren. Deze oplossing maakt gebruik van Azure Databricks.
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 03/15/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 458c93fd3e13a958137c762a0979af918a70d930
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023033"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Azure IoT Central uitbreiden met aangepaste analyses met behulp van Azure Databricks

In deze hand leiding wordt uitgelegd hoe u, als oplossings ontwikkelaar, uw IoT Central-toepassing kunt uitbreiden met aangepaste analyses en visualisaties. In het voor beeld wordt een [Azure Databricks](/azure/azure-databricks/) -werk ruimte gebruikt voor het analyseren van de IOT Central telemetrische stroom en voor het genereren van visualisaties zoals [box plots](https://wikipedia.org/wiki/Box_plot).  

In deze hand leiding wordt uitgelegd hoe u IoT Central uitbreidt dan wat u al kunt doen met de [ingebouwde analyse hulpprogramma's](./howto-create-custom-analytics.md).

In deze hand leiding leert u het volgende:

* Telemetrie streamen vanuit een IoT Central-toepassing met *doorlopende gegevens export*.
* Maak een Azure Databricks omgeving om telemetrie van apparaten te analyseren en af te zetten.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze hand leiding wilt uitvoeren, hebt u een actief Azure-abonnement nodig.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

### <a name="iot-central-application"></a>IoT Central-toepassing

Maak een IoT Central-toepassing op de website van [Azure IOT Central Application Manager](https://aka.ms/iotcentral) met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Prijs plan | Standard |
| Toepassingsjabloon | Analyses in de Store-voor waarde |
| De naam van de toepassing | Accepteer de standaard waarde of kies uw eigen naam |
| URL | Accepteer de standaard waarde of kies uw eigen unieke URL-voor voegsel |
| Directory | Uw Azure Active Directory-Tenant |
| Azure-abonnement | Uw Azure-abonnement |
| Region | Uw dichtstbijzijnde regio |

In de voor beelden en scherm afbeeldingen in dit artikel wordt gebruikgemaakt van de **Verenigde Staten** regio. Kies een locatie dicht bij u en zorg ervoor dat u alle resources in dezelfde regio maakt.

Deze toepassings sjabloon bevat twee gesimuleerde Thermo staat-apparaten die telemetrie verzenden.

### <a name="resource-group"></a>Resourcegroep

Gebruik de [Azure Portal om een resource groep te maken](https://portal.azure.com/#create/Microsoft.ResourceGroup) met de naam **IoTCentralAnalysis** die de andere resources bevat die u maakt. Maak uw Azure-resources op dezelfde locatie als uw IoT Central-toepassing.

### <a name="event-hubs-namespace"></a>Event Hubs-naamruimte

Gebruik de [Azure Portal om een event hubs naam ruimte te maken](https://portal.azure.com/#create/Microsoft.EventHub) met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Naam    | De naam van de naam ruimte kiezen |
| Prijscategorie | Basic |
| Abonnement | Uw abonnement |
| Resourcegroep | IoTCentralAnalysis |
| Locatie | VS - oost |
| Doorvoereenheden | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks werk ruimte

Gebruik de [Azure Portal om een Azure Databricks service te maken](https://portal.azure.com/#create/Microsoft.Databricks) met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Werkruimtenaam    | De naam van uw werk ruimte kiezen |
| Abonnement | Uw abonnement |
| Resourcegroep | IoTCentralAnalysis |
| Locatie | VS - oost |
| Prijscategorie | Standard |

Wanneer u de vereiste resources hebt gemaakt, ziet uw **IoTCentralAnalysis** -resource groep eruit als de volgende scherm afbeelding:

:::image type="content" source="media/howto-create-custom-analytics/resource-group.png" alt-text="afbeelding van IoT Central analyse resource groep.":::

## <a name="create-an-event-hub"></a>Een Event Hub maken

U kunt een IoT Central-toepassing configureren om voortdurend telemetrie te exporteren naar een Event Hub. In deze sectie maakt u een Event Hub voor het ontvangen van telemetrie van uw IoT Central-toepassing. De Event Hub levert de telemetrie naar uw Stream Analytics-taak voor verwerking.

1. Ga in het Azure Portal naar uw Event Hubs-naam ruimte en selecteer **+ Event hub**.
1. Geef een naam op voor uw Event Hub **centralexport**.
1. Selecteer **centralexport** in de lijst met Event hubs in uw naam ruimte. Kies vervolgens **beleid voor gedeelde toegang**.
1. Selecteer **+ Toevoegen**. Maak een beleid met de naam **SendListen** met de claims **Send** en **listing** .
1. Wanneer het beleid gereed is, selecteert u het in de lijst en kopieert u vervolgens de **verbindings reeks-primaire sleutel** waarde.
1. Noteer deze connection string. u kunt dit later doen wanneer u uw Databricks-notebook configureert om te lezen van de Event Hub.

De naam ruimte van uw Event Hubs ziet eruit als in de volgende scherm afbeelding:

:::image type="content" source="media/howto-create-custom-analytics/event-hubs-namespace.png" alt-text="afbeelding van Event Hubs naam ruimte.":::

## <a name="configure-export-in-iot-central"></a>Exporteren configureren in IoT Central

In deze sectie configureert u de toepassing voor het streamen van telemetrie van de gesimuleerde apparaten naar uw Event Hub.

Ga op de website van [Azure IOT Central Application Manager](https://aka.ms/iotcentral) naar de IOT Central toepassing die u eerder hebt gemaakt. Als u de export wilt configureren, maakt u eerst een doel:

1. Ga naar de pagina voor het **exporteren van gegevens** en selecteer vervolgens **bestemmingen**.
1. Selecteer **+ nieuwe bestemming**.
1. Gebruik de waarden in de volgende tabel om een doel te maken:

    | Instelling | Waarde |
    | ----- | ----- |
    | Doel naam | Telemetrie-Event Hub |
    | Doeltype | Azure Event Hubs |
    | Verbindingsreeks | De Event Hub connection string u eerder een notitie hebt gemaakt |

    De **Event hub** wordt weer gegeven als **centralexport**.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-1.png" alt-text="Scherm opname van gegevens export bestemming":::

1. Selecteer **Opslaan**.

De export definitie maken:

1. Ga naar de pagina voor het **exporteren van gegevens** en selecteer **+ Nieuw exporteren**.

1. Gebruik de waarden in de volgende tabel om de export te configureren:

    | Instelling | Waarde |
    | ------- | ----- |
    | Export naam | Event hub exporteren |
    | Ingeschakeld | Uit |
    | Type gegevens dat moet worden geëxporteerd | Telemetrie |
    | Bestemmingen | Selecteer **+ bestemming** en selecteer vervolgens **telemetrie Event hub** |

1. Selecteer **Opslaan**.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-2.png" alt-text="Scherm opname van definitie van gegevens export":::

Wacht totdat de export status **in orde** is op de pagina voor het **exporteren van gegevens** voordat u doorgaat.

## <a name="configure-databricks-workspace"></a>Databricks-werk ruimte configureren

Ga in het Azure Portal naar uw Azure Databricks-service en selecteer **werk ruimte starten**. Er wordt een nieuw tabblad geopend in uw browser en u wordt aangemeld bij uw werk ruimte.

### <a name="create-a-cluster"></a>Een cluster maken

Selecteer op de pagina **Azure Databricks** , onder de lijst met algemene taken, de optie **Nieuw cluster**.

Gebruik de informatie in de volgende tabel om uw cluster te maken:

| Instelling | Waarde |
| ------- | ----- |
| Clusternaam | centralanalysis |
| Cluster modus | Standard |
| Databricks Runtime versie | 5,5 LTS (scala 2,11, Spark 2.4.5) |
| Python-versie | 3 |
| Automatisch schalen inschakelen | Nee |
| Beëindigen na minuten van inactiviteit | 30 |
| Type werk nemer | Standard_DS3_v2 |
| IT | 1 |
| Type stuur programma | Gelijk aan werk nemer |

Het maken van een cluster kan enkele minuten duren. wacht totdat het maken van het cluster is voltooid voordat u doorgaat.

### <a name="install-libraries"></a>Bibliotheken installeren

Wacht op de pagina **clusters** totdat de cluster status **actief** is.

De volgende stappen laten zien hoe u de bibliotheek kunt importeren die nodig is voor uw voor beeld in het cluster:

1. Wacht op de pagina **clusters** totdat de status van het interactieve cluster **centralanalysis** wordt **uitgevoerd**.

1. Selecteer het cluster en klik vervolgens op het tabblad **tape wisselaars** .

1. Kies op het tabblad **tape wisselaars** de optie **nieuwe installeren**.

1. Kies op de pagina **bibliotheek installeren** de optie **maven** als de bron van de bibliotheek.

1. Voer in het tekstvak **coördinaten** de volgende waarde in: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Kies **installeren** om de bibliotheek op het cluster te installeren.

1. De status van de tape wisselaar is nu **geïnstalleerd**:

:::image type="content" source="media/howto-create-custom-analytics/cluster-libraries.png" alt-text="Scherm opname van de geïnstalleerde bibliotheek.":::

### <a name="import-a-databricks-notebook"></a>Een Databricks-notebook importeren

Gebruik de volgende stappen om een Databricks-notebook te importeren dat de python-code bevat voor het analyseren en visualiseren van uw IoT Central telemetrie:

1. Navigeer naar de pagina **werk ruimte** in uw Databricks-omgeving. Selecteer de vervolg keuzelijst naast de naam van uw account en kies vervolgens **importeren**.

1. Kies uit een URL om te importeren en voer het volgende adres in: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Kies **importeren** om het notitie blok te importeren.

1. Selecteer de **werk ruimte** om het geïmporteerde notitie blok weer te geven:

:::image type="content" source="media/howto-create-custom-analytics/import-notebook.png" alt-text="Scherm opname van het geïmporteerde notitie blok.":::

5. Bewerk de code in de eerste python-cel om de Event Hubs connection string die u eerder hebt opgeslagen, toe te voegen:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Analyse uitvoeren

Als u de analyse wilt uitvoeren, moet u het notitie blok aan het cluster koppelen:

1. Selecteer **losgekoppeld** en selecteer vervolgens het **centralanalysis** -cluster.
1. Als het cluster niet wordt uitgevoerd, start u het.
1. Selecteer de knop uitvoeren om het notitie blok te starten.

Er wordt mogelijk een fout in de laatste cel weer geven. Als dit het geval is, controleert u of de vorige cellen actief zijn, wacht u enkele gegevens naar opslag en voert u de laatste cel opnieuw uit.

### <a name="view-smoothed-data"></a>Vloeiende gegevens weer geven

Schuif in het notitie blok omlaag naar cel 14 om een grafiek van de gemiddelde vochtigheids graad op apparaattype te bekijken. Dit diagram blijft voortdurend bijgewerkt als streaming-telemetrie arriveert:

:::image type="content" source="media/howto-create-custom-analytics/telemetry-plot.png" alt-text="Scherm afbeelding van vloeiende telemetrie-grafieken.":::

U kunt de grootte van de grafiek in het notitie blok wijzigen.

### <a name="view-box-plots"></a>Vakken in het vak weer geven

Schuif in het notitie blok omlaag naar cel 20 om de [vakken](https://en.wikipedia.org/wiki/Box_plot)weer te geven. De vakken zijn gebaseerd op statische gegevens zodat u ze kunt bijwerken. u moet de cel opnieuw uitvoeren:

:::image type="content" source="media/howto-create-custom-analytics/box-plots.png" alt-text="Scherm afbeelding van Boxplot.":::

U kunt de grootte van de grafieken in het notitie blok wijzigen.

## <a name="tidy-up"></a>Opruimen

Als u wilt opruimen na deze procedure en overbodige kosten wilt voor komen, verwijdert u de resource groep **IoTCentralAnalysis** in de Azure Portal.

U kunt de IoT Central toepassing verwijderen van de **beheer** pagina binnen de toepassing.

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u het volgende geleerd:

* Telemetrie streamen vanuit een IoT Central-toepassing met *doorlopende gegevens export*.
* Maak een Azure Databricks omgeving om telemetriegegevens te analyseren en af te zetten.

Nu u weet hoe u aangepaste analyses maakt, is de voorgestelde volgende stap informatie over het [visualiseren en analyseren van uw Azure IOT Central-gegevens in een Power bi dash board](howto-connect-powerbi.md).