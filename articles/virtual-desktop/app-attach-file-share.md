---
title: Windows Virtual Desktop MSIX-app-attach voor bestands delen instellen - Azure
description: Een bestands share instellen voor msix-app-attach voor Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a2d4ebee02d85d10d5db8ec2de0bb1be334770dc
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717653"
---
# <a name="set-up-a-file-share-for-msix-app-attach"></a>Een bestands share instellen voor msix-app-attach

Alle MSIX-afbeeldingen moeten worden opgeslagen op een netwerk share die toegankelijk is voor gebruikers in een hostgroep met alleen-lezen machtigingen.

De MSIX-app-attach heeft geen afhankelijkheden van het type opslag-fabric dat door de bestands share wordt gebruikt. De overwegingen voor de MSIX-app-attach-share zijn hetzelfde als die voor een FSLogix-share. Zie Opslagopties voor [FSLogix-profielcontainers in](store-fslogix-profile.md)Windows Virtual Desktop voor meer informatie over opslagvereisten.

## <a name="performance-requirements"></a>Prestatievereisten

De limieten voor de grootte van de MSIX-app koppelen voor uw systeem zijn afhankelijk van het opslagtype dat u gebruikt voor het opslaan van de VHD- of VHDx-bestanden, evenals de groottebeperkingen van de VHD-, HADD- of CIM-bestanden en het bestandssysteem.

De volgende tabel geeft een voorbeeld van het aantal resources dat een enkele MSIX-afbeelding van 1 GB met één MSIX-app in de VM vereist voor elke VM:

| Resource             | Vereisten |
|----------------------|--------------|
| IOPS voor stabiele status    | 1 IOPS       |
| Aanmelden bij computer opstarten | 10 IOPS      |
| Latentie              | 400 ms       |

De vereisten kunnen sterk variëren, afhankelijk van hoeveel MSIX-verpakte toepassingen zijn opgeslagen in de MSIX-afbeelding. Voor grotere MSIX-afbeeldingen moet u meer bandbreedte toewijzen.

### <a name="storage-recommendations"></a>Aanbevelingen voor opslag

Azure biedt meerdere opslagopties die kunnen worden gebruikt voor het koppelen van MISX-apps. U kunt het beste Azure Files of Azure NetApp Files omdat deze opties de beste waarde bieden tussen kosten- en beheeroverhead. In het [artikel Opslagopties voor FSLogix-profielcontainers in Windows Virtual Desktop](store-fslogix-profile.md) vergelijkt u de verschillende beheerde opslagoplossingen die Azure biedt in de context van Windows Virtual Desktop.

### <a name="optimize-msix-app-attach-performance"></a>Prestaties van MSIX-app-attach optimaliseren

Hier zijn enkele andere dingen die u het beste kunt doen om de prestaties van het koppelen van MSIX-apps te optimaliseren:

- De opslagoplossing die u voor de MSIX-app-attach gebruikt, moet zich op dezelfde datacenterlocatie bevinden als de sessiehosts.
- Sluit de volgende VHD-, VHDX- en CIM-bestanden uit van antivirusscans om prestatieknelpunten te voorkomen:
   
    - <MSIXAppAttachFileShare \> \* . Vhd
    - <MSIXAppAttachFileShare \> \* . VHDX
    - \\\\storageaccount.file.core.windows.net \\ \* \* delen. Vhd
    - \\\\storageaccount.file.core.windows.net \\ \* \* delen. VHDX
    - <MSIXAppAttachFileShare>. Cim
    - \\\\storageaccount.file.core.windows.net \\ \* \* delen. Cim

- Scheid de opslag-fabric voor MSIX-app-attach van FSLogix-profielcontainers.
- Alle VM-systeemaccounts en gebruikersaccounts moeten alleen-lezenmachtigingen hebben voor toegang tot de bestands share.
- Alle noodherstelplannen voor Windows Virtual Desktop moeten het repliceren van de MSIX-app-bestands share koppelen op uw secundaire failoverlocatie. Zie Een plan voor bedrijfscontinuïteit en herstel na noodherstel instellen voor meer informatie over [herstel na noodherstel.](disaster-recovery.md)

## <a name="how-to-set-up-the-file-share"></a>De bestands share instellen

Het installatieproces voor de MSIX-app-bestands share koppelen is grotendeels hetzelfde als het installatieproces voor [FSLogix-profielbestands shares.](create-host-pools-user-profile.md) U moet gebruikers echter verschillende machtigingen toewijzen. Voor het koppelen van de MSIX-app zijn alleen-lezen machtigingen vereist voor toegang tot de bestands share.

Als u uw MSIX-toepassingen opslaat in Azure Files, moet u voor uw sessiehosts aan alle sessiehost-VM's zowel op rollen gebaseerd toegangsbeheer (RBAC) als bestands shareMachtigingen voor New Technology File System (NTFS) voor de share toewijzen.

| Azure-object                      | Vereiste rol                                     | Functie Rol                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| Sessiehost (VM-computerobjecten)| Inzender voor opslagbestandsgegevens via SMB-share          | Mapinhoud lezen en uitvoeren, lezen en weergeven  |
| Beheerders op bestands share              | Inzender met verhoogde bevoegdheden voor opslagbestandsgegevens via SMB-share | Volledig beheer                                  |
| Gebruikers op bestands share               | Inzender voor opslagbestandsgegevens via SMB-share          | Mapinhoud lezen en uitvoeren, lezen en weergeven  |

VM-machtigingen voor sessiehosts toewijzen voor het opslagaccount en de bestands share:

1. Maak een Active Directory Domain Services (AD DS) beveiligingsgroep.

2. Voeg de computeraccounts voor alle sessiehost-VM's toe als leden van de groep.

3. Synchroniseer de AD DS naar Azure Active Directory (Azure AD).

4. Een opslagaccount maken.

5. Maak een bestands share onder het opslagaccount door de instructies in [Een Azure-bestands share maken te volgen.](../storage/files/storage-how-to-create-file-share.md#create-a-file-share)

6. Voeg het opslagaccount toe aan AD DS door de instructies te volgen in Deel [1: verificatie AD DS uw Azure-bestands shares inschakelen.](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module)

7. Wijs de gesynchroniseerde AD DS toe aan Azure AD en wijs aan het opslagaccount de rol Inzender voor opslagbestandsgegevens SMB-share toe.

8. Als u de bestands share aan een sessiehost wilt toevoegen, volgt u de instructies in Deel 2: machtigingen op [shareniveau toewijzen aan een identiteit.](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)

9. Verleen NTFS-machtigingen voor de bestands share aan de AD DS groep.

10. Stel NTFS-machtigingen in voor de gebruikersaccounts. U hebt een OE (operating unit) nodig die afkomstig is van de AD DS waar de accounts in de VM bij horen.

Nadat u de identiteit aan uw opslag hebt toegewezen, volgt u de instructies [in](#next-steps) de artikelen in Volgende stappen om andere vereiste machtigingen te verlenen voor de identiteit die u hebt toegewezen aan de VM's.

U moet er ook voor zorgen dat uw sessiehost-VM's machtigingen hebben voor New Technology File System (NTFS). U moet een container voor operationele eenheden hebben die afkomstig is van Active Directory Domain Services (AD DS), en uw gebruikers moeten lid zijn van die operationele eenheid om deze machtigingen te kunnen gebruiken.

## <a name="next-steps"></a>Volgende stappen

Dit zijn de andere dingen die u moet doen nadat u de bestands share hebt ingesteld:

- Meer informatie over het instellen van Azure Active Directory Domain Services (AD DS) in Een profielcontainer maken met [Azure Files en AD DS.](create-file-share.md)
- Meer informatie over het instellen Azure Files en Azure AD DS in Een profielcontainer maken met [Azure Files en Azure AD DS.](create-profile-container-adds.md)
- Meer informatie over het instellen van Azure NetApp Files voor MSIX-app-attach in Een [profielcontainer maken met Azure NetApp Files en AD DS](create-fslogix-profile-container.md).
- Meer informatie over het gebruik van een bestands share op basis van een virtuele machine in Een profielcontainer voor een [hostgroep maken met behulp van een bestands share](create-host-pools-user-profile.md).

Wanneer u klaar bent, vindt u hier enkele andere resources die mogelijk nuttig zijn:

- Stel onze communityvragen over deze functie op de [Windows Virtual Desktop TechCommunity.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- U kunt ook feedback geven voor Windows Virtual Desktop in Windows Virtual Desktop [feedbackhub.](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)
- [Verklarende woordenlijst voor MSIX-app-koppelen](app-attach-glossary.md)
- [Veelgestelde vragen over het koppelen van MSIX-apps](app-attach-faq.md)
