---
title: 'Zelfstudie: een nieuw type gatewayapparaat definiëren in Azure IoT Central | Microsoft Docs'
description: Deze zelfstudie laat zien hoe u als bouwer een nieuw type gatewayapparaat kunt definiëren in uw Azure IoT Central-toepassing.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: f95df6fb74071dc16814f11fb48521f984c89778
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995519"
---
# <a name="tutorial---define-a-new-iot-gateway-device-type-in-your-azure-iot-central-application"></a>Zelfstudie: een nieuw type IoT-gatewayapparaat definiëren in uw Azure IoT Central-toepassing

*Dit artikel is van toepassing op oplossingenbouwers en apparaatontwikkelaars.*

Deze zelfstudie laat zien hoe u, als bouwer van oplossingen, een sjabloon voor een gatewayapparaat kunt gebruiken om een nieuw type gatewayapparaat te definiëren in uw IoT Central-toepassing. U configureert vervolgens een of meer downstreamapparaten die via een gatewayapparaat verbinding maken met uw IoT Central-toepassing. 

In deze zelfstudie maakt u een sjabloon voor een **Smart Building**-gatewayapparaat. Een **Smart Building**-gatewayapparaat heeft relaties met andere downstreamapparaten.

![Diagram van de relatie tussen een gatewayapparaat en downstreamapparaten](./media/tutorial-define-gateway-device-type/gatewaypattern.png)

Een gatewayapparaat stelt downstreamapparaten in staat te communiceren met uw IoT Central- toepassing, en kan daarnaast het volgende doen:

* Eigen telemetrie verzenden, zoals temperatuur.
* Reageren op updates van schrijfbare eigenschappen die zijn gemaakt via een operator. Een operator kan bijvoorbeeld het verzendinterval van de telemetrie wijzigen.
* Reageren op opdrachten, zoals het opnieuw opstarten van het apparaat.

> [!div class="checklist"]
> Sjablonen voor downstreamapparaten maken, een gateway-apparaatsjabloon maken, de apparaatsjabloon publiceren en de gesimuleerde apparaten maken

## <a name="prerequisites"></a>Vereisten

U moet [Een Azure IoT Central-toepassing maken](./quick-deploy-iot-central.md) om deze zelfstudie te voltooien.

## <a name="create-downstream-device-templates"></a>Sjablonen voor downstreamapparaten maken

In deze zelfstudie wordt gebruikgemaakt van apparaatsjablonen voor een **S1 Sensor**-apparaat en een **RS40 Occupancy Sensor**-apparaat voor het genereren van gesimuleerde downstreamapparaten.

Een apparaatsjabloon maken voor een **S1 Sensor**-apparaat:

1. Selecteer in het linkerdeelvenster de optie **Apparaatsjablonen**. Selecteer vervolgens **+** om te beginnen met toevoegen van de sjabloon.

1. Schuif omlaag totdat u de tegel voor het **S1 Sensor**-apparaat ziet. Selecteer de tegel en selecteer vervolgens **Volgende: Aanpassen**.

1. Selecteer op de pagina **Controleren** de optie **Maken** om de apparaatsjabloon toe te voegen aan de toepassing. 

Een apparaatsjabloon maken voor een **_RS40 Occupancy Sensor_*-apparaat:

1. Selecteer in het linkerdeelvenster de optie **Apparaatsjablonen**. Selecteer vervolgens **+** om te beginnen met toevoegen van de sjabloon.

1. Schuif omlaag totdat u de tegel voor het **_RS40 Occupancy Sensor_*-apparaat ziet. Selecteer de tegel en selecteer vervolgens **Volgende: Aanpassen**.

1. Selecteer op de pagina **Controleren** de optie **Maken** om de apparaatsjabloon toe te voegen aan de toepassing. 

U hebt nu apparaatsjablonen voor de twee typen downstreamapparaten:

![Apparaatsjablonen voor downstreamapparaten](./media/tutorial-define-gateway-device-type/downstream-device-types.png)


## <a name="create-a-gateway-device-template"></a>Een gateway-apparaatsjabloon maken

In deze zelfstudie maakt u een nieuwe apparaatsjabloon voor een gatewayapparaat. U gebruikt deze sjabloon later om een gesimuleerd gatewayapparaat in de toepassing te maken.

Een nieuwe sjabloon voor gatewayapparaten toevoegen aan de toepassing:

1. Selecteer in het linkerdeelvenster de optie **Apparaatsjablonen**. Selecteer vervolgens **+** om te beginnen met toevoegen van de sjabloon.

1. Selecteer op de pagina **Sjabloontype selecteren** de tegel **IoT-apparaat**, en selecteer vervolgens **Volgende: Aanpassen**.

1. Schakel op de pagina **Apparaat aanpassen** het selectievakje **Gatewayapparaat** in.

1. Selecteer op de pagina **Beoordelen** de optie **Maken**. 

1. Voer **Smart Building-gatewayapparaat** in als de sjabloonnaam.

1. Selecteer op de pagina **Een mogelijkheidsprofiel maken** de tegel **Aangepast**.

1. Selecteer **+** om een interface toe te voegen.  Kies de standaardinterface **Apparaatgegevens**.

### <a name="add-relationships"></a>Relaties toevoegen

Vervolgens voegt u relaties toe aan de sjablonen voor de sjablonen voor downstreamapparaten:

1. Selecteer in de sjabloon **Smart Building-gatewayapparaat** de optie **Relaties**.

1. Selecteer **+ Relatie toevoegen**. Voer **Omgevingssensor** in als weergavenaam, en selecteer **S1 Sensor** als doel.

1. Selecteer opnieuw **+ Relatie toevoegen**. Voer **Aanwezigheidssensor** in als weergavenaam, en selecteer **RS40 Occupancy Sensor** als doel.

1. Selecteer **Opslaan**.

![De sjabloon Smart Building-gatewayapparaat, met Relaties weergegeven](./media/tutorial-define-gateway-device-type/relationships.png)

### <a name="add-cloud-properties"></a>Cloudeigenschappen toevoegen

Een sjabloon voor een gatewayapparaat kan cloudeigenschappen bevatten. Cloudeigenschappen bestaan alleen in de IoT Central-toepassing en worden nooit verzonden naar of ontvangen van een apparaat.

Cloudeigenschappen toevoegen aan de sjabloon **Smart Building-gatewayapparaat**:

1. Selecteer in de sjabloon **Smart Building-gatewayapparaat** de optie **Cloudeigenschappen**.

1.  Gebruik de informatie in de volgende tabel om twee cloudeigenschappen toe te voegen aan de sjabloon voor het gatewayapparaat.

    | Weergavenaam      | Semantisch type | Schema |
    | ----------------- | ------------- | ------ |
    | Laatste servicedatum | Geen          | Date   |
    | Naam van klant     | Geen          | Tekenreeks |

2. Selecteer **Opslaan**.

### <a name="create-views"></a>Weergaven maken

Als bouwer kunt u de toepassing aanpassen om relevante informatie over het omgevingssensorapparaat weer te geven aan een operator. Dankzij uw aanpassingen kan de operator de omgevingssensorapparaten beheren die zijn verbonden met de toepassing. U kunt twee soorten weergaven maken die een operator kan gebruiken om met apparaten te werken:

* Formulieren voor het weergeven en bewerken van apparaat- en cloudeigenschappen.
* Dashboards om apparaten te visualiseren.

De standaardweergaven genereren voor de sjabloon **Smart Building-gatewayapparaat**:

1. Selecteer in de sjabloon **Smart Building-gatewayapparaat** de optie **Weergaven**.

1. Selecteer de tegel **Standaardweergaven genereren** en zorg ervoor dat alle opties zijn geselecteerd.

1. Selecteer **Standaarddashboardweergave(n) genereren**.

## <a name="publish-the-device-template"></a>De apparaatsjabloon publiceren

Voordat u een gesimuleerd gatewayapparaat kunt maken of verbinding kunt maken met een echt gatewayapparaat, moet u de apparaatsjabloon publiceren.

De sjabloon van het gatewayapparaat publiceren:

1. Selecteer de sjabloon **Smart Building-gatewayapparaat** op de pagina **Apparaatsjablonen**.

2. Selecteer **Publiceren**.

3. Kies in het dialoogvenster **Een apparaatsjabloon publiceren** de optie **Publiceren**.

Nadat een apparaatsjabloon is gepubliceerd, is deze zichtbaar op de pagina **Apparaten** en voor de operator. U kunt in een gepubliceerde apparaatsjabloon geen apparaatmodel bewerken zonder een nieuwe versie te maken. Maar u kunt de cloudeigenschappen, aanpassingen en weergaven in een gepubliceerde apparaatsjabloon wel bijwerken. Deze updates zorgen er niet voor dat een nieuwe versie wordt gemaakt. Nadat u wijzigingen hebt aangebracht, selecteert u **Publiceren** om de wijzigingen naar uw operator te pushen.

## <a name="create-the-simulated-devices"></a>De gesimuleerde apparaten maken

In deze zelfstudie wordt gebruikgemaakt van gesimuleerde downstreamapparaten en een gesimuleerd gatewayapparaat.

Een gesimuleerd gatewayapparaat maken:

1. Selecteer op de pagina **Apparaten** het **Smart Building-gatewayapparaat** in de lijst met apparaatsjablonen.

1. Selecteer **+** om een nieuw apparaat toe te voegen.

1. Laat de gegenereerde **apparaat-id** en **apparaatnaam** ongewijzigd. Zorg ervoor dat de schakeloptie **Gesimuleerd** op **Aan** staat. Selecteer **Maken**.

Een gesimuleerd downstreamapparaat maken:

1. Selecteer op de pagina **Apparaten** de **RS40 Occupancy Sensor** in de lijst met apparaatsjablonen.

1. Selecteer **+** om een nieuw apparaat toe te voegen.

1. Laat de gegenereerde **apparaat-id** en **apparaatnaam** ongewijzigd. Zorg ervoor dat de schakeloptie **Gesimuleerd** op **Aan** staat. Selecteer **Maken**.

1. Selecteer op de pagina **Apparaten** de **S1 Sensor** in de lijst met apparaatsjablonen.

1. Selecteer **+** om een nieuw apparaat toe te voegen.

1. Laat de gegenereerde **apparaat-id** en **apparaatnaam** ongewijzigd. Zorg ervoor dat de schakeloptie **Gesimuleerd** op **Aan** staat. Selecteer **Maken**.

![Gesimuleerde apparaten in uw toepassing](./media/tutorial-define-gateway-device-type/simulated-devices.png)

### <a name="add-downstream-device-relationships-to-a-gateway-device"></a>Relaties tussen gesimuleerde apparaten en een gatewayapparaat toevoegen

Nu u de gesimuleerde apparaten in uw toepassing hebt, kunt u relaties tot stand brengen tussen de downstreamapparaten en het gatewayapparaat:

1. Selecteer op de pagina **Apparaten** de **S1 Sensor** in de lijst met apparaatsjablonen, en selecteer vervolgens uw gesimuleerde **S1 Sensor**-apparaat.

1. Selecteer **Verbinding maken met gateway**.

1. Selecteer in het dialoogvenster **Verbinding maken met een gateway** de sjabloon **Smart Building-gatewayapparaat**. Selecteer vervolgens de gesimuleerde instantie die u eerder hebt gemaakt.

1. Selecteer **Toevoegen**.

1. Selecteer op de pagina **Apparaten** de optie **RS40 Occupancy Sensor** in de lijst met apparaatsjablonen. Selecteer vervolgens uw gesimuleerde **RS40 Occupancy Sensor**-apparaat.

1. Selecteer **Verbinding maken met gateway**.

1. Selecteer in het dialoogvenster **Verbinding maken met een gateway** de sjabloon **Smart Building-gatewayapparaat**. Selecteer vervolgens de gesimuleerde instantie die u eerder hebt gemaakt.

1. Selecteer **Toevoegen**.

Beide gesimuleerde downstreamapparaten zijn nu verbonden met het gesimuleerde gatewayapparaat. Als u naar de weergave **Downstreamapparaten** voor uw gatewayapparaat gaat, ziet u de gerelateerde downstreamapparaten:

![Weergave Downstreamapparaten](./media/tutorial-define-gateway-device-type/downstream-device-view.png)

Selecteer een gatewayapparaatsjabloon en gatewayapparaatinstantie, en selecteer **Toevoegen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

* Een nieuwe IoT-gateway maken als een apparaatsjabloon.
* Cloudeigenschappen maken.
* Aanpassingen maken.
* Een visualisatie definiëren voor de telemetrie van het apparaat.
* Relaties toevoegen.
* Uw apparaatsjabloon publiceren.

> [!NOTE]
> Codegeneratie op basis van VS Code wordt momenteel niet ondersteund voor gatewayapparaten die zijn gemodelleerd in IoT Central.

Als apparaatontwikkelaar kunt u hierna het volgende leren:

> [!div class="nextstepaction"]
> [Een Azure IoT Edge-apparaat toevoegen aan uw Azure IoT Central-toepassing](tutorial-add-edge-as-leaf-device.md)
