---
author: baanders
description: bestand opnemen om lokale verificatie in te stellen voor DefaultAzureCredential in Azure Digital Twins-voorbeelden - met intro
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: dcef8030c47e771e5cd771dac09b5f96e3d38abd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102473730"
---
### <a name="set-up-local-azure-credentials"></a>Lokale Azure-referenties instellen

In dit voorbeeld wordt gebruikgemaakt van [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (onderdeel van de `Azure.Identity`-bibliotheek) om gebruikers te verifiëren bij de Azure Digital Twins-instantie wanneer u deze uitvoert op uw lokale machine. Voor meer informatie over verschillende manieren waarop een client-app kan verifiëren met Azure Digital Twins, raadpleegt u [*Instructies: Verificatiecode voor app schrijven*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]