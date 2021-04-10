---
title: Azure Disk Encryption met Azure AD (vorige versie)
description: Dit artikel bevat de vereisten voor het gebruik van Microsoft Azure schijf versleuteling voor IaaS-Vm's.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 8db9aad279c151073f8c674854d139276a84b064
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555377"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure Disk Encryption met Azure AD (vorige versie)

**De nieuwe versie van Azure Disk Encryption elimineert de vereiste voor het opgeven van een Azure AD-toepassings parameter om VM-schijf versleuteling in te scha kelen. Met de nieuwe versie hoeft u geen Azure AD-referenties meer op te geven tijdens het inschakelen van de versleutelings stap. Alle nieuwe Vm's moeten worden versleuteld zonder de para meters van de Azure AD-toepassing met de nieuwe versie. Zie [Azure Disk Encryption voor Windows-vm's](disk-encryption-overview.md)om instructies te bekijken voor het inschakelen van VM-schijf versleuteling met de nieuwe versie. Vm's die al zijn versleuteld met Azure AD-toepassings parameters, worden nog steeds ondersteund en blijven behouden met de AAD-syntaxis.**

Dit artikel vormt een aanvulling op [Azure Disk Encryption voor Windows-vm's](disk-encryption-overview.md) met aanvullende vereisten en voor waarden voor Azure Disk Encryption met Azure AD (eerdere versie). De sectie [ondersteunde vm's en besturings systemen](disk-encryption-overview.md#supported-vms-and-operating-systems) blijft hetzelfde.

## <a name="networking-and-group-policy"></a>Netwerken en groepsbeleid

**Als u de functie Azure Disk Encryption wilt inschakelen met de oudere AAD-parameter syntaxis, moeten de virtuele machines van IaaS voldoen aan de volgende vereisten voor netwerk eindpunt configuratie:** 
  - Om een token te krijgen om verbinding te maken met uw sleutel kluis, moet de IaaS-VM verbinding kunnen maken met een Azure Active Directory-eind punt \[ login.microsoftonline.com \] .
  - Als u de versleutelings sleutels naar uw sleutel kluis wilt schrijven, moet de IaaS-VM verbinding kunnen maken met het eind punt van de sleutel kluis.
  - De IaaS-VM moet verbinding kunnen maken met een Azure Storage-eind punt dat als host fungeert voor de Azure extension-opslag plaats en een Azure-opslag account dat als host fungeert voor de VHD-bestanden.
  -  Als uw beveiligings beleid de toegang tot het Internet beperkt met Azure-Vm's, kunt u de voor gaande URI omzetten en een specifieke regel configureren om uitgaande connectiviteit met de IP-adressen toe te staan. Zie [Azure Key Vault achter een firewall](../../key-vault/general/access-behind-firewall.md)voor meer informatie.
  - De virtuele machine die moet worden versleuteld, moet worden geconfigureerd voor het gebruik van TLS 1,2 als standaard protocol. Als TLS 1,0 expliciet is uitgeschakeld en de .NET-versie niet is bijgewerkt naar 4,6 of hoger, wordt met de volgende register wijziging ADE de recentere versie van TLS geselecteerd:

```console
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001` 
```

**groepsbeleid:**
 - De Azure Disk Encryption oplossing maakt gebruik van de BitLocker externe sleutel beveiliging voor Windows IaaS Vm's. Voor virtuele machines die zijn toegevoegd aan een domein, moet u geen groeps beleid pushen waarmee TPM-beveiligingen worden afgedwongen. Zie [referentie voor BitLocker-Groepsbeleid](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)voor informatie over het groeps beleid voor het toestaan van BitLocker zonder compatibele TPM.

-  Het BitLocker-beleid voor virtuele machines die lid zijn van een domein met aangepast groeps beleid moet de volgende instelling hebben: [gebruikers opslag configureren voor BitLocker-herstel gegevens-> herstel sleutel van 256-bits toestaan](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption mislukt wanneer aangepaste groeps beleids instellingen voor BitLocker incompatibel zijn. Op computers die niet over de juiste beleids instelling beschikken, past u het nieuwe beleid toe, dwingt u het nieuwe beleid af om bij te werken (gpupdate.exe/Force) en moet u de computer mogelijk opnieuw opstarten.  

## <a name="encryption-key-storage-requirements"></a>Opslag vereisten voor de versleutelings sleutel  

Voor Azure Disk Encryption is een Azure Key Vault vereist om sleutels en geheimen voor schijf versleuteling te beheren en te beheren. Uw sleutel kluis en Vm's moeten zich in dezelfde Azure-regio en hetzelfde abonnement bevinden.

Zie [een sleutel kluis maken en configureren voor Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-key-vault-aad.md)voor meer informatie.
 
## <a name="next-steps"></a>Volgende stappen

- [Een sleutel kluis maken en configureren voor Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-key-vault-aad.md)
- [Azure Disk Encryption met Azure AD inschakelen op Windows-Vm's (vorige versie)](disk-encryption-windows-aad.md)
- [SysteemAzure Disk Encryption vereisten CLI-script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption van vereisten Power shell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
