---
title: Gegevens importeren in de ontwerp functie
titleSuffix: Azure Machine Learning
description: Meer informatie over het importeren van gegevens in Azure Machine Learning Designer met behulp van Azure Machine Learning gegevens sets en de module gegevens importeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: a2cc0840b7ba4b26cf9f5b1219fc189230870774
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97739855"
---
# <a name="import-data-into-azure-machine-learning-designer"></a>Gegevens importeren in Azure Machine Learning Designer

In dit artikel leert u hoe u uw eigen gegevens in de ontwerp functie kunt importeren om aangepaste oplossingen te maken. Er zijn twee manieren waarop u gegevens kunt importeren in de ontwerp functie: 

* **Azure machine learning gegevens sets** : [gegevens sets](concept-data.md#datasets) registreren in azure machine learning om geavanceerde functies in te scha kelen die u helpen bij het beheren van uw gegevens.
* **Gegevens module importeren** : gebruik de module [gegevens importeren](algorithm-module-reference/import-data.md) om rechtstreeks toegang te krijgen tot gegevens uit online gegevens bronnen.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-azure-machine-learning-datasets"></a>Azure Machine Learning gegevens sets gebruiken

U wordt aangeraden gegevens [sets](concept-data.md#datasets) te gebruiken voor het importeren in de ontwerp functie. Wanneer u een gegevensset registreert, kunt u profiteren van geavanceerde gegevens functies, zoals [versie beheer en bijhouden](how-to-version-track-datasets.md) van [gegevens](how-to-monitor-datasets.md).

### <a name="register-a-dataset"></a>Een gegevensset registreren

U kunt bestaande gegevens sets [programmatisch registreren met de SDK](how-to-create-register-datasets.md#datasets-sdk) of [visueel in azure machine learning Studio](how-to-connect-data-ui.md#create-datasets).

U kunt de uitvoer voor elke ontwerp module ook registreren als een gegevensset.

1. Selecteer de module die de gegevens levert die u wilt registreren.

1. Selecteer in het deel venster Eigenschappen de optie **uitvoer en logboek**  >  **registratie gegevensset registreren**.

    ![Scherm afbeelding die laat zien hoe u kunt navigeren naar de optie gegevensset registreren](media/how-to-designer-import-data/register-dataset-designer.png)

Als de gegevens van de module-uitvoer een tabel indeling hebben, moet u ervoor kiezen om de uitvoer als een **Bestands gegevensset** of een **tabellaire gegevensset** te registreren.

 - **File dataset** registreert de uitvoermap van de module als een bestands gegevensset. De uitvoermap bevat een gegevens bestand en meta bestanden die door de ontwerp functie intern worden gebruikt. Selecteer deze optie als u de geregistreerde gegevensset in de ontwerp functie wilt blijven gebruiken. 

 - **In tabellaire gegevensset** wordt alleen het uitvoer gegevensbestand van de module geregistreerd als een gegevensset in tabel vorm. Deze indeling kan eenvoudig worden gebruikt door andere hulpprogram ma's, bijvoorbeeld in geautomatiseerde Machine Learning of de python-SDK. Selecteer deze optie als u van plan bent de geregistreerde gegevensset buiten de ontwerp functie te gebruiken.  
 

### <a name="use-a-dataset"></a>Een gegevensset gebruiken

U kunt de geregistreerde gegevens sets vinden in het module palet, onder **gegevens sets**. Als u een gegevensset wilt gebruiken, sleept u deze naar het pijp lijn-canvas. Verbind vervolgens de uitvoer poort van de gegevensset met andere modules op het canvas. 

Als u een bestands gegevensset registreert, is het uitvoer poort type van de gegevensset **AnyDirectory**. Als u een gegevensset in tabel vorm registreert, wordt het type uitvoer poort van de gegevensset als **DataFrameDirectory**. Houd er rekening mee dat als u de uitvoer poort van de gegevensset verbindt met andere modules in de ontwerp functie, het poort type van gegevens sets en modules moet worden uitgelijnd.

![Scherm opname van de locatie van opgeslagen gegevens sets in het palet voor ontwerpen](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> De ontwerp functie ondersteunt het [versie beheer](how-to-version-track-datasets.md)van de gegevensset. Geef de gegevensset-versie op in het eigenschappen paneel van de module gegevensset.

### <a name="limitations"></a>Beperkingen 

- Momenteel kunt u tabellaire gegevensset alleen visualiseren in de ontwerp functie. Als u een bestands gegevensset buiten Designer registreert, kunt u deze niet visualiseren op het canvas van de ontwerp functie.
- Uw gegevensset wordt opgeslagen in het virtuele netwerk (VNet). Als u wilt visualiseren, moet u de werk ruimte beheerde identiteit van het gegevens archief inschakelen.
    1. Ga naar de gerelateerde gegevens opslag en **Klik op referenties bijwerken** 
     :::image type="content" source="./media/resource-known-issues/datastore-update-credential.png" alt-text="gegevens bijwerken":::
    1. Selecteer **Ja** om de beheerde identiteit van de werk ruimte in te scha kelen.
    :::image type="content" source="./media/resource-known-issues/enable-workspace-managed-identity.png" alt-text="Beheerde identiteit van werk ruimte inschakelen":::

## <a name="import-data-using-the-import-data-module"></a>Gegevens importeren met behulp van de module gegevens importeren

We raden u aan om gegevens sets te gebruiken voor het importeren van informatie, maar u kunt ook de module [gegevens importeren](algorithm-module-reference/import-data.md) gebruiken. De module gegevens importeren slaat de registratie van uw gegevensset in Azure Machine Learning over en importeert gegevens rechtstreeks vanuit een [Data Store](concept-data.md#datastores) -of http-URL.

Zie de [referentie pagina gegevens importeren](algorithm-module-reference/import-data.md)voor gedetailleerde informatie over het gebruik van de module gegevens importeren.

> [!NOTE]
> Als uw gegevensset te veel kolommen bevat, kan de volgende fout optreden: "validatie mislukt vanwege grootte beperking". Als u dit wilt voor komen, [registreert u de gegevensset in de gegevens sets-interface](how-to-connect-data-ui.md#create-datasets).

## <a name="supported-sources"></a>Ondersteunde bronnen

In deze sectie vindt u de gegevens bronnen die worden ondersteund door de ontwerper. Gegevens worden in de ontwerp functie geleverd vanuit een gegevens opslag of een [gegevensset in tabel vorm](how-to-create-register-datasets.md#dataset-types).

### <a name="datastore-sources"></a>Data Store-bronnen
Zie [toegang tot gegevens in azure Storage-services](how-to-access-data.md#supported-data-storage-service-types)voor een lijst met ondersteunde gegevens opslag bronnen.

### <a name="tabular-dataset-sources"></a>Bron tabel van gegevensset

De ontwerp functie ondersteunt tabellaire gegevens sets die zijn gemaakt op basis van de volgende bronnen:
 * Bestanden met scheidings tekens
 * JSON-bestanden
 * Parquet-bestanden
 * SQL-query's

## <a name="data-types"></a>Gegevenstypen

De Designer herkent intern de volgende gegevens typen:

* Tekenreeks
* Geheel getal
* Decimaal
* Booleaans
* Date

De ontwerp functie maakt gebruik van een intern gegevens type om gegevens tussen modules door te geven. U kunt uw gegevens in de indeling van de gegevens tabel expliciet converteren met behulp van de module [converteren naar dataset](algorithm-module-reference/convert-to-dataset.md) . Alle modules die andere indelingen dan de interne indeling accepteren, worden de gegevens op de achtergrond geconverteerd voordat deze aan de volgende module worden door gegeven.

## <a name="data-constraints"></a>Gegevens beperkingen

Modules in de ontwerp functie worden beperkt door de grootte van het berekenings doel. Voor grotere gegevens sets moet u een grotere Azure Machine Learning Reken Resource gebruiken. Zie [Wat zijn Compute-doelen in azure machine learning?](concept-compute-target.md#azure-machine-learning-compute-managed) voor meer informatie over Azure machine learning compute.

## <a name="access-data-in-a-virtual-network"></a>Toegang tot gegevens in een virtueel netwerk

Als uw werk ruimte zich in een virtueel netwerk bevindt, moet u aanvullende configuratie stappen uitvoeren om gegevens in de ontwerp functie te visualiseren. Zie [Azure machine learning Studio gebruiken in een virtueel](how-to-enable-studio-virtual-network.md)netwerk van Azure voor meer informatie over het gebruik van data stores en gegevens sets in een virtueel netwerk.

## <a name="next-steps"></a>Volgende stappen

Leer de basis principes van Designer met deze [zelf studie: prijs van auto Mobile voor speld met de ontwerp functie](tutorial-designer-automobile-price-train-score.md).
