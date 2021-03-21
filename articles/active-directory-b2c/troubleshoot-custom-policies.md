---
title: Problemen oplossen met aangepaste beleids regels in Azure Active Directory B2C
description: Meer informatie over benaderingen voor het oplossen van fouten bij het werken met aangepaste beleids regels in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 87ea77540e5fcaac0c4231403473d25ebae46aac
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94840250"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Problemen met Azure AD B2C aangepaste beleids regels en het Framework voor identiteits ervaring oplossen

Als u Azure Active Directory B2C (Azure AD B2C) aangepast beleid gebruikt, kunt u problemen ondervinden bij het instellen van het Framework voor identiteits ervaring in de XML-indeling van de beleids taal. Leer hoe u aangepaste beleids regels kunt schrijven, bijvoorbeeld om een nieuwe taal te leren. In dit artikel worden enkele hulp middelen en tips beschreven die u kunnen helpen bij het detecteren en oplossen van problemen.

In dit artikel vindt u informatie over het oplossen van problemen met de Azure AD B2C aangepaste beleids configuratie. Er wordt geen oplossing voor de Relying Party toepassing of de bijbehorende id-bibliotheek.

## <a name="xml-editing"></a>XML bewerken

De meest voorkomende fout bij het instellen van aangepast beleid is XML met een onjuiste indeling. Een goede XML-editor is bijna essentieel. Er wordt een XML-bestand met inhoud, kleur codes, vooraf gevulde termen en XML-elementen geïndexeerd en kan worden gevalideerd tegen een XML-schema.

Twee van onze favoriete editors zijn [Visual Studio code](https://code.visualstudio.com/) en [Klad blok + +](https://notepad-plus-plus.org/).

Validatie van XML-schema identificeert fouten voordat u het XML-bestand uploadt. In de hoofdmap van het [Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)haalt u het bestand XML schema definition file *TrustFrameworkPolicy_0.3.0.0. XSD*. Als u wilt weten hoe u het XSD-schema bestand gebruikt voor validatie in uw editor, zoekt u naar *XML-hulpprogram ma's* en *XML-validatie* of vergelijk bare informatie in de documentatie van de editor.

Mogelijk vindt u een beoordeling van de XML-regels handig. Azure AD B2C weigert XML-opmaak fouten die worden gedetecteerd. In sommige gevallen kan XML met onjuiste opmaak fout berichten veroorzaken die misleidend zijn.

## <a name="upload-policies-and-policy-validation"></a>Beleids regels en beleids validatie uploaden

Validatie van het XML-beleids bestand wordt automatisch uitgevoerd bij het uploaden. De meeste fouten zorgen ervoor dat het uploaden mislukt. Validatie bevat het beleids bestand dat u uploadt. Het bevat ook de keten van bestanden waarnaar het upload bestand verwijst (het Relying Party-beleids bestand, het extensie bestand en het basis bestand).

Veelvoorkomende validatie fouten zijn onder andere:

> Fout fragment: `...makes a reference to ClaimType with id "displayName" but neither the policy nor any of its base policies contain such an element`

* De waarde voor claim type is mogelijk verkeerd gespeld of bestaat niet in het schema.
* Claim type-waarden moeten worden gedefinieerd in ten minste een van de bestanden in het beleid.
    Bijvoorbeeld: `<ClaimType Id="issuerUserId">`
* Als claim type is gedefinieerd in het extensie bestand, maar dit ook wordt gebruikt in een TechnicalProfile-waarde in het basis bestand, resulteert het uploaden van het basis bestand in een fout.

> Fout fragment: `...makes a reference to a ClaimsTransformation with id...`

* De oorzaak van deze fout kan hetzelfde zijn als voor de fout claim type.

> Fout fragment: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`

* Controleer of de TenantId-waarde in `<TrustFrameworkPolicy\>` de `<BasePolicy\>` elementen en overeenkomt met uw doel Azure AD B2C Tenant.

## <a name="troubleshoot-the-runtime"></a>Problemen met de runtime oplossen

* Gebruik **nu uitvoeren** en `https://jwt.ms` om uw beleids regels onafhankelijk van uw web-of mobiele toepassing te testen. Deze website fungeert als een Relying Party-toepassing. De inhoud van het JSON-webtoken (JWT) dat wordt gegenereerd door uw Azure AD B2C-beleid wordt weer gegeven.

    Een test toepassing maken die kan worden omgeleid naar `https://jwt.ms` voor token inspectie:

    [!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

* Gebruik [Fiddler](https://www.telerik.com/fiddler)om de uitwisseling van berichten tussen uw client browser en Azure AD B2C te traceren. Het kan u helpen een indicatie te krijgen van waar uw gebruikers zich niet meer in de Orchestration-stappen bevinden.

* Gebruik in de **ontwikkelings modus** [Application Insights](troubleshoot-with-application-insights.md) om de activiteit van uw gebruikers traject voor identiteits ervaring te traceren. In de **ontwikkelings modus** kunt u de uitwisseling van claims tussen het Framework voor identiteits ervaring en de verschillende claim providers zien die zijn gedefinieerd door technische profielen, zoals id-providers, API-gebaseerde services, de Azure AD B2C gebruikers lijst en andere services, zoals Azure AD multi-factor Authentication.

## <a name="recommended-practices"></a>Aanbevolen procedures

**Bewaar meerdere versies van uw scenario's. Deze groeperen in een project met uw toepassing.** De basis-, uitbrei dingen-en Relying Party-bestanden zijn direct afhankelijk van elkaar. Sla ze op als groep. Wanneer er nieuwe functies aan uw beleid worden toegevoegd, kunt u afzonderlijke werk versies gebruiken. Werk versies in uw eigen bestands systeem faseren met de toepassings code waarmee ze communiceren. Uw toepassingen kunnen veel verschillende Relying Party-beleid aanroepen in een Tenant. Ze worden mogelijk afhankelijk van de claims die ze verwachten van uw Azure AD B2C-beleid.

**Technische profielen ontwikkelen en testen met bekende gebruikers reizen.** Geteste Starter Pack-beleids regels gebruiken om uw technische profielen in te stellen. U kunt ze afzonderlijk testen voordat u ze opneemt in uw eigen trajecten voor gebruikers.

**Ontwikkel en test gebruikers ritten met geteste technische profielen.** Wijzig de indelings stappen van een gebruikers traject incrementeel. Bouw geleidelijk uw beoogde scenario's.

## <a name="next-steps"></a>Volgende stappen

Beschikbaar op GitHub: down load het [Active Directory-B2C-Custom-Policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) . zip-archief. U kunt ook de opslag plaats klonen:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
```
