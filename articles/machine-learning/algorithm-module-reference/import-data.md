---
title: 'Gegevens importeren: module verwijzing'
titleSuffix: Azure Machine Learning
description: Leer hoe u de module gegevens importeren in Azure Machine Learning kunt gebruiken om gegevens in een machine learning pijp lijn te laden vanuit bestaande Cloud Data Services.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 69d27c102ca059974da87224e44f0ad7aa103fff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94592631"
---
# <a name="import-data-module"></a>Gegevens module importeren

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om gegevens te laden in een machine learning pijp lijn vanuit bestaande Cloud Data Services. 

> [!Note]
> Alle functionaliteit van deze module kan worden uitgevoerd door **gegevens opslag** en **gegevens sets** op de werk ruimte-landings pagina. U wordt aangeraden om data **Store** en **DataSet** te gebruiken. Dit omvat aanvullende functies zoals gegevens bewaking. Zie voor meer informatie [hoe u toegang krijgt tot gegevens](../how-to-access-data.md) en het artikel gegevens [sets registreren](../how-to-create-register-datasets.md) .
> Nadat u een gegevensset hebt geregistreerd, kunt u deze vinden in de categorie **gegevens** sets  ->  **mijn gegevens sets** in de ontwerp interface. Deze module is gereserveerd voor Studio-gebruikers (klassiek) voor een vertrouwde ervaring. 
>

De module **gegevens importeren** ondersteunt het lezen van gegevens uit de volgende bronnen:

- URL via HTTP
- Azure-Cloud opslag via [**data stores**](../how-to-access-data.md))
    - Azure Blob-container
    - Azure-bestandsshare
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Azure SQL Database
    - Azure-PostgreSQL    

Voordat u Cloud opslag gebruikt, moet u eerst een gegevens opslag registreren in uw Azure Machine Learning-werk ruimte. Zie [toegang tot gegevens](../how-to-access-data.md)voor meer informatie. 

Nadat u de gewenste gegevens hebt gedefinieerd en verbinding wilt maken met de bron, wordt het gegevens type van elke kolom afgeleid op basis van de waarden die erin worden **[weer gegeven en](./import-data.md)** worden de gegevens in uw ontwerp pijplijn geladen. De uitvoer van **import gegevens** is een gegevensset die kan worden gebruikt met een wille keurige ontwerp pijplijn.

Als de bron gegevens worden gewijzigd, kunt u de gegevensset vernieuwen en nieuwe gegevens toevoegen door [import gegevens](./import-data.md)opnieuw uit te voeren.

> [!WARNING]
> Als uw werk ruimte zich in een virtueel netwerk bevindt, moet u uw gegevens opslag configureren voor het gebruik van de functies van de visualisatie van de ontwerp functie. Zie [Azure machine learning Studio gebruiken in een virtueel](../how-to-enable-studio-virtual-network.md)netwerk van Azure voor meer informatie over het gebruik van data stores en gegevens sets in een virtueel netwerk.


## <a name="how-to-configure-import-data"></a>Import gegevens configureren

1. Voeg de module **gegevens importeren** toe aan de pijp lijn. U kunt deze module vinden in de categorie **gegevens invoer en uitvoer** in de ontwerp functie.

1. Selecteer de module om het rechterdeel venster te openen.

1. Selecteer **gegevens bron** en kies het type gegevens bron. Dit kan HTTP of gegevens opslag zijn.

    Als u gegevens opslag kiest, kunt u bestaande gegevens opslag selecteren die al in uw Azure Machine Learning-werk ruimte is geregistreerd of een nieuw gegevens archief maken. Definieer vervolgens het pad van de gegevens die moeten worden geïmporteerd in het gegevens archief. U kunt eenvoudig door het pad bladeren door op scherm afbeelding **Bladeren** te klikken. ![ hier ziet u de koppeling Browse pad waarmee het dialoog venster Padselectie wordt geopend.](media/module/import-data-path.png)

    > [!NOTE]
    > De module **gegevens importeren** is alleen voor gegevens **in tabel vorm** .
    > Als u meerdere tabellaire gegevens bestanden één keer wilt importeren, hebt u de volgende voor waarden nodig, anders worden er fouten weer gegeven:
    > 1. Als u alle gegevens bestanden in de map wilt toevoegen, moet u `folder_name/**` voor het **pad** invoeren.
    > 2. Alle gegevens bestanden moeten worden gecodeerd in Unicode-8.
    > 3. Alle gegevens bestanden moeten dezelfde kolom nummers en kolom namen hebben.
    > 4. Het resultaat van het importeren van meerdere gegevens bestanden is het samen voegen van alle rijen uit meerdere bestanden in de juiste volg orde.

1. Selecteer het voorbeeld schema voor het filteren van de kolommen die u wilt toevoegen. U kunt ook geavanceerde instellingen definiëren als scheidings teken bij het parseren van opties.

    ![import-data-preview](media/module/import-data.png)

1. Het selectie vakje, **uitvoer opnieuw genereren**, bepaalt of de module moet worden uitgevoerd om de uitvoer tijdens de uitvoering opnieuw te genereren. 

    Deze optie is standaard niet geselecteerd, wat betekent dat als de module met dezelfde para meters eerder is uitgevoerd, de uitvoer van de laatste uitvoering opnieuw wordt gebruikt om de uitvoerings tijd te verminderen. 

    Als deze is geselecteerd, voert het systeem de module opnieuw uit om de uitvoer opnieuw te genereren. Selecteer deze optie daarom wanneer onderliggende gegevens in de opslag worden bijgewerkt, zodat u de meest recente gegevens kunt ophalen.


1. Verzend de pijp lijn.

    Wanneer gegevens importeren de gegevens in de ontwerp functie laadt, wordt het gegevens type van elke kolom afgeleid op basis van de waarden die het bevat, ofwel numeriek of categorische.

    Als er een header aanwezig is, wordt de header gebruikt om de kolommen van de uitvoer gegevensset te benoemen.

    Als er geen bestaande kolom koppen in de gegevens staan, worden nieuwe kolom namen gegenereerd met de indeling Kol1, col2,... , coln*.

## <a name="results"></a>Resultaten

Wanneer het importeren is voltooid, klikt u met de rechter muisknop op de uitvoer gegevensset en selecteert u **visualiseren** om te zien of de gegevens zijn geïmporteerd.

Als u de gegevens wilt opslaan voor hergebruik in plaats van een nieuwe set gegevens te importeren telkens wanneer de pijp lijn wordt uitgevoerd, selecteert u het pictogram **gegevensset registreren** onder het tabblad **uitvoer en logboeken** in het rechterdeel venster van de module. Kies een naam voor de gegevensset. De opgeslagen gegevensset behoudt de gegevens op het moment van opslaan, de gegevensset wordt niet bijgewerkt wanneer de pijp lijn opnieuw wordt uitgevoerd, zelfs als de gegevensset in de pijp lijn wordt gewijzigd. Dit kan handig zijn voor het maken van moment opnamen van gegevens.

Na het importeren van de gegevens zijn mogelijk extra voor bereid voor het maken en analyseren van modellen nodig:

- Gebruik [meta gegevens bewerken](./edit-metadata.md) om kolom namen te wijzigen, om een kolom te verwerken als een ander gegevens type, of om aan te geven dat sommige kolommen labels of functies zijn.

- Gebruik [select columns in dataset](./select-columns-in-dataset.md) om een subset van kolommen te selecteren die u wilt transformeren of gebruiken in model lering. De getransformeerde of verwijderde kolommen kunnen eenvoudig opnieuw worden samengevoegd met de oorspronkelijke gegevensset met behulp van de module [add columns](./add-columns.md) .  

- Gebruik [partitie en voor beeld](./partition-and-sample.md) om de gegevensset te verdelen, steek proeven uit te voeren of de bovenste n rijen te verkrijgen.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
