---
title: Apparaten automatisch inrichten met DPS met behulp van X. 509-certificaten-Azure IoT Edge | Microsoft Docs
description: X. 509-certificaten gebruiken voor het testen van automatische inrichting van apparaten voor Azure IoT Edge met Device Provisioning Service
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 04/09/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: ee51b31246760e4619eef1e16e800b16ea886de0
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430710"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>Een IoT Edge apparaat maken en inrichten met X. 509-certificaten

Met [Azure IOT hub Device Provisioning Service (DPS)](../iot-dps/index.yml)kunt u IOT edge apparaten automatisch inrichten met X. 509-certificaten. Als u niet bekend bent met het proces van automatische inrichting, bekijkt u het overzicht voor [inrichting](../iot-dps/about-iot-dps.md#provisioning-process) voordat u verdergaat.

In dit artikel wordt beschreven hoe u een Device Provisioning Service-inschrijving maakt met behulp van X. 509-certificaten op een IoT Edge apparaat met de volgende stappen:

* Certificaten en sleutels genereren.
* Een afzonderlijke inschrijving voor een apparaat of een groeps registratie voor een set apparaten maken.
* Installeer de IoT Edge runtime en registreer het apparaat bij IoT Hub.

Het gebruik van X. 509-certificaten als Attestation-mechanisme is een uitstekende manier om productie te schalen en het inrichten van apparaten te vereenvoudigen. X. 509-certificaten worden meestal gerangschikt in een vertrouwens keten van certificaten. Als u begint met een zelfondertekend of een vertrouwd basis certificaat, ondertekent elk certificaat in de keten het volgende lagere certificaat. Met dit patroon maakt u via elk tussenliggend certificaat een overgedragen vertrouwens keten van het basis certificaat tot het uiteindelijke ' leaf '-certificaat dat op een apparaat is geïnstalleerd.

## <a name="prerequisites"></a>Vereisten

* Een actieve IoT Hub.
* Een fysiek of virtueel apparaat dat moet worden IoT Edge apparaat.
* De meest recente versie van [Git](https://git-scm.com/download/) is geïnstalleerd.
* Een exemplaar van de IoT Hub Device Provisioning Service in azure, gekoppeld aan uw IoT-hub.
  * Als u geen Device Provisioning service-exemplaar hebt, volgt u de instructies in [de IOT hub DPS instellen](../iot-dps/quick-setup-auto-provision.md).
  * Nadat u de Device Provisioning Service hebt uitgevoerd, kopieert u de waarde van **id-bereik** van de pagina overzicht. U gebruikt deze waarde bij het configureren van de IoT Edge runtime.

## <a name="generate-device-identity-certificates"></a>Certificaten voor apparaat-identiteiten genereren

Het certificaat van de apparaat-id is een Leaf-certificaat dat is verbonden via een vertrouwens keten van een certificaat aan het hoogste X. 509 Certificate Authority (CA)-certificaat. Het certificaat van de apparaat-id moet de algemene naam (CN) hebben die is ingesteld op de apparaat-ID die u op het apparaat in uw IoT-hub wilt hebben.

Certificaten voor apparaat-id's worden alleen gebruikt voor het inrichten van het IoT Edge apparaat en het verifiëren van het apparaat met Azure IoT Hub. Ze ondertekenen geen certificaten, in tegens telling tot de CA-certificaten die het IoT Edge apparaat aan modules of blad apparaten geeft voor verificatie. Zie [Azure IOT Edge details van certificaat gebruik](iot-edge-certs.md)voor meer informatie.

Nadat u het certificaat voor de apparaat-id hebt gemaakt, hebt u twee bestanden: een. CER-of. pem-bestand dat het open bare gedeelte van het certificaat bevat, en een. CER-of. pem-bestand met de persoonlijke sleutel van het certificaat. Als u groeps registratie in DPS wilt gebruiken, hebt u ook het open bare gedeelte van een tussenliggend of basis-CA-certificaat nodig in dezelfde certificaat keten van vertrouwen.

U hebt de volgende bestanden nodig om automatische inrichting in te stellen met X. 509:

* Het certificaat van de apparaat-id en het certificaat van de persoonlijke sleutel. Het certificaat van de apparaat-id wordt geüpload naar DPS als u een afzonderlijke inschrijving maakt. De persoonlijke sleutel wordt door gegeven aan de IoT Edge runtime.
* Een volledig keten certificaat dat ten minste de apparaat-id en de tussenliggende certificaten moet bevatten. Het volledige keten certificaat wordt door gegeven aan de IoT Edge runtime.
* Een tussenliggend of basis-CA-certificaat van de certificaat vertrouwens keten. Dit certificaat wordt geüpload naar DPS als u een groeps registratie maakt.

> [!NOTE]
> Op dit moment wordt een beperking in libiothsm voor komen dat certificaten worden gebruikt die op of na 1 januari 2038 verlopen.

### <a name="use-test-certificates"></a>Test certificaten gebruiken

Als u geen certificerings instantie beschikbaar hebt om nieuwe identiteits certificaten te maken en dit scenario wilt proberen, bevat de Azure IoT Edge Git-opslag plaats scripts die u kunt gebruiken voor het genereren van test certificaten. Deze certificaten zijn alleen bedoeld voor ontwikkelings tests en mogen niet worden gebruikt in de productie omgeving.

Als u test certificaten wilt maken, volgt u de stappen in [demo certificaten maken om IOT Edge apparaatfuncties te testen](how-to-create-test-certificates.md). Voltooi de twee vereiste secties voor het instellen van de scripts voor het genereren van certificaten en het maken van een basis-CA-certificaat. Volg vervolgens de stappen om een certificaat voor een apparaat-id te maken. Wanneer u klaar bent, moet u de volgende certificaat keten en sleutel paar hebben:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

U hebt beide certificaten nodig op het IoT Edge-apparaat. Als u afzonderlijke inschrijvingen wilt gebruiken in DPS, uploadt u het. cert. pem-bestand. Als u groeps registratie in DPS wilt gebruiken, hebt u ook een tussenliggend of basis-CA-certificaat nodig in de certificaat keten van de vertrouwens relatie die u wilt uploaden. Als u voorbeeld certificaten gebruikt, gebruikt u het `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` certificaat voor groeps registratie.

## <a name="create-a-dps-individual-enrollment"></a>Een individuele inschrijving voor DPS maken

Gebruik uw gegenereerde certificaten en sleutels voor het maken van een afzonderlijke inschrijving in DPS voor één IoT Edge apparaat. Individuele inschrijvingen maken het open bare deel van het identiteits certificaat van een apparaat en komen overeen met het certificaat op het apparaat.

Als u meerdere IoT Edge apparaten wilt inrichten, volgt u de stappen in de volgende sectie, [een registratie voor de DPS-groep maken](#create-a-dps-group-enrollment).

Wanneer u een inschrijving in DPS maakt, hebt u de mogelijkheid om een **eerste dubbele toestand** van het apparaat te declareren. In het dubbele apparaat kunt u Tags instellen om apparaten te groeperen op elke gewenste metrische waarde in uw oplossing, zoals regio, omgeving, locatie of apparaattype. Deze tags worden gebruikt voor het maken van [automatische implementaties](how-to-deploy-at-scale.md).

Zie [registratie van apparaten beheren](../iot-dps/how-to-manage-enrollments.md)voor meer informatie over inschrijvingen in de Device Provisioning Service.

   > [!TIP]
   > In de Azure CLI kunt u een [inschrijving](/cli/azure/ext/azure-iot/iot/dps/enrollment) of een [registratie groep](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) maken en de vlag voor **rand ingeschakeld** gebruiken om op te geven dat een apparaat, of groep apparaten, een IOT edge apparaat is.

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw exemplaar van IOT hub Device Provisioning Service.

1. Selecteer onder **instellingen** de optie **inschrijvingen beheren**.

1. Selecteer **Individuele inschrijving toevoegen** en voer de volgende stappen uit om de registratie te configureren:  

   * **Mechanisme**: Selecteer **X. 509**.

   * **Primair certificaat. pem-of CER-bestand**: upload het open bare bestand van het identiteits certificaat van het apparaat. Als u de scripts voor het genereren van een test certificaat hebt gebruikt, kiest u het volgende bestand:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **IOT hub apparaat-id**: Geef indien gewenst een id op voor het apparaat. U kunt apparaat-Id's gebruiken om een afzonderlijk apparaat te richten op het implementeren van een module. Als u geen apparaat-ID opgeeft, wordt de algemene naam (CN) in het X. 509-certificaat gebruikt.

   * **IOT edge apparaat**: Selecteer **waar** om te declareren dat de inschrijving voor een IOT edge apparaat is.

   * **Selecteer de IOT-hubs waaraan dit apparaat kan worden toegewezen**: Kies de gekoppelde IOT-hub waarmee u uw apparaat wilt verbinden. U kunt meerdere hubs kiezen en het apparaat wordt toegewezen aan een van deze op basis van het geselecteerde toewijzings beleid.

   * **Eerste dubbele toestand van apparaat**: Voeg een tag-waarde toe die moet worden toegevoegd aan het apparaat, net als u wilt. U kunt tags gebruiken om groepen apparaten te richten op automatische implementatie. Bijvoorbeeld:

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

Nu een inschrijving voor dit apparaat bestaat, kan de IoT Edge runtime automatisch het apparaat inrichten tijdens de installatie. Ga verder naar de sectie [de IOT Edge runtime installeren](#install-the-iot-edge-runtime) om uw IOT edge-apparaat in te stellen.

## <a name="create-a-dps-group-enrollment"></a>Inschrijving van een DPS-groep maken

Gebruik uw gegenereerde certificaten en sleutels voor het maken van een groeps registratie in DPS voor meerdere IoT Edge-apparaten. Groeps registraties maken gebruik van een tussenliggend of basis-CA-certificaat van de certificaat vertrouwens keten die wordt gebruikt voor het genereren van de afzonderlijke identiteits certificaten van het apparaat.

Als u in plaats daarvan een enkel IoT Edge apparaat wilt inrichten, volgt u de stappen in de vorige sectie en [maakt u een individuele inschrijving voor DPS](#create-a-dps-individual-enrollment).

Wanneer u een inschrijving in DPS maakt, hebt u de mogelijkheid om een **eerste dubbele toestand** van het apparaat te declareren. In het dubbele apparaat kunt u Tags instellen om apparaten te groeperen op elke gewenste metrische waarde in uw oplossing, zoals regio, omgeving, locatie of apparaattype. Deze tags worden gebruikt voor het maken van [automatische implementaties](how-to-deploy-at-scale.md).

### <a name="verify-your-root-certificate"></a>Uw basis certificaat verifiëren

Wanneer u een registratie groep maakt, hebt u de mogelijkheid om een geverifieerd certificaat te gebruiken. U kunt een certificaat met DPS verifiëren door te bewijzen dat u eigenaar bent van het basis certificaat. Zie voor meer informatie [bewijs materiaal voor X. 509 CA-certificaten](../iot-dps/how-to-verify-certificates.md).

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw exemplaar van IOT hub Device Provisioning Service.

1. **Certificaten** selecteren in het menu aan de linkerkant.

1. Selecteer **toevoegen** om een nieuw certificaat toe te voegen.

1. Voer een beschrijvende naam in voor het certificaat en blader vervolgens naar het CER-of PEM-bestand dat het open bare deel van het X. 509-certificaat vertegenwoordigt.

   Als u de demo certificaten gebruikt, uploadt u het `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` certificaat.

1. Selecteer **Opslaan**.

1. Uw certificaat moet nu worden weer gegeven op de pagina **certificaten** . Selecteer deze optie om de details van het certificaat te openen.

1. Selecteer **verificatie code genereren** en kopieer de gegenereerde code.

1. Of u nu uw eigen CA-certificaat of de demo certificaten gebruikt, u kunt het bewerkings programma dat is opgenomen in de IoT Edge-opslag plaats gebruiken om het bewijs van bezit te controleren. Het verificatie programma gebruikt uw CA-certificaat voor het ondertekenen van een nieuw certificaat met de gegeven verificatie code als onderwerpnaam.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Upload het zojuist gegenereerde verificatie certificaat op dezelfde pagina met certificaat details in de Azure Portal.

1. Selecteer **Verifiëren**.

### <a name="create-enrollment-group"></a>Registratie groep maken

Zie [registratie van apparaten beheren](../iot-dps/how-to-manage-enrollments.md)voor meer informatie over inschrijvingen in de Device Provisioning Service.

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw exemplaar van IOT hub Device Provisioning Service.

1. Selecteer onder **instellingen** de optie **inschrijvingen beheren**.

1. Selecteer **registratie groep toevoegen** en voer de volgende stappen uit om de registratie te configureren:

   * **Groeps naam**: Geef een naam op voor het onthouden van deze groeps registratie.

   * **Attestation-type**: Selecteer **certificaat**.

   * **IOT edge apparaat**: Selecteer **waar**. Voor de registratie van een groep moeten alle apparaten worden IoT Edge apparaten of geen van beide.

   * **Certificaat type**: Selecteer **CA-certificaat** als u een gecontroleerd CA-certificaat hebt opgeslagen bij DPS of het **tussenliggende certificaat** als u een nieuw bestand alleen voor deze inschrijving wilt uploaden.

   * **Primair certificaat**: als u in de laatste sectie het CA-certificaat hebt gekozen, kiest u uw certificaat in de vervolg keuzelijst. Als u een tussenliggend certificaat hebt gekozen, uploadt u het open bare bestand van een CA-certificaat in de certificaat keten van de vertrouwens relatie die is gebruikt voor het genereren van de certificaten van de identiteit van het apparaat.

   * **Selecteer de IOT-hubs waaraan dit apparaat kan worden toegewezen**: Kies de gekoppelde IOT-hub waarmee u uw apparaat wilt verbinden. U kunt meerdere hubs kiezen en het apparaat wordt toegewezen aan een van deze op basis van het geselecteerde toewijzings beleid.

   * **Eerste dubbele toestand van apparaat**: Voeg een tag-waarde toe die moet worden toegevoegd aan het apparaat, net als u wilt. U kunt tags gebruiken om groepen apparaten te richten op automatische implementatie. Bijvoorbeeld:

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

Nu een inschrijving voor dit apparaat bestaat, kan de IoT Edge runtime automatisch het apparaat inrichten tijdens de installatie. Ga door naar de volgende sectie om uw IoT Edge-apparaat in te stellen.

## <a name="install-the-iot-edge-runtime"></a>De IoT Edge-runtime installeren

De IoT Edge-runtime wordt op alle IoT Edge-apparaten geïmplementeerd. De onderdelen worden in containers uitgevoerd en bieden u de mogelijkheid om extra containers op het apparaat te implementeren, zodat u code aan de rand kunt uitvoeren.

Volg de stappen in [install the Azure IOT Edge runtime](how-to-install-iot-edge.md)en ga vervolgens terug naar dit artikel om het apparaat in te richten.

X. 509-inrichting met DPS wordt alleen ondersteund in IoT Edge versie 1.0.9 of nieuwer.

## <a name="configure-the-device-with-provisioning-information"></a>Het apparaat configureren met inrichtings gegevens

Zodra de runtime op uw apparaat is geïnstalleerd, configureert u het apparaat met de informatie die wordt gebruikt om verbinding te maken met Device Provisioning Service en IoT Hub.

De volgende informatie is gereed:

* De waarde voor het bereik van de DPS **-id** . U kunt deze waarde ophalen van de overzichts pagina van uw DPS-instantie in de Azure Portal.
* Het Chaining-bestand van het apparaat-ID certificaat op het apparaat.
* Het sleutel bestand van de apparaat-id op het apparaat.
* Een optionele registratie-ID. Als u niets opgeeft, wordt de ID opgehaald uit de algemene naam in het certificaat van de apparaat-id.

### <a name="linux-device"></a>Linux-apparaat

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
       method: "x509"
   #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

   Gebruik eventueel de `always_reprovision_on_startup` `dynamic_reprovisioning` regels of om het herinrichtings gedrag van uw apparaat te configureren. Als een apparaat is ingesteld op het opnieuw inrichten bij het opstarten, wordt altijd geprobeerd eerst met DPS in te richten en vervolgens terug te vallen naar de inrichtings back-up als dat mislukt. Als een apparaat is ingesteld op dynamisch opnieuw inrichten, wordt IoT Edge opnieuw opgestart en wordt het opnieuw ingericht als er een herinrichtings gebeurtenis wordt gedetecteerd. Zie IoT Hub voor het opnieuw [inrichten van apparaten](../iot-dps/concepts-device-reprovision.md)voor meer informatie.

1. De waarden van `scope_id` , `identity_cert` en `identity_pk` met uw DPS en apparaatgegevens bijwerken.

   Wanneer u het X. 509-certificaat en de sleutel gegevens toevoegt aan het bestand config. yaml, moeten de paden worden verstrekt als bestands-Uri's. Bijvoorbeeld:

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. Geef een `registration_id` voor het apparaat op als u wilt, of verlaat deze regel om het apparaat te registreren met de CN-naam van het identiteits certificaat.

1. Start de IoT Edge-runtime opnieuw zodat alle configuratie wijzigingen die u op het apparaat hebt aangebracht, worden opgehaald.

   ```bash
   sudo systemctl restart iotedge
   ```

### <a name="windows-device"></a>Windows-apparaat

1. Open een Power shell-venster in de beheerders modus. Zorg ervoor dat u een AMD64-sessie van Power shell gebruikt bij het installeren van IoT Edge, niet in Power shell (x86).

1. Met de opdracht **Initialize-IoTEdge** configureert u de IoT Edge-runtime op uw machine. De opdracht wordt standaard ingesteld op hand matig inrichten met Windows-containers. Gebruik daarom de `-DpsX509` vlag om automatische inrichting met X. 509-certificaat verificatie te gebruiken.

   Vervang de tijdelijke aanduidingen voor `{scope_id}` , `{identity cert chain path}` , en `{identity key path}` door de juiste waarden van uw DPS-exemplaar en de bestands paden op het apparaat.

   Voeg het toe `-RegistrationId {registration_id}` Als u de apparaat-id wilt instellen op een andere waarde dan de CN-naam van het identiteits certificaat.

   Voeg de `-ContainerOs Linux` para meter toe als u Linux-containers in Windows gebruikt.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >In het bestand config. yaml worden uw certificaat en sleutel gegevens opgeslagen als bestands-Uri's. De Initialize-IoTEdge-opdracht verwerkt echter deze opmaak stap voor u, zodat u het absolute pad naar het certificaat en de sleutel bestanden op uw apparaat kunt opgeven.

## <a name="verify-successful-installation"></a>Geslaagde installatie controleren

Als de runtime is gestart, kunt u naar uw IoT Hub gaan en IoT Edge modules op het apparaat implementeren.

U kunt controleren of de afzonderlijke registratie die u hebt gemaakt in Device Provisioning Service is gebruikt. Navigeer naar het Device Provisioning service-exemplaar in het Azure Portal. Open de inschrijvings gegevens voor de afzonderlijke inschrijving die u hebt gemaakt. U ziet dat de status van de registratie is **toegewezen** en dat de apparaat-id wordt vermeld.

Gebruik de volgende opdrachten op het apparaat om te controleren of de runtime is geïnstalleerd en is gestart.

### <a name="linux-device"></a>Linux-apparaat

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

## <a name="next-steps"></a>Volgende stappen

Met het inschrijvings proces voor Device Provisioning Service kunt u de apparaat-ID en de dubbele Tags van het apparaat instellen op hetzelfde moment als u het nieuwe apparaat inricht. U kunt deze waarden gebruiken om afzonderlijke apparaten of groepen apparaten te richten met behulp van automatische Apparaatbeheer. Meer informatie over [het implementeren en bewaken van IOT Edge modules op schaal met behulp van de Azure Portal](how-to-deploy-at-scale.md) of [met behulp van Azure cli](how-to-deploy-cli-at-scale.md).
