---
title: Gebruikers stromen in Azure Active Directory B2C | Microsoft Docs
titleSuffix: Azure AD B2C
description: Meer informatie over het flexibele beleids raamwerk van Azure Active Directory B2C en het maken van verschillende gebruikers stromen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 06253b571fd71623501c27fd5b0d9d4013727fc2
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840187"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Gebruikers stromen in Azure Active Directory B2C

Om u te helpen bij het instellen van de meest voorkomende identiteits taken voor uw toepassingen, bevat de Azure AD B2C Portal vooraf gedefinieerde, Configureer bare beleids regels met de naam **gebruikers stromen**. Met een gebruikers stroom kunt u bepalen hoe gebruikers met uw toepassing communiceren wanneer ze zich aanmelden, registreren, een profiel bewerken of een wacht woord opnieuw instellen. Met gebruikers stromen kunt u de volgende mogelijkheden bepalen:

- Account typen die worden gebruikt voor aanmelding, zoals sociale accounts zoals een Facebook-of lokale account
- Kenmerken die moeten worden verzameld van de consument, zoals de voor naam, de post code en de grootte van de schoenen
- Azure AD-Multi-Factor Authentication
- Aanpassing van de gebruikersinterface
- Informatie die de toepassing ontvangt als claims in een token

U kunt veel gebruikers stromen van verschillende typen in uw Tenant maken en deze naar behoefte gebruiken in uw toepassingen. Gebruikersstromen kunnen opnieuw worden gebruikt in verschillende toepassingen. Met deze flexibiliteit kunt u identiteits ervaringen definiëren en wijzigen met minimale of geen wijzigingen in uw code. Uw toepassing activeert een gebruikers stroom met behulp van een standaard-HTTP-verificatie aanvraag die een para meter voor de gebruikers stroom bevat. Er wordt een aangepast [token](tokens-overview.md) ontvangen als antwoord.

In de volgende voor beelden ziet u de query reeks parameter "p" waarmee de gebruikers stroom wordt opgegeven die moet worden gebruikt:

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow
```

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow
```

## <a name="user-flow-versions"></a>Gebruikersstroomversies

Azure AD B2C bevat verschillende soorten gebruikers stromen:

- **Meld u aan en meld u** aan voor beide registratie-en aanmeldings ervaringen met één configuratie. Gebruikers ondervinden het pad naar de juiste locatie, afhankelijk van de context. Ook inbegrepen zijn afzonderlijke **registratie** -of **aanmeldings stromen voor** gebruikers. We raden u echter aan de gebruikers stroom voor gecombineerde aanmelding en aanmeldings in te stellen.
- **Profiel bewerken** : Hiermee kunnen gebruikers hun profiel gegevens bewerken.
- **Wacht woord opnieuw instellen** : Hiermee kunt u configureren of en hoe gebruikers hun wacht woord opnieuw kunnen instellen.

De meeste gebruikers stroom typen hebben zowel een **Aanbevolen** versie als een **standaard** versie. Zie [versies van gebruikers stroom](user-flow-versions.md)voor meer informatie.

> [!IMPORTANT]
> Als u al eerder met gebruikers stromen hebt gewerkt in Azure AD B2C, zult u zien dat we hebben gerefereerd aan de manier waarop we verwijzen naar de versie van de gebruikers stroom. Eerder boden we V1-versies (klaar voor productie) en V1.1- en V2-versies (preview) aan. Nu hebben we gebruikers stromen geconsolideerd in twee versies:
>
>- **Aanbevolen** gebruikers stromen zijn de nieuwe Preview-versies van gebruikers stromen. Ze zijn uitgebreid getest en combi neren alle functies van de oudere versies van **v2** en **v 1.1** . Als u verdergaat, worden de nieuwe aanbevolen gebruikers stromen gehandhaafd en bijgewerkt. Zodra u bent overstappen op deze nieuwe aanbevolen gebruikers stromen, hebt u toegang tot nieuwe functies wanneer ze worden uitgebracht.
>- **Standaard** gebruikers stromen, voorheen bekend als **v1**, zijn algemeen beschikbaar, productie-kant-en-klare gebruikers stromen. Als uw gebruikers stroom kritiek zijn en afhankelijk zijn van uiterst stabiele versies, kunt u de standaard gebruikers stromen blijven gebruiken, waardoor deze versies niet behouden blijven en worden bijgewerkt.
>
>Alle verouderde preview-gebruikers stromen (V 1.1 en v2) bevinden zich op **1 augustus 2021** in een pad naar afschaffing. Waar mogelijk wordt u ten zeerste aangeraden om zo snel mogelijk [over te scha kelen naar de nieuwe **Aanbevolen** gebruikers stromen](user-flow-versions.md#how-to-switch-to-a-new-recommended-user-flow) , zodat u altijd kunt profiteren van de nieuwste functies en updates.

## <a name="linking-user-flows"></a>Gebruikers stromen koppelen

Een **registratie-of aanmeldings** gebruikers stroom met lokale accounts bevat een **verg eten wacht woord?** koppeling op de eerste pagina van de ervaring. Als u op deze koppeling klikt, wordt de gebruikers stroom voor wacht woord opnieuw instellen niet automatisch geactiveerd.

In plaats daarvan wordt de fout code `AADB2C90118` geretourneerd naar uw toepassing. Uw toepassing moet deze fout code verwerken door een specifieke gebruikers stroom uit te voeren waarmee het wacht woord opnieuw wordt ingesteld. Bekijk een voor beeld met een [eenvoudig ASP.net](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) -voor beeld dat het koppelen van gebruikers stromen laat zien.

## <a name="email-address-storage"></a>E-mail adres opslag

Een e-mail adres kan worden vereist als onderdeel van een gebruikers stroom. Als de gebruiker zich verifieert bij een sociale ID-provider, wordt het e-mail adres opgeslagen in de eigenschap **otherMails** . Als een lokaal account is gebaseerd op een gebruikers naam, wordt het e-mail adres opgeslagen in een eigenschap met een sterke authenticatie Details. Als een lokaal account is gebaseerd op een e-mail adres, wordt het e-mail adres opgeslagen in de eigenschap **signInNames** .

Het e-mail adres is niet gegarandeerd om in een van deze gevallen te worden geverifieerd. Een Tenant beheerder kan e-mail verificatie uitschakelen in het basis beleid voor lokale accounts. Zelfs als verificatie via e-mail adres is ingeschakeld, worden adressen niet gecontroleerd als ze afkomstig zijn van een sociale ID-provider en ze zijn niet gewijzigd.

Alleen de eigenschappen **otherMails** en **signInNames** worden weer gegeven via de Microsoft Graph-API. Het e-mail adres in de detail eigenschap sterke authenticatie is niet beschikbaar.

## <a name="next-steps"></a>Volgende stappen

Als u de aanbevolen gebruikers stromen wilt maken, volgt u de instructies in de [zelf studie: een gebruikers stroom maken](tutorial-create-user-flows.md).
