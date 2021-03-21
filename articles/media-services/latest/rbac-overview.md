---
title: Toegangs beheer op basis van rollen voor Media Services accounts
description: In dit artikel wordt het toegangs beheer op basis van rollen (Azure RBAC) voor Azure Media Services-accounts beschreven.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 7c9edce71e0d0565de80153bfd5b3d5f1edb2e8f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955153"
---
# <a name="azure-role-based-access-control-azure-rbac-for-media-services-accounts"></a>Op rollen gebaseerd toegangs beheer voor Azure (Azure RBAC) voor Media Services accounts

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Op dit moment worden door Azure Media Services geen aangepaste rollen gedefinieerd die specifiek zijn voor de service. Klanten kunnen de ingebouwde rollen van **eigenaar** of **Inzender** gebruiken om volledige toegang te krijgen tot het Media Services-account. Het belangrijkste verschil tussen deze rollen is: de **eigenaar** kan bepalen wie toegang heeft tot een resource en de **mede werker** niet kan. De ingebouwde rol **lezer** kan ook worden gebruikt, maar de gebruiker of toepassing heeft alleen lees toegang tot de Media Services-api's. 

## <a name="design-principles"></a>Ontwerpprincipes

Een van de belangrijkste principes van de API v3 is het beter beveiligen van de API. V3-Api's retour neren geen geheimen of referenties voor **Get** -of **List** -bewerkingen. De sleutels zijn altijd null, leeg of opgeschoond uit het antwoord. De gebruiker moet een afzonderlijke actie methode aanroepen om geheimen of referenties op te halen. De rol van **lezer** kan geen bewerkingen aanroepen zoals Asset. ListContainerSas, StreamingLocator. ListContentKeys, ContentKeyPolicies. GetPolicyPropertiesWithSecrets. Door afzonderlijke acties uit te stellen, kunt u, indien gewenst, meer gedetailleerde Azure RBAC-beveiligings machtigingen instellen in een aangepaste rol.

Ga als volgt te werk om de bewerkingen weer te geven Media Services ondersteunt:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

In het artikel [ingebouwde roldefinities](../../role-based-access-control/built-in-roles.md) wordt duidelijk aangegeven wat de rol verleent. 

Raadpleeg de volgende artikelen voor meer informatie:

- [Klassieke abonnementsbeheerdersrollen, Azure-rollen en Azure AD-rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Wat is Azure RBAC (toegangsbeheer op basis van rollen)?](../../role-based-access-control/overview.md)
- [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de REST API](../../role-based-access-control/role-assignments-rest.md)
- [Bewerkingen voor de resource provider Media Services](../../role-based-access-control/resource-provider-operations.md#microsoftmedia)

## <a name="next-steps"></a>Volgende stappen

- [Ontwikkelen met Media Services v3-Api's](media-services-apis-overview.md)
- [Beleid voor inhouds sleutels ophalen met behulp van Media Services .NET](get-content-key-policy-dotnet-howto.md)
