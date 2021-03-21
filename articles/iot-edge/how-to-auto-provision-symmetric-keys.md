---
title: Apparaat inrichten met behulp van symmetrische sleutel Attestation-Azure IoT Edge
description: Symmetrische-sleutel attest gebruiken om het automatisch inrichten van apparaten te testen voor Azure IoT Edge met Device Provisioning Service
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: eb5cbc2f2db0ba9f92a637c7e9a905d2f746880a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200842"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Een IoT Edge apparaat maken en inrichten met behulp van symmetrische sleutel attest

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge apparaten kunnen automatisch worden ingericht met behulp van de [Device Provisioning Service](../iot-dps/index.yml) , net zoals apparaten die niet Edge-ingeschakeld zijn. Als u niet bekend bent met het proces van automatische inrichting, bekijkt u het overzicht voor [inrichting](../iot-dps/about-iot-dps.md#provisioning-process) voordat u verdergaat.

In dit artikel wordt beschreven hoe u een individuele of groeps registratie service voor het inrichten van een apparaat voor de inrichting van symmetrische sleutels op een IoT Edge apparaat maakt met de volgende stappen:

* Maak een instantie van IoT Hub Device Provisioning Service (DPS).
* Een inschrijving voor het apparaat maken.
* Installeer de IoT Edge runtime en maak verbinding met de IoT Hub.

Symmetrische-sleutel attest is een eenvoudige benadering voor het verifiëren van een apparaat met een Device Provisioning service-exemplaar. Deze Attestation-methode vertegenwoordigt een ' Hello World '-ervaring voor ontwikkel aars die nieuw zijn voor het inrichten van apparaten of waarvoor geen strikte beveiligings vereisten gelden. Attestation van apparaten met behulp van een [TPM](../iot-dps/concepts-tpm-attestation.md) of [X. 509-certificaten](../iot-dps/concepts-x509-attestation.md) is veiliger en moet worden gebruikt voor strengere beveiligings vereisten.

## <a name="prerequisites"></a>Vereisten

* Een actieve IoT Hub
* Een fysiek of virtueel apparaat

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>De IoT Hub Device Provisioning Service instellen

Maak een nieuw exemplaar van de IoT Hub Device Provisioning Service in Azure en koppel deze aan uw IoT-hub. U kunt de instructies voor [het instellen van de IOT hub DPS](../iot-dps/quick-setup-auto-provision.md)volgen.

Nadat u de Device Provisioning Service hebt uitgevoerd, kopieert u de waarde van **id-bereik** van de pagina overzicht. U gebruikt deze waarde bij het configureren van de IoT Edge runtime.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Een unieke registratie-ID voor het apparaat kiezen

Er moet een unieke registratie-ID worden gedefinieerd om elk apparaat te identificeren. U kunt het MAC-adres, serie nummer of unieke informatie van het apparaat gebruiken.

In dit voor beeld gebruiken we een combi natie van een MAC-adres en serie nummer met de volgende teken reeks voor een registratie-ID: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6` .

Maak een unieke registratie-ID voor uw apparaat. Geldige tekens zijn kleine letters en streepjes ('-').

## <a name="create-a-dps-enrollment"></a>Een DPS-inschrijving maken

Gebruik de registratie-ID van uw apparaat om een afzonderlijke inschrijving in DPS te maken.

Wanneer u een inschrijving in DPS maakt, hebt u de mogelijkheid om een **eerste dubbele toestand** van het apparaat te declareren. In het dubbele apparaat kunt u Tags instellen om apparaten te groeperen op elke gewenste metrische waarde in uw oplossing, zoals regio, omgeving, locatie of apparaattype. Deze tags worden gebruikt voor het maken van [automatische implementaties](how-to-deploy-at-scale.md).

> [!TIP]
> Groeps registraties zijn ook mogelijk bij het gebruik van symmetrische sleutel attest en dezelfde beslissingen als afzonderlijke inschrijvingen.

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw exemplaar van IOT hub Device Provisioning Service.

1. Selecteer onder **instellingen** de optie **inschrijvingen beheren**.

1. Selecteer **Individuele inschrijving toevoegen** en voer de volgende stappen uit om de registratie te configureren:  

   1. Selecteer voor **mechanisme** **symmetrische sleutel**.

   1. Schakel het selectie vakje **sleutels automatisch genereren** in.

   1. Geef de **registratie-id** op die u hebt gemaakt voor uw apparaat.

   1. Geef een **IOT hub apparaat-id** voor uw apparaat op als u wilt. U kunt apparaat-Id's gebruiken om een afzonderlijk apparaat te richten op het implementeren van een module. Als u geen apparaat-ID opgeeft, wordt de registratie-ID gebruikt.

   1. Selecteer **waar** om te declareren dat de inschrijving voor een IOT edge apparaat is. Voor de registratie van een groep moeten alle apparaten worden IoT Edge apparaten of geen van beide.

      > [!TIP]
      > In de Azure CLI kunt u een [inschrijving](/cli/azure/ext/azure-iot/iot/dps/enrollment) of een [registratie groep](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) maken en de vlag voor **rand ingeschakeld** gebruiken om op te geven dat een apparaat, of groep apparaten, een IOT edge apparaat is.

   1. Accepteer de standaard waarde van het toewijzings beleid van de Device Provisioning Service voor de **manier waarop u apparaten aan hubs wilt toewijzen** of kies een andere waarde die specifiek is voor deze inschrijving.

   1. Kies de gekoppelde **IOT hub** waarmee u uw apparaat wilt verbinden. U kunt meerdere hubs kiezen en het apparaat wordt toegewezen aan een van deze op basis van het geselecteerde toewijzings beleid.

   1. Kies **hoe u wilt dat apparaatgegevens worden verwerkt bij het opnieuw inrichten** wanneer apparaten na de eerste keer worden ingericht.

   1. Voeg indien gewenst een tag-waarde toe aan de **eerste dubbele toestand** van het apparaat. U kunt tags gebruiken om groepen apparaten te richten op het implementeren van modules. Bijvoorbeeld:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Zorg ervoor dat **vermelding inschakelen** is ingesteld op **inschakelen**.

   1. Selecteer **Opslaan**.

Nu een inschrijving voor dit apparaat bestaat, kan de IoT Edge runtime automatisch het apparaat inrichten tijdens de installatie. Zorg ervoor dat u de waarde van de **primaire sleutel** van uw registratie kopieert om te gebruiken tijdens de installatie van de IOT Edge runtime, of dat u een apparaatcode wilt maken voor gebruik met een groeps registratie.

## <a name="derive-a-device-key"></a>Een apparaatcode afleiden

> [!NOTE]
> Deze sectie is alleen vereist als u een groeps registratie gebruikt.

Elk apparaat gebruikt de afgeleide apparaatwachtwoord met uw unieke registratie-ID voor het uitvoeren van de symmetrische sleutel attest met de inschrijving tijdens het inrichten. Als u de apparaatcode wilt genereren, gebruikt u de sleutel die u hebt gekopieerd uit uw DPS-inschrijving om een [HMAC-sha256](https://wikipedia.org/wiki/HMAC) van de unieke registratie-id voor het apparaat te berekenen en zet u het resultaat om in Base64-indeling.

Neem de primaire of secundaire sleutel van uw inschrijving niet op in uw apparaatcode.

### <a name="linux-workstations"></a>Linux-werk stations

Als u een Linux-werk station gebruikt, kunt u openssl gebruiken om uw afgeleide apparaatwachtwoord te genereren, zoals wordt weer gegeven in het volgende voor beeld.

Vervang de waarde van **Key** door de **primaire sleutel** die u eerder hebt genoteerd.

Vervang de waarde van **REG_ID** door de registratie-id van uw apparaat.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Windows-werk stations

Als u een Windows-werk station gebruikt, kunt u Power shell gebruiken om uw afgeleide apparaatwachtwoord te genereren, zoals wordt weer gegeven in het volgende voor beeld.

Vervang de waarde van **Key** door de **primaire sleutel** die u eerder hebt genoteerd.

Vervang de waarde van **REG_ID** door de registratie-id van uw apparaat.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>De IoT Edge-runtime installeren

De IoT Edge-runtime wordt op alle IoT Edge-apparaten geïmplementeerd. De onderdelen worden in containers uitgevoerd en bieden u de mogelijkheid om extra containers op het apparaat te implementeren, zodat u code aan de rand kunt uitvoeren.

Volg de stappen in [install the Azure IOT Edge runtime](how-to-install-iot-edge.md)en ga vervolgens terug naar dit artikel om het apparaat in te richten.

## <a name="configure-the-device-with-provisioning-information"></a>Het apparaat configureren met inrichtings gegevens

Zodra de runtime op uw apparaat is geïnstalleerd, configureert u het apparaat met de informatie die wordt gebruikt om verbinding te maken met Device Provisioning Service en IoT Hub.

De volgende informatie is gereed:

* De waarde voor het bereik van de DPS **-id**
* De **registratie-id** van het apparaat dat u hebt gemaakt
* De **primaire sleutel** die u hebt gekopieerd uit de DPS-inschrijving

> [!TIP]
> Voor groeps registraties hebt u de [afgeleide sleutel](#derive-a-device-key) van elk apparaat nodig in plaats van de primaire sleutel voor de DPS-registratie.

### <a name="linux-device"></a>Linux-apparaat

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
1. Open het configuratie bestand op het IoT Edge-apparaat.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Zoek de sectie inrichtings configuraties van het bestand. Verwijder de opmerkingen van de regels voor de inrichting van de symmetrische DPS-sleutel en zorg ervoor dat alle andere inrichtings regels worden afgemeld.

   De `provisioning:` regel mag geen voor gaande witruimte hebben en geneste items moeten worden inge sprongen met twee spaties.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "symmetric_key"
       registration_id: "<REGISTRATION_ID>"
       symmetric_key: "<SYMMETRIC_KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. De waarden van `scope_id` , `registration_id` en `symmetric_key` met uw DPS en apparaatgegevens bijwerken.

1. Gebruik eventueel de `always_reprovision_on_startup` `dynamic_reprovisioning` regels of om het herinrichtings gedrag van uw apparaat te configureren. Als een apparaat is ingesteld op het opnieuw inrichten bij het opstarten, wordt altijd geprobeerd eerst met DPS in te richten en vervolgens terug te vallen naar de inrichtings back-up als dat mislukt. Als een apparaat is ingesteld op dynamisch opnieuw inrichten, wordt IoT Edge opnieuw opgestart en wordt het opnieuw ingericht als er een herinrichtings gebeurtenis wordt gedetecteerd. Zie IoT Hub voor het opnieuw [inrichten van apparaten](../iot-dps/concepts-device-reprovision.md)voor meer informatie.

1. Start de IoT Edge-runtime opnieuw zodat alle configuratie wijzigingen die u op het apparaat hebt aangebracht, worden opgehaald.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Maak een configuratie bestand voor uw apparaat op basis van een sjabloon bestand dat wordt meegeleverd als onderdeel van de IoT Edge installatie.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Open het configuratie bestand op het IoT Edge-apparaat.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. De **inrichtings** sectie van het bestand zoeken. Verwijder de opmerkingen bij de regels voor DPS inrichten met symmetrische sleutel en zorg ervoor dat andere inrichtings regels worden uitgeoefend.

   ```toml
   # DPS provisioning with symmetric key
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "symmetric_key"
   registration_id = "<REGISTRATION_ID>"

   symmetric_key = "<PRIMARY_KEY OR DERIVED_KEY>"
   ```

1. De waarden van `id_scope` , `registration_id` en `symmetric_key` met uw DPS en apparaatgegevens bijwerken.

   De para meter voor de symmetrische sleutel kan een waarde van een inline-sleutel, een bestands-URI of een PKCS # 11-URI accepteren. Verwijder slechts één symmetrische-sleutel regel, op basis van de indeling die u gebruikt.

   Als u een PKCS # 11-Uri's gebruikt, zoekt u de sectie **PKCS # 11** in het configuratie bestand en geeft u informatie op over de PKCS # 11-configuratie.

1. Sla het bestand config. toml op en sluit het af.

1. De configuratie wijzigingen Toep assen die u hebt aangebracht in IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Windows-apparaat

1. Open een Power shell-venster in de beheerders modus. Zorg ervoor dat u een AMD64-sessie van Power shell gebruikt bij het installeren van IoT Edge, niet in Power shell (x86).

1. Met de opdracht **Initialize-IoTEdge** configureert u de IoT Edge-runtime op uw machine. De opdracht wordt standaard ingesteld op hand matig inrichten met Windows-containers. Gebruik daarom de `-DpsSymmetricKey` vlag voor het gebruik van automatische inrichting met symmetrische sleutel verificatie.

   Vervang de waarden van de tijdelijke aanduidingen voor `{scope_id}` , `{registration_id}` en `{symmetric_key}` met de gegevens die u eerder hebt verzameld.

   Voeg de `-ContainerOs Linux` para meter toe als u Linux-containers in Windows gebruikt.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Geslaagde installatie controleren

Als de runtime is gestart, kunt u naar uw IoT Hub gaan en IoT Edge modules op het apparaat implementeren. Gebruik de volgende opdrachten op het apparaat om te controleren of de runtime is geïnstalleerd en is gestart.

### <a name="linux-device"></a>Linux-apparaat

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Controleer de status van de IoT Edge-service.

```cmd/sh
systemctl status iotedge
```

Bekijk service Logboeken.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Een lijst met actieve modules weer geven.

```cmd/sh
iotedge list
```

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Controleer de status van de IoT Edge-service.

```cmd/sh
sudo iotedge system status
```

Bekijk service Logboeken.

```cmd/sh
sudo iotedge system logs
```

Een lijst met actieve modules weer geven.

```cmd/sh
sudo iotedge list
```

:::moniker-end

### <a name="windows-device"></a>Windows-apparaat

Controleer de status van de IoT Edge-service.

```powershell
Get-Service iotedge
```

Bekijk service Logboeken.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Een lijst met actieve modules weer geven.

```powershell
iotedge list
```

U kunt controleren of de afzonderlijke registratie die u hebt gemaakt in Device Provisioning Service is gebruikt. Navigeer naar het Device Provisioning service-exemplaar in het Azure Portal. Open de inschrijvings gegevens voor de afzonderlijke inschrijving die u hebt gemaakt. U ziet dat de status van de registratie is **toegewezen** en dat de apparaat-id wordt vermeld.

## <a name="next-steps"></a>Volgende stappen

Met het inschrijvings proces voor Device Provisioning Service kunt u de apparaat-ID en de dubbele Tags van het apparaat instellen op hetzelfde moment als u het nieuwe apparaat inricht. U kunt deze waarden gebruiken om afzonderlijke apparaten of groepen apparaten te richten met behulp van automatische Apparaatbeheer. Meer informatie over [het implementeren en bewaken van IOT Edge modules op schaal met behulp van de Azure Portal](how-to-deploy-at-scale.md) of [met behulp van Azure cli](how-to-deploy-cli-at-scale.md).
