---
title: Een sleutelkluis maken en configureren Azure Disk Encryption Azure AD (vorige versie)
description: In dit artikel leert u hoe u een sleutelkluis maakt en configureert voor Azure Disk Encryption met Azure AD.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: f2f301556bd24adb5e4a18f15717374ef26c400b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777883"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Een sleutelkluis maken en configureren voor Azure Disk Encryption met Azure AD (vorige versie)

**Met de nieuwe versie van Azure Disk Encryption hoeft u geen Azure AD-toepassingsparameter op te geven om VM-schijfversleuteling in te kunnenschakelen. Met de nieuwe release hoeft u geen Azure AD-referenties meer op te geven tijdens de stap Versleuteling inschakelen. Alle nieuwe VM's moeten worden versleuteld zonder de Azure AD-toepassingsparameters met behulp van de nieuwe release. Zie voor instructies voor het inschakelen van VM-schijfversleuteling met behulp van de nieuwe versie [Azure Disk Encryption.](disk-encryption-overview.md) VM's die al zijn versleuteld met Azure AD-toepassingsparameters worden nog steeds ondersteund en moeten worden onderhouden met de AAD-syntaxis.**

Azure Disk Encryption gebruikt Azure Key Vault om sleutels en geheimen voor schijfversleuteling te beheren.  Zie [Aan de slag met Azure Key Vault](../../key-vault/general/overview.md) en [Uw sleutelkluis beveiligen](../../key-vault/general/security-overview.md) voor meer informatie over sleutelkluizen. 

Het maken en configureren van een sleutelkluis voor gebruik Azure Disk Encryption met Azure AD (vorige versie) bestaat uit drie stappen:

1. Een sleutelkluis maken. 
2. Stel een Azure AD-toepassing en service-principal in.
3. Het toegangsbeleid voor de sleutelkluis voor de Azure AD-app instellen.
4. Geavanceerd toegangsbeleid voor de sleutelkluis instellen.
 
U kunt eventueel ook een sleutelversleutelingssleutel genereren of importeren (KEK).

Zie het [hoofdartikel Een sleutelkluis](disk-encryption-key-vault.md) maken en configureren voor Azure Disk Encryption voor stappen voor het installeren van hulpprogramma's [en het maken van verbinding met Azure.](disk-encryption-key-vault.md#install-tools-and-connect-to-azure)

> [!Note]
> De stappen in dit artikel zijn geautomatiseerd in het [CLI Azure Disk Encryption script](https://github.com/ejarvi/ade-cli-getting-started) met vereisten en Azure Disk Encryption [PowerShell-script.](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)


## <a name="create-a-key-vault"></a>Een sleutelkluis maken 
Azure Disk Encryption is geïntegreerd met [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) u de schijfversleutelingssleutels en -geheimen in uw key vault-abonnement kunt beheren en beheren. U kunt een sleutelkluis maken of een bestaande gebruiken voor Azure Disk Encryption. Zie [Aan de slag met Azure Key Vault](../../key-vault/general/overview.md) en [Uw sleutelkluis beveiligen](../../key-vault/general/security-overview.md) voor meer informatie over sleutelkluizen. U kunt een Resource Manager, Azure PowerShell of de Azure CLI gebruiken om een sleutelkluis te maken. 


>[!WARNING]
>Om ervoor te zorgen dat de versleutelingsgeheimen geen regionale grenzen overschrijden, moeten Azure Disk Encryption de Key Vault en de VM's zich op dezelfde locatie in dezelfde regio bevinden. Maak en gebruik een Key Vault die zich in dezelfde regio als de VM moet versleutelen. 


### <a name="create-a-key-vault-with-powershell"></a>Een sleutelkluis maken met PowerShell

U kunt een sleutelkluis maken Azure PowerShell de [cmdlet New-AzKeyVault.](/powershell/module/az.keyvault/New-azKeyVault) Zie [Az.KeyVault](/powershell/module/az.keyvault/)Key Vault aanvullende cmdlets voor meer informatie. 

1. Maak indien nodig een nieuwe resourcegroep met [New-AzResourceGroup.](/powershell/module/az.Resources/New-azResourceGroup)  Gebruik [Get-AzLocation](/powershell/module/az.resources/get-azlocation)om de locaties van het datacenter weer te geven. 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Een nieuwe sleutelkluis maken met [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Let op **de kluisnaam,** **resourcegroepnaam,** **resource-id,** **kluis-URI** en de **object-id** die worden geretourneerd voor later gebruik wanneer u de schijven versleutelt. 


### <a name="create-a-key-vault-with-azure-cli"></a>Een sleutelkluis maken met Azure CLI
U kunt uw sleutelkluis beheren met Azure CLI met behulp van [de az keyvault-opdrachten.](/cli/azure/keyvault#commands) Gebruik az keyvault create om een [sleutelkluis te maken.](/cli/azure/keyvault#az_keyvault_create)

1. Maak zo nodig een nieuwe resourcegroep met [az group create.](/cli/azure/group#az_group_create) Als u een lijst met locaties wilt maken, [gebruikt u az account list-locations](/cli/azure/account#az_account_list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Maak een nieuwe sleutelkluis [met az keyvault create.](/cli/azure/keyvault#az_keyvault_create)
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Noteer **de kluisnaam** (naam), de naam van de **resourcegroep,** **de resource-id** (id), de **kluis-URI** en de **object-id** die later worden geretourneerd voor gebruik. 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a>Een sleutelkluis maken met een Resource Manager sjabloon

U kunt een sleutelkluis maken met behulp van [Resource Manager sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Klik in de quickstart-sjabloon van Azure op **Implementeren naar Azure**.
2. Selecteer het abonnement, de resourcegroep, de locatie van de resourcegroep, Key Vault naam, object-id, juridische voorwaarden en overeenkomst en klik vervolgens op **Kopen.** 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a>Een Azure AD-app en service-principal instellen 
Wanneer u versleuteling wilt inschakelen op een VM die wordt uitgevoerd in Azure, Azure Disk Encryption en schrijft u de versleutelingssleutels naar uw sleutelkluis. Voor het beheren van versleutelingssleutels in uw sleutelkluis is Azure AD-verificatie vereist. Maak hiervoor een Azure AD-toepassing. Voor verificatiedoeleinden kunt u azure AD-verificatie op basis van clientgeheim of [azure AD-verificatie op basis van clientcertificaten gebruiken.](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a>Een Azure AD-app en service-principal instellen met Azure PowerShell 
Als u de volgende opdrachten wilt uitvoeren, gebruikt u de [Azure AD PowerShell-module](/powershell/azure/active-directory/install-adv2). 

1. Gebruik de [PowerShell-cmdlet New-AzADApplication](/powershell/module/az.resources/new-azadapplication) om een Azure AD-toepassing te maken. MyApplicationHomePage en MyApplicationUri kunnen elke wens zijn.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. De $azureAdApplication.ApplicationId is de Azure AD ClientID en de $aadClientSecret is het clientgeheim dat u later gaat gebruiken om de Azure Disk Encryption. Bewaarde het Azure AD-clientgeheim op de juiste wijze. Als `$azureAdApplication.ApplicationId` u wordt uitgevoerd, ziet u de ApplicationID.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a>Een Azure AD-app en service-principal instellen met Azure CLI

U kunt uw service-principals beheren met Azure CLI met behulp van [de opdrachten az ad sp.](/cli/azure/ad/sp) Zie Een [Azure-service-principal maken voor meer informatie.](/cli/azure/create-an-azure-service-principal-azure-cli)

1. Maak een nieuwe service-principal.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  De geretourneerde appId is de Azure AD ClientID die wordt gebruikt in andere opdrachten. Het is ook de SPN die u gebruikt voor az keyvault set-policy. Het wachtwoord is het clientgeheim dat u later moet gebruiken om het wachtwoord Azure Disk Encryption. Bewaarde het Azure AD-clientgeheim op de juiste wijze.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a>Een Azure AD-app en service-principal instellen via de Azure Portal
Gebruik de stappen in het artikel Portal gebruiken om een Azure Active Directory en [service-principal](../../active-directory/develop/howto-create-service-principal-portal.md) te maken die toegang hebben tot resources om een Azure AD-toepassing te maken. Elke stap die hieronder wordt vermeld, gaat u rechtstreeks naar de sectie met het artikel die u wilt voltooien. 

1. [Vereiste machtigingen controleren](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)
2. [Een Azure Active Directory-toepassing maken](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) 
     - U kunt elke naam en aanmeldings-URL gebruiken die u wilt gebruiken bij het maken van de toepassing.
3. [Haal de toepassings-id en de verificatiesleutel op.](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) 
     - De verificatiesleutel is het clientgeheim en wordt gebruikt als de AadClientSecret voor Set-AzVMDiskEncryptionExtension. 
        - De verificatiesleutel wordt door de toepassing gebruikt als referentie om u aan te melden bij Azure AD. In de Azure Portal dit geheim sleutels genoemd, maar heeft geen relatie met sleutelkluizen. Beveilig dit geheim op de juiste wijze. 
     - De toepassings-id wordt later gebruikt als de AadClientId voor Set-AzVMDiskEncryptionExtension en als de ServicePrincipalName voor Set-AzKeyVaultAccessPolicy. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a>Het toegangsbeleid voor de sleutelkluis voor de Azure AD-app instellen
Als u versleutelingsgeheimen wilt schrijven naar een opgegeven Key Vault, heeft Azure Disk Encryption de client-id en het clientgeheim van de Azure Active Directory-toepassing nodig die machtigingen heeft om geheimen naar de Key Vault. 

> [!NOTE]
> Azure Disk Encryption moet u het volgende toegangsbeleid voor uw Azure AD-clienttoepassing configureren: _WrapKey_ en _Set_ permissions.

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a>Stel het toegangsbeleid voor de sleutelkluis voor de Azure AD-app in met Azure PowerShell
Uw Azure AD-toepassing heeft rechten nodig voor toegang tot de sleutels of geheimen in de kluis. Gebruik de cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) om machtigingen te verlenen aan de toepassing, met behulp van de client-id (die is gegenereerd toen de toepassing werd geregistreerd) als de parameterwaarde _–ServicePrincipalName._ Zie voor meer informatie het blogbericht [Azure Key Vault - Step by Step](/archive/blogs/kv/azure-key-vault-step-by-step). 

1. Stel het toegangsbeleid voor de sleutelkluis voor de AD-toepassing in met PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a>Het toegangsbeleid voor de sleutelkluis voor de Azure AD-app instellen met Azure CLI
Gebruik [az keyvault set-policy om](/cli/azure/keyvault#az_keyvault_set_policy) het toegangsbeleid in te stellen. Zie Manage Key Vault using CLI 2.0 (Een Key Vault [cli 2.0) voor meer informatie.](../../key-vault/general/manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret)

Geef de service-principal die u hebt gemaakt via de Azure CLI toegang tot het ophalen van geheimen en het verpakken van sleutels met de volgende opdracht:

```azurecli-interactive
az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a>Het toegangsbeleid voor de sleutelkluis voor de Azure AD-app instellen met de portal

1. Open de resourcegroep met uw sleutelkluis.
2. Selecteer uw sleutelkluis, ga naar **Toegangsbeleid** en klik vervolgens **op Nieuwe toevoegen.**
3. Zoek **onder Principal selecteren** naar de Azure AD-toepassing die u hebt gemaakt en selecteer deze. 
4. Voor **Sleutelmachtigingen** raadpleegt **u Sleutel verpakken** onder **Cryptografische bewerkingen.**
5. Voor **Geheime machtigingen,** raadpleegt **u Set** onder Secret Management **Operations**.
6. Klik **op OK** om het toegangsbeleid op te slaan. 

![Azure Key Vault cryptografiebewerkingen - Sleutel verpakken](../media/disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault geheim instellen - Instellen](../media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a>Geavanceerd toegangsbeleid voor de sleutelkluis instellen
Het Azure-platform heeft toegang nodig tot de versleutelingssleutels of geheimen in uw sleutelkluis om ze beschikbaar te maken voor de VM voor het opstarten en ontsleutelen van de volumes. Schijfversleuteling inschakelen voor de sleutelkluis of implementaties mislukken.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a>Geavanceerd toegangsbeleid voor Key Vault instellen met Azure PowerShell
 Gebruik de PowerShell-cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) voor sleutelkluizen om schijfversleuteling in te schakelen voor de sleutelkluis.

  - **Key Vault inschakelen voor schijfversleuteling:** EnabledForDiskEncryption is vereist voor Azure Disk Encryption.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Key Vault inschakelen voor implementatie, indien nodig:** Hiermee kan de resourceprovider Microsoft.Compute geheimen ophalen van deze sleutelkluis wanneer er naar deze sleutelkluis wordt verwezen bij het maken van een resource, bijvoorbeeld een virtuele machine.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Key Vault inschakelen voor sjabloonimplementatie, indien nodig:** Hiermee kan Azure Resource Manager geheimen van deze sleutelkluis ophalen wanneer er naar deze sleutelkluis wordt verwezen bij een sjabloonimplementatie.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a>Geavanceerd toegangsbeleid voor Key Vault instellen met behulp van de Azure CLI
Gebruik [az keyvault update](/cli/azure/keyvault#az_keyvault_update) om schijfversleuteling in te schakelen voor de sleutelkluis. 

 - **Key Vault inschakelen voor schijfversleuteling:** Enabled-for-disk-encryption is vereist. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Schakel Key Vault in voor implementatie, indien nodig:** Sta Virtual Machines toe certificaten op te halen die zijn opgeslagen als geheimen uit de kluis.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Key Vault inschakelen voor sjabloonimplementatie, indien nodig:** Hiermee kan Resource Manager geheimen uit de kluis ophalen.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a>Geavanceerd toegangsbeleid voor Key Vault instellen via de Azure Portal

1. Selecteer uw sleutelkault, ga naar **Toegangsbeleid** en klik **om geavanceerd toegangsbeleid weer te geven.**
2. Schakel het selectievakje **Toegang tot Azure Disk Encryption voor volumeversleuteling inschakelen** in.
3. Selecteer indien nodig **Toegang tot Azure Virtual Machines inschakelen voor implementatie** en/of **Toegang tot Azure Resource Manager inschakelen voor sjabloonimplementatie**. 
4. Klik op **Opslaan**.

![Geavanceerd toegangsbeleid voor Azure Key Vault](../media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a>Een sleutelversleutelingssleutel instellen (optioneel)
Als u een Key Encryption Key (KEK) wilt gebruiken als een extra beveiligingslaag voor versleutelingssleutels, voegt u een KEK toe aan uw sleutelkluis. Gebruik de [cmdlet Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) om een sleutelversleutelingssleutel te maken in de sleutelkluis. U kunt ook een KEK importeren uit uw on-premises HSM voor sleutelbeheer. Zie onze [Key Vault-documentatie](../../key-vault/keys/hsm-protected-keys.md) voor meer informatie. Wanneer er een KEK is opgegeven, gebruikt Azure Disk Encryption die sleutel om de versleutelingsgeheimen te verpakken voordat er naar Key Vault wordt geschreven. 

* Gebruik bij het genereren van sleutels een RSA-sleuteltype. Azure Disk Encryption biedt nog geen ondersteuning voor het gebruik van Elliptic Curve-sleutels.

* Uw sleutelkluisgeheim en KEK-URL's moeten een versie hebben. Azure dwingt deze beperking van versiebeheer af. Zie de volgende voorbeelden voor geldige geheim- en KEK-URL's:

  * Voorbeeld van een geldige geheime URL:   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Voorbeeld van een geldige KEK-URL:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption biedt geen ondersteuning voor het opgeven van poortnummers als onderdeel van sleutelkluisgeheimen en KEK-URL's. Voorbeelden van niet-ondersteunde en ondersteunde sleutelkluis-URL's:

  * Onacceptabele URL van sleutelkluis  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Acceptabele URL voor sleutelkluis:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a>Een sleutelversleutelingssleutel instellen met Azure PowerShell 
Voordat u het PowerShell-script gebruikt, moet u bekend zijn met de Azure Disk Encryption om inzicht te krijgen in de stappen in het script. Het voorbeeldscript moet mogelijk worden gewijzigd voor uw omgeving. Dit script maakt alle Azure Disk Encryption en versleutelt een bestaande IaaS-VM, en verpakt de schijfversleutelingssleutel met behulp van een sleutelversleutelingssleutel. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="certificate-based-authentication-optional"></a>Verificatie op basis van certificaten (optioneel)
Als u certificaatverificatie wilt gebruiken, kunt u er een uploaden naar uw sleutelkluis en deze implementeren naar de client. Voordat u het PowerShell-script gebruikt, moet u bekend zijn met de Azure Disk Encryption om inzicht te krijgen in de stappen in het script. Het voorbeeldscript moet mogelijk worden gewijzigd voor uw omgeving.

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="certificate-based-authentication-and-a-kek-optional"></a>Verificatie op basis van certificaten en een KEK (optioneel)

Als u certificaatverificatie wilt gebruiken en de versleutelingssleutel wilt verpakken met een KEK, kunt u het onderstaande script als voorbeeld gebruiken. Voordat u het PowerShell-script gebruikt, moet u bekend zijn met alle voorgaande Azure Disk Encryption om inzicht te krijgen in de stappen in het script. Het voorbeeldscript moet mogelijk worden gewijzigd voor uw omgeving.

     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>Volgende stappen

[Een Azure Disk Encryption azure ad inschakelen op Virtuele Windows-VM's (vorige versie)](disk-encryption-windows-aad.md)
