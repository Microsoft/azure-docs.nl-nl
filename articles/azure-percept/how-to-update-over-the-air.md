---
title: Uw Azure percept DK via de lucht bijwerken
description: Meer informatie over het ontvangen van de Air-updates voor uw Azure percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: b8f9e6f4bc091abbd1bb08ecbd649c1411e5ab20
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095388"
---
# <a name="update-your-azure-percept-dk-over-the-air"></a>Uw Azure percept DK via de lucht bijwerken

Volg deze hand leiding voor informatie over het bijwerken van de vervoerders van uw Azure percept DK over-the-Air met apparaat bijwerken voor IoT Hub.

## <a name="import-your-update-file-and-manifest-file"></a>Importeer het update bestand en het manifest bestand.

> [!NOTE]
> Als u de update al hebt geïmporteerd, kunt u rechtstreeks door gaan met het **maken van een bijgewerkte groep voor een apparaat**.

1. Ga naar de Azure-IoT Hub die u voor uw Azure percept-apparaat gebruikt. In het deel venster aan de linkerkant selecteert u **updates voor apparaten** onder **automatische Apparaatbeheer**.
 
1. Boven aan het scherm worden verschillende tabbladen weer gegeven. Selecteer het tabblad **updates** .
 
1. Selecteer **+ nieuwe update importeren** onder de kop **gereed voor implementatie** .
 
1. Klik op de vakken onder **Selecteer manifest bestand importeren** en **Selecteer update bestanden** om het juiste manifest bestand (. json) en één update bestand (. SWU) te selecteren. U kunt deze update bestanden [hier](https://go.microsoft.com/fwlink/?linkid=2155625)vinden voor uw Azure percept-apparaat.
 
1. Selecteer het mappictogram of het tekstvak onder **Selecteer een opslag container** en selecteer vervolgens het juiste opslag account.
 
1. Als u al een opslag container hebt gemaakt, kunt u deze opnieuw gebruiken. Selecteer anders **+ container** om een nieuwe opslag container te maken voor OTA-updates. Selecteer de container die u wilt gebruiken en klik op **selecteren**.
 
    >[!Note]
    >Als u geen container hebt, wordt u gevraagd er een te maken.
 
1. Selecteer **verzenden** om het import proces te starten. Het verzend proces duurt ongeveer 4 minuten.
 
    >[!Note]
    >U wordt mogelijk gevraagd om een regel voor het toevoegen van een cross-Origin-aanvraag (CORS) toe te voegen voor toegang tot de geselecteerde opslag container. Selecteer **regel toevoegen en voer de bewerking opnieuw uit** om door te gaan.
 
    >[!Note]
    >Als gevolg van de grootte van de afbeelding ziet u mogelijk de pagina **verzenden...** Voor Maxi maal 5 minuten voordat u de volgende stap ziet.
    
1. Het import proces wordt gestart en u wordt omgeleid naar het tabblad **import geschiedenis** van de pagina **updates van apparaten** . Klik op **vernieuwen** om de voortgang te bewaken terwijl het import proces is voltooid. Afhankelijk van de grootte van de update, kan dit enkele minuten of langer duren (tijdens piek tijden moet de import service tot 1hr worden uitgevoerd).

1. Wanneer de status kolom aangeeft dat het importeren is geslaagd, selecteert u het tabblad **gereed voor implementatie** en klikt u op **vernieuwen**. U ziet nu dat uw geïmporteerde update in de lijst wordt weer geven.
 
## <a name="create-a-device-update-group"></a>Een update groep voor een apparaat maken
Met Device update voor IoT Hub kunt u een update voor specifieke groepen van Azure percept DKs. Als u een groep wilt maken, moet u een tag toevoegen aan uw doelset apparaten in azure IoT Hub.

> [!NOTE]
> Als u al een groep hebt gemaakt, kunt u direct door gaan naar de volgende stap.

Vereisten voor groeps Tags:
- U kunt elke wille keurige waarde toevoegen aan uw tag, behalve bij niet- **gecategoriseerd**, wat een gereserveerde waarde is.
- De label waarde mag niet langer zijn dan 255 tekens.
- De label waarde mag alleen de volgende speciale tekens bevatten: '. ', '-', ' _ ', ' ~ '.
- Tags en groeps namen zijn hoofdletter gevoelig.
- Een apparaat kan slechts één tag hebben. Alle volgende tags die aan het apparaat worden toegevoegd, overschrijven het vorige label.
- Een apparaat kan slechts tot één groep behoren.

1. Voeg een tag toe aan uw apparaat (en).
    1. Zoek in het navigatie deel venster van **IOT Edge** in het linkernavigatievenster uw Azure percept DK en navigeer naar het **apparaat**.
    1. Voeg een nieuwe **Update van het apparaat toe voor IOT hub** label waarde zoals hieronder wordt weer gegeven (Wijzig ```<CustomTagValue>``` uw waarde, d.w.z. AzurePerceptGroup1). Meer informatie over device-dubbele [JSON-document Tags](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins#device-twins).

    ```
    "tags": {
    "ADUGroup": "<CustomTagValue>"
    },
    ```

 
1. Klik op **Opslaan** om eventuele opmaak problemen op te lossen.
 
1. Maak een groep door een bestaande Azure IoT Hub-tag te selecteren.
    1. Ga terug naar uw Azure IoT Hub-pagina.
    1. Selecteer **apparaat bijwerken** onder **automatische Apparaatbeheer** in het menu aan de linkerkant.
    1. Selecteer het tabblad **groepen** . Op deze pagina wordt het aantal niet-gegroepeerde apparaten weer gegeven die zijn verbonden met het bijwerken van het apparaat.
    1. Selecteer **+ toevoegen** om een nieuwe groep te maken.
    1. Selecteer een IoT Hub-tag in de lijst en klik op **verzenden**.
    1. Zodra de groep is gemaakt, wordt de lijst met nalevings diagrammen en groepen bijgewerkt. De grafiek toont het aantal apparaten in verschillende statussen van naleving: **bij de nieuwste update**, **nieuwe updates die beschikbaar** zijn, updates die worden **uitgevoerd** en **nog niet zijn gegroepeerd**.
 

## <a name="deploy-an-update"></a>Een update implementeren
1. U moet de zojuist gemaakte groep zien met een nieuwe update die wordt vermeld onder **beschik bare updates** (mogelijk moet u één keer vernieuwen). Selecteer de update.
 
1. Bevestig dat de juiste apparaatgroep is geselecteerd als de doel apparaatgroep. Selecteer een **begin datum** en **begin tijd** voor uw implementatie en klik vervolgens op **implementatie maken**. 

    >[!CAUTION]
    >Als u de begin tijd in het verleden instelt, wordt de implementatie onmiddellijk geactiveerd.
 
1. Controleer de nalevings grafiek. U ziet dat de update nu wordt uitgevoerd.
 
1. Nadat de update is voltooid, wordt de nieuwe update status weer gegeven in uw nalevings diagram.
 
1. Selecteer het tabblad **implementaties** boven aan de pagina **updates van apparaten** .
 
1. Selecteer uw implementatie om de details van de implementatie weer te geven. Mogelijk moet u op **vernieuwen** klikken totdat de **status** is gewijzigd in **geslaagd**.

## <a name="next-steps"></a>Volgende stappen

De dev kit is nu bijgewerkt. U kunt door gaan met de ontwikkeling en bewerking van uw Devkit.