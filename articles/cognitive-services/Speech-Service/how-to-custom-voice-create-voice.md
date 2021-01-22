---
title: Een aangepaste Voice-speech-service maken
titleSuffix: Azure Cognitive Services
description: Wanneer u klaar bent om uw gegevens te uploaden, gaat u naar de aangepaste Voice Portal. Een aangepast spraak project maken of selecteren. Het project moet de juiste taal-en land instellingen en de gender eigenschappen delen als de gegevens die u wilt gebruiken voor uw spraak training.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: b59d9ebf55f7a4c02891a782b7271eec2f521576
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663278"
---
# <a name="create-a-custom-voice"></a>Een Custom Voice maken

In [gegevens voorbereiden voor aangepaste spraak](how-to-custom-voice-prepare-data.md), worden de verschillende gegevens typen beschreven die u kunt gebruiken voor het trainen van een aangepaste stem en de verschillende indelings vereisten. Wanneer u uw gegevens hebt voor bereid, kunt u deze uploaden naar de [aangepaste Voice Portal](https://aka.ms/custom-voice-portal)of via de aangepaste training-API voor spraak. Hier worden de stappen beschreven voor het trainen van een aangepaste stem via de portal.

> [!NOTE]
> Op deze pagina wordt ervan uitgegaan dat u aan de [slag gaat met aangepaste stem](how-to-custom-voice.md) en [gegevens voorbereidt voor aangepaste spraak](how-to-custom-voice-prepare-data.md)en een aangepast spraak project hebt gemaakt.

Controleer de talen die worden ondersteund voor aangepaste spraak: [taal voor aanpassing](language-support.md#customization).

## <a name="upload-your-datasets"></a>Uw gegevens sets uploaden

Wanneer u klaar bent om uw gegevens te uploaden, gaat u naar de [aangepaste Voice Portal](https://aka.ms/custom-voice-portal). Een aangepast spraak project maken of selecteren. Het project moet de juiste taal-en land instellingen en de gender eigenschappen delen als de gegevens die u wilt gebruiken voor uw spraak training. Selecteer bijvoorbeeld `en-GB` als de audio-opnames in het Engels met een UK-accent zijn gedaan.

Ga naar het tabblad **gegevens** en klik op **gegevens uploaden**. Selecteer in de wizard het juiste gegevens type dat overeenkomt met wat u hebt voor bereid.

Elke gegevensset die u uploadt, moet voldoen aan de vereisten voor het gegevens type dat u kiest. Het is belang rijk dat u uw gegevens op de juiste wijze opmaakt voordat deze worden geüpload. Dit zorgt ervoor dat de gegevens correct worden verwerkt door de aangepaste Voice-service. Ga naar [voor bereide gegevens voor aangepaste spraak](how-to-custom-voice-prepare-data.md) en zorg ervoor dat uw gegevens timen zijn opgemaakt.

> [!NOTE]
> Gebruikers met een gratis abonnement (F0) kunnen twee gegevens sets tegelijk uploaden. Gebruikers met een standaard abonnement (S0) kunnen vijf gegevens sets tegelijk uploaden. Als u de limiet bereikt, wacht u totdat ten minste één van de gegevens sets is geïmporteerd. Probeer het opnieuw.

> [!NOTE]
> Het maximum aantal gegevens sets dat per abonnement mag worden geïmporteerd is 10. zip-bestanden voor gratis abonnement (F0) gebruikers en 500 voor Standard Subscription (S0)-gebruikers.

Gegevens sets worden automatisch gevalideerd zodra u op de knop uploaden hebt geklikt. De gegevens validatie omvat de serie controles van de audio bestanden om de bestands indeling, grootte en sampling frequentie te controleren. Los de fouten op en verzend deze opnieuw. Wanneer de aanvraag voor het importeren van gegevens is gestart, ziet u een vermelding in de gegevens tabel die overeenkomt met de gegevensset die u zojuist hebt geüpload.

De volgende tabel toont de verwerkings statussen voor geïmporteerde gegevens sets:

| Staat | Betekenis |
| ----- | ------- |
| Wordt verwerkt | Uw gegevensset is ontvangen en wordt verwerkt. |
| Geslaagd | Uw gegevensset is gevalideerd en kan nu worden gebruikt voor het bouwen van een spraak model. |
| Mislukt | De gegevensset is tijdens de verwerking mislukt omdat er veel redenen zijn, bijvoorbeeld bestands fouten, gegevens problemen of netwerk problemen. |

Nadat de validatie is voltooid, ziet u het totale aantal overeenkomende uitingen voor elk van de gegevens sets in de kolom **uitingen** . Als het gegevens type dat u hebt geselecteerd lange-audio segmentatie vereist, bevat deze kolom alleen de uitingen die we voor u hebben gesegmenteerd op basis van uw transcripten of via de speech transcriptie-service. U kunt de gegevensset die is gevalideerd, verder downloaden om de details weer te geven van de uitingen die zijn geïmporteerd en de transcripten van de toewijzing. Hint: de segmentering van lange audio kan meer dan een uur duren om de gegevens verwerking te volt ooien.

Voor de gegevens sets en-US en zh-CN kunt u een rapport verder downloaden om de uitspraak cijfers en het geluids niveau voor elk van uw opnamen te controleren. De uitspraak punten variëren van 0 tot en met 100. Een score onder 70 duidt doorgaans op een probleem met de spraak of het script komt niet overeen. Een zwaar accent kan uw uitspraak score verlagen en invloed hebben op de gegenereerde digitale stem.

Een hogere signaal-naar-ruis verhouding (SNR) duidt op een lagere ruis in uw audio. U kunt normaal gesp roken een 50 + SNR bereiken door op professionele Studios te registreren. Audio met een SNR lager dan 20 kan leiden tot duidelijk ruis in uw gegenereerde stem.

U kunt een uitingen met lage uitspraak scores of slecht signaal-naar-ruis-verhouding opnieuw opnemen. Als u niet opnieuw kunt vastleggen, kunt u deze uitingen uitsluiten van uw gegevensset.

## <a name="build-your-custom-voice-model"></a>Uw aangepaste spraak model bouwen

Nadat uw gegevensset is gevalideerd, kunt u deze gebruiken om uw aangepaste spraak model te maken.

1.  Navigeer naar **tekst-naar-spraak > aangepaste spraak > [naam van project] > training**.

2.  Klik op **model trainen**.

3.  Voer vervolgens een **naam** en **Beschrijving** in die u helpen bij het identificeren van dit model.

    Kies een naam zorgvuldig. De naam die u hier opgeeft, is de naam die u gebruikt om de stem in uw aanvraag voor spraak synthese op te geven als onderdeel van de SSML-invoer. Alleen letters, cijfers en enkele Lees tekens zoals-, \_ en (', ') zijn toegestaan. Gebruik verschillende namen voor verschillende spraak modellen.

    Een veelvoorkomend gebruik van het veld **Beschrijving** bestaat uit het vastleggen van de namen van de gegevens sets die zijn gebruikt voor het maken van het model.

4.  Kies op de pagina **Selecteer een trainings gegevens** een of meer gegevens sets die u wilt gebruiken voor de training. Controleer het aantal uitingen voordat u deze verzendt. U kunt beginnen met een wille keurig aantal uitingen voor en-US-en zh-CN-Voice-modellen. Voor andere landen moet u meer dan 2.000 uitingen selecteren om een stem te kunnen trainen.

    > [!NOTE]
    > Dubbele audio namen worden verwijderd uit de training. Zorg ervoor dat de gegevens sets die u selecteert niet dezelfde audio namen bevatten voor meerdere zip-bestanden.

    > [!TIP]
    > Het gebruik van de gegevens sets uit dezelfde spreker is vereist voor kwaliteits resultaten. Wanneer de gegevens sets die u hebt ingediend voor training, een totaal aantal van minder dan 6.000 afzonderlijke uitingen bevatten, traint u uw spraak model via de statistische methode voor parametrische synthese. Als uw trainings gegevens een totaal aantal van 6.000 afzonderlijke uitingen overschrijden, kunt u een trainings proces met de methode voor het samen voegen van de synthese starten. Normaal gesp roken kan de samenvoegings technologie leiden tot meer natuurlijke en kwalitatief hoogwaardige stem resultaten. [Neem contact op met het aangepaste spraak team](https://go.microsoft.com/fwlink/?linkid=2108737) als u een model wilt trainen met de nieuwste Neural TTS-technologie die een digitale stem kan produceren die gelijk is aan de openbaar beschik bare [Neural stemmen](language-support.md#neural-voices).

5.  Klik op **trainen** om te beginnen met het maken van uw spraak model.

In de tabel training wordt een nieuw item weer gegeven dat overeenkomt met dit nieuwe model. In de tabel wordt ook de volgende status weer gegeven: verwerken, geslaagd, mislukt.

De status die wordt weer gegeven, weerspiegelt het proces van het converteren van uw gegevensset naar een spraak model, zoals hier wordt weer gegeven.

| Staat | Betekenis |
| ----- | ------- |
| Wordt verwerkt | Uw spraak model wordt gemaakt. |
| Geslaagd | Uw spraak model is gemaakt en kan worden geïmplementeerd. |
| Mislukt | Uw spraak model is niet in de cursus opgetreden omdat er veel redenen zijn, bijvoorbeeld ongevraagde gegevens problemen of netwerk problemen. |

De opleidings tijd is afhankelijk van het volume van de verwerkte audio gegevens. Typische tijden variëren van ongeveer 30 minuten voor honderden uitingen tot 40 uur voor 20.000 uitingen. Zodra uw model training is voltooid, kunt u beginnen met testen.

> [!NOTE]
> Gebruikers met een gratis abonnement (F0) kunnen tegelijkertijd één spraak lettertype trainen. Gebruikers met een standaard abonnement (S0) kunnen drie stemmen tegelijk trainen. Als u de limiet bereikt, wacht u tot ten minste één van de spraak lettertypen is voltooid en probeert u het opnieuw.

> [!NOTE]
> Het maximum aantal spraak modellen dat per abonnement mag worden getraind, is 10 modellen voor gebruikers met een gratis abonnement (F0) en 100 voor Standard Subscription (S0)-gebruikers.

Als u de Neural-functie voor spraak training gebruikt, kunt u een model trainen dat is geoptimaliseerd voor realtime streaming-scenario's of een HD Neural-model dat is geoptimaliseerd voor asynchrone [lange audio-synthese](long-audio-api.md).  

## <a name="test-your-voice-model"></a>Uw spraak model testen

Nadat het letter type is gemaakt, kunt u het testen voordat u het voor gebruik implementeert.

1.  Navigeer naar **tekst-naar-spraak > aangepaste spraak > [naam van project] > testen**.

2.  Klik op **test toevoegen**.

3.  Selecteer een of meer modellen die u wilt testen.

4.  Geef de tekst op die de stem (en) moet spreken. Als u ervoor hebt gekozen om meerdere modellen tegelijk te testen, wordt dezelfde tekst gebruikt voor het testen van verschillende modellen.

    > [!NOTE]
    > De taal van de tekst moet hetzelfde zijn als de taal van uw spraak lettertype. Alleen goed getrainde modellen kunnen worden getest. In deze stap wordt alleen tekst zonder opmaak ondersteund.

5.  Klik op **Create**.

Zodra u uw test aanvraag hebt verzonden, keert u terug naar de pagina test. De tabel bevat nu een vermelding die overeenkomt met uw nieuwe aanvraag en de kolom Status. Het kan een paar minuten duren om spraak te maken. Wanneer de kolom status **is geslaagd**, kunt u de audio afspelen of de tekst invoer (een. txt-bestand) en audio-uitvoer (een. wav-bestand) downloaden en de Audition voor kwaliteit verder afstemmen.

U kunt ook de test resultaten vinden op de detail pagina van de modellen die u hebt geselecteerd voor het testen. Ga naar het tabblad **training** en klik op de naam van het model om de detail pagina van het model in te voeren.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Een aangepast spraak eindpunt maken en gebruiken

Nadat u uw spraak model hebt gemaakt en getest, implementeert u het in een aangepast tekst-naar-spraak-eind punt. Vervolgens gebruikt u dit eind punt in plaats van het gebruikelijke eind punt bij het maken van tekst-naar-spraak-aanvragen via de REST API. Uw aangepaste eind punt kan alleen worden aangeroepen door het abonnement dat u hebt gebruikt voor het implementeren van het letter type.

Als u een nieuw aangepast spraak eindpunt wilt maken, gaat u naar **tekst-naar-spraak > aangepaste spraak >-implementatie**. Selecteer **eind punt toevoegen** en voer een **naam** en **Beschrijving** in voor het aangepaste eind punt. Selecteer vervolgens het aangepaste spraak model dat u wilt koppelen aan dit eind punt.

Nadat u op de knop **toevoegen** hebt geklikt, ziet u in de tabel van het eind punt een vermelding voor het nieuwe eind punt. Het kan een paar minuten duren voordat een nieuw eind punt wordt gemaakt. Wanneer de status van de implementatie is **voltooid**, is het eind punt klaar voor gebruik.

> [!NOTE]
> Gratis abonnement (F0) gebruikers kunnen slechts één model implementeren. Gebruikers met een standaard abonnement (S0) kunnen Maxi maal 50 eind punten maken, elk met een eigen aangepaste stem.

> [!NOTE]
> Als u uw aangepaste stem wilt gebruiken, moet u de naam van het spraak model opgeven, de aangepaste URI rechtstreeks in een HTTP-aanvraag gebruiken en hetzelfde abonnement gebruiken om de verificatie van de TTS-Service door te geven.

Nadat het eind punt is geïmplementeerd, wordt de naam van het eind punt weer gegeven als een koppeling. Klik op de koppeling om informatie weer te geven die specifiek is voor uw eind punt, zoals de eindpunt sleutel, eind punt-URL en voorbeeld code.

Online testen van het eind punt is ook beschikbaar via de aangepaste Voice Portal. Kies **eind punt controleren** op de detail pagina van het **eind** punt om uw eind punt te testen. De pagina eindpunt test wordt weer gegeven. Voer de tekst in die moet worden gesp roken (in de [indeling](speech-synthesis-markup.md) onbewerkte tekst of SSML in het tekstvak. Selecteer **afspelen** om de tekst te belui Steren die in uw aangepaste spraak letter type wordt gesp roken. Deze test functie wordt in rekening gebracht op basis van het gebruik van uw aangepaste spraak synthese.

Het aangepaste eind punt is functioneel identiek aan het standaard eindpunt dat wordt gebruikt voor tekst-naar-spraak-aanvragen. Zie [rest API](rest-text-to-speech.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Hand leiding: uw stem voorbeelden vastleggen](record-custom-voice-samples.md)
* [Naslag informatie over de tekst-naar-spraak-API](rest-text-to-speech.md)
* [Lange audio-API](long-audio-api.md)