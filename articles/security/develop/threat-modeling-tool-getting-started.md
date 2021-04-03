---
title: Aan de slag-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Meer informatie over hoe u kunt beginnen met het Threat Modeling Tool. Maak een diagram, Identificeer bedreigingen, beperk bedreigingen en valideer elke oplossing.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: jegeib
ms.openlocfilehash: 322f5f6a79bdce23706b2211ccc04ef2451675d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94515724"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Aan de slag met de Threat Modeling Tool

De Microsoft Threat Modeling Tool 2018 is uitgebracht als GA in september 2018 als gratis **[Klik-en-down load](https://aka.ms/threatmodelingtool)**. Met de wijziging in het leverings mechanisme kunnen wij de nieuwste verbeteringen en oplossingen voor fouten naar klanten pushen telkens wanneer ze het hulp programma openen, waardoor het eenvoudiger te onderhouden en te gebruiken is.
In dit artikel wordt stapsgewijs beschreven hoe u aan de slag gaat met de micro soft SDL Threat Modeling-benadering en ziet u hoe u het hulp programma kunt gebruiken om fantastische bedreigings modellen te ontwikkelen als een backbone van uw beveiligings proces.

Dit artikel is gebaseerd op de bestaande kennis van de SDL Threat Modeling-aanpak. Raadpleeg voor een snelle beoordeling de **[webtoepassingen voor bedreigings modellen](/previous-versions/msp-n-p/ff648006(v=pandp.10))** en een gearchiveerde versie van **[beveiligings fouten tegen komen met behulp van het MSDN-artikel STRIDEe benadering](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** dat is gepubliceerd in 2006.

Als u snel wilt samenvatten, is het maken van een diagram, het identificeren van bedreigingen, het beperken van de oplossing en het valideren van elke oplossing. Hier volgt een diagram dat dit proces markeert:

![SDL-proces](./media/threat-modeling-tool-getting-started/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>Het proces voor het maken van bedreigings modellen starten

Wanneer u de Threat Modeling Tool start, ziet u enkele dingen, zoals in de afbeelding wordt weer gegeven:

![Lege start pagina](./media/threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Sectie bedreigings model

| Onderdeel                                   | Details                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Feedback, suggesties en problemen knop** | Hiermee gaat u naar het **[MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** voor alle zaken SDL. Het biedt u de mogelijkheid om te lezen wat andere gebruikers doen, samen met tijdelijke oplossingen en aanbevelingen. Als u nog steeds niet kunt vinden wat u zoekt, kunt u een e-mail sturen tmtextsupport@microsoft.com naar ons ondersteunings team om u te helpen                                                                                                                            |
| **Een model maken**                          | Hiermee opent u een leeg canvas om uw diagram te tekenen. Zorg ervoor dat u selecteert welke sjabloon u voor uw model wilt gebruiken                                                                                                                                                                                                                                                                                                                                                                       |
| **Sjabloon voor nieuwe modellen**                 | Voordat u een model maakt, moet u selecteren welke sjabloon u wilt gebruiken. Onze hoofd sjabloon is de Azure Threat model-sjabloon, die Azure-specifieke stencils, bedreigingen en oplossingen bevat. Voor algemene modellen selecteert u de SDL TM-Knowledge Base in de vervolg keuzelijst. Wilt u uw eigen sjabloon maken of een nieuwe opgeven voor alle gebruikers? Bekijk onze github-pagina voor **[sjabloon opslagplaatsen](https://github.com/Microsoft/threat-modeling-templates)** voor meer informatie                              |
| **Een model openen**                            | <p>Hiermee opent u eerder opgeslagen bedreigings modellen. De functie recent geopende modellen is handig als u uw meest recente bestanden moet openen. Wanneer u de muis aanwijzer over de selectie houdt, vindt u twee manieren om modellen te openen:</p><p><ul><li>Open op deze computer: klassieke manier om een bestand te openen met behulp van lokale opslag</li><li>Openen vanuit OneDrive: teams kunnen mappen in OneDrive gebruiken om al hun bedreigings modellen op één locatie op te slaan en te delen om de productiviteit en samen werking te verg Roten</li></ul></p> |
| **Hand leiding aan de slag**                   | Hiermee opent u de hoofd pagina van **[Microsoft Threat Modeling tool](threat-modeling-tool.md)**                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Sjabloon sectie

| Onderdeel               | Details                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Nieuwe sjabloon maken** | Hiermee opent u een lege sjabloon waarmee u kunt bouwen. Tenzij u uitgebreide kennis hebt bij het maken van nieuwe sjablonen, raden we u aan om bestaande te bouwen |
| **Sjabloon openen**       | Hiermee opent u de bestaande sjablonen waarmee u wijzigingen kunt aanbrengen                                                                                                              |

Het Threat Modeling Tool team is voortdurend bezig met het verbeteren van de functionaliteit en ervaring van het hulp programma. Enkele kleine wijzigingen kunnen plaatsvinden in de loop van het jaar, maar voor alle belang rijke wijzigingen moet herschrijven in de hand leiding worden uitgevoerd. Raadpleeg het regel matig om te controleren of u de laatste aankondigingen krijgt.

## <a name="building-a-model"></a>Een model bouwen

In deze sectie volgen we het volgende:

- Cristina (een ontwikkelaar)
- Ricardo (een programma manager) en
- Ashish (een tester)

Ze maken gebruik van het proces voor het ontwikkelen van het eerste bedreigings model.

> Ricardo: Hi Cristina, ik heb gewerkt aan het bedreigings model diagram en wil ervoor zorgen dat we het gegevens recht hebben ontvangen. Kunt u het zien?
> Cristina: absoluut. Laten we dit eens bekijken.
> Ricardo opent het hulp programma en deelt zijn scherm met Cristina.

![Basis risico model](./media/threat-modeling-tool-feature-overview/basictmt.png)

> Cristina: OK, dat is eenvoudig, maar u kunt er een stap mee doen?
> Ricardo: zeker! Dit is de uitsplitsing:
> - Onze menselijke gebruiker wordt getekend als een externe entiteit, een vier kant
> - Ze verzenden opdrachten naar onze webserver: de cirkel
> - De webserver is een Consulting van een Data Base (twee parallelle lijnen)

Wat Ricardo laat zien Cristina is een GSD-, kortere voor **[Data flow-diagram](https://en.wikipedia.org/wiki/Data_flow_diagram)**. Met de Threat Modeling Tool kunnen gebruikers vertrouwens grenzen opgeven, aangegeven door de rode stippel lijnen, om aan te geven waar verschillende entiteiten in het besturings element staan. IT-beheerders hebben bijvoorbeeld een Active Directory systeem voor verificatie doeleinden nodig, dus de Active Directory bevindt zich buiten het besturings element.

> Cristina: zoekt direct naar mij. Hoe zit het met de bedreigingen?
> Ricardo: laat mij u zien.

## <a name="analyzing-threats"></a>Bedreigingen analyseren

Zodra hij op de analyse weergave klikt vanuit de selectie van het pictogram menu (bestand met vergroot glas), wordt er een lijst met gegenereerde Threat Modeling Tool bedreigingen gemaakt die op basis van de standaard sjabloon is gevonden, die gebruikmaakt van de SDL-aanpak **[STRIDE (spoofing, knoeien, informatie vrijgeven, afwijzing, denial of service en uitbrei ding van bevoegdheden)](https://en.wikipedia.org/wiki/STRIDE_(security))**. Het idee is dat software onder een voorspel bare set bedreigingen komt, die u kunt vinden met behulp van deze 6 categorieën.

Deze methode is vergelijkbaar met het beveiligen van uw huis door ervoor te zorgen dat elke deur en elk venster een vergrendelings mechanisme heeft voordat een alarm systeem of Chasing wordt toegevoegd na de dief.

![Basis bedreigingen](./media/threat-modeling-tool-getting-started/basicthreats.png)

Ricardo begint met het selecteren van het eerste item in de lijst. Wat gebeurt er nu:

Ten eerste is de interactie tussen de twee stencils verbeterd

![In de scherm opname ziet u twee stencils en de gekromde pijl waarmee ze worden verbonden met een dikker gewicht lijn.](./media/threat-modeling-tool-getting-started/interaction.png)

Ten tweede wordt er meer informatie over de bedreiging weer gegeven in de dreiging venster Eigenschappen

![Scherm afbeelding toont de bedreigings venster Eigenschappen, die titel, categorie, beschrijving, interactie en prioriteit bevat.](./media/threat-modeling-tool-getting-started/interactioninfo.png)

De gegenereerde bedreiging helpt hem potentiële ontwerp fouten te begrijpen. De STRIDE-categorisatie geeft u een idee over mogelijke aanvals vectoren, terwijl de extra beschrijving er precies op kan wijzen wat er mis is, samen met mogelijke manieren om het probleem te verhelpen. Hij kan Bewerk bare velden gebruiken om notities te schrijven in de details van de motivering of om prioriteits niveaus te wijzigen, afhankelijk van de fout balk van de organisatie.

Azure-sjablonen bevatten aanvullende details waarmee gebruikers niet alleen kunnen begrijpen wat er mis is, maar ook hoe ze kunnen worden opgelost door beschrijvingen, voor beelden en hyper links toe te voegen aan specifieke documentatie over Azure.

De beschrijving die hem heeft gegeven, is het belang rijk om een verificatie mechanisme toe te voegen om te voor komen dat gebruikers worden vervalst, waardoor de eerste dreiging wordt onthuld waaraan wordt gewerkt. Een paar minuten in de discussie met Cristina, ze begrijpen het belang van het implementeren van toegangs beheer en-rollen. Ricardo heeft enkele snelle notities ingevuld om er zeker van te zijn dat deze zijn geïmplementeerd.

Zoals Ricardo in de bedreigingen onder het vrijgeven van informatie heeft gestaan, heeft hij het Access Control-plan een aantal alleen-lezen accounts nodig voor het genereren van controles en rapporten. Hij heeft gevraag of dit een nieuwe bedreiging zou moeten zijn, maar de oplossingen waren hetzelfde, zodat hij de dreiging dienovereenkomstig heeft genoteerd.
Hij heeft ook gedacht dat informatie over een beetje meer en gerealiseerd dat de back-uptapes versleuteling nodig hadden, een taak voor het operationele team.

Bedreigingen die niet van toepassing zijn op het ontwerp door bestaande oplossingen of beveiligings garanties kunnen worden gewijzigd in ' niet van toepassing ' in de vervolg keuzelijst status. Er zijn drie andere opties: niet gestart – standaard selectie, vereist onderzoek: wordt gebruikt voor het opvolgen van items en het oplossen van problemen – zodra het volledig is uitgevoerd.

## <a name="reports--sharing"></a>Rapporten & delen

Nadat Ricardo de lijst met Cristina heeft door lopen en belang rijke opmerkingen, verkleiningen/motieven, prioriteit en status wijzigingen toevoegt, selecteert hij Reports-> volledig rapport maken-> rapport opslaan, waarin u een goed rapport kunt afdrukken.

![Scherm afbeelding toont een representatief bedreigings model rapport.](./media/threat-modeling-tool-feature-overview/report.png)

Als Ricardo het bestand wil delen, kan hij dit eenvoudig doen door het OneDrive-account van de organisatie op te slaan. Zodra hij dat doet, kan hij de document koppeling kopiëren en delen met zijn collega's. 

## <a name="threat-modeling-meetings"></a>Threat model-vergaderingen

Wanneer Ricardo zijn bedreigings model naar zijn collega heeft verzonden met behulp van OneDrive, Ashish, de tester, werd underwhelmed. Leek bijvoorbeeld Ricardo en Cristina gemist enkele belang rijke hoek gevallen, die eenvoudig kunnen worden aangetast. Zijn skepticism is een aanvulling op bedreigings modellen.

In dit scenario heeft hij nadat Ashish het bedreigings model heeft geduurd, opgeroepen voor twee bedreigingen voor bedreigings modellering: één vergadering om op het proces te synchroniseren en door loop de diagrammen en vervolgens een tweede vergadering voor het beoordelen en afmelden van bedreigingen.

In de eerste vergadering heeft Ashish 10 minuten besteed aan iedereen via het SDL Threat model proces. Vervolgens stelt hij het diagram van het bedreigings model samen en wordt het uitgebreid uitgelegd. Binnen vijf minuten is een belang rijk onderdeel geïdentificeerd.

Een paar minuten later hebben Ashish en Ricardo een uitgebreide bespreking ontvangen van de manier waarop de webserver is gebouwd. Het was niet de ideale manier om een vergadering voort te zetten, maar iedereen die uiteindelijk heeft besloten dat het verschil zich voordoet, werd in de toekomst tijd bespaard.

In de tweede vergadering heeft het team werd uitgelegd hoe door de bedreigingen, besproken hoe ze zich kunnen aanpakken en afgemeld op het bedreigings model. Ze hebben het document gecontroleerd in broncode beheer en blijven de ontwikkeling voortzetten.

## <a name="thinking-about-assets"></a>Nadenken over assets

Sommige lezers die bedreigingen hebben gemodelleerd, kunnen merken dat we nog niet over assets hebben gesp roken. We hebben ontdekt dat veel software technici hun software beter begrijpen dan het concept van assets en welke activa een aanvaller mogelijk geïnteresseerd is in.

Als u een bedreigings model maakt, kunt u beginnen met uw familie, onvervangbare Foto's of waardevolle illustraties. Misschien kunt u beginnen met het nadenken over wie zich kan afbreeken en het huidige beveiligings systeem. Of u kunt beginnen met het overwegen van de fysieke functies, zoals de groep of de front-porch. Deze zijn vergelijkbaar met het nadenken over assets, aanvallers of software ontwerp. Een van deze drie benaderingen werkt.

De aanpak van bedreigings modellen die we hier hebben gepresenteerd, is aanzienlijk eenvoudiger dan wat micro soft in het verleden heeft gedaan. We hebben vastgesteld dat de aanpak van het software ontwerp goed werkt voor veel teams. We hopen dat u dat ook hebt.

## <a name="next-steps"></a>Volgende stappen

Stuur uw vragen, opmerkingen en problemen naar tmtextsupport@microsoft.com . **[Down load](https://aka.ms/threatmodelingtool)** de Threat Modeling tool om aan de slag te gaan.