---
title: Een kennis archief met Storage Explorer weer geven
titleSuffix: Azure Cognitive Search
description: Bekijk en analyseer een Azure Cognitive Search Knowledge Store met de Storage Explorer van Azure Portal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: ba0b02067c032f9038051c169866588ded44af73
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85566014"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Een kennis archief met Storage Explorer weer geven

In dit artikel leert u hoe u verbinding kunt maken met een kennis archief en hoe u deze kunt verkennen met behulp van Storage Explorer in de Azure Portal.

## <a name="prerequisites"></a>Vereisten

+ Volg de stappen in [een kennis archief maken in azure Portal](knowledge-store-create-portal.md) om het voor beeld-kennis archief te maken dat wordt gebruikt in dit overzicht.

+ U hebt ook de naam nodig van het Azure Storage-account dat u hebt gebruikt om het kennis archief te maken, samen met de toegangs sleutel van de Azure Portal.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Een kennis archief in Storage Explorer weer geven, bewerken en er een query op uitvoeren

1. Open in het Azure Portal [het opslag account](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) dat u hebt gebruikt om het kennis archief te maken.

1. Klik op **Storage Explorer** in het linkernavigatievenster van het opslag account.

1. Vouw de lijst **tabellen** uit om een lijst met Azure-tabel prognoses weer te geven die zijn gemaakt tijdens het uitvoeren van de wizard **gegevens importeren** in uw hotel voor beeld van de voorbeeld gegevens.

Selecteer een tabel om de verrijkte gegevens weer te geven, met inbegrip van belang rijke zinsdelen en sentiment-scores.

   ![Tabellen in Storage Explorer weer geven](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Tabellen in Storage Explorer weer geven")

Klik op **bewerken** als u het gegevens type voor tabel waarden wilt wijzigen of afzonderlijke waarden in de tabel wilt wijzigen. Wanneer u het gegevens type voor een kolom in één tabelrij wijzigt, wordt deze toegepast op alle rijen.

   ![Tabel in Storage Explorer bewerken](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Tabel in Storage Explorer bewerken")

Als u query's wilt uitvoeren, klikt u op **query** op de opdracht balk en voert u de voor waarden in.  

   ![Query tabel in Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Query tabel in Storage Explorer")

## <a name="clean-up"></a>Opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling **Alle resources** of **Resourcegroepen** in het navigatiedeelvenster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, indexeerfuncties en gegevensbronnen. U kunt afzonderlijke items in de portal verwijderen om onder de limiet te blijven.

## <a name="next-steps"></a>Volgende stappen

Verbind dit kennis archief met Power BI voor een diepere analyse of ga door met de code met behulp van de REST API en postman om een ander kennis archief te maken.

> [!div class="nextstepaction"]
> [Verbinding maken met Power bi](knowledge-store-connect-power-bi.md) 
>  [Een kennis archief maken in rest](knowledge-store-create-rest.md)
