---
title: Uw Azure percept DK over-the-Air bijwerken (OTA)
description: Meer informatie over het ontvangen van over-the-Air (OTA)-updates voor uw Azure percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to
ms.openlocfilehash: a3f586f853201534bbaa613e8538d55485ffe147
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063114"
---
# <a name="update-your-azure-percept-dk-over-the-air-ota"></a>Uw Azure percept DK over-the-Air bijwerken (OTA)

Volg deze hand leiding voor informatie over het bijwerken van het besturings systeem en de firmware van de vervoerders van uw Azure percept DK over-the-Air (OTA) met update voor het apparaat voor IoT Hub.

## <a name="prerequisites"></a>Vereisten

- Azure percept DK (Devkit)
- [Azure-abonnement](https://azure.microsoft.com/free/)
- [Setup van Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): u hebt uw dev kit verbonden met een Wi-Fi netwerk, een IOT hub gemaakt en de dev kit verbonden met de IOT hub
- [Het bijwerken van het apparaat voor IoT Hub is geslaagd](./how-to-set-up-over-the-air-updates.md)

## <a name="import-your-update-file-and-manifest-file"></a>Uw update bestand en manifest bestand importeren

> [!NOTE]
> Als u de update al hebt geïmporteerd, kunt u rechtstreeks overs Laan om **een update groep voor een apparaat te maken**.

1. [Down load het juiste manifest bestand (. json) en update bestand (. SWU) voor uw Azure percept-apparaat](https://go.microsoft.com/fwlink/?linkid=2155625).

1. Ga naar de Azure-IoT Hub die u voor uw Azure percept-apparaat gebruikt. In het deel venster aan de linkerkant selecteert u **updates voor apparaten** onder **automatische Apparaatbeheer**.

1. Boven aan het scherm worden verschillende tabbladen weer gegeven. Selecteer het tabblad **updates** .

1. Selecteer **+ nieuwe update importeren** onder de kop **gereed voor implementatie** .

1. Klik op de vakken onder **Selecteer manifest bestand importeren** en **Selecteer bestanden bijwerken** om uw manifest bestand (. json) en update bestand (. SWU) te selecteren.

1. Selecteer het mappictogram of het tekstvak onder **Selecteer een opslag container** en selecteer het juiste opslag account. Als u al een opslag container hebt gemaakt, kunt u deze opnieuw gebruiken. Selecteer anders **+ container** om een nieuwe opslag container te maken voor OTA-updates. Selecteer de container die u wilt gebruiken en klik op **selecteren**.

1. Selecteer **verzenden** om het import proces te starten. Als gevolg van de grootte van de installatie kopie kan het inzendings proces tot vijf minuten duren.

    > [!NOTE]
    > U wordt mogelijk gevraagd om een regel voor het toevoegen van een cross-Origin-aanvraag (CORS) toe te voegen voor toegang tot de geselecteerde opslag container. Selecteer **regel toevoegen en voer de bewerking opnieuw uit** om door te gaan.

1. Wanneer het import proces wordt gestart, wordt u omgeleid naar het tabblad **import geschiedenis** van de pagina **apparaat bijwerken** . Klik op **vernieuwen** om de voortgang te bewaken terwijl het import proces is voltooid. Afhankelijk van de grootte van de update kan dit enkele minuten of langer duren (tijdens piek tijden kan de import service tot wel één uur duren).

1. Wanneer de **status** kolom aangeeft dat het importeren is geslaagd, selecteert u het tabblad **gereed voor implementatie** en klikt u op **vernieuwen**. U ziet nu dat uw geïmporteerde update in de lijst wordt weer geven.

## <a name="create-a-device-update-group"></a>Een update groep voor een apparaat maken

Met Device update voor IoT Hub kunt u een update voor specifieke groepen van Azure percept DKs. Als u een groep wilt maken, moet u een tag toevoegen aan uw doelset apparaten in azure IoT Hub.

> [!NOTE]
> Als u al een groep hebt gemaakt, kunt u door gaan naar de volgende sectie.

Vereisten voor groeps Tags:

- U kunt elke wille keurige waarde toevoegen aan uw tag, met uitzonde ring van ' niet-gecategoriseerd ', een gereserveerde waarde.
- De label waarde mag niet langer zijn dan 255 tekens.
- De label waarde mag alleen de volgende speciale tekens bevatten: '. ', '-', ' _ ', ' ~ '.
- Tags en groeps namen zijn hoofdletter gevoelig.
- Een apparaat kan slechts één tag hebben. Alle volgende tags die aan het apparaat worden toegevoegd, overschrijven het vorige label.
- Een apparaat kan slechts tot één groep behoren.

1. Een tag toevoegen aan uw apparaat (en):

    1. Zoek in het navigatie deel venster van **IOT Edge** in het linkernavigatievenster uw Azure percept DK en navigeer naar het **apparaat**.

    1. Voeg een nieuwe **Update van het apparaat toe voor IOT hub** label waarde zoals hieronder wordt weer gegeven ( ```<CustomTagValue>``` verwijst naar de waarde/naam van de tag, bijvoorbeeld AzurePerceptGroup1). Meer informatie over device-dubbele [JSON-document Tags](../iot-hub/iot-hub-devguide-device-twins.md#device-twins).

        ```
        "tags": {
        "ADUGroup": "<CustomTagValue>"
        },
        ```

1. Klik op **Opslaan** om eventuele opmaak problemen op te lossen.

1. Een groep maken door een bestaande Azure IoT Hub-tag te selecteren:

    1. Ga terug naar uw Azure IoT Hub-pagina.

    1. Selecteer **apparaat bijwerken** onder **automatische Apparaatbeheer** in het menu aan de linkerkant.

    1. Selecteer het tabblad **groepen** . Op deze pagina wordt het aantal niet-gegroepeerde apparaten weer gegeven die zijn verbonden met het bijwerken van het apparaat.

    1. Selecteer **+ toevoegen** om een nieuwe groep te maken.

    1. Selecteer een IoT Hub-tag in de lijst en klik op **verzenden**.

    1. Zodra de groep is gemaakt, wordt de lijst met nalevings diagrammen en groepen bijgewerkt. De grafiek toont het aantal apparaten in verschillende statussen van naleving: **bij de nieuwste update**, **nieuwe updates die beschikbaar** zijn, updates die worden **uitgevoerd** en **nog niet zijn gegroepeerd**.

## <a name="deploy-an-update"></a>Een update implementeren

1. U moet de zojuist gemaakte groep zien met een nieuwe update die wordt vermeld onder **beschik bare updates** (mogelijk moet u één keer vernieuwen). Selecteer de update.

1. Bevestig dat de juiste apparaatgroep is geselecteerd als de doel apparaatgroep. Selecteer een **begin datum** en **begin tijd** voor uw implementatie en klik vervolgens op **implementatie maken**.

    > [!CAUTION]
    > Als u de begin tijd in het verleden instelt, wordt de implementatie onmiddellijk geactiveerd.

1. Controleer de nalevings grafiek. U ziet dat de update nu wordt uitgevoerd.

1. Nadat de update is voltooid, wordt de nieuwe update status weer gegeven in uw nalevings diagram.

1. Selecteer het tabblad **implementaties** boven aan de pagina **updates van apparaten** .

1. Selecteer uw implementatie om de details van de implementatie weer te geven. Mogelijk moet u op **vernieuwen** klikken totdat de **status** is gewijzigd in **geslaagd**.

## <a name="next-steps"></a>Volgende stappen

De dev kit is nu bijgewerkt. U kunt de ontwikkeling en bewerking voortzetten met uw dev kit.