---
title: Indexeer functie toegang tot Azure Storage met behulp van een vertrouwde service uitzondering
titleSuffix: Azure Cognitive Search
description: Schakel gegevens toegang door een Indexeer functie in azure Cognitive Search in op gegevens die veilig zijn opgeslagen in Azure Storage.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e139c15ef6de00376a4e1a88000d263c3486994b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92101372"
---
# <a name="indexer-access-to-azure-storage-using-the-trusted-service-exception-azure-cognitive-search"></a>Indexeer functie toegang tot Azure Storage met behulp van de uitzonde ring voor vertrouwde services (Azure Cognitive Search)

Indexeer functies in een Azure Cognitive Search-service die toegang hebben tot gegevens in Azure Storage accounts, kunnen gebruikmaken van de functie voor de [uitzonde ring van vertrouwde services](../storage/common/storage-network-security.md#exceptions) om veilig toegang te krijgen tot gegevens. Dit mechanisme biedt klanten die geen toegang tot de [Indexeer functie kunnen verlenen met behulp van IP-firewall regels](search-indexer-howto-access-ip-restricted.md) een eenvoudig, veilig en gratis alternatief voor toegang tot gegevens in opslag accounts.

> [!NOTE]
> Ondersteuning voor toegang tot gegevens in opslag accounts via een vertrouwde service-uitzonde ring is beperkt tot Azure Blob-opslag en Azure Data Lake Gen2-opslag. Azure-tabel opslag wordt niet ondersteund.

## <a name="step-1-configure-a-connection-using-a-managed-identity"></a>Stap 1: een verbinding configureren met behulp van een beheerde identiteit

Volg de instructies in [een verbinding met een Azure Storage account instellen met behulp van een beheerde identiteit](search-howto-managed-identities-storage.md). Wanneer u klaar bent, hebt u uw zoek service bij Azure Active Directory als een vertrouwde service geregistreerd en hebt u machtigingen verleend in Azure Storage die de specifieke rechten van de zoek identiteit voor toegang tot gegevens of informatie geeft.

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>Stap 2: vertrouwde micro soft-Services toegang geven tot het opslag account

Ga in het Azure Portal naar het tabblad **firewalls en virtuele netwerken** van het opslag account. Zorg ervoor dat de optie **vertrouwde micro soft-Services toegang geven tot dit opslag account** is ingeschakeld. Met deze optie wordt alleen het specifieke exemplaar van de zoek service met de juiste op rollen gebaseerde toegang tot het opslag account (sterke verificatie) toegestaan om toegang te krijgen tot gegevens in het opslag account, zelfs als deze worden beveiligd door de IP-firewall regels.

![Uitzonde ring voor vertrouwde services](media\search-indexer-howto-secure-access\exception.png "Uitzonde ring voor vertrouwde services")

Indexeer functies kunnen nu toegang krijgen tot gegevens in het opslag account, zelfs als het account is beveiligd via IP-firewall regels.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure Storage Indexeer functies:

- [Indexer van Azure Blob](search-howto-indexing-azure-blob-storage.md)
- [Indexeer functie Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
- [Indexeer functie van Azure-tabel](search-howto-indexing-azure-tables.md)