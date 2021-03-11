---
title: Gids voor het oplossen van problemen Azure Disk Encryption
description: In dit artikel vindt u tips voor het oplossen van problemen met Microsoft Azure schijf versleuteling voor Windows-Vm's.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 057aba3e49d32694563f412101be499342f2aad0
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550600"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Gids voor het oplossen van problemen Azure Disk Encryption

Deze hand leiding is voor IT-professionals, gegevens beveiligings analisten en Cloud beheerders waarvan de organisaties Azure Disk Encryption gebruiken. In dit artikel vindt u informatie over het oplossen van problemen met schijf versleuteling.

Voordat u een van de volgende stappen uitvoert, moet u eerst controleren of de virtuele machines die u probeert te versleutelen, worden ondersteund door de [ondersteunde VM-grootten en-besturings systemen](disk-encryption-overview.md#supported-vms-and-operating-systems)en dat u aan alle vereisten hebt voldaan:

- [Netwerkvereisten](disk-encryption-overview.md#networking-requirements)
- [Groeps beleids vereisten](disk-encryption-overview.md#group-policy-requirements)
- [Opslag vereisten voor de versleutelings sleutel](disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Problemen met Azure Disk Encryption achter een firewall oplossen

Wanneer de connectiviteit wordt beperkt door een firewall, een proxy vereiste of NSG-instellingen (netwerk beveiligings groep), kan de mogelijkheid van de uitbrei ding voor het uitvoeren van de benodigde taken worden onderbroken. Deze onderbreking kan resulteren in status berichten, zoals ' extensie status is niet beschikbaar op de virtuele machine '. In de verwachte scenario's kan de versleuteling niet worden voltooid. De volgende secties bevatten enkele veelvoorkomende Firewall problemen die u kunt onderzoeken.

### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen
Alle instellingen voor de netwerk beveiligings groep die worden toegepast, moeten het eind punt nog steeds toestaan om te voldoen aan de gedocumenteerde [vereisten](disk-encryption-overview.md#networking-requirements) voor de netwerk configuratie voor schijf versleuteling.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault achter een firewall

Wanneer versleuteling wordt ingeschakeld met [Azure AD-referenties](disk-encryption-windows-aad.md#), moet de doel-VM verbinding kunnen maken met zowel Azure Active Directory-eind punten als Key Vault-eind punten. De huidige Azure Active Directory-verificatie-eind punten worden onderhouden in de secties 56 en 59 van de documentatie voor [Microsoft 365 url's en IP-](/microsoft-365/enterprise/urls-and-ip-address-ranges) adresbereiken. Key Vault instructies vindt u in de documentatie over het [verkrijgen van toegang tot Azure Key Vault achter een firewall](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Azire Instance Metadata Service 
De virtuele machine moet toegang hebben tot het [meta gegevens service](../windows/instance-metadata-service.md) -eind punt van Azure, dat gebruikmaakt van een bekende niet-Routeer bare IP-adres ( `169.254.169.254` ) die alleen vanuit de virtuele machine kan worden geopend.  Proxy configuraties waarmee lokaal HTTP-verkeer naar dit adres wordt gewijzigd (bijvoorbeeld het toevoegen van een X-doorgestuurd-for-header) worden niet ondersteund.

## <a name="troubleshooting-windows-server-2016-server-core"></a>Problemen met Windows Server 2016 Server Core oplossen

In Windows Server 2016 Server Core is het onderdeel bdehdcfg niet standaard beschikbaar. Dit onderdeel is vereist door Azure Disk Encryption. Het wordt gebruikt om het systeem volume van het besturingssysteem volume te splitsen. dit wordt slechts één keer uitgevoerd voor de levens duur van de virtuele machine. Deze binaire bestanden zijn niet vereist tijdens latere versleutelings bewerkingen.

U kunt dit probleem omzeilen door de volgende vier bestanden te kopiëren van een Windows Server 2016 Data Center-VM naar dezelfde locatie op Server Core:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. Voer de volgende opdracht in:

   ```
   bdehdcfg.exe -target default
   ```

1. Met deze opdracht maakt u een systeem partitie van 550 MB. Start het systeem opnieuw op.

1. Gebruik Disk Part om de volumes te controleren en ga vervolgens verder.  

Bijvoorbeeld:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```

## <a name="troubleshooting-encryption-status"></a>Problemen met de versleutelings status oplossen 

De portal kan een schijf weer geven als versleuteld, zelfs nadat deze is ontsleuteld in de virtuele machine.  Dit kan gebeuren wanneer opdrachten op laag niveau worden gebruikt om de schijf rechtstreeks van de virtuele machine te ontsleutelen, in plaats van de Azure Disk Encryption beheer opdrachten op een hoger niveau te gebruiken.  Met de opdrachten op een hoger niveau wordt de schijf niet alleen ontsleuteld vanuit de virtuele machine, maar buiten de virtuele machine worden ook belang rijke versleutelings instellingen en extensie-instellingen van het platform bijgewerkt die zijn gekoppeld aan de virtuele machine.  Als deze niet in de uitlijning worden bewaard, kan het platform geen versleutelings status rapporteren of de virtuele machine op de juiste wijze inrichten.

Als u Azure Disk Encryption wilt uitschakelen met Power shell, gebruikt u [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) gevolgd door [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Het uitvoeren van Remove-AzVMDiskEncryptionExtension voordat de versleuteling is uitgeschakeld, mislukt.

Gebruik [AZ VM Encryption Disable](/cli/azure/vm/encryption)om Azure Disk Encryption uit te scha KELEN met cli. 

## 



## <a name="next-steps"></a>Volgende stappen

In dit document hebt u meer geleerd over enkele veelvoorkomende problemen in Azure Disk Encryption en hoe u deze problemen oplost. Raadpleeg de volgende artikelen voor meer informatie over deze service en de mogelijkheden ervan:

- [Schijf versleuteling Toep assen in Azure Security Center](../../security-center/asset-inventory.md)
- [Versleuteling van inactieve gegevens in Azure](../../security/fundamentals/encryption-atrest.md)