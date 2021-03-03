---
title: Notebooks importeren en uitvoeren vanuit een GitHub-opslag plaats in Azure Cosmos DB
description: Informatie over het maken van verbinding met GitHub en het importeren van de notitie blokken van een GitHub-opslag plaats naar uw Azure Cosmos-account. Na het importeren kunt u deze uitvoeren, bewerken en de wijzigingen weer opslaan in GitHub.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: f7002b3968e91447a26315f31347ad469aa5daca
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690648"
---
# <a name="import-notebooks-from-a-github-repo-into-azure-cosmos-db"></a>Notitie blokken importeren van een GitHub-opslag plaats in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Nadat u de [ondersteuning voor notebooks](enable-notebooks.md) voor uw Azure Cosmos-accounts hebt ingeschakeld, kunt u nieuwe notitie blokken maken, nieuwe notitie blokken uploaden van uw lokale computer of de bestaande notitie blokken importeren uit uw github-accounts. In dit artikel wordt beschreven hoe u de werk ruimte van uw notitie blok verbindt met GitHub en de notitie blokken importeert uit een GitHub-opslag plaats naar uw Azure Cosmos-account. Na het importeren kunt u ze uitvoeren, wijzigingen aanbrengen en de wijzigingen weer opslaan in GitHub.

## <a name="get-notebooks-from-github"></a>Notitie blokken ophalen van GitHub

U kunt verbinding maken met uw eigen GitHub-opslag plaatsen of andere open bare GitHub-opslag plaatsen voor het lezen, ontwerpen en delen van notitie blokken in Azure Cosmos DB. Gebruik de volgende stappen om verbinding te maken met een GitHub-account:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) en navigeer naar uw Azure Cosmos-account.

1. Open het tabblad **Data Explorer** . Op dit tabblad worden alle bestaande data bases, containers en notitie blokken weer gegeven.

1. Selecteer de menu opdracht **verbinding maken met github** .

1. Er wordt een tabblad geopend waarin u kunt kiezen om verbinding te maken met alleen **open bare opslag plaatsen** of **open bare en privé opslag plaatsen**.  Nadat u de optie vereist hebt gekozen, selecteert u **toegang verlenen**. Er is autorisatie vereist voor Azure Cosmos DB om toegang te krijgen tot de opslag plaatsen in uw GitHub-account.

   :::image type="content" source="./media/import-github-notebooks/authorize-access-github.png" alt-text="Azure Cosmos DB toegang verlenen tot uw GitHub-opslag plaatsen":::

1. U wordt doorgestuurd naar de webpagina ' github.com ' waar u de autorisatie kunt bevestigen. Selecteer de knop **AzureCosmosDBNotebooks autoriseren** en voer het wacht woord voor uw github-account in bij de prompt.

1. Nadat de autorisatie is voltooid, gaat u terug naar uw Azure Cosmos-account. U kunt vervolgens alle open bare/persoonlijke opslag plaatsen bekijken uit uw GitHub-account. U kunt een opslag plaats selecteren in de lijst die beschikbaar is of rechtstreeks een opslag plaats toevoegen met behulp van de URL.

1. Wanneer u de vereiste opslag plaats selecteert, wordt de opslag plaats-vermelding verplaatst van de sectie **vastgemaakte opslag plaatsen** naar **vastgemaakte opslag plaatsen** sectie. Als dat nodig is, kunt u ook een specifieke vertakking van die opslag plaats kiezen waaruit de notitie blokken moeten worden geïmporteerd.

   :::image type="content" source="./media/import-github-notebooks/choose-repo-branch.png" alt-text="Een opslag plaats en een vertakking kiezen":::

1. Selecteer **OK** om de import bewerking te volt ooien. Alle notitie blokken die beschikbaar zijn in de geselecteerde vertakking van uw opslag plaats, worden geïmporteerd in uw Azure Cosmos-account.

Nadat u hebt geïntegreerd met een GitHub-account, kunt u alleen de lijst met opslag plaatsen en notitie blokken in uw Azure Cosmos-account zien. Deze instructie is waar, zelfs als meerdere gebruikers zich aanmelden bij het Azure Cosmos DB-account en hun eigen accounts toevoegen. Met andere woorden, meerdere gebruikers kunnen hetzelfde Azure Cosmos-account gebruiken om de notebook-werk ruimte te verbinden met GitHub. Elke gebruiker ziet echter alleen de lijst met opslag plaatsen en notitie blokken die ze hebben geïmporteerd. De notitie blokken die door anderen zijn geïmporteerd, zijn niet zichtbaar voor u.

Als u uw GitHub-account wilt loskoppelen van de werk ruimte notebooks, opent u het tabblad **Data Explorer** , selecteert `…` u naast **github opslag plaatsen** en selecteert u **verbinding verbreken met github**.

## <a name="edit-a-notebook-and-push-changes-to-github"></a>Een notitie blok en push wijzigingen in GitHub bewerken

U kunt een bestaand notitie blok bewerken of een nieuw notitie blok toevoegen aan de opslag plaats en de wijzigingen opnieuw opslaan in GitHub.

Nadat u een bestaand notitie blok hebt bewerkt, selecteert u **Opslaan**. Er wordt een dialoog venster geopend waarin u het commit-bericht kunt invoeren voor de wijzigingen die u hebt aangebracht. Selecteer **door voeren** en het notitie blok in github wordt bijgewerkt. U kunt de updates valideren door u aan te melden bij uw GitHub-account en de doorvoer geschiedenis te controleren.

Na het door voeren van de wijzigingen in de normale GitHub-stroom worden meestal wijzigingen naar een extern bericht pushen. In dit geval is de doorvoer optie echter het doel van ' fase ring, het door voeren en het pushen van ' uw updates voor GitHub.

:::image type="content" source="./media/import-github-notebooks/commit-changes-github.png" alt-text="Notitie blokken bewerken en wijzigingen door voeren in GitHub":::

Nadat u het notitie blok hebt bewerkt, kunt u [het publiceren naar de notitieblok galerie](publish-notebook-gallery.md). 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de voor delen van [Azure Cosmos DB Jupyter-notebooks.](cosmosdb-jupyter-notebooks.md)
* [Galerie met voor beelden van notebook verkennen](https://cosmos.azure.com/gallery.html)
* [Notitie blokken publiceren naar de galerie met Azure Cosmos DB notebooks](publish-notebook-gallery.md)
* [Python-notebookfuncties en-opdrachten gebruiken](use-python-notebook-features-and-commands.md)
* [C#-notebookfuncties en -opdrachten gebruiken](use-csharp-notebook-features-and-commands.md)
