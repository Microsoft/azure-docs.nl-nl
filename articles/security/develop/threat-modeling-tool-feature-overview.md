---
title: Overzicht van Microsoft Threat Modeling Tool-functies-Azure
description: Meer informatie over alle functies die beschikbaar zijn in de Threat Modeling Tool, zoals de analyse weergave en rapporten.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 08/17/2017
ms.openlocfilehash: 65a88f309602462e77336181316c0d5bf19a8a1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90980681"
---
# <a name="threat-modeling-tool-feature-overview"></a>Overzicht van Threat Modeling Tool-functies

De Threat Modeling Tool kan u helpen bij uw behoeften voor het maken van bedreigingen. Zie aan [de slag met de Threat Modeling tool](threat-modeling-tool-getting-started.md)voor een basis kennis van het hulp programma.

> [!NOTE]
>Het Threat Modeling Tool wordt regel matig bijgewerkt, dus Raadpleeg deze hand leiding vaak om onze nieuwste functies en verbeteringen te bekijken.

Als u een lege pagina wilt openen, selecteert u **een model maken**.

![Lege pagina](./media/threat-modeling-tool-feature-overview/tmtstart.png)

Als u de beschik bare functies in het hulp programma wilt zien, gebruikt u het bedreigings model dat door ons team is gemaakt in het voor beeld [aan de slag](threat-modeling-tool-getting-started.md) .

![Basis risico model](./media/threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Navigatie

Voordat we de ingebouwde functies bespreken, bekijken we de belangrijkste onderdelen die in het hulp programma zijn gevonden.

### <a name="menu-items"></a>Menu-items

De ervaring is vergelijkbaar met andere micro soft-producten. Laten we de menu opdrachten op het hoogste niveau bekijken.

![Menu-items](./media/threat-modeling-tool-feature-overview/menuitems.png)

| Label                               | Details      |
| --------------------------------------- | ------------ |
| **File** | <ul><li>Bestanden openen, opslaan en sluiten</li><li>Meld u aan en meld u af bij de OneDrive-accounts.</li><li>Koppelingen delen (weer geven en bewerken).</li><li>Bestands gegevens weer geven.</li><li>Pas een nieuwe sjabloon toe op bestaande modellen.</li></ul> |
| **Bewerken** | Acties ongedaan maken en opnieuw uitvoeren, evenals kopiëren, plakken en verwijderen. |
| **Weergeven** | <ul><li>Scha kelen tussen **analyse** -en **ontwerp** weergaven.</li><li>Open gesloten vensters (bijvoorbeeld stencils, element eigenschappen en berichten).</li><li>De indeling opnieuw instellen op de standaard instellingen.</li></ul> |
| **Diagram** | Diagrammen toevoegen en verwijderen en door tabs met diagrammen bladeren. |
| **Rapporten** | Maak HTML-rapporten om met anderen te delen. |
| **Help** | Zoek gidsen voor hulp bij het gebruik van het hulp programma. |

De symbolen zijn snelkoppelingen voor de menu's op het hoogste niveau:

| Symbool                               | Details      |
| --------------------------------------- | ------------ |
| **Openen** | Hiermee opent u een nieuw bestand. |
| **Opslaan** | Hiermee slaat u het huidige bestand. |
| **Ontwerp** | Hiermee opent u de **ontwerp** weergave, waar u modellen kunt maken. |
| **Analyseren** | Toont gegenereerde bedreigingen en hun eigenschappen. |
| **Diagram toevoegen** | Hiermee voegt u een nieuw diagram toe (vergelijkbaar met nieuwe tabbladen in Excel). |
| **Diagram verwijderen** | Hiermee verwijdert u het huidige diagram. |
| **Kopiëren/knippen/plakken** | Hiermee kopieert, knipt en plakt u elementen. |
| **Ongedaan maken/opnieuw uitvoeren** | Acties ongedaan maakt en opnieuw uitvoeren. |
| **In-en uitzoomen** | In-en uitzoomen op het diagram voor een betere weer gave. |
| **Feedback** | Hiermee opent u het MSDN-forum. |

### <a name="canvas"></a>Canvas

Het canvas is de ruimte waar u elementen kunt slepen en neerzetten. Slepen en neerzetten is de snelste en meest efficiënte manier om modellen te bouwen. U kunt ook met de rechter muisknop op klikken en items selecteren in het menu om algemene versies van elementen toe te voegen, zoals wordt weer gegeven:

#### <a name="drop-the-stencil-on-the-canvas"></a>Het stencil op het canvas neerzetten

![Canvas verwijderen](./media/threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Het stencil selecteren

![Element eigenschappen](./media/threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Stencils

Op basis van de sjabloon die u selecteert, vindt u alle beschik bare stencils die kunnen worden gebruikt. Als u de juiste elementen niet kunt vinden, gebruikt u een andere sjabloon. U kunt ook een sjabloon aanpassen aan uw behoeften. Over het algemeen kunt u een combi natie van categorieën als volgt vinden:

| Stencil naam                               | Details      |
| --------------------------------------- | ------------ |
| **Proces** | Toepassingen, invoeg toepassingen voor browsers, threads, virtuele machines |
| **Externe interactie** | Verificatie providers, browsers, gebruikers, webtoepassingen |
| **Gegevensarchief** | Cache, opslag, configuratie bestanden, data bases, REGI ster |
| **Gegevens stroom** | Binary, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, named pipe, RPC/DCOM, SMB, UDP |
| **Grens lijn/rand vertrouwen** | Bedrijfs netwerken, Internet, computer, sandbox, gebruikers-en kernelmodus-modus |

### <a name="notesmessages"></a>Opmerkingen/berichten

| Onderdeel                               | Details      |
| --------------------------------------- | ------------ |
| **Berichten** | De logica van interne hulp middelen waarmee gebruikers worden gewaarschuwd wanneer er een fout optreedt, zoals geen gegevens stromen tussen elementen. |
| **Opmerkingen** | Hand matige notities worden tijdens het ontwerp-en controle proces toegevoegd aan het bestand door engineering teams. |

### <a name="element-properties"></a>Element eigenschappen

Element eigenschappen variëren afhankelijk van de elementen die u selecteert. Naast de grenzen van vertrouwens relaties, bevatten alle andere elementen drie algemene selecties:

| Eigenschap element                               | Details      |
| --------------------------------------- | ------------ |
| **Naam** | Dit is handig voor het benoemen van uw processen, winkels, interactoren en stromen, zodat ze eenvoudig kunnen worden herkend. |
| **Buiten bereik** | Als deze is geselecteerd, wordt het element uit de matrix voor het genereren van bedreigingen gehaald (niet aanbevolen). |
| **Reden voor buiten bereik** | Veld motivering om gebruikers te laten weten waarom buiten het bereik is geselecteerd. |

Eigenschappen worden gewijzigd onder elke element categorie. Selecteer elk element om de beschik bare opties te controleren. Of u kunt de sjabloon openen voor meer informatie. Laten we de functies eens bekijken.

## <a name="welcome-screen"></a>Welkomstscherm

Wanneer u de app opent, ziet u het **welkomst** scherm.

### <a name="open-a-model"></a>Een model openen

Beweeg de muis aanwijzer over **Open een model** om twee opties weer te geven: **Open vanaf deze computer** en **Open in OneDrive**. Met de eerste optie opent u het scherm **bestand openen** . Met de tweede optie gaat u door het aanmeldings proces voor OneDrive. Nadat de verificatie is geslaagd, kunt u mappen en bestanden selecteren.

![Model openen](./media/threat-modeling-tool-feature-overview/openmodel.png)

![Openen vanaf computer of OneDrive](./media/threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Feedback, suggesties en problemen

Wanneer u **feedback, suggesties en problemen** selecteert, gaat u naar het MSDN-forum voor sdl-hulpprogram ma's. U kunt lezen wat andere mensen zeggen over het hulp programma, met inbegrip van tijdelijke oplossingen en nieuwe ideeën.

![In de scherm afbeelding ziet u een knop met de tekst feedback, suggesties en problemen.](./media/threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>Ontwerpweergave

Wanneer u een nieuw model opent of maakt, wordt de **ontwerp** weergave geopend.

### <a name="add-elements"></a>Elementen toevoegen

U kunt op twee manieren elementen toevoegen aan het raster:

- **Slepen en neerzetten**: Sleep het gewenste element naar het raster. Gebruik vervolgens de element eigenschappen om aanvullende informatie te geven.
- **Klik** met de rechter muisknop op: Klik met de rechter muisknop ergens in het raster en selecteer items in de vervolg keuzelijst. Een algemene weer gave van het element dat u selecteert, wordt weer gegeven op het scherm.

### <a name="connect-elements"></a>Elementen verbinden

U kunt elementen op twee manieren verbinden:

- **Slepen en neerzetten**: Sleep de gewenste gegevensstroom naar het raster en Verbind beide uiteinden met de juiste elementen.
- **Klik op + SHIFT**: Klik op het eerste element (gegevens verzenden), houd de Shift-toets ingedrukt en selecteer vervolgens het tweede element (gegevens ontvangen). Klik met de rechter muisknop en selecteer **verbinding maken**. Als u een bidirectionele gegevens stroom gebruikt, is de volg orde niet zo belang rijk.

### <a name="properties"></a>Eigenschappen

 Als u de eigenschappen wilt zien die kunnen worden gewijzigd op de stencils, selecteert u het stencil en worden de gegevens dienovereenkomstig ingevuld. In het volgende voor beeld ziet u vóór en nadat een **database** stencil naar het diagram is gesleept:

#### <a name="before"></a>Voor

![Voor](./media/threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Na

![Na](./media/threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Berichten

Als u een bedreigings model maakt en verg eten bent om gegevens stromen naar elementen te koppelen, krijgt u een melding. U kunt het bericht negeren of u kunt de instructies volgen om het probleem op te lossen. 

![Scherm afbeelding toont een bedreigings model connector dat niet is verbonden met elementen, met het bericht dat dit probleem veroorzaakt.](./media/threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Notities

Als u notities wilt toevoegen aan uw diagram, gaat u naar het tabblad **notities** van het tabblad **berichten** .

## <a name="analysis-view"></a>Analyse weergave

Nadat u het diagram hebt gemaakt, selecteert u het **analyse** symbool (het vergroot glas) op de werk balk snelkoppelingen om over te scha kelen naar de **analyse** weergave.

![Analyse weergave](./media/threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Gegenereerde bedreigings selectie

Wanneer u een bedreiging selecteert, kunt u drie afzonderlijke functies gebruiken:

| Functie                               | Informatie      |
| --------------------------------------- | ------------ |
| **Indicator lezen** | <p>De dreiging is gemarkeerd als gelezen. Hiermee kunt u bijhouden welke items u hebt bekeken.</p><p>![Indicator voor lezen/lezen](./media/threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Interactie focus** | <p>De interactie in het diagram dat bij een bedreiging hoort, is gemarkeerd.</p><p>![Interactie focus](./media/threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Bedreigingseigenschappen** | <p>Meer informatie over de bedreiging wordt weer gegeven in het venster **bedreigings eigenschappen** .</p><p>![Bedreigingseigenschappen](./media/threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Prioriteit wijzigen

U kunt het prioriteits niveau van elke gegenereerde bedreiging wijzigen. Met verschillende kleuren kunt u eenvoudig bedreigingen met hoge, middel grote en lage prioriteit identificeren.

![Prioriteit wijzigen](./media/threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Bewerk bare velden van bedreigings eigenschappen

Zoals in de voor gaande afbeelding wordt weer gegeven, kunt u de gegevens wijzigen die door het hulp programma worden gegenereerd. U kunt ook informatie toevoegen aan bepaalde velden, zoals uitvulling. Deze velden worden gegenereerd door de sjabloon. Als u meer informatie voor elke dreiging nodig hebt, kunt u wijzigingen aanbrengen.

![Bedreigingseigenschappen](./media/threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Rapporten

Nadat u de prioriteiten hebt gewijzigd en de status van elke gegenereerde bedreiging hebt bijgewerkt, kunt u het bestand opslaan en/of een rapport afdrukken. Ga naar **rapport**  >  **volledig rapport maken**. Geef het rapport een naam en ziet er ongeveer als volgt uit:

![Scherm afbeelding toont een voor beeld van een bedreigings model rapport, met inbegrip van een samen vatting, diagrammen en andere informatie.](./media/threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Volgende stappen

- Stuur uw vragen, opmerkingen en problemen naar tmtextsupport@microsoft.com . **[Down load](https://aka.ms/threatmodelingtool)** de Threat Modeling tool om aan de slag te gaan.
- Ga naar onze [github](https://github.com/Microsoft/threat-modeling-templates) -pagina om een sjabloon voor de community bij te dragen.