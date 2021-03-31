---
title: Azure Internet Analyzer | Microsoft Docs
description: Meer informatie over Azure Internet Analyzer
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: overview
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 2512092c69390ce604c26ab6c5dd000afafc7c05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91360885"
---
# <a name="what-is-internet-analyzer-preview"></a>Wat is Internet Analyzer? (Preview)

Internet Analyzer is een meetplatform aan de clientzijde waarmee u kunt testen hoe wijzigingen in de netwerkinfrastructuur van invloed zijn op de prestaties voor uw klanten. Of u nu een migratie uitvoert van on-premises naar Azure of een nieuwe Azure-service evalueert, u met Internet Analyzer kunt u leren van informatie uit de gegevens van uw gebruikers en uitgebreide analyses van Microsoft om meer inzicht te krijgen in uw netwerkarchitectuur en om deze te optimaliseren met Azure: voordat u de migratie uitvoert.

Internet Analyzer gebruikt een kleine JavaScript-client die is ingebed in uw webtoepassing om de latentie van uw eindgebruikers te meten aan de geselecteerde set van netwerkbestemmingen die we _eindpunten_ noemen. Met Internet Analyzer kunt u meerdere tests naast elkaar instellen, zodat u verschillende scenario's kunt evalueren terwijl uw infrastructuur en de behoeften van de klant zich ontwikkelen. Internet Analyzer biedt aangepaste en vooraf geconfigureerde eindpunten die u het gemak en de flexibiliteit bieden voor het nemen van vertrouwde prestatiebeslissingen voor uw eindgebruikers. 


> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="quick--customizable-tests"></a>Snelle en aanpasbare tests

Internet Analyzer behandelt prestatiegerelateerde vragen voor cloudmigratie, implementeren in een nieuwe of aanvullende Azure-regio of het testen van nieuwe platforms voor toepassingen en inhoud in Azure, zoals [Azure Front Door](https://azure.microsoft.com/services/frontdoor/) en [Microsoft Azure CDN](https://azure.microsoft.com/services/cdn/). 

Elke test die u in Internet Analyzer maakt, bestaat uit twee eindpunten: eindpunt A en eindpunt B. De prestaties van eindpunt B worden geanalyseerd ten opzichte van eindpunt A. 

U kunt uw eigen aangepaste eindpunt configureren of een keuze selecteren uit een aantal vooraf geconfigureerde Azure-eindpunten. Aangepaste eindpunten worden gebruikt bij het evalueren van on-premises workloads, uw exemplaren bij andere cloudproviders en aangepaste Azure-configuraties. Tests kunnen bestaan uit twee aangepaste eindpunten; ten minste één aangepast eindpunt moet echter worden gehost in Azure. Vooraf geconfigureerde Azure-eindpunten zijn een snelle en eenvoudige manier om de prestaties van populaire Azure-netwerkplatforms zoals Azure Front Door, Azure Traffic Manager en Azure CDN te evalueren. 

Tijdens de preview zijn de volgende vooraf geconfigureerde eindpunten beschikbaar: 

* **Azure-regio's**
    * Brazil South
    * India - centraal
    * Central US
    * Azië - oost
    * VS - oost
    * Japan - west
    * Europa - noord
    * Zuid-Afrika - noord
    * Azië - zuidoost 
    * VAE - noord
    * Verenigd Koninkrijk West  
    * Europa -west
    * VS - west 
    * VS - west 2
* **Meerdere combinaties van Azure-regio's** 
    * US - oost, Brazilië - zuid 
    * US - oost, Azië - oost 
    * Europa - west, Brazilië - zuid
    * Europa - west, Azië - zuidoost
    * Europa - west, AE - noord
    * US - west, US - oost 
    * US - west, Europa - west
    * US - west, AE - noord
    * Europa - west, AE - noord, Azië - zuidoost
    * US - west, Europa - west, Azië - oost
    * US - west, Europa - noord, Azië - zuidoost, AE - noord, Zuid-Afrika - noord 
* **Azure + Azure Front Door** - geïmplementeerd op een of meer combinaties van Azure-regio's die hierboven worden vermeld
* **Azure + Azure CDN van Microsoft** - geïmplementeerd op één combinatie van Azure-regio's die hierboven worden vermeld
* **Azure + Azure Traffic Manager** - geïmplementeerd op meerdere combinaties van Azure-regio's die hierboven worden vermeld

## <a name="suggested-test-scenarios"></a>Voorgestelde testscenario's 

Om u te helpen de beste prestatiebeslissingen voor uw klanten te nemen, stelt Internet Analyzer u in staat om twee eindpunten te evalueren voor uw specifieke populatie van eindgebruikers. 

Hoewel Internet Analyzer een groot aantal vragen kan beantwoorden, zijn enkele van de meest voorkomende: 
* Wat is de invloed op prestaties van migratie naar de cloud? 
    * *Voorgestelde test: Aangepast (uw huidige on-premises infrastructuur) versus Azure (elk vooraf geconfigureerd eindpunt)*
* Wat is de waarde van het plaatsen van mijn gegevens aan de rand versus in een datacentrum? 
    *  *Voorgestelde test: Azure versus Azure Front Door, Azure versus Azure CDN van Microsoft*
* Wat is het prestatievoordeel van Azure Front Door?
    *  *Voorgestelde test: Aangepast/Azure/CDN versus Azure Front Door*
* Wat is het prestatievoordeel van Azure CDN van Microsoft? 
    *  *Voorgestelde test: Custom/ Azure/ AFD versus Azure CDN van Microsoft*
* Hoe verloopt Azure CDN van Microsoft? 
    *  *Voorgestelde test: Aangepast (ander CDN-eindpunt) versus Azure CDN van Microsoft*
* Wat is de beste cloud voor uw populatie eindgebruikers in elke regio? 
    *  *Voorgestelde test: Aangepast (andere cloudservice) versus Azure (elk vooraf geconfigureerd eindpunt)*

## <a name="how-it-works"></a>Uitleg

Als u Internet Analyzer wilt gebruiken, stelt u een Internet Analyzer-resource in Azure Portal in en installeert u de kleine JavaScript-client in uw toepassing. De client meet de latentie van uw eindgebruikers naar uw geselecteerde eindpunten door een afbeelding van één pixel via HTTPS te downloaden. Na het verzamelen van latentiemetingen verzendt de client de meetgegevens naar Internet Analyzer.

Wanneer een gebruiker de webtoepassing bezoekt, selecteert de JavaScript-client twee eindpunten om te meten over alle geconfigureerde tests. Voor elk eindpunt voert de cliënt een _koude_ en _warme_ meting uit. Bij de _koude_ meting treedt extra latentie op naast de pure netwerklatentie tussen de gebruiker en het eindpunt, zoals DNS-resolutie, handshake van TCP-verbinding en SSL/TLS-onderhandeling. De _warme_ meting volgt net na de _koude_ meting en maakt gebruik van het aanhoudende TCP-verbindingsbeheer van moderne browsers om een nauwkeurige meting van end-to-end latentie te krijgen. Wanneer het wordt ondersteund door de browser van de gebruiker, wordt de W3C resource timing-API gebruikt voor een nauwkeurige meettijd. Op dit moment worden alleen warme latentiemetingen gebruikt voor analyse.

![Diagram van een eindgebruiker die verbinding maakt met een toepassingsserver met ingesloten client en met de twee eindpunten op internet vanuit verschillende opties. De gebruiker uploadt metingen naar Internet Analyzer.](./media/ia-overview/architecture.png)


## <a name="scorecards"></a>Scorecards 

Zodra een test is gestart, worden telemetriegegevens zichtbaar in uw Internet Analyzer-resource onder het tabblad Scorecard. Deze gegevens worden altijd geaggregeerd. Gebruik de volgende filters om te wijzigen welke weergave van de gegevens u ziet: 

* **Test:** Selecteer de test waarvoor u de resultaten wilt weergeven. Testgegevens worden weergegeven zodra er voldoende gegevens zijn om de analyse te voltooien – in de meeste gevallen binnen 24 uur. 
* **Periode en einddatum:** Internet Analyzer genereert drie scorecards per dag - elke scorecard weerspiegelt een andere aggregatieperiode - de 24 uur voorafgaand (dag), de zeven dagen voorafgaand (week) en de 30 dagen voorafgaand (maand). Gebruik het filter Einddatum om de periode te selecteren die u wilt zien. 
* **Land:** Gebruik dit filter om gegevens te bekijken die specifiek zijn voor eindgebruikers die zich in een land bevinden. Met het globale filter worden gegevens over alle geografische gebieden weergegeven.  

Op de pagina [Uw scorecard interpreteren](internet-analyzer-scorecard.md) vindt u meer informatie over scorecards. 


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van uw eerste Internet Analyzer-resource](internet-analyzer-create-test-portal.md).
* Lees de [Veelgestelde vragen over Internet Analyzer](internet-analyzer-faq.md). 
