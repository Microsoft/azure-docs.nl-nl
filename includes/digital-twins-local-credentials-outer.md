---
author: baanders
description: bestand opnemen om lokale verificatie in te stellen voor DefaultAzureCredential in Azure Digital Twins-voorbeelden - met intro
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35643370d6fd313d42f954a578b73befc025c040
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011248"
---
### <a name="set-up-local-azure-credentials"></a>Lokale Azure-referenties instellen

In dit voorbeeld wordt gebruikgemaakt van [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (onderdeel van de `Azure.Identity`-bibliotheek) om gebruikers te verifiëren bij de Azure Digital Twins-instantie wanneer u deze uitvoert op uw lokale machine. Voor meer informatie over verschillende manieren waarop een client-app kan verifiëren met Azure Digital Twins, raadpleegt u [*Instructies: Verificatiecode voor app schrijven*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]