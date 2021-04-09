---
title: Batch-accounts en Azure Storage accounts
description: Meer informatie over Azure Batch-accounts en hoe ze worden gebruikt vanuit een ontwikkelings oogpunt.
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 83108a265f91c9feef2fab424f1819939c2d58c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896745"
---
# <a name="batch-accounts-and-azure-storage-accounts"></a>Batch-accounts en Azure Storage accounts

Een Azure Batch account is een unieke geïdentificeerde entiteit binnen de batch-service. De meeste batch-oplossingen gebruiken [Azure Storage](../storage/index.yml) voor het opslaan van bron bestanden en uitvoer bestanden, zodat elk batch-account meestal is gekoppeld aan een bijbehorend opslag account.

## <a name="batch-accounts"></a>Batch-accounts

Alle verwerkingen en resources zijn gekoppeld aan een batch-account. Als uw toepassing een aanvraag indient voor de Batch-service, verifieert deze de aanvraag met de Azure Batch-accountnaam, de URL van het account en een toegangssleutel of een Azure Active Directory-token.

U kunt in één batch-account meerdere batch-workloads uitvoeren. U kunt ook uw workloads verdelen over batch-accounts die deel uitmaken van hetzelfde abonnement, maar zich in verschillende Azure-regio's bevinden.

U kunt een batch-account maken met behulp van de [Azure Portal](batch-account-create-portal.md) of via een programma, zoals met de [Batch Management .net-bibliotheek](batch-management-dotnet.md). Wanneer u het account maakt, kunt u een gekoppeld Azure-opslagaccount maken waarin u taakgerelateerde invoer- en uitvoergegevens of toepassingen kunt opslaan.

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]


## <a name="azure-storage-accounts"></a>Azure Storage-accounts

Bij de meeste Batch-oplossingen wordt gebruikgemaakt van Azure Storage om resourcebestanden en uitvoerbestanden op te slaan. Uw Batch-taken (inclusief standaardtaken, begintaken, jobvoorbereidingstaken en jobvrijgevingstaken) geven bijvoorbeeld gewoonlijk bronbestanden op die zich in een opslagaccount bevinden. Opslag accounts slaan ook de gegevens op die worden verwerkt en de uitvoer gegevens die worden gegenereerd.

Batch biedt ondersteuning voor de volgende typen Azure Storage-account:

- Accounts voor algemeen gebruik v2 (GPv2-accounts)
- Accounts voor algemeen gebruik v1 (GPv1-accounts)
- Blob-opslagaccounts (momenteel ondersteund voor toepassingen in de configuratie van de virtuele machine)

Zie [overzicht van Azure Storage-account](../storage/common/storage-account-overview.md)voor meer informatie over opslag accounts.

U kunt een opslagaccount koppelen aan uw Batch-account. Dit kunt u doen tijdens het maken van het Batch-account of later. Denk bij het kiezen van een opslagaccount aan de kosten- en prestatievereisten. Het GPv2-account en het Blob Storage-account bieden, vergeleken met een GPv1-account, bijvoorbeeld hogere [limieten voor capaciteit en schaalbaarheid](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/). (Neem contact op met de ondersteuning van Azure om een verhoging van een opslag limiet aan te vragen.) Met deze account opties kunt u de prestaties verbeteren van batch-oplossingen die een groot aantal parallelle taken bevatten die worden gelezen of geschreven naar het opslag account.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [knoop punten en groepen](nodes-and-pools.md).
- Meer informatie over het maken en beheren van batch-accounts met behulp van de [Azure Portal](batch-account-create-portal.md) of [Batch Management .net](batch-management-dotnet.md).
- Meer informatie over het gebruik van [privé-eind punten](private-connectivity.md) met Azure batch accounts.
