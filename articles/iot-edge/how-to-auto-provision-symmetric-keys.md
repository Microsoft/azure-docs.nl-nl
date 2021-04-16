---
title: Apparaat inrichten met attestation met symmetrische sleutels - Azure IoT Edge
description: Attestation met symmetrische sleutels gebruiken om automatische apparaatinrichting te testen voor Azure IoT Edge met Device Provisioning Service
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 66e1e561c14b169d41028e151ac054888830b881
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481970"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Een apparaat met een IoT Edge maken en inrichten met behulp van attestation met een symmetrische sleutel

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge kunnen automatisch worden ingericht met [Device Provisioning Service,](../iot-dps/index.yml) net zoals apparaten die niet zijn ingeschakeld voor edge. Als u niet bekend bent met het proces van automatische inrichting, bekijkt u het overzicht voor [inrichting](../iot-dps/about-iot-dps.md#provisioning-process) voordat u verdergaat.

In dit artikel wordt beschreven hoe u met behulp van attestation met een symmetrische sleutel op een IoT Edge apparaat een afzonderlijke of groepsinschrijving voor Device Provisioning Service maakt met behulp van de volgende stappen:

* Maak een exemplaar van IoT Hub Device Provisioning Service (DPS).
* Maak een inschrijving voor het apparaat.
* Installeer de IoT Edge runtime en maak verbinding met de IoT Hub.

Attestation met een symmetrische sleutel is een eenvoudige benadering voor het authenticeren van een apparaat met een Device Provisioning Service-exemplaar. Deze attestation-methode is een 'Hallo wereld'-ervaring voor ontwikkelaars die geen ervaring hebben met het inrichten van apparaten of die geen strikte beveiligingsvereisten hebben. Apparaatattestatie met behulp van [een TPM-](../iot-dps/concepts-tpm-attestation.md) of [X.509-certificaat](../iot-dps/concepts-x509-attestation.md) is veiliger en moet worden gebruikt voor strengere beveiligingsvereisten.

## <a name="prerequisites"></a>Vereisten

* Een actief IoT Hub
* Een fysiek of virtueel apparaat

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>De IoT Hub Device Provisioning Service

Maak een nieuw exemplaar van de IoT Hub Device Provisioning Service in Azure en koppel deze aan uw IoT-hub. U kunt de instructies volgen in [De IoT Hub DPS instellen.](../iot-dps/quick-setup-auto-provision.md)

Nadat de Device Provisioning Service is uitgevoerd, kopieert u de waarde van **Id-bereik** op de overzichtspagina. U gebruikt deze waarde wanneer u de IoT Edge configureert.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Kies een unieke registratie-id voor het apparaat

Er moet een unieke registratie-id worden gedefinieerd om elk apparaat te identificeren. U kunt het MAC-adres, serienummer of unieke gegevens van het apparaat gebruiken.

In dit voorbeeld gebruiken we een combinatie van een MAC-adres en serienummer die de volgende tekenreeks vormen voor een registratie-id: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6` .

Maak een unieke registratie-id voor uw apparaat. Geldige tekens zijn kleine letters alfanumeriek en streepje ('-').

## <a name="create-a-dps-enrollment"></a>Een DPS-inschrijving maken

Gebruik de registratie-id van uw apparaat om een afzonderlijke inschrijving in DPS te maken.

Wanneer u een inschrijving in DPS maakt, hebt u de mogelijkheid om de status van de initiële **apparaat-dubbel te declaren.** In de apparaat dubbel kunt u tags instellen om apparaten te groepen op elke metrische gegevens die u nodig hebt in uw oplossing, zoals regio, omgeving, locatie of apparaattype. Deze tags worden gebruikt om automatische [implementaties te maken.](how-to-deploy-at-scale.md)

> [!TIP]
> Groepsinschrijvingen zijn ook mogelijk bij het gebruik van attestation met symmetrische sleutels en waarbij dezelfde beslissingen worden genomen als afzonderlijke inschrijvingen.

1. [Navigeer in Azure Portal](https://portal.azure.com)naar uw exemplaar van IoT Hub Device Provisioning Service.

1. Selecteer **onder** Instellingen **de optie Inschrijvingen beheren.**

1. Selecteer **Afzonderlijke inschrijving toevoegen en** voltooi vervolgens de volgende stappen om de inschrijving te configureren:  

   1. Selecteer **bij Mechanisme** de optie **Symmetrische sleutel**.

   1. Schakel het **selectievakje Sleutels automatisch genereren** in.

   1. Geef de **registratie-id op** die u voor uw apparaat hebt gemaakt.

   1. Geef een **IoT Hub apparaat-id** voor uw apparaat op als u dat wilt. U kunt apparaat-ID's gebruiken om een afzonderlijk apparaat te targeten voor module-implementatie. Als u geen apparaat-id op geeft, wordt de registratie-id gebruikt.

   1. Selecteer **Waar** om aan te geven dat de inschrijving voor een IoT Edge is. Voor een groepsinschrijving moeten alle apparaten zijn IoT Edge of geen van beide.

      > [!TIP]
      > In de Azure CLI kunt [](/cli/azure/iot/dps/enrollment) u een [](/cli/azure/iot/dps/enrollment-group) inschrijving of een registratiegroep maken en de vlag Edge **gebruiken** om op te geven dat een apparaat of groep apparaten een IoT Edge is.

   1. Accepteer de standaardwaarde uit het toewijzingsbeleid van device provisioning service voor de manier waarop u apparaten wilt toewijzen aan **hubs** of kies een andere waarde die specifiek is voor deze inschrijving.

   1. Kies het **gekoppelde IoT Hub** u uw apparaat wilt verbinden. U kunt meerdere hubs kiezen en het apparaat wordt toegewezen aan een van deze hubs volgens het geselecteerde toewijzingsbeleid.

   1. Kies **hoe u wilt dat apparaatgegevens worden verwerkt** bij het opnieuw inrichten wanneer apparaten na de eerste keer om inrichting vragen.

   1. Voeg indien nodig een tagwaarde **toe** aan de status van de initiële apparaat-dubbel. U kunt tags gebruiken voor doelgroepen van apparaten voor module-implementatie. Bijvoorbeeld:

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

   1. Zorg **ervoor dat Vermelding** inschakelen is ingesteld op **Inschakelen.**

   1. Selecteer **Opslaan**.

Nu er een inschrijving bestaat voor dit apparaat, kan de IoT Edge het apparaat automatisch inrichten tijdens de installatie. Zorg ervoor dat u de  waarde van de primaire sleutel van uw inschrijving kopieert voor gebruik bij het installeren van de IoT Edge-runtime of als u apparaatsleutels gaat maken voor gebruik met een groepsinschrijving.

## <a name="derive-a-device-key"></a>Een apparaatsleutel afleiden

> [!NOTE]
> Deze sectie is alleen vereist als u een groepsinschrijving gebruikt.

Elk apparaat gebruikt de afgeleide apparaatsleutel met uw unieke registratie-id om tijdens het inrichten een attestation met een symmetrische sleutel uit te voeren met de inschrijving. Als u de apparaatsleutel wilt genereren, gebruikt u de sleutel die u hebt gekopieerd uit uw DPS-inschrijving om een [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) van de unieke registratie-id voor het apparaat te berekenen en het resultaat te converteren naar Base64-indeling.

Neem de primaire of secundaire sleutel van uw inschrijving niet op in uw apparaatcode.

### <a name="linux-workstations"></a>Linux-werkstations

Als u een Linux-werkstation gebruikt, kunt u openssl gebruiken om uw afgeleide apparaatsleutel te genereren, zoals wordt weergegeven in het volgende voorbeeld.

Vervang de waarde van **KEY door** de primaire **sleutel die** u eerder hebt genoteerd.

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

### <a name="windows-based-workstations"></a>Windows-werkstations

Als u een Windows-werkstation gebruikt, kunt u PowerShell gebruiken om uw afgeleide apparaatsleutel te genereren, zoals wordt weergegeven in het volgende voorbeeld.

Vervang de waarde van **KEY door** de primaire **sleutel die** u eerder hebt genoteerd.

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

De IoT Edge-runtime wordt op alle IoT Edge-apparaten geïmplementeerd. De onderdelen worden uitgevoerd in containers en bieden u de mogelijkheid om extra containers op het apparaat te implementeren, zodat u code aan de rand kunt uitvoeren.

Volg de stappen in [De Azure IoT Edge runtime installeren](how-to-install-iot-edge.md)en ga vervolgens terug naar dit artikel om het apparaat in terichten.

## <a name="configure-the-device-with-provisioning-information"></a>Het apparaat configureren met inrichtingsgegevens

Zodra de runtime op uw apparaat is geïnstalleerd, configureert u het apparaat met de informatie die wordt gebruikt om verbinding te maken met Device Provisioning Service en IoT Hub.

De volgende informatie is gereed:

* De **dps-id-bereikwaarde**
* De **apparaatregistratie-id die u** hebt gemaakt
* De **primaire sleutel die** u hebt gekopieerd uit de DPS-inschrijving

> [!TIP]
> Voor groepsinschrijvingen hebt u de [](#derive-a-device-key) afgeleide sleutel van elk apparaat nodig in plaats van de primaire dps-inschrijvingssleutel.

### <a name="linux-device"></a>Linux-apparaat

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
1. Open het configuratiebestand op het IoT Edge apparaat.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Zoek de sectie inrichtingsconfiguraties van het bestand. Maak opmerkingen bij de regels voor het inrichten van symmetrische DPS-sleutels en zorg ervoor dat eventuele andere inrichtingslijnen worden becommentereerd.

   De `provisioning:` regel mag geen voorgaande witruimte hebben en geneste items moeten worden ingesprongen door twee spaties.

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

1. Werk de waarden van `scope_id` , en bij met uw `registration_id` `symmetric_key` DPS- en apparaatgegevens.

1. U kunt eventueel de regels of gebruiken om het gedrag van de inrichting van uw apparaat `always_reprovision_on_startup` `dynamic_reprovisioning` te configureren. Als een apparaat bij het opstarten opnieuw wordt ingericht, probeert het altijd eerst in terichten met DPS en vervolgens terug te vallen op de inrichtingsback-up als dat mislukt. Als een apparaat is ingesteld op dynamisch opnieuw in te stellen, wordt IoT Edge opnieuw opgestart en opnieuw in te stellen als er een herinrichtingsgebeurtenis wordt gedetecteerd. Zie concepten voor het opnieuw [IoT Hub van apparaten voor meer informatie.](../iot-dps/concepts-device-reprovision.md)

1. Start de IoT Edge opnieuw op, zodat alle configuratiewijzigingen die u op het apparaat hebt aangebracht, worden opgehaald.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Maak een configuratiebestand voor uw apparaat op basis van een sjabloonbestand dat is opgegeven als onderdeel van de IoT Edge installatie.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Open het configuratiebestand op het IoT Edge apparaat.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Zoek de **sectie Inrichten** van het bestand. Maak commentaar van de regels voor DPS-inrichting met een symmetrische sleutel en zorg ervoor dat eventuele andere inrichtingslijnen als commentaar worden opgenomen.

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

1. Werk de waarden van `id_scope` , en bij met uw `registration_id` `symmetric_key` DPS- en apparaatgegevens.

   De parameter voor de symmetrische sleutel kan een waarde van een inlinesleutel, een bestands-URI of een PKCS#11-URI accepteren. Maak slechts één symmetrische sleutelregel los, op basis van de indeling die u gebruikt.

   Als u PKCS#11 URI's gebruikt, gaat u naar de sectie **PKCS#11** in het configuratiebestand en geeft u informatie op over uw PKCS#11-configuratie.

1. Sla het bestand config.toml op en sluit het.

1. Pas de configuratiewijzigingen toe die u hebt aangebracht op IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Windows-apparaat

1. Open een PowerShell-venster in de beheerdersmodus. Zorg ervoor dat u een AMD64-sessie van PowerShell gebruikt bij het installeren van IoT Edge, niet powershell (x86).

1. Met de opdracht **Initialize-IoTEdge** configureert u de IoT Edge-runtime op uw machine. De opdracht wordt standaard ingesteld op handmatige inrichting met Windows-containers, dus gebruik de vlag om automatische inrichting met verificatie met `-DpsSymmetricKey` symmetrische sleutels te gebruiken.

   Vervang de tijdelijke aanduidingen voor `{scope_id}` , en door de gegevens die u eerder hebt `{registration_id}` `{symmetric_key}` verzameld.

   Voeg de `-ContainerOs Linux` parameter toe als u Linux-containers in Windows gebruikt.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Controleren of de installatie is geslaagd

Als de runtime is gestart, kunt u naar uw IoT Hub en beginnen met het implementeren IoT Edge modules op uw apparaat. Gebruik de volgende opdrachten op uw apparaat om te controleren of de runtime is geïnstalleerd en gestart.

### <a name="linux-device"></a>Linux-apparaat

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Controleer de status van de IoT Edge-service.

```cmd/sh
systemctl status iotedge
```

Bekijk servicelogboeken.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lijst met modules die worden uitgevoerd.

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

Bekijk servicelogboeken.

```cmd/sh
sudo iotedge system logs
```

Lijst met modules die worden uitgevoerd.

```cmd/sh
sudo iotedge list
```

:::moniker-end

### <a name="windows-device"></a>Windows-apparaat

Controleer de status van de IoT Edge-service.

```powershell
Get-Service iotedge
```

Bekijk servicelogboeken.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lijst met modules die worden uitgevoerd.

```powershell
iotedge list
```

U kunt controleren of de afzonderlijke inschrijving die u in Device Provisioning Service hebt gemaakt, is gebruikt. Navigeer naar uw Device Provisioning Service-exemplaar in de Azure Portal. Open de inschrijvingsgegevens voor de afzonderlijke inschrijving die u hebt gemaakt. U ziet dat de status van de inschrijving is **toegewezen** en dat de apparaat-id wordt weergegeven.

## <a name="next-steps"></a>Volgende stappen

Met het device provisioning service-inschrijvingsproces kunt u de apparaat-id en apparaattweetags instellen op hetzelfde moment dat u het nieuwe apparaat inrichten. U kunt deze waarden gebruiken voor afzonderlijke apparaten of groepen apparaten met behulp van automatisch apparaatbeheer. Meer informatie over [het implementeren en controleren IoT Edge modules op](how-to-deploy-at-scale.md) schaal met behulp van de Azure Portal of azure [CLI.](how-to-deploy-cli-at-scale.md)
