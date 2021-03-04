---
title: Een spraak assistent maken met Azure percept DK en Azure percept-audio
description: Meer informatie over het maken en implementeren van een oplossing zonder code voor uw Azure percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 3c5e6fd62e4f4db9ccc1306d32d09b8338cbf963
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098023"
---
# <a name="create-a-voice-assistant-with-azure-percept-dk-and-azure-percept-audio"></a>Een spraak assistent maken met Azure percept DK en Azure percept-audio

In deze zelf studie maakt u een spraak assistent op basis van een sjabloon die u kunt gebruiken met uw Azure percept DK-en Azure percept-audio. De ondergeschikte Voice-assistent wordt uitgevoerd in [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) en bevat een selectie van met spraak beheerde virtuele objecten. Als u een object wilt beheren, zegt u uw tref woord. Dit is een woord of korte zin waarmee uw apparaat wordt geactiveerd, gevolgd door een opdracht. Elke sjabloon reageert op een set specifieke opdrachten.

In deze hand leiding vindt u instructies voor het instellen van uw apparaten, het maken van een spraak assistent en de benodigde bronnen voor [spraak Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview) , het testen van uw Voice-assistent, het configureren van uw tref woord en het maken van aangepaste tref woorden.

## <a name="prerequisites"></a>Vereisten

- Azure percept DK (Devkit)
- Azure percept-audio
- Spreker of hoofd telefoon waarmee verbinding kan worden gemaakt met een audio aansluiting van 3,5 mm (optioneel)
- [Azure-abonnement](https://azure.microsoft.com/free/)
- [Setup van Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): u hebt uw Devkit met een Wi-Fi-netwerk verbonden, een IOT hub gemaakt en uw Devkit verbonden met de IOT hub
- [Setup van Azure percept-audio](./quickstart-percept-audio-setup.md)


## <a name="create-a-voice-assistant-using-an-available-template"></a>Een spraak assistent maken met behulp van een beschik bare sjabloon

1. Ga naar [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Open het tabblad **demo's & zelf studies** .

    :::image type="content" source="./media/tutorial-no-code-speech/portal-overview.png" alt-text="Scherm opname van Azure Portal Home page.":::

1. Klik op **sjablonen voor spraak assistenten uitproberen** onder **spraak zelf studies en demo's**. Hiermee opent u een venster aan de rechter kant van het scherm.

1. Ga als volgt te werk in het venster:

    1. Selecteer in het vervolg keuzemenu **IOT hub** de IOT-hub waarmee uw Devkit is verbonden.

    1. Selecteer uw Devkit in het vervolg keuzemenu voor het **apparaat** .

    1. Selecteer een van de beschik bare sjablonen voor spraak assistenten.

    1. Klik op het selectie vakje **Ik ga akkoord met de voor waarden & voor dit project** .

    1. Klik op **Create**.

    :::image type="content" source="./media/tutorial-no-code-speech/template-creation.png" alt-text="Scherm afbeelding van het maken van een sjabloon voor de Voice Assistant.":::

1. Nadat u op **maken** hebt geklikt, opent de portal een nieuw venster voor het maken van uw speech-thema Resource. Ga als volgt te werk in het venster:

    1. Selecteer uw Azure-abonnement in het vak **abonnement** .

    1. Selecteer uw voorkeurs resource groep in de vervolg keuzelijst **resource groep** . Als u een nieuwe resource groep wilt maken voor gebruik met uw Voice Assistant, klikt u op **maken** onder het vervolg keuzemenu en volgt u de aanwijzingen.

    1. Voer een naam in voor het voor voegsel van de **toepassing**. Dit is het voor voegsel voor het project en de naam van uw aangepaste opdracht.

    1. Selecteer onder **regio** de regio waarin u resources wilt implementeren.

    1. Selecteer onder **Luis-Voorspellings categorie** de optie **standaard** (de laag gratis biedt geen ondersteuning voor spraak aanvragen).

    1. Klik op de knop **Maken**. Resources voor de toepassing Voice Assistant worden geïmplementeerd in uw abonnement.

        > [!WARNING]
        > Sluit **het venster pas nadat** de implementatie van de resource door de portal is voltooid. Het sluiten van het venster kan leiden tot onverwacht gedrag van de spraak assistent. Zodra de resource is geïmplementeerd, wordt de demo weer gegeven.

    :::image type="content" source="./media/tutorial-no-code-speech/resource-group.png" alt-text="Scherm opname van het venster voor het selecteren van het abonnement en de resource groep.":::

## <a name="test-out-your-voice-assistant"></a>Uw Voice Assistant testen

Zeg het tref woord gevolgd door een opdracht om met uw Voice Assistant te communiceren. Wanneer het oormerk de SoM uw tref woord herkent, verzendt het apparaat een klok (die u kunt horen als er een spreker of koptelefoon is verbonden) en worden de Led's blauw. De Led's worden overgeschakeld naar race Blue terwijl uw opdracht wordt verwerkt. Het antwoord van de Voice Assistant op uw opdracht wordt in tekst in het demo venster afgedrukt en audibly via uw spreker/hoofd telefoon verzonden. Het standaard trefwoord (vermeld naast **aangepast tref woord**) is ingesteld op "computer" en elke sjabloon heeft een reeks compatibele opdrachten waarmee u in het demo venster kunt werken met virtuele objecten. Als u bijvoorbeeld de demo van de horeca of de gezondheids zorg gebruikt, zegt u ' computer, TV inschakelen ' om de virtuele TV in te scha kelen.

:::image type="content" source="./media/tutorial-no-code-speech/hospitality-demo.png" alt-text="Scherm afbeelding van het horeca-demo venster.":::

### <a name="hospitality-and-healthcare-demo-commands"></a>Demo-opdrachten voor horeca en gezondheids zorg

Zowel de gezondheids zorg-als horeca-demo's hebben virtuele Tv's, verlichting, blinden en thermo staten die u kunt gebruiken. De volgende opdrachten (en aanvullende variaties) worden ondersteund:

* "De lichten in-of uitschakelen."
* "De TV in-of uitschakelen."
* "AC in-of uitschakelen."
* "Open/sluit de blinden."
* "De Tempe ratuur instellen op X graden." (X is de gewenste Tempe ratuur, bijvoorbeeld 75.)

:::image type="content" source="./media/tutorial-no-code-speech/healthcare-demo.png" alt-text="Scherm afbeelding van het demo venster van de gezondheids zorg.":::

### <a name="automotive-demo-commands"></a>Demonstratie-opdrachten voor auto's

De automobiel demo bevat een virtuele Seat warmere, ontontdooier en thermo staat waarmee u kunt werken. De volgende opdrachten (en aanvullende variaties) worden ondersteund:

* "De deontdooier in-of uitschakelen."
* "De zitplaats warmer inschakelen of uitschakelen."
* "De Tempe ratuur instellen op X graden." (X is de gewenste Tempe ratuur, bijvoorbeeld 75.)
* "De Tempe ratuur met Y graden verg Roten/verkleinen."


:::image type="content" source="./media/tutorial-no-code-speech/auto-demo.png" alt-text="Scherm opname van het venster met auto demonstratie.":::

### <a name="inventory-demo-commands"></a>Inventaris demo-opdrachten

De inventaris demo bevat een selectie van virtuele blauwe, gele en groene vakken om samen te werken met een virtuele inventaris-app. De volgende opdrachten (en aanvullende variaties) worden ondersteund:

* "X-vakken toevoegen/verwijderen." (X is het aantal vakken, bijvoorbeeld 4.)
* "Order/verzend X-vakjes".
* "Hoeveel dozen zijn er op voor Raad?"
* "Aantal Y-vakken." (Y is de kleur van de vakken, bijvoorbeeld geel.)
* "Verzend alles op voor Raad."


:::image type="content" source="./media/tutorial-no-code-speech/inventory-demo.png" alt-text="Scherm opname van het demo venster van de inventaris.":::

## <a name="configure-your-keyword"></a>Uw tref woord configureren

U kunt het tref woord aanpassen voor uw Voice Assistant-toepassing.

1. Klik op **wijzigen** naast **aangepast tref woord** in het demo venster.

1. Selecteer een van de beschik bare tref woorden. U kunt kiezen uit een aantal voor beelden van tref woorden en aangepaste tref woorden die u hebt gemaakt.

1. Klik op **Opslaan**.

### <a name="create-a-custom-keyword"></a>Een aangepast tref woord maken

U kunt uw eigen tref woord maken voor uw spraak toepassing. De training voor uw aangepaste tref woord kan in slechts enkele minuten worden voltooid.

1. Klik op **+ aangepast tref woord maken** boven aan het voorbeeld venster. 

1. Voer uw gewenste tref woord in, dat een enkel woord of een korte zin kan zijn.

1. Selecteer uw **spraak bron** (deze wordt vermeld naast de **aangepaste opdracht** in het demo venster en bevat het voor voegsel van de toepassing).

1. Klik op **Opslaan**. 

## <a name="create-a-custom-command"></a>Een aangepaste opdracht maken

De portal biedt ook functionaliteit voor het maken van aangepaste opdrachten met bestaande spraak bronnen. ' Aangepaste opdracht ' verwijst naar de Voice Assistant-toepassing zelf, niet een specifieke opdracht in de bestaande toepassing. Als u een aangepaste opdracht maakt, maakt u een nieuw spraak project, dat u verder moet ontwikkelen in [Speech Studio](https://speech.microsoft.com/).

Als u een nieuwe aangepaste opdracht wilt maken vanuit het demo venster, klikt u boven aan de pagina op **+ aangepaste opdracht maken** en gaat u als volgt te werk:

1. Voer een naam in voor de aangepaste opdracht.

1. Voer een beschrijving van het project in (optioneel).

1. Selecteer de taal van uw voor keur.

1. Selecteer uw spraak bron.

1. Selecteer uw LUIS-resource.

1. Selecteer uw LUIS-ontwerp bron of maak een nieuwe.

1. Klik op **Create**.

:::image type="content" source="./media/tutorial-no-code-speech/custom-commands.png" alt-text="Scherm opname van het venster voor het maken van aangepaste opdrachten.":::

Wanneer u een aangepaste opdracht hebt gemaakt, moet u naar [Speech Studio](https://speech.microsoft.com/) gaan om verder te kunnen ontwikkelen. Als u Speech Studio opent en de vermelde aangepaste opdracht niet ziet, voert u de volgende stappen uit:

1. Klik in het menu aan de linkerkant in azure percept Studio op **Speech** onder **AI projects**.

1. Selecteer het tabblad **opdrachten** .

    :::image type="content" source="./media/tutorial-no-code-speech/ai-projects.png" alt-text="Scherm afbeelding van een lijst met aangepaste opdrachten die kunnen worden bewerkt.":::

1. Selecteer de aangepaste opdracht die u wilt ontwikkelen. Hiermee opent u het project in speech Studio.

    :::image type="content" source="./media/tutorial-no-code-speech/speech-studio.png" alt-text="Scherm afbeelding van het Start scherm van speech Studio.":::

Raadpleeg de documentatie van de [Speech-Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/custom-commands)voor meer informatie over het ontwikkelen van aangepaste opdrachten.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="voice-assistant-was-created-but-does-not-respond-to-commands"></a>De Voice Assistant is gemaakt maar reageert niet op opdrachten

Controleer de LED-lampjes op het upkaartgebied:

* Drie effen blauwe lichten geven aan dat de Voice Assistant gereed is en wacht op het tref woord.
* Als de LED van het Center (L02) wit is, is de initialisatie van de Devkit voltooid en moet deze worden geconfigureerd met een sleutel woord.
* Als het middel punt (L02) wordt geknipperd op wit, wordt de initialisatie van het audio-SoM nog niet voltooid. Het kan enkele minuten duren voordat de initialisatie is voltooid.

Zie het [LED-artikel](./audio-button-led-behavior.md)voor meer informatie over de LED-Indica tors.

### <a name="voice-assistant-does-not-respond-to-a-custom-keyword-created-in-speech-studio"></a>De Voice Assistant reageert niet op een aangepast tref woord dat is gemaakt in speech Studio

Dit kan gebeuren als de spraak module verouderd is. Volg deze stappen om de spraak module bij te werken naar de nieuwste versie:

1. Klik op **apparaten** in het deel venster aan de linkerkant van de start pagina van Azure percept Studio.

1. Zoek en selecteer uw apparaat.

    :::image type="content" source="./media/tutorial-no-code-speech/devices.png" alt-text="Scherm opname van de apparaten lijst in azure percept Studio.":::

1. Selecteer in het venster apparaat het tabblad **spraak** .

1. Controleer de versie van de spraak module. Als er een update beschikbaar is, wordt er een knop **bijwerken** weer geven naast het versie nummer.

1. Klik op **bijwerken** om de update van de spraak module te implementeren. Het update proces duurt over het algemeen 2-3 minuten.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met uw Voice Assistant-toepassing, voert u de volgende stappen uit om de spraak bronnen op te schonen die u tijdens deze zelf studie hebt geïmplementeerd:

1. Selecteer in de [Azure Portal](https://portal.azure.com) **resource groepen** in het linkerdeel venster of typ deze in de zoek balk.

    :::image type="content" source="./media/tutorial-no-code-speech/azure-portal.png" alt-text="Scherm opname van Azure Portal start pagina met het deel venster links en resource groepen.":::

1. Selecteer uw resourcegroep.

1. Selecteer alle zes resources die het voor voegsel van de toepassing bevatten en klik op het pictogram **verwijderen** in het bovenste menu paneel.
\
    :::image type="content" source="./media/tutorial-no-code-speech/select-resources.png" alt-text="Scherm opname van de spraak bronnen die voor verwijdering zijn geselecteerd.":::

1. Als u het verwijderen wilt bevestigen, typt u **Ja** in het bevestigings venster, controleert u of u de juiste resources hebt geselecteerd en klikt u op **verwijderen**.

    :::image type="content" source="./media/tutorial-no-code-speech/delete-confirmation.png" alt-text="Scherm opname van bevestigings venster voor verwijdering.":::

> [!WARNING]
> Hiermee verwijdert u alle aangepaste tref woorden die zijn gemaakt met de spraak bronnen die u wilt verwijderen, en de demo van de Voice-assistent werkt niet meer.

## <a name="next-steps"></a>Volgende stappen

Nu u een spraak oplossing zonder code hebt gemaakt, kunt u een [visuele oplossing zonder code](./tutorial-nocode-vision.md) maken voor uw Azure percept DK.
