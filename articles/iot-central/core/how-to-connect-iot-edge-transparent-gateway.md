---
title: Een IoT Edge transparante gateway verbinden met een Azure IoT Central-toepassing
description: Apparaten verbinden via een IoT Edge transparante gateway naar een IoT Central toepassing
author: dominicbetts
ms.author: dobett
ms.date: 03/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: bdfb5f65106f3f8843b4aa52b752f5e563ab03f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620045"
---
# <a name="how-to-connect-devices-through-an-iot-edge-transparent-gateway"></a>Apparaten verbinden via een IoT Edge transparante gateway

Een IoT Edge apparaat kan fungeren als een gateway die een verbinding biedt tussen andere apparaten in een lokaal netwerk en uw IoT Central toepassing. U gebruikt een gateway wanneer het apparaat niet rechtstreeks toegang heeft tot uw IoT Central-toepassing.

IoT Edge ondersteunt de [ *transparante* gateway patronen en *vertalingen*](../../iot-edge/iot-edge-as-gateway.md). In dit artikel wordt een overzicht gegeven van hoe u het transparante gateway patroon implementeert. In dit patroon geeft de gateway berichten van het downstream-apparaat door aan het IoT Hub-eind punt in uw IoT Central toepassing.

In dit artikel worden virtuele machines gebruikt voor het hosten van het downstream-apparaat en de gateway. In een echt scenario worden het downstream-apparaat en de gateway uitgevoerd op fysieke apparaten op uw lokale netwerk.

## <a name="prerequisites"></a>Vereisten

U hebt een actief Azure-abonnement nodig om de stappen in deze zelfstudie te voltooien.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Voltooi de quickstart [Een Azure IoT Central-toepassing maken](./quick-deploy-iot-central.md) om een IoT Central-toepassing te maken met behulp van de toepassing **Aangepaste app > Aangepaste sjabloon**.

Als u de stappen in dit artikel wilt volgen, moet u de volgende bestanden downloaden naar uw computer:

- [Thermo staat-apparaat model](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-1.json)
- [Transparant gateway-manifest](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/EdgeTransparentGatewayManifest.json)

## <a name="add-device-templates"></a>Device-sjablonen toevoegen

Op zowel de downstream-apparaten als het gateway apparaat zijn apparaatprofielen in IoT Central vereist. Met IoT Central kunt u de relatie tussen uw downstream-apparaten en uw gateway model leren zodat u ze kunt weer geven en beheren nadat ze zijn verbonden.

Als u een sjabloon voor een downstream-apparaat wilt maken, maakt u een sjabloon voor standaard apparaten waarmee de mogelijkheden van uw apparaat worden gemodelleerd. In het voor beeld in dit artikel wordt het Thermo model weer gegeven.

Een apparaatprofiel maken voor een downstream-apparaat:

1. Maak een sjabloon voor het apparaat en kies **IOT-apparaat** als het sjabloon type.

1. Voer op de pagina **aanpassen** van de wizard een naam in, zoals *Thermo* staat voor de sjabloon voor het apparaat.

1. Nadat u de sjabloon voor het apparaat hebt gemaakt, selecteert u **een model importeren**. Selecteer een model zoals de *thermostat-1.jsvoor* het bestand dat u eerder hebt gedownload.

1. Als u een aantal standaard weergaven voor de Thermo staat wilt genereren, selecteert u weer gaven en kiest u **Standaard weergaven genereren**.

1. De sjabloon van het apparaat publiceren.

Een apparaatprofiel maken voor een transparante IoT Edge gateway:

1. Maak een sjabloon voor het apparaat en kies **Azure IOT Edge** als sjabloon type.

1. Voer op de pagina **aanpassen** van de wizard een naam in, zoals de *rand gateway* voor de sjabloon.

1. Op de pagina **aanpassen** van de wizard controleert u het **gateway apparaat met downstream-apparaten**.

1. Selecteer op de pagina **aanpassen** van de wizard de optie **Bladeren**. Upload de *EdgeTransparentGatewayManifest.jsvoor* het bestand dat u eerder hebt gedownload.

1. Voeg een vermelding toe aan **relaties** met de sjabloon voor het downstream-apparaat.

De volgende scherm afbeelding toont de pagina **relaties** voor een IOT Edge gateway apparaat dat downstream-apparaten heeft die gebruikmaken van de sjabloon van het **Thermo** -apparaat:

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/device-template-relationship.png" alt-text="Scherm opname van de sjabloon relatie IoT Edge gateway apparaat met een sjabloon voor het downstream-apparaat van een thermo staat.":::

In de vorige scherm afbeelding ziet u een IoT Edge gateway-apparaat sjabloon waarvoor geen modules zijn gedefinieerd. Een transparante gateway heeft geen modules nodig omdat de IoT Edge runtime berichten van de downstream-apparaten doorstuurt naar IoT Central. Als de gateway zelf telemetrie moet verzenden, eigenschappen moet synchroniseren of opdrachten kunnen verwerken, kunt u deze mogelijkheden definiëren in het standaard onderdeel of in een module.

Voeg de vereiste Cloud eigenschappen en-weer gaven toe voordat u de gateway-en downstream-Apparaatinstellingen publiceert.

## <a name="add-the-devices"></a>De apparaten toevoegen

Wanneer u de apparaten toevoegt aan uw IoT Central-toepassing, kunt u de relatie tussen de downstream-apparaten en de transparante gateway definiëren.

De apparaten toevoegen:

1. Navigeer naar de pagina apparaten in uw IoT Central-toepassing.

1. Een exemplaar van de transparante gateway toevoegen IoT Edge apparaat. In dit artikel is de apparaat-ID van de gateway `edgegateway` .

1. Voeg een of meer exemplaren van het downstream-apparaat toe. In dit artikel zijn de downstream-apparaten Thermo Staten met Id's `thermostat1` en `thermostat2` .

1. Selecteer elk downstream-apparaat in de lijst met apparaten en selecteer **koppelen aan gateway**.

Op de volgende scherm afbeelding ziet u de lijst met apparaten die zijn gekoppeld aan een gateway op de pagina **downstream-apparaten** :

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-devices.png" alt-text="Scherm opname van de lijst met downstream-apparaten die zijn verbonden met een transparante gateway.":::

In een transparante gateway maken de downstream-apparaten verbinding met de gateway zelf, niet naar een aangepaste module die wordt gehost door de gateway.

Voordat u de apparaten implementeert, hebt u het volgende nodig:

- **Id-bereik** van uw IOT Central-toepassing.
- **Apparaat-ID-** waarden voor de gateway en downstream-apparaten.
- **Primaire sleutel** waarden voor de gateway en downstream-apparaten.

Als u deze waarden wilt vinden, gaat u naar elk apparaat in de lijst met apparaten en selecteert u **verbinding maken**. Noteer deze waarden voordat u doorgaat.

## <a name="deploy-the-gateway-and-devices"></a>De gateway en apparaten implementeren

De volgende stappen laten zien hoe u de gateway-en downstream-apparaten kunt implementeren op Azure virtual machines, zodat u dit scenario kunt uitproberen. In een echt scenario worden het downstream-apparaat en de gateway uitgevoerd op fysieke apparaten op uw lokale netwerk.

Als u het transparante Gateway scenario wilt uitproberen, selecteert u de volgende knop om twee virtuele Linux-machines te implementeren. Eén virtuele machine is een transparante IoT Edge gateway, de andere is een downstream-apparaat dat een thermo staat simuleert:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

Als de twee virtuele machines zijn geïmplementeerd en worden uitgevoerd, controleert u of het IoT Edge gateway apparaat wordt uitgevoerd op de `edgegateway` virtuele machine:

1. Ga naar de pagina **apparaten** in uw IOT Central-toepassing. Als het IoT Edge gateway apparaat is verbonden met IoT Central, wordt de status ervan **ingericht**.

1. Open het IoT Edge gateway apparaat en controleer de status van de modules op de pagina **modules** . Als de runtime van IoT Edge is gestart, wordt de status van de modules **$edgeAgent** en **$edgeHub** **uitgevoerd**:

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/iot-edge-runtime.png" alt-text="Scherm opname van de modules $edgeAgent en $edgeHub die worden uitgevoerd op de IoT Edge gateway.":::

> [!TIP]
> Mogelijk moet u enkele minuten wachten terwijl de virtuele machine wordt gestart en het apparaat is ingericht in uw IoT Central-toepassing.

## <a name="configure-the-gateway"></a>De gateway configureren

Om uw IoT Edge-apparaat als transparante gateway te laten functioneren, heeft het een aantal certificaten nodig om zijn identiteit aan alle downstream-apparaten te bewijzen. In dit artikel worden demo certificaten gebruikt. Gebruik certificaten van uw certificerings instantie in een productie omgeving.

De demo certificaten genereren en installeren op uw gateway apparaat:

1. Gebruik SSH om verbinding te maken en u aan te melden op uw virtuele machine met het gateway apparaat.

1. Voer de volgende opdrachten uit om de IoT Edge opslagplaats te klonen en uw demo certificaten te genereren:

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    Nadat u de vorige opdrachten hebt uitgevoerd, zijn de volgende bestanden gereed voor gebruik in de volgende stappen:

    - *~/certs/certs/Azure-IOT-test-only.root.ca.cert.pem* : het basis-CA-certificaat dat wordt gebruikt om alle andere demo certificaten te maken voor het testen van een IOT Edge scenario.
    - *~/certs/certs/IOT-Edge-Device-mycacert-Full-chain.cert.pem* : een CA-certificaat van het apparaat waarnaar wordt verwezen vanuit het bestand *config. yaml* . In een gateway scenario is dit CA-certificaat de manier waarop het IoT Edge-apparaat zijn identiteit controleert op downstream-apparaten.
    - *~/certs/private/IOT-Edge-Device-mycacert.key.pem* : de persoonlijke sleutel die is gekoppeld aan het CA-certificaat van het apparaat.

    Zie [demo certificaten maken om IOT Edge apparaatfuncties te testen](../../iot-edge/how-to-create-test-certificates.md)voor meer informatie over deze demo certificaten.

1. Open het bestand *config. yaml* in een tekst editor. Bijvoorbeeld:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Zoek de `Certificate settings` instellingen. Verwijder de opmerking en wijzig de certificaat instellingen als volgt:

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    In het voor beeld dat hierboven wordt weer gegeven, wordt ervan uitgegaan dat u bent aangemeld als **AzureUser** en een CA-certificaat hebt gemaakt met de naam ' mycacert '.

1. Sla de wijzigingen op en start de IoT Edge runtime opnieuw:

    ```bash
    sudo systemctl restart iotedge
    ```

Als de IoT Edge runtime wordt gestart nadat de wijzigingen zijn aangebracht, wordt de status van de modules **$edgeAgent** en **$EdgeHub** gewijzigd in **wordt uitgevoerd** op de pagina **modules** voor uw gateway-apparaat in IOT Central.

Als de runtime niet wordt gestart, controleert u de wijzigingen die u hebt aangebracht in *config. yaml* en raadpleegt u [problemen met uw IOT edge apparaat oplossen](../../iot-edge/troubleshoot.md).

Uw transparante gateway is nu geconfigureerd en gereed om het door sturen van telemetrie vanaf downstream-apparaten te starten.

## <a name="provision-a-downstream-device"></a>Een downstream-apparaat inrichten

Momenteel kan IoT Edge niet automatisch een downstream-apparaat inrichten op uw IoT Central toepassing. De volgende stappen laten zien hoe u het apparaat kunt inrichten `thermostat1` . Als u deze stappen wilt uitvoeren, hebt u een omgeving met python 3,5 (of hoger) geïnstalleerd en hebt u een Internet verbinding nodig. De [Azure Cloud shell](https://shell.azure.com/) heeft python 3,5 vooraf geïnstalleerd:

1. Voer de volgende opdracht uit om de `azure.iot.device` module te installeren:

    ```bash
    pip install azure.iot.device
    ```

1. Voer de volgende opdracht uit om het python-script te downloaden dat het inrichten doet:

    ```bash
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/provision_device.py
    ```

1. Als u het `thermostat1` downstream-apparaat in uw IOT Central-toepassing wilt inrichten, voert u de volgende opdrachten uit, vervangt u `{your application id scope}` en `{your device primary key}`  :

    ```bash
    export IOTHUB_DEVICE_DPS_DEVICE_ID=thermostat1
    export IOTHUB_DEVICE_DPS_ID_SCOPE={your application id scope}
    export IOTHUB_DEVICE_DPS_DEVICE_KEY={your device primary key}
    python provision_device.py
    ```

Controleer in uw IoT Central-toepassing of de **Apparaatstatus** voor het thermostat1-apparaat nu is **ingericht**. 

## <a name="configure-a-downstream-device"></a>Een downstream-apparaat configureren

In de vorige sectie hebt u de `edgegateway` virtuele machine geconfigureerd met de demo certificaten, zodat deze als gateway kan worden uitgevoerd. De `leafdevice` virtuele machine is gereed voor het installeren van een thermo staat Simulator die gebruikmaakt van de gateway om verbinding te maken met IOT Central.

De `leafdevice` virtuele machine heeft een kopie nodig van het basis-CA-certificaat dat u op de `edgegateway` virtuele machine hebt gemaakt. Kopieer het */Home/AzureUser/certs/certs/Azure-IOT-test-only.root.ca.cert.pem* -bestand van de `edgegateway` virtuele machine naar de basismap van de `leafdevice` virtuele machine. U kunt de **SCP** opdracht gebruiken om bestanden te kopiëren van en naar een virtuele Linux-machine.

Zie [de gateway verbinding testen](../../iot-edge/how-to-connect-downstream-device.md#test-the-gateway-connection)voor meer informatie over het controleren van de verbinding van het downstream-apparaat met de gateway.

De Thermo staat Simulator op de virtuele machine uit te voeren `leafdevice` :

1. Down load het python-voor beeld naar uw basismap:

    ```bash
    cd ~
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/simple_thermostat.py
    ```

1. Installeer de python-module van het Azure IoT-apparaat:

    ```bash
    sudo apt update
    sudo apt install python3-pip
    pip3 install azure.iot.device
    ```

1. Stel de omgevingsvariabelen in om het voorbeeld te configureren. Vervang door `{your device shared key}` de primaire sleutel van de `thermostat1` u eerder een notitie hebt gemaakt. Deze variabelen nemen de naam van de virtuele machine van de gateway `edgegateway` en de id van het Thermo staat-apparaat `thermostat1` :

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    export IOTHUB_DEVICE_CONNECTION_STRING="HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}"
    export IOTEDGE_ROOT_CA_CERT_PATH=~/azure-iot-test-only.root.ca.cert.pem
    ```

    U ziet hoe de connection string de naam van het gateway-apparaat gebruikt en niet de naam van een IoT-hub.

1. Als u de code wilt uitvoeren, gebruikt u de volgende opdracht:

    ```bash
    python3 simple_thermostat.py
    ```

    De uitvoer van deze opdracht ziet er als volgt uit:

    ```output
    Connecting using Connection String HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry for temperature
    Sent message
    Sent message
    Sent message
    ...
    ```

1. Als u de telemetrie in IoT Central wilt zien, gaat u naar de **overzichts** pagina voor het **thermostat1** -apparaat:

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-device-telemetry.png" alt-text="Scherm opname van de telemetrie van het downstream-apparaat.":::

    Op de pagina **over** kunt u eigenschaps waarden weer geven die zijn verzonden vanaf het downstream-apparaat en op de **opdracht** pagina kunt u opdrachten aanroepen op het downstream-apparaat.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een transparante gateway met IoT Central configureert, is de voorgestelde volgende stap meer informatie over [IOT Edge](../../iot-edge/about-iot-edge.md).
