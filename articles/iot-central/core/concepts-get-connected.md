---
title: Connectiviteit van apparaten in azure IoT Central | Microsoft Docs
description: In dit artikel vindt u belang rijke concepten met betrekking tot de connectiviteit van apparaten in azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 1/15/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
- device-developer
ms.openlocfilehash: dc0655aba424d29a4055f0d50a20057f22d084ed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015452"
---
# <a name="get-connected-to-azure-iot-central"></a>Verbinding maken met Azure IoT Central

*Dit artikel is van toepassing op Opera tors en ontwikkel aars van apparaten.*

In dit artikel wordt beschreven hoe apparaten verbinding maken met een Azure IoT Central-toepassing. Voordat een apparaat gegevens kan uitwisselen met IoT Central, moet het:

- *Verifiëren*. Verificatie met de IoT Central-toepassing gebruikt ofwel een _SAS-token (Shared Access Signature)_ of een _X. 509-certificaat_. X. 509-certificaten worden aanbevolen in productie omgevingen.
- *Registreren*. Apparaten moeten zijn geregistreerd bij de IoT Central-toepassing. U kunt geregistreerde apparaten weer geven op de pagina **apparaten** in de toepassing.
- *Koppelen aan een sjabloon voor een apparaat*. In een IoT Central-toepassing definiëren Apparaatbeheer de gebruikers interface die Opera tors gebruiken om verbonden apparaten weer te geven en te beheren.

IoT Central ondersteunt de volgende scenario's voor registratie van twee apparaten:

- *Automatische registratie*. Het apparaat wordt automatisch geregistreerd wanneer het voor het eerst verbinding maakt. Met dit scenario kunnen Oem's apparaten massaal produceren die verbinding kunnen maken zonder eerst te worden geregistreerd. Een OEM genereert de juiste referenties voor het apparaat en configureert de apparaten in de fabriek. U kunt eventueel een operator vereisen om het apparaat goed te keuren voordat de gegevens worden verzonden. Voor dit scenario moet u een X. 509-of SAS- _groeps registratie_ configureren in uw toepassing.
- *Hand matige registratie*. Opera tors registreren afzonderlijke apparaten op de pagina **apparaten** of [importeren een CSV-bestand](howto-manage-devices.md#import-devices) om apparaten bulksgewijs te registreren. In dit scenario kunt u de registratie van X. 509-of SAS- _groep_ of x. 509-of SAS- _afzonderlijke inschrijving_ gebruiken.

Apparaten die verbinding maken met IoT Central moeten de *IoT Plug en Play-conventies* volgen. Een van deze conventies is dat een apparaat de _model-id_ moet verzenden van het model dat het apparaat implementeert wanneer het verbinding maakt. Met de model-ID kan de IoT Central toepassing het apparaat koppelen aan de juiste Device-sjabloon.

IoT Central maakt gebruik van de [Azure IOT hub Device Provisioning-Service (DPS)](../../iot-dps/about-iot-dps.md) om het verbindings proces te beheren. Een apparaat maakt eerst verbinding met een DPS-eind punt om de informatie op te halen die nodig is om verbinding te maken met uw toepassing. Intern gebruikt uw IoT Central-toepassing een IoT-hub voor het afhandelen van connectiviteit met apparaten. Met DPS kunt u het volgende doen:

- IoT Central voor het ondersteunen van onboarding en op schaal verbinding van apparaten.
- U kunt de referenties van het apparaat genereren en de apparaten offline configureren zonder de apparaten te registreren via IoT Central-gebruikersinterface.
- U kunt uw eigen apparaat-id's gebruiken om apparaten te registreren in IoT Central. Het gebruik van uw eigen apparaat-id's vereenvoudigt de integratie met bestaande back-officesystemen.
- Een enkele, consistente manier om apparaten te verbinden met IoT Central.

In dit artikel worden de volgende stappen voor het verbinden van apparaten beschreven:

- [X. 509-groeps registratie](#x509-group-enrollment)
- [Registratie van SAS-groep](#sas-group-enrollment)
- [Individuele inschrijving](#individual-enrollment)
- [Apparaatregistratie](#device-registration)
- [Een apparaat koppelen aan een apparaatprofiel](#associate-a-device-with-a-device-template)

## <a name="x509-group-enrollment"></a>X. 509-groeps registratie

In een productie omgeving, met behulp van X. 509-certificaten is het aanbevolen mechanisme voor verificatie van apparaten voor IoT Central. Zie voor meer informatie [apparaat-verificatie met behulp van X. 509 CA-certificaten](../../iot-hub/iot-hub-x509ca-overview.md).

Een apparaat met een X. 509-certificaat verbinden met uw toepassing:

1. Maak een *registratie groep* die gebruikmaakt van het Attestation-type **certificaten (X. 509)** .
1. Een tussenliggend of root X. 509-certificaat toevoegen en verifiëren in de registratie groep.
1. Genereer een blad certificaat van het basis certificaat in de registratie groep. Verzend het Leaf-certificaat van het apparaat wanneer dit verbinding maakt met uw toepassing.

Zie [apparaten verbinden met X. 509-certificaten](how-to-connect-devices-x509.md) voor meer informatie.

### <a name="for-testing-purposes-only"></a>Alleen voor test doeleinden

Voor alleen testen kunt u de volgende hulpprogram ma's gebruiken om basis-, tussenliggende en apparaat certificaten te genereren:

- [Hulpprogram ma's voor de Azure IOT Device Provisioning apparaat SDK](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md): een verzameling Node.js-hulpprogram ma's die u kunt gebruiken om X. 509-certificaten en-sleutels te genereren en te controleren.
- Als u een DevKit-apparaat gebruikt, wordt met dit [opdracht regel programma](https://aka.ms/iotcentral-docs-dicetool) een CA-certificaat gegenereerd dat u aan uw IOT Central-toepassing kunt toevoegen om de certificaten te controleren.
- [Testen van CA-certificaten voor voor beelden en zelf studies beheren](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): een verzameling Power shell-en bash-scripts voor het volgende:
  - Maak een certificaat keten.
  - Sla de certificaten op als CER-bestanden die u wilt uploaden naar uw IoT Central-toepassing.
  - Gebruik de verificatie code uit de IoT Central toepassing om het verificatie certificaat te genereren.
  - Maak blad certificaten voor uw apparaten met behulp van uw apparaat-Id's als een para meter voor het hulp programma.

## <a name="sas-group-enrollment"></a>Registratie van SAS-groep

Een apparaat met SAS-sleutel van het apparaat verbinden met uw toepassing:

1. Maak een *registratie groep* die gebruikmaakt van het Attestation **-type Shared Access Signature (SAS)** .
1. Kopieer de groep primaire of secundaire sleutel van de registratie groep.
1. Gebruik de Azure CLI om een apparaatcode te genereren op basis van de groeps sleutel:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. Gebruik de gegenereerde apparaatcode wanneer het apparaat verbinding maakt met uw IoT Central-toepassing.

## <a name="individual-enrollment"></a>Individuele inschrijving

Klanten die elk hun eigen verificatie referenties verbinden, gebruiken afzonderlijke inschrijvingen. Een afzonderlijke inschrijving is een vermelding voor één apparaat dat verbinding mag maken. Afzonderlijke inschrijvingen kunnen X. 509 Leaf-certificaten of SAS-tokens (van een fysieke of virtuele Trusted Platform Module) gebruiken als Attestation-mechanismen. Een apparaat-id mag alleen letters, cijfers en het teken `-` bevatten. Zie voor meer informatie [DPS individuele inschrijving](../../iot-dps/concepts-service.md#individual-enrollment).

> [!NOTE]
> Wanneer u een afzonderlijke inschrijving voor een apparaat maakt, heeft dit voor rang op de standaard opties voor het inschrijven van groepen in uw IoT Central-toepassing.

### <a name="create-individual-enrollments"></a>Afzonderlijke registraties maken

IoT Central ondersteunt de volgende Attestation-mechanismen voor individuele inschrijvingen:

- **Attestation van symmetrische sleutels:** Symmetrische-sleutel attest is een eenvoudige benadering voor het verifiëren van een apparaat met het DPS-exemplaar. Als u een afzonderlijke inschrijving wilt maken die gebruikmaakt van symmetrische sleutels, opent u de pagina **apparaat-verbinding** voor het apparaat, selecteert u **afzonderlijke inschrijving** als de verbindings methode en de **Shared Access Signature (SAS)** als mechanisme. Voer Base64 Encoded Primary en secundaire sleutels in en sla uw wijzigingen op. Gebruik de **id-Scope**, de **apparaat-id** en de primaire of secundaire sleutel om verbinding te maken met uw apparaat.

    > [!TIP]
    > Voor testen kunt u **openssl** gebruiken om base64-gecodeerde sleutels te genereren: `openssl rand -base64 64`

- **X. 509-certificaten:** Als u een afzonderlijke registratie met X. 509-certificaten wilt maken, opent u de pagina **verbinding met apparaat** , selecteert u **afzonderlijke inschrijving** als de verbindings methode en **certificaten (X. 509)** als mechanisme. Voor apparaats certificaten die worden gebruikt met een afzonderlijke inschrijvings vermelding is vereist dat de certificaat verlener en het onderwerp CN worden ingesteld op de apparaat-ID.

    > [!TIP]
    > Voor het testen kunt u [Hulpprogram ma's gebruiken voor de Azure IOT Device Provisioning Device SDK voor Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) om een zelfondertekend certificaat te genereren: `node create_test_cert.js device "mytestdevice"`

- **Attestation van trusted platform module (TPM):** Een [TPM](../../iot-dps/concepts-tpm-attestation.md) is een type hardware Security module. Het gebruik van een TPM is een van de veiligste manieren om verbinding te maken met een apparaat. In dit artikel wordt ervan uitgegaan dat u een afzonderlijke, firmware of geïntegreerde TPM gebruikt. Software geëmuleerde Tpm's zijn goed geschikt voor het maken van prototypen of tests, maar bieden geen hetzelfde beveiligings niveau als discrete, firmware of geïntegreerde Tpm's. Gebruik geen software-Tpm's in productie. Als u een afzonderlijke inschrijving wilt maken die gebruikmaakt van een TPM, opent u de pagina **verbinding met apparaat** , selecteert u **afzonderlijke inschrijving** als de verbindings methode en **TPM** als mechanisme. Voer de TPM-goedkeurings sleutel in en sla de verbindings gegevens van het apparaat op.

## <a name="device-registration"></a>Apparaatregistratie

Voordat een apparaat verbinding kan maken met een IoT Central toepassing, moet het zijn geregistreerd in de toepassing:

- Apparaten kunnen zichzelf automatisch registreren wanneer ze voor het eerst verbinding maken. Als u deze optie wilt gebruiken, moet u de [X. 509 Group-registratie](#x509-group-enrollment) of de [registratie van SAS-groepen](#sas-group-enrollment)gebruiken.
- Een operator kan een CSV-bestand importeren om een lijst met apparaten in de toepassing bulksgewijs te registreren.
- Een operator kan een afzonderlijk apparaat hand matig registreren op de pagina **apparaten** in de toepassing.

Met IoT Central kunnen Oem's apparaten massaal produceren die automatisch kunnen worden geregistreerd. Een OEM genereert de juiste referenties voor het apparaat en configureert de apparaten in de fabriek. Wanneer een klant een apparaat voor de eerste keer inschakelt, wordt er verbinding gemaakt met DPS, waarna het apparaat automatisch wordt verbonden met de juiste IoT Central-toepassing. Desgewenst kunt u een operator verplichten om het apparaat goed te keuren voordat de gegevens naar de toepassing worden verzonden.

> [!TIP]
> Op de pagina **beheer > apparaat-verbinding** wordt met de optie **automatisch goed keuren** bepaald of een operator het apparaat hand matig moet goed keuren voordat het kan beginnen met het verzenden van gegevens.

### <a name="automatically-register-devices-that-use-x509-certificates"></a>Apparaten die gebruikmaken van X. 509-certificaten automatisch registreren

1. Genereer de Blade certificaten voor uw apparaten met behulp van het basis-of tussenliggende certificaat dat u hebt toegevoegd aan de [registratie groep X. 509](#x509-group-enrollment). Gebruik de apparaat-Id's als de `CNAME` in de Blade certificaten. Een apparaat-id mag alleen letters, cijfers en het teken `-` bevatten.

1. Als OEM, flits elk apparaat met een apparaat-ID, een gegenereerd X. 509-blad certificaat en de waarde voor het bereik van de toepassings **-id** . De apparaatcode moet ook de model-ID verzenden van het model dat het apparaat implementeert.

1. Wanneer u overschakelt op een apparaat, maakt het eerst verbinding met DPS om de IoT Central verbindings gegevens op te halen.

1. Het apparaat gebruikt de informatie van DPS om verbinding te maken met en te registreren bij, uw IoT Central-toepassing.

De IoT Central toepassing gebruikt de model-ID die door het apparaat wordt verzonden om [het geregistreerde apparaat te koppelen aan een apparaatprofiel](#associate-a-device-with-a-device-template).

### <a name="automatically-register-devices-that-use-sas-tokens"></a>Apparaten die SAS-tokens gebruiken automatisch registreren

1. Kopieer de primaire sleutel van de groep uit de registratie groep voor **SAS-IOT-apparaten** :

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="Primaire sleutel van de registratie groep van SAS-IoT-apparaten groeperen":::

1. Gebruik de `az iot central device compute-device-key` opdracht voor het genereren van de SAS-sleutels van het apparaat. Gebruik de primaire sleutel van de groep uit de vorige stap. De apparaat-ID mag letters, cijfers en het `-` teken bevatten:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. Als OEM, flits elk apparaat met de apparaat-ID, de gegenereerde SAS-sleutel voor het apparaat en de waarde voor het bereik van de toepassings **-id** . De apparaatcode moet ook de model-ID verzenden van het model dat het apparaat implementeert.

1. Wanneer u overschakelt op een apparaat, maakt het eerst verbinding met DPS om de IoT Central registratie gegevens op te halen.

1. Het apparaat gebruikt de informatie van DPS om verbinding te maken met en te registreren bij, uw IoT Central-toepassing.

De IoT Central toepassing gebruikt de model-ID die door het apparaat wordt verzonden om [het geregistreerde apparaat te koppelen aan een apparaatprofiel](#associate-a-device-with-a-device-template).

### <a name="bulk-register-devices-in-advance"></a>Apparaten op grote schaal vooraf registreren

Als u een groot aantal apparaten wilt registreren bij uw IoT Central-toepassing, gebruikt u een CSV-bestand om [apparaat-id's en apparaatnamen te importeren](howto-manage-devices.md#import-devices).

Als uw apparaten SAS-tokens gebruiken om te verifiëren, [exporteert u een CSV-bestand van uw IOT Central-toepassing](howto-manage-devices.md#export-devices). Het geëxporteerde CSV-bestand bevat de apparaat-Id's en de SAS-sleutels.

Als uw apparaten X. 509-certificaten gebruiken voor verificatie, genereert u X. 509-blad certificaten voor uw apparaten met behulp van het basis-of tussenliggende certificaat dat u hebt geüpload naar de registratie groep X. 509. De apparaat-Id's gebruiken die u hebt geïmporteerd als de `CNAME` waarde in de blad certificaten.

Apparaten moeten de waarde voor **id-bereik** voor uw toepassing gebruiken en een model-id verzenden wanneer ze verbinding maken.

> [!TIP]
> U kunt de waarde voor **id-bereik** vinden in **beheer > apparaat verbinding**.

### <a name="register-a-single-device-in-advance"></a>Eén apparaat vooraf registreren

Deze aanpak is nuttig wanneer u experimenteert met IoT Central of apparaten testen. Selecteer **+ Nieuw** op de pagina **apparaten** om een afzonderlijk apparaat te registreren. U kunt de SAS-sleutels voor verbinding met apparaat gebruiken om het apparaat te verbinden met uw IoT Central-toepassing. Kopieer de _SAS-sleutel_ van het apparaat uit de verbindings gegevens voor een geregistreerd apparaat:

![SAS-sleutels voor een afzonderlijk apparaat](./media/concepts-get-connected/single-device-sas.png)

## <a name="associate-a-device-with-a-device-template"></a>Een apparaat koppelen aan een apparaatprofiel

IoT Central koppelt automatisch een apparaat aan een apparaatprofiel wanneer het apparaat verbinding maakt. Een apparaat verzendt een [model-id](../../iot-fundamentals/iot-glossary.md?toc=/azure/iot-central/toc.json&bc=/azure/iot-central/breadcrumb/toc.json#model-id) wanneer deze verbinding maakt. IoT Central maakt gebruik van de model-ID voor het identificeren van de apparaatprofiel voor dat specifieke model van het apparaat. Het detectie proces werkt als volgt:

1. Als de sjabloon voor het apparaat al is gepubliceerd in de IoT Central toepassing, wordt het apparaat gekoppeld aan de sjabloon voor het apparaat.
1. Als de sjabloon voor het apparaat nog niet is gepubliceerd in de IoT Central toepassing, IoT Central zoekt u het model van het apparaat in de [open bare model opslagplaats](https://github.com/Azure/iot-plugandplay-models). Als IoT Central het model vindt, wordt het gebruikt voor het genereren van een basis sjabloon voor het apparaat.
1. Als IoT Central het model niet in de open bare model opslagplaats vindt, wordt het apparaat gemarkeerd als niet- **gekoppeld**. Een operator kan een apparaatprofiel maken voor het apparaat en vervolgens het niet-gekoppelde apparaat migreren naar de nieuwe apparaatprofiel.

In de volgende scherm afbeelding ziet u hoe u de model-ID van een sjabloon in IoT Central kunt weer geven. Selecteer een onderdeel in een sjabloon en selecteer vervolgens **identiteit weer geven**:

:::image type="content" source="media/concepts-get-connected/model-id.png" alt-text="Scherm opname met model-ID in de sjabloon voor het apparaat van de Thermo staat.":::

U kunt het model van de [Thermo](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/thermostat-1.json) staat weer geven in de open bare model opslagplaats. De definitie van de model-ID ziet er als volgt uit:

```json
"@id": "dtmi:com:example:Thermostat;1"
```

## <a name="device-status-values"></a>Waarden van apparaatstatus

Wanneer een echt apparaat verbinding maakt met uw IoT Central-toepassing, verandert de apparaatstatus als volgt:

1. De apparaatstatus wordt eerst **geregistreerd**. Deze status betekent dat het apparaat wordt gemaakt in IoT Central en een apparaat-ID heeft. Een apparaat wordt geregistreerd wanneer:
    - Er wordt een nieuw echt apparaat toegevoegd op de pagina **apparaten** .
    - Er wordt een set apparaten toegevoegd met behulp van **importeren** op de pagina **apparaten** .

1. De apparaatstatus wordt gewijzigd in **ingericht** wanneer het apparaat dat is verbonden met uw IOT Central-toepassing met geldige referenties de inrichtings stap heeft voltooid. In deze stap gebruikt het apparaat DPS om automatisch een connection string op te halen uit het IoT Hub dat door uw IoT Central-toepassing wordt gebruikt. Het apparaat kan nu verbinding maken met IoT Central en beginnen met het verzenden van gegevens.

1. Een operator kan een apparaat blok keren. Wanneer een apparaat is geblokkeerd, kunnen er geen gegevens worden verzonden naar uw IoT Central-toepassing. Geblokkeerde apparaten hebben de status **geblokkeerd**. Een operator moet het apparaat opnieuw instellen om het verzenden van gegevens te hervatten. Wanneer een operator de blok kering van een apparaat opheffen, wordt de status teruggezet naar de vorige waarde, **geregistreerd** of **ingericht**.

1. Als de apparaatstatus wacht op **goed keuring**, betekent dit dat de optie **automatisch goed keuren** is uitgeschakeld. Een operator moet een apparaat expliciet goed keuren voordat er gegevens worden verzonden. Apparaten die niet hand matig zijn geregistreerd op de pagina **apparaten** , maar die zijn verbonden met geldige referenties, hebben de status van het apparaat **wacht op goed keuring**. Opera tors kunnen deze apparaten goed keuren op de pagina **apparaten** met behulp van de knop **goed keuren** .

1. Als de apparaatstatus niet is **gekoppeld**, betekent dit dat er voor het apparaat waarmee verbinding wordt gemaakt met IOT Central geen sjabloon voor het apparaat is gekoppeld. Deze situatie treedt doorgaans op in de volgende scenario's:

    - Er wordt een set apparaten toegevoegd met behulp van **importeren** op de pagina **apparaten** zonder de sjabloon voor het apparaat op te geven.
    - Een apparaat is hand matig geregistreerd op de pagina **apparaten** zonder de sjabloon voor het apparaat op te geven. Het apparaat is vervolgens verbonden met geldige referenties.  

    De operator kan een apparaat koppelen aan een sjabloon op de pagina **apparaten** met behulp van de knop **migreren** .

## <a name="sdk-support"></a>SDK-ondersteuning

De Sdk's van het Azure-apparaat bieden u de eenvoudigste manier voor het implementeren van uw apparaatcode. De volgende Sdk's voor apparaten zijn beschikbaar:

- [Azure IoT-SDK voor C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT-SDK voor Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT-SDK voor Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT-SDK voor Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT-SDK voor .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK-functies en IoT Hub connectiviteit

Alle communicatie van apparaten met IoT Hub gebruikt de volgende IoT Hub connectiviteits opties:

- [Apparaat-naar-Cloud-berichten](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Cloud-naar-apparaat-berichten](../../iot-hub/iot-hub-devguide-messages-c2d.md)
- [Apparaat apparaatdubbels](../../iot-hub/iot-hub-devguide-device-twins.md)

De volgende tabel bevat een overzicht van de manier waarop functies van Azure IoT Central-apparaten worden toegewezen aan IoT Hub-functies:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Telemetrie | Apparaat-naar-Cloud-berichten |
| Offline opdrachten | Cloud-naar-apparaat-berichten |
| Eigenschap | Dubbele gerapporteerde eigenschappen van het apparaat |
| Eigenschap (schrijfbaar) | Dubbele gewenste en gerapporteerde eigenschappen van het apparaat |
| Opdracht | Directe methoden |

### <a name="protocols"></a>Protocollen

De Sdk's van het apparaat ondersteunen de volgende netwerk protocollen om verbinding te maken met een IoT-hub:

- MQTT
- AMQP
- HTTPS

Zie voor meer informatie over deze protocollen en richt lijnen voor het kiezen van een [communicatie protocol](../../iot-hub/iot-hub-devguide-protocols.md).

Als uw apparaat een van de ondersteunde protocollen niet kan gebruiken, gebruikt u Azure IoT Edge om Protocol conversie uit te voeren. IoT Edge ondersteunt andere intelligentie-in-Edge-scenario's voor het offloaden van de verwerking van de Azure IoT Central-toepassing.

## <a name="security"></a>Beveiliging

Alle gegevens die worden uitgewisseld tussen apparaten en uw Azure-IoT Central, worden versleuteld. IoT Hub verifieert elke aanvraag van een apparaat dat verbinding maakt met een van de IoT Hub-eind punten van het apparaat. Om te voor komen dat referenties via de kabel worden uitgewisseld, gebruikt een apparaat ondertekende tokens om te verifiëren. Zie [toegang tot IOT hub beheren](../../iot-hub/iot-hub-devguide-security.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als u een ontwikkelaar van een apparaat bent, kunt u het volgende doen:

- Bekijk [Aanbevolen procedures](concepts-best-practices.md) voor het ontwikkelen van apparaten.
- Bekijk een voor beeld van een code die laat zien hoe u SAS-tokens gebruikt in [de zelf studie: een client toepassing maken en verbinden met uw Azure IOT Central-toepassing](tutorial-connect-device.md)
- Meer informatie over het [verbinden van apparaten met X. 509-certificaten met behulp van Node.js apparaat-SDK voor IOT Central toepassing](how-to-connect-devices-x509.md)
- Meer informatie over het [controleren van de connectiviteit van apparaten met behulp van Azure cli](./howto-monitor-devices-azure-cli.md)
- Meer informatie over [Azure IOT edge apparaten en Azure IOT Central](./concepts-iot-edge.md)
