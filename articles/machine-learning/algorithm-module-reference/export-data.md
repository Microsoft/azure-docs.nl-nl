---
title: 'Gegevens exporteren: module verwijzing'
titleSuffix: Azure Machine Learning
description: Gebruik de module gegevens exporteren in Azure Machine Learning Designer om resultaten en tussenliggende gegevens buiten Azure Machine Learning op te slaan.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/19/2021
ms.openlocfilehash: 90755aef66fa51084d83d036722187b61449a6fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656903"
---
# <a name="export-data-module"></a>Gegevens module exporteren

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om resultaten, tussenliggende gegevens en werk gegevens van uw pijp lijnen op te slaan in opslag locaties in de Cloud. 

Deze module biedt ondersteuning voor het exporteren van uw gegevens naar de volgende Cloud gegevens Services:

- Azure Blob-container
- Azure-bestandsshare
- Azure Data Lake Storage Gen1
- Azure Data Lake Storage Gen2
- Azure SQL-database

Voordat u uw gegevens exporteert, moet u eerst een gegevens opslag registreren in uw Azure Machine Learning-werk ruimte. Zie [toegang tot gegevens in azure Storage-services](../how-to-access-data.md)voor meer informatie.

## <a name="how-to-configure-export-data"></a>Export gegevens configureren

1. Voeg de module **gegevens exporteren** toe aan uw pijp lijn in de ontwerp functie. U kunt deze module vinden in de categorie **invoer en uitvoer** .

1. Verbind gegevens met het **exporteren** naar de module die de gegevens bevat die u wilt exporteren.

1. Selecteer **gegevens exporteren** om het deel venster **Eigenschappen** te openen.

1. Selecteer een bestaande gegevens opslag in de vervolg keuzelijst voor **gegevens opslag**. U kunt ook een nieuw gegevens archief maken. Raadpleeg de informatie [in azure Storage-services om toegang te krijgen tot de gegevens](../how-to-access-data.md).

    > [!NOTE]
    > Het exporteren van gegevens van een bepaald gegevens type naar een SQL database-kolom die is opgegeven als een ander gegevens type, wordt niet ondersteund. De doel tabel hoeft niet eerst te bestaan.

1. Het selectie vakje, **uitvoer opnieuw genereren**, bepaalt of de module moet worden uitgevoerd om de uitvoer tijdens de uitvoering opnieuw te genereren. 

    Deze optie is standaard niet geselecteerd, wat betekent dat als de module met dezelfde para meters eerder is uitgevoerd, de uitvoer van de laatste uitvoering opnieuw wordt gebruikt om de uitvoerings tijd te verminderen. 

    Als deze is geselecteerd, voert het systeem de module opnieuw uit om de uitvoer opnieuw te genereren.

1. Definieer het pad in het gegevens archief waarin de gegevens zich bevinden. Het pad is een relatief pad. Neem `data/testoutput` als voor beeld, wat betekent dat de invoer gegevens van de **export gegevens** worden geëxporteerd naar `data/testoutput` in de gegevens opslag die u hebt ingesteld in de **uitvoer instellingen** van de module.

    > [!NOTE]
    > De lege paden of **URL-paden** zijn niet toegestaan.


1. Selecteer bij **bestands indeling** de indeling waarin de gegevens moeten worden opgeslagen.
 
1. Verzend de pijp lijn.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
