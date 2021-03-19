---
title: Een toepassingscertificaat toevoegen aan een cluster in PowerShell
description: 'Azure PowerShell-voorbeeldscript: een toepassingscertificaat toevoegen aan een Service Fabric-cluster.'
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 0f5a7d49200c7dec08f71f9cb0256630728e4711
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90090139"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Een toepassingscertificaat toevoegen aan een Service Fabric-cluster

In dit voorbeeldscript wordt uitgelegd hoe u een certificaat in Key Vault maakt en hoe u het certificaat implementeert op een van de virtuele-machineschaalsets waarop uw cluster wordt uitgevoerd. Dit scenario maakt niet rechtstreeks gebruik van Service Fabric, maar is afhankelijk van Key Vault en virtuele-machineschaalsets.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installeer zo nodig de Azure PowerShell volgens de instructies in de [Azure PowerShell handleiding](/powershell/azure/) en voer vervolgens `Connect-AzAccount` uit om verbinding maken met Azure. 

## <a name="create-a-certificate-in-key-vault"></a>Een certificaat maken in Key Vault

```powershell
$VaultName = ""
$CertName = ""
$SubjectName = "CN="

$policy = New-AzKeyVaultCertificatePolicy -SubjectName $SubjectName -IssuerName Self -ValidityInMonths 12
Add-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName -CertificatePolicy $policy
```

## <a name="or-upload-an-existing-certificate-into-key-vault"></a>Of een bestaand certificaat uploaden naar Key Vault

```powershell
$VaultName= ""
$CertName= ""
$CertPassword= ""
$PathToPFX= ""

$bytes = [System.IO.File]::ReadAllBytes($PathToPFX)
$base64 = [System.Convert]::ToBase64String($bytes)
$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $CertPassword
   } | ConvertTo-Json
$contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
$content = [System.Convert]::ToBase64String($contentbytes)

$SecretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

# Upload the certificate to the key vault as a secret
$Secret = Set-AzKeyVaultSecret -VaultName $VaultName -Name $CertName -SecretValue $SecretValue

```

## <a name="update-virtual-machine-scale-sets-profile-with-certificate"></a>Profiel voor virtuele-machineschaalsets bijwerken met certificaat

```powershell
$ResourceGroupName = ""
$VMSSName = ""
$CertStore = "My" # Update this with the store you want your certificate placed in, this is LocalMachine\My

# If you have added your certificate to the keyvault certificates, use
$CertConfig = New-AzVmssVaultCertificateConfig -CertificateUrl (Get-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName).SecretId -CertificateStore $CertStore

# Otherwise, if you have added your certificate to the keyvault secrets, use
$CertConfig = New-AzVmssVaultCertificateConfig -CertificateUrl (Get-AzKeyVaultSecret -VaultName $VaultName -Name $CertName).Id -CertificateStore $CertStore

$VMSS = Get-AzVmss -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMSSName

# If this KeyVault is already known by the virtual machine scale set, for example if the cluster certificate is deployed from this keyvault, use
$VMSS.virtualmachineprofile.osProfile.secrets[0].vaultCertificates.Add($CertConfig)

# Otherwise use
$VMSS = Add-AzVmssSecret -VirtualMachineScaleSet $VMSS -SourceVaultId (Get-AzKeyVault -VaultName $VaultName).ResourceId  -VaultCertificate $CertConfig
```

## <a name="update-the-virtual-machine-scale-set"></a>De virtuele-machineschaalset bijwerken
```powershell
Update-AzVmss -ResourceGroupName $ResourceGroupName -VirtualMachineScaleSet $VMSS -VMScaleSetName $VMSSName
```

> Als u het certificaat op meerdere typen knooppunten in uw cluster wilt plaatsen, moeten het tweede en derde gedeelte van dit script worden herhaald voor elk knooppunttype dat het certificaat moet bevatten.

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzKeyVaultCertificatePolicy](/powershell/module/az.keyvault/New-AzKeyVaultCertificatePolicy) | Hiermee maakt u een beleid in het geheugen dat het certificaat vertegenwoordigt |
| [Add-AzKeyVaultCertificate](/powershell/module/az.keyvault/Add-AzKeyVaultCertificate)| Hiermee wordt het beleid geïmplementeerd in Key Vault-certificaten |
| [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/Set-AzKeyVaultSecret)| Hiermee wordt het beleid geïmplementeerd in Key Vault-geheimen |
| [New-AzVmssVaultCertificateConfig](/powershell/module/az.compute/New-AzVmssVaultCertificateConfig) | Hiermee maakt u een configuratie in het geheugen dat het certificaat in een VM vertegenwoordigt |
| [Get-AzVmss](/powershell/module/az.compute/Get-AzVmss) |  |
| [Add-AzVmssSecret](/powershell/module/az.compute/Add-AzVmssSecret) | Hiermee wordt het certificaat toegevoegd aan de definitie in het geheugen van de virtuele-machineschaalset |
| [Update-AzVmss](/powershell/module/az.compute/Update-AzVmss) | Hiermee wordt de nieuwe definitie van de virtuele-machineschaalset geïmplementeerd |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).

Meer voorbeelden voor Azure Powershell voor Azure Service Fabric vindt u in de [voorbeelden van Azure PowerShell](../service-fabric-powershell-samples.md).
