---
title: Een Azure app-identiteit (Power shell) maken | Azure
titleSuffix: Microsoft identity platform
description: Hierin wordt beschreven hoe u met Azure PowerShell een Azure Active Directory-toepassing en service-principal maakt en toegang tot resources verleent via toegangsbeheer op basis van rollen. Het laat zien hoe u de toepassing verifieert met een certificaat.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev , devx-track-azurepowershell
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.date: 02/22/2021
ms.author: ryanwi
ms.reviewer: tomfitz
ms.openlocfilehash: b27af53d615fa9c0c46699a52a004098dc46b7b2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688532"
---
# <a name="how-to-use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Procedure: Azure PowerShell gebruiken om een service-principal te maken met een certificaat

Wanneer u een app of een script hebt waarvoor toegang tot resources vereist is, kunt u een identiteit voor de app instellen en de app met eigen referenties verifiëren. Deze identiteit staat bekend als een service-principal. Hiermee kunt u het volgende doen:

* Andere machtigingen aan de identiteit van de app toekennen dan uw eigen machtigingen. Deze machtigingen worden meestal beperkt tot de functies die met de app moeten kunnen worden uitgevoerd.
* Een certificaat voor verificatie gebruiken bij het uitvoeren van een onbewaakt script.

> [!IMPORTANT]
> In plaats van een service-principal te maken, kunt u overwegen beheerde identiteiten te gebruiken voor Azure-resources voor uw toepassings identiteit. Als uw code wordt uitgevoerd op een service die beheerde identiteiten ondersteunt en toegang krijgt tot bronnen die ondersteuning bieden voor Azure Active Directory-verificatie (Azure AD), zijn beheerde identiteiten een betere optie. Zie [Wat is beheerde identiteiten voor Azure-resources?](../managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten voor Azure-resources, waaronder de services die momenteel door worden ondersteund.

In dit artikel leest u hoe een service-principal maakt die zichzelf verifieert met een certificaat. Zie [Een Azure-service-principal maken met Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps) voor het instellen van een service-principal met een wachtwoord.

Voor dit artikel hebt u de [meest recente versie](/powershell/azure/install-az-ps) van PowerShell nodig.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="required-permissions"></a>Vereiste machtigingen

U kunt dit artikel alleen volt ooien als u over voldoende machtigingen beschikt voor uw Azure AD-en Azure-abonnement. U moet in het bijzonder een app kunnen maken in azure AD en de Service-Principal toewijzen aan een rol.

De eenvoudigste manier om te controleren of uw account over de juiste machtigingen beschikt, verloopt via de portal. Zie [Check required permission](howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) (Vereiste machtiging controleren).

## <a name="assign-the-application-to-a-role"></a>De toepassing aan een rol toewijzen
Als u toegang wilt krijgen tot resources in uw abonnement, moet u de toepassing toewijzen aan een rol. Bepaal welke rol de juiste machtigingen voor de toepassing biedt. Zie [ingebouwde rollen van Azure](../../role-based-access-control/built-in-roles.md)voor meer informatie over de beschik bare rollen.

U kunt het bereik instellen op het niveau van het abonnement, de resource groep of de resource. Machtigingen worden overgenomen door lagere bereik niveaus. Als u bijvoorbeeld een toepassing toevoegt aan de rol van *lezer* voor een resource groep, betekent dit dat de resource groep en alle resources die deze bevat, kunnen worden gelezen. Selecteer de rol *Inzender* om de toepassing toe te staan acties uit te voeren zoals opnieuw opstarten, het starten en stoppen van exemplaren.

## <a name="create-service-principal-with-self-signed-certificate"></a>Service-principal met een zelfondertekend certificaat maken

In het volgende voorbeeld wordt een eenvoudig scenario behandeld. Maakt gebruik van [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) voor het maken van een service-principal met een zelfondertekend certificaat en maakt gebruik van [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) om de rol van [lezer](../../role-based-access-control/built-in-roles.md#reader) toe te wijzen aan de Service-Principal. De roltoewijzing is afgestemd op uw huidige, geselecteerde Azure-abonnement. Als u een ander abonnement wilt selecteren, gebruikt u [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext).

> [!NOTE]
> De New-SelfSignedCertificate-cmdlet en de PKI-module worden momenteel niet ondersteund in Power shell core. 

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $sp.ApplicationId
```

Het voor beeld in slaap stand gedurende 20 seconden zodat de nieuwe Service-Principal in azure AD kan worden door gegeven. Als de wachttijd in het script te kort is, wordt de foutmelding "Principal {ID} bestaat niet in de map {DIR-ID}." weergegeven. Om deze fout op te lossen, wacht u even en voert u de opdracht **New-AzRoleAssignment** opnieuw uit.

U kunt de roltoewijzing beperken tot een specifieke resourcegroep met behulp van de parameter **ResourceGroupName**. Of tot een specifieke resource met behulp van de parameters **ResourceType** en **ResourceName**. 

Als u **niet beschikt over Windows 10 of Windows Server 2016**, downloadt u de [cmdlet New-SelfSignedCertificateEx](https://www.pkisolutions.com/tools/pspki/New-SelfSignedCertificateEx/) van PKI-oplossingen. Pak de inhoud uit en importeer de cmdlet die u nodig hebt.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

Vervang de volgende twee regels in het script om het certificaat te genereren.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Certificaat opgeven via een automatisch PowerShell-script

Wanneer u zich aanmeldt als Service-Principal, geeft u de Tenant-ID van de Directory voor uw AD-app op. Een Tenant is een exemplaar van Azure AD.

```powershell
$TenantId = (Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzADApplication -DisplayNameStartWith exampleapp).ApplicationId

$Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -eq "CN=exampleappScriptCert" }).Thumbprint
Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Service-principal met certificaat van certificeringsinstantie maken

In het volgende voorbeeld wordt een service-principal gemaakt met behulp van een certificaat dat is uitgegeven door een certificeringsinstantie. De toewijzing is afgestemd op het opgegeven Azure-abonnement. De service-principal wordt toegevoegd aan de rol van [lezer](../../role-based-access-control/built-in-roles.md#reader) . Als er een fout optreedt bij het toewijzen van de rol, wordt het opnieuw geprobeerd.

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Connect-AzAccount
 Import-Module Az.Resources
 Set-AzContext -Subscription $SubscriptionId

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }

 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Certificaat opgeven via een automatisch PowerShell-script
Wanneer u zich aanmeldt als Service-Principal, geeft u de Tenant-ID van de Directory voor uw AD-app op. Een Tenant is een exemplaar van Azure AD.

```powershell
Param (

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

De toepassings-id en tenant-id zijn geen gevoelige gegevens, dus u kunt deze rechtstreeks in uw script insluiten. Als u de tenant-id wilt ophalen, gebruikt u:

```powershell
(Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
```

Als u de toepassings-id wilt ophalen, gebruikt u:

```powershell
(Get-AzADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Referenties wijzigen

Als u de referenties voor een AD-App wilt wijzigen, gebruikt u de cmdlets [Remove-AzADAppCredential](/powershell/module/az.resources/remove-azadappcredential) en [New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential) met behulp van beveiligings problemen of verlopen.

Als u alle referenties voor een toepassing wilt verwijderen, gebruikt u:

```powershell
Get-AzADApplication -DisplayName exampleapp | Remove-AzADAppCredential
```

Als u een waarde voor een certificaat wilt toevoegen, maakt u een zelfondertekend certificaat zoals aangegeven in dit artikel. Vervolgens gebruikt u:

```powershell
Get-AzADApplication -DisplayName exampleapp | New-AzADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Fouten opsporen

Bij het maken van een service-principal kunnen de volgende foutmeldingen worden weergegeven:

* **Authentication_Unauthorized** (Verificatie geweigerd) of **No subscription found in the context.** (Geen abonnement gevonden in de context.) -U ziet deze fout wanneer uw account niet over de [vereiste machtigingen](#required-permissions) beschikt voor Azure AD om een app te registreren. Normaal gesp roken ziet u deze fout wanneer alleen gebruikers met beheerders rechten in uw Azure Active Directory apps kunnen registreren en uw account geen beheerder is. Vraag uw beheerder om u toe te wijzen aan een beheerdersrol of gebruikers in staat te stellen apps te registreren.

* Uw account heeft **geen toestemming om de actie ' micro soft. Authorization/roleAssignments/write ' over scope '/Subscriptions/{GUID} ' uit te voeren. '** : u ziet deze fout wanneer uw account niet voldoende machtigingen heeft om een rol toe te wijzen aan een identiteit. Vraag de abonnementsbeheerder om u toe te voegen aan de rol Administrator voor gebruikerstoegang.

## <a name="next-steps"></a>Volgende stappen

* Zie [Een Azure-service-principal maken met Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps) voor het instellen van een service-principal met een wachtwoord.
* Zie [Toepassingsobjecten en service-principalobjecten](app-objects-and-service-principals.md) voor meer informatie over toepassingen en service-principals.
* Zie [verificatie scenario's voor Azure AD](./authentication-vs-authorization.md)voor meer informatie over Azure AD-verificatie.
* Voor informatie over het werken met app-registraties met behulp van **Microsoft Graph**, zie de API-naslag informatie voor [toepassingen](/graph/api/resources/application) .
