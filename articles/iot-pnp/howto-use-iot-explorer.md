---
title: Azure IoT Explorer installeren en gebruiken | Microsoft Docs
description: Installeer het hulp programma Azure IoT Explorer en gebruik het om te communiceren met IoT Plug en Play-apparaten die zijn verbonden met IoT hub. Hoewel dit artikel zich richt op het werken met IoT Plug en Play-apparaten, kunt u het hulp programma gebruiken voor elk apparaat dat is verbonden met uw hub.
author: dominicbetts
ms.author: dobett
ms.date: 11/10/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: contperf-fy21q2
ms.openlocfilehash: b0a86a32148086a3b644d0bf147d12be0d812536
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97030312"
---
# <a name="install-and-use-azure-iot-explorer"></a>Azure IoT Explorer installeren en gebruiken

Azure IoT Explorer is een grafisch hulp programma voor interactie met en apparaten die zijn verbonden met uw IoT-hub. Dit artikel richt zich op het gebruik van het hulp programma om uw IoT Plug en Play-apparaten te testen. Nadat u het hulp programma hebt geïnstalleerd op uw lokale computer, kunt u het gebruiken om verbinding te maken met een hub. U kunt het hulp programma gebruiken voor het weer geven van de telemetrie die de apparaten verzenden, het werken met apparaateigenschappen en het aanroepen van opdrachten.

In dit artikel leest u informatie over:

- Installeer en configureer het Azure IoT Explorer-hulp programma.
- Gebruik het hulp programma om te communiceren met uw IoT Plug en Play-apparaten en deze te testen.

Zie het GitHub [Leesmij](https://github.com/Azure/azure-iot-explorer/blob/master/README.md)voor meer informatie over het gebruik van het hulp programma.

Als u het hulp programma Azure IoT Explorer wilt gebruiken, hebt u het volgende nodig:

- Een Azure IoT-hub. Er zijn veel manieren om een IoT-hub toe te voegen aan uw Azure-abonnement, zoals [het maken van een IOT-hub met behulp van de Azure cli](../iot-hub/iot-hub-create-using-cli.md). U hebt de IoT hub-connection string nodig om het hulp programma Azure IoT Explorer uit te voeren. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Een apparaat dat is geregistreerd in uw IoT-hub. U kunt IoT Explorer gebruiken om apparaatregistratie te maken en te beheren in uw IoT Hub.

## <a name="install-azure-iot-explorer"></a>Azure IoT Explorer installeren

Ga naar [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer/releases) en vouw de lijst met assets uit voor de meest recente release. Down load en installeer de meest recente versie van de toepassing.

>[!Important]
> Bijwerken naar versie 0.13. x voor het omzetten van modellen vanuit elke opslag plaats op basis van [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models)

## <a name="use-azure-iot-explorer"></a>Azure IoT Explorer gebruiken

Voor een apparaat kunt u een verbinding maken met uw eigen apparaat of een van de voor beelden van gesimuleerde apparaten gebruiken. Zie voor een aantal voor beelden van gesimuleerde apparaten die in verschillende talen zijn geschreven, de [toepassing een voor beeld van een IoT Plug en Play-apparaat verbinden met IOT hub](quickstart-connect-device.md) Quick Start.

### <a name="connect-to-your-hub"></a>Verbinding maken met uw hub

De eerste keer dat u Azure IoT Explorer uitvoert, wordt u gevraagd om de connection string van de IoT-hub. Nadat u de connection string hebt toegevoegd, selecteert u **verbinding maken**. U kunt de instellingen van het hulp programma gebruiken om over te scha kelen naar een andere IoT-hub door de connection string bij te werken.

De model definitie voor een IoT-Plug en Play apparaat wordt opgeslagen in de open bare opslag plaats, op het aangesloten apparaat of in een lokale map. Het hulp programma zoekt standaard naar uw model definitie in de open bare opslag plaats en op het aangesloten apparaat. U kunt bronnen toevoegen en verwijderen of de prioriteit van de bronnen in **instellingen** configureren:

Een bron toevoegen:

1. Ga naar de **instellingen voor Home/IoT Plug en Play**
2. Selecteer **toevoegen** en kies uw bron uit een opslag plaats of een lokale map.

Een bron verwijderen:

1. Ga naar de **instellingen voor Home/IoT Plug en Play**
2. Zoek de bron die u wilt verwijderen.
3. Selecteer **X** om deze te verwijderen.

Wijzig de bron prioriteiten:

U kunt een van de model definitie bronnen slepen en neerzetten naar een andere classificatie in de lijst.

### <a name="view-devices"></a>Apparaten weergeven

Wanneer het hulp programma verbinding maakt met uw IoT-hub, wordt de lijst pagina met **apparaten** weer gegeven met een lijst met de apparaat-id's die zijn geregistreerd bij uw IOT-hub. U kunt een wille keurig item in de lijst selecteren om meer informatie weer te geven.

Op de pagina met **apparaten** lijst kunt u het volgende doen:

- Selecteer **Nieuw** om een nieuw apparaat te registreren bij uw hub. Voer vervolgens een apparaat-ID in. Gebruik de standaard instellingen om automatisch verificatie sleutels te genereren en de verbinding met uw hub in te scha kelen.
- Selecteer een apparaat en selecteer vervolgens **verwijderen** om een apparaat-id te verwijderen. Controleer de details van het apparaat voordat u deze actie voltooit, zodat u zeker weet dat u de juiste apparaat-id wilt verwijderen.

## <a name="interact-with-a-device"></a>Interactie met een apparaat

Selecteer op de pagina **apparaten** lijst een waarde in de kolom **apparaat-id** om de detail pagina voor het geregistreerde apparaat weer te geven. Voor elk apparaat zijn er twee secties: **apparaat** en **digitaal twee**.

### <a name="device"></a>Apparaat

Deze sectie bevat de **identiteit** van het apparaat, het  **apparaat dubbele**, **telemetrie**, **directe methode**, **Cloud-naar-apparaat-bericht**, **module-identiteits**  tabbladen.

- U kunt de gegevens van de [apparaat-id](../iot-hub/iot-hub-devguide-identity-registry.md) weer geven en bijwerken op het tabblad **apparaat-id** .
- U kunt de dubbele gegevens van het [apparaat](../iot-hub/iot-hub-devguide-device-twins.md) openen op het tabblad **apparaat dubbele** .
- Als een apparaat is verbonden en actief gegevens verzendt, kunt u de [telemetrie](../iot-hub/iot-hub-devguide-messages-read-builtin.md) weer geven op het tabblad **telemetrie** .
- U kunt op het tabblad **directe methode** een [rechtstreekse methode](../iot-hub/iot-hub-devguide-direct-methods.md) aanroepen op het apparaat.
- U kunt een [Cloud-naar-apparaat-bericht](../iot-hub/iot-hub-devguide-messages-c2d.md) verzenden op het tabblad **Cloud-naar-apparaat-berichten** .
- U kunt toegang krijgen tot de [module dubbele](../iot-hub/iot-hub-devguide-module-twins.md) informatie.

### <a name="iot-plug-and-play-components"></a>IoT-Plug en Play onderdelen

Als het apparaat met behulp van een **model-id** is verbonden met de hub, wordt het tabblad **IOT Plug en Play-onderdelen** weer gegeven, waar u de **model-id** kunt zien.

Als de **model-id** beschikbaar is in een van de geconfigureerde bronnen-open bare opslag plaats of lokale map, wordt de lijst met onderdelen weer gegeven. Als u een onderdeel selecteert, worden de eigenschappen, opdrachten en telemetrie weer gegeven.

Op de pagina **onderdeel** kunt u de alleen-lezen eigenschappen weer geven, beschrijf bare eigenschappen bijwerken, opdrachten aanroepen en de telemetriegegevens bekijken die door dit onderdeel worden geproduceerd.

:::image type="content" source="media/howto-use-iot-explorer/components-iot-explorer.png" alt-text="Onderdelen in Azure IoT Explorer weergeven":::

#### <a name="properties"></a>Eigenschappen

:::image type="content" source="media/howto-use-iot-explorer/properties-iot-explorer.png" alt-text="Eigenschappen weer geven in azure IoT Explorer":::

U kunt de alleen-lezen eigenschappen die zijn gedefinieerd in een interface weer geven op het tabblad **Eigenschappen (alleen-lezen)** . U kunt de Beschrijf bare eigenschappen die zijn gedefinieerd in een interface op het tabblad **Eigenschappen (schrijfbaar)** bijwerken:

1. Ga naar het tabblad **Eigenschappen (schrijfbaar)** .
1. Klik op de eigenschap die u wilt bijwerken.
1. Voer de nieuwe waarde voor de eigenschap in.
1. Bekijk een voor beeld van de payload die naar het apparaat moet worden verzonden.
1. Verzend de wijziging.

Nadat u een wijziging hebt ingediend, kunt u de status van de update volgen: **synchronisatie**, **geslaagd** of **fout**. Wanneer de synchronisatie is voltooid, ziet u de nieuwe waarde van uw eigenschap in de kolom **gemelde eigenschap** . Als u naar andere pagina's navigeert voordat de synchronisatie is voltooid, ontvangt het hulp programma nog steeds een melding wanneer de update is voltooid. U kunt ook het meldingen centrum van het hulp programma gebruiken om de meldings geschiedenis te bekijken.

#### <a name="commands"></a>Opdracht

Als u een opdracht wilt verzenden naar een apparaat, gaat u naar het tabblad **opdrachten** :

1. Vouw in de lijst met opdrachten de opdracht uit die u wilt activeren.
1. Voer de vereiste waarden voor de opdracht in.
1. Bekijk een voor beeld van de payload die naar het apparaat moet worden verzonden.
1. Dien de opdracht in.

#### <a name="telemetry"></a>Telemetrie

Als u de telemetrie voor de geselecteerde interface wilt weer geven, gaat u naar het tabblad **telemetrie** .

#### <a name="known-issues"></a>Bekende problemen

Zie voor een overzicht van de IoT-functies die worden ondersteund door de nieuwste versie van het hulp programma, de [lijst met onderdelen](https://github.com/Azure/azure-iot-explorer/wiki).

## <a name="next-steps"></a>Volgende stappen

In dit procedure-artikel hebt u geleerd hoe u Azure IoT Explorer kunt installeren en gebruiken om te communiceren met uw IoT Plug en Play-apparaten. Een voorgestelde volgende stap is om te leren hoe u [de DTDL-hulpprogram ma's voor ontwerpen installeert en gebruikt](howto-use-dtdl-authoring-tools.md).
