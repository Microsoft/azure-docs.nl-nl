---
author: baanders
description: bestand opnemen dat een codeoplossing beschrijft voor de beperking van de tenantoverschrijdende met Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 2702f3c70d9e6f1a0bdad8695414e2d5fab02411
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589327"
---
In het volgende voorbeeld ziet u hoe u een tenant-id-waarde in de `InteractiveBrowserTenantId` opties in kunt stellen `DefaultAzureCredential` voor:

:::image type="content" source="../articles/digital-twins/media/troubleshoot-error-404/defaultazurecredentialoptions.png" alt-text="Schermopname van code met de methode DefaultAzureCredentialOptions. De waarde van InteractiveBrowserTenantId is ingesteld op een voorbeeld van de tenant-id.":::

Er zijn vergelijkbare opties beschikbaar om een tenant in te stellen voor verificatie met Visual Studio en Visual Studio Code. Zie de [defaultAzureCredentialOptions-documentatie](/dotnet/api/azure.identity.defaultazurecredentialoptions?view=azure-dotnet&preserve-view=true)voor meer informatie over de beschikbare opties.