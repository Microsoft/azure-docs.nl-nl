---
title: Veelgestelde vragen over spraak naar tekst
titleSuffix: Azure Cognitive Services
description: Krijg antwoorden op veelgestelde vragen over de spraak-naar-tekst service.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: panosper
ms.openlocfilehash: bcb4408df08f3854b067c8b805b78433a3d5075c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103491245"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Veelgestelde vragen over spraak naar tekst

Als u in deze veelgestelde vragen geen antwoorden op uw vragen kunt vinden, kunt u [Andere ondersteunings opties](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)bekijken.

## <a name="general"></a>Algemeen

**V: wat is het verschil tussen een basislijn model en een aangepaste spraak op het tekst model?**

**A**: een basislijn model is getraind met behulp van gegevens van micro soft en is al geïmplementeerd in de Cloud. U kunt een aangepast model gebruiken om een model aan te passen aan een specifieke omgeving met specifieke omgevings ruis of-taal. In fabrieks-, auto-of geluids-en geluids eigen straten is een aangepast akoestisch model vereist. Voor onderwerpen als biologie, natuur kunde, radiology, product namen en aangepaste acroniemen zou een aangepast taal model nodig zijn. Als u een aangepast model traint, moet u beginnen met Verwante tekst om de herkenning van speciale termen en zinsdelen te verbeteren.

**V: waar kan ik beginnen als ik een basis lijn model wil gebruiken?**

**A**: u moet eerst een [abonnements sleutel](overview.md#try-the-speech-service-for-free)ophalen. Als u REST-aanroepen naar de voorgeïmplementeerde basislijn modellen wilt maken, raadpleegt u de [rest api's](./overview.md#reference-docs). Als u websockets wilt gebruiken, [downloadt u de SDK](speech-sdk.md).

**V: moet ik altijd een aangepast spraak model maken?**

**A**: Nee. Als uw toepassing gebruikmaakt van een generieke, dagelijkse taal, hoeft u geen model aan te passen. Als uw toepassing wordt gebruikt in een omgeving waar zich weinig of geen achtergrond ruis bevindt, hoeft u geen model aan te passen.

U kunt basis lijnen en aangepaste modellen implementeren in de portal en vervolgens nauw keurige tests uitvoeren. U kunt deze functie gebruiken om de nauw keurigheid van een basislijn model te meten versus een aangepast model.

**V: Hoe weet ik wanneer de verwerking voor mijn gegevensset of model is voltooid?**

**A**: momenteel is de status van het model of de gegevensset in de tabel de enige manier om te weten. Wanneer de verwerking is voltooid, is de status **geslaagd**.

**V: kan ik meer dan één model maken?**

**A**: er is geen limiet voor het aantal modellen dat u kunt hebben in uw verzameling.

**V: Ik heb een fout gemaakt. Hoe kan ik het importeren van gegevens of het maken van het model dat wordt uitgevoerd annuleren?**

**A**: op dit moment kunt u een akoestische of taal aanpassings proces niet terugdraaien. U kunt geïmporteerde gegevens en modellen verwijderen wanneer ze zich in een Terminal status bevinden.

**V: Ik krijg een aantal resultaten voor elke woord groep met de gedetailleerde uitvoer indeling. Welk account moet ik gebruiken?**

**A**: Neem altijd het eerste resultaat, zelfs als een ander resultaat (' N-Best ') mogelijk een hogere betrouwbaarheids waarde heeft. De spraak service beschouwt het eerste resultaat als het beste. Het kan ook een lege teken reeks zijn als er geen spraak is herkend.

De overige resultaten zijn waarschijnlijk erger en er zijn mogelijk geen volledige kapitalisatie en lees tekens toegepast. Deze resultaten zijn vooral nuttig in speciale scenario's, zoals het geven van gebruikers de optie om correcties uit een lijst te kiezen of om onjuist herkende opdrachten af te handelen.

**V: Waarom zijn er verschillende basis modellen?**

**A**: u kunt kiezen uit meer dan één basis model in de speech-service. Elke model naam bevat de datum waarop deze is toegevoegd. Wanneer u begint met het trainen van een aangepast model, kunt u het meest recente model gebruiken om de beste nauw keurigheid te bereiken. Oudere basis modellen zijn nog steeds beschikbaar wanneer een nieuw model beschikbaar wordt gemaakt. U kunt door gaan met het model dat u hebt gebruikt totdat het is buiten gebruik gesteld (Zie [model en levenscyclus van het eind punt](./how-to-custom-speech-model-and-endpoint-lifecycle.md)). U wordt nog steeds geadviseerd om over te scha kelen naar het meest recente basis model voor betere nauw keurigheid.

**V: kan ik mijn bestaande model (model stacking) bijwerken?**

**A**: u kunt een bestaand model niet bijwerken. Als oplossing kunt u de oude gegevensset combi neren met de nieuwe gegevensset en deze opnieuw aanpassen.

De oude gegevensset en de nieuwe gegevensset moeten worden gecombineerd in één ZIP-bestand (voor akoestische gegevens) of in een txt-bestand (voor taal gegevens). Wanneer de aanpassing is voltooid, moet het nieuwe, bijgewerkte model opnieuw worden geïmplementeerd om een nieuw eind punt te verkrijgen

**V: wanneer een nieuwe versie van een basis model beschikbaar is, wordt mijn implementatie automatisch bijgewerkt?**

**A**: implementaties worden niet automatisch bijgewerkt.

Als u een model hebt aangepast en geïmplementeerd, blijft die implementatie. U kunt het geïmplementeerde model buiten gebruik stellen, opnieuw aanpassen met de nieuwere versie van het basis model en opnieuw implementeren voor een betere nauw keurigheid.

Basis modellen en aangepaste modellen worden na enige tijd buiten gebruik gesteld (Zie [model en levenscyclus van het eind punt](./how-to-custom-speech-model-and-endpoint-lifecycle.md)).

**V: kan ik mijn model downloaden en lokaal uitvoeren?**

**A**: u kunt een aangepast model lokaal uitvoeren in een [docker-container](speech-container-howto.md?tabs=cstt).

**V: kan ik mijn gegevens sets, modellen en implementaties kopiëren naar of verplaatsen naar een andere regio of een ander abonnement?**

**A**: u kunt de [rest API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) gebruiken om een aangepast model naar een andere regio of een ander abonnement te kopiëren. Gegevens sets of implementaties kunnen niet worden gekopieerd. U kunt een gegevensset opnieuw importeren in een ander abonnement en eind punten maken met behulp van de model kopieën.

**V: zijn mijn aanvragen geregistreerd?**

**A**: standaard aanvragen worden niet geregistreerd (audio of transcriptie). Indien nodig kunt u *logboek inhoud van deze eindpunt* optie selecteren wanneer u [een aangepast eind punt maakt](how-to-custom-speech-train-model.md#deploy-a-custom-model). U kunt ook audio logboek registratie inschakelen in de [Speech SDK](how-to-use-logging.md) op basis van per aanvraag zonder een aangepast eind punt te maken. In beide gevallen worden de geluids-en herkennings resultaten van aanvragen opgeslagen in beveiligde opslag. Voor abonnementen die gebruikmaken van micro soft-opslag, zijn ze 30 dagen beschikbaar.

U kunt de geregistreerde bestanden op de implementatie pagina in speech Studio exporteren als u een aangepast eind punt gebruikt met *logboek inhoud van dit eind punt* ingeschakeld. Als audio logboek registratie via de SDK is ingeschakeld, roept u de [API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModelLogs) aan voor toegang tot de bestanden.

**V: mijn aanvragen worden beperkt?**

**A**: Zie de [quota en limieten voor spraak Services](speech-services-quotas-and-limits.md).

**V: hoe worden er kosten in rekening gebracht voor Dual Channel audio?**

**A**: als u elk kanaal afzonderlijk verzendt (elk kanaal in een eigen bestand), wordt u in rekening gebracht voor de duur van elk bestand. Als u één bestand met elk kanaal hebt verzonden, worden er kosten in rekening gebracht voor de duur van het ene bestand. Raadpleeg de [pagina met prijzen voor Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)voor meer informatie over prijzen.

> [!IMPORTANT]
> Neem contact op met een van de ondersteunings kanalen als u meer privacy-problemen hebt die verhinderen dat u de aangepaste spraak service kunt gebruiken.

## <a name="increasing-concurrency"></a>Gelijktijdige gelijktijdigheid
Zie de [quota en limieten voor spraak Services](speech-services-quotas-and-limits.md).


## <a name="importing-data"></a>Gegevens importeren

**V: wat is de limiet voor de grootte van een gegevensset en waarom is het de limiet?**

**A**: de limiet wordt veroorzaakt door de beperking van de grootte van een bestand voor http-upload. Zie de [quota en limieten voor spraak Services](speech-services-quotas-and-limits.md) voor de daad werkelijke limiet. U kunt uw gegevens in meerdere gegevens sets splitsen en allemaal selecteren om het model te trainen.

**V: kan ik mijn tekst bestanden opzip zodat ik een groter tekst bestand kan uploaden?**

**A**: Nee. Op dit moment zijn alleen niet-gecomprimeerde tekst bestanden toegestaan.

**V: het gegevens rapport geeft aan dat er mislukte uitingen zijn. Wat is het probleem?**

**A**: een mislukte upload van 100 procent van de uitingen in een bestand is geen probleem. Als het meren deel van de uitingen in een akoestische of taal-gegevensset (bijvoorbeeld meer dan 95 procent) is geïmporteerd, kan de gegevensset bruikbaar zijn. We raden u echter aan om te begrijpen waarom de uitingen is mislukt en de problemen op te lossen. De meest voorkomende problemen, zoals opmaak fouten, zijn gemakkelijk te herstellen.

## <a name="creating-an-acoustic-model"></a>Een akoestische model maken

**V: hoeveel akoestische gegevens heb ik nodig?**

**A**: we raden u aan om te beginnen met tussen 30 minuten en één uur aan akoestische gegevens.

**V: welke gegevens moet ik verzamelen?**

**A**: gegevens verzamelen die zich zo dicht mogelijk bij het toepassings scenario behoren en het gebruik van de aanvraag. De gegevens verzameling moet overeenkomen met de doel toepassing en gebruikers in termen van apparaten, omgevingen en typen sprekers. Over het algemeen moet u zo veel mogelijk gegevens verzamelen uit een groot aantal luid sprekers.

**V: hoe kan ik akoestische gegevens verzamelen?**

**A**: u kunt een zelfstandige toepassing voor het verzamelen van gegevens maken of de software voor het opnemen van audio gebruiken. U kunt ook een versie van uw toepassing maken die de audio gegevens registreert en vervolgens de gegevens gebruikt.

**V: moet ik de aanpassings gegevens zelf detranscriberen?**

**A**: Ja. U kunt het zelf detranscriberen of een professionele transcriptie-service gebruiken. Sommige gebruikers hebben de voor keur aan professionele transcribers en anderen gebruiken crowdsourcing of de transcripties zelf.

**V: hoe lang duurt het om een aangepast model met audio gegevens te trainen?**

**A**: het trainen van een model met audio gegevens kan een langdurige procedure zijn. Afhankelijk van de hoeveelheid gegevens kan het enkele dagen duren voordat een aangepast model is gemaakt. Als deze niet binnen een week kan worden voltooid, kan de service de trainings bewerking afbreken en het model rapporteren als mislukt.

Gebruik een van de [regio's](custom-speech-overview.md#set-up-your-azure-account) waar speciale hardware beschikbaar is voor training. De spraak service maakt gebruik van Maxi maal 20 uur aan audio voor de training in deze regio's. In andere regio's wordt Maxi maal 8 uur gebruikt.

Over het algemeen verwerkt de service ongeveer 10 uur aan audio gegevens per dag in regio's met specifieke hardware. Het kan alleen ongeveer 1 uur aan audio gegevens per dag in andere regio's verwerken. U kunt het volledig getrainde model naar een andere regio kopiëren met behulp van de [rest API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription). Training met alleen tekst is veel sneller en eindigt doorgaans binnen enkele minuten.

Sommige basis modellen kunnen niet worden aangepast met audio gegevens. Voor hen gebruikt de service alleen de tekst van de transcriptie voor training en worden de audio gegevens genegeerd. De training wordt vervolgens veel sneller uitgevoerd en de resultaten zijn hetzelfde als training met alleen tekst. Zie [taal ondersteuning](language-support.md#speech-to-text) voor een lijst met basis modellen die ondersteuning bieden voor training met audio gegevens.

## <a name="accuracy-testing"></a>Nauw keurig testen

**V: wat is een woord fout (WER) en hoe wordt dit berekend?**

**A**: wer is de evaluatie-metric voor spraak herkenning. WER wordt geteld als het totale aantal fouten, inclusief invoegingen, verwijderingen en vervangingen, gedeeld door het totale aantal woorden in de verwijzings transcriptie. Zie de [nauw keurigheid van Custom speech evalueren](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy)voor meer informatie.

**V: Hoe kan ik bepalen of de resultaten van een nauw keurigheid testen goed zijn?**

**A**: in de resultaten ziet u een vergelijking tussen het basis model en het model dat u hebt aangepast. U moet zich richten op het basis model om aanpassing van de voor keur te maken.

**V: Hoe kan ik de WER van een basis model te bepalen zodat ik kan zien of er een verbetering is opgetreden?**

**A**: de resultaten van de offline test tonen de nauw keurigheid van het aangepaste model en de verbetering van de basis lijn.

## <a name="creating-a-language-model"></a>Een taal model maken

**V: hoeveel tekst gegevens heb ik moeten uploaden?**

**A**: het hangt af van de manier waarop de woorden lijst en zinsdelen die in uw toepassing worden gebruikt, afkomstig zijn uit de start taal modellen. Voor alle nieuwe woorden is het handig om zo veel mogelijk voor beelden te bieden van het gebruik van deze woorden. Voor veelvoorkomende woord groepen die worden gebruikt in uw toepassing, met inbegrip van zinsdelen in de taal gegevens, is ook nuttig omdat het systeem ook kan Luis teren naar deze voor waarden. Het is gebruikelijk om ten minste 100 en meestal honderden of meer uitingen in de taal-gegevensset te hebben. Als sommige typen query's waarschijnlijk vaker worden gebruikt dan andere, kunt u meerdere exemplaren van de algemene query's in de gegevensset invoegen.

**V: kan ik gewoon een lijst met woorden uploaden?**

**A**: als u een lijst met woorden uploadt, worden de woorden toegevoegd aan de vocabulaire, maar wordt het systeem niet leren hoe de woorden doorgaans worden gebruikt. Door volledige of gedeeltelijke uitingen (zinnen of zinsdelen te bieden van dingen die gebruikers waarschijnlijk zeggen), kan het taal model de nieuwe woorden en hoe ze worden gebruikt, leren. Het aangepaste taal model is niet alleen geschikt voor het toevoegen van nieuwe woorden aan het systeem, maar ook voor het aanpassen van de kans op bekende woorden voor uw toepassing. Het bieden van een volledige uitingen helpt het systeem beter te leren.

## <a name="tenant-model-custom-speech-with-microsoft-365-data"></a>Tenant model (Custom Speech met Microsoft 365 gegevens)

**V: welke informatie is opgenomen in het Tenant model en hoe wordt het gemaakt?**

**A:** Een Tenant model wordt samengesteld op basis van e-mail berichten en documenten van [open bare groepen](https://support.microsoft.com/office/learn-about-microsoft-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2) die door iedereen in uw organisatie kunnen worden bekeken.

**V: welke spraak ervaring zijn verbeterd door het Tenant model?**

**A:** Wanneer het Tenant model is ingeschakeld, gemaakt en gepubliceerd, wordt het gebruikt voor het verbeteren van de herkenning van bedrijfs toepassingen die zijn gebouwd met behulp van de spraak service; Dit geeft ook een Azure AD-token van de gebruiker door dat het lidmaatschap van de onderneming aangeeft.

De spraak ervaring die is ingebouwd in Microsoft 365, zoals dicteer functie en Power Point-ondertiteling, wordt niet gewijzigd wanneer u een Tenant model maakt voor uw speech service-toepassingen.

## <a name="next-steps"></a>Volgende stappen

- [Problemen oplossen](troubleshooting.md)
- [Releaseopmerkingen](releasenotes.md)