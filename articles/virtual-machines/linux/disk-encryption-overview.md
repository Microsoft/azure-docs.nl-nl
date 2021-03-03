---
title: Azure Disk Encryption inschakelen voor Linux-VM's
description: In dit artikel vindt u instructies voor het inschakelen van Microsoft Azure schijf versleuteling voor virtuele Linux-machines.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: de67e356e54328944c55f41dc0c9670e2540e82e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694373"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Azure Disk Encryption voor Linux-VM's 

Azure Disk Encryption helpt om uw gegevens te beschermen en te beveiligen, zodat u aan de beveiligings- en nalevingsafspraken van uw organisatie voldoet. Het maakt gebruik van de [DM-cryptografie](https://en.wikipedia.org/wiki/Dm-crypt) functie van Linux om volume versleuteling te bieden voor het besturings systeem en de gegevens schijven van Azure virtual machines (vm's), en is geïntegreerd met [Azure Key Vault](../../key-vault/index.yml) om u te helpen de coderings sleutels en geheimen voor schijven te controleren en te beheren.

Azure Disk Encryption zone is flexibel, op dezelfde manier als Virtual Machines. Zie [Azure-Services die ondersteuning bieden voor Beschikbaarheidszones](../../availability-zones/az-region.md)voor meer informatie.

Als u [Azure Security Center](../../security-center/index.yml)gebruikt, wordt u gewaarschuwd als u virtuele machines hebt die niet zijn versleuteld. De waarschuwingen worden weer gegeven als hoge ernst en de aanbeveling is om deze Vm's te versleutelen.

![Waarschuwing voor schijf versleuteling van Azure Security Center](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Als u eerder Azure Disk Encryption met Azure AD hebt gebruikt om een virtuele machine te versleutelen, moet u deze optie blijven gebruiken om uw virtuele machine te versleutelen. Zie [Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-overview-aad.md) voor meer informatie. 
> - Bepaalde aanbevelingen kunnen leiden tot meer gegevens, het netwerk of het gebruik van reken bronnen, wat leidt tot extra licentie-of abonnements kosten. U moet een geldig actief Azure-abonnement hebben om resources te maken in Azure in de ondersteunde regio's.

Meer informatie over de basis principes van Azure Disk Encryption voor Linux in slechts enkele minuten kunt u met behulp van [een virtuele Linux-machine maken en versleutelen met Azure cli Quick](disk-encryption-cli-quickstart.md) start of [met behulp van een virtuele Linux-machine maken en versleutelen met Azure PowerShell Snelstartgids](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Ondersteunde Vm's en besturings systemen

### <a name="supported-vms"></a>Ondersteunde Vm's

Virtuele Linux-machines zijn beschikbaar in [verschillende grootten](../sizes.md). Azure Disk Encryption wordt ondersteund op Vm's van generatie 1 en generatie 2. Azure Disk Encryption is ook beschikbaar voor virtuele machines met Premium Storage.

Zie [Azure VM-grootten zonder lokale tijdelijke schijf](../azure-vms-no-temp-disk.md).

Azure Disk Encryption is ook niet beschikbaar op [de Basic-, a-Series vm's](https://azure.microsoft.com/pricing/details/virtual-machines/series/)of op virtuele machines die niet voldoen aan deze minimale geheugen vereisten:

| Virtuele machine | Minimale geheugen vereiste |
|--|--|
| Virtuele Linux-machines wanneer gegevens volumes alleen worden versleuteld| 2 GB |
| Linux-Vm's bij het versleutelen van gegevens en OS-volumes, waarbij het gebruik van het basis-(/) bestands systeem 4 GB of minder is | 8 GB |
| Linux-Vm's bij het versleutelen van gegevens en besturingssysteem volumes, waarbij het gebruik van het root-(/) bestands systeem groter is dan 4 GB | Het root-bestandssysteem gebruik * 2. Een voor beeld: voor een 16 GB aan root File System-gebruik is ten minste GB RAM vereist |

Zodra het versleutelings proces van de besturingssysteem schijf is voltooid op virtuele Linux-machines, kan de virtuele machine worden geconfigureerd om te worden uitgevoerd met minder geheugen.

Zie [Azure Disk Encryption: niet-ondersteunde scenario's](disk-encryption-linux.md#unsupported-scenarios)voor meer uitzonde ringen.

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

Azure Disk Encryption wordt ondersteund op een subset van de door [Azure goedgekeurde Linux-distributies](endorsed-distros.md). Dit is een subset van alle mogelijke distributies van Linux server.

![Venn-diagram van Linux-server distributies die ondersteuning bieden voor Azure Disk Encryption](./media/disk-encryption/ade-supported-distros.png)

Linux-server distributies die niet zijn goedgekeurd door Azure, bieden geen ondersteuning voor Azure Disk Encryption; van degenen die zijn goedgekeurd, worden alleen de volgende distributies en versies ondersteund Azure Disk Encryption:


| Uitgever | Aanbieding | SKU | URN | Ondersteund volume type voor versleuteling |
| --- | --- |--- | --- |
| Canonical | Ubuntu | 18.04-LTS | Canoniek: UbuntuServer: 18.04-LTS: nieuwste | Besturings systeem en gegevens schijf |
| Canonical | Ubuntu 18.04 | 18,04-DAGELIJKS-LTS | Canoniek: UbuntuServer: 18.04-DAILY-LTS: nieuwste | Besturings systeem en gegevens schijf |
| Canonical | Ubuntu 16.04 | 16,04-DAGELIJKS-LTS | Canoniek: UbuntuServer: 16.04-DAILY-LTS: nieuwste | Besturings systeem en gegevens schijf |
| Canonical | Ubuntu 14.04.5</br>[met een afgestemde kernel van Azure bijgewerkt tot 4,15 of hoger](disk-encryption-troubleshooting.md) | 14.04.5-LTS | Canoniek: UbuntuServer: 14.04.5-LTS: nieuwste | Besturings systeem en gegevens schijf |
| Canonical | Ubuntu 14.04.5</br>[met een afgestemde kernel van Azure bijgewerkt tot 4,15 of hoger](disk-encryption-troubleshooting.md) | 14.04.5-DAILY-LTS | Canoniek: UbuntuServer: 14.04.5-DAILY-LTS: nieuwste | Besturings systeem en gegevens schijf |
| RedHat | RHEL 8-LVM | 8-LVM | RedHat: RHEL: 8-LVM: nieuwste | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RedHat | RHEL 8,2 | 8,2 | RedHat: RHEL: 8.2: nieuwste | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RedHat | RHEL 8,1 | 8.1 | RedHat: RHEL: 8.1: nieuwste | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RedHat | RHEL 7-LVM | 7-LVM | RedHat: RHEL: 7-LVM: 7.8.2020111201 | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RedHat | RHEL 7,8 | 7,8 | RedHat: RHEL: 7,8: nieuwste | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RedHat | RHEL 7,7 | 7,7 | RedHat: RHEL: 7,7: nieuwste | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RedHat | RHEL 7,6 | 7.6 | RedHat: RHEL: 7,6: nieuwste | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RedHat | RHEL 7.5 | 7,5 | RedHat: RHEL: 7.5: nieuwste | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RedHat | RHEL 7,4 | 7.4 | RedHat: RHEL: 7.4: nieuwste | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RedHat | RHEL 7,3 | 7.3 | RedHat: RHEL: 7.3: nieuwste | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RedHat | RHEL 7,2 | 7.2 | RedHat: RHEL: 7.2: nieuwste | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RedHat | RHEL 6,8 | 6.8 | RedHat: RHEL: 6,8: nieuwste | Gegevens schijf (zie opmerking hieronder) |
| RedHat | RHEL 6,7 | 6.7 | RedHat: RHEL: 6,7: nieuwste | Gegevens schijf (zie opmerking hieronder) |
| OpenLogic | CentOS 8-LVM | 8-LVM | Open Logic: CentOS-LVM: 8-LVM: nieuwste | Besturings systeem en gegevens schijf |
| OpenLogic | CentOS 8,2 | 8_2 | Open Logic: CentOS: 8_2: nieuwste | Besturings systeem en gegevens schijf |
| OpenLogic | CentOS 8,1 | 8_1 | Open Logic: CentOS: 8_1: nieuwste | Besturings systeem en gegevens schijf |
| OpenLogic | CentOS 7-LVM | 7-LVM | Open Logic: CentOS-LVM: 7-LVM: 7.8.2020111100 | Besturings systeem en gegevens schijf |
| OpenLogic | CentOS 7,8 | 7,8 | Open Logic: CentOS: 7_8: nieuwste | Besturings systeem en gegevens schijf |
| OpenLogic | CentOS 7,7 | 7,7 | Open Logic: CentOS: 7,7: nieuwste | Besturings systeem en gegevens schijf |
| OpenLogic | CentOS 7,6 | 7.6 | Open Logic: CentOS: 7,6: nieuwste | Besturings systeem en gegevens schijf |
| OpenLogic | CentOS 7.5 | 7,5 | Open Logic: CentOS: 7.5: nieuwste | Besturings systeem en gegevens schijf |
| OpenLogic | CentOS 7.4 | 7.4 | Open Logic: CentOS: 7.4: nieuwste | Besturings systeem en gegevens schijf |
| OpenLogic | CentOS 7,3 | 7.3 | Open Logic: CentOS: 7.3: nieuwste | Besturings systeem en gegevens schijf |
| OpenLogic | CentOS 7.2 n | 7,2 n | Open Logic: CentOS: 7,2 n: nieuwste | Besturings systeem en gegevens schijf |
| OpenLogic | CentOS 7.1 | 7.1 | Open Logic: CentOS: 7.1: nieuwste | Alleen de gegevensschijf |
| OpenLogic | CentOS 7,0 | 7.0 | Open Logic: CentOS: 7.0: nieuwste | Alleen de gegevensschijf |
| OpenLogic | CentOS 6,8 | 6.8 | Open Logic: CentOS: 6,8: nieuwste | Alleen de gegevensschijf |
| SUSE | openSUSE 42,3 | 42,3 | SUSE: openSUSE-Schrikkel: 42.3: nieuwste | Alleen de gegevensschijf |
| SUSE | SLES 12-SP4 | 12-SP4 | SUSE: SLES: 12-SP4: nieuwste | Alleen de gegevensschijf |
| SUSE | SLES HPC 12-SP3 | 12-SP3 | SUSE: SLES-HPC: 12-SP3: nieuwste | Alleen de gegevensschijf |

> [!NOTE]
> De nieuwe Azure Disk Encryption-implementatie wordt ondersteund voor RHEL-besturings systeem en gegevens schijf voor RHEL7 betalen per gebruik-installatie kopieën.  
>
> ADE wordt ook ondersteund voor RHEL-uw eigen abonnementen, maar pas **nadat** het abonnement is geregistreerd. Zie [Red Hat Enterprise Linux uw gouden installatie kopieën met uw eigen abonnement in azure](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) voor meer informatie.

## <a name="additional-vm-requirements"></a>Aanvullende VM-vereisten

Azure Disk Encryption vereist dat de DM-cryptografie-en vfat-modules aanwezig zijn op het systeem. Als u vfat uit de standaard installatie kopie verwijdert of uitschakelt, wordt het sleutel volume niet door het systeem gelezen en wordt de benodigde sleutel voor het ontgrendelen van de schijven bij de volgende keer opnieuw opstarten voor komen. De stappen voor het beveiligen van het systeem die de vfat-module van het systeem verwijderen of het uitvouwen van het besturings systeem mountpoints/mappen op gegevens stations zijn niet compatibel met Azure Disk Encryption. 

Voordat u versleuteling inschakelt, moeten de gegevens schijven die moeten worden versleuteld, op de juiste manier worden weer gegeven in/etc/fstab. Gebruik de optie ' geen fout ' bij het maken van vermeldingen en kies een permanente blok apparaatnaam (als apparaatnamen in de indeling '/dev/sdX ' mag niet worden gekoppeld aan dezelfde schijf tijdens het opnieuw opstarten, met name na de versleuteling). Zie [problemen met de naam wijzigingen van Linux VM-apparaten oplossen](../troubleshooting/troubleshoot-device-names-problems.md)voor meer informatie over dit gedrag.

Controleer of de bestand/etc/fstab-instellingen correct zijn geconfigureerd voor koppelen. Als u deze instellingen wilt configureren, voert u de koppeling-a-opdracht uit of start u de VM opnieuw op. vervolgens wordt de koppeling op die manier geactiveerd. Als dat is voltooid, controleert u de uitvoer van de lsblk-opdracht om te controleren of het station nog steeds is gekoppeld. 

- Als het bestand/etc/fstab-bestand het station niet correct koppelt voordat u versleuteling inschakelt, kan Azure Disk Encryption het niet goed koppelen.
- Het Azure Disk Encryption proces verplaatst de koppelings gegevens uit bestand/etc/fstab en in een eigen configuratie bestand als onderdeel van het versleutelings proces. Geen waarschuwing weer gegeven om te zien welke vermelding ontbreekt in bestand/etc/fstab nadat de versleuteling van de gegevens schijf is voltooid.
- Voordat u begint met het versleutelen, moet u alle services en processen die kunnen schrijven naar gekoppelde gegevens schijven stoppen en uitschakelen, zodat ze niet automatisch opnieuw worden opgestart na het opnieuw opstarten. Hierdoor blijven bestanden geopend op deze partities, waardoor de versleutelings procedure niet opnieuw kan worden gekoppeld, waardoor de versleuteling mislukt. 
- Nadat het systeem opnieuw is opgestart, zal het Azure Disk Encryption proces de zojuist versleutelde schijven te koppelen. Ze zijn niet onmiddellijk beschikbaar na het opnieuw opstarten. Het proces heeft tijd nodig om de versleutelde stations te starten, te ontgrendelen en vervolgens te koppelen voordat andere processen kunnen worden geopend. Dit proces kan meer dan een minuut duren na het opnieuw opstarten, afhankelijk van de systeem kenmerken.

Hier volgt een voor beeld van de opdrachten die worden gebruikt voor het koppelen van de gegevens schijven en het maken van de benodigde bestand/etc/fstab-vermeldingen:

```bash
UUID0="$(blkid -s UUID -o value /dev/sda1)"
UUID1="$(blkid -s UUID -o value /dev/sda2)"
mkdir /data0
mkdir /data1
echo "UUID=$UUID0 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "UUID=$UUID1 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```
## <a name="networking-requirements"></a>Netwerkvereisten

Als u de functie Azure Disk Encryption wilt inschakelen, moeten de virtuele Linux-machines voldoen aan de volgende vereisten voor netwerk eindpunt configuratie:
  - Om een token te krijgen om verbinding te maken met uw sleutel kluis, moet de virtuele Linux-machine verbinding kunnen maken met een Azure Active Directory-eind punt, \[ login.microsoftonline.com \] .
  - Als u de versleutelings sleutels naar uw sleutel kluis wilt schrijven, moet de virtuele Linux-machine verbinding kunnen maken met het eind punt van de sleutel kluis.
  - De virtuele Linux-machine moet verbinding kunnen maken met een Azure Storage-eind punt dat als host fungeert voor de Azure extension-opslag plaats en een Azure Storage-account dat de VHD-bestanden host.
  -  Als uw beveiligings beleid de toegang tot het Internet beperkt met Azure-Vm's, kunt u de voor gaande URI omzetten en een specifieke regel configureren om uitgaande connectiviteit met de IP-adressen toe te staan. Zie [Azure Key Vault achter een firewall](../../key-vault/general/access-behind-firewall.md)voor meer informatie.  

## <a name="encryption-key-storage-requirements"></a>Opslag vereisten voor de versleutelings sleutel  

Voor Azure Disk Encryption is een Azure Key Vault vereist om sleutels en geheimen voor schijf versleuteling te beheren en te beheren. Uw sleutel kluis en Vm's moeten zich in dezelfde Azure-regio en hetzelfde abonnement bevinden.

Zie [een sleutel kluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md)voor meer informatie.

## <a name="terminology"></a>Terminologie
In de volgende tabel worden enkele algemene termen gedefinieerd die worden gebruikt in azure Disk Encryption Documentation:

| Terminologie | Definitie |
| --- | --- |
| Azure Key Vault | Key Vault is een cryptografische service voor sleutel beheer die is gebaseerd op FIPS (Federal Information Processing Standards) gevalideerde hardware security modules. Deze standaarden helpen u bij het beveiligen van uw cryptografische sleutels en gevoelige geheimen. Zie voor meer informatie de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -documentatie en het [maken en configureren van een sleutel kluis voor Azure Disk Encryption](disk-encryption-key-vault.md). |
| Azure CLI | [De Azure cli](/cli/azure/install-azure-cli) is geoptimaliseerd voor het beheren en beheren van Azure-resources vanaf de opdracht regel.|
| DM-Crypt |[DM-cryptografie](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) is het op Linux gebaseerde, transparante schijf versleutelings subsysteem dat wordt gebruikt om schijf versleuteling in te scha kelen op Linux vm's. |
| Sleutel versleutelings sleutel (KEK) | De asymmetrische sleutel (RSA 2048) die u kunt gebruiken om het geheim te beveiligen of in te pakken. U kunt een door HSM (Hardware Security module) beveiligde of met software beschermde sleutel opgeven. Zie voor meer informatie de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -documentatie en het [maken en configureren van een sleutel kluis voor Azure Disk Encryption](disk-encryption-key-vault.md). |
| PowerShell-cmdlets | Zie [Azure PowerShell-cmdlets](/powershell/azure/)voor meer informatie. |


## <a name="next-steps"></a>Volgende stappen

- [Snelstartgids: een virtuele Linux-machine maken en versleutelen met Azure CLI ](disk-encryption-cli-quickstart.md)
- [Snelstartgids: een virtuele Linux-machine maken en versleutelen met Azure PowerShell](disk-encryption-powershell-quickstart.md) 
- [Azure Disk Encryption-scenario's voor virtuele Linux-machines](disk-encryption-linux.md)
- [SysteemAzure Disk Encryption vereisten CLI-script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption van vereisten Power shell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Een sleutelkluis voor Azure Disk Encryption maken en configureren](disk-encryption-key-vault.md)
