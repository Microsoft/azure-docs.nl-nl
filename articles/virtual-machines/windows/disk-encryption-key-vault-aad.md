---
title: Een sleutel kluis maken en configureren voor Azure Disk Encryption met Azure AD (vorige versie)
description: In dit artikel vindt u informatie over het maken en configureren van een sleutel kluis voor Azure Disk Encryption met Azure AD.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 878828a40f7a14fe8bb5728ac06585e6aca2eec8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555479"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Een sleutel kluis maken en configureren voor Azure Disk Encryption met Azure AD (vorige versie)

**De nieuwe versie van Azure Disk Encryption elimineert de vereiste voor het opgeven van een Azure AD-toepassings parameter om VM-schijf versleuteling in te scha kelen. Met de nieuwe versie hoeft u geen Azure AD-referenties meer op te geven tijdens het inschakelen van de versleutelings stap. Alle nieuwe Vm's moeten worden versleuteld zonder de para meters van de Azure AD-toepassing met de nieuwe versie. Zie [Azure Disk Encryption](disk-encryption-overview.md)als u instructies wilt weer geven voor het inschakelen van VM-schijf versleuteling met de nieuwe versie. Vm's die al zijn versleuteld met Azure AD-toepassings parameters, worden nog steeds ondersteund en blijven behouden met de AAD-syntaxis.**

Azure Disk Encryption gebruikt Azure Key Vault om sleutels en geheimen voor schijfversleuteling te beheren.  Zie [Aan de slag met Azure Key Vault](../../key-vault/general/overview.md) en [Uw sleutelkluis beveiligen](../../key-vault/general/secure-your-key-vault.md) voor meer informatie over sleutelkluizen. 

Een sleutel kluis maken en configureren voor gebruik Azure Disk Encryption met Azure AD (eerdere versie) omvat drie stappen:

1. Een sleutelkluis maken. 
2. Stel een Azure AD-toepassing en Service-Principal in.
3. Het toegangsbeleid voor de sleutelkluis voor de Azure AD-app instellen.
4. Geavanceerd toegangsbeleid voor de sleutelkluis instellen.
 
U kunt eventueel ook een sleutelversleutelingssleutel genereren of importeren (KEK).

Zie de belangrijkste procedure [voor het maken en configureren van een sleutel kluis voor Azure Disk Encryption](disk-encryption-key-vault.md) artikel voor meer informatie over het [installeren van hulpprogram ma's en het maken van verbinding met Azure](disk-encryption-key-vault.md#install-tools-and-connect-to-azure).

> [!Note]
> De stappen in dit artikel zijn geautomatiseerd in het [Azure Disk Encryption vereisten CLI-script](https://github.com/ejarvi/ade-cli-getting-started) en [Azure Disk Encryption vereisten Power shell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).


## <a name="create-a-key-vault"></a>Een sleutelkluis maken 
Azure Disk Encryption is geïntegreerd met [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) , zodat u de schijf versleutelings sleutels en geheimen in uw sleutel kluis abonnement kunt controleren en beheren. U kunt een sleutel kluis maken of een bestaande gebruiken voor Azure Disk Encryption. Zie [Aan de slag met Azure Key Vault](../../key-vault/general/overview.md) en [Uw sleutelkluis beveiligen](../../key-vault/general/secure-your-key-vault.md) voor meer informatie over sleutelkluizen. U kunt een resource manager-sjabloon, Azure PowerShell of de Azure CLI gebruiken om een sleutel kluis te maken. 


>[!WARNING]
>Om ervoor te zorgen dat de versleutelings geheimen geen regionale grenzen overschrijden, moeten Azure Disk Encryption de Key Vault en de virtuele machines in dezelfde regio naast elkaar hebben. Maak en gebruik een Key Vault dat zich in dezelfde regio bevindt als de virtuele machine die moet worden versleuteld. 


### <a name="create-a-key-vault-with-powershell"></a>Een sleutel kluis maken met Power shell

U kunt een sleutel kluis maken met Azure PowerShell met behulp van de cmdlet [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) . Zie [AZ. sleutel kluis](/powershell/module/az.keyvault/)voor extra cmdlets voor Key Vault. 

1. Maak indien nodig een nieuwe resource groep met [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Gebruik [Get-AzLocation](/powershell/module/az.resources/get-azlocation)om de locaties van data centers weer te geven. 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Een nieuwe sleutel kluis maken met [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Noteer de **naam** van de kluis, de naam van de **resource groep**, de **resource-id**, de **kluis-URI** en de **object-id** die wordt geretourneerd voor later gebruik wanneer u de schijven versleutelt. 


### <a name="create-a-key-vault-with-azure-cli"></a>Een sleutel kluis maken met Azure CLI
U kunt uw sleutel kluis beheren met Azure CLI met behulp van de opdracht [AZ-kluis](/cli/azure/keyvault#commands) . Gebruik [AZ Key kluis Create](/cli/azure/keyvault#az-keyvault-create)om een sleutel kluis te maken.

1. Maak indien nodig een nieuwe resource groep met [AZ Group Create](/cli/azure/group#az-group-create). Gebruik [AZ account list-locations](/cli/azure/account#az-account-list) om locaties weer te geven 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Maak een nieuwe sleutel kluis met [AZ Key kluis Create](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Noteer de naam van de **kluis** (naam), de naam van de **resource groep**, de **resource-id** (id), de **kluis-URI** en de **object-id** die wordt geretourneerd voor later gebruik. 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a>Een sleutel kluis maken met een resource manager-sjabloon

U kunt een sleutel kluis maken met behulp van de [Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Klik in de quickstart-sjabloon van Azure op **Implementeren naar Azure**.
2. Selecteer het abonnement, de resource groep, de locatie van de resource groep, de Key Vault naam, de object-ID, de juridische voor waarden en de overeenkomst en klik vervolgens op **kopen**. 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a>Een Azure AD-app en service-principal instellen 
Wanneer versleuteling moet worden ingeschakeld op een actieve virtuele machine in azure, Azure Disk Encryption genereert en schrijft de versleutelings sleutels naar uw sleutel kluis. Voor het beheren van versleutelings sleutels in uw sleutel kluis is Azure AD-verificatie vereist. Maak een Azure AD-toepassing voor dit doel einde. Voor verificatie doeleinden kunt u verificatie op basis van client geheim of [Azure AD-verificatie op basis van client certificaten](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)gebruiken.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a>Een Azure AD-app en-service-principal instellen met Azure PowerShell 
Als u de volgende opdrachten wilt uitvoeren, moet u de [Azure AD Power shell-module](/powershell/azure/active-directory/install-adv2)ophalen en gebruiken. 

1. Gebruik de Power shell [-cmdlet New-AzADApplication](/powershell/module/az.resources/new-azadapplication) om een Azure AD-toepassing te maken. MyApplicationHomePage en MyApplicationUri kunnen wille keurige waarden zijn.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. De $azureAdApplication. ApplicationId is de Azure AD ClientID en de $aadClientSecret is het client geheim dat u later gaat gebruiken om Azure Disk Encryption in te scha kelen. Beveilig het Azure AD-client geheim op de juiste manier. Met `$azureAdApplication.ApplicationId` wordt de ApplicationID weer gegeven.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a>Een Azure AD-app en-service-principal instellen met Azure CLI

U kunt de service-principals beheren met Azure CLI met behulp van de [AZ AD SP](/cli/azure/ad/sp) -opdrachten. Zie [een Azure-service-principal maken](/cli/azure/create-an-azure-service-principal-azure-cli)voor meer informatie.

1. Maak een nieuwe service-principal.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  De geretourneerde appId is de Azure AD-ClientID die wordt gebruikt in andere opdrachten. Het is ook de SPN die u gebruikt voor AZ set-Policy. Het wacht woord is het client geheim dat u later moet gebruiken om Azure Disk Encryption in te scha kelen. Beveilig het Azure AD-client geheim op de juiste manier.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a>Een Azure AD-app en service-principal instellen, hoewel de Azure Portal
Gebruik de stappen in de [Portal gebruiken om een Azure Active Directory-toepassing en Service-Principal te maken die toegang hebben tot bronnen](../../active-directory/develop/howto-create-service-principal-portal.md) artikelen om een Azure AD-toepassing te maken. In elke stap die hieronder wordt vermeld, gaat u rechtstreeks naar de sectie artikel om dit te volt ooien. 

1. [Vereiste machtigingen verifiëren](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)
2. [Een Azure Active Directory-toepassing maken](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) 
     - U kunt elke naam en aanmeldings-URL gebruiken die u wilt bij het maken van de toepassing.
3. [Haal de toepassings-id en de verificatie sleutel](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)op. 
     - De verificatie sleutel is het client geheim en wordt gebruikt als de AadClientSecret voor set-AzVMDiskEncryptionExtension. 
        - De verificatie sleutel wordt door de toepassing gebruikt als referentie om zich aan te melden bij Azure AD. In de Azure Portal heet dit geheim sleutels, maar het heeft geen betrekking op sleutel kluizen. Beveilig dit geheim op de juiste manier. 
     - De toepassings-ID wordt later gebruikt als AadClientId voor Set-AzVMDiskEncryptionExtension en als ServicePrincipalName voor set-AzKeyVaultAccessPolicy. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a>Het toegangsbeleid voor de sleutelkluis voor de Azure AD-app instellen
Om versleutelings geheimen te schrijven naar een opgegeven Key Vault, heeft Azure Disk Encryption de client-ID en het client geheim nodig van de Azure Active Directory toepassing die machtigingen heeft om geheimen naar de Key Vault te schrijven. 

> [!NOTE]
> Azure Disk Encryption moet u het volgende toegangs beleid configureren voor uw Azure AD-client toepassing: _WrapKey_ en machtigingen _instellen_ .

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a>Stel het toegangs beleid voor de sleutel kluis in voor de Azure AD-app met Azure PowerShell
Uw Azure AD-toepassing heeft rechten nodig om toegang te krijgen tot de sleutels of geheimen in de kluis. Gebruik de cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) om machtigingen toe te kennen aan de toepassing, met behulp van de client-id (die is gegenereerd toen de toepassing is geregistreerd) als de parameter waarde _– ServicePrincipalName_ . Zie voor meer informatie het blog bericht [Azure Key Vault-stap voor stap](/archive/blogs/kv/azure-key-vault-step-by-step). 

1. Stel het toegangs beleid voor de sleutel kluis in voor de AD-toepassing met Power shell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a>Het toegangs beleid voor de sleutel kluis instellen voor de Azure AD-app met Azure CLI
Gebruik [AZ sleutel kluis set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) om het toegangs beleid in te stellen. Zie [Key Vault beheren met CLI 2,0](../../key-vault/general/manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret)voor meer informatie.

Geef de service-principal die u hebt gemaakt via de Azure CLI-toegang om geheimen en terugloop sleutels op te halen met de volgende opdracht:

```azurecli-interactive
az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a>Het toegangs beleid voor de sleutel kluis instellen voor de Azure AD-app met de portal

1. Open de resource groep met uw sleutel kluis.
2. Selecteer uw sleutel kluis, ga naar **toegangs beleid** en klik vervolgens op **nieuwe toevoegen**.
3. Zoek onder **Principal selecteren** naar de Azure AD-toepassing die u hebt gemaakt en selecteer deze. 
4. Voor **sleutel machtigingen** controleert u de **toets SHIFT** onder **cryptografische bewerkingen**.
5. Controleer bij **geheime machtigingen** **instellen** onder **geheime beheer bewerkingen**.
6. Klik op **OK** om het toegangs beleid op te slaan. 

![Azure Key Vault cryptografische bewerkingen-terugloop sleutel](../media/disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault geheime machtigingen: instellen](../media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a>Geavanceerd toegangsbeleid voor de sleutelkluis instellen
Het Azure-platform heeft toegang nodig tot de versleutelingssleutels of geheimen in uw sleutelkluis om ze beschikbaar te maken voor de VM voor het opstarten en ontsleutelen van de volumes. Het inschakelen van schijf versleuteling op de sleutel kluis of implementaties mislukt.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a>Geavanceerd toegangs beleid voor sleutel kluis instellen met Azure PowerShell
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

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a>Geavanceerd toegangs beleid voor sleutel kluis instellen met behulp van Azure CLI
Gebruik [az keyvault update](/cli/azure/keyvault#az-keyvault-update) om schijfversleuteling in te schakelen voor de sleutelkluis. 

 - **Key Vault inschakelen voor schijfversleuteling:** Enabled-for-disk-encryption is vereist. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Schakel Key Vault in voor implementatie, indien nodig:** Hiermee staat u toe dat Virtual Machines certificaten kunt ophalen die zijn opgeslagen als geheimen van de kluis.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Key Vault inschakelen voor sjabloonimplementatie, indien nodig:** Hiermee kan Resource Manager geheimen uit de kluis ophalen.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a>Geavanceerd toegangs beleid voor sleutel kluis instellen via de Azure Portal

1. Selecteer uw sleutel kluis, ga naar **toegangs beleid** en **Klik om geavanceerd toegangs beleid weer te geven**.
2. Schakel het selectievakje **Toegang tot Azure Disk Encryption voor volumeversleuteling inschakelen** in.
3. Selecteer indien nodig **Toegang tot Azure Virtual Machines inschakelen voor implementatie** en/of **Toegang tot Azure Resource Manager inschakelen voor sjabloonimplementatie**. 
4. Klik op **Opslaan**.

![Geavanceerd toegangsbeleid voor Azure Key Vault](../media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a>Een Key Encryption Key instellen (optioneel)
Als u een Key Encryption Key (KEK) wilt gebruiken als een extra beveiligingslaag voor versleutelingssleutels, voegt u een KEK toe aan uw sleutelkluis. Gebruik de cmdlet [add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) om een sleutel versleutelings sleutel te maken in de sleutel kluis. U kunt ook een KEK importeren uit uw on-premises sleutel beheer HSM. Zie onze [Key Vault-documentatie](../../key-vault/keys/hsm-protected-keys.md) voor meer informatie. Wanneer er een KEK is opgegeven, gebruikt Azure Disk Encryption die sleutel om de versleutelingsgeheimen te verpakken voordat er naar Key Vault wordt geschreven. 

* Gebruik een RSA-sleutel type bij het genereren van sleutels. Azure Disk Encryption biedt nog geen ondersteuning voor het gebruik van elliptische curve toetsen.

* Uw sleutel kluis geheim en KEK Url's moeten een versie nummer hebben. Azure dwingt deze beperking van versiebeheer af. Zie de volgende voorbeelden voor geldige geheim- en KEK-URL's:

  * Voor beeld van een geldige geheime URL:   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Voor beeld van een geldige KEK-URL:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption biedt geen ondersteuning voor het opgeven van poortnummers als onderdeel van sleutelkluisgeheimen en KEK-URL's. Voorbeelden van niet-ondersteunde en ondersteunde sleutelkluis-URL's:

  * Onaanvaardbaar URL voor sleutel kluis  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Aanvaard bare sleutel kluis-URL:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a>Een coderings sleutel met Azure PowerShell instellen 
Voordat u het Power shell-script gebruikt, moet u vertrouwd zijn met de Azure Disk Encryption vereisten om inzicht te krijgen in de stappen in het script. Het voorbeeld script heeft mogelijk wijzigingen nodig voor uw omgeving. Met dit script worden alle Azure Disk Encryption vereisten gemaakt en wordt een bestaande IaaS-VM versleuteld, waarbij de schijf versleutelings sleutel wordt ingedrukt met behulp van een sleutel versleutelings sleutel. 

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
Als u verificatie via een certificaat wilt gebruiken, kunt u het uploaden naar uw sleutel kluis en implementeren op de client. Voordat u het Power shell-script gebruikt, moet u vertrouwd zijn met de Azure Disk Encryption vereisten om inzicht te krijgen in de stappen in het script. Het voorbeeld script heeft mogelijk wijzigingen nodig voor uw omgeving.

     
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

Als u verificatie via certificaat wilt gebruiken en de versleutelings sleutel wilt verpakken met een KEK, kunt u het onderstaande script als voor beeld gebruiken. Voordat u het Power shell-script gebruikt, moet u vertrouwd zijn met alle voor gaande Azure Disk Encryption vereisten om inzicht te krijgen in de stappen in het script. Het voorbeeld script heeft mogelijk wijzigingen nodig voor uw omgeving.

     
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

[Azure Disk Encryption met Azure AD inschakelen op Windows-Vm's (vorige versie)](disk-encryption-windows-aad.md)
