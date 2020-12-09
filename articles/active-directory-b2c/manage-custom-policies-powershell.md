---
title: Aangepaste beleids regels beheren met Power shell
titleSuffix: Azure AD B2C
description: Gebruik de Azure Active Directory (Azure AD) Power shell-cmdlet voor programmatisch beheer van uw Azure AD B2C aangepaste beleids regels. Aangepaste beleids regels maken, lezen, bijwerken en verwijderen met Power shell.
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fcc482e215e646fec20516f35641bd05398d2f2d
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928712"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Azure AD B2C aangepaste beleids regels beheren met Azure PowerShell

Azure PowerShell biedt verschillende cmdlets voor het opdracht regel-en op scripts gebaseerde aangepaste beleids beheer in uw Azure AD B2C-Tenant. Meer informatie over het gebruik van de Azure AD Power shell-module voor het volgende:

* Aangepaste beleids regels in een Azure AD B2C-Tenant weer geven
* Een beleid van een Tenant downloaden
* Een bestaand beleid bijwerken door de inhoud te overschrijven
* Een nieuw beleid uploaden naar uw Azure AD B2C-Tenant
* Een aangepast beleid verwijderen van een Tenant

## <a name="prerequisites"></a>Vereisten

* [Azure AD B2C Tenant](tutorial-create-tenant.md)en referenties voor een gebruiker in de directory met de [B2C IEF Policy](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) beheerdersrol
* [Aangepast beleid](custom-policy-get-started.md) dat is geüpload naar uw Tenant
* [Module Azure AD Power shell voor Graph **Preview**](/powershell/azure/active-directory/install-adv2)

## <a name="connect-powershell-session-to-b2c-tenant"></a>Een Power shell-sessie verbinden met de B2C-Tenant

Als u wilt werken met aangepast beleid in uw Azure AD B2C-Tenant, moet u eerst uw Power shell-sessie verbinden met de Tenant met behulp van de opdracht [Connect-AzureAD][Connect-AzureAD] .

Voer de volgende opdracht uit en vervang `{b2c-tenant-name}` door de naam van uw Azure AD B2C-Tenant. Meld u aan met een account dat is toegewezen aan de [B2C IEF Policy Administrator](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) Role in de Directory.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

Voor beeld van opdracht uitvoer met een geslaagde aanmelding:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>Alle aangepaste beleids regels in de Tenant weer geven

Als u aangepaste beleids regels detecteert, kan een Azure AD B2C beheerder bedrijfs logica voor hun bewerkingen controleren, beheren en toevoegen. Gebruik de opdracht [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] om een lijst met de id's van het aangepaste beleid in een Azure AD B2C-Tenant op te halen.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

Voor beeld van uitvoer van opdracht:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>Een beleid downloaden

Nadat u de lijst met beleids-Id's hebt bekeken, kunt u een specifiek beleid richten op [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] om de inhoud te downloaden.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

In dit voor beeld wordt het beleid met de ID *B2C_1A_signup_signin* gedownload:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

Als u de beleids inhoud lokaal wilt bewerken, pipet u de uitvoer van de opdracht naar een bestand met het `-OutputFilePath` argument en opent u het bestand in uw favoriete editor.

Voor beeld van opdracht verzen ding van uitvoer naar een bestand:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>Een bestaand beleid bijwerken

Nadat u een beleids bestand hebt bewerkt dat u hebt gemaakt of gedownload, kunt u het bijgewerkte beleid publiceren naar Azure AD B2C met behulp van de opdracht [set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] .

Als u de `Set-AzureADMSTrustFrameworkPolicy` opdracht geeft met de id van een beleid dat al in uw Azure AD B2C Tenant bestaat, wordt de inhoud van dat beleid overschreven.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Voor beeld opdracht:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

Zie de opdracht verwijzing [set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] voor meer voor beelden.

## <a name="upload-a-new-policy"></a>Een nieuw beleid uploaden

Wanneer u een wijziging aanbrengt in een aangepast beleid dat wordt uitgevoerd in productie, wilt u mogelijk meerdere versies van het beleid publiceren voor terugval of A/B-test scenario's. Of u wilt een kopie maken van een bestaand beleid, dit wijzigen met enkele kleine wijzigingen en deze vervolgens als nieuw beleid uploaden voor gebruik door een andere toepassing.

Gebruik de opdracht [New-AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] om een nieuw beleid te uploaden:

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Voor beeld opdracht:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>Een aangepast beleid verwijderen

Als u de levens cyclus van een schone bewerking wilt behouden, is het raadzaam om het ongebruikte aangepaste beleid regel matig te verwijderen. Het is bijvoorbeeld mogelijk dat u oude beleids versies wilt verwijderen nadat u een migratie naar een nieuwe set beleids regels hebt uitgevoerd en de functionaliteit van het nieuwe beleid hebt gecontroleerd. Als u een set aangepaste beleids regels probeert te publiceren en een fout bericht ontvangt, kan het zinvol zijn om de beleids regels die zijn gemaakt als onderdeel van de mislukte release, te verwijderen.

Gebruik de opdracht [Remove-AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] om een beleid uit uw Tenant te verwijderen.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

Voor beeld opdracht:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>Problemen met uploaden van beleid oplossen

Wanneer u probeert een nieuw aangepast beleid te publiceren of een bestaand beleid bij te werken, kan onjuiste XML-indeling en fouten in de overname keten van het beleids bestand validatie fouten veroorzaken.

Dit is bijvoorbeeld een poging om een beleid bij te werken met inhoud die ongeldige XML bevat (de uitvoer wordt afgekapt voor de boog):

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

Zie [problemen met Azure AD B2C aangepaste beleids regels en het Framework voor identiteits ervaring oplossen](./troubleshoot-custom-policies.md)voor meer informatie over het oplossen van een aangepast beleid.

## <a name="next-steps"></a>Volgende stappen

Zie [aangepaste beleids regels implementeren vanuit een Azure DevOps-pijp lijn](deploy-custom-policies-devops.md)voor meer informatie over het gebruik van Power shell om aangepast beleid te implementeren als onderdeel van een pijp lijn voor continue integratie/continue levering (CI/cd).

<!-- LINKS - External -->
[Connect-AzureAD]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/set-azureadmstrustframeworkpolicy
