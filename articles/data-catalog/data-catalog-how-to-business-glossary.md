---
title: De zakelijke woorden lijst instellen in Azure Data Catalog
description: Instructies voor het markeren van de zakelijke woorden lijst in Azure Data Catalog voor het definiëren en gebruiken van een algemene bedrijfs woordenlijst voor het labelen van geregistreerde gegevensassets.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: f91728435b885f3b4d9415bc81e18fdaaea0148f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021942"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>De zakelijke woorden lijst instellen voor de onderhevige labels

## <a name="introduction"></a>Introductie

Azure Data Catalog maakt detectie van gegevens bronnen mogelijk, zodat u de gegevens bronnen die u nodig hebt om analyses uit te voeren en beslissingen te nemen, gemakkelijk kunt ontdekken en begrijpen. Deze mogelijkheden maken het grootste effect wanneer u het breedste bereik van beschik bare gegevens bronnen kunt vinden en begrijpen.

Een Data Catalog-functie waarmee meer informatie over activa gegevens wordt gelabeld. Door gebruik te maken van labels kunt u tref woorden koppelen aan een Asset of een kolom, waardoor het actief gemakkelijker wordt gedetecteerd via zoeken of bladeren. Tags toevoegen helpt u ook om de context en het doel van de Asset gemakkelijker te begrijpen.

Tagging kan echter soms problemen veroorzaken. Hier volgen enkele voor beelden van problemen die door labels kunnen worden geïntroduceerd:

* Het gebruik van afkortingen op sommige assets en uitgevouwen tekst op andere activa. Deze inconsistentie belemmert de detectie van assets, hoewel de bedoeling was om de activa met dezelfde tag te labelen.
* Mogelijke variaties in betekenis, afhankelijk van de context. Een tag met de naam *omzet* voor een klant gegevensverzameling kan bijvoorbeeld omzet per klant betekenen, maar hetzelfde label voor een driemaandelijkse verkoop gegevensset kan een driemaandelijkse omzet van het bedrijf betekenen.  

Data Catalog bevat een zakelijke woorden lijst om deze en andere vergelijk bare uitdagingen te helpen aanpakken.

Door de Data Catalog zakelijke woorden lijst te gebruiken, kan een organisatie belang rijke zakelijke voor waarden en hun definities voor het maken van een gemeen schappelijke zakelijke woorden lijst documenteren. Dit beheer biedt consistentie in het gegevens gebruik binnen de hele organisatie. Nadat een term is gedefinieerd in de zakelijke woorden lijst, kan deze worden toegewezen aan een gegevensasset in de catalogus. Deze benadering, onderliggend *labelen*, is dezelfde benadering als labelen.

## <a name="glossary-availability-and-privileges"></a>Beschik baarheid en bevoegdheden van de woorden lijst

De zakelijke woorden lijst is alleen beschikbaar in de Standard-editie van Azure Data Catalog. De gratis versie van Data Catalog bevat geen woorden lijst en biedt geen mogelijkheden voor het beheersen van tags.

U kunt de bedrijfs woordenlijst openen via de optie voor de **woorden lijst** in het navigatie menu van de Data Catalog-Portal.  

![Data Catalog: toegang tot de woorden lijst van de onderneming](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Data Catalog beheerders en leden van de rol Administrators van woorden lijst kunnen termen van een woorden lijst maken, bewerken en verwijderen in de zakelijke woorden lijst. Alle Data Catalog gebruikers kunnen de termen definities en label activa weer geven met woordenlijst termen.

![Data Catalog-een nieuwe woordenlijst term toevoegen](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Termen voor een woorden lijst maken

Data Catalog beheerders en verklarende woorden lijst beheerders kunnen termen voor een woorden lijst maken door te klikken op de knop **nieuwe term** . Elke woordenlijst term bevat de volgende velden:

* Een bedrijfs definitie voor de term
* Een beschrijving voor het vastleggen van het beoogde gebruik of bedrijfs regels voor de activa of de kolom
* Een lijst met belanghebbenden die de meeste weten over de term
* De bovenliggende term die de hiërarchie definieert waarin de term is ingedeeld

## <a name="glossary-term-hierarchies"></a>Hiërarchieën voor woordenlijst termen

Door gebruik te maken van de Data Catalog zakelijke woorden lijst, kan een organisatie zijn bedrijfs woordenlijst beschrijven als een hiërarchie van voor waarden en kan er een classificatie worden gemaakt van termen die beter de bedrijfs taxonomie vertegenwoordigen.

Een term moet uniek zijn voor een bepaald niveau van de hiërarchie. Dubbele namen zijn niet toegestaan. Er is geen limiet voor het aantal niveaus in een hiërarchie, maar een hiërarchie is vaak gemakkelijker te begrijpen wanneer er drie niveaus of minder zijn.

Het gebruik van hiërarchieën in de bedrijfs woordenlijst is optioneel. Als u het veld voor de bovenliggende term leeg laat, worden in de woorden lijst een platte (niet-hiërarchische) voor waarden weer geven.  

## <a name="tagging-assets-with-glossary-terms"></a>Activa labelen met woordenlijst termen

Nadat de terminologie van de woorden lijst in de catalogus is gedefinieerd, wordt de ervaring van het labelen van assets geoptimaliseerd om de woorden lijst te doorzoeken als een gebruiker een tag typt. In de Data Catalog-portal wordt een lijst weer gegeven met de overeenkomende termen voor woorden lijsten waaruit u kunt kiezen. Als de gebruiker in de lijst een term voor een woorden lijst selecteert, wordt de term aan de Asset toegevoegd als een tag (ook wel een woordenlijst Label genoemd). De gebruiker kan er ook voor kiezen om een nieuwe tag te maken door een term te typen die niet voor komt in de woorden lijst (ook wel een gebruikers label genoemd).

![Gegevens Asset gelabeld met één gebruikers label en twee woordenlijst Tags](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Gebruikers codes zijn het enige type tag dat wordt ondersteund in de gratis versie van Data Catalog.

### <a name="hover-behavior-on-tags"></a>Gedrag bij aanwijzen op labels

In de Data Catalog Portal worden de twee typen labels visueel onderscheiden en worden verschillende aanwijs gedragen weer gegeven. Wanneer u de muis aanwijzer boven een gebruikers label houdt, ziet u de code tekst en de gebruikers die de tag hebben toegevoegd. Wanneer u de muis aanwijzer boven een woordenlijst Label houdt, ziet u ook de definitie van de woordenlijst term en een koppeling om de zakelijke woorden lijst te openen om de volledige definitie van de term weer te geven.

### <a name="search-filters-for-tags"></a>Zoek filters voor labels

De woordenlijst Tags en gebruikers Tags zijn beide doorzoekbaar en u kunt ze als filters toep assen in een zoek opdracht.

## <a name="summary"></a>Samenvatting

Door gebruik te maken van de zakelijke woorden lijst in Azure Data Catalog en de beheerde code ring, kunt u gegevensassets op een consistente manier identificeren, beheren en detecteren. De zakelijke woorden lijst kan het leren van de bedrijfs woordenlijst door de leden van de organisatie promoten. De verklarende woorden lijst biedt ook ondersteuning voor het vastleggen van zinvolle meta gegevens, waardoor Asset Discovery en inzichten eenvoudiger zijn.

## <a name="next-steps"></a>Volgende stappen

* [REST API documentatie voor zakelijke woordenlijst bewerkingen](/rest/api/datacatalog/data-catalog-glossary)
