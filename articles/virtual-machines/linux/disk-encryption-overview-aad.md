---
title: Azure Disk Encryption met vereisten voor Azure AD-app (vorige versie)
description: In dit artikel vindt u aanvulling op Azure Disk Encryption voor Linux-Vm's met aanvullende vereisten en voor waarden voor Azure Disk Encryption met Azure AD.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: e27eb64139c20ac2b8c776edc2d0840b80fddb62
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558335"
---
# <a name="azure-disk-encryption-with-azure-active-directory-ad-previous-release"></a>Azure Disk Encryption met Azure Active Directory (AD) (vorige versie)

De nieuwe versie van Azure Disk Encryption elimineert de vereiste voor het leveren van een Azure Active Directory (Azure AD)-toepassings parameter om VM-schijf versleuteling in te scha kelen. Met de nieuwe versie hoeft u geen Azure AD-referenties meer op te geven tijdens het inschakelen van de versleutelings stap. Alle nieuwe Vm's moeten worden versleuteld zonder de para meters van de Azure AD-toepassing met behulp van de nieuwe release. Zie [Azure Disk Encryption voor Linux-vm's](disk-encryption-overview.md)voor instructies over het inschakelen van VM-schijf versleuteling met behulp van de nieuwe versie. Vm's die al zijn versleuteld met Azure AD-toepassings parameters, worden nog steeds ondersteund en blijven behouden met de AAD-syntaxis.

In dit artikel vindt u aanvulling op [Azure Disk Encryption voor Linux-vm's](disk-encryption-overview.md) met aanvullende vereisten en voor waarden voor Azure Disk Encryption met Azure AD (eerdere versie).

De informatie in deze secties blijft hetzelfde:

- [Ondersteunde Vm's en besturings systemen](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Aanvullende VM-vereisten](disk-encryption-overview.md#additional-vm-requirements)


## <a name="networking-and-group-policy"></a>Netwerken en groepsbeleid

Als u de functie Azure Disk Encryption wilt inschakelen met behulp van de oudere AAD-parameter syntaxis, moeten de IaaS-Vm's (Infrastructure as a Service) voldoen aan de volgende vereisten voor netwerk eindpunt configuratie: 
  - Als u een token wilt ontvangen om verbinding te maken met uw sleutel kluis, moet de IaaS-VM verbinding kunnen maken met een Azure AD-eind punt \[ login.microsoftonline.com \] .
  - Als u de versleutelings sleutels naar uw sleutel kluis wilt schrijven, moet de IaaS-VM verbinding kunnen maken met het eind punt van de sleutel kluis.
  - De IaaS-VM moet verbinding kunnen maken met een Azure Storage-eind punt dat als host fungeert voor de Azure extension-opslag plaats en een Azure-opslag account dat als host fungeert voor de VHD-bestanden.
  -  Als uw beveiligings beleid de toegang tot het Internet beperkt met Azure-Vm's, kunt u de voor gaande URI omzetten en een specifieke regel configureren om uitgaande connectiviteit met de IP-adressen toe te staan. Zie [Azure Key Vault achter een firewall](../../key-vault/general/access-behind-firewall.md)voor meer informatie.
  - In Windows, als TLS 1,0 expliciet is uitgeschakeld en de .NET-versie niet is bijgewerkt naar 4,6 of hoger, kan de volgende register wijziging Azure Disk Encryption de meest recente TLS-versie te selecteren:

  ```config-registry
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001
    
  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001` 
  ```

### <a name="group-policy"></a>Groepsbeleid
 - De Azure Disk Encryption oplossing maakt gebruik van de BitLocker externe sleutel beveiliging voor Windows IaaS Vm's. Voor virtuele machines die zijn gekoppeld aan een domein, moet u geen groeps beleid pushen waarmee TPM-beveiligingen worden afgedwongen. Zie voor informatie over de groepsbeleid voor de optie **BitLocker toestaan zonder compatibele TPM**, [BitLocker-Groepsbeleid verwijzing](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

- Het BitLocker-beleid op virtuele machines die lid zijn van een domein met een aangepaste groepsbeleid moet de volgende instelling bevatten: [gebruikers opslag van BitLocker-herstel gegevens configureren-> 256-bits herstel sleutel toestaan](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption mislukt wanneer aangepaste groepsbeleid instellingen voor BitLocker incompatibel zijn. Op computers die niet over de juiste beleids instelling beschikken, past u het nieuwe beleid toe, dwingt u het nieuwe beleid af om bij te werken (gpupdate.exe/Force) en start u vervolgens opnieuw op als dat is vereist. 

## <a name="encryption-key-storage-requirements"></a>Opslag vereisten voor de versleutelings sleutel 

Azure Disk Encryption vereist Azure Key Vault voor het beheer en beheren van schijf versleutelings sleutels en geheimen. Uw sleutel kluis en Vm's moeten zich in dezelfde Azure-regio en hetzelfde abonnement bevinden.

Zie [een sleutel kluis maken en configureren voor Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-key-vault-aad.md)voor meer informatie.
 
## <a name="next-steps"></a>Volgende stappen

- [Een sleutel kluis maken en configureren voor Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-key-vault-aad.md)
- [Azure Disk Encryption met Azure AD inschakelen op virtuele Linux-machines (vorige versie)](disk-encryption-linux-aad.md)
- [SysteemAzure Disk Encryption vereisten CLI-script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption van vereisten Power shell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
