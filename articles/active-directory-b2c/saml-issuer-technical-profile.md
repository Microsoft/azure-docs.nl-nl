---
title: Een technisch profiel voor een SAML-Uitgever definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een technisch profiel voor een SAML-verlener (Security Assertion Markup Language token) in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 73c381624b69acb6fe7a6296a3153160812818bf
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886410"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Een technisch profiel voor een SAML-token Uitgever definiëren in een Azure Active Directory B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) worden verschillende typen beveiligings tokens meegegeven wanneer elke verificatie stroom wordt verwerkt. Een technisch profiel voor een SAML-token uitgever verzendt een SAML-token dat terugkeert naar de Relying Party toepassing (service provider). Normaal gesp roken is dit technische profiel de laatste Orchestration-stap in de gebruikers reis.

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `SAML2` . Stel het element **OutputTokenFormat** in op `SAML2` .

In het volgende voor beeld ziet u een technisch profiel voor `Saml2AssertionIssuer` :

```xml
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="SAML2"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
    <Item Key="TokenNotBeforeSkewInSeconds">600</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Invoer, uitvoer en persistente claims

De **InputClaims**-, **OutputClaims**-en **PersistClaims** -elementen zijn leeg of ontbreken. De **InutputClaimsTransformations** -en **OutputClaimsTransformations** -elementen zijn ook afwezig.

## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| IssuerUri | Nee | De naam van de verlener die wordt weer gegeven in het SAML-antwoord. De waarde moet overeenkomen met de naam die is geconfigureerd in de Relying Party-toepassing. |
| XmlSignatureAlgorithm | Nee | De methode die Azure AD B2C gebruikt om de SAML-bevestigingen te ondertekenen. Mogelijke waarden: `Sha256` , `Sha384` , `Sha512` of `Sha1` . Zorg ervoor dat u het handtekening algoritme aan beide zijden met dezelfde waarde configureert. Gebruik alleen de algoritme die door uw certificaat wordt ondersteund. Zie [SAML-meta gegevens voor relying](relyingparty.md#metadata) Party voor informatie over het configureren van het SAML-antwoord|
|TokenNotBeforeSkewInSeconds| Nee| Geeft het verschil aan, als een geheel getal, voor het tijds tempel dat het begin van de geldigheids periode aangeeft. Hoe hoger dit getal is, hoe meer tijd de geldigheids periode begint ten opzichte van de tijd dat de claims worden uitgegeven voor de Relying Party. Als de TokenNotBeforeSkewInSeconds bijvoorbeeld is ingesteld op 60 seconden en het token is uitgegeven om 13:05:10 UTC, is het token geldig van 13:04:10 UTC. De standaardwaarde is 0. De maximum waarde is 3600 (één uur). |

## <a name="cryptographic-keys"></a>Cryptografische sleutels

Het CryptographicKeys-element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| MetadataSigning | Ja | Het x509-certificaat (RSA key set) dat wordt gebruikt voor het ondertekenen van SAML-meta gegevens. Azure AD B2C gebruikt deze sleutel om de meta gegevens te ondertekenen. |
| SamlMessageSigning| Ja| Geef het x509-certificaat (RSA key set) op dat moet worden gebruikt voor het ondertekenen van SAML-berichten. Azure AD B2C gebruikt deze sleutel om het antwoord `<samlp:Response>` te ondertekenen dat naar de Relying Party wordt verzonden.|

## <a name="session-management"></a>Sessiebeheer

Voor het configureren van de Azure AD B2C SAML-sessies tussen een Relying Party toepassing, het kenmerk van het `UseTechnicalProfileForSessionManagement` element, verwijzing naar [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) SSO-sessie.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor een voor beeld van het gebruik van een SAML-Uitgever technisch profiel:

- [Een SAML-toepassing registreren in Azure AD B2C](connect-with-saml-service-providers.md)

