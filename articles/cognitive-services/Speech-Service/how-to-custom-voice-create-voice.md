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
ms.openlocfilehash: 541448f08e4ce9961d34063dcc225bf89d969a73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101703368"
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

In de weer gave gegevens detail kunt u de uitspraak cijfers en het geluids niveau voor elk van uw gegevens sets verder controleren. De uitspraak punten variëren van 0 tot en met 100. Een score onder 70 duidt doorgaans op een probleem met de spraak of het script komt niet overeen. Een zwaar accent kan uw uitspraak score verlagen en invloed hebben op de gegenereerde digitale stem.

Een hogere signaal-naar-ruis verhouding (SNR) duidt op een lagere ruis in uw audio. U kunt normaal gesp roken een 50 + SNR bereiken door op professionele Studios te registreren. Audio met een SNR lager dan 20 kan leiden tot duidelijk ruis in uw gegenereerde stem.

U kunt een uitingen met lage uitspraak scores of slecht signaal-naar-ruis-verhouding opnieuw opnemen. Als u niet opnieuw kunt vastleggen, kunt u deze uitingen uitsluiten van uw gegevensset.

> [!NOTE]
> Als u aangepaste Neural Voice gebruikt, moet u uw stem talen hand Steren op het tabblad spraak- **talen** . Wanneer u het opname script voorbereidt, moet u de onderstaande zin gebruiken om de stem-talen bevestiging te verkrijgen van het gebruik van hun stem gegevens om een TTS-spraak model te maken en synthetische spraak te genereren. "I [Geef uw voor-en achternaam op] Houd er rekening mee dat de opnamen van mijn stem worden gebruikt door [de naam van het bedrijf te vermelden] om een synthetische versie van mijn stem te maken en te gebruiken."
Deze zin wordt gebruikt om te controleren of de opnamen in uw trainings gegevens sets worden uitgevoerd door dezelfde persoon die de toestemming doet. [Lees hier meer over hoe uw gegevens worden verwerkt en hoe Voice-talen worden geverifieerd](/legal/cognitive-services/speech-service/custom-neural-voice/data-privacy-security-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext). 

## <a name="build-your-custom-voice-model"></a>Uw aangepaste spraak model bouwen

Nadat uw gegevensset is gevalideerd, kunt u deze gebruiken om uw aangepaste spraak model te maken.

1.  Navigeer naar **tekst-naar-spraak > aangepaste spraak > [naam van project] > model**.

2.  Klik op **model trainen**.

3.  Voer vervolgens een **naam** en **Beschrijving** in die u helpen bij het identificeren van dit model.

    Kies een naam zorgvuldig. De naam die u hier opgeeft, is de naam die u gebruikt om de stem in uw aanvraag voor spraak synthese op te geven als onderdeel van de SSML-invoer. Alleen letters, cijfers en enkele Lees tekens zoals-, \_ en (', ') zijn toegestaan. Gebruik verschillende namen voor verschillende spraak modellen.

    Een veelvoorkomend gebruik van het veld **Beschrijving** bestaat uit het vastleggen van de namen van de gegevens sets die zijn gebruikt voor het maken van het model.

4.  Kies op de pagina **Selecteer een trainings gegevens** een of meer gegevens sets die u wilt gebruiken voor de training. Controleer het aantal uitingen voordat u deze verzendt. U kunt beginnen met een wille keurig aantal uitingen voor en-US-en zh-CN-stem modellen met behulp van de ' adaptieve ' Trainings methode. Voor andere landen moet u meer dan 2.000 uitingen selecteren voor het trainen van een stem met behulp van een Standard-laag met inbegrip van de trainings methoden ' statistisch parametrische ' en ' samen voegen ' en meer dan 300 uitingen voor het trainen van een aangepaste Neural-stem. 

    > [!NOTE]
    > Dubbele audio namen worden verwijderd uit de training. Zorg ervoor dat de gegevens sets die u selecteert niet dezelfde audio namen bevatten voor meerdere zip-bestanden.

    > [!TIP]
    > Het gebruik van de gegevens sets uit dezelfde spreker is vereist voor kwaliteits resultaten. Voor verschillende trainings methoden is een andere grootte van de trainings gegevens vereist. Als u een model wilt trainen met de methode ' statistisch parametrische ', zijn er ten minste 2.000 afzonderlijke uitingen vereist. Voor de methode ' samen voegen ' is het 6.000 uitingen, terwijl voor ' Neural ' de minimale vereiste voor de gegevens grootte 300 uitingen is.

5. Selecteer in de volgende stap de **methode training** . 

    > [!NOTE]
    > Als u een Neural-stem wilt trainen, moet u een profiel voor spraak-talen opgeven met het bestand met de bestands toestemming van de stem talen bevestiging dat zijn/haar spraak gegevens worden gebruikt voor het trainen van een aangepast spraak model. Aangepaste Neural Voice is beschikbaar met beperkte toegang. Zorg ervoor dat u bekend bent met de [vereiste AI-vereisten](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) en [Pas de toegang hier toe](https://aka.ms/customneural). 
    
    Op deze pagina kunt u ook selecteren om uw script te uploaden om het te testen. Het test script moet een txt-bestand zijn dat kleiner is dan 1 MB. De ondersteunde coderings indeling bevat ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE of UTF-16-to. Elke alinea van de utterance resulteert in een afzonderlijke audio. Als u alle zinnen wilt combi neren in één audio, maakt u ze op één alinea. 

6. Klik op **trainen** om te beginnen met het maken van uw spraak model.

In de tabel training wordt een nieuw item weer gegeven dat overeenkomt met dit nieuwe model. In de tabel wordt ook de volgende status weer gegeven: verwerken, geslaagd, mislukt.

De status die wordt weer gegeven, weerspiegelt het proces van het converteren van uw gegevensset naar een spraak model, zoals hier wordt weer gegeven.

| Staat | Betekenis |
| ----- | ------- |
| Wordt verwerkt | Uw spraak model wordt gemaakt. |
| Geslaagd | Uw spraak model is gemaakt en kan worden geïmplementeerd. |
| Mislukt | Uw spraak model is niet in de cursus opgetreden omdat er veel redenen zijn, bijvoorbeeld ongevraagde gegevens problemen of netwerk problemen. |

De trainings tijd is afhankelijk van het volume van de verwerkte audio gegevens en de trainings methode die u hebt geselecteerd. Het bereik kan variëren van 30 minuten tot 40 uur. Zodra uw model training is voltooid, kunt u beginnen met testen. 

> [!NOTE]
> Gebruikers met een gratis abonnement (F0) kunnen tegelijkertijd één spraak lettertype trainen. Gebruikers met een standaard abonnement (S0) kunnen drie stemmen tegelijk trainen. Als u de limiet bereikt, wacht u tot ten minste één van de spraak lettertypen is voltooid en probeert u het opnieuw.

> [!NOTE]
> De training van aangepaste Neural stemmen is niet gratis. Controleer hier de [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) . 

> [!NOTE]
> Het maximum aantal spraak modellen dat per abonnement mag worden getraind, is 10 modellen voor gebruikers met een gratis abonnement (F0) en 100 voor Standard Subscription (S0)-gebruikers.

Als u de Neural-functie voor spraak training gebruikt, kunt u een model trainen dat is geoptimaliseerd voor realtime streaming-scenario's of een HD Neural-model dat is geoptimaliseerd voor asynchrone [lange audio-synthese](long-audio-api.md).  

## <a name="test-your-voice-model"></a>Uw spraak model testen

Elke training genereert 100 voor beeld van audio bestanden automatisch, zodat u het model kunt testen. Nadat u het spraak model hebt gemaakt, kunt u het testen voordat u het implementeert voor gebruik.

1.  Navigeer naar **tekst-naar-spraak > aangepaste spraak > [naam van project] > model**.

2.  Klik op de naam van het model dat u wilt testen.

3.  Op de detail pagina van het model vindt u de voor beeld-audio bestanden op het tabblad **testen** . 

De kwaliteit van de stem is afhankelijk van een aantal factoren, zoals de grootte van de trainings gegevens, de kwaliteit van de opname, de nauw keurigheid van het transcript bestand, hoe goed de vastgelegde stem in de trainings gegevens overeenkomt met de persoonlijkheid van de ontworpen stem voor uw beoogde gebruiks voorbeeld, en nog veel meer. [Bekijk hier meer informatie over de mogelijkheden en beperkingen van onze technologie en de best practice om de kwaliteit van uw model te verbeteren](/legal/cognitive-services/speech-service/custom-neural-voice/characteristics-and-limitations-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext). 

## <a name="create-and-use-a-custom-voice-endpoint"></a>Een aangepast spraak eindpunt maken en gebruiken

Nadat u uw spraak model hebt gemaakt en getest, implementeert u het in een aangepast tekst-naar-spraak-eind punt. Vervolgens gebruikt u dit eind punt in plaats van het gebruikelijke eind punt bij het maken van tekst-naar-spraak-aanvragen via de REST API. Uw aangepaste eind punt kan alleen worden aangeroepen door het abonnement dat u hebt gebruikt voor het implementeren van het letter type.

Als u een nieuw aangepast spraak eindpunt wilt maken, gaat u naar **tekst-naar-spraak > aangepast spraak > eind punt**. Selecteer **eind punt toevoegen** en voer een **naam** en **Beschrijving** in voor het aangepaste eind punt. Selecteer vervolgens het aangepaste spraak model dat u wilt koppelen aan dit eind punt.

Nadat u op de knop **toevoegen** hebt geklikt, ziet u in de tabel van het eind punt een vermelding voor het nieuwe eind punt. Het kan een paar minuten duren voordat een nieuw eind punt wordt gemaakt. Wanneer de status van de implementatie is **voltooid**, is het eind punt klaar voor gebruik.

U kunt het eind punt **onderbreken** en **hervatten** als u dit niet altijd gebruikt. Wanneer een eind punt na de onderbreking opnieuw wordt geactiveerd, blijft de eind punt-URL hetzelfde, zodat u de code in uw apps niet hoeft te wijzigen. 

U kunt het eind punt ook bijwerken naar een nieuw model. Als u het model wilt wijzigen, moet u ervoor zorgen dat het nieuwe model dezelfde naam heeft als het type dat u wilt bijwerken. 

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