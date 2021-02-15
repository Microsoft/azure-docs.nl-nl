---
title: Notitie blokken in het Azure Cosmos DB-account inschakelen (preview)
description: Met de ingebouwde notitie blokken van Azure Cosmos DB kunt u uw gegevens in de portal analyseren en visualiseren. In dit artikel wordt beschreven hoe u deze functie inschakelt voor Cosmos-accounts.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/09/2021
ms.author: dech
ms.custom: references_regions
ms.openlocfilehash: b8ce745a0a0db96e7f6b9e3a0140d46364246e4a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379604"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Notebooks inschakelen voor Azure Cosmos DB accounts (preview-versie)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Ingebouwde notitie blokken voor Azure Cosmos DB zijn momenteel beschikbaar in [29 regio's](#supported-regions). Als u notitie blokken wilt gebruiken, [maakt u een nieuw Cosmos-account](#create-a-new-cosmos-account) of [schakelt u notitie blokken in voor een bestaand account](#enable-notebooks-in-an-existing-cosmos-account) in een van deze regio's. 

Met ingebouwde Jupyter-notebooks in Azure Cosmos DB kunt u uw gegevens van de Azure Portal analyseren en visualiseren. In dit artikel wordt beschreven hoe u deze functie voor uw Azure Cosmos DB-account kunt inschakelen.

## <a name="create-a-new-cosmos-account"></a>Een nieuw Cosmos-account maken
Vanaf 10 februari 2021 worden voor nieuwe Azure Cosmos-accounts die zijn gemaakt in een van de [ondersteunde regio's](#supported-regions) automatisch notitie blokken ingeschakeld. Er is geen aanvullende configuratie nodig om notebooks in te scha kelen. Gebruik de volgende instructies om een nieuw account te maken:
1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
1. Selecteer **Een resource maken** > **Databases** > **Azure Cosmos DB**.
1. Voer de basis instellingen voor het account in. 
 
   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="De pagina Nieuw account voor Azure Cosmos DB":::

1. Selecteer **Controleren + maken**. U kunt de optie **netwerk** en **Tags** overs Laan. 
1. Controleer de accountinstellingen en selecteer vervolgens **Maken**. Het duurt een paar minuten om het account te maken. Wacht tot de portal-pagina **Uw implementatie is voltooid** weergeeft. 

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="Het deelvenster Meldingen in Azure Portal":::

1. Selecteer **Ga naar resource** om naar de Azure Cosmos DB-accountpagina te gaan.

   :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="De Azure Cosmos DB-accountpagina":::

1. Ga naar het deel venster **Data Explorer** . Nu ziet u de werk ruimte notitie blokken.

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="Nieuwe Azure Cosmos DB notitie blokken-werk ruimte":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Notebooks inschakelen in een bestaand Cosmos-account

U kunt ook notebooks inschakelen op bestaande accounts. Deze stap hoeft slechts één keer per account te worden uitgevoerd.

1. Ga naar het **Data Explorer** deel venster in uw Cosmos-account.
1. Selecteer **notebooks inschakelen**.

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="Een nieuwe werk ruimte voor notitie blokken maken in Data Explorer":::

1. Hiermee wordt u gevraagd om een nieuwe werk ruimte voor notitie blokken te maken. Selecteer **volledige installatie.**
1. Uw account is nu ingeschakeld voor het gebruik van notitie blokken.

## <a name="create-and-run-your-first-notebook"></a>Uw eerste notitie blok maken en uitvoeren

Als u wilt controleren of u notebooks kunt gebruiken, selecteert u een van de notitie blokken onder voorbeeld notitieblokken. Hiermee wordt een kopie van het notitie blok opgeslagen in uw werk ruimte en geopend.

In dit voor beeld gebruiken we **GettingStarted. ipynb**. 

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="GettingStarted. ipynb-notebook weer geven":::

Als u het notitie blok wilt uitvoeren:
1. Selecteer de eerste code cel die python-code bevat. 
1. Selecteer **uitvoeren** om de cel uit te voeren. U kunt ook **SHIFT + ENTER** gebruiken om de cel uit te voeren.
1. Vernieuw het resource venster om de data base en de container te zien die zijn gemaakt.

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="Aan de slag-notebook uitvoeren":::

U kunt ook **Nieuw notitie blok** selecteren om een nieuw notitie blok te maken of een bestaand notebook bestand (. ipynb) te uploaden door **Upload File** te selecteren in het menu **mijn notitie blokken** . 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="Een nieuw notitie blok maken of uploaden":::

## <a name="supported-regions"></a>Ondersteunde regio’s
Ingebouwde notitie blokken voor Azure Cosmos DB zijn momenteel beschikbaar in 29 Azure-regio's. Voor nieuwe Azure Cosmos-accounts die zijn gemaakt in deze regio's, worden automatisch notitie blokken ingeschakeld. Er zijn gratis notitie blokken met uw account. 

- Australië - centraal
- Australië - centraal 2
- Australië - oost
- Australië - zuidoost
- Brazilië - zuid
- Canada - midden
- Canada - oost
- India - centraal
- Central US
- VS - oost
- VS - oost 2
- Frankrijk - centraal
- Frankrijk - zuid
- Duitsland - noord
- Duitsland - west-centraal
- Japan - west
- Korea - zuid
- VS Noord-Centraal
- Europa - noord
- VS Zuid-Centraal
- Azië - zuidoost
- Zwitserland - noord
- UAE - centraal
- Verenigd Koninkrijk Zuid
- Verenigd Koninkrijk West
- VS-West-Centraal
- Europa -west
- India - west
- VS - west 2

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de voor delen van [Azure Cosmos DB Jupyter-notebooks](cosmosdb-jupyter-notebooks.md)
* [Galerie met voor beelden van notebook verkennen](https://cosmos.azure.com/gallery.html)
* [Python-notebookfuncties en-opdrachten gebruiken](use-python-notebook-features-and-commands.md)
* [C#-notebookfuncties en -opdrachten gebruiken](use-csharp-notebook-features-and-commands.md)
* [Notitie blokken importeren uit een GitHub-opslag plaats](import-github-notebooks.md)


