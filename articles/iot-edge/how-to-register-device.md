---
title: Een nieuw apparaat registreren - Azure IoT Edge | Microsoft Docs
description: Een apparaat met één IoT Edge registreren in IoT Hub voor handmatige inrichting met ofwel symmetrische sleutels of X.509-certificaten
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: b5d761cfa947b3fd4e5f718e603219c650e8dd72
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481868"
---
# <a name="register-an-iot-edge-device-in-iot-hub"></a>Een apparaat IoT Edge registreren in IoT Hub

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Dit artikel bevat de stappen voor het registreren van een IoT Edge apparaat in IoT Hub.

Elk apparaat dat verbinding maakt met een IoT-hub heeft een apparaat-id die wordt gebruikt voor het bijhouden van cloud-naar-apparaat- of apparaat-naar-cloud-communicatie. U configureert een apparaat met de verbindingsgegevens, waaronder de hostnaam van de IoT-hub, de apparaat-id en de informatie die het apparaat gebruikt om te verifiëren IoT Hub.

De stappen in dit artikel doorlopen een proces met de naam handmatige inrichting, waarbij u één apparaat verbindt met de IoT-hub. Voor handmatige inrichting hebt u twee opties voor het IoT Edge apparaten:

* **Symmetrische sleutel:** wanneer u een nieuwe apparaat-id maakt in IoT Hub, maakt de service twee sleutels. U kunt een van de sleutels op het apparaat plaatsen en de sleutel wordt aan IoT Hub tijdens de authenticatie.

  Deze verificatiemethode is sneller om aan de slag te gaan, maar niet zo veilig.

* **X.509 zelf-ondertekend:** u maakt twee X.509-identiteitscertificaten en plaats deze op het apparaat. Wanneer u een nieuwe apparaat-id maakt in IoT Hub, geeft u de vingerafdruk van beide certificaten op. Wanneer het apparaat wordt geverifieerd voor IoT Hub, wordt er één certificaat IoT Hub controleert of het certificaat overeenkomt met de vingerafdruk.

  Deze verificatiemethode is veiliger en wordt aanbevolen voor productiescenario's.

In dit artikel worden beide verificatiemethoden beschreven.

Als u veel apparaten moet instellen en deze niet handmatig wilt inrichten, gebruikt u een van de volgende artikelen om te leren hoe IoT Edge werkt met de IoT Hub Device Provisioning Service:

* [Apparaten maken en IoT Edge met behulp van X.509-certificaten](how-to-auto-provision-x509-certs.md)
* [Apparaten met IoT Edge maken en inrichten met een TPM](how-to-auto-provision-simulated-device-linux.md)
* [Symmetrische sleutels IoT Edge apparaten maken en inrichten](how-to-auto-provision-symmetric-keys.md)

## <a name="prerequisites"></a>Vereisten

# <a name="portal"></a>[Portal](#tab/azure-portal)

Een gratis of standaard [IoT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Een gratis of standaard [IoT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) voor Visual Studio Code

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

* Een gratis of standaard [IoT-hub](../iot-hub/iot-hub-create-using-cli.md) in uw Azure-abonnement.
* [Azure CLI](/cli/azure/install-azure-cli) in uw omgeving. Uw Azure CLI-versie moet minimaal 2.0.70 of hoger zijn. Gebruik `az --version` om de versie te valideren. In deze versie worden az-extensie-opdrachten ondersteund en is voor het eerst het Knack-opdrachtframework opgenomen.

---

## <a name="option-1-register-with-symmetric-keys"></a>Optie 1: Registreren met symmetrische sleutels

U kunt verschillende hulpprogramma's gebruiken om een nieuw apparaat IoT Edge registreren in IoT Hub en de connection string ophalen, afhankelijk van uw voorkeur.

# <a name="portal"></a>[Portal](#tab/azure-portal)

In uw IoT-hub in de Azure Portal worden IoT Edge afzonderlijk gemaakt en beheerd van IoT-apparaten die niet aan de rand zijn ingeschakeld.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en ga naar uw IoT Hub.

1. Selecteer in het linkerdeelvenster **IoT Edge** in het menu en selecteer vervolgens **Een apparaat IoT Edge toevoegen.**

   ![Een IoT Edge toevoegen vanuit het Azure Portal](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Geef op **de pagina Een apparaat** maken de volgende informatie op:

   * Maak een beschrijvende apparaat-id.
   * Selecteer **Symmetrische sleutel** als verificatietype.
   * Gebruik de standaardinstellingen om automatisch verificatiesleutels te genereren en het nieuwe apparaat te verbinden met uw hub.

1. Selecteer **Opslaan**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>Aanmelden voor toegang tot uw IoT-hub

U kunt de Azure IoT-extensies voor Visual Studio Code gebruiken om bewerkingen uit te voeren met uw IoT-hub. Om deze bewerkingen te laten werken, moet u zich aanmelden bij uw Azure-account en uw hub selecteren.

1. Open in Visual Studio Code de **weergave Explorer.**
1. Vouw onderaan de Explorer de sectie **Azure IoT Hub** uit.

   ![Vouw de Azure IoT Hub Apparaten uit](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Klik op de **...** in **de Azure IoT Hub** sectiekop. Als u het beletselteken niet ziet, klikt u op of beweegt u de muisaanwijzer over de koptekst.
1. Kies **Selecteren IoT Hub**.
1. Als u niet bent aangemeld bij uw Azure-account, volgt u de aanwijzingen om dit te doen.
1. Selecteer uw Azure-abonnement.
1. Selecteer uw IoT-hub.

### <a name="register-a-new-device-with-visual-studio-code"></a>Een nieuw apparaat registreren met Visual Studio Code

1. Vouw in Visual Studio Code Explorer de sectie **Azure IoT Hub** uit.
1. Klik op de **...** in **de Azure IoT Hub** sectiekop. Als u het beletselteken niet ziet, klikt u op of beweegt u de muisaanwijzer over de koptekst.
1. Selecteer **Apparaat IoT Edge maken.**
1. Geef in het tekstvak dat wordt geopend een id op voor uw apparaat.

In het uitvoerscherm ziet u het resultaat van de opdracht . De apparaatgegevens worden afgedrukt, waaronder de **deviceId** die u hebt opgegeven en de **connectionString** die u kunt gebruiken om uw fysieke apparaat te verbinden met uw IoT-hub.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de [opdracht az iot hub device-identity create](/cli/azure/iot/hub/device-identity) om een nieuwe apparaat-id te maken in uw IoT-hub. Bijvoorbeeld:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Deze opdracht bevat drie parameters:

* `--device-id` of `-d` : Geef een beschrijvende naam op die uniek is binnen uw IoT-hub.
* `--hub-name` of `-n` : Geef de naam van uw IoT-hub op.
* `--edge-enabled` of `--ee` : declareer dat het apparaat een IoT Edge is.

   ![az iot hub device-identity create output](./media/how-to-register-device/create-edge-device-cli.png)

---

Nu u een apparaat hebt geregistreerd in IoT Hub, haalt u de connection string op die u gebruikt om de installatie en inrichting van de IoT Edge voltooien. Volg de stappen verder in dit artikel om geregistreerde apparaten [weer te zien en verbindingsreeksen op te halen.](#view-registered-devices-and-retrieve-connection-strings)

## <a name="option-2-register-with-x509-certificates"></a>Optie 2: Registreren met X.509-certificaten

Handmatig inrichten met X.509-certificaten vereist IoT Edge versie 1.0.10 of hoger.

Voor X.509-certificaatverificatie worden de verificatiegegevens van elk apparaat opgegeven in de vorm van vingerafdrukgegevens die afkomstig zijn uit de *identiteitscertificaten* van uw apparaat. Deze vingerafdruk wordt aan de IoT Hub het moment van de apparaatregistratie, zodat de service het apparaat kan herkennen wanneer het verbinding maakt.

### <a name="create-certificates-and-thumbprints"></a>Certificaten en vingerafdruk maken

Wanneer u een apparaat IoT Edge X.509-certificaten inrichten, gebruikt u een zogenaamd *apparaat-id-certificaat.* Dit certificaat wordt alleen gebruikt voor het inrichten van IoT Edge apparaat en het authenticeren van het apparaat met Azure IoT Hub. Het is een gewoon certificaat dat geen andere certificaten ondertekent. Het apparaatidentiteitscertificaat staat los van de certificaten van de certificeringsinstantie (CA) die het IoT Edge voor verificatie aan modules of downstreamapparaten presenteert. Zie Begrijpen hoe Azure IoT Edge certificaten gebruikt voor meer informatie over hoe de CA-certificaten worden gebruikt in [IoT Edge apparaten.](iot-edge-certs.md)

U hebt de volgende bestanden nodig voor handmatige inrichting met X.509:

* Twee certificaten voor apparaat-id's met de bijbehorende persoonlijke-sleutelcertificaten in .cer- of .pem-indelingen.

  Er wordt één set certificaat-/sleutelbestanden verstrekt aan de IoT Edge runtime. Wanneer u apparaat-id-certificaten maakt, stelt u de algemene naam (CN) van het certificaat in met de apparaat-id die u wilt dat het apparaat in uw IoT-hub heeft.

* Vingerafdruk van beide apparaat-id-certificaten.

  Vingerafdrukwaarden zijn 40 hexjx tekens voor SHA-1-hashes of 64-hexx-tekens voor SHA-256-hashes. Beide vingerafdrukgegevens worden ter IoT Hub het moment van apparaatregistratie verstrekt.

Als u geen certificaten beschikbaar hebt, kunt u democertificaten maken om de functies van [IoT Edge testen.](how-to-create-test-certificates.md) Volg de instructies in dat artikel om scripts voor het maken van certificaten in te stellen, een basis-CA-certificaat te maken en vervolgens twee certificaten IoT Edge apparaatidentiteit te maken.

Een manier om de vingerafdruk van een certificaat op te halen, is met de volgende openssl-opdracht:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

### <a name="register-a-new-device"></a>Een nieuw apparaat registreren

U kunt verschillende hulpprogramma's gebruiken om een nieuw apparaat IoT Edge registreren in IoT Hub en de vingerafdruk van het certificaat te uploaden.

# <a name="portal"></a>[Portal](#tab/azure-portal)

In uw IoT-hub in de Azure Portal worden IoT Edge gemaakt en afzonderlijk beheerd van IoT-apparaten die niet aan de rand zijn ingeschakeld.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en ga naar uw IoT Hub.

1. Selecteer in het linkerdeelvenster **IoT Edge** in het menu en selecteer vervolgens **Een apparaat IoT Edge toevoegen.**

   ![Een apparaat IoT Edge toevoegen vanuit het Azure Portal](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Geef op **de pagina** Een apparaat maken de volgende informatie op:

   * Maak een beschrijvende apparaat-id. Noteer deze apparaat-id, want u gebruikt deze in de volgende sectie.
   * Selecteer **X.509 Self-Signed** als verificatietype.
   * Geef de vingerafdruk van het primaire en secundaire identiteitscertificaat op. Vingerafdrukwaarden zijn 40 hexjxe tekens voor SHA-1-hashes of 64-hexx-tekens voor SHA-256-hashes.

1. Selecteer **Opslaan**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Momenteel biedt de Azure IoT-extensie voor Visual Studio Code geen ondersteuning voor apparaatregistratie met X.509-certificaten.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de [opdracht az iot hub device-identity create](/cli/azure/iot/hub/device-identity) om een nieuwe apparaat-id te maken in uw IoT-hub. Bijvoorbeeld:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Deze opdracht bevat verschillende parameters:

* `--device-id` of `-d` : Geef een beschrijvende naam op die uniek is voor uw IoT-hub. Noteer deze apparaat-id, want u gebruikt deze in de volgende sectie.
* `hub-name` of `-n` : Geef de naam van uw IoT-hub op.
* `--edge-enabled` of `--ee` : declareer dat het apparaat een IoT Edge is.
* `--auth-method` of `--am` : declareer het autorisatietype dat het apparaat gaat gebruiken. In dit geval gebruiken we X.509-certificaatvingerafdrukken.
* `--primary-thumbprint` of `--ptp` : geef een X.509-certificaatvingerafdruk op die als primaire sleutel moet worden gebruikt.
* `--secondary-thumbprint` of `--stp` : Geef een X.509-certificaatvingerafdruk op die u als secundaire sleutel wilt gebruiken.

---

Nu u een apparaat hebt geregistreerd in IoT Hub, kunt u de runtime van IoT Edge installeren en inrichten op uw apparaat. IoT Edge apparaten die worden geverifieerd met X.509-certificaten geen verbindingsreeksen gebruiken, kunt u doorgaan met de volgende stap:

* [Een linux-Azure IoT Edge installeren of verwijderen](how-to-install-iot-edge.md)
* [Een windows-Azure IoT Edge installeren of verwijderen](how-to-install-iot-edge-windows-on-windows.md)

## <a name="view-registered-devices-and-retrieve-connection-strings"></a>Geregistreerde apparaten weergeven en verbindingsreeksen ophalen

Apparaten die gebruikmaken van verificatie met een symmetrische sleutel, hebben hun verbindingsreeksen nodig om de installatie en inrichting van de IoT Edge te voltooien.

Apparaten die gebruikmaken van X.509-certificaatverificatie, hebben geen verbindingsreeksen nodig. In plaats daarvan hebben deze apparaten hun naam van de IoT-hub, hun apparaatnaam en hun certificaatbestanden nodig om de installatie en inrichting van de IoT Edge voltooien.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Alle edge-apparaten die verbinding maken met uw IoT-hub worden weergegeven op de **pagina IoT Edge** edge.

![Gebruik de Azure Portal om alle apparaten IoT Edge in uw IoT-hub te bekijken](./media/how-to-register-device/portal-view-devices.png)

Wanneer u klaar bent om uw apparaat in te stellen, hebt u de connection string nodig die uw fysieke apparaat koppelt aan de identiteit ervan in de IoT-hub.

Apparaten die worden geverifieerd met symmetrische sleutels, hebben hun verbindingsreeksen die in de portal kunnen worden kopiëren.

1. Klik op **IoT Edge** pagina in de portal op de apparaat-id in de lijst IoT Edge apparaten.
2. Kopieer de waarde van primaire **verbindingsreeks of** **secundaire verbindingsreeks.**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Uw IoT Edge weergeven met Visual Studio Code

Alle apparaten die verbinding maken met uw IoT-hub worden vermeld in de sectie Azure IoT Hub van de Visual Studio Code Explorer.  IoT Edge apparaten zijn te onderscheiden van niet-Edge-apparaten met een ander pictogram en het feit dat de **$edgeAgent-** en **$edgeHub-modules** op elk apparaat IoT Edge geïmplementeerd.

![VS Code gebruiken om alle apparaten in IoT Edge IoT-hub weer te geven](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>De connection string ophalen met Visual Studio Code

Wanneer u klaar bent om uw apparaat in te stellen, hebt u de connection string die uw fysieke apparaat koppelt aan de identiteit ervan in de IoT-hub.

1. Klik met de rechtermuisknop op de id van uw apparaat in **Azure IoT Hub** sectie.
1. Selecteer **Apparaatverbindingsreeks kopiëren.**

   De connection string wordt gekopieerd naar het klembord.

U kunt apparaatgegevens **ook selecteren** in het snelmenu om alle apparaatgegevens, inclusief de connection string, in het uitvoervenster weer te geven.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Uw IoT Edge weergeven met de Azure CLI

Gebruik de [opdracht az iot hub device-identity list om](/cli/azure/iot/hub/device-identity) alle apparaten in uw IoT Hub weer te geven. Bijvoorbeeld:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Voor elk apparaat dat is geregistreerd als IoT Edge apparaat, wordt de eigenschap **capabilities.iotEdge** ingesteld op **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>De connection string ophalen met de Azure CLI

Wanneer u klaar bent om uw apparaat in te stellen, hebt u de connection string die uw fysieke apparaat koppelt aan de identiteit ervan in de IoT-hub. Gebruik de [opdracht az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string) om de connection string voor één apparaat te retourneren:

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

>[!TIP]
>De `connection-string show` opdracht is geïntroduceerd in versie 0.9.8 van de Azure IoT-extensie, en vervangt de afgeschafte `show-connection-string` opdracht. Als er een fout wordt weergegeven bij het uitvoeren van deze opdracht, moet u ervoor zorgen dat uw extensieversie is bijgewerkt naar 0.9.8 of hoger. Zie [IoT-extensie](https://github.com/Azure/azure-iot-cli-extension)Microsoft Azure Azure CLI voor meer informatie en de meest recente updates.

De waarde voor de `device-id` parameter is casegevoelig.

Wanneer u de connection string op een apparaat kopieert, moet u de aanhalingstekens rond de connection string.

---

## <a name="next-steps"></a>Volgende stappen

Nu u een apparaat hebt geregistreerd in IoT Hub, kunt u de runtime van IoT Edge installeren en inrichten op uw apparaat.

* [Een linux-Azure IoT Edge installeren of verwijderen](how-to-install-iot-edge.md)
* [Een windows-Azure IoT Edge installeren of verwijderen](how-to-install-iot-edge-windows-on-windows.md)