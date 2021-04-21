---
title: Azure Disk Encryption voor Linux
description: Implementeert Azure Disk Encryption linux naar een virtuele machine met behulp van een extensie voor virtuele machines.
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
author: ejarvi
ms.author: ejarvi
ms.date: 03/19/2020
ms.collection: linux
ms.openlocfilehash: 8c0f233c2eb154636d64f747bb43bd392295aa9b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792377"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption voor Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Overzicht

Azure Disk Encryption maakt gebruik van het dm-crypt-subsysteem in Linux om volledige schijfversleuteling te bieden voor bepaalde [Linux-distributies van Azure.](../linux/disk-encryption-overview.md)  Deze oplossing is geïntegreerd met Azure Key Vault voor het beheren van schijfversleutelingssleutels en -geheimen.

## <a name="prerequisites"></a>Vereisten

Zie voor een volledige lijst met vereisten de Azure Disk Encryption [linux-VM's,](../linux/disk-encryption-overview.md)met name de volgende secties:

- [Ondersteunde VM's en besturingssystemen](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Aanvullende VM-vereisten](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Netwerkvereisten](../linux/disk-encryption-overview.md#networking-requirements)
- [Opslagvereisten voor versleutelingssleutels](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Extensieschema

Er zijn twee versies van het extensieschema voor Azure Disk Encryption (ADE):
- v1.1: een nieuwer aanbevolen schema dat geen gebruik maakt van Azure Active Directory eigenschappen (AAD).
- v0.1: een ouder schema dat AAD-Azure Active Directory vereist. 

Als u een doelschema wilt selecteren, `typeHandlerVersion` moet de eigenschap zijn ingesteld op de versie van het schema dat u wilt gebruiken.

### <a name="schema-v11-no-aad-recommended"></a>Schema v1.1: Geen AAD (aanbevolen)

Het v1.1-schema wordt aanbevolen en vereist geen Azure Active Directory eigenschappen (AAD).

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


### <a name="schema-v01-with-aad"></a>Schema v0.1: met AAD 

Voor het 0.1-schema zijn `AADClientID` en `AADClientSecret` of `AADClientCertificate` vereist.

Met `AADClientSecret` behulp van :

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

Met `AADClientCertificate` behulp van :

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


### <a name="property-values"></a>Eigenschapswaarden

| Name | Waarde/voorbeeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.Security | tekenreeks |
| type | AzureDiskEncryptionForLinux | tekenreeks |
| typeHandlerVersion | 1.1, 0.1 | int |
| (0.1 schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0.1 schema) AADClientSecret | wachtwoord | tekenreeks |
| (0.1 schema) AADClientCertificate | Vingerafdruk | tekenreeks |
| (optioneel) (0.1 schema) Passphrase | wachtwoord | tekenreeks |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON-woordenlijst |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | tekenreeks | 
| (optioneel - standaard RSA-OAEP ) KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | tekenreeks |
| KeyVaultURL | url | tekenreeks |
| KeyVaultResourceId | url | tekenreeks |
| (optioneel) KeyEncryptionKeyURL | url | tekenreeks |
| (optioneel) KekVaultResourceId | url | tekenreeks |
| (optioneel) SequenceVersion | uniqueidentifier | tekenreeks |
| VolumeType | Besturingssysteem, gegevens, alle | tekenreeks |

## <a name="template-deployment"></a>Sjabloonimplementatie

Zie Azure Quickstart Template [201-encrypt-running-linux-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)voor een voorbeeld van sjabloonimplementatie op basis van schema v1.1.

Zie Azure Quickstart Template [201-encrypt-running-linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)voor een voorbeeld van sjabloonimplementatie op basis van schema v0.1.

>[!WARNING]
> - Als u eerder een Azure Disk Encryption met Azure AD hebt gebruikt om een VM te versleutelen, moet u deze optie blijven gebruiken om uw VM te versleutelen.
> - Bij het versleutelen van Linux-besturingssysteemvolumes moet de VM als niet-beschikbaar worden beschouwd. We raden u ten zeerste aan om SSH-aanmeldingen te vermijden terwijl de versleuteling wordt uitgevoerd, om te voorkomen dat er problemen zijn met het blokkeren van geopende bestanden die tijdens het versleutelingsproces moeten worden geopend. Als u de voortgang wilt controleren, gebruikt u de PowerShell-cmdlet [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) of de CLI-opdracht [VM-versleuteling tonen.](/cli/azure/vm/encryption#az_vm_encryption_show) Dit proces kan enkele uren duren voor een besturingssysteemvolume van 30 GB, plus extra tijd voor het versleutelen van gegevensvolumes. De versleutelingstijd van gegevensvolumes is evenredig met de grootte en hoeveelheid van de gegevensvolumes, tenzij de versleutelingsindeling alle optie wordt gebruikt. 
> - Het uitschakelen van versleuteling op Linux-VM's wordt alleen ondersteund voor gegevensvolumes. Het wordt niet ondersteund op gegevens- of besturingssysteemvolumes als het besturingssysteemvolume is versleuteld. 

>[!NOTE]
> Als parameter `VolumeType` is ingesteld op Alle, worden gegevensschijven alleen versleuteld als ze correct zijn bevestigd.

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Raadpleeg de Azure Disk Encryption voor probleemoplossing voor [het oplossen van problemen.](../linux/disk-encryption-troubleshooting.md)

### <a name="support"></a>Ondersteuning

Als u op enig moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op de [MSDN Azure-](https://azure.microsoft.com/support/community/)en Stack Overflow forums . 

U kunt ook een incident ondersteuning voor Azure indienen. Ga naar [ondersteuning voor Azure](https://azure.microsoft.com/support/options/) selecteer Ondersteuning krijgen. Lees de veelgestelde vragen Microsoft Azure ondersteuning voor [meer informatie Ondersteuning voor Azure het gebruik van een Microsoft Azure.](https://azure.microsoft.com/support/faq/)

## <a name="next-steps"></a>Volgende stappen

* Zie Extensies en functies voor virtuele machines voor Linux voor meer informatie [over VM-extensies.](features-linux.md)
* Zie Virtuele Linux-machines Azure Disk Encryption meer informatie [over virtuele Linux-machines.](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines)