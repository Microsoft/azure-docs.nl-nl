---
title: Zoek opdrachten opslaan en gegevensassets vastmaken in Azure Data Catalog
description: Procedures voor het markeren van artikelen in Azure Data Catalog voor het opslaan van gegevens bronnen en gegevensassets voor later gebruik.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 52a8d8fcd0b9b9ee1ff918ead250c88b83f86cb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674832"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Zoek opdrachten opslaan en gegevensassets vastmaken in Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introductie
Azure Data Catalog biedt mogelijkheden voor het detecteren van gegevens bronnen. U kunt de catalogus snel doorzoeken en filteren om gegevens bronnen te zoeken en inzicht te krijgen in de beoogde doel einden, zodat u gemakkelijker de juiste gegevens kunt vinden voor de taak bij de hand.

Maar wat als u regel matig moet werken met dezelfde gegevens? En wat gebeurt er als u en andere gebruikers regel matig uw kennis bijdragen aan dezelfde gegevens bronnen in de catalogus? In deze situaties is het mogelijk om dezelfde Zoek opdrachten herhaaldelijk uit te voeren. Dit is waar opgeslagen Zoek opdrachten en vastgemaakte gegevensassets u kunnen helpen.

## <a name="saved-searches"></a>Opgeslagen Zoek opdrachten
Een opgeslagen zoek opdracht in Data Catalog is een herbruikbare, gebruikersspecifieke Zoek definitie. U kunt een zoek opdracht definiëren, inclusief zoek termen, tags en andere filters, en deze vervolgens opslaan. U kunt de opgeslagen Zoek definitie later opnieuw uitvoeren om gegevens assets te retour neren die overeenkomen met de zoek criteria.

### <a name="create-a-saved-search"></a>Een opgeslagen zoek opdracht maken
Als u een opgeslagen zoek opdracht wilt maken, gaat u als volgt te werk:
1. Klik in de Azure Data Catalog Portal in het **huidige Zoek** venster op **Opslaan**. 

    ![Huidige Zoek instellingen koppeling opslaan](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Voer de zoek criteria in die u opnieuw wilt gebruiken en klik vervolgens op **Opslaan**.

    ![Huidige Zoek naam Zoek instellingen opgeslagen](./media/data-catalog-how-to-save-pin/02-name.png)

3. Wanneer u hierom wordt gevraagd, voert u een naam in voor de opgeslagen zoek opdracht. Kies een naam die betekenisvol is en die de gegevensassets beschrijft die worden geretourneerd door de zoek opdracht.

### <a name="manage-saved-searches"></a>Opgeslagen Zoek opdrachten beheren
Nadat u een of meer zoek opdrachten hebt opgeslagen, wordt de optie **opgeslagen Zoek opdrachten** weer gegeven onder het **huidige zoekvak** . Wanneer de lijst is uitgevouwen, worden alle opgeslagen Zoek opdrachten weer gegeven.

 ![Data Catalog-lijst met opgeslagen Zoek opdrachten](./media/data-catalog-how-to-save-pin/03-list.png)

Voer een van de volgende bewerkingen uit:

* Als u een zoek opdracht wilt uitvoeren, selecteert u een opgeslagen zoek opdracht in de lijst.

* Als u een lijst met beheer opties voor een opgeslagen zoek opdracht wilt weer geven, klikt u op de pijl-omlaag naast de zoek naam.

    ![Opties voor het beheren van opgeslagen Zoek opdrachten](./media/data-catalog-how-to-save-pin/04-managing.png)

* Selecteer **naam wijzigen** om een nieuwe naam voor de opgeslagen zoek opdracht op te geven. De zoek definitie is niet gewijzigd.

* Als u de opgeslagen zoek opdracht wilt verwijderen uit de lijst, selecteert u **verwijderen** en bevestigt u de verwijdering.

* Als u de opgeslagen zoek opdracht als standaard zoekopdracht wilt markeren, selecteert u **Opslaan als standaard**. Als u een ' lege ' Zoek opdracht uitvoert vanaf de start pagina van Azure Data Catalog, wordt uw standaard zoekopdracht uitgevoerd. Daarnaast wordt de zoek opdracht die is gemarkeerd als standaard zoek opdracht weer gegeven boven aan de lijst **opgeslagen Zoek opdrachten** .

### <a name="organizational-saved-searches"></a>Opgeslagen Zoek opdrachten in de organisatie
Alle gebruikers in uw organisatie kunnen Zoek opdrachten opslaan voor eigen gebruik. Data Catalog-beheerders kunnen ook Zoek opdrachten opslaan voor alle gebruikers binnen de organisatie. Wanneer beheerders een zoek opdracht opslaan, worden ze weer gegeven met een **share binnen de optie bedrijf** . Als u deze optie selecteert, wordt de opgeslagen zoek opdracht gedeeld voor alle gebruikers in de organisatie.

 ![Data Catalog-opgeslagen Zoek opdrachten in de organisatie](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Vastgemaakte gegevensassets
Met opgeslagen Zoek opdrachten kunt u Zoek definities opslaan en opnieuw gebruiken. De gegevensassets die worden geretourneerd door de Zoek opdrachten, kunnen na verloop van tijd veranderen als de inhoud van de catalogus verandert. Wanneer u gegevensassets vastmaakt, kunt u specifieke gegevensassets expliciet identificeren, zodat ze gemakkelijker toegankelijk zijn zonder dat ze een zoek opdracht hoeven te gebruiken.

Het vastmaken van een gegevens activum is eenvoudig. Als u de gegevensasset wilt toevoegen aan uw vastgemaakte lijst, klikt u gewoon op het **speld** pictogram. Het pictogram wordt weer gegeven in de hoek van de activa tegel in de tegel weergave en in de meest linkse kolom in de lijst weergave in de Azure Data Catalog Portal.

![Data Catalog-het pictogram voor het vastmaken van gegevens elementen](./media/data-catalog-how-to-save-pin/05-pinning.png)

Losmaken is even eenvoudig een gegevensasset. Klik op het pictogram **losmaken** om de instelling voor de geselecteerde Asset te scha kelen.

![Data Catalog-het pictogram voor het losmaken van het gegevens element](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>De sectie mijn assets
De start pagina van de Data Catalog-Portal bevat een sectie **mijn assets** die de activa van belang voor de huidige gebruiker weergeeft. Deze sectie bevat zowel vastgemaakte activa als opgeslagen Zoek opdrachten.

![De sectie mijn assets op de start pagina](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Samenvatting
Azure Data Catalog biedt mogelijkheden waarmee u de gegevens bronnen die u nodig hebt, gemakkelijker kunt detecteren, zodat u en andere leden van de organisatie minder tijd hoeven te best Eden aan het zoeken naar gegevens en er meer tijd aan werkt. Opgeslagen Zoek opdrachten en vastgemaakte gegevensassets zijn gebaseerd op deze kern mogelijkheden, zodat gebruikers eenvoudig gegevens bronnen kunnen identificeren die ze vaak gebruiken.
