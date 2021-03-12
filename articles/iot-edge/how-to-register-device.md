---
title: Een nieuw apparaat registreren-Azure IoT Edge | Microsoft Docs
description: Registreer één IoT Edge apparaat in IoT Hub voor hand matige inrichting met symmetrische sleutels of X. 509-certificaten
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: d75f184a324a9d418b0af2e3cf5790205af0fa42
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200723"
---
# <a name="register-an-iot-edge-device-in-iot-hub"></a>Een IoT Edge apparaat registreren in IoT Hub

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Dit artikel bevat de stappen voor het registreren van een nieuw IoT Edge apparaat in IoT Hub.

Elk apparaat dat verbinding maakt met een IoT-hub heeft een apparaat-ID die wordt gebruikt voor het bijhouden van Cloud-naar-apparaat-en apparaat-naar-Cloud-communicatie. U configureert een apparaat met de verbindings gegevens, waaronder de hostnaam van de IoT-hub, de apparaat-ID en de informatie die het apparaat gebruikt om te verifiëren bij IoT Hub.

De stappen in dit artikel begeleiden u bij het proces hand matig inrichten, waarbij u één apparaat verbindt met de IoT-hub. Voor hand matige inrichting hebt u twee opties voor het verifiëren van IoT Edge apparaten:

* **Symmetrische sleutel**: wanneer u een nieuwe apparaat-id in IOT hub maakt, maakt de service twee sleutels. U plaatst een van de sleutels op het apparaat en geeft de sleutel aan IoT Hub bij het verifiëren.

  Deze verificatie methode is sneller om aan de slag te gaan, maar niet zo veilig.

* **X. 509 zelfondertekend**: u maakt twee X. 509-identiteits certificaten en plaatst deze op het apparaat. Wanneer u een nieuwe apparaat-id in IoT Hub maakt, geeft u de vinger afdrukken van beide certificaten. Wanneer het apparaat wordt geverifieerd bij IoT Hub, wordt er één certificaat weer gegeven en IoT Hub wordt gecontroleerd of het certificaat overeenkomt met de vinger afdruk.

  Deze verificatie methode is veiliger en aanbevolen voor productie scenario's.

In dit artikel worden beide verificatie methoden besproken.

Als u veel apparaten hebt die moeten worden ingesteld en u deze niet hand matig wilt inrichten, gebruikt u een van de volgende artikelen om te leren hoe IoT Edge werkt met de IoT Hub Device Provisioning Service:

* [IoT Edge apparaten maken en inrichten met X. 509-certificaten](how-to-auto-provision-x509-certs.md)
* [IoT Edge apparaten maken en inrichten met een TPM](how-to-auto-provision-simulated-device-linux.md)
* [IoT Edge apparaten maken en inrichten met behulp van symmetrische sleutels](how-to-auto-provision-symmetric-keys.md)

## <a name="prerequisites"></a>Vereisten

# <a name="portal"></a>[Portal](#tab/azure-portal)

Een gratis of standaard [IOT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Een gratis of standaard [IOT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IOT-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) voor Visual Studio code

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

* Een gratis of standaard [IOT-hub](../iot-hub/iot-hub-create-using-cli.md) in uw Azure-abonnement.
* [Azure cli](/cli/azure/install-azure-cli) in uw omgeving. Uw Azure CLI-versie moet mini maal 2.0.70 of nieuwer zijn. Gebruik `az --version` om de versie te valideren. In deze versie worden az-extensie-opdrachten ondersteund en is voor het eerst het Knack-opdrachtframework opgenomen.

---

## <a name="option-1-register-with-symmetric-keys"></a>Optie 1: registreren met symmetrische sleutels

U kunt verschillende hulpprogram ma's gebruiken om een nieuw IoT Edge apparaat te registreren in IoT Hub en de connection string op te halen, afhankelijk van uw voor keur.

# <a name="portal"></a>[Portal](#tab/azure-portal)

In uw IoT-hub in de Azure Portal worden IoT Edge apparaten afzonderlijk gemaakt en beheerd van IoT-apparaten die niet Edge zijn ingeschakeld.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en ga naar uw IoT Hub.

1. Selecteer in het linkerdeel venster **IOT Edge** in het menu en selecteer vervolgens **een IOT edge apparaat toevoegen**.

   ![Een IoT Edge apparaat toevoegen vanuit de Azure Portal](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Geef op de pagina **een apparaat maken** de volgende informatie op:

   * Een beschrijvende apparaat-ID maken.
   * Selecteer **symmetrische sleutel** als het verificatie type.
   * Gebruik de standaard instellingen voor het automatisch genereren van verificatie sleutels en het verbinden van het nieuwe apparaat met uw hub.

1. Selecteer **Opslaan**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>Meld u aan voor toegang tot uw IoT-hub

U kunt de Azure IoT-uitbrei dingen voor Visual Studio code gebruiken om bewerkingen uit te voeren met uw IoT-hub. Als u deze bewerkingen wilt gebruiken, moet u zich aanmelden bij uw Azure-account en uw hub selecteren.

1. Open in Visual Studio code de weer gave **Explorer** .
1. Vouw de sectie **Azure-IOT hub** aan de onderkant van de Explorer uit.

   ![Sectie Azure IoT Hub-apparaten uitvouwen](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Klik in de koptekst van de Azure- **IOT hub** op de sectie **..** .. Als u het weglatings teken niet ziet, klikt u op de koptekst of beweegt u de muis aanwijzer over de kop.
1. Kies **IOT hub selecteren**.
1. Als u niet bent aangemeld bij uw Azure-account, volgt u de aanwijzingen om dit te doen.
1. Selecteer uw Azure-abonnement.
1. Selecteer uw IoT-hub.

### <a name="register-a-new-device-with-visual-studio-code"></a>Een nieuw apparaat registreren met Visual Studio code

1. Vouw in Visual Studio code Explorer de sectie **Azure IOT hub** uit.
1. Klik in de koptekst van de Azure- **IOT hub** op de sectie **..** .. Als u het weglatings teken niet ziet, klikt u op de koptekst of beweegt u de muis aanwijzer over de kop.
1. Selecteer **IOT edge apparaat maken**.
1. Geef in het tekstvak dat wordt geopend, een ID op voor uw apparaat.

In het uitvoer scherm ziet u het resultaat van de opdracht. De apparaatgegevens worden afgedrukt, inclusief de **deviceId** die u hebt geleverd en de **Connections Tring** die u kunt gebruiken om uw fysieke apparaat te verbinden met uw IOT-hub.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de opdracht [AZ IOT hub apparaat-Identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) om een nieuwe apparaat-id te maken in uw IOT-hub. Bijvoorbeeld:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Deze opdracht omvat drie para meters:

* `--device-id` of `-d` : Geef een beschrijvende naam op die uniek is binnen uw IOT-hub.
* `--hub-name` of `-n` : Geef de naam op van uw IOT-hub.
* `--edge-enabled` of `--ee` : Declareer dat het apparaat een IOT edge apparaat is.

   ![AZ IOT hub apparaat-Identity Create output](./media/how-to-register-device/create-edge-device-cli.png)

---

Nu u een apparaat hebt geregistreerd in IoT Hub, haalt u de connection string op die u gebruikt voor het volt ooien van de installatie en het inrichten van de IoT Edge runtime. Volg de stappen verderop in dit artikel om [geregistreerde apparaten weer te geven en verbindings reeksen](#view-registered-devices-and-retrieve-connection-strings)op te halen.

## <a name="option-2-register-with-x509-certificates"></a>Optie 2: registreren bij X. 509-certificaten

Voor hand matige inrichting met X. 509-certificaten is IoT Edge versie 1.0.10 of nieuwer vereist.

Voor X. 509-certificaat authenticatie wordt de verificatie gegevens van elk apparaat verstrekt in de vorm van de *vinger afdrukken* die zijn gemaakt op basis van de identiteits certificaten van uw apparaat. Deze vinger afdrukken worden IoT Hub op het moment van de registratie van het apparaat, zodat de service het apparaat kan herkennen wanneer het verbinding maakt.

### <a name="create-certificates-and-thumbprints"></a>Certificaten en vinger afdrukken maken

Wanneer u een IoT Edge apparaat inricht met X. 509-certificaten, gebruikt u wat een *apparaat-identiteits certificaat* wordt genoemd. Dit certificaat wordt alleen gebruikt voor het inrichten van een IoT Edge apparaat en het verifiëren van het apparaat met Azure IoT Hub. Het is een Leaf-certificaat dat geen andere certificaten ondertekent. Het certificaat van de apparaat-id is gescheiden van de certificaten van de certificerings instantie (CA) die het IoT Edge apparaat aan modules of downstream-apparaten geeft voor verificatie. Zie [begrijpen hoe Azure IOT Edge certificaten gebruikt](iot-edge-certs.md)voor meer informatie over hoe de CA-certificaten worden gebruikt in IOT edge apparaten.

U hebt de volgende bestanden nodig voor hand matige inrichting met X. 509:

* Twee certificaten met apparaat-id's met bijbehorende certificaten voor persoonlijke sleutels in CER-of PEM-indelingen.

  Er wordt één set certificaat/sleutel bestanden aan de IoT Edge-runtime gegeven. Wanneer u certificaten voor apparaat-id's maakt, stelt u de algemene naam (CN) van het certificaat in met de apparaat-ID die u op het apparaat wilt hebben in uw IoT-hub.

* Vinger afdrukken van certificaten voor apparaat-id's.

  Vingerafdruk waarden zijn 40-Hex-tekens voor SHA-1-hashes of 64-Hex-tekens voor SHA-256-hashes. Beide vinger afdrukken worden IoT Hub op het moment van de registratie van het apparaat.

Als er geen certificaten beschikbaar zijn, kunt u [demo certificaten maken om IOT Edge apparaatfuncties te testen](how-to-create-test-certificates.md). Volg de instructies in dit artikel voor het instellen van scripts voor het maken van certificaten, het maken van een basis-CA-certificaat en het maken van twee IoT Edge certificaten voor apparaat-id's.

Een van de manieren om de vinger afdruk op te halen uit een certificaat is met de volgende openssl-opdracht:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

### <a name="register-a-new-device"></a>Een nieuw apparaat registreren

U kunt verschillende hulpprogram ma's gebruiken om een nieuw IoT Edge apparaat te registreren in IoT Hub en de certificaat vingerafdrukken te uploaden.

# <a name="portal"></a>[Portal](#tab/azure-portal)

In uw IoT-hub in de Azure Portal worden IoT Edge apparaten afzonderlijk gemaakt en beheerd van IoT-apparaten die niet Edge zijn ingeschakeld.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en ga naar uw IoT Hub.

1. Selecteer in het linkerdeel venster **IOT Edge** in het menu en selecteer vervolgens **een IOT edge apparaat toevoegen**.

   ![Een IoT Edge apparaat toevoegen vanuit de Azure Portal](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Geef op de pagina **een apparaat maken** de volgende informatie op:

   * Een beschrijvende apparaat-ID maken. Noteer deze apparaat-ID, zoals u deze in de volgende sectie gebruikt.
   * Selecteer **X. 509 zelf ondertekend** als verificatie type.
   * Geef de primaire en secundaire vinger afdrukken voor identiteits certificaten op. Vingerafdruk waarden zijn 40-Hex-tekens voor SHA-1-hashes of 64-Hex-tekens voor SHA-256-hashes.

1. Selecteer **Opslaan**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

De Azure IoT-extensie voor Visual Studio code biedt momenteel geen ondersteuning voor apparaatregistratie met X. 509-certificaten.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de opdracht [AZ IOT hub apparaat-Identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) om een nieuwe apparaat-id te maken in uw IOT-hub. Bijvoorbeeld:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Deze opdracht bevat verschillende para meters:

* `--device-id` of `-d` : Geef een beschrijvende naam op die uniek is voor uw IOT-hub. Noteer deze apparaat-ID, zoals u deze in de volgende sectie gebruikt.
* `hub-name` of `-n` : Geef de naam op van uw IOT-hub.
* `--edge-enabled` of `--ee` : Declareer dat het apparaat een IOT edge apparaat is.
* `--auth-method` of `--am` : Declareer het autorisatie type dat het apparaat gaat gebruiken. In dit geval gebruiken we X. 509-certificaat vingerafdrukken.
* `--primary-thumbprint` of `--ptp` : Geef een vinger afdruk van een X. 509-certificaat op om te gebruiken als primaire sleutel.
* `--secondary-thumbprint` of `--stp` : Geef een vinger afdruk van een X. 509-certificaat op dat als secundaire sleutel moet worden gebruikt.

---

Nu u een apparaat hebt geregistreerd in IoT Hub, kunt u de IoT Edge runtime installeren en inrichten op het apparaat. IoT Edge-apparaten die worden geverifieerd met X. 509-certificaten gebruiken geen verbindings reeksen, dus u kunt door gaan met de volgende stap:

* [Azure IoT Edge voor Linux installeren of verwijderen](how-to-install-iot-edge.md)
* [Azure IoT Edge voor Windows installeren of verwijderen](how-to-install-iot-edge-windows-on-windows.md)

## <a name="view-registered-devices-and-retrieve-connection-strings"></a>Geregistreerde apparaten weer geven en verbindings reeksen ophalen

Apparaten die gebruikmaken van symmetrische sleutel verificatie hebben hun verbindings reeksen nodig om de installatie en inrichting van de IoT Edge-runtime te volt ooien.

Voor apparaten die gebruikmaken van X. 509-certificaat verificatie zijn geen verbindings reeksen nodig. In plaats daarvan hebben deze apparaten de naam van de IoT-hub, de apparaatnaam en hun certificaat bestanden nodig om de installatie en het inrichten van de IoT Edge-runtime te volt ooien.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Alle apparaten met rand mogelijkheden die verbinding maken met uw IoT-hub, worden weer gegeven op de pagina **IOT Edge** .

![De Azure Portal gebruiken om alle IoT Edge apparaten in uw IoT-hub weer te geven](./media/how-to-register-device/portal-view-devices.png)

Wanneer u klaar bent om uw apparaat in te stellen, hebt u de connection string nodig die het fysieke apparaat koppelt aan de identiteit in de IoT-hub.

Voor apparaten die worden geverifieerd met symmetrische sleutels, zijn de verbindings reeksen beschikbaar voor het kopiëren in de portal.

1. Klik op de pagina **IOT Edge** in de portal op de apparaat-id in de lijst met IOT edge apparaten.
2. Kopieer de waarde van de **primaire verbindings reeks** of **secundaire verbindings reeks**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>IoT Edge apparaten weer geven met Visual Studio code

Alle apparaten die verbinding maken met uw IoT-hub, worden weer gegeven in de sectie **Azure IOT hub** van Visual Studio code Explorer. IoT Edge apparaten zijn onderscheiden van niet-rand apparaten met een ander pictogram, en het feit dat de **$edgeAgent** en **$edgeHub** modules op elk IOT edge apparaat worden geïmplementeerd.

![VS code gebruiken om alle IoT Edge apparaten in uw IoT-hub weer te geven](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>De connection string ophalen met Visual Studio code

Wanneer u klaar bent om uw apparaat in te stellen, hebt u de connection string nodig die het fysieke apparaat koppelt aan de identiteit in de IoT-hub.

1. Klik met de rechter muisknop op de ID van uw apparaat in het gedeelte **Azure IOT hub** .
1. Selecteer **apparaat verbindings reeks kopiëren**.

   De connection string wordt naar het klem bord gekopieerd.

U kunt ook **apparaatgegevens ophalen** selecteren in het menu met de rechter muisknop om alle apparaatgegevens, inclusief de Connection String, weer te geven in het uitvoer venster.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>IoT Edge apparaten weer geven met de Azure CLI

Gebruik de opdracht [AZ IOT hub Device-Identity List](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) om alle apparaten in uw IOT-hub weer te geven. Bijvoorbeeld:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Elk apparaat dat is geregistreerd als IoT Edge apparaat heeft de eigenschaps **mogelijkheden. iotEdge** ingesteld op **True**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>De connection string ophalen met de Azure CLI

Wanneer u klaar bent om uw apparaat in te stellen, hebt u de connection string nodig die het fysieke apparaat koppelt aan de identiteit in de IoT-hub. Gebruik de opdracht [AZ IOT hub apparaat-Identity Connection-String show](/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string#ext_azure_iot_az_iot_hub_device_identity_connection_string_show) om de Connection String voor één apparaat te retour neren:

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

>[!TIP]
>De `connection-string show` opdracht is geïntroduceerd in versie 0.9.8 van de Azure IOT-extensie, waarbij de afgeschafte opdracht wordt vervangen `show-connection-string` . Als er een fout optreedt bij het uitvoeren van deze opdracht, moet u ervoor zorgen dat de extensie versie is bijgewerkt naar 0.9.8 of hoger. Zie [Microsoft Azure IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension)voor meer informatie en de meest recente updates.

De waarde voor de `device-id` para meter is hoofdletter gevoelig.

Wanneer u de connection string kopieert om op een apparaat te gebruiken, neemt u de aanhalings tekens rond de connection string niet op.

---

## <a name="next-steps"></a>Volgende stappen

Nu u een apparaat hebt geregistreerd in IoT Hub, kunt u de IoT Edge runtime installeren en inrichten op het apparaat.

* [Azure IoT Edge voor Linux installeren of verwijderen](how-to-install-iot-edge.md)
* [Azure IoT Edge voor Windows installeren of verwijderen](how-to-install-iot-edge-windows-on-windows.md)