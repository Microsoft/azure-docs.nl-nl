---
title: Apparaten automatisch inrichten met DPS met behulp van X.509-certificaten - Azure IoT Edge | Microsoft Docs
description: X.509-certificaten gebruiken om automatische apparaatinrichting te testen voor Azure IoT Edge Device Provisioning Service
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: f3c783c57b49b45943882703aec6d735d12bf830
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481953"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>Een apparaat met IoT Edge X.509 maken en inrichten

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Met [de Azure IoT Hub Device Provisioning Service (DPS)](../iot-dps/index.yml)kunt u automatisch apparaten IoT Edge met behulp van X.509-certificaten. Als u niet bekend bent met het proces van automatische inrichting, bekijkt u het overzicht voor [inrichting](../iot-dps/about-iot-dps.md#provisioning-process) voordat u verdergaat.

In dit artikel wordt beschreven hoe u een Device Provisioning Service-inschrijving maakt met behulp van X.509-certificaten op een IoT Edge apparaat met de volgende stappen:

* Certificaten en sleutels genereren.
* Maak een afzonderlijke inschrijving voor een apparaat of een groepsinschrijving voor een set apparaten.
* Installeer de IoT Edge runtime en registreer het apparaat bij IoT Hub.

Het gebruik van X.509-certificaten als attestation-mechanisme is een uitstekende manier om productie te schalen en het inrichten van apparaten te vereenvoudigen. Normaal gesproken worden X.509-certificaten gerangschikt in een vertrouwensketen voor certificaten. Vanaf een zelf-ondertekend of vertrouwd basiscertificaat ondertekent elk certificaat in de keten het volgende lagere certificaat. Dit patroon maakt een gedelegeerde vertrouwensketen van het basiscertificaat tot en met elk tussencertificaat tot het laatste leaf-certificaat dat op een apparaat is geïnstalleerd.

## <a name="prerequisites"></a>Vereisten

* Een actief IoT Hub.
* Een fysiek of virtueel apparaat dat het IoT Edge is.
* De nieuwste versie van [Git is](https://git-scm.com/download/) geïnstalleerd.
* Een exemplaar van de IoT Hub Device Provisioning Service in Azure, gekoppeld aan uw IoT-hub.
  * Als u geen Device Provisioning Service-exemplaar hebt, volgt u de instructies in [IoT Hub DPS.](../iot-dps/quick-setup-auto-provision.md)
  * Nadat u Device Provisioning Service hebt uitgevoerd, kopieert u de waarde van **Id-bereik** op de overzichtspagina. U gebruikt deze waarde wanneer u de IoT Edge configureert.

## <a name="generate-device-identity-certificates"></a>Identiteitscertificaten voor apparaten genereren

Het apparaat-id-certificaat is een gewoon certificaat dat via een vertrouwensketen verbinding maakt met het bovenste certificaat van de X.509-certificeringsinstantie (CA). Voor het apparaat-id-certificaat moet de algemene naam (CN) zijn ingesteld op de apparaat-id die u op het apparaat in uw IoT-hub wilt hebben.

Apparaat-id-certificaten worden alleen gebruikt voor het inrichten van IoT Edge apparaat en het verifiëren van het apparaat met Azure IoT Hub. Ze ondertekenen geen certificaten, in tegenstelling tot de CA-certificaten die het IoT Edge aan modules of leaf-apparaten voor verificatie. Zie de gebruiksgegevens van [Azure IoT Edge certificaat voor meer informatie.](iot-edge-certs.md)

Nadat u het apparaat-id-certificaat hebt gemaakt, moet u twee bestanden hebben: een CER- of PEM-bestand dat het openbare gedeelte van het certificaat bevat, en een CER- of PEM-bestand met de persoonlijke sleutel van het certificaat. Als u van plan bent om groepsinschrijving in DPS te gebruiken, hebt u ook het openbare gedeelte van een tussenliggend of basis-CA-certificaat in dezelfde certificaatketen van vertrouwensrelatie nodig.

U hebt de volgende bestanden nodig om automatische inrichting met X.509 in te stellen:

* Het apparaat-id-certificaat en het persoonlijke sleutelcertificaat. Het apparaat-id-certificaat wordt geüpload naar DPS als u een afzonderlijke inschrijving maakt. De persoonlijke sleutel wordt doorgegeven aan de IoT Edge runtime.
* Een volledig ketencertificaat, dat ten minste de apparaat-id en de tussenliggende certificaten moet hebben. Het volledige ketencertificaat wordt doorgegeven aan de IoT Edge runtime.
* Een tussenliggend of basis-CA-certificaat uit de certificaatketen van vertrouwensrelatie. Dit certificaat wordt geüpload naar DPS als u een groepsinschrijving maakt.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
> [!NOTE]
> Op dit moment voorkomt een beperking in libiothsm het gebruik van certificaten die op of na 1 januari 2038 verlopen.

:::moniker-end

### <a name="use-test-certificates-optional"></a>Testcertificaten gebruiken (optioneel)

Als u geen certificeringsinstantie beschikbaar hebt om nieuwe identiteitscertificaten te maken en dit scenario wilt uitproberen, bevat de git-opslagplaats Azure IoT Edge scripts die u kunt gebruiken om testcertificaten te genereren. Deze certificaten zijn alleen ontworpen voor ontwikkelingstesten en mogen niet in productie worden gebruikt.

Als u testcertificaten wilt maken, volgt u de stappen in Democertificaten maken om [de apparaatfuncties IoT Edge testen.](how-to-create-test-certificates.md) Voltooi de twee vereiste secties om de scripts voor het genereren van certificaten in te stellen en een basis-CA-certificaat te maken. Volg vervolgens de stappen om een apparaat-id-certificaat te maken. Wanneer u klaar bent, hebt u de volgende certificaatketen en het volgende sleutelpaar nodig:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

U hebt beide certificaten op het IoT Edge nodig. Als u afzonderlijke inschrijving in DPS gaat gebruiken, uploadt u het bestand .cert.pem. Als u groepsinschrijving in DPS wilt gebruiken, hebt u ook een ca-tussen- of -basiscertificaat in dezelfde vertrouwensketen nodig om te uploaden. Als u democertificaten gebruikt, gebruikt u het `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` certificaat voor groepsinschrijving.

## <a name="create-a-dps-individual-enrollment"></a>Een afzonderlijke DPS-inschrijving maken

Gebruik de gegenereerde certificaten en sleutels om een afzonderlijke inschrijving in DPS te maken voor één IoT Edge apparaat. Afzonderlijke inschrijvingen nemen het openbare gedeelte van het identiteitscertificaat van een apparaat en komen overeen met het certificaat op het apparaat.

Als u meerdere apparaten met IoT Edge wilt inrichten, volgt u de stappen in de volgende sectie Een [DPS-groepsinschrijving maken.](#create-a-dps-group-enrollment)

Wanneer u een inschrijving in DPS maakt, hebt u de mogelijkheid om de status van de initiële **apparaat-dubbel te declaren.** In de apparaat dubbel kunt u tags instellen om apparaten te groepen op elke metrische gegevens die u nodig hebt in uw oplossing, zoals regio, omgeving, locatie of apparaattype. Deze tags worden gebruikt om automatische [implementaties te maken.](how-to-deploy-at-scale.md)

Zie How [to manage device enrollments](../iot-dps/how-to-manage-enrollments.md)(Apparaatinschrijvingen beheren) voor meer informatie over inschrijvingen in Device Provisioning Service.

   > [!TIP]
   > In de Azure CLI kunt [](/cli/azure/iot/dps/enrollment) u een [](/cli/azure/iot/dps/enrollment-group) inschrijving of een registratiegroep maken en de vlag Edge gebruiken om op te geven dat een apparaat of groep apparaten een IoT Edge is. 

1. [Navigeer in Azure Portal](https://portal.azure.com)naar uw exemplaar van IoT Hub Device Provisioning Service.

1. Selecteer **onder Instellingen** de optie **Inschrijvingen beheren.**

1. Selecteer **Afzonderlijke inschrijving toevoegen en** voltooi de volgende stappen om de inschrijving te configureren:  

   * **Mechanisme:** selecteer **X.509.**

   * **PEM- of CER-bestand** van primair certificaat: upload het openbare bestand van het apparaat-id-certificaat. Als u de scripts hebt gebruikt om een testcertificaat te genereren, kiest u het volgende bestand:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **IoT Hub apparaat-id:** geef een id op voor uw apparaat als u wilt. U kunt apparaat-ID's gebruiken om een afzonderlijk apparaat te targeten voor module-implementatie. Als u geen apparaat-id op geeft, wordt de algemene naam (CN) in het X.509-certificaat gebruikt.

   * **IoT Edge apparaat:** selecteer **Waar om** aan te geven dat de inschrijving voor een IoT Edge is.

   * **Selecteer de IoT-hubs** aan wie dit apparaat kan worden toegewezen: kies de gekoppelde IoT-hub waar u uw apparaat mee wilt verbinden. U kunt meerdere hubs kiezen en het apparaat wordt toegewezen aan een van deze hubs volgens het geselecteerde toewijzingsbeleid.

   * **Initiële status van** apparaat-dubbel: voeg een tagwaarde toe die u wilt toevoegen aan de apparaat dubbel. U kunt tags gebruiken voor doelgroepen van apparaten voor automatische implementatie. Bijvoorbeeld:

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

1. Selecteer **Opslaan**.

Nu er een inschrijving bestaat voor dit apparaat, kan de IoT Edge het apparaat automatisch inrichten tijdens de installatie. Ga door naar [de sectie IoT Edge runtime](#install-the-iot-edge-runtime) installeren om uw apparaat IoT Edge instellen.

## <a name="create-a-dps-group-enrollment"></a>Een DPS-groepsinschrijving maken

Gebruik de gegenereerde certificaten en sleutels om een groepsinschrijving te maken in DPS voor meerdere IoT Edge apparaten. Groepsinschrijvingen maken gebruik van een tussenliggend ca-certificaat of basiscertificaat uit de vertrouwensketen die wordt gebruikt voor het genereren van de afzonderlijke certificaten voor apparaat-id's.

Als u in plaats daarvan één apparaat met IoT Edge wilt inrichten, volgt u de stappen in de vorige sectie Een afzonderlijke [DPS-inschrijving maken.](#create-a-dps-individual-enrollment)

Wanneer u een inschrijving in DPS maakt, hebt u de mogelijkheid om de status van een initiële **apparaat-dubbel te declaren.** In de apparaat dubbel kunt u tags instellen om apparaten te groepen op elke metrische gegevens die u nodig hebt in uw oplossing, zoals regio, omgeving, locatie of apparaattype. Deze tags worden gebruikt om automatische [implementaties te maken.](how-to-deploy-at-scale.md)

### <a name="verify-your-root-certificate"></a>Uw basiscertificaat verifiëren

Wanneer u een registratiegroep maakt, hebt u de mogelijkheid om een geverifieerd certificaat te gebruiken. U kunt een certificaat bij DPS controleren door te bewijzen dat u eigenaar bent van het basiscertificaat. Zie Bewijs van eigendom voor [X.509 CA-certificaten](../iot-dps/how-to-verify-certificates.md)voor meer informatie.

1. [Navigeer in Azure Portal](https://portal.azure.com)naar uw exemplaar van IoT Hub Device Provisioning Service.

1. Selecteer **Certificaten** in het menu aan de linkerkant.

1. Selecteer **Toevoegen om** een nieuw certificaat toe te voegen.

1. Voer een gebruiksvriendelijke naam in voor uw certificaat en blader naar het CER- of PEM-bestand dat het openbare deel van uw X.509-certificaat vertegenwoordigt.

   Als u de democertificaten gebruikt, uploadt u het `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` certificaat.

1. Selecteer **Opslaan**.

1. Uw certificaat wordt nu weergegeven op de **pagina** Certificaten. Selecteer deze om de certificaatdetails te openen.

1. Selecteer **Verificatiecode genereren en** kopieer vervolgens de gegenereerde code.

1. Of u nu uw eigen CA-certificaat hebt meegebracht of de democertificaten gebruikt, u kunt het verificatieprogramma in de IoT Edge-opslagplaats gebruiken om het bewijs van eigendom te controleren. Het verificatieprogramma gebruikt uw CA-certificaat om een nieuw certificaat te ondertekenen dat de opgegeven verificatiecode als onderwerpnaam heeft.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Upload op dezelfde pagina met certificaatdetails in Azure Portal het zojuist gegenereerde verificatiecertificaat.

1. Selecteer **Verifiëren**.

### <a name="create-enrollment-group"></a>Registratiegroep maken

Zie How [to manage device enrollments](../iot-dps/how-to-manage-enrollments.md)(Apparaatinschrijvingen beheren) voor meer informatie over inschrijvingen in Device Provisioning Service.

1. [Navigeer in Azure Portal](https://portal.azure.com)naar uw exemplaar van IoT Hub Device Provisioning Service.

1. Selecteer **onder Instellingen** de optie **Inschrijvingen beheren.**

1. Selecteer **Registratiegroep toevoegen en** voltooi de volgende stappen om de inschrijving te configureren:

   * **Groepsnaam:** geef een onthoudende naam op voor deze groepsinschrijving.

   * **Attestation-type:** selecteer **Certificaat**.

   * **IoT Edge apparaat:** selecteer **Waar.** Voor een groepsinschrijving moeten alle apparaten zijn IoT Edge of geen van beide.

   * **Certificaattype:** selecteer **CA-certificaat** als u een geverifieerd CA-certificaat  hebt opgeslagen met DPS of tussencertificaat als u een nieuw bestand wilt uploaden voor alleen deze inschrijving.

   * **Primair certificaat:** als u in de laatste sectie ca-certificaat hebt gekozen, kiest u uw certificaat in de vervolgkeuzelijst. Als u tussenliggend certificaat hebt gekozen, uploadt u het openbare bestand van een CA-certificaat in de vertrouwensketen die is gebruikt voor het genereren van de identiteitscertificaten van het apparaat.

   * **Selecteer de IoT-hubs** aan wie dit apparaat kan worden toegewezen: kies de gekoppelde IoT-hub waar u uw apparaat mee wilt verbinden. U kunt meerdere hubs kiezen en het apparaat wordt toegewezen aan een van deze hubs volgens het geselecteerde toewijzingsbeleid.

   * **Initiële status van** apparaat-dubbel: voeg een tagwaarde toe die u wilt toevoegen aan de apparaat dubbel. U kunt tags gebruiken voor doelgroepen van apparaten voor automatische implementatie. Bijvoorbeeld:

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

1. Selecteer **Opslaan**.

Nu er een inschrijving bestaat voor dit apparaat, kan de IoT Edge het apparaat automatisch inrichten tijdens de installatie. Ga door naar de volgende sectie om uw apparaat IoT Edge instellen.

## <a name="install-the-iot-edge-runtime"></a>De IoT Edge-runtime installeren

De IoT Edge-runtime wordt op alle IoT Edge-apparaten geïmplementeerd. De onderdelen worden uitgevoerd in containers en bieden u de mogelijkheid om extra containers op het apparaat te implementeren, zodat u code aan de rand kunt uitvoeren.

Volg de stappen in [De Azure IoT Edge runtime installeren](how-to-install-iot-edge.md)en ga vervolgens terug naar dit artikel om het apparaat in terichten.

X.509-inrichting met DPS wordt alleen ondersteund in IoT Edge versie 1.0.9 of hoger.

## <a name="configure-the-device-with-provisioning-information"></a>Het apparaat configureren met inrichtingsgegevens

Zodra de runtime op uw apparaat is geïnstalleerd, configureert u het apparaat met de informatie die wordt gebruikt om verbinding te maken met Device Provisioning Service en IoT Hub.

De volgende informatie is gereed:

* De waarde **dps-id-bereik.** U kunt deze waarde ophalen op de overzichtspagina van uw DPS-exemplaar in de Azure Portal.
* Het certificaatketenbestand voor apparaat-id's op het apparaat.
* Het bestand met de apparaat-id-sleutel op het apparaat.
* Een optionele registratie-id. Als deze niet wordt opgegeven, wordt de id uit de algemene naam in het certificaat voor apparaat-id's gehaald.

### <a name="linux-device"></a>Linux-apparaat

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Open het configuratiebestand op het IoT Edge apparaat.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Zoek de sectie inrichtingsconfiguraties van het bestand. Maak de opmerkingen bij de regels voor het inrichten van DPS X.509-certificaten los en zorg ervoor dat eventuele andere inrichtingsregels als commentaar worden vermeld.

   De `provisioning:` regel mag geen voorgaande witruimte hebben en geneste items moeten worden ingesprongen door twee spaties.

   ```yml
   # DPS X.509 provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "x509"
   #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Werk de waarden `scope_id` van , en bij met uw `identity_cert` `identity_pk` DPS- en apparaatgegevens.

   Wanneer u het X.509-certificaat en de sleutelgegevens aan het bestand config.yaml toevoegt, moeten de paden worden opgegeven als bestands-URI's. Bijvoorbeeld:

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. Geef desgewenst een op `registration_id` voor het apparaat. Laat deze regel anders weg als opmerking om het apparaat te registreren met de CN-naam van het identiteitscertificaat.

1. U kunt eventueel de regels of gebruiken om het inrichtingsgedrag van uw apparaat `always_reprovision_on_startup` `dynamic_reprovisioning` te configureren. Als een apparaat bij het opstarten opnieuw wordt ingericht, probeert het altijd eerst in terichten met DPS en vervolgens terug te vallen op de inrichtingsback-up als dat mislukt. Als een apparaat is ingesteld op dynamisch opnieuw in te stellen, wordt IoT Edge opnieuw opgestart en opnieuw in te stellen als er een nieuwe inrichtingsgebeurtenis wordt gedetecteerd. Zie concepten voor het opnieuw [IoT Hub van apparaten voor meer informatie.](../iot-dps/concepts-device-reprovision.md)

1. Sla het bestand config.yaml op en sluit het.

1. Start de IoT Edge opnieuw op, zodat alle configuratiewijzigingen die u op het apparaat hebt aangebracht, worden opgehaald.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1. -->

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

1. Zoek de **sectie Inrichten** van het bestand. Maak de opmerkingen bij de regels voor DPS-inrichting met het X.509-certificaat los en zorg ervoor dat andere inrichtingsregels als commentaar worden vermeld.

   ```toml
   # DPS provisioning with X.509 certificate
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "x509"
   # registration_id = "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"

   identity_cert = "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

1. Werk de waarden van `id_scope` , en bij met uw `identity_cert` `identity_pk` DPS- en apparaatgegevens.

   De waarde van het identiteitscertificaat kan worden opgegeven als een bestands-URI of dynamisch worden uitgegeven met behulp van EST of een lokale certificeringsinstantie. Maak slechts één regel uitcommenter, op basis van de indeling die u wilt gebruiken.

   De persoonlijke sleutelwaarde voor identiteit kan worden opgegeven als een bestands-URI of een PKCS#11-URI. Maak slechts één regel uitcommenter, op basis van de indeling die u wilt gebruiken.

   Als u PKCS#11 URI's gebruikt, gaat u naar de sectie **PKCS#11** in het configuratiebestand en geeft u informatie op over uw PKCS#11-configuratie.

1. Geef eventueel een op `registration_id` voor het apparaat. Laat anders die regel als commentaar weg om het apparaat te registreren met de algemene naam van het identiteitscertificaat.

1. Sla het bestand op en sluit het.

1. Pas de configuratiewijzigingen toe die u hebt aangebracht op IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Windows-apparaat

1. Open een PowerShell-venster in de beheerdersmodus. Zorg ervoor dat u een AMD64-sessie van PowerShell gebruikt bij het installeren van IoT Edge, niet powershell (x86).

1. Met de opdracht **Initialize-IoTEdge** configureert u de IoT Edge-runtime op uw machine. De opdracht wordt standaard ingesteld op handmatige inrichting met Windows-containers, dus gebruik de vlag om automatische inrichting met `-DpsX509` X.509-certificaatverificatie te gebruiken.

   Vervang de tijdelijke aanduidingen voor , en door de juiste waarden van uw `{scope_id}` `{identity cert chain path}` `{identity key path}` DPS-exemplaar en de bestandspaden op uw apparaat.

   Voeg de toe als u de apparaat-id wilt instellen als iets `-RegistrationId {registration_id}` anders dan de CN-naam van het identiteitscertificaat.

   Voeg de `-ContainerOs Linux` parameter toe als u Linux-containers in Windows gebruikt.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >In het configuratiebestand worden uw certificaat- en sleutelgegevens opgeslagen als bestands-URI's. Met de Initialize-IoTEdge wordt deze opmaakstap echter voor u verwerkt, zodat u het absolute pad naar het certificaat en de sleutelbestanden op uw apparaat kunt geven.

## <a name="verify-successful-installation"></a>Controleren of de installatie is geslaagd

Als de runtime is gestart, kunt u naar uw IoT Hub en beginnen met het implementeren IoT Edge modules op uw apparaat.

U kunt controleren of de afzonderlijke inschrijving die u in Device Provisioning Service hebt gemaakt, is gebruikt. Navigeer naar uw Device Provisioning Service-exemplaar in de Azure Portal. Open de inschrijvingsgegevens voor de afzonderlijke inschrijving die u hebt gemaakt. U ziet dat de status van de inschrijving is **toegewezen** en dat de apparaat-id wordt weergegeven.

Gebruik de volgende opdrachten op uw apparaat om te controleren of de runtime is geïnstalleerd en gestart.

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

## <a name="next-steps"></a>Volgende stappen

Met het device provisioning service-inschrijvingsproces kunt u de apparaat-id en apparaattweetags instellen op hetzelfde moment dat u het nieuwe apparaat inrichten. U kunt deze waarden gebruiken voor afzonderlijke apparaten of groepen apparaten met behulp van automatisch apparaatbeheer. Meer informatie over [het implementeren en bewaken IoT Edge modules op schaal](how-to-deploy-at-scale.md) met behulp van de Azure Portal of met behulp van Azure [CLI.](how-to-deploy-cli-at-scale.md)
