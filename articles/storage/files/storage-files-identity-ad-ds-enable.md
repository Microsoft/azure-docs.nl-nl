---
title: AD DS-verificatie inschakelen voor Azure-bestands shares
description: Meer informatie over het inschakelen van Active Directory Domain Services verificatie via SMB voor Azure-bestands shares. Uw virtuele Windows-machines die lid zijn van een domein, kunnen vervolgens toegang krijgen tot Azure-bestands shares met behulp van AD DS referenties.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/13/2020
ms.author: rogarana
ms.openlocfilehash: 5ee4481b3151e28d5d37760e486a43adbc194994
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553218"
---
# <a name="part-one-enable-ad-ds-authentication-for-your-azure-file-shares"></a>Deel 1: Schakel AD DS verificatie in voor uw Azure-bestands shares 

Voordat u Active Directory Domain Services (AD DS)-verificatie inschakelt, moet u het [overzichts artikel](storage-files-identity-auth-active-directory-enable.md) lezen om inzicht te krijgen in de ondersteunde scenario's en vereisten.

In dit artikel wordt het proces beschreven dat vereist is voor het inschakelen van Active Directory Domain Services-verificatie (AD DS) voor uw opslag account. Nadat u de functie hebt ingeschakeld, moet u uw opslag account en uw AD DS configureren om AD DS referenties te gebruiken voor de verificatie van uw Azure-bestands share. Als u AD DS verificatie via SMB voor Azure-bestands shares wilt inschakelen, moet u uw opslag account registreren bij AD DS en vervolgens de vereiste domein eigenschappen instellen voor het opslag account.

Als u uw opslag account met AD DS wilt registreren, maakt u een account in uw AD DS. U kunt dit proces nadenken alsof het een account is dat een on-premises Windows-Bestands server in uw AD DS aanduidt. Wanneer de functie is ingeschakeld op het opslag account, is dit van toepassing op alle nieuwe en bestaande bestands shares in het account.

## <a name="option-one-recommended-use-azfileshybrid-powershell-module"></a>Optie 1 (aanbevolen): gebruik de Power shell-module AzFilesHybrid

Met de cmdlets in de AzFilesHybrid Power shell-module worden de nodige wijzigingen aangebracht en wordt de functie voor u ingeschakeld. Omdat sommige onderdelen van de cmdlets werken met uw on-premises AD DS, wordt uitgelegd wat de cmdlets doen, zodat u kunt bepalen of de wijzigingen worden uitgelijnd met uw nalevings-en beveiligings beleid en ervoor te zorgen dat u over de juiste machtigingen beschikt om de cmdlets uit te voeren. We raden u aan om de AzFilesHybrid-module te gebruiken. Als u dit niet kunt doen, geeft u de stappen op zodat u deze hand matig kunt uitvoeren.

### <a name="download-azfileshybrid-module"></a>AzFilesHybrid-module downloaden

- [De AzFilesHybrid-module downloaden en uitpakken (Ga naar module: v 0.2.0 +)](https://github.com/Azure-Samples/azure-files-samples/releases) Houd er rekening mee dat AES 256 Kerberos-versleuteling wordt ondersteund op v 0.2.2 of hoger. Als u de functie hebt ingeschakeld met een AzFilesHybrid-versie lager dan v 0.2.2 en u wilt bijwerken om AES 256 Kerberos-versleuteling te ondersteunen, raadpleegt u [dit artikel](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption). 
- Installeer en voer de module uit in een domein dat is gekoppeld aan een on-premises AD DS met AD DS referenties die machtigingen hebben voor het maken van een account voor service aanmelding of een computer account in de doel-AD.
-  Voer het script uit met behulp van een on-premises AD DS referentie die is gesynchroniseerd met uw Azure AD. De referenties van de on-premises AD DS moeten de eigenaar van het opslag account of de Azure-rol van de Inzender hebben.

### <a name="run-join-azstorageaccountforauth"></a>Join-AzStorageAccountForAuth uitvoeren

De `Join-AzStorageAccountForAuth` cmdlet voert het equivalent van een offline domein deelname uit namens het opgegeven opslag account uit. Het script maakt gebruik van de cmdlet voor het maken van een [computer account](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) in uw AD-domein. Als u om welke reden dan ook geen computer account kunt gebruiken, kunt u het script wijzigen om in plaats daarvan een [service-aanmeldings account](/windows/win32/ad/about-service-logon-accounts) te maken. Als u ervoor kiest om de opdracht hand matig uit te voeren, moet u het account selecteren dat het meest geschikt is voor uw omgeving.

Het AD DS-account dat is gemaakt door de cmdlet vertegenwoordigt het opslag account. Als het AD DS-account wordt gemaakt onder een organisatie-eenheid (OE) die het verlopen van wacht woorden afdwingt, moet u het wacht woord bijwerken vóór de maximale wachtwoord duur. Het bijwerken van het wacht woord van het account vóór die datum resulteert in verificatie fouten bij het openen van Azure-bestands shares. Zie [AD DS account wachtwoord bijwerken](storage-files-identity-ad-ds-update-password.md)voor meer informatie over het bijwerken van het wacht woord.

Vervang de tijdelijke aanduidingen door uw eigen waarden in de onderstaande para meters voordat u deze in Power shell uitvoert.
> [!IMPORTANT]
> Met de cmdlet domein toevoegen wordt een AD-account gemaakt dat het opslag account (bestands share) in AD vertegenwoordigt. Zie [Veelgestelde vragen](./storage-files-faq.md#security-authentication-and-access-control) voor meer informatie over het registreren van een computer account of een service-aanmeldings account. Voor computer accounts is er een standaard leeftijd voor wachtwoord verloop ingesteld in AD om 30 dagen. Op dezelfde manier kan voor het service-aanmeldings account een standaard leeftijd voor het wacht woord voor het verlopen van het AD-domein of de organisatie-eenheid (OE) zijn ingesteld.
> Voor beide account typen raden we u aan de leeftijd van het wacht woord te controleren dat in uw AD-omgeving is geconfigureerd en om [het wacht woord van uw opslag account-id](storage-files-identity-ad-ds-update-password.md) van het ad-account bij te werken vóór de maximale wachtwoord duur. U kunt overwegen om [een nieuwe AD-organisatie-eenheid (OE) in AD te maken](/powershell/module/addsadministration/new-adorganizationalunit) en het verloop beleid voor wacht woorden op [computer accounts](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)) of service-aanmeldings accounts dienovereenkomstig uit te scha kelen. 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer Azure role assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
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

## <a name="option-2-manually-perform-the-enablement-actions"></a>Optie 2: de activerings acties hand matig uitvoeren

Als u het bovenstaande script al hebt uitgevoerd `Join-AzStorageAccountForAuth` , gaat u naar de sectie [controleren of de functie is ingeschakeld](#confirm-the-feature-is-enabled) . U hoeft de volgende hand matige stappen niet uit te voeren.

### <a name="checking-environment"></a>Omgeving controleren

Eerst moet u de status van uw omgeving controleren. U moet in het bijzonder controleren of [Active Directory Power shell](/powershell/module/addsadministration/) is geïnstalleerd en of de shell wordt uitgevoerd met beheerders bevoegdheden. Controleer vervolgens of de [Az.Storage 2.0-module](https://www.powershellgallery.com/packages/Az.Storage/2.0.0) is geïnstalleerd, en installeer deze als dit niet het geval is. Nadat u deze controles hebt voltooid, controleert u uw AD DS om te zien of er een [computer account](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (standaard) of een [service aanmeldings account](/windows/win32/ad/about-service-logon-accounts) is dat al is gemaakt met SPN/UPN als CIFS/uw-Storage-account-name. file. core. Windows. net. Als het account niet bestaat, maakt u er een zoals beschreven in de volgende sectie.

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>Een identiteit maken die het opslag account in uw AD hand matig weergeeft

Als u dit account hand matig wilt maken, maakt u een nieuwe Kerberos-sleutel voor uw opslag account. Gebruik vervolgens die Kerberos-sleutel als wacht woord voor uw account met de onderstaande Power shell-cmdlets. Deze sleutel wordt alleen gebruikt tijdens de installatie en kan niet worden gebruikt voor beheer-of gegevenslaag bewerkingen voor het opslag account. 

```PowerShell
# Create the Kerberos key on the storage account and get the Kerb1 key as the password for the AD identity to represent the storage account
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName kerb1
Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -ListKerbKey | where-object{$_.Keyname -contains "kerb1"}
```

Wanneer u deze sleutel hebt, moet u een service-of computer account maken onder uw organisatie-eenheid. Gebruik de volgende specificatie (Vergeet niet om de voorbeeld tekst te vervangen door de naam van uw opslag account):

SPN: ' CIFS/uw-Storage-account-name-hier. file. core. Windows. net ' wacht woord: Kerberos-sleutel voor uw opslag account.

Als uw organisatie-eenheid het wacht woord verloopt, moet u het wacht woord bijwerken vóór de maximale wachtwoord duur om verificatie fouten te voor komen bij het openen van Azure-bestands shares. Zie [het wacht woord voor de identiteit van uw opslag account bijwerken in AD](storage-files-identity-ad-ds-update-password.md) voor meer informatie.

Behoud de SID van de zojuist gemaakte identiteit. u hebt deze nodig voor de volgende stap. De identiteit die u hebt gemaakt die het opslag account vertegenwoordigt, hoeft niet te worden gesynchroniseerd met Azure AD.

### <a name="enable-the-feature-on-your-storage-account"></a>De functie op uw opslag account inschakelen

Nu kunt u de functie inschakelen in uw opslag account. Geef enkele configuratie details op voor de domein eigenschappen in de volgende opdracht en voer deze uit. De SID van het opslag account die is vereist in de volgende opdracht is de SID van de identiteit die u hebt gemaakt in uw AD DS in [de vorige sectie](#creating-an-identity-representing-the-storage-account-in-your-ad-manually).

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

U kunt de Debug-AzStorageAccountAuth cmdlet uitvoeren om een set basis controles uit te voeren op uw AD-configuratie met de aangemelde AD-gebruiker. Deze cmdlet wordt ondersteund met versie AzFilesHybrid v 0.1.2 en hoger. Zie voor meer informatie over de controles die worden uitgevoerd in deze cmdlet [kan geen Azure files koppelen aan AD-referenties](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) in de gids voor probleem oplossing voor Windows.

```PowerShell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="confirm-the-feature-is-enabled"></a>Controleren of de functie is ingeschakeld

U kunt controleren of de functie is ingeschakeld in uw opslag account met het volgende script:

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

Als dit lukt, ziet de uitvoer er als volgt uit:

```PowerShell
DomainName:<yourDomainHere>
NetBiosDomainName:<yourNetBiosDomainNameHere>
ForestName:<yourForestNameHere>
DomainGuid:<yourGUIDHere>
DomainSid:<yourSIDHere>
AzureStorageID:<yourStorageSIDHere>
```
## <a name="next-steps"></a>Volgende stappen

De functie is nu ingeschakeld voor uw opslag account. Als u de functie wilt gebruiken, moet u machtigingen op share niveau toewijzen. Ga door naar de volgende sectie.

[Deel twee: machtigingen op share niveau toewijzen aan een identiteit](storage-files-identity-ad-ds-assign-permissions.md)