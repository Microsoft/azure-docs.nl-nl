---
title: Overgangs gids voor Azure Monitor Designer naar werkmappen
description: Overstappen van weer gaven naar werkmappen in Azure Monitor.
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 7c43d1e4dda10cca6b868ef814ec8298d6e21fd0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736964"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Overgangs gids voor Azure Monitor Designer naar werkmappen
[View Designer](view-designer.md) is een functie van Azure monitor waarmee u aangepaste weer gaven kunt maken waarmee u gegevens in uw werk ruimte log Analytics kunt visualiseren, met grafieken, lijsten en tijd lijnen. Ze zijn overgegaan naar werkmappen om een flexibel canvas te bieden voor gegevens analyse en het maken van uitgebreide visuele rapporten in de Azure Portal. Dit artikel helpt u bij het maken van de overgang van de weer gave Designer naar werkmappen. 


## <a name="workbooks-overview"></a>Overzicht van werkmappen
[Werkmappen](../vm/vminsights-workbooks.md) combi neren tekst, [logboek query's](/azure/data-explorer/kusto/query/), metrische gegevens en para meters in uitgebreide interactieve rapporten. Team leden met dezelfde toegang tot Azure-resources kunnen ook werkmappen bewerken.

Werkmappen zijn handig voor scenario's zoals:

-   Het gebruik van uw virtuele machine verkennen wanneer u de metrische gegevens die van belang zijn, vooraf niet kent: CPU-gebruik, schijf ruimte, geheugen, netwerk afhankelijkheden, enzovoort. In tegens telling tot andere hulpprogram ma's voor gebruiks analyse kunt u met werkmappen meerdere soorten visualisaties en analyses combi neren, waardoor ze ideaal zijn voor dit soort vrije-vorm onderzoek.
-   Uitleg over uw team hoe een onlangs ingerichte virtuele machine wordt uitgevoerd, door metrische gegevens voor sleutel items en andere logboek gebeurtenissen weer te geven.
-   Het delen van de resultaten van een experiment voor het wijzigen van de grootte van uw virtuele machine met andere leden van uw team. U kunt de doel stellingen voor het experiment uitleggen met tekst, vervolgens elk gebruik van metrische gegevens en analyse query's weer geven die worden gebruikt voor het evalueren van het experiment, samen met duidelijke aanroep-outs voor of elke metriek boven of onder het doel ligt.
-   Rapportage van de impact van een storing in het gebruik van uw virtuele machine, het combi neren van gegevens, tekst uitleg en een bespreking van de volgende stappen om te voor komen dat er storingen in de toekomst worden vermeden.


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>Waarom converteer ik weergave Designer-Dash boards naar werkmappen?

De weer gave Designer biedt de mogelijkheid om verschillende op query's gebaseerde weer gaven en visualisaties te genereren. Aanpassingen op hoog niveau blijven echter beperkt, zoals bijvoorbeeld het formatteren van rasters en tegelindelingen of het selecteren van alternatieve graphics om uw gegevens weer te geven. De weer gave Designer is beperkt tot een totaal van negen afzonderlijke tegels om uw gegevens weer te geven.

Workbooks is een platform dat het volledige potentieel van uw gegevens benut. werkmappen behouden niet alleen alle mogelijkheden, maar bieden ook ondersteuning voor aanvullende functionaliteit via tekst, metrische gegevens, para meters en nog veel meer. Met werkmappen kunnen gebruikers bijvoorbeeld dichte rasters consolideren en zoek balken toevoegen om de gegevens eenvoudig te filteren en te analyseren. 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>Voor delen van het gebruik van werkmappen over de ontwerp functie voor weer gaven

* Ondersteunt logboeken en metrische gegevens.
* Hiermee worden zowel persoonlijke weer gaven voor afzonderlijke Access Control-als gedeelde werkmap weergaven toegestaan.
* Aangepaste indelings opties voor de besturings elementen tabs, formaat wijzigen en schalen.
* Ondersteuning voor het uitvoeren van query's op meerdere Log Analytics werk ruimten, Application Insights toepassingen en abonnementen.
* Hiermee schakelt u aangepaste para meters in waarmee gekoppelde grafieken en visualisaties dynamisch worden bijgewerkt.
* Sjabloon galerie biedt ondersteuning voor open bare GitHub.

Hoewel deze hand leiding eenvoudige stappen biedt om een aantal veelgebruikte weer gave-ontwerp weergaven te maken, kunnen gebruikers met werkmappen de vrijheid hebben om een eigen aangepaste visualisaties en metrische gegevens te maken en te ontwerpen. De volgende scherm afbeelding is afkomstig uit de [sjabloon gebruiks werkruimte](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook) en toont een voor beeld van wat werkmappen kan maken:


![Voor beeld van werkmappen-toepassing](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>Het gebruik van werkmappen starten
Werkmappen openen vanuit de werkmappen zijn ingeschakeld in Log Analytics werk ruimten als een item in de navigatie balk aan de zijkant, direct onder de locatie weergave ontwerp.

![Navigatie in werkmappen](media/view-designer-conversion-overview/workbooks-nav.png)

Zodra deze is geselecteerd, wordt er een galerie weer gegeven met alle opgeslagen werkmappen en sjablonen voor uw werk ruimte.

![Galerie met werkmappen](media/view-designer-conversion-overview/workbooks-gallery.png)

Als u een nieuwe werkmap wilt starten, kunt u de **lege** sjabloon onder **snel starten** selecteren of het pictogram **Nieuw** in de bovenste navigatie balk. Als u sjablonen wilt weer geven of als u wilt terugkeren naar opgeslagen werkmappen, selecteert u het item in de galerie of zoekt u naar de naam in de zoek balk.

Als u een werkmap wilt opslaan, moet u het rapport opslaan met een specifieke titel, abonnement, resource groep en locatie.
In de werkmap worden de instellingen van de werk ruimte van de LA automatisch gewijzigd, met hetzelfde abonnement, de resource groep, maar gebruikers kunnen deze rapport instellingen wijzigen. Werkmappen worden standaard opgeslagen in *mijn rapporten*, die alleen toegankelijk zijn voor de afzonderlijke gebruiker. Ze kunnen ook rechtstreeks worden opgeslagen in gedeelde rapporten of later worden gedeeld.

![Werkmappen opslaan](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>Volgende stappen

- [Conversieopties](view-designer-conversion-options.md)