---
title: 'Meta gegevens bewerken: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module meta gegevens bewerken in de Azure Machine Learning voor het wijzigen van meta gegevens die zijn gekoppeld aan kolommen in een gegevensset.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/10/2020
ms.openlocfilehash: e279a7c9f6810ece482b043284f0be0719a3dafe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90908043"
---
# <a name="edit-metadata-module"></a>Meta gegevens module bewerken

In dit artikel wordt een module beschreven die is opgenomen in Azure Machine Learning Designer.

Gebruik de module meta gegevens bewerken om meta gegevens te wijzigen die zijn gekoppeld aan kolommen in een gegevensset. De waarde en het gegevens type van de gegevensset worden gewijzigd na gebruik van de module meta gegevens bewerken.

Veelvoorkomende meta gegevens wijzigingen kunnen het volgende omvatten:
  
+ Booleaanse of numerieke kolommen behandelen als categorische waarden.
  
+ Geeft aan welke kolom het **klassen** label bevat of de waarden bevat die u wilt categoriseren of voors pellen.
  
+ Kolommen markeren als onderdelen.
  
+ Datum/tijd-waarden wijzigen in numerieke waarden of andersom.
  
+ De naam van kolommen wijzigen.
  
 Gebruik de meta gegevens bewerken op elk gewenst moment om de definitie van een kolom te wijzigen, meestal om te voldoen aan de vereisten voor een downstream-module. Sommige modules werken bijvoorbeeld alleen met specifieke gegevens typen of vereisen markeringen voor de kolommen, zoals `IsFeature` of `IsCategorical` .  
  
 Nadat u de vereiste bewerking hebt uitgevoerd, kunt u de meta gegevens herstellen naar de oorspronkelijke staat.
  
## <a name="configure-edit-metadata"></a>Meta gegevens bewerken configureren
  
1. Voeg in Azure Machine Learning Designer de module meta gegevens bewerken toe aan uw pijp lijn en verbind de gegevensset die u wilt bijwerken. U kunt de module vinden in de categorie **gegevens transformatie** .
  
1. Klik in het rechterdeel venster van de module op **kolom bewerken** en kies de kolom of de set kolommen waarmee u wilt werken. U kunt kolommen afzonderlijk op naam of index kiezen of u kunt een groep kolommen op type kiezen.  
  
1. Selecteer de optie **gegevens type** als u een ander gegevens type aan de geselecteerde kolommen wilt toewijzen. Mogelijk moet u het gegevens type voor bepaalde bewerkingen wijzigen. Als uw bron-gegevensset bijvoorbeeld getallen heeft verwerkt als tekst, moet u deze wijzigen in een numeriek gegevens type voordat u wiskundige bewerkingen kunt gebruiken.

    + De ondersteunde gegevens typen zijn **teken reeks**, **geheel getal**, **dubbel**, **Booleaans** en **datum/tijd**.

    + Als u meerdere kolommen selecteert, moet u de meta gegevens wijzigingen Toep assen op *alle* geselecteerde kolommen. Stel bijvoorbeeld dat u twee of drie numerieke kolommen kiest. U kunt deze allemaal wijzigen in een teken reeks gegevens type en ze in één bewerking een andere naam geven. U kunt echter niet één kolom wijzigen in een teken reeks gegevens type en een andere kolom van een float-waarde tot een geheel getal.
  
    + Als u geen nieuw gegevens type opgeeft, worden de meta gegevens van de kolom ongewijzigd.

    + Het kolom Type en de waarden worden gewijzigd nadat u de bewerking meta gegevens bewerken hebt uitgevoerd. U kunt het oorspronkelijke gegevens type op elk gewenst moment herstellen met behulp van meta gegevens bewerken om het kolom gegevens type opnieuw in te stellen.  

    > [!NOTE]
    > De **datum notatie** is [gebaseerd op python ingebouwde datum/tijd-indeling](https://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior).  
    > Als u een wille keurig type getal wijzigt in het type **datum/tijd** , laat u het veld **datum in notatie** leeg. Het is momenteel niet mogelijk om de indeling van de doel gegevens op te geven.

1. Selecteer de optie **categorische** om op te geven dat de waarden in de geselecteerde kolommen als categorieën moeten worden behandeld.

    Stel dat u een kolom hebt met de cijfers 0, 1 en 2, maar weet dat de getallen de waarde "rook," "niet-verrookr" en "onbekend" bevatten. In dat geval door de kolom als categorische te markeren, zorgt u ervoor dat de waarden alleen worden gebruikt om gegevens te groeperen en niet in numerieke berekeningen.
  
1. Gebruik de optie **velden** als u de manier wilt wijzigen waarop Azure machine learning de gegevens in een model gebruikt.

    + **Functie**: gebruik deze optie om een kolom te markeren als een functie in modules die alleen op functie Kolommen werken. Standaard worden alle kolommen als onderdelen behandeld.  
  
    + **Label**: gebruik deze optie om het label te markeren, dat ook wel het voorspel bare kenmerk of de doel variabele wordt genoemd. Voor veel modules moet exact één label kolom aanwezig zijn in de gegevensset.

        In veel gevallen kan Azure Machine Learning afleiden dat een kolom een klassen label bevat. Door deze meta gegevens in te stellen, kunt u ervoor zorgen dat de kolom correct wordt geïdentificeerd. Als u deze optie instelt, worden er geen gegevens waarden gewijzigd. Het wijzigt alleen de manier waarop sommige algoritmen voor machine learning de gegevens verwerken.
  
    > [!TIP]
    > Hebt u gegevens die niet in deze categorieën passen? Uw gegevensset kan bijvoorbeeld waarden bevatten zoals unieke id's die niet nuttig zijn als variabelen. Soms kunnen dergelijke Id's problemen veroorzaken bij gebruik in een model.
    >
    > Gelukkig houdt Azure Machine Learning al uw gegevens bij, zodat u deze kolommen niet hoeft te verwijderen uit de gegevensset. Als u bewerkingen wilt uitvoeren op een bepaalde set kolommen, verwijdert u alleen tijdelijk alle andere kolommen met de module [kolommen in gegevensset selecteren](select-columns-in-dataset.md) . Later kunt u de kolommen weer samen voegen met de gegevensset met behulp van de module [kolommen toevoegen](add-columns.md) .  
  
1. Gebruik de volgende opties om eerdere selecties te wissen en meta gegevens te herstellen naar de standaard waarden.  
  
    + **Functie wissen**: gebruik deze optie om de functie vlag te verwijderen.  
  
         Alle kolommen worden in eerste instantie behandeld als functies. Voor modules die wiskundige bewerkingen uitvoeren, moet u mogelijk deze optie gebruiken om te voor komen dat numerieke kolommen als variabelen worden beschouwd.
  
    + **Label wissen**: gebruik deze optie om de meta gegevens van het **Label** te verwijderen uit de opgegeven kolom.  
  
    + **Score wissen**: gebruik deze optie om de meta gegevens van de **Score** te verwijderen uit de opgegeven kolom.  
  
         U kunt een kolom momenteel niet expliciet markeren als een score in Azure Machine Learning. Een aantal bewerkingen resulteert echter in een kolom die intern als score wordt gemarkeerd. Een aangepaste R-module kan ook Score waarden uitvoeren.

1. Voer voor **nieuwe kolom namen** de nieuwe naam van de geselecteerde kolom of kolommen in.  
  
    + Kolom namen kunnen alleen tekens gebruiken die worden ondersteund door UTF-8-code ring. Lege teken reeksen, null-waarden of namen die uitsluitend uit spaties bestaan, zijn niet toegestaan.  
  
    + Als u de naam van meerdere kolommen wilt wijzigen, voert u de namen in als een door komma's gescheiden lijst in volg orde van de kolom indexen.  
  
    + De naam van alle geselecteerde kolommen moet worden gewijzigd. U kunt geen kolommen weglaten of overs Laan.  
  
1. Verzend de pijp lijn.  

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning.
