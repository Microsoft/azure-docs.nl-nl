---
title: Ondersteunings-en Help-opties voor Azure AD-App-ontwikkel aars
description: Informatie over het verkrijgen van hulp en ondersteuning voor ontwikkel vragen en problemen bij het maken van een toepassing die is geïntegreerd met micro soft-identiteiten (Azure Active Directory en Microsoft-account)
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: bce9479d063d091eb4fa68d2452d8a4218d45db9
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219940"
---
# <a name="support-and-help-options-for-developers"></a>Ondersteunings- en Help-opties voor ontwikkelaars

Als u net begint met de integratie met Azure Active Directory (Azure AD), micro soft-identiteiten of Microsoft Graph-API, of wanneer u een nieuwe functie implementeert voor uw toepassing, zijn er situaties waarin u hulp nodig hebt bij de Community of de ondersteunings opties kunt begrijpen die u als ontwikkelaar hebt. Dit artikel helpt u bij het begrijpen van deze opties, waaronder:

> [!div class="checklist"]
> * Zoeken of uw vraag niet is beantwoord door de Community of dat er al een bestaande documentatie is voor de functie die u wilt implementeren.
> * In sommige gevallen wilt u de ondersteunings Programma's gebruiken om u te helpen bij het opsporen van een specifiek probleem.
> * Als u het antwoord dat u nodig hebt niet kunt vinden, kunt u een vraag stellen op *micro soft Q&a*
> * Als u een probleem met een van onze verificatie bibliotheken tegen komt, kunt u een *github* -probleem veroorzaken
> * Ten slotte, als u met iemand moet praten, wilt u mogelijk een ondersteunings aanvraag openen

## <a name="search"></a>Zoeken

Als u een vraag hebt over de ontwikkeling, kunt u het antwoord mogelijk vinden in de documentatie, github-voor [beelden](https://github.com/azure-samples)of antwoorden op [micro soft Q&een](https://docs.microsoft.com/answers/products/) vragen.

### <a name="scoped-search"></a>Zoek opdracht in bereik

Voor snellere resultaten kunt u uw zoek opdracht bereiken naar [micro soft Q&](https://docs.microsoft.com/answers/products/)de documentatie en de code voorbeelden met behulp van de volgende query in uw favoriete zoek machine:

```
{Your Search Terms} (site:http://www.docs.microsoft.com/answers/products/ OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Waar *{uw zoek termen}* overeenkomen met uw zoek woorden.

## <a name="use-the-development-support-tools"></a>De hulpprogram ma's voor ontwikkelings ondersteuning gebruiken

| Hulpprogramma  | Beschrijving  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Plak een ID of toegangs token om de claim namen en-waarden te decoderen. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Hulp programma waarmee u aanvragen kunt doen en reacties op de Microsoft Graph-API ziet. |

## <a name="post-a-question-to-microsoft-qa"></a>Een vraag stellen aan micro soft Q&A

[Micro soft Q&A](https://docs.microsoft.com/answers/products/) is het voorkeurs kanaal voor vragen met betrekking tot ontwikkeling. Hier zijn leden van de ontwikkelaars community en micro soft-team leden rechtstreeks betrokken bij het helpen om uw problemen op te lossen.

Als u via zoeken geen antwoord op uw vraag kunt vinden, kunt u een nieuwe vraag verzenden naar [micro soft Q&a](https://docs.microsoft.com/answers/products/) . Gebruik een van de volgende tags bij het stellen van vragen om de community te helpen uw vraag sneller te identificeren en te beantwoorden:

|Onderdeel/gebied  | Tags |
|---------|---------|
| ADAL-bibliotheek | [adal](https://docs.microsoft.com/answers/topics/azure-ad-adal-deprecation.html) |
| MSAL-bibliotheek     | [msal](https://docs.microsoft.com/answers/topics/azure-ad-msal.html) |
| OWIN-middleware  | [[Azure-Active-Directory]](https://docs.microsoft.com/answers/topics/azure-active-directory.html) |
| [Azure B2B](../external-identities/what-is-b2b.md)  | [[Azure-AD-B2B]](https://docs.microsoft.com/answers/topics/azure-ad-b2b.html) |
| [Azure-B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[Azure-AD-B2C]](https://docs.microsoft.com/answers/topics/azure-ad-b2c.html) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[Azure-AD-Graph]](https://docs.microsoft.com/answers/topics/azure-ad-graph.html) |
| Elk ander gebied dat betrekking heeft op verificatie-of autorisatie-onderwerpen | [[Azure-Active-Directory]](https://docs.microsoft.com/answers/topics/azure-active-directory.html) |

De volgende berichten van [micro soft Q&A](https://docs.microsoft.com/answers/products/) bevatten tips voor het stellen van vragen en het toevoegen van de bron code. Volg deze richt lijnen om de kans te verg Roten dat leden van de Community uw vraag snel kunnen beoordelen en beantwoorden:

* [Hoe kan ik een goede vraag stellen](https://docs.microsoft.com/answers/articles/24951/how-to-write-a-quality-question.html)
* [Een mini maal, volledig en verifieerbaar voor beeld maken](https://docs.microsoft.com/answers/articles/24907/how-to-write-a-quality-answer.html)

## <a name="create-a-github-issue"></a>Een GitHub-probleem maken

Als u een fout of probleem met betrekking tot onze bibliotheken vindt, kunt u een probleem veroorzaken in onze GitHub-opslag plaatsen. Omdat onze bibliotheken open source zijn, kunt u ook een pull-aanvraag indienen.

Voor een lijst met bibliotheken en hun GitHub-opslag plaatsen raadpleegt u het volgende:

* [Azure Active Directory-bibliotheken (Authentication Library) en github-opslag plaatsen (ADAL)](../azuread-dev/active-directory-authentication-libraries.md)
* [Micro soft Authentication Library (MSAL)-](reference-v2-libraries.md) bibliotheken en github-opslag plaatsen

## <a name="open-a-support-request"></a>Een ondersteunings aanvraag openen

Als u contact moet opnemen met iemand, kunt u een ondersteunings aanvraag openen. Als u een Azure-klant bent, zijn er verschillende ondersteunings opties beschikbaar. Zie [Deze pagina voor het](https://azure.microsoft.com/support/plans/)vergelijken van plannen. Ondersteuning voor ontwikkel aars is ook beschikbaar voor Azure-klanten. Zie [Deze pagina](https://azure.microsoft.com/support/plans/developer/)voor meer informatie over het kopen van ondersteunings abonnementen voor ontwikkel aars.

* Als u al een ondersteunings abonnement voor Azure hebt, kunt u [hier een ondersteunings aanvraag openen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Als u geen Azure-klant bent, kunt u ook een ondersteunings aanvraag openen met micro soft via [onze commerciële ondersteuning](https://support.serviceshub.microsoft.com/supportforbusiness).

U kunt ook een [virtuele agent](https://support.microsoft.com/contactus/?ws=support) proberen om ondersteuning te verkrijgen of vragen te stellen.