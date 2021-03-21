---
title: Zelfstudie - Een Azure IoT Edge-apparaat toevoegen aan Azure IoT Central | Microsoft Docs
description: Zelfstudie - Als operator kunt u een Azure IoT Edge-apparaat toevoegen aan uw Azure IoT Central-app
author: rangv
ms.author: rangv
ms.date: 05/29/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
- iot-edge
ms.openlocfilehash: 373d144b4df818a075f0088e9cbf31cb5027e747
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101724877"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Zelfstudie: Een Azure IoT Edge-apparaat toevoegen aan uw Azure IoT Central-toepassing

*Dit artikel is van toepassing op operators, oplossingenbouwers en ontwikkelaars van apparaten.*

In deze zelfstudie wordt getoond hoe u een Azure IoT Edge-apparaat aan uw Azure IoT Central-toepassing kunt toevoegen en deze kunt configureren. In de zelfstudie wordt een door IoT Edge ingeschakelde virtuele Linux-machine (VM) gebruikt om een IoT Edge-apparaat te simuleren. Het IoT Edge-apparaat gebruikt een module die gesimuleerde telemetrie van een omgeving genereert. U kunt de telemetrie bekijken in een dashboard in uw IoT Central-toepassing.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een apparaatsjabloon maken voor een IoT Edge-apparaat
> * Een IoT Edge-apparaat maken in IoT Central
> * Een gesimuleerd IoT Edge-apparaat implementeren op een virtuele Linux-machine

## <a name="prerequisites"></a>Vereisten

Voltooi de quickstart [Een Azure IoT Central-toepassing maken](./quick-deploy-iot-central.md) om een IoT Central-toepassing te maken met behulp van de toepassing **Aangepaste app > Aangepaste sjabloon**.

U hebt een actief Azure-abonnement nodig om de stappen in deze zelfstudie te voltooien.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Download het IoT Edge-manifestbestand vanuit GitHub. Klik met de rechtermuisknop op de volgende koppeling en selecteer **Koppeling opslaan als**: [EnvironmentalSensorManifest.json](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>Sjabloonapparaat maken

In deze sectie maakt u een IoT Central-apparaatsjabloon voor een IoT Edge-apparaat. U importeert een IoT Edge-manifest om aan de slag te gaan en wijzigt vervolgens de sjabloon om telemetriegegevens en weergaven toe te voegen:

### <a name="import-manifest-to-create-template"></a>Manifest importeren om sjabloon te maken

Om een apparaatsjabloon maken van een IoT Edge-manifest te maken:

1. Ga in uw IoT Central-toepassing naar **Apparaatsjablonen** en selecteer **en Nieuwe**.

1. Selecteer op de pagina **Sjabloontype selecteren** de tegel **Azure IoT Edge**. Selecteer vervolgens **Volgende: Aanpassen**.

1. Voer op de pagina **Een Azure IoT Edge-implementatiemanifest uploaden** de *Omgevingssensor van het Edge-apparaat* in als de naam van de sjabloon. Selecteer vervolgens **Bladeren** om de **EnvironmentalSensorManifest.json** te uploaden die u eerder hebt gedownload. Selecteer vervolgens **Volgende: Review**.

1. Selecteer op de pagina **Beoordelen** de optie **Maken**.

1. Selecteer de interface **Beheren** in de module **SimulatedTemperatureSensor** om de twee eigenschappen weer te geven die in het manifest zijn gedefinieerd:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/imported-manifest.png" alt-text="Het apparaatprofiel is gemaakt op basis van IoT Edge-manifest":::

> [!TIP]
> Dit implementatie manifest haalt module-installatie kopieën op uit een Azure Container Registry-opslag plaats waarvoor geen referenties zijn vereist om verbinding te maken. Als u module-installatie kopieën uit een privé-opslag plaats wilt gebruiken, stelt u de container register referenties in het manifest in.

### <a name="add-telemetry-to-manifest"></a>Telemetrie toevoegen aan uw manifest

Een IoT Edge-manifest definieert niet de telemetrie die een module verzendt. U kunt de telemetrie-definities toevoegen aan de sjabloon voor het apparaat in IoT Central. De **SimulatedTemperatureSensor**-module verzendt telemetrie-berichten die eruitzien als de volgende JSON:

```json
{
  "machine": {
    "temperature": 75.0,
    "pressure": 40.2
  },
  "ambient": {
    "temperature": 23.0,
    "humidity": 30.0
  },
  "timeCreated": ""
}
```

Om de telemetrie-definities toe te voegen aan de sjabloon voor het apparaat:

1. Selecteer de interface **Beheren** in het sjabloon **Omgevingssensor van Edge-apparaat**.

1. Selecteer  **en een mogelijkheid toevoegen**. Voer *machine* in als de **Weergavenaam** en zorg ervoor dat het **Capaciteitstype** **Telemetrie** is.

1. Selecteer **Object** als schematype en selecteer **Definiëren**. Voeg op de pagina objectdefinitie *temperatuur* en *druk* toe als kenmerken van het type **Double** en selecteer vervolgens **Toepassen**.

1. Selecteer  **en een mogelijkheid toevoegen**. Voer *omgeving* in als de **Weergavenaam** en zorg ervoor dat het **Capaciteitstype** **Telemetrie** is.

1. Selecteer **Object** als schematype en selecteer **Definiëren**. Voeg op de pagina objectdefinitie *temperatuur* en *vochtigheid* toe als kenmerken van het type **Double** en selecteer vervolgens **Toepassen**.

1. Selecteer  **en een mogelijkheid toevoegen**. Voer *timeCreated* in als de **Weergavenaam** en zorg ervoor dat het **Capaciteitstype** **Telemetrie** is.

1. Selecteer **Datum/tijd** als schematype.

1. Selecteer **Opslaan** om de sjabloon bij te werken.

De interface **Beheren** bevat nu de telemetrietypen **machine**, **omgeving** en **timeCreated**:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/manage-interface.png" alt-text="Interface met telemetrietypen machine en omgeving":::

### <a name="add-views-to-template"></a>Weergaven toevoegen aan sjabloon

De sjabloon voor het apparaat heeft nog geen weergave waarmee een operator de telemetrie van het IoT Edge apparaat kan zien. Een weergave toevoegen aan de sjabloon voor het apparaat:

1. Selecteer de **Weergaven** in het sjabloon **Omgevingssensor van Edge-apparaat**.

1. Selecteer op de pagina **Selecteren om een nieuwe weergave toe te voegen** de tegel  **Het apparaat wordt gevisualiseerd**.

1. Wijzig de weergavenaam in *Telemetrie van het IoT Edge-apparaat weergeven*.

1. Selecteer de telemetrietypen **omgeving** en **machine**. Selecteer vervolgens **Tegel toevoegen**.

1. Selecteer **Opslaan** om de weergave **Telemetrie van IoT Edge-apparaat weergeven** op te slaan.

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png" alt-text="Apparaatsjabloon met telemetrieweergave":::

### <a name="publish-the-template"></a>De sjabloon publiceren

Voordat u een apparaat kunt toevoegen dat gebruikmaakt van de apparaatsjabloon **Omgevingssensor van Edge-apparaat**, moet u het sjabloon publiceren.

Ga naar het sjabloon **Omgevingssensor van Edge-apparaat** en selecteer **Publiceren**. Selecteer **Publiceren** in het deelvenster **Dit apparaatsjabloon op de toepassing publiceren** om het sjabloon te publiceren:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/publish-template.png" alt-text="De sjabloon van het apparaat publiceren":::

## <a name="add-iot-edge-device"></a>IoT Edge-apparaat toevoegen

Nu u het sjabloon **Omgevingssensor van het Edge-apparaat** hebt gepubliceerd, kunt u een apparaat toevoegen aan uw IoT Central-toepassing:

1. Ga in uw IoT Central-toepassing naar de pagina **Apparaten** en selecteer **Omgevingssensor van het Edge-apparaat** in de lijst met beschikbare sjablonen.

1. Selecteer **en Nieuwe** om een nieuw apparaat uit de sjabloon toe te voegen. Selecteer op de pagina **Nieuw apparaat maken** de optie **Maken**.

U hebt nu een nieuw apparaat met de status **Geregistreerd**:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/new-device.png" alt-text="Nieuw, geregistreerd apparaat":::

### <a name="get-the-device-credentials"></a>De apparaatreferenties ophalen

Wanneer u het IoT Edge-apparaat later in deze zelfstudie implementeert, hebt u de referenties nodig waarmee het apparaat verbinding kan maken met uw IoT Central-toepassing. De apparaatreferenties ophalen:

1. Selecteer op de pagina **Apparaat** het apparaat dat u hebt gemaakt.

1. Selecteer **Verbinden**.

1. Noteer het **id-bereik**, de **apparaat-id** en de **Primaire sleutel** op de pagina **Apparaatverbinding**. U gebruikt deze waarden later.

1. Selecteer **Sluiten**.

U bent nu klaar met het configureren van uw IoT Central-toepassing om een IoT Edge-apparaat verbinding te laten maken.

## <a name="deploy-an-iot-edge-device"></a>Een IoT Edge-apparaat implementeren

In deze zelfstudie gebruikt u een met Azure IoT Edge ingeschakelde Linux-VM die is gemaakt in Azure om een IoT Edge-apparaat te simuleren. Als u de virtuele machine met IoT Edge ingeschakeld wilt maken in uw Azure-abonnement, klikt u op:

[![De knop Implementeren naar Azure voor iotedge-vm-Deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

Op de pagina **Aangepaste implementatie**:

1. Selecteer uw Azure-abonnement.

1. Selecteer **Nieuwe maken** om een nieuwe resourcegroep te maken met de naam *central-edge-rg*.

1. Kies een regio dicht bij u in de buurt.

1. Voeg een uniek **DNS-labelvoorvoegsel** toe, zoals *contoso-central-edge*.

1. Kies een gebruikersnaam voor de beheerder voor de virtuele machine.

1. Voer *temp* in als de verbindingsreeks. Later configureert u het apparaat om verbinding te maken via DPM.

1. Accepteer de standaardwaarden voor de VM-grootte, de Ubuntu-versie en de locatie.

1. Selecteer **Wachtwoord** als het verificatietype.

1. Voer een wachtwoord in voor de virtuele machine.

1. Selecteer vervolgens **Controleren en maken**.

1. Controleer uw keuzes en selecteer vervolgens **Maken**:

    :::image type="content" source="media/tutorial-add-edge-as-leaf-device/vm-deployment.png" alt-text="Een IoT Edge-VM maken":::

Het uitvoeren van implementatie duurt enkele minuten. Wanneer de implementatie is voltooid, gaat u naar de resourcegroep **central-edge-rg** in de Azure-portal.

### <a name="configure-the-iot-edge-vm"></a>De IoT Edge-VM configureren

Als u IoT Edge in de VM wilt configureren om DPM te gebruiken om uw IoT Central-toepassing te registreren en er verbinding mee te maken:

1. Selecteer in de resourcegroep **contoso-edge-rg** het exemplaar van de virtuele machine.

1. Selecteer in **Seriële console** in het gedeelte **Ondersteuning en probleemoplossing**. Als u wordt gevraagd om de diagnostische gegevens over opstarten te configureren, volgt u de instructies in de portal.

1. Druk op **Invoeren** om de prompt `login:` weer te geven. Voer uw gebruikersnaam en wachtwoord in om u aan te melden.

1. Voer de volgende opdracht uit om de versie van IoT Edge-runtime te controleren. Op het moment van schrijven is de versie 1.0.9.1:

    ```bash
    sudo iotedge --version
    ```

1. Gebruik de `nano`-editor om het IoT Edge-bestand config.yaml te openen:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Schuif omlaag totdat u `# Manual provisioning configuration` ziet. Commentaar bij de volgende drie regels, zoals wordt weergegeven in het volgende fragment:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. Schuif omlaag totdat u `# DPS symmetric key provisioning configuration` ziet. Verwijder de opmerking bij de volgende acht regels, zoals wordt weergegeven in het volgende fragment:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

    > [!TIP]
    > Zorg ervoor dat er geen ruimte meer is vóór `provisioning:`

1. Vervang `{scope_id}` door het **id-bereik** dat u eerder heeft opgeschreven.

1. Vervang `{registration_id}` door de **apparaat-id** die u eerder hebt opgeschreven.

1. Vervang `{symmetric_key}` door het **primaire sleutel** die u eerder heeft opgeschreven.

1. Sla de wijzigingen op (**CTRL-O**) en sluit de `nano`-editor af (**CTRL-X**).

1. Voer de volgende opdracht uit om de IoT Edge-daemon opnieuw te starten:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Voer de volgende opdracht uit om de status van de IoT Edge-modules te controleren:

    ```bash
    iotedge list
    ```

    Hieronder ziet u een voorbeeld van uitvoer van de modules die worden uitgevoerd:

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

    > [!TIP]
    > Mogelijk moet u wachten totdat alle modules worden uitgevoerd.

## <a name="view-the-telemetry"></a>De telemetrie weergeven

Het gesimuleerde IoT Edge-apparaat wordt nu uitgevoerd in de virtuele machine. In uw IoT Central-toepassing is de apparaatstatus nu **Ingericht** op de pagina **Apparaten**:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/provisioned-device.png" alt-text="Ingericht IoT Edge-apparaat":::

U kunt de telemetrie van het apparaat zien op de pagina **Telemetrie van IoT Edge-apparaat weergeven**:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png" alt-text="Apparaattelemetrie":::

Op de pagina **Modules** wordt de status van de IoT Edge-modules op het apparaat weergegeven:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/edge-module-status.png" alt-text="Status van apparaatmodule":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om verder te gaan met de IoT Edge-VM, kunt u de resources die u in deze zelfstudie hebt gebruikt, blijven behouden en opnieuw gebruiken. Als dat niet het geval is, kunt u de resources die u in deze zelfstudie hebt gemaakt, verwijderen om extra kosten te voorkomen:

* Als u de IoT Edge-VM en de bijbehorende resources wilt verwijderen, verwijdert u de resourcegroep **contoso-edge-rg** in de Azure-portal.
* Als u de IoT Central-toepassing wilt verwijderen, gaat u naar de pagina **Uw toepassing** in het gedeelte **Beheer** van de toepassing en selecteert u **Verwijderen**.

Als ontwikkelaar van oplossingen of als operator, hebt u nu geleerd hoe u IoT Edge-apparaten in IoT Central kunt gebruiken en beheren. Een voorgestelde volgende stap is:

> [!div class="nextstepaction"]
> [Apparaatgroepen gebruiken om apparaattelemetrie te analyseren](./tutorial-use-device-groups.md)

## <a name="next-steps"></a>Volgende stappen

Als ontwikkelaar van apparaten, hebt u nu geleerd hoe u IoT Edge-apparaten in IoT Central kunt gebruiken en beheren. Een voorgestelde volgende stap is om deze artikelen te lezen:

> [!div class="nextstepaction"]
> [IoT Edge-modules ontwikkelen](../../iot-edge/tutorial-develop-for-linux.md)