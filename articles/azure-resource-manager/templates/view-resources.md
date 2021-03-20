---
title: Eigenschappen van de bron ontdekken
description: Hierin wordt beschreven hoe u naar bron eigenschappen zoekt.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: c8bbe0dcb1bc9dc9751a1dc0d0b98a6368473546
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91327745"
---
# <a name="discover-resource-properties"></a>Eigenschappen van de bron ontdekken

Voordat u Resource Manager-sjablonen maakt, moet u weten welke resource typen er beschikbaar zijn en welke waarden u moet gebruiken in uw sjabloon. In dit artikel ziet u enkele manieren waarop u de eigenschappen kunt vinden die u wilt toevoegen aan uw sjabloon.

## <a name="find-resource-provider-namespaces"></a>Naam ruimten van resource provider zoeken

Resources in een ARM-sjabloon worden gedefinieerd met de naam ruimte en het resource type van de resource provider. Bijvoorbeeld: micro soft. Storage/Storage accounts is de volledige naam van het bron type van het opslag account. Micro soft. Storage is de naam ruimte. Zie [resource providers voor Azure-Services](../management/azure-services-resource-providers.md)als u de naam ruimten voor de resource typen die u wilt gebruiken nog niet kent.

![Toewijzing van naam ruimte Resource Manager-resource provider](./media/view-resources/resource-provider-namespace-and-azure-service-mapping.png)

## <a name="export-templates"></a>Sjablonen exporteren

De eenvoudigste manier om de sjabloon eigenschappen voor uw bestaande resources op te halen is het exporteren van de sjabloon. Zie voor meer informatie [het exporteren van één en meerdere resources naar een sjabloon in de Azure Portal](./export-template-portal.md).

## <a name="use-resource-manager-tools-extension"></a>Resource Manager-hulpprogram ma's uitbrei ding gebruiken

Met Visual Studio code en de uitbrei ding Azure Resource Manager tools kunt u precies zien welke eigenschappen er nodig zijn voor elk resource type. Ze bieden IntelliSense en fragmenten die vereenvoudigen hoe u een resource in uw sjabloon definieert. Zie Quick Start (Engelstalig) voor meer informatie over het [maken van Azure Resource Manager sjablonen met Visual Studio code](./quickstart-create-templates-use-visual-studio-code.md#add-an-azure-resource).

Op de volgende scherm afbeelding ziet u een opslag account resource wordt toegevoegd aan een sjabloon:

![Extensie fragmenten voor Resource Manager-hulpprogram ma's](./media/view-resources/resource-manager-tools-extension-snippets.png)

De uitbrei ding biedt ook een lijst met opties voor de configuratie-eigenschappen.

![Hulpprogram ma's voor Resource Manager-extensie Configureer bare waarden](./media/view-resources/resource-manager-tools-extension-configurable-properties.png)

## <a name="use-template-reference"></a>Sjabloonverwijzing gebruiken

De verwijzing naar de Azure Resource Manager-sjabloon is de meest uitgebreide bron voor sjabloon schema. U kunt API-versies, sjabloon indeling en eigenschaps informatie vinden.

1. Blader naar [Azure Resource Manager sjabloon verwijzing](/azure/templates/).
1. Selecteer in de linkernavigatiebalk **opslag** en selecteer vervolgens **alle resources**. Op de pagina alle resources vindt u een overzicht van de resource typen en de versies.

    ![Bron versies van de sjabloon verwijzing](./media/view-resources/resource-manager-template-reference-resource-versions.png)

    Als u het resource type weet, kunt u rechtstreeks naar deze pagina gaan met de volgende URL-indeling: `https://docs.microsoft.com/azure/templates/{provider-namespace}/{resource-type}` .

1. Selecteer de nieuwste versie. Het is raadzaam om de nieuwste API-versie te gebruiken.

    De sectie **sjabloon indeling** bevat een lijst met alle eigenschappen voor het opslag account. **SKU** bevindt zich in de lijst:

    ![indeling van sjabloon verwijzing opslag account](./media/view-resources/resource-manager-template-reference-storage-account-sku.png)

    Schuif omlaag om het **SKU-object** te bekijken in de sectie **eigenschaps waarden** . In het artikel worden de toegestane waarden voor de SKU-naam weer gegeven:

    ![SKU-waarden voor referentie opslag account voor sjabloon](./media/view-resources/resource-manager-template-reference-storage-account-sku-values.png)

    Aan het einde van de pagina bevat de sectie **Quick Start templates** enkele van de Azure Quick Start-sjablonen die het resource type bevatten:

    ![sjabloon verwijzing opslag account Quick Start sjablonen](./media/view-resources/resource-manager-template-reference-quickstart-templates.png)

De sjabloon verwijzing is gekoppeld aan elk van de Azure service-documentatie sites.  Bijvoorbeeld de Key Vault- [Documentatie site](../../key-vault/general/overview.md):

![Naslag informatie over Resource Manager-sjabloon Key Vault](./media/view-resources/resource-manager-template-reference-key-vault.png)

## <a name="use-resource-explorer"></a>Resource Explorer gebruiken

Resource Explorer is inge sloten in de Azure Portal. Voordat u deze methode gebruikt, hebt u een opslag account nodig. Als u er nog geen hebt, selecteert u de volgende knop om er een te maken:

[![Implementeren in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Voer in het zoekvak **resource Explorer** in en selecteer vervolgens **resource Explorer**.

    ![Scherm afbeelding toont het zoeken naar resource Explorer in de Azure Portal.](./media/view-resources/azure-portal-resource-explorer.png)

1. Vouw aan de linkerkant **abonnementen** uit en vouw vervolgens uw Azure-abonnement uit. U kunt het opslag account vinden onder **providers** of **ResourceGroups**.

    ![Azure Portal resource Verkenner](./media/view-resources/azure-portal-resource-explorer-home.png)

    - **Providers**: Vouw **providers**  ->  **micro soft. Storage**  ->  **Storage accounts** uit en selecteer vervolgens uw opslag account.
    - **ResourceGroups**: Selecteer de resource groep die het opslag account bevat, selecteer **resources** en selecteer vervolgens het opslag account.

    Aan de rechter kant ziet u de SKU-configuratie voor het bestaande opslag account, vergelijkbaar met:

    ![SKU van Azure Portal resource Explorer-opslag account](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="use-resourcesazurecom"></a>Resources.azure.com gebruiken

Resources.azure.com is een open bare website die door iedereen met een Azure-abonnement kan worden geopend. Deze is beschikbaar als preview-versie.  U kunt in plaats daarvan [resource Explorer](#use-resource-explorer) gebruiken. Dit hulp programma biedt de volgende functies:

- Ontdek de Azure resource management-Api's.
- Down load API-documentatie en schema-informatie.
- Maak API-aanroepen rechtstreeks in uw eigen abonnementen.

U hebt een opslag account nodig om te laten zien hoe u schema-informatie kunt ophalen met behulp van dit hulp programma. Als u er nog geen hebt, selecteert u de volgende knop om er een te maken:

[![Implementeren in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. Blader naar [resources.Azure.com](https://resources.azure.com/). Het duurt enkele minuten voordat het hulp programma het linkerdeel venster populair heeft.
1. Selecteer **abonnementen**.

    ![resource.azure.com API-toewijzing](./media/view-resources/resources-azure-com-api-mapping.png)

    Het knoop punt aan de linkerkant komt overeen met de API-aanroep aan de rechter kant. U kunt de API-aanroep maken door de knop **ophalen** te selecteren.
1. Vouw aan de linkerkant **abonnementen** uit en vouw vervolgens uw Azure-abonnement uit. U kunt het opslag account vinden onder **providers** of **ResourceGroups**.

    - **Providers**: Vouw **providers**  ->  **micro soft. Storage**  ->  **Storage accounts** uit en blader vervolgens naar het opslag account.
    - **ResourceGroups**: Selecteer de resource groep die het opslag account bevat en selecteer vervolgens **resources**.

    Aan de rechter kant ziet u de SKU-configuratie voor het bestaande opslag account, vergelijkbaar met:

    ![SKU van Azure Portal resource Explorer-opslag account](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u sjabloon schema gegevens kunt vinden. Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](./template-syntax.md)voor meer informatie over het maken van Resource Manager-sjablonen.
