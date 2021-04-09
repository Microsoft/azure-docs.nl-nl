---
title: De duplicaten van rijen opsporen en Null-waarden zoeken met behulp van gegevens stroom fragmenten
description: Meer informatie over het eenvoudig ontdubbelen van rijen en het vinden van Null-waarden met behulp van code fragmenten in gegevens stromen
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: 1b49b2584c4cb462c7c0f520fe8d1b5bf69c8674
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393663"
---
# <a name="dedupe-rows-and-find-nulls-by-using-data-flow-snippets"></a>De duplicaten van rijen opsporen en Null-waarden zoeken met behulp van gegevens stroom fragmenten

[!INCLUDE [appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Door code fragmenten te gebruiken bij het toewijzen van gegevens stromen, kunt u eenvoudig veelvoorkomende taken uitvoeren, zoals gegevensontdubbeling en null-filtering. In dit artikel wordt uitgelegd hoe u deze functies eenvoudig kunt toevoegen aan uw pijp lijnen met behulp van gegevens stroom script fragmenten.
<br>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>Een pijplijn maken

1. Selecteer **nieuwe pijp lijn**.

1. Voeg een gegevens stroom activiteit toe.

1. Selecteer het tabblad **bron instellingen** , voeg een bron transformatie toe en verbind deze vervolgens met een van de gegevens sets.

    ![Scherm afbeelding van het deel venster Bron instellingen voor het toevoegen van een bron type.](media/data-flow/snippet-adf-2.png)

    De controle fragmenten die worden ontdubbeld en NULL gebruiken generieke patronen die gebruikmaken van het gegevens stroom schema drift. De fragmenten werken met elk schema uit uw gegevensset of met gegevens sets die geen vooraf gedefinieerd schema hebben.

1. Kopieer in de sectie ' DISTINCT Row using all columns ' van de [Data flow script (DFS)](./data-flow-script.md#distinct-row-using-all-columns)het code fragment voor distinctrows.

1. [Ga naar de documentatie pagina voor gegevens stroom scripts en kopieer het code fragment voor afzonderlijke rijen.](./data-flow-script.md#distinct-row-using-all-columns)

    ![Scherm opname van een bron fragment.](media/data-flow/snippet-adf-3.png)

1. In uw script, na de definitie voor `source1` , druk op ENTER en plak het code fragment.

1. Gebruik een van de volgende methoden:

   * Verbind dit geplakte code fragment met de bron transformatie die u eerder in de grafiek hebt gemaakt door **source1** vóór de geplakte code te typen.

   * U kunt ook de nieuwe trans formatie in de ontwerp functie verbinden door de inkomende stroom te selecteren in het nieuwe transformatie knooppunt in de grafiek.

     ![Scherm afbeelding van het deel venster instellingen voor voorwaardelijke splitsing.](media/data-flow/snippet-adf-4.png)

   De gegevens stroom verwijdert nu dubbele rijen uit uw bron door gebruik te maken van de geaggregeerde trans formatie, die door alle rijen wordt gegroepeerd met behulp van een algemene hash voor alle kolom waarden.
    
1. Voeg een code fragment toe voor het splitsen van uw gegevens in één stream die rijen met Null-waarden en een andere stroom zonder null-waarden bevat. Hiervoor doet u het volgende:

1. [Ga terug naar de fragment bibliotheek en kopieer de code voor de NULL-controles.](./data-flow-script.md#check-for-nulls-in-all-columns)

   b. Selecteer in de ontwerp functie voor gegevens stromen opnieuw **script** en plak vervolgens deze nieuwe transformatie code onderaan. Met deze actie verbindt u het script met uw vorige trans formatie door de naam van die trans formatie vóór het geplakte fragment te plaatsen.

   De grafiek van uw gegevens stroom moet er nu ongeveer als volgt uitzien:

    ![Scherm afbeelding van de grafiek van de gegevens stroom.](media/data-flow/snippet-adf-1.png)

  U hebt nu een werk stroom met algemene deduping en null-controles gemaakt door bestaande code fragmenten uit de gegevensstroom script bibliotheek te nemen en toe te voegen aan uw bestaande ontwerp.

## <a name="next-steps"></a>Volgende stappen

* Bouw de rest van uw gegevensstroom logica met behulp van trans [formaties](concepts-data-flow-overview.md)voor gegevens stromen.