---
title: Azure AD-tenant-app-claims aanpassen (PowerShell)
titleSuffix: Microsoft identity platform
description: Op deze pagina wordt Azure Active Directory claimtoewijzing beschreven.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: e77155f8a6efd3916ae90fcb562d688bb5b5126f
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598887"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Hoe: Claims aanpassen die worden ingediend in tokens voor een specifieke app in een tenant (preview)

> [!NOTE]
> Deze functie vervangt en vervangt de [claimaanpassing](active-directory-saml-claims-customization.md) die momenteel via de portal wordt aangeboden. Als u in dezelfde toepassing claims aan past met behulp van de portal naast de Graph/PowerShell-methode die in dit document wordt beschreven, negeren tokens die voor die toepassing zijn uitgegeven de configuratie in de portal. Configuraties die zijn gemaakt via de methoden die in dit document worden beschreven, worden niet weergegeven in de portal.

> [!NOTE]
> Deze mogelijkheid is momenteel beschikbaar als openbare preview. Wees voorbereid om terug te keren of eventuele wijzigingen te verwijderen. De functie is beschikbaar in elk Azure Active Directory (Azure AD)-abonnement tijdens de openbare preview. Wanneer de functie echter algemeen beschikbaar komt, is voor sommige aspecten van de functie mogelijk een Azure AD Premium-abonnement vereist. Deze functie ondersteunt het configureren van claimtoewijzingsbeleid voor WS-Fed-, SAML-, OAuth- en OpenID Connect protocollen.

Deze functie wordt gebruikt door tenantbeheerders om de claims aan te passen die worden ingediend in tokens voor een specifieke toepassing in hun tenant. U kunt claimtoewijzingsbeleid gebruiken voor het volgende:

- Selecteer welke claims zijn opgenomen in tokens.
- Maak claimtypen die nog niet bestaan.
- Kies of wijzig de bron van gegevens die in specifieke claims worden ingediend.

In dit artikel worden enkele veelvoorkomende scenario's beschreven die u kunnen helpen inzicht te krijgen in het gebruik van het beleidstype [voor claimtoewijzing.](reference-claims-mapping-policy-type.md)

Wanneer u een beleid voor claimtoewijzing maakt, kunt u ook een claim van een directoryschema-extensiekenmerk in tokens genereren. Gebruik *ExtensionID voor* het extensiekenmerk in plaats *van id* in het `ClaimsSchema` -element.  Zie Using directory schema extension attributes (Kenmerken van directoryschema-extensies gebruiken) voor meer informatie [over extensiekenmerken.](active-directory-schema-extensions.md)

## <a name="prerequisites"></a>Vereisten

In de volgende voorbeelden maakt, updatet, koppelt en verwijdert u beleidsregels voor service-principals. Beleidsregels voor claimtoewijzing kunnen alleen worden toegewezen aan service-principal-objecten. Als u niet eerder met Azure AD werkt, raden we u aan om te leren hoe u een [Azure AD-tenant kunt krijgen](quickstart-create-new-tenant.md) voordat u verdergaat met deze voorbeelden.

Ga als volgt te werk om aan de slag te gaan:

1. Download de nieuwste openbare [preview-versie van de Azure AD PowerShell-module.](https://www.powershellgallery.com/packages/AzureADPreview)
1. Voer de opdracht Verbinden uit om u aan te melden bij uw Azure AD-beheerdersaccount. Voer deze opdracht uit telkens wanneer u een nieuwe sessie start.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Voer de volgende opdracht uit om alle beleidsregels te zien die in uw organisatie zijn gemaakt. U wordt aangeraden deze opdracht na de meeste bewerkingen in de volgende scenario's uit te voeren om te controleren of uw beleid wordt gemaakt zoals verwacht.

   ``` powershell
   Get-AzureADPolicy
   ```

## <a name="omit-the-basic-claims-from-tokens"></a>De basisclaims weglaten uit tokens

In dit voorbeeld maakt u een [](reference-claims-mapping-policy-type.md#claim-sets) beleid dat de basisclaimset verwijdert uit tokens die zijn uitgegeven aan gekoppelde service-principals.

1. Maak een beleid voor claimtoewijzing. Met dit beleid, gekoppeld aan specifieke service-principals, wordt de basisclaimset uit tokens verwijderd.
   1. Voer de volgende opdracht uit om het beleid te maken:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Voer de volgende opdracht uit om uw nieuwe beleid weer te geven en de ObjectId van het beleid op te halen:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Wijs het beleid toe aan uw service-principal. U moet ook de ObjectId van uw service-principal op te halen.
   1. Als u alle service-principals van uw organisatie wilt zien, kunt u [een query uitvoeren op Microsoft Graph API](/graph/traverse-the-graph). Of meld u [in Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)aan bij uw Azure AD-account.
   2. Wanneer u de ObjectId van uw service-principal hebt, moet u de volgende opdracht uitvoeren:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="include-the-employeeid-and-tenantcountry-as-claims-in-tokens"></a>De EmployeeID en TenantCountry opnemen als claims in tokens

In dit voorbeeld maakt u een beleid dat de EmployeeID en TenantCountry toevoegt aan tokens die zijn uitgegeven aan gekoppelde service-principals. De EmployeeID wordt als het naamclaimtype in zowel SAML-tokens als JWT's uitgezonden. De TenantCountry wordt in zowel SAML-tokens als JWT's als het claimtype land/regio ingediend. In dit voorbeeld blijven we de basisclaims die zijn ingesteld in de tokens opnemen.

1. Maak een beleid voor claimtoewijzing. Dit beleid, gekoppeld aan specifieke service-principals, voegt de claims EmployeeID en TenantCountry toe aan tokens.
   1. Voer de volgende opdracht uit om het beleid te maken:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Voer de volgende opdracht uit om uw nieuwe beleid te zien en de object-id van het beleid op te halen:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Wijs het beleid toe aan uw service-principal. U moet ook de ObjectId van uw service-principal op halen.
   1. Als u alle service-principals van uw organisatie wilt zien, kunt u [een query uitvoeren op Microsoft Graph API](/graph/traverse-the-graph). Of meld u [in Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)aan bij uw Azure AD-account.
   2. Wanneer u de ObjectId van uw service-principal hebt, moet u de volgende opdracht uitvoeren:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="use-a-claims-transformation-in-tokens"></a>Een claimtransformatie gebruiken in tokens

In dit voorbeeld maakt u een beleid dat een aangepaste claim 'JoinedData' naar JWT's stuurt die zijn uitgegeven aan gekoppelde service-principals. Deze claim bevat een waarde die is gemaakt door de gegevens die zijn opgeslagen in het kenmerk extensionattribute1 te samenvoegen op het gebruikersobject met '.sandbox'. In dit voorbeeld sluiten we de basisclaims uit die zijn ingesteld in de tokens.

1. Maak een beleid voor claimtoewijzing. Dit beleid, gekoppeld aan specifieke service-principals, voegt de claims EmployeeID en TenantCountry toe aan tokens.
   1. Voer de volgende opdracht uit om het beleid te maken:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Voer de volgende opdracht uit om uw nieuwe beleid weer te geven en de ObjectId van het beleid op te halen:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Wijs het beleid toe aan uw service-principal. U moet ook de ObjectId van uw service-principal op te halen.
   1. Als u alle service-principals van uw organisatie wilt zien, kunt u [een query uitvoeren op Microsoft Graph API](/graph/traverse-the-graph). Of meld u [in Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)aan bij uw Azure AD-account.
   2. Wanneer u de ObjectId van uw service-principal hebt, moet u de volgende opdracht uitvoeren:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="security-considerations"></a>Beveiligingsoverwegingen

Toepassingen die tokens ontvangen, zijn afhankelijk van het feit dat de claimwaarden gezaghebbend zijn uitgegeven door Azure AD en niet kunnen worden gemanipuleerd. Wanneer u de inhoud van het token echter wijzigt via beleidsregels voor claimtoewijzing, zijn deze veronderstellingen mogelijk niet meer juist. Toepassingen moeten expliciet bevestigen dat tokens zijn gewijzigd door de maker van het claimtoewijzingsbeleid om zichzelf te beschermen tegen claimtoewijzingsbeleid dat is gemaakt door kwaadwillende actoren. Dit kan op de volgende manieren worden gedaan:

- Een aangepaste ondertekeningssleutel configureren
- Werk het toepassingsmanifest bij om toe te staan claims te accepteren.
 
Zonder deze fout retournt Azure AD de [ `AADSTS50146` foutcode](reference-aadsts-error-codes.md#aadsts-error-codes).

### <a name="custom-signing-key"></a>Aangepaste ondertekeningssleutel

Als u een aangepaste ondertekeningssleutel wilt toevoegen aan het service-principal-object, kunt u de cmdlet Azure PowerShell gebruiken om een certificaatsleutelreferentie voor uw toepassingsobject [`New-AzureADApplicationKeyCredential`](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential) te maken.

Apps waar claimtoewijzing is ingeschakeld, moeten hun token-ondertekeningssleutels valideren door toe te voegen aan hun `appid={client_id}` [OpenID Connect metagegevensaanvragen](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document). Hieronder ziet u de indeling van het OpenID Connect metagegevensdocument dat u moet gebruiken:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="update-the-application-manifest"></a>Het toepassingsmanifest bijwerken

U kunt de eigenschap ook instellen `acceptMappedClaims` op `true` in het [toepassingsmanifest](reference-app-manifest.md). Zoals beschreven in het [resourcetype apiApplication,](/graph/api/resources/apiapplication#properties)kan een toepassing claimtoewijzing gebruiken zonder een aangepaste ondertekeningssleutel op te geven.

Hiervoor moet de aangevraagde token-doelgroep een geverifieerde domeinnaam van uw Azure AD-tenant gebruiken. Dit betekent dat u ervoor moet zorgen dat u de (vertegenwoordigd door de in het toepassingsmanifest) in te stellen op of (gewoon met de standaard `Application ID URI` `identifierUris` `https://contoso.com/my-api` `https://contoso.onmicrosoft.com/my-api` tenantnaam).

Als u geen geverifieerd domein gebruikt, retourneert Azure AD een foutcode met het bericht `AADSTS501461` 'AcceptMappedClaims wordt alleen ondersteund voor een token doelgroep die overeenkomt met de toepassings-GUID of een doelgroep binnen de geverifieerde domeinen van de *tenant. Wijzig de resource-id of gebruik een toepassingsspecifieke ondertekeningssleutel.*

## <a name="next-steps"></a>Volgende stappen

- Lees het [naslagartikel over claimtoewijzingsbeleidstype](reference-claims-mapping-policy-type.md) voor meer informatie.
- Zie How to: Customize claims issued in the SAML token for enterprise applications (Claims aanpassen die zijn uitgegeven in het SAML-token voor bedrijfstoepassingen) voor meer informatie over het aanpassen van claims die zijn uitgegeven in het [SAML-token](active-directory-saml-claims-customization.md) via de Azure Portal.
- Zie Using directory schema extension attributes in claims (Kenmerken van [directoryschema-extensies gebruiken in claims) voor meer informatie over extensiekenmerken.](active-directory-schema-extensions.md)
