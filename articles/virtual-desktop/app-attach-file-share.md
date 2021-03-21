---
title: Windows Virtual Desktop set up file share MSIX app attach preview-Azure
description: Een bestands share instellen voor MSIX app attach voor Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 49a350b77958901aae5e54e82d856e4f3772702e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97930783"
---
# <a name="set-up-a-file-share-for-msix-app-attach-preview"></a>Een bestands share instellen voor het koppelen van MSIX-apps (preview-versie)

> [!IMPORTANT]
> MSIX app attach is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Alle MSIX-installatie kopieën moeten worden opgeslagen op een netwerk share die toegankelijk is voor gebruikers in een hostgroep met alleen-lezen machtigingen.

MSIX app attach (preview) heeft geen afhankelijkheden voor het type opslag infrastructuur dat door de bestands share wordt gebruikt. De overwegingen voor het koppelen van de MSIX-app zijn hetzelfde als die voor een FSLogix-share. Zie [opslag opties voor FSLogix-profiel containers in Windows virtueel bureau blad](store-fslogix-profile.md)voor meer informatie over opslag vereisten.

## <a name="performance-requirements"></a>Prestatievereisten

MSIX-app koppelen grootte limieten voor uw systeem zijn afhankelijk van het opslag type dat u gebruikt voor het opslaan van de VHD-of VHDx-bestanden, evenals de grootte beperkingen van de VHD-, VHSD-of CIM-bestanden en het bestands systeem.

In de volgende tabel ziet u een voor beeld van het aantal resources dat één MSIX-installatie kopie van 1 GB met één MSIX-app voor elke VM vereist:

| Resource             | Vereisten |
|----------------------|--------------|
| IOPs met stabiele status    | 1 IOPs       |
| Aanmelden bij computer opstarten | 10 IOPs      |
| Latentie              | 400 MS       |

De vereisten kunnen sterk variëren, afhankelijk van het aantal MSIX-toepassingen dat in de MSIX-installatie kopie wordt opgeslagen. Voor grotere MSIX-installatie kopieën moet u meer band breedte toewijzen.

### <a name="storage-recommendations"></a>Aanbevelingen voor opslag

Azure biedt meerdere opslag opties die kunnen worden gebruikt voor het koppelen van MISX-apps. We raden u aan Azure Files of Azure NetApp Files uit te voeren, omdat deze opties de beste waarde bieden tussen kosten-en beheer overhead. Met de artikel [opslag opties voor FSLogix-profiel containers in het virtuele bureau blad van Windows](store-fslogix-profile.md) vergelijkt u de verschillende Managed Storage-oplossingen Azure-aanbiedingen in de context van Windows virtueel bureau blad.

### <a name="optimize-msix-app-attach-performance"></a>Prestaties van MSIX-app-koppeling optimaliseren

Hier volgen enkele andere zaken die u kunt doen om de MSIX-koppelings prestaties van de app te optimaliseren:

- De opslag oplossing die u gebruikt voor het koppelen van MSIX-apps moet zich op dezelfde locatie van het Data Center bevindt als de sessie-hosts.
- Als u prestatie knelpunten wilt voor komen, moet u de volgende VHD-, VHDX-en CIM-bestanden uitsluiten van antivirus scans:
   
    - <MSIXAppAttachFileShare \> \* . SCHIJVEN
    - <MSIXAppAttachFileShare \> \* . INDELING
    - \\\\storageaccount.file.core.windows.net- \\ share \* \* . SCHIJVEN
    - \\\\storageaccount.file.core.windows.net- \\ share \* \* . INDELING
    - <MSIXAppAttachFileShare>. VRACHT
    - \\\\storageaccount.file.core.windows.net- \\ share \* \* . VRACHT

- Scheid de opslag infrastructuur voor de MSIX-app die is gekoppeld vanuit FSLogix-profiel containers.
- Alle VM-systeem accounts en gebruikers accounts moeten alleen-lezen-machtigingen hebben voor toegang tot de bestands share.
- Voor nood herstel plannen voor virtuele Windows-Bureau bladen moet de bestands share voor het koppelen van de MSIX-app in uw secundaire failover-locatie worden gerepliceerd. Zie [een plan voor bedrijfs continuïteit en herstel na nood gevallen instellen](disaster-recovery.md)voor meer informatie over herstel na nood gevallen.

## <a name="how-to-set-up-the-file-share"></a>De bestands share instellen

Het installatie proces voor de bestands share MSIX app attach is grotendeels hetzelfde als [het installatie proces voor bestands shares van het FSLogix-profiel](create-host-pools-user-profile.md). U moet echter wel verschillende machtigingen toewijzen aan gebruikers. Voor het koppelen van de MSIX-app zijn alleen-lezen-machtigingen vereist voor toegang tot de bestands share.

Als u uw MSIX-toepassingen in Azure Files opslaat, moet u voor uw sessie-hosts alle host-Vm's op basis van het toegangs beheer (RBAC) en bestands shares van het nieuwe technologie bestands systeem (NTFS) op de share toewijzen.

| Azure-object                      | Vereiste rol                                     | Functie                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| Session Host (VM-computer objecten)| Inzender voor opslagbestandsgegevens via SMB-share          | Lezen en uitvoeren, lezen, Mapinhoud weer geven  |
| Beheerders op bestands share              | Inzender met verhoogde bevoegdheden voor opslagbestandsgegevens via SMB-share | Volledig beheer                                  |
| Gebruikers op bestands share               | Inzender voor opslagbestandsgegevens via SMB-share          | Lezen en uitvoeren, lezen, Mapinhoud weer geven  |

Vm's voor de sessiehost toewijzen voor het opslag account en de bestands share:

1. Maak een beveiligings groep voor Active Directory Domain Services (AD DS).

2. Voeg de computer accounts voor alle hosts voor sessie-Vm's toe als leden van de groep.

3. Synchroniseer de AD DS groep naar Azure Active Directory (Azure AD).

4. Een opslagaccount maken.

5. Maak een bestands share onder het opslag account door de instructies in [een Azure-bestands share maken](../storage/files/storage-how-to-create-file-share.md#create-file-share)te volgen.

6. Voeg het opslag account toe aan AD DS door de instructies in [deel één te volgen: schakel AD DS-verificatie in voor uw Azure-bestands shares](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module).

7. Wijs de gesynchroniseerde AD DS groep toe aan Azure AD en wijs het opslag account toe aan de rol Inzender voor het opslaan van gegevens van de SMB-share.

8. Koppel de bestands share aan een sessiehost door de instructies in [deel twee te volgen: machtigingen op share niveau toewijzen aan een identiteit](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).

9. Ken NTFS-machtigingen voor de bestands share toe aan de groep AD DS.

10. Stel NTFS-machtigingen in voor de gebruikers accounts. U hebt een operationele eenheid (OE) nodig die afkomstig is van de AD DS waarvan de accounts in de virtuele machine deel uitmaken.

Nadat u de identiteit aan uw opslag hebt toegewezen, volgt u de instructies in de artikelen in de [volgende stappen](#next-steps) om andere vereiste machtigingen te verlenen aan de identiteit die u aan de vm's hebt toegewezen.

U moet er ook voor zorgen dat de virtuele machines van de sessiehost nieuwe NTFS-machtigingen (File System) hebben. U moet een container voor operationele eenheden hebben die is gebrond van Active Directory Domain Services (AD DS), en uw gebruikers moeten lid zijn van die operationele eenheid om deze machtigingen te kunnen gebruiken.

## <a name="next-steps"></a>Volgende stappen

Dit zijn de andere zaken die u moet doen nadat u de bestands share hebt ingesteld:

- Meer informatie over het instellen van Azure Active Directory Domain Services (AD DS) op [een profiel container maken met Azure files en AD DS](create-file-share.md).
- Meer informatie over het instellen van Azure Files en Azure AD DS bij [het maken van een profiel container met Azure files en azure AD DS](create-profile-container-adds.md).
- Meer informatie over het instellen van Azure NetApp Files voor MSIX-app-koppeling in [een profiel container maken met Azure NetApp files en AD DS](create-fslogix-profile-container.md).
- Meer informatie over het gebruik van een bestands share op basis van een virtuele machine op een [profiel container maken voor een hostgroep met een bestands share](create-host-pools-user-profile.md).

Wanneer u klaar bent, ziet u hier een aantal andere bronnen die u mogelijk handig vindt:

- Vraag onze vragen van de community over deze functie op het [virtuele bureau blad-TechCommunity van Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).
- U kunt ook feedback geven voor Windows virtueel bureau blad op de [Windows Virtual Desktop feedback hub](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).
- [Woorden lijst voor het toevoegen van MSIX-apps](app-attach-glossary.md)
- [Veelgestelde vragen over het koppelen van MSIX-apps](app-attach-faq.md)
