---
title: Spraak assistenten in Windows-ontwerp richtlijnen
titleSuffix: Azure Cognitive Services
description: Richt lijnen voor aanbevolen procedures voor het ontwerpen van een spraak agent.
services: cognitive-services
author: cfogg6
manager: spencer.king
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: adamwa
ms.openlocfilehash: 76a29af80891142fcf7f56f93f5c7acc5c58ab04
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92165142"
---
# <a name="design-assistant-experiences-for-windows-10"></a>Ontwerp assistent-ervaring voor Windows 10

Voor spraak assistenten die zijn ontwikkeld in Windows 10 moeten de richt lijnen voor gebruikers ervaring worden geïmplementeerd om de beste ervaring te bieden voor spraak activering in Windows 10. In dit document worden ontwikkel aars begeleid bij het inzicht in het belangrijkste werk dat nodig is voor een telefoon assistent die kan worden geïntegreerd met de Windows 10-shell.

## <a name="contents"></a>Inhoud

- [Overzicht van de weer gaven voor spraak activering die worden ondersteund in Windows 10](#summary-of-voice-activation-views-supported-in-windows-10)
- [Samen vatting van vereisten](#requirements-summary)
- [Aanbevolen procedures voor een goede Luister ervaring](#best-practices-for-good-listening-experiences)
- [Ontwerp richtlijnen voor spraak activering in app](#design-guidance-for-in-app-voice-activation)
- [Ontwerp richtlijnen voor spraak activering boven slot](#design-guidance-for-voice-activation-above-lock)
- [Ontwerp richtlijnen voor preview van spraak activering](#design-guidance-for-voice-activation-preview)

## <a name="summary-of-voice-activation-views-supported-in-windows-10"></a>Overzicht van de weer gaven voor spraak activering die worden ondersteund in Windows 10

Windows 10 leidt tot een activerings ervaring voor de context van de klant op basis van de context van het apparaat. De volgende samenvattings tabel is een overzicht op hoog niveau van de verschillende weer gaven die beschikbaar zijn wanneer het scherm is ingeschakeld.

| Weer gave (Beschik baarheid) | Apparaatcontext | Doel van de klant | Wordt weer gegeven wanneer | Ontwerp behoeften |
| --- | --- | --- | --- | --- |
| **In-app (19H1)** | Onder slot heeft de assistent de focus | Interactie met de app assistent | Assistent verwerkt de aanvraag in-app | Luister ervaring voor de hoofd weergave van de app |
| **Boven slot (19H2)** | Boven slot, niet-geverifieerd | Interactie met de assistent, maar vanaf een afstand | Het systeem is vergrendeld en de activering van de assistent is | Visuele elementen in volledig scherm voor de gebruikers interface van het ver-veld. Het beleid voor het weigeren van de ontgrendeling wordt geïmplementeerd. |
| **Preview van spraak activering (20H1)** | De assistent heeft de focus niet onder slot | Interactie met de assistent, maar op een minder storende manier | Het systeem is onder slot en assistent-aanvragen voor achtergrond activering | Minimale canvas. Wijzig de grootte van de app-weer gave naar behoefte of hand matig. |

## <a name="requirements-summary"></a>Samen vatting van vereisten

Er is minimale inspanning vereist om toegang te krijgen tot de verschillende ervaringen. Assistenten moeten echter de juiste ontwerp richtlijnen implementeren voor elke weer gave. Deze tabel bevat een controle lijst met de vereisten die moeten worden gevolgd.

| **Weer gave spraak activering** | **Samen vatting van assistent-vereisten** |
| --- | --- |
| **In-app** | <ul><li>De aanvraag in-app verwerken</li><li>Biedt UI-indica toren voor Luister statussen</li><li>Gebruikers interface wordt aangepast als de venster grootten wordt gewijzigd</li></ul> |
| **Boven vergren deling** | <ul><li>Vergrendelings status en activering van aanvragen detecteren</li><li>Bieden altijd permanente UX waarmee de toegang tot het Windows-vergrendelings scherm wordt geblokkeerd</li><li>Een visuele weer gave van het volledige scherm en een spraak-eerste ervaring bieden</li><li>De onderstaande instructies voor negeren</li><li>Volg de onderstaande veiligheids overwegingen voor privacy en beveiliging</li></ul> |
| **Preview van spraak activering** | <ul><li>Status van ontgrendelen detecteren en achtergrond activering aanvragen</li><li>Mini maal luisterende UX in het voorbeeld venster tekenen</li><li>Een close X in de rechter bovenhoek tekenen en de audio gegevensstromen stoppen wanneer deze wordt ingedrukt</li><li>Het formaat van de app-weer gave voor de hoofd assistent zo nodig verg Roten of verkleinen om antwoorden te geven</li></ul> |

## <a name="best-practices-for-good-listening-experiences"></a>Aanbevolen procedures voor een goede Luister ervaring

Assistenten moeten een luister ervaring bouwen om kritieke feedback te geven zodat de klant de status van de assistent kan begrijpen. Hieronder ziet u enkele mogelijke statussen bij het bouwen van een assistent-ervaring. Dit zijn alleen mogelijke suggesties, geen verplichte richt lijnen.

- De assistent is beschikbaar voor spraak invoer
- Het activeren van de assistent (een tref woord of mic op de knop)
- De assistent is actief streaming van audio naar de assistent-Cloud
- De assistent is klaar voor de klant om te beginnen met spreken
- De assistent is te horen dat er woorden worden genoemd
- Assistent begrijpt dat de klant klaar is met spreken
- De assistent is bezig met het verwerken en voorbereiden van een reactie
- De assistent reageert

Zelfs als statussen snel veranderen, is het een goed idee om UX voor staten te bieden, aangezien de duur van het Windows-ecosysteem variabel is. Visuele feedback en korte audio klokken of chirps, ook wel earcons genoemd, &quot; &quot; kunnen deel uitmaken van de oplossing. Op dezelfde manier kunnen visuele kaarten die zijn gekoppeld aan audio beschrijvingen, goed reageren op Opties.

## <a name="design-guidance-for-in-app-voice-activation"></a>Ontwerp richtlijnen voor spraak activering in app

Wanneer de assistent-app de focus heeft, is de klant intentie duidelijk te communiceren met de app, zodat alle spraak activeringen worden afgehandeld door de weer gave van de hoofd toepassing. Het formaat van deze weer gave kan worden gewijzigd door de klant. De rest van dit document maakt gebruik van het concrete voor beeld van een financiële service-assistent met de naam contoso om assistent-shell interacties te verklaren. In deze en volgende diagrammen wordt de klant aangegeven dat deze wordt weer gegeven in de TTS-spraak bellen aan de linkerkant met de assistent-antwoorden in de bolletjes bellen aan de rechter kant.

**Weer gave in de app. Begin status wanneer de activering van de telefoon begint:** 
 ![ scherm opname met de app contoso Financiën Assistant open op de standaard canvas. Een tekststrip-spraak ballon aan de rechter kant geeft ' Contoso ' aan.](media/voice-assistants/windows_voice_assistant/initial_state.png)

**Weer gave in de app. Na een geslaagde spraak activering begint de luister ervaring:** ![ scherm opname van de Voice Assistant in Windows terwijl de Voice Assistant luistert](media/voice-assistants/windows_voice_assistant/listening.png)

**Weer gave in de app. Alle reacties blijven in de app-ervaring.** ![ Scherm opname van de Voice Assistant op Windows als assistent-antwoorden](media/voice-assistants/windows_voice_assistant/response.png)

## <a name="design-guidance-for-voice-activation-above-lock"></a>Ontwerp richtlijnen voor spraak activering boven slot

Assistenten die beschikbaar zijn met 19H2 zijn beschikbaar voor een Windows Voice-activerings platform en kunnen hiervoor het antwoord geven dat hierboven wordt vermeld.

### <a name="customer-opt-in"></a>Opt-in voor klant

Spraak activering boven slot is altijd standaard uitgeschakeld. Klanten kunnen zich aanmelden via de Windows-instellingen>privacy>Voice-activering. Zie de [bovenstaande hand leiding voor het vergren delen](windows-voice-assistants-implementation-guide.md#detecting-above-lock-activation-user-preference)van de implementatie voor meer informatie over het controleren en vragen om deze instelling.

### <a name="not-a-lock-screen-replacement"></a>Geen vergrendelings scherm vervangen

Hoewel meldingen of andere standaard app-vergrendelings punten voor het vergren delen van apps beschikbaar blijven voor de assistent, wordt in het Windows-vergrendelings scherm altijd de eerste klant ervaring gedefinieerd totdat een spraak activering wordt uitgevoerd. Nadat de spraak activering is gedetecteerd, wordt de app assistent tijdelijk weer gegeven boven het vergrendelings scherm. Als u de Verwar ring van klanten wilt voor komen, moet de app de gebruikers interface nooit in de apps van de assistent stellen om te vragen om eventuele referenties of zich aan te melden.

![Scherm opname van een Windows-vergrendelings scherm](media/voice-assistants/windows_voice_assistant/lock_screen1.png)

### <a name="above-lock-experience-following-voice-activation"></a>Bovenstaande Vergrendel ervaring na spraak activering

Wanneer het scherm is ingeschakeld, is de app assistent volledig scherm zonder titel balk boven het vergrendelings scherm. Grotere visuele elementen en sterke spraak beschrijvingen met de krachtige interface voor spraak-primair bieden de mogelijkheid om te voor komen dat de klant te ver naar de gebruikers interface kan lezen of dat de hand een andere (niet-PC) taak heeft.

Wanneer het scherm wordt uitgeschakeld, kan de app van de assistent een earcon afspelen om aan te geven dat de assistent is geactiveerd en een alleen spraak ervaring biedt.

![Scherm afbeelding van de bovenstaande Voice-assistent vergren delen](media/voice-assistants/windows_voice_assistant/above_lock_listening.png)

### <a name="dismissal-policies"></a>Beleid voor negeren

De-assistent moet de instructies voor het afwijzen van de app in deze sectie implementeren om het voor klanten gemakkelijker te maken zich aan te melden bij de volgende keer dat ze hun Windows-PC willen gebruiken. Hieronder vindt u specifieke vereisten, die door de assistent moeten worden geïmplementeerd:

- **Alle assistentvormen die boven de vergren deling worden weer gegeven, moeten een X** in de rechter bovenhoek van de assistent bevatten.
- Als u **op een wille keurige toets drukt, moet u ook de app assistent sluiten**. Toetsenbord invoer is een traditioneel signaal voor het vergren delen van apps dat de klant wil aanmelden. Daarom mogen toetsen bord/tekst invoer niet worden omgeleid naar de app. In plaats daarvan moet de app zichzelf sluiten wanneer de toetsenbord invoer wordt gedetecteerd, zodat de klant zich eenvoudig kan aanmelden bij hun apparaat.
- **Als het scherm wordt uitgeschakeld, moet de app zichzelf negeren.** Dit zorgt ervoor dat de gebruiker de volgende keer dat de klant de PC gebruikt, het aanmeldings scherm wordt weer gegeven en op de computer wacht.
- Als de app wordt &quot; gebruikt &quot; , kan deze boven de vergren deling worden voortgezet. &quot;in gebruik is &quot; een wille keurige invoer of uitvoer. Bijvoorbeeld: wanneer u muziek of video stroomsgewijst, kan de app de vergren deling boven slot voortzetten. &quot;Volg &quot; de stappen in en andere dialoog vensters voor multidraaien om de app boven slot te houden.
- **Implementatie Details over het negeren van de toepassing** vindt u [in de bovenstaande implementatie handleiding voor vergren delen](windows-voice-assistants-implementation-guide.md#closing-the-application).

![Scherm afbeelding met de bovenstaande vergrendelings weergave van de app contoso Finance Assistant.](media/voice-assistants/windows_voice_assistant/above_lock_response.png)

![Scherm afbeelding van een bureau blad met het vergrendelings scherm van Windows.](media/voice-assistants/windows_voice_assistant/lock_screen2.png)

### <a name="privacy-amp-security-considerations-above-lock"></a>&amp;Beveiligings overwegingen voor privacy boven vergren deling

Veel Pc's zijn draagbaar, maar niet altijd binnen het bereik van de klant. Ze zijn mogelijk kort in Hotel kamers, stoelen van vlieg tuigen of werk ruimten, waarbij anderen fysieke toegang hebben. Als assistenten die boven de vergren deling zijn ingeschakeld, niet zijn voor bereid, kunnen ze worden onderhevig aan de klasse van zogenaamde &quot; [kwaad Maid](https://en.wikipedia.org/wiki/Evil_maid_attack) - &quot; aanvallen.

Daarom moeten assistenten de richt lijnen in deze sectie volgen om uw ervaring te beveiligen. Interactie boven slot vindt plaats wanneer de Windows-gebruiker niet is geverifieerd. Dit betekent dat in **het algemeen ook invoer naar de assistent moet worden behandeld als niet-geverifieerd**.

- Assistenten moeten **een lijst met toegestane vaardig heden implementeren om te bepalen welke kwalificaties veilig en veilig toegankelijk zijn voor de** bovenstaande vergren deling.
- Luidspreker-ID-technologieën kunnen een rol spelen bij het verlichten van enkele Risico's, maar de luidspreker-ID is geen geschikte vervanging voor Windows-verificatie.
- In de lijst met toegestane vaardig heden moet rekening worden gehouden met drie klassen van acties of vaardig heden:

| **Actie klasse** | **Beschrijving** | **Voor beelden (geen volledige lijst)** |
| --- | --- | --- |
| Veilig zonder verificatie | Algemene informatie over het gebruik of de opdracht en het beheer van de Basic-app | &quot;Wat is de tijd? &quot; , &quot; het volgende nummer afspelen&quot; |
| Veilig met luid spreker-ID | Imitatie risico, persoonlijke gegevens onthullen. | &quot;Wat&#39;s mijn volgende afspraak? &quot; , &quot; mijn boodschappen lijstnummer bekijken &quot; en &quot; de oproep beantwoorden&quot; |
| Veilig alleen na Windows-authenticatie | Acties met een hoog risico die een aanvaller zou kunnen gebruiken om de klant te beschadigen | &quot;Koop meer boodschappen &quot; , &quot; Verwijder mijn (belang rijk) afspraak &quot; , &quot; Verzend een (gemiddelde) SMS &quot; -bericht, &quot; Start een webpagina (kwaadwillend)&quot; |

In het geval van Contoso is algemene informatie over open bare aandelen informatie veilig, zonder verificatie. Klantspecifieke informatie, zoals het eigendoms aantal van shares, is waarschijnlijk veilig met de spreker-ID. Koop-of verkoop voorraden mogen echter nooit worden toegestaan zonder Windows-verificatie.

Om de ervaring verder te beveiligen, **worden webkoppelingen of andere startende app-naar-app altijd geblokkeerd door Windows tot de klant zich aanmeldt.** Als last van de laatste oplossing behoudt micro soft het recht om een toepassing te verwijderen uit de lijst met ingeschakelde assistenten als een ernstig beveiligings probleem niet tijdig wordt opgelost.

## <a name="design-guidance-for-voice-activation-preview"></a>Ontwerp richtlijnen voor preview van spraak activering

Op het moment dat de assistent-app _niet_ de focus heeft, biedt Windows een minder OPVALLENDERE gebruikers interface voor spraak activering om de klant in stroom te houden. Dit geldt met name voor het geval van onwaare activeringen die zeer storend zouden zijn als ze de volledige app hebben gestart. Het belangrijkste idee is dat elke assistent een eigen huis heeft in de shell, het taak balk pictogram van de assistent. Wanneer de aanvraag voor de achtergrond wordt geactiveerd, wordt er een kleine weer gave boven het pictogram van de assistent op de taak balk weer gegeven. Assistenten moeten op dit canvas een kleine Luister ervaring bieden. Na het verwerken van de aanvragen kunnen assistenten het formaat van deze weer gave wijzigen om een in-context antwoord weer te geven of om de weer gave van de hoofd toepassing uit te voeren om grotere, gedetailleerdere visuele elementen weer te geven.

- Om het minimale te blijven, heeft de preview geen titel balk, dus **moet de assistent een X in de rechter bovenhoek tekenen zodat klanten de weer gave kunnen sluiten.** Raadpleeg [de toepassing sluiten](windows-voice-assistants-implementation-guide.md#closing-the-application) voor de specifieke api's die moeten worden aangeroepen wanneer de knop verwijderen wordt ingedrukt.
- Voor het ondersteunen van voor beelden van spraak activering kunnen assistenten klanten uitnodigen om de assistent aan de taak balk vast te maken tijdens de eerste uitvoering.

**Preview van spraak activering: begin status**

De assistent Contoso heeft een start op de taak balk: hun krullen, rond pictogram.

![Scherm afbeelding van de Voice Assistant op Windows als een taak balk pictogram vooraf activeren](media/voice-assistants/windows_voice_assistant/pre_compact_view.png)

**Wanneer de activering wordt uitgevoerd**, vraagt de assistent de activering van de achtergrond aan. De assistent krijgt een klein voorbeeld venster (standaard breedte 408 en hoogte: 248). Als Voice-activering aan de server zijde bepaalt dat het signaal een onjuiste waarde heeft, kan deze weer gave worden genegeerd voor een minimale onderbreking.

![Scherm opname van de Voice Assistant op Windows in compacte weer gave tijdens het controleren van de activering](media/voice-assistants/windows_voice_assistant/compact_view_activating.png)

**Wanneer de laatste activering is bevestigd**, presenteert de assistent de luister ervaring. De assistent moet altijd een Negeer X in de rechter bovenhoek van de preview-versie van spraak activering tekenen.

![Scherm afbeelding van de Voice Assistant op Windows-Luis teren in compacte weer gave](media/voice-assistants/windows_voice_assistant/compact_view_listening.png)

In de preview-versie van spraak activering worden **snelle antwoorden** weer gegeven. Met een TryResizeView kunnen assistenten verschillende grootten aanvragen.

![Scherm afbeelding van de Voice Assistant op Windows-antwoorden in de compacte weer gave](media/voice-assistants/windows_voice_assistant/compact_view_response.png)

**Hand**. De assistent kan op elk gewenst moment de weer gave van de hoofd toepassing afleveren om meer informatie, dialoog venster of antwoorden te bieden die meer scherm onroerend goed vereisen. Raadpleeg de sectie [overgang van compacte weer gave naar volledige weer gave](windows-voice-assistants-implementation-guide.md#transition-from-compact-view-to-full-view) voor informatie over de implementatie.

![Scherm afbeeldingen van de Voice Assistant in Windows voor en na het uitbreiden van de compacte weer gave](media/voice-assistants/windows_voice_assistant/compact_transition.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met het ontwikkelen van uw Voice Assistant](how-to-windows-voice-assistants-get-started.md)