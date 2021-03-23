---
title: Een data factory in Azure Data Factory kopiëren of klonen
description: Meer informatie over het kopiëren of klonen van een data factory in Azure Data Factory
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: e13aa0a66d1c1a65462e80f14efc048dd2f06c8c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780273"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Een data factory in Azure Data Factory kopiëren of klonen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt beschreven hoe u een data factory kopieert of kloont in Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Cases gebruiken voor het klonen van een data factory

Hier volgen enkele van de omstandigheden waarin het handig is om een data factory te kopiëren of te klonen:

- **Data Factory verplaatsen** naar een nieuwe regio. Als u uw Data Factory naar een andere regio wilt verplaatsen, kunt u het beste een kopie maken in de doel regio en de bestaande verwijderen.

- De **naam van Data Factory wijzigen**. Azure biedt geen ondersteuning voor het hernoemen van resources. Als u de naam van een data factory wilt wijzigen, kunt u de data factory met een andere naam klonen en de bestaande verwijderen.

- **Fout opsporing van wijzigingen** wanneer de functies voor fout opsporing niet voldoende zijn. In de meeste scenario's kunt u [debug](iterative-development-debugging.md)gebruiken. In andere gevallen is het testen van wijzigingen in een gekloonde sandbox-omgeving meer zinvol. De manier waarop de ETL-pijp lijnen met para meters zouden werken wanneer een trigger wordt geactiveerd bij de ontvangst van bestanden en het Tumblingvenstertriggers-tijd venster, kunnen bijvoorbeeld niet eenvoudig worden testable via debug. In dergelijke gevallen kunt u een sandbox-omgeving klonen om te experimenteren. Omdat Azure Data Factory kosten voornamelijk door het aantal uitvoeringen, leidt een tweede Factory niet tot extra kosten.

## <a name="how-to-clone-a-data-factory"></a>Een data factory klonen

1. Als u een vereiste hebt, moet u eerst uw doel-data factory maken vanuit de Azure Portal.

1. Als u zich in de GIT-modus bevindt:
    1. Elke keer dat u vanuit de portal publiceert, wordt de Resource Manager-sjabloon van de fabriek in GIT opgeslagen in de ADF- \_ publicatie vertakking
    1. Verbind de nieuwe fabriek met _dezelfde_ opslag plaats en bouw vanuit de ADF- \_ publicatie vertakking. Resources, zoals pijp lijnen, gegevens sets en triggers, worden door lopen

1. Als u zich in de Live-modus bevindt:
    1. Met Data Factory gebruikers interface kunt u de volledige payload van uw data factory exporteren naar een resource manager-sjabloon bestand en een parameter bestand. Ze kunnen worden geopend via de knop sjabloon van **arm-sjabloon \ exporteren Resource Manager** in de portal.
    1. U kunt de juiste wijzigingen aanbrengen in het parameter bestand en nieuwe waarden wisselen voor de nieuwe Factory
    1. Vervolgens kunt u deze implementeren via de standaard implementatie methoden van Resource Manager-sjablonen.

1. Als u een SelfHosted IntegrationRuntime in uw bron-Factory hebt, moet u deze vooraf maken met dezelfde naam in de doel-Factory. Als u de SelfHosted-Integration Runtime tussen verschillende fabrieken wilt delen, kunt u het patroon dat u [hier](create-shared-self-hosted-integration-runtime-powershell.md) hebt gepubliceerd, gebruiken om SelfHosted IR te delen.

1. Uit veiligheids overwegingen bevat de gegenereerde Resource Manager-sjabloon geen geheime informatie, zoals wacht woorden voor gekoppelde services. Daarom moet u de referenties opgeven als implementatie parameters. Als hand matig in te zetten referentie niet wenselijk is voor uw instellingen, kunt u de verbindings reeksen en wacht woorden van Azure Key Vault in plaats daarvan ophalen. [Meer informatie](store-credentials-in-key-vault.md)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de richt lijnen voor het maken van een data factory in het Azure Portal in [een Data Factory maken met behulp van de Azure Data Factory-gebruikers interface](quickstart-create-data-factory-portal.md).
