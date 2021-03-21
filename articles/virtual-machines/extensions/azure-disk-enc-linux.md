---
title: Azure Disk Encryption voor Linux
description: Implementeert Azure Disk Encryption voor Linux naar een virtuele machine met behulp van een extensie van een virtuele machine.
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
author: ejarvi
ms.author: ejarvi
ms.date: 03/19/2020
ms.collection: linux
ms.openlocfilehash: 7c79391e3459804a4b5ce72c2230d17af3269641
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102566257"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption voor Linux (micro soft. Azure. Security. AzureDiskEncryptionForLinux)

## <a name="overview"></a>Overzicht

Azure Disk Encryption maakt gebruik van het subsysteem DM-cryptografie in Linux om versleuteling van de volledige schijf te bieden voor het [selecteren van Azure Linux-distributies](../linux/disk-encryption-overview.md).  Deze oplossing is geïntegreerd met Azure Key Vault voor het beheren van schijf versleutelings sleutels en geheimen.

## <a name="prerequisites"></a>Vereisten

Zie voor een volledige lijst met vereisten [Azure Disk Encryption voor Linux-vm's](../linux/disk-encryption-overview.md), met name de volgende secties:

- [Ondersteunde Vm's en besturings systemen](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Aanvullende VM-vereisten](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Netwerkvereisten](../linux/disk-encryption-overview.md#networking-requirements)
- [Opslag vereisten voor de versleutelings sleutel](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Extensie schema

Er zijn twee versies van het uitbreidings schema voor Azure Disk Encryption (ADE):
- v 1.1-een nieuwer aanbevolen schema dat geen eigenschappen van Azure Active Directory (AAD) gebruikt.
- v 0.1-een ouder schema dat eigenschappen van Azure Active Directory (AAD) vereist. 

Als u een doel schema wilt selecteren, `typeHandlerVersion` moet de eigenschap zijn ingesteld op de versie van het schema dat u wilt gebruiken.

### <a name="schema-v11-no-aad-recommended"></a>Schema v 1.1: geen AAD (aanbevolen)

Het v 1.1-schema wordt aanbevolen en vereist geen eigenschappen van Azure Active Directory (AAD).

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>Schema v 0,1: met AAD 

Het 0,1-schema vereist `AADClientID` en ofwel `AADClientSecret` of `AADClientCertificate` .

Gebruiken `AADClientSecret` :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

Gebruiken `AADClientCertificate` :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Eigenschaps waarden

| Name | Waarde/voor beeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Micro soft. Azure. Security | tekenreeks |
| type | AzureDiskEncryptionForLinux | tekenreeks |
| typeHandlerVersion | 1,1, 0,1 | int |
| (0,1-schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0,1-schema) AADClientSecret | wachtwoord | tekenreeks |
| (0,1-schema) AADClientCertificate | vingerafdruk | tekenreeks |
| Beschrijving (0,1-schema) Wachtzin | wachtwoord | tekenreeks |
| DiskFormatQuery | {"dev_path": "", "naam": "", "file_system": ""} | JSON-woorden lijst |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | tekenreeks | 
| (optioneel-standaard-RSA-OAEP) KeyEncryptionAlgorithm | ' RSA-OAEP ', ' RSA-OAEP-256 ', ' RSA1_5 ' | tekenreeks |
| KeyVaultURL | url | tekenreeks |
| KeyVaultResourceId | url | tekenreeks |
| Beschrijving KeyEncryptionKeyURL | url | tekenreeks |
| Beschrijving KekVaultResourceId | url | tekenreeks |
| Beschrijving SequenceVersion | uniqueidentifier | tekenreeks |
| VolumeType | Besturings systeem, gegevens, alle | tekenreeks |

## <a name="template-deployment"></a>Sjabloonimplementatie

Voor een voor beeld van een sjabloon implementatie op basis van schema v 1.1 raadpleegt u de Azure Quick Start-sjabloon [201-versleutelen-uitvoeren-Linux-VM-zonder-Aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

Voor een voor beeld van een sjabloon implementatie op basis van schema v 0,1 raadpleegt u de Azure Quick Start-sjabloon [201-versleutelen-uitvoeren-Linux-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

>[!WARNING]
> - Als u eerder Azure Disk Encryption met Azure AD hebt gebruikt om een virtuele machine te versleutelen, moet u deze optie blijven gebruiken om uw virtuele machine te versleutelen.
> - Bij het versleutelen van Linux-besturingssysteem volumes moet de virtuele machine worden beschouwd als niet-beschikbaar. We raden u ten zeerste aan om SSH-aanmeldingen te voor komen terwijl de versleuteling wordt uitgevoerd om te voor komen dat er geopende bestanden worden geblokkeerd die moeten worden geopend tijdens het versleutelings proces. Als u de voortgang wilt controleren, gebruikt u de Power shell [-cmdlet Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) of de [VM-versleutelings show](/cli/azure/vm/encryption#az-vm-encryption-show) cli-opdracht. Dit proces kan naar verwachting enkele uren duren voor een besturingssysteem volume van 30 GB, plus extra tijd voor het versleutelen van gegevens volumes. De versleutelings tijd voor gegevens volumes is evenredig met de grootte en hoeveelheid van de gegevens volumes, tenzij de optie alle versleutelings indeling wordt gebruikt. 
> - Het uitschakelen van versleuteling op Linux Vm's wordt alleen ondersteund voor gegevens volumes. Het wordt niet ondersteund op gegevens of besturingssysteem volumes als het volume van het besturings systeem is versleuteld. 

>[!NOTE]
> Als `VolumeType` para meter is ingesteld op alle, worden gegevens schijven alleen versleuteld als ze juist zijn gekoppeld.

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Raadpleeg de [Azure Disk Encryption Troubleshooting Guide (Engelstalig](../linux/disk-encryption-troubleshooting.md)) voor het oplossen van problemen.

### <a name="support"></a>Ondersteuning

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/community/). 

U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar [ondersteuning voor Azure](https://azure.microsoft.com/support/options/) en selecteer ondersteuning verkrijgen. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.

## <a name="next-steps"></a>Volgende stappen

* Zie [extensies en functies van virtuele machines voor Linux](features-linux.md)voor meer informatie over VM-extensies.
* Zie [virtuele Linux-machines](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines)voor meer informatie over Azure Disk Encryption voor Linux.