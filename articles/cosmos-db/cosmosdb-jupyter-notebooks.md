---
title: Inleiding tot de ingebouwde ondersteuning voor Jupyter Notebooks in Azure Cosmos DB (preview)
description: Ontdek hoe u de ingebouwde ondersteuning voor Jupyter Notebooks in Azure Cosmos DB kunt gebruiken om query's interactief uit te voeren.
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/19/2020
author: deborahc
ms.author: dech
ms.openlocfilehash: 4f57d2793a28f4bbb201764c67af82f31d0b9333
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100369795"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Ingebouwde ondersteuning voor Jupyter Notebooks in Azure Cosmos DB (preview)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Jupyter Notebook is een open-source webtoepassing waarmee u documenten kunt maken en delen die live code, vergelijkingen, visualisaties en verhalende tekst bevatten. 

Ingebouwde Jupyter Notebooks van Azure Cosmos DB worden direct in Azure Portal en uw Azure Cosmos DB-accounts geïntegreerd, waardoor ze handig en gebruiksvriendelijk zijn. Ontwikkelaars, gegevenswetenschappers, ingenieurs en analisten kunnen de vertrouwde Jupyter Notebook-ervaring gebruiken voor gegevensverkenning, gegevensopschoning, gegevenstransformaties, numerieke simulaties, statische modellering, gegevensvisualisatie en machine learning.

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png" alt-text="Visualisaties van Jupyter Notebooks in Azure Cosmos DB":::

Azure Cosmos DB ondersteunt zowel C# -als Python-notebooks voor alle API's, waaronder Core (SQL), Cassandra, Gremlin, Table en API voor MongoDB. De notebook bevat ingebouwde opdrachten en functies waarmee u eenvoudig Azure Cosmos DB-resources maakt, gegevens kunt uploaden en uw gegevens in Azure Cosmos DB kunt opvragen en visualiseren. 

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png" alt-text="Ondersteuning voor Jupyter Notebooks in Azure Cosmos DB":::

## <a name="benefits-of-jupyter-notebooks"></a>Voordelen van Jupyter Notebooks

Jupyter Notebooks werden oorspronkelijk ontwikkeld voor toepassingen voor gegevenswetenschap die zijn geschreven in Python en R. Ze kunnen echter op verschillende manieren worden gebruikt voor verschillende soorten projecten, waaronder:

**Gegevensvisualisatie:** Met Jupyter Notebooks kunt u gegevens visualiseren in de vorm van een gedeeld notebook dat een gegevensset weergeeft als een afbeelding. U kunt visualisaties maken, interactieve wijzigingen aanbrengen in de gedeelde code en gegevensset en de resultaten delen.

**Code delen:** Services zoals GitHub bieden manieren om code te delen, maar ze zijn grotendeels niet-interactief. Met een Jupyter Notebook kunt u code weergeven, deze uitvoeren en de resultaten rechtstreeks in Azure Portal weergeven.

**Live interacties met code:** Code in een Jupyter Notebook is dynamisch; u kunt deze bewerken en de updates incrementeel uitvoeren in real-time. U kunt ook gebruikersbesturingselementen insluiten (bijvoorbeeld schuifregelaars of tekstinvoervelden) die worden gebruikt als invoerbronnen voor code, demo's of concepten (POC's).

**Documentatie van codevoorbeelden en resultaten van gegevensverkenning:** Als u een stukje code hebt en per regel wilt uitleggen hoe het werkt, kunt u dit insluiten in een Jupyter-notebook. U kunt interactiviteit tegelijkertijd met de documentatie toevoegen.

**Ingebouwde opdrachten voor Azure Cosmos DB:** Met de ingebouwde magic-opdrachten van Azure Cosmos DB kunt u eenvoudig communiceren met uw account. U kunt opdrachten als %%upload en %%sql gebruiken om gegevens te uploaden naar een container en er een query op uit te voeren met behulp van [SQL API-syntaxis](sql-query-getting-started.md). U hoeft geen aanvullende aangepaste code te schrijven.

**Alles in één locatie-omgeving:** Jupyter Notebooks combineren code, RTF-tekst, afbeeldingen, video's, animaties, wiskundige vergelijkingen, grafieken, kaarten, interactieve cijfers, widgets en grafische gebruikersinterfaces tot één document.

## <a name="components-of-a-jupyter-notebook"></a>Onderdelen van een Jupyter Notebook

Jupyter Notebooks kunnen diverse typen onderdelen bevatten, die zijn georganiseerd in afzonderlijke blokken of cellen:

**Tekst en HTML:** Tekst zonder opmaak of tekst die in de markdown-syntaxis is geannoteerd om HTML te genereren, kan op elk gewenst moment in het document worden ingevoegd. CSS-opmaak kan ook inline worden opgenomen of worden toegevoegd aan de sjabloon die wordt gebruikt voor het genereren van de notebook.

**Code en uitvoer:** Jupyter Notebooks ondersteunen Python- en C#-code. De resultaten van de uitgevoerde code worden direct na de codeblokken weergegeven en de codeblokken kunnen meerdere keren in elke gewenste volgorde worden uitgevoerd.

**Visualisaties:** U kunt afbeeldingen en grafieken genereren op basis van de code met behulp van modules als Matplotlib, Plotly, Bokeh en anderen. Net als bij de uitvoer worden deze visualisaties weergegeven naast de code die ze genereert. Net als bij de uitvoer worden deze visualisaties weergegeven naast de code die ze genereert.

**Multimedia:** Omdat Jupyter Notebooks zijn gebouwd op webtechnologie, kunnen ze alle typen multimedia weergeven die door een webpagina worden ondersteund. U kunt deze toevoegen aan een notebook als HTML-elementen of u kunt ze programmatisch genereren met behulp van de module `IPython.display`.

**Gegevens:** U kunt de gegevens uit Azure Cosmos-containers of de resultaten van query's programmatisch importeren in een Jupyter Notebook. Gebruik ingebouwde magic-opdrachten om gegevens te uploaden in Azure Cosmos DB of er query's op uit te voeren. 

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen om aan de slag te gaan met ingebouwde Jupyter Notebooks in Azure Cosmos DB:

* [Notebooks in een Azure Cosmos-account inschakelen](enable-notebooks.md)
* [Galerie met voor beelden van notebook verkennen](https://cosmos.azure.com/gallery.html)
* [Python-notebookfuncties en-opdrachten gebruiken](use-python-notebook-features-and-commands.md)
* [C#-notebookfuncties en -opdrachten gebruiken](use-csharp-notebook-features-and-commands.md)
* [Notitie blokken importeren uit een GitHub-opslag plaats](import-github-notebooks.md)