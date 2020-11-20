---
title: Ondersteunde Microsoft Graph bewerkingen
titleSuffix: Azure AD B2C
description: Een index van de Microsoft Graph bewerkingen die worden ondersteund voor het beheer van Azure AD B2C resources, waaronder gebruikers, gebruikers stromen, id-providers, aangepast beleid, beleids sleutels en meer.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9db46d13c9a798204958a7c295df9cca169fc08f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954032"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Microsoft Graph bewerkingen die beschikbaar zijn voor Azure AD B2C

De volgende Microsoft Graph API-bewerkingen worden ondersteund voor het beheer van Azure AD B2C resources, waaronder gebruikers, id-providers, gebruikers stromen, aangepaste beleids regels en beleids sleutels.

Elke koppeling in de volgende secties is gericht op de corresponderende pagina binnen de Microsoft Graph API-verwijzing voor die bewerking.

## <a name="user-management"></a>Gebruikersbeheer

- [Gebruikers weergeven](/graph/api/user-list)
- [Een consument gebruiker maken](/graph/api/user-post-users)
- [Een gebruiker ophalen](/graph/api/user-get)
- [Een gebruiker bijwerken](/graph/api/user-update)
- [Een gebruiker verwijderen](/graph/api/user-delete)

Zie voor meer informatie over het beheren van Azure AD B2C gebruikers accounts met de Microsoft Graph-API [Azure AD B2C gebruikers accounts beheren met Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="user-phone-number-management"></a>Telefoon nummer beheer gebruiker

- [Add](https://docs.microsoft.com/graph/api/authentication-post-phonemethods)
- [Ophalen](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-get)
- [Bijwerken](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-update)
- [Verwijderen](https://docs.microsoft.com/graph/api/phoneauthenticationmethod-delete)

Zie [B2C authentication methods](https://docs.microsoft.com/graph/api/resources/b2cauthenticationmethodspolicy)(Engelstalig) voor meer informatie over het beheren van het telefoon nummer van de gebruiker met de Microsoft Graph-API.

## <a name="identity-providers-user-flow"></a>Id-providers (gebruikers stroom)

De id-providers beheren die beschikbaar zijn voor uw gebruikers stromen in uw Azure AD B2C-Tenant.

- [Id-providers weer geven die zijn geregistreerd in de Azure AD B2C Tenant](/graph/api/identityprovider-list)
- [Een id-provider maken](/graph/api/identityprovider-post-identityproviders)
- [Een id-provider ophalen](/graph/api/identityprovider-get)
- [ID-provider bijwerken](/graph/api/identityprovider-update)
- [Een id-provider verwijderen](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Gebruikersstroom

Vooraf gemaakte beleids regels configureren voor aanmelding, aanmelden, gecombineerde registratie en aanmelding, wacht woord opnieuw instellen en profiel update.

- [Gebruikers stromen weer geven](/graph/api/identityuserflow-list)
- [Een gebruikersstroom maken](/graph/api/identityuserflow-post-userflows)
- [Een gebruikers stroom ophalen](/graph/api/identityuserflow-get)
- [Een gebruikers stroom verwijderen](/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Aangepast beleid

Met de volgende bewerkingen kunt u uw Azure AD B2C Trust Framework-beleid beheren, ook wel [aangepast beleid](custom-policy-overview.md)genoemd.

- [Alle beleids regels voor vertrouwens relaties die zijn geconfigureerd in een Tenant weer geven](/graph/api/trustframework-list-trustframeworkpolicies)
- [Vertrouwens raamwerk beleid maken](/graph/api/trustframework-post-trustframeworkpolicy)
- [Eigenschappen van een bestaand vertrouwens raamwerk beleid lezen](/graph/api/trustframeworkpolicy-get)
- [Het vertrouwens raamwerk beleid bijwerken of maken.](/graph/api/trustframework-put-trustframeworkpolicy)
- [Een bestaand vertrouwens raamwerk beleid verwijderen](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Beleidssleutels

In het Framework voor identiteits ervaring worden de geheimen opgeslagen waarnaar wordt verwezen in een aangepast beleid om de vertrouwens relatie tussen onderdelen tot stand te brengen. Deze geheimen kunnen symmetrische of asymmetrische sleutels/waarden zijn. In de Azure Portal worden deze entiteiten als **beleids sleutels** weer gegeven.

De resource op het hoogste niveau voor beleids sleutels in de Microsoft Graph-API is de [betrouw bare Framework sleutel](/graph/api/resources/trustframeworkkeyset). Elke **sleutelset** bevat ten minste één **sleutel**. Als u een sleutel wilt maken, moet u eerst een lege sleutelset maken en vervolgens een sleutel genereren in de sleutelset. U kunt een hand matig geheim maken, een certificaat uploaden of een PKCS12/pfx-profiel-sleutel. De sleutel kan een gegenereerd geheim zijn, een teken reeks die u definieert (zoals het Facebook-toepassings geheim) of een certificaat dat u uploadt. Als een sleutelset meerdere sleutels heeft, is slechts een van de sleutels actief.

### <a name="trust-framework-policy-keyset"></a>Sleutelset van vertrouwens raamwerk beleid

- [De set vertrouwens raamwerk-Series weer geven](/graph/api/trustframework-list-keysets)
- [Een vertrouwens raamwerk-Series maken](/graph/api/trustframework-post-keysets)
- [Een sleutelset ophalen](/graph/api/trustframeworkkeyset-get)
- [Een vertrouwens raamwerk-Series bijwerken](/graph/api/trustframeworkkeyset-update)
- [Een vertrouwens raamwerk-Series verwijderen](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Beleids sleutel vertrouwens raamwerk

- [Momenteel actieve sleutel in de sleutelset ophalen](/graph/api/trustframeworkkeyset-getactivekey)
- [Een sleutel in sleutelset genereren](/graph/api/trustframeworkkeyset-generatekey)
- [Een geheim op basis van een teken reeks uploaden](/graph/api/trustframeworkkeyset-uploadsecret)
- [Een X. 509-certificaat uploaden](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Een certificaat voor PKCS12/pfx-profiel-indeling uploaden](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Toepassingen

- [Toepassingen weergeven](/graph/api/application-list)
- [Een app maken](/graph/api/resources/application)
- [Toepassing bijwerken](/graph/api/application-update)
- [ServicePrincipal maken](/graph/api/resources/serviceprincipal)
- [Oauth2Permission-toekenning maken](/graph/api/resources/oauth2permissiongrant)
- [Toepassing verwijderen](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Eigenschappen van toepassings uitbreiding

- [Uitbrei ding-eigenschappen weer geven](/graph/api/application-list-extensionproperty)

Azure AD B2C biedt een directory die 100 aangepaste kenmerken per gebruiker kan bevatten. Voor gebruikers stromen worden deze extensie-eigenschappen [beheerd met behulp van de Azure Portal](custom-policy-custom-attributes.md). Azure AD B2C maakt voor aangepaste beleids regels de eigenschap voor u, de eerste keer dat het beleid een waarde naar de extensie-eigenschap schrijft.

## <a name="audit-logs"></a>Auditlogboeken

- [Audit logboeken weer geven](/graph/api/directoryaudit-list)

Zie [toegang tot Azure AD B2C audit logboeken](view-audit-logs.md)voor meer informatie over het openen van Azure AD B2C controle logboeken met de Microsoft Graph-API.