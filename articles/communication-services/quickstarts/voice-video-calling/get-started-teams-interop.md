---
title: 'Quickstart: Teams-interop in Azure Communication Services'
titleSuffix: An Azure Communication Services quickstart
description: In deze quickstart leert u hoe u kunt deelnemen aan een Teams-vergadering met de Azure Communication-aanroepende SDK.
author: chpalm
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: 0e75d2b480a9cbfd2977d9d449c1ea12bdfe4920
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106095608"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Quickstart: Voeg u aanroepende app toe aan een Teams-meeting

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

> [!IMPORTANT]
> Vul [dit formulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u)in om [teams Tenant interoperabiliteit](../../concepts/teams-interop.md)in of uit te scha kelen.

Ga aan de slag met Azure Communication Services door uw aanroep oplossing te verbinden met micro soft teams met de Java script SDK.

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop-ios.md)]
::: zone-end

De functionaliteit die in dit document wordt beschreven, maakt gebruik van de algemene Beschik baarheid-versie van de Sdk's voor communicatie Services. Voor samenwerkings verbanden van teams is de bèta versie van de Sdk's voor communicatie Services vereist. De bèta-Sdk's kunnen worden bekeken op de [pagina release opmerkingen](https://github.com/Azure/Communication/tree/master/releasenotes).

Wanneer u de stap pakket installeren uitvoert met de bèta-Sdk's, wijzigt u de versie van uw pakket in de nieuwste bèta versie door Version `@1.0.0-beta.10` (versie op het moment van het schrijven van dit artikel) op te geven in de naam van het `communication-calling` pakket. U hoeft de pakket opdracht niet te wijzigen `communication-common` . Bijvoorbeeld:

```console
npm install @azure/communication-calling@1.0.0-beta.10 --save
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Communication Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd. Meer informatie over het [opschonen van resources](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen:

- Bekijk ons [voorbeeld van een aanroephero](../../samples/calling-hero-sample.md)
- Meer informatie over het [aanroepen van SDK-mogelijkheden](./calling-client-samples.md)
- Meer informatie over [de werking van aanroepen](../../concepts/voice-video-calling/about-call-types.md)
