---
title: Azure Disk Encryption voor Windows
description: Hiermee worden Azure Disk Encryption geïmplementeerd op een virtuele Windows-machine met behulp van een extensie van een virtuele machine.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: 361751107212d556aaee6886902d2798c014ff39
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180180"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption voor Windows (micro soft. Azure. Security. AzureDiskEncryption)

## <a name="overview"></a>Overzicht

Azure Disk Encryption maakt gebruik van BitLocker om versleuteling van de volledige schijf te bieden op virtuele machines van Azure waarop Windows wordt uitgevoerd.  Deze oplossing is geïntegreerd met Azure Key Vault voor het beheren van schijf versleutelings sleutels en geheimen in uw sleutel kluis abonnement. 

## <a name="prerequisites"></a>Vereisten

Zie voor een volledige lijst met vereisten [Azure Disk Encryption voor Windows-vm's](../windows/disk-encryption-overview.md), met name de volgende secties:

- [Ondersteunde Vm's en besturings systemen](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Netwerkvereisten](../windows/disk-encryption-overview.md#networking-requirements)
- [groepsbeleid vereisten](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>Extensie schema

Er zijn twee versies van het uitbreidings schema voor Azure Disk Encryption (ADE):
- v 2.2-een nieuwer aanbevolen schema dat geen eigenschappen van Azure Active Directory (AAD) gebruikt.
- v 1.1-een ouder schema dat eigenschappen van Azure Active Directory (AAD) vereist. 

Als u een doel schema wilt selecteren, `typeHandlerVersion` moet de eigenschap zijn ingesteld op de versie van het schema dat u wilt gebruiken.

### <a name="schema-v22-no-aad-recommended"></a>Schema v 2.2: geen AAD (aanbevolen)

Het v 2.2-schema wordt aanbevolen voor alle nieuwe virtuele machines en vereist geen Azure Active Directory eigenschappen.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KekVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>Schema v 1.1: met AAD 

Het 1,1-schema vereist `aadClientID` en `aadClientSecret` `AADClientCertificate` wordt niet aanbevolen voor nieuwe vm's.

Gebruiken `aadClientSecret` :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
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
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Eigenschaps waarden

| Naam | Waarde/voor beeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Micro soft. Azure. Security | tekenreeks |
| type | AzureDiskEncryption | tekenreeks |
| typeHandlerVersion | 2,2, 1,1 | tekenreeks |
| (1,1-schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (1,1-schema) AADClientSecret | wachtwoord | tekenreeks |
| (1,1-schema) AADClientCertificate | vingerafdruk | tekenreeks |
| EncryptionOperation | EnableEncryption | tekenreeks | 
| (optioneel-standaard-RSA-OAEP) KeyEncryptionAlgorithm | ' RSA-OAEP ', ' RSA-OAEP-256 ', ' RSA1_5 ' | tekenreeks |
| KeyVaultURL | url | tekenreeks |
| KeyVaultResourceId | url | tekenreeks |
| Beschrijving KeyEncryptionKeyURL | url | tekenreeks |
| Beschrijving KekVaultResourceId | url | tekenreeks |
| Beschrijving SequenceVersion | uniqueidentifier | tekenreeks |
| VolumeType | Besturings systeem, gegevens, alle | tekenreeks |

## <a name="template-deployment"></a>Sjabloonimplementatie

Voor een voor beeld van een sjabloon implementatie op basis van schema v 2.2 raadpleegt u Azure Quick Start-sjabloon [201-versleutelen-uitvoeren-Windows-VM-zonder-Aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).

Voor een voor beeld van een sjabloon implementatie op basis van schema v 1.1 raadpleegt u de Azure Quick Start-sjabloon [201-Encrypt-running-Windows-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

>[!NOTE]
> Als `VolumeType` para meter is ingesteld op alle, worden gegevens schijven alleen versleuteld als ze juist zijn ingedeeld. 

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Raadpleeg de [Azure Disk Encryption Troubleshooting Guide (Engelstalig](../windows/disk-encryption-troubleshooting.md)) voor het oplossen van problemen.

### <a name="support"></a>Ondersteuning

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/community/). 

U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar [ondersteuning voor Azure](https://azure.microsoft.com/support/options/) en selecteer ondersteuning verkrijgen. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.

## <a name="next-steps"></a>Volgende stappen

* Zie [virtuele machines en functies voor Windows](features-windows.md)voor meer informatie over uitbrei dingen.
* Zie [Windows virtual machines](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines)voor meer informatie over Azure Disk Encryption voor Windows.
