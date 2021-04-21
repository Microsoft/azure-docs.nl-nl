---
title: Verificatie AD DS Azure-bestands shares inschakelen
description: Meer informatie over het inschakelen Active Directory Domain Services verificatie via SMB voor Azure-bestands shares. Uw virtuele Windows-machines die lid zijn van een domein, hebben vervolgens toegang tot Azure-bestands shares met behulp AD DS referenties.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/13/2020
ms.author: rogarana
ms.openlocfilehash: f38911b1fffb083902ba67e262141b6780a43ada
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817842"
---
# <a name="part-one-enable-ad-ds-authentication-for-your-azure-file-shares"></a>Deel 1: verificatie AD DS azure-bestands shares inschakelen 

Voordat u verificatie Active Directory Domain Services (AD DS) inschakelen, moet u het [](storage-files-identity-auth-active-directory-enable.md) overzichtsartikel lezen om inzicht te krijgen in de ondersteunde scenario's en vereisten.

In dit artikel wordt het proces beschreven dat is vereist Active Directory Domain Services verificatie (AD DS) in uw opslagaccount in te schakelen. Nadat u de functie hebt inschakelen, moet u uw opslagaccount en uw AD DS configureren om AD DS te gebruiken voor de authenticatie bij uw Azure-bestands share. Als u AD DS via SMB wilt inschakelen voor Azure-bestands shares, moet u uw opslagaccount registreren bij AD DS en vervolgens de vereiste domeineigenschappen instellen voor het opslagaccount.

Als u uw opslagaccount wilt registreren bij AD DS, maakt u een account dat het in uw AD DS. U kunt dit proces zien alsof het lijkt op het maken van een account dat een on-premises Windows-bestandsserver in uw AD DS. Wanneer de functie is ingeschakeld voor het opslagaccount, is deze van toepassing op alle nieuwe en bestaande bestands shares in het account.

## <a name="option-one-recommended-use-azfileshybrid-powershell-module"></a>Optie 1 (aanbevolen): AzFilesHybrid PowerShell-module gebruiken

De cmdlets in de PowerShell-module AzFilesHybrid brengen de benodigde wijzigingen aan en maken de functie voor u mogelijk. Omdat sommige onderdelen van de cmdlets communiceren met uw on-premises AD DS, wordt uitgelegd wat de cmdlets doen, zodat u kunt bepalen of de wijzigingen zijn afgestemd op uw nalevings- en beveiligingsbeleid en dat u de juiste machtigingen hebt om de cmdlets uit te voeren. Hoewel we het gebruik van de module AzFilesHybrid aanraden, bieden we de stappen als u dit niet kunt doen, zodat u deze handmatig kunt uitvoeren.

### <a name="download-azfileshybrid-module"></a>AzFilesHybrid-module downloaden

- [Download de AzFilesHybrid-module (ga-module: v0.2.0+)](https://github.com/Azure-Samples/azure-files-samples/releases) en los Houd er rekening mee dat AES 256 Kerberos-versleuteling wordt ondersteund op v0.2.2 of hoger. Als u de functie hebt ingeschakeld met een AzFilesHybrid-versie lager dan v0.2.2 en u wilt bijwerken ter ondersteuning van AES 256 Kerberos-versleuteling, raadpleegt u dit [artikel](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption). 
- Installeer en voer de module uit op een apparaat dat lid is van een on-premises AD DS met AD DS-referenties die zijn machtigingen voor het maken van een service-aanmeldingsaccount of een computeraccount in de doel-AD.
-  Voer het script uit met behulp van een on-premises AD DS die is gesynchroniseerd met uw Azure AD. De on-premises AD DS moeten de eigenaar van het opslagaccount of de Azure-rolmachtigingen inzender hebben.

### <a name="run-join-azstorageaccountforauth"></a>Voer Join-AzStorageAccountForAuth

De `Join-AzStorageAccountForAuth` cmdlet voert het equivalent van een offline domein-join uit namens het opgegeven opslagaccount. Het script gebruikt de cmdlet om een [computeraccount in uw](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) AD-domein te maken. Als u om welke reden dan ook geen computeraccount kunt gebruiken, kunt u het script wijzigen om in plaats daarvan een [aanmeldingsaccount voor de service te](/windows/win32/ad/about-service-logon-accounts) maken. Als u ervoor kiest om de opdracht handmatig uit te voeren, moet u het account selecteren dat het meest geschikt is voor uw omgeving.

Het AD DS dat door de cmdlet is gemaakt, vertegenwoordigt het opslagaccount. Als het AD DS is gemaakt onder een organisatie-eenheid (OE) die afdwingt dat het wachtwoord verloopt, moet u het wachtwoord bijwerken vóór de maximale wachtwoordleeftijd. Als het accountwachtwoord niet vóór die datum wordt bijgewerkt, leidt dit tot verificatiefouten bij het openen van Azure-bestands shares. Zie Wachtwoord bijwerken AD DS voor meer informatie over het bijwerken [van het wachtwoord.](storage-files-identity-ad-ds-update-password.md)

Vervang de waarden van de tijdelijke aanduiding door uw eigen waarden in de onderstaande parameters voordat u deze in PowerShell gaat uitvoeren.
> [!IMPORTANT]
> De cmdlet voor domeindeelvoeging maakt een AD-account dat het opslagaccount (bestands share) in AD vertegenwoordigt. U kunt ervoor kiezen om u te registreren als een computeraccount of servicelogoaccount. Zie [Veelgestelde vragen](./storage-files-faq.md#security-authentication-and-access-control) voor meer informatie. Voor computeraccounts is er een standaardverlooptijd voor wachtwoorden ingesteld in AD op 30 dagen. Op dezelfde manier kan voor het aanmeldingsaccount van de service een standaardverlooptijd voor het wachtwoord zijn ingesteld voor het AD-domein of de organisatie-eenheid (OE).
> Voor beide accounttypen raden we u aan de vervaldatum van het [](storage-files-identity-ad-ds-update-password.md) wachtwoord die is geconfigureerd in uw AD-omgeving te controleren en het wachtwoord van uw opslagaccount-id van het AD-account bij te werken vóór de maximale wachtwoordleeftijd. U kunt overwegen om [een nieuwe organisatie-eenheid (OE)](/powershell/module/addsadministration/new-adorganizationalunit) van AD te maken en het verloopbeleid voor wachtwoorden voor [computeraccounts](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)) of servicelogoaccounts dienovereenkomstig uit te stellen. 

```PowerShell
# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

# Login with an Azure AD credential that has either storage account owner or contributer Azure role assignment
# If you are logging into an Azure environment other than Public (ex. AzureUSGovernment) you will need to specify that.
# See https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-ps
# for more information.
Connect-AzAccount

# Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

# Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account (default parameter value), depends on the AD permission you have and preference. 
# Run Get-Help Join-AzStorageAccountForAuth for more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -StorageAccountName $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" <# Default is set as ComputerAccount #> `
        -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" <# If you don't provide the OU name as an input parameter, the AD identity that represents the storage account is created under the root directory. #> `
        -EncryptionType "<AES256/RC4/AES256,RC4>" <# Specify the encryption agorithm used for Kerberos authentication. Default is configured as "'RC4','AES256'" which supports both 'RC4' and 'AES256' encryption. #>

#Run the command below if you want to enable AES 256 authentication. If you plan to use RC4, you can skip this step.
Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, see Azure Files Windows troubleshooting guide.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="option-2-manually-perform-the-enablement-actions"></a>Optie 2: de inschakelenacties handmatig uitvoeren

Als u het bovenstaande script al hebt uitgevoerd, gaat u naar de sectie `Join-AzStorageAccountForAuth` Bevestigen dat de functie is [ingeschakeld.](#confirm-the-feature-is-enabled) U hoeft de volgende handmatige stappen niet uit te voeren.

### <a name="checking-environment"></a>Omgeving controleren

Eerst moet u de status van uw omgeving controleren. U moet met name controleren of [Active Directory PowerShell](/powershell/module/addsadministration/) is geïnstalleerd en of de shell wordt uitgevoerd met beheerdersbevoegdheden. Controleer vervolgens of de [Az.Storage 2.0-module](https://www.powershellgallery.com/packages/Az.Storage/2.0.0) is geïnstalleerd, en installeer deze als dit niet het geval is. Nadat u deze controles hebt uitgevoerd, controleert u uw AD DS om te zien of er een [computeraccount](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (standaard) of [een servicelogoaccount](/windows/win32/ad/about-service-logon-accounts) is dat al is gemaakt met SPN/UPN als 'cifs/your-storage-account-name-here.file.core.windows.net'. Als het account niet bestaat, maakt u er een zoals beschreven in de volgende sectie.

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>Handmatig een identiteit maken die het opslagaccount in uw AD vertegenwoordigt

Als u dit account handmatig wilt maken, maakt u een nieuwe Kerberos-sleutel voor uw opslagaccount. Gebruik vervolgens die Kerberos-sleutel als het wachtwoord voor uw account met de onderstaande PowerShell-cmdlets. Deze sleutel wordt alleen gebruikt tijdens de installatie en kan niet worden gebruikt voor besturings- of gegevensvlakbewerkingen voor het opslagaccount. 

```PowerShell
# Create the Kerberos key on the storage account and get the Kerb1 key as the password for the AD identity to represent the storage account
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName kerb1
Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -ListKerbKey | where-object{$_.Keyname -contains "kerb1"}
```

Zodra u die sleutel hebt, maakt u een service- of computeraccount onder uw OE. Gebruik de volgende specificatie (vergeet niet om de voorbeeldtekst te vervangen door de naam van uw opslagaccount):

SPN: 'cifs/your-storage-account-name-here.file.core.windows.net' Wachtwoord: Kerberos-sleutel voor uw opslagaccount.

Als uw OE afdwingt dat het wachtwoord verloopt, moet u het wachtwoord bijwerken vóór de maximale wachtwoordleeftijd om verificatiefouten bij het openen van Azure-bestands shares te voorkomen. Zie [Het wachtwoord van uw opslagaccount-id bijwerken in AD](storage-files-identity-ad-ds-update-password.md) voor meer informatie.

Bewaar de SID van de zojuist gemaakte identiteit. U hebt deze nodig voor de volgende stap. De identiteit die u hebt gemaakt voor het opslagaccount, hoeft niet te worden gesynchroniseerd met Azure AD.

### <a name="enable-the-feature-on-your-storage-account"></a>De functie inschakelen voor uw opslagaccount

U kunt de functie nu inschakelen voor uw opslagaccount. Geef in de volgende opdracht enkele configuratiedetails voor de domeineigenschappen op en voer deze uit. De opslagaccount-SID die is vereist in de volgende opdracht is de SID van de identiteit die u hebt gemaakt in uw AD DS in [de vorige sectie](#creating-an-identity-representing-the-storage-account-in-your-ad-manually).

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```

### <a name="debugging"></a>Foutopsporing

U kunt de cmdlet Debug-AzStorageAccountAuth uitvoeren om een set basiscontroles uit te voeren op uw AD-configuratie met de aangemelde AD-gebruiker. Deze cmdlet wordt ondersteund met versie AzFilesHybrid v 0.1.2 en hoger. Zie Unable to mount Azure Files with AD [credentials](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) in de probleemoplossingsgids voor Windows voor meer informatie over de controles die in deze cmdlet worden uitgevoerd.

```PowerShell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="confirm-the-feature-is-enabled"></a>Bevestig dat de functie is ingeschakeld

U kunt controleren of de functie is ingeschakeld voor uw opslagaccount met het volgende script:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Als dit lukt, moet de uitvoer er als volgende uitzien:

```PowerShell
DomainName:<yourDomainHere>
NetBiosDomainName:<yourNetBiosDomainNameHere>
ForestName:<yourForestNameHere>
DomainGuid:<yourGUIDHere>
DomainSid:<yourSIDHere>
AzureStorageID:<yourStorageSIDHere>
```
## <a name="next-steps"></a>Volgende stappen

U hebt de functie nu ingeschakeld voor uw opslagaccount. Als u de functie wilt gebruiken, moet u machtigingen op shareniveau toewijzen. Ga door naar de volgende sectie.

[Deel 2: machtigingen op shareniveau toewijzen aan een identiteit](storage-files-identity-ad-ds-assign-permissions.md)