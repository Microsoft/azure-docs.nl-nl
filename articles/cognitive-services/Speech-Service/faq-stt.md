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
ms.date: 08/20/2020
ms.author: panosper
ms.openlocfilehash: 7c0b399f84e793d9e762c89dbbd1901bb5a95f26
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024324"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Veelgestelde vragen over spraak naar tekst

Als u in deze veelgestelde vragen geen antwoorden op uw vragen kunt vinden, kunt u [Andere ondersteunings opties](../cognitive-services-support-options.md?context=%252fazure%252fcognitive-services%252fspeech-service%252fcontext%252fcontext%253fcontext%253d%252fazure%252fcognitive-services%252fspeech-service%252fcontext%252fcontext)bekijken.

## <a name="general"></a>Algemeen

**V: wat is het verschil tussen een basislijn model en een aangepaste spraak op het tekst model?**

**A**: een basislijn model is getraind met behulp van gegevens van micro soft en is al geïmplementeerd in de Cloud. U kunt een aangepast model gebruiken om een model aan te passen aan een specifieke omgeving met specifieke omgevings ruis of-taal. In fabrieks-, auto-of geluids-en geluids eigen straten is een aangepast akoestisch model vereist. Voor onderwerpen als biologie, natuur kunde, radiology, product namen en aangepaste acroniemen zou een aangepast taal model nodig zijn.

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

**V: wat is het verschil tussen het zoek-en dicteer model en het gespreks model?**

**A**: u kunt kiezen uit meer dan één basislijn model in de speech-service. Het gespreks model is handig voor het herkennen van spraak die in een gespreks stijl wordt gesp roken. Dit model is ideaal voor het transcriberen van telefoon gesprekken. Het model voor zoeken en dicteren is ideaal voor apps met spraak activering. Het universele model is een nieuw model dat gericht is op beide scenario's. Het universele model bevindt zich momenteel op of boven het kwaliteits niveau van het gespreks model in de meeste landen.

**V: kan ik mijn bestaande model (model stacking) bijwerken?**

**A**: u kunt een bestaand model niet bijwerken. Als oplossing kunt u de oude gegevensset combi neren met de nieuwe gegevensset en deze opnieuw aanpassen.

De oude gegevensset en de nieuwe gegevensset moeten worden gecombineerd in één ZIP-bestand (voor akoestische gegevens) of in een txt-bestand (voor taal gegevens). Wanneer de aanpassing is voltooid, moet het nieuwe, bijgewerkte model opnieuw worden geïmplementeerd om een nieuw eind punt te verkrijgen

**V: wanneer een nieuwe versie van een basis lijn beschikbaar is, wordt mijn implementatie automatisch bijgewerkt?**

**A**: implementaties worden niet automatisch bijgewerkt.

Als u een model hebt aangepast en geïmplementeerd met basis lijn V 1.0, blijft die implementatie ongewijzigd. Klanten kunnen het geïmplementeerde model buiten gebruik stellen, opnieuw aanpassen met de nieuwere versie van de basis lijn en opnieuw implementeren.

**V: kan ik mijn model downloaden en lokaal uitvoeren?**

**A**: modellen kunnen niet lokaal worden gedownload en uitgevoerd.

**V: zijn mijn aanvragen geregistreerd?**

**A**: standaard aanvragen worden niet geregistreerd (audio of transcriptie). Indien nodig kunt u *logboek inhoud van deze eindpunt* optie selecteren wanneer u [een aangepast eind punt maakt](how-to-custom-speech-deploy-model.md) om tracering in te scha kelen. Vervolgens worden aanvragen geregistreerd in Azure in beveiligde opslag.

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

**A**: de limiet wordt veroorzaakt door de beperking van de grootte van een bestand voor http-upload. Zie de [quota en limieten voor spraak Services](speech-services-quotas-and-limits.md) voor de daad werkelijke limiet.

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

**A**: Ja! U kunt het zelf detranscriberen of een professionele transcriptie-service gebruiken. Sommige gebruikers hebben de voor keur aan professionele transcribers en anderen gebruiken crowdsourcing of de transcripties zelf.

## <a name="accuracy-testing"></a>Nauw keurig testen

**V: kan ik offline testen van mijn aangepaste akoestische model uitvoeren met behulp van een aangepast taal model?**

**A**: Ja, alleen het aangepaste taal model selecteren in de vervolg keuzelijst bij het instellen van de offline test.

**V: kan ik offline testen van mijn aangepaste taal model uitvoeren met behulp van een aangepast akoestisch model?**

**A**: Ja, selecteer het aangepaste geluids model in de vervolg keuzelijst bij het instellen van de offline test.

**V: wat is een woord fout (WER) en hoe wordt dit berekend?**

**A**: wer is de evaluatie-metric voor spraak herkenning. WER wordt geteld als het totale aantal fouten, inclusief invoegingen, verwijderingen en vervangingen, gedeeld door het totale aantal woorden in de verwijzings transcriptie. Zie [Word-fout frequentie](https://en.wikipedia.org/wiki/Word_error_rate)voor meer informatie.

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