---
title: Container bestanden voor het FSLogix-Profiel van Windows virtueel bureau blad-Azure
description: In dit artikel worden FSLogix-profiel containers in virtueel bureau blad van Windows en Azure files beschreven.
author: Heidilohr
ms.topic: conceptual
ms.date: 01/04/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: ef7f19d835f4fef1a911da01015321b2dda67682
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861883"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix-profielcontainers en Azure Files

De Windows Virtual Desktop-service beveelt FSLogix-profiel containers aan als een gebruikers profiel oplossing. FSLogix is ontworpen om profielen te roamen in omgevingen met externe computers, zoals Windows Virtual Desktop. Een volledig gebruikersprofiel wordt opgeslagen in één container. Bij het aanmelden wordt deze container dynamisch gekoppeld aan de computer omgeving met behulp van systeem eigen ondersteunde virtuele harde schijven (VHD) en Hyper-V virtuele harde schijf (VHDX). Het gebruikersprofiel is onmiddellijk beschikbaar en wordt in het systeem op dezelfde manier weergegeven als een systeemeigen gebruikersprofiel. In dit artikel wordt beschreven hoe FSLogix-profiel containers die worden gebruikt met Azure Files functie in Windows virtueel bureau blad.

>[!NOTE]
>Zie [opslag opties voor FSLogix-profiel containers](store-fslogix-profile.md)als u op zoek bent naar het vergelijkings materiaal over de verschillende FSLogix-profiel container opslag opties in Azure.

## <a name="user-profiles"></a>Gebruikersprofielen

Een gebruikers profiel bevat gegevens elementen over een individu, inclusief configuratie gegevens zoals bureaublad instellingen, permanente netwerk verbindingen en toepassings instellingen. Windows maakt standaard een lokaal gebruikers profiel dat nauw geïntegreerd is met het besturings systeem.

Een profiel voor externe gebruikers biedt een partitie tussen gebruikers gegevens en het besturings systeem. Hiermee kan het besturings systeem worden vervangen of gewijzigd zonder dat dit van invloed is op de gebruikers gegevens. In Extern bureaublad Session Host (RDSH) en Virtual Desktop Infrastructure (VDI) kan het besturings systeem om de volgende redenen worden vervangen:

- Een upgrade van het besturings systeem
- Een bestaande virtuele machine (VM) vervangen
- Een gebruiker die deel uitmaakt van een gegroepeerde (niet-permanente) RDSH-of VDI-omgeving

Micro soft-producten kunnen worden gebruikt voor verschillende technologieën voor externe gebruikers profielen, waaronder deze technologieën:
- Zwervende gebruikers profielen (ZWERVENDE gebruikers profielen)
- Gebruikers profiel schijven (UDP)
- Enter prise State roaming (ESR)

UPD en ZWERVENDE gebruikers profielen zijn de meest gebruikte technologieën voor gebruikers profielen in de omgevingen met Extern bureaublad sessiehost en virtuele harde schijven (VHD).

### <a name="challenges-with-previous-user-profile-technologies"></a>Uitdagingen met eerdere gebruikers profiel technologieën

Bestaande en verouderde micro soft-oplossingen voor gebruikers profielen zijn met diverse uitdagingen gearriveerd. Geen van de voor gaande oplossingen heeft alle gebruikers profielen afgehandeld die bij een RDSH-of VDI-omgeving worden geleverd. Bijvoorbeeld, UPD kan geen grote OST-bestanden verwerken en ZWERVENDE gebruikers profielen de moderne instellingen niet behouden.

#### <a name="functionality"></a>Functionaliteit

In de volgende tabel worden de voor delen en beperkingen van eerdere gebruikers profiel technologieën weer gegeven.

| Technologie | Moderne instellingen | Win32-instellingen | BESTURINGSSYSTEEM instellingen | Gebruikersgegevens | Ondersteund op de server-SKU | Back-end-opslag in azure | On-premises back-end-opslag | Versie ondersteuning | Volgende aanmeldings tijd |Opmerkingen|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Gebruikers profiel schijven (UDP)** | Ja | Ja | Ja | Ja | Ja | Nee | Ja | Win 7 + | Ja | |
| **Zwervend gebruikers profiel (ZWERVENDE gebruikers profielen), onderhouds modus** | Nee | Ja | Ja | Ja | Ja| Nee | Ja | Win 7 + | Nee | |
| **Enterprise State Roaming (ESR)** | Ja | Nee | Ja | Nee | Opmerkingen weer geven | Ja | Nee | Win 10 | Nee | Functies op de server-SKU, maar geen ondersteunende gebruikers interface |
| **User Experience Virtualization (UE-V)** | Ja | Ja | Ja | Nee | Ja | Nee | Ja | Win 7 + | Nee |  |
| **OneDrive-Cloud bestanden** | Nee | Nee | Nee | Ja | Opmerkingen weer geven | Opmerkingen weer geven  | Opmerkingen weer geven | Win 10 RS3 | Nee | Niet getest op server-SKU. Back-end-opslag in Azure is afhankelijk van de synchronisatieclient. On-premises back-end-opslag moet een synchronisatieclient hebben. |

#### <a name="performance"></a>Prestaties

UPD vereist [opslagruimten direct (S2D)](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) om prestatie vereisten te verhelpen. UPD maakt gebruik van het SMB-protocol (Server Message Block). Het profiel wordt gekopieerd naar de virtuele machine waarin de gebruiker wordt geregistreerd. UPD met S2D is de oplossing die we voor het virtuele bureau blad van Windows raden.

#### <a name="cost"></a>Kosten

Terwijl S2D-clusters de benodigde prestaties behaalden, zijn de kosten duur voor zakelijke klanten, maar met name kostbaar voor klanten met een kleine en middel grote onderneming (SMB). Voor deze oplossing betalen bedrijven voor opslag schijven, samen met de kosten van de virtuele machines die gebruikmaken van de schijven voor een share.

#### <a name="administrative-overhead"></a>Meer beheertaken

S2D-clusters vereisen een besturings systeem dat met een patch in een beveiligde status wordt bijgewerkt en onderhouden. Deze processen en de complexiteit van het instellen van S2D-nood herstel, maken S2D alleen voor ondernemingen met een speciaal IT-personeel.

## <a name="fslogix-profile-containers"></a>FSLogix-profiel containers

Op 19 november 2018 heeft [micro soft FSLogix aangeschaft](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix is een oplossing voor veel uitdagingen met een profiel container. De sleutel is onder andere:

- **Prestaties:** De [FSLogix-profiel containers](/fslogix/configure-profile-container-tutorial/) zijn hoge prestaties en kunnen prestatie problemen oplossen die de Exchange-modus met een historische cache hebben geblokkeerd.
- **OneDrive:** Zonder FSLogix-profiel containers wordt OneDrive voor bedrijven niet ondersteund in niet-permanente RDSH-of VDI-omgevingen. Op de pagina voor het [ondersteunen van OneDrive](/onedrive/sync-vdi-support) kan u zien hoe ze communiceren. Zie [de Synchronisatieclient gebruiken op virtuele Bureau bladen](/deployoffice/rds-onedrive-business-vdi/)voor meer informatie.
- **Aanvullende mappen:** FSLogix biedt de mogelijkheid om gebruikers profielen uit te breiden voor het toevoegen van extra mappen.

Sinds de aanschaf is micro soft begonnen met het vervangen van bestaande gebruikers profiel oplossingen, zoals UPD, met FSLogix-profiel containers.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Integratie met Azure Active Directory-domein service Azure Files

De prestaties en functies van FSLogix-profiel containers profiteren van de Cloud. Op 2019 7 augustus heeft Microsoft Azure bestanden de algemene Beschik baarheid aangekondigd van [Azure files verificatie met Azure Active Directory Domain Service (AD DS)](../storage/files/storage-files-active-directory-overview.md). Door zowel kosten als administratieve overhead te adresseren, Azure Files met Azure AD DS-verificatie een Premium-oplossing voor gebruikers profielen in de virtueel-bureaublad service van Windows.

## <a name="best-practices-for-windows-virtual-desktop"></a>Aanbevolen procedures voor virtueel bureau blad van Windows

Virtueel bureau blad van Windows biedt volledige controle over de grootte, het type en het aantal Vm's dat door klanten wordt gebruikt. Zie [Wat is Windows virtueel bureau blad?](overview.md)voor meer informatie.

Om ervoor te zorgen dat uw virtuele Windows-bureau blad-omgeving de best practices volgt:

- Azure Files Storage-account moet zich in dezelfde regio bevinden als de virtuele machines van de host.
- Azure Files machtigingen moeten overeenkomen met de machtigingen die zijn beschreven in [vereisten-profiel containers](/fslogix/fslogix-storage-config-ht).
- Elke VM van een hostgroep moet zijn gebaseerd op hetzelfde type en dezelfde grootte-VM op basis van dezelfde hoofd installatie kopie.
- Elke VM van een hostgroep moet zich in dezelfde resource groep beheersen voor het beheren, schalen en bijwerken.
- Voor optimale prestaties moeten de opslag oplossing en de FSLogix-profiel container zich op dezelfde locatie in het Data Center bevinden.
- Het opslag account met de master installatie kopie moet zich in dezelfde regio en hetzelfde abonnement bevinden als de virtuele machines die worden ingericht.

## <a name="next-steps"></a>Volgende stappen

Gebruik de volgende hand leidingen om een virtuele Windows-desktop omgeving in te stellen.

- Zie [een Tenant maken in Windows virtueel bureau blad](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)om te beginnen met het bouwen van de oplossing voor desktop virtualisatie.
- Zie [een hostgroep maken met Azure Marketplace](create-host-pools-azure-marketplace.md)voor meer informatie over het maken van een hostgroep in uw virtuele Windows-bureau blad-Tenant.
- Zie [Azure Files share instellen](/azure/storage/files/storage-files-active-directory-enable/)voor meer informatie over het instellen van volledig beheerde bestands shares in de Cloud.
- Zie [een profiel container maken voor een hostgroep met een bestands share voor](create-host-pools-user-profile.md)meer informatie over het configureren van FSLogix-profiel containers.
- Zie [app-groepen beheren voor Windows virtueel bureau blad](manage-app-groups.md)om gebruikers toe te wijzen aan een hostgroep.
- Zie [verbinding maken met het virtuele bureau blad van Windows](connect-web.md)voor toegang tot uw virtuele bureau blad-resources via een webbrowser.
