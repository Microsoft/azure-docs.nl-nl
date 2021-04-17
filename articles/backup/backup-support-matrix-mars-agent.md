---
title: Ondersteuningsmatrix voor de MARS-agent
description: Dit artikel bevat een Azure Backup ondersteuning wanneer u een back-up van computers met de MARS-agent (Microsoft Azure Recovery Services) gebruikt.
ms.date: 04/09/2021
ms.topic: conceptual
ms.openlocfilehash: 20bca0e9ca9dfd735501e68bd0e5a6d69d2ef68e
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576496"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Ondersteuningsmatrix voor back-up met Microsoft Azure Recovery Services-agent (MARS)

U kunt de Azure Backup service gebruiken [om](backup-overview.md) back-up te maken van on-premises machines en apps en om back-up te maken van virtuele Azure-machines (VM's). In dit artikel vindt u een overzicht van de ondersteuningsinstellingen en -beperkingen wanneer u de MARS-agent (Microsoft Azure Recovery Services) gebruikt om back-up van machines te maken.

## <a name="the-mars-agent"></a>De MARS-agent

Azure Backup gebruikt de MARS-agent om back-ups te maken van gegevens van on-premises machines en Azure-VM's naar een Recovery Services-back-upkluis in Azure. De MARS-agent kan:

- Voer uit op on-premises Windows-machines, zodat ze rechtstreeks een back-up kunnen maken van een Recovery Services-kluis in Azure.
- Voer uit op Windows-VM's, zodat ze rechtstreeks een back-up naar een kluis kunnen maken.
- Uitvoeren op Microsoft Azure Backup Server (MABS) of een System Center Data Protection Manager (DPM)-server. In dit scenario maken machines en workloads een back-up naar MABS of naar de DPM-server. De MARS-agent back-upt deze server vervolgens naar een kluis in Azure.

> [!NOTE]
>Azure Backup biedt geen ondersteuning voor automatische aanpassing van de klok voor zomertijd (DST). Wijzig het beleid om ervoor te zorgen dat er rekening wordt gehouden met zomer- en wintertijd om discrepantie tussen de werkelijke tijd en de geplande back-uptijd te voorkomen.

Uw back-upopties zijn afhankelijk van waar de agent is geïnstalleerd. Zie architectuur Azure Backup [met behulp van de MARS-agent voor meer informatie.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders) Zie Back-up maken naar DPM of MABS voor meer informatie over mabs- en [DPM-back-uparchitectuur.](backup-architecture.md#architecture-back-up-to-dpmmabs) Zie ook [vereisten voor](backup-support-matrix-mabs-dpm.md) de back-uparchitectuur.

**Installatie** | **Details**
--- | ---
De nieuwste MARS-agent downloaden | U kunt de nieuwste versie van de agent downloaden uit de kluis of [rechtstreeks downloaden.](https://aka.ms/azurebackup_agent)
Rechtstreeks op een computer installeren | U kunt de MARS-agent rechtstreeks installeren op een on-premises Windows-server of op een Windows-VM met een van de [ondersteunde besturingssystemen.](./backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)
Installeren op een back-upserver | Wanneer u DPM of MABS in stelt om een back-up te maken naar Azure, downloadt en installeert u de MARS-agent op de server. U kunt de agent installeren op [ondersteunde besturingssystemen](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) in de ondersteuningsmatrix van de back-upserver.

> [!NOTE]
> Azure-VM's die zijn ingeschakeld voor back-up hebben standaard een Azure Backup-extensie. Deze extensie back-up van de hele VM. U kunt de MARS-agent naast de extensie installeren en uitvoeren op een Azure-VM als u een back-up wilt maken van specifieke mappen en bestanden, in plaats van de volledige VM.
> Wanneer u de MARS-agent op een virtuele Azure-VM uit te voeren, maakt deze een back-up van bestanden of mappen die zich in tijdelijke opslag op de VM. Back-ups mislukken als de bestanden of mappen uit de tijdelijke opslag worden verwijderd of als de tijdelijke opslag wordt verwijderd.

## <a name="cache-folder-support"></a>Ondersteuning voor cachemappen

Wanneer u de MARS-agent gebruikt om een back-up te maken van gegevens, maakt de agent een momentopname van de gegevens en slaat deze op in een lokale cachemap voordat deze de gegevens naar Azure verzendt. De cachemap (scratch) heeft verschillende vereisten:

**Cache** | **Details**
--- | ---
Grootte |  Vrije ruimte in de cachemap moet ten minste 5 tot 10 procent van de totale grootte van uw back-upgegevens zijn.
Locatie | De cachemap moet lokaal worden opgeslagen op de computer waar een back-up van wordt gemaakt en moet online zijn. De cachemap mag zich niet op een netwerk share, op verwisselbare media of op een offline volume.
Map | De cachemap mag niet worden versleuteld op een ontdubbeld volume of in een map die gecomprimeerd is, verspreid is of die een reparsepunt heeft.
Locatiewijzigingen | U kunt de cachelocatie wijzigen door de back-up-engine ( ) te stoppen en `net stop bengine` de cachemap naar een nieuw station te kopiëren. (Zorg ervoor dat het nieuwe station voldoende ruimte heeft.) Werk vervolgens twee registeritems onder **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** **(Config/ScratchLocation** en **Config/CloudBackupProvider/ScratchLocation)** bij naar de nieuwe locatie en start de engine opnieuw.

## <a name="networking-and-access-support"></a>Ondersteuning voor netwerken en toegang

### <a name="url-and-ip-access"></a>URL- en IP-toegang

De MARS-agent moet toegang hebben tot deze URL's:

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *. MicrosoftOnline.com
- *. Windows.net
- `www.msftconnecttest.com`

En voor deze IP-adressen:

- 20.190.128.0/18
- 40.126.0.0/18

Voor toegang tot alle hierboven vermelde URL's en IP-adressen wordt het HTTPS-protocol op poort 443 gebruikt.

Wanneer u een back-up maakt van bestanden en mappen van virtuele Azure-machines met behulp van de MARS-agent, moet het virtuele Azure-netwerk ook worden geconfigureerd om toegang toe te staan. Als u netwerkbeveiligingsgroepen (NSG's) gebruikt, gebruikt u de servicetag *AzureBackup* om uitgaande toegang tot Azure Backup toe te staan. Naast de Azure Backup-tag moet u ook connectiviteit voor verificatie en gegevensoverdracht toestaan met behulp van [NSG-regels](../virtual-network/network-security-groups-overview.md#service-tags) voor Azure AD (*AzureActiveDirectory*) en Azure Storage (*Storage*). In de volgende stappen wordt het proces voor het maken van een regel voor de Azure Backup-tag beschreven:

1. In **Alle services** gaat u naar **Netwerkbeveiligingsgroepen** en selecteert u de netwerkbeveiligingsgroep.
2. Selecteer de optie **Uitgaande beveiligingsregels** onder **Instellingen**.
3. Selecteer **Toevoegen**. Voer alle vereiste details in voor het maken van een nieuwe regel, zoals beschreven in de [instellingen voor beveiligingsregels](../virtual-network/manage-network-security-group.md#security-rule-settings). Controleer of de optie **Doel** is ingesteld op *Servicetag* en **Doelservicetag** is ingesteld op *AzureBackup*.
4. Selecteer **Toevoegen om** de zojuist gemaakte uitgaande beveiligingsregel op te slaan.

U kunt op vergelijkbare wijze ook uitgaande NSG-beveiligingsregels maken voor Azure Storage en Azure AD. Zie [dit artikel](../virtual-network/service-tags-overview.md) voor meer informatie over servicetags.

### <a name="azure-expressroute-support"></a>Azure ExpressRoute ondersteuning

U kunt een back-up maken van uw gegevens via Azure ExpressRoute met openbare peering (beschikbaar voor oude circuits) en Microsoft-peering. Back-up via persoonlijke peering wordt niet ondersteund.

Met openbare peering: zorg voor toegang tot de volgende domeinen/adressen:

* URL's
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP-adressen
  * 20.190.128.0/18
  * 40.126.0.0/18

Met Microsoft-peering selecteert u de volgende services/regio's en relevante communitywaarden:

- Azure Backup (op basis van de locatie van uw Recovery Services-kluis)
- Azure Active Directory (12076:5060)
- Azure Storage (op basis van de locatie van uw Recovery Services-kluis)

Zie routeringsvereisten voor [ExpressRoute voor meer informatie.](../expressroute/expressroute-routing.md#bgp)

>[!NOTE]
>Openbare peering is afgeschaft voor nieuwe circuits.

### <a name="private-endpoint-support"></a>Ondersteuning voor privé-eindpunten

U kunt nu privé-eindpunten gebruiken om veilig een back-up van uw gegevens te maken van servers naar uw Recovery Services-kluis. Aangezien Azure Active Directory momenteel geen ondersteuning biedt voor privé-eindpunten, moeten IP's en FQDN's die vereist zijn voor Azure Active Directory afzonderlijk uitgaande toegang worden toegestaan.

Wanneer u de MARS-agent gebruikt om een back-up te maken van uw on-premises resources, moet u ervoor zorgen dat uw on-premises netwerk (met uw resources waar een back-up van moet worden gemaakt) is verbonden met het Azure VNet dat een privé-eindpunt voor de kluis bevat. U kunt vervolgens doorgaan met het installeren van de MARS-agent en back-up configureren. U moet er echter voor zorgen dat alle communicatie voor back-up alleen via het peered netwerk gebeurt.

Als u privé-eindpunten voor de kluis verwijdert nadat er een MARS-agent is geregistreerd, moet u de container opnieuw registreren bij de kluis. U hoeft de beveiliging voor deze niet te stoppen.

Lees meer over [privé-eindpunten voor Azure Backup.](private-endpoints.md)

### <a name="throttling-support"></a>Ondersteuning voor beperking

**Functie** | **Details**
--- | ---
Bandbreedtebeheer | Ondersteund. Gebruik in de MARS-agent Eigenschappen **wijzigen om** de bandbreedte aan te passen.
Netwerkbeperking | Niet beschikbaar voor computers met een back-up met Windows Server 2008 R2, Windows Server 2008 SP2 of Windows 7.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

>[!NOTE]
> De MARS-agent biedt geen ondersteuning voor Windows Server Core-SKU's.

U kunt de MARS-agent gebruiken om rechtstreeks een back-up te maken naar Azure op de hieronder vermelde besturingssystemen die worden uitgevoerd op:

1. On-premises Windows-servers
2. Azure-VM's met Windows

De besturingssystemen moeten 64-bits zijn en moeten de nieuwste servicespacks en updates uitvoeren. De volgende tabel bevat een overzicht van deze besturingssystemen:

**Besturingssysteem** | **Bestanden/mappen** | **Systeemtoestand** | **Software-/modulevereisten**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Ja | Nee |  Controleer de bijbehorende serverversie op software-/modulevereisten
Windows 8.1 (Enterprise, Pro)| Ja |Nee | Controleer de bijbehorende serverversie op software-/modulevereisten
Windows 8 (Enterprise, Pro) | Ja | Nee | Controleer de bijbehorende serverversie op software-/modulevereisten
Windows Server 2016 (Standard, Datacenter, Essentials) | Ja | Ja | - .NET 4.5 <br> - Windows PowerShell <br> - Meest recente compatibele Microsoft VC++ Redistributable <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Ja | Ja | - .NET 4.5 <br> - Windows PowerShell <br> - Meest recente compatibele Microsoft VC++ Redistributable <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 (Standard, Datacenter, Foundation) | Ja | Ja |- .NET 4.5 <br> -Windows PowerShell <br> - Meest recente compatibele Microsoft VC++ Redistributable <br> - Microsoft Management Console (MMC) 3.0 <br> - Deployment Image Servicing and Management (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | Ja | Nee | - .NET 4.5 <br> - Windows PowerShell <br> - Meest recente compatibele Microsoft VC++ Redistributable <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2019 (Standard, Datacenter, Essentials) | Ja | Ja | - .NET 4.5 <br> - Windows PowerShell <br> - Meest recente compatibele Microsoft VC++ Redistributable <br> - Microsoft Management Console (MMC) 3.0

Zie Supported [MABS and DPM operating systems (Ondersteunde MABS- en DPM-besturingssystemen) voor meer informatie.](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)

### <a name="operating-systems-at-end-of-support"></a>Besturingssystemen aan het einde van de ondersteuning

De volgende besturingssystemen staan aan het einde van de ondersteuning en het wordt sterk aanbevolen om het besturingssysteem bij te werken om beveiligd te blijven.

Als bestaande toezeggingen verhinderen dat het besturingssysteem wordt ge upgraden, kunt u overwegen de Windows-servers te migreren naar azure-VM's en back-ups van virtuele Azure-VM's te gebruiken om beveiligd te blijven. Ga hier [naar de migratiepagina](https://azure.microsoft.com/migration/windows-server/) voor meer informatie over het migreren van uw Windows-server.

Voor on-premises of gehoste omgevingen, waar u het besturingssysteem niet kunt upgraden of migreren naar Azure, activeert u Uitgebreide beveiligingsupdates voor de machines om beveiligd en ondersteund te blijven. U ziet dat alleen specifieke edities in aanmerking komen voor uitgebreide beveiligingsupdates. Ga naar de [pagina Met veelgestelde](https://www.microsoft.com/windows-server/extended-security-updates) vragen voor meer informatie.

| **Besturingssysteem**                                       | **Bestanden/mappen** | **Systeemtoestand** | **Software-/modulevereisten**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Ja               | Nee                 | Controleer de bijbehorende serverversie op software-/modulevereisten |
| Windows Server 2008 R2 (Standard, Enterprise, Datacenter, Foundation) | Ja               | Ja                | - .NET 3.5, .NET 4.5 <br>  - Windows PowerShell <br>  - Compatibel Microsoft VC++ Redistributable <br>  - Microsoft Management Console (MMC) 3.0 <br>  - Deployment Image Servicing and Management (DISM.exe) |
| Windows Server 2008 SP2 (Standard, Datacenter, Foundation)  | Ja               | Nee                 | - .NET 3.5, .NET 4.5 <br>  - Windows PowerShell <br>  - Compatibel Microsoft VC++ Redistributable <br>  - Microsoft Management Console (MMC) 3.0 <br>  - Deployment Image Servicing and Management (DISM.exe) <br>  - Virtual Server 2005 base + KB948515 |

## <a name="backup-limits"></a>Back-uplimieten

### <a name="size-limits"></a>Groottelimieten

Azure Backup beperkt de grootte van een bestands- of mapgegevensbron waar een back-up van kan worden maken. De items die u vanaf één volume back-upt, mogen niet groter zijn dan de grootten die in deze tabel worden samengevat:

**Besturingssysteem** | **Maximale grootte**
--- | ---
Windows Server 2012 of hoger |54.400 GB
Windows Server 2008 R2 SP1 |1700 GB
Windows Server 2008 SP2| 1700 GB
Windows 8 of hoger| 54.400 GB
Windows 7| 1700 GB

### <a name="minimum-retention-limits"></a>Minimale bewaarlimieten

Hier volgen de minimale bewaarperioden die kunnen worden ingesteld voor de verschillende herstelpunten:

|Herstelpunt |Duur  |
|---------|---------|
|Dagelijks herstelpunt    |   7 dagen      |
|Wekelijks herstelpunt     |    4 weken     |
|Maandelijks herstelpunt    |   3 maanden      |
|Jaarlijks herstelpunt  |      1 jaar   |

### <a name="other-limitations"></a>Andere beperkingen

- MARS biedt geen ondersteuning voor de beveiliging van meerdere machines met dezelfde naam voor één kluis.

## <a name="supported-file-types-for-backup"></a>Ondersteunde bestandstypen voor back-up

**Type** | **Ondersteuning**
--- | ---
Gecodeerde<sup>*</sup>| Ondersteund.
Gecomprimeerd | Ondersteund.
Sparse | Ondersteund.
Gecomprimeerd en sparse |Ondersteund.
Vaste koppelingen| Wordt niet ondersteund. Overgeslagen.
Reparsepunt| Wordt niet ondersteund. Overgeslagen.
Versleuteld en sparse |Wordt niet ondersteund. Overgeslagen.
Gecomprimeerde stroom| Wordt niet ondersteund. Overgeslagen.
Sparse stroom| Wordt niet ondersteund. Overgeslagen.
OneDrive (gesynchroniseerde bestanden zijn sparse-streams)| Wordt niet ondersteund.
Mappen met DSF-replicatie ingeschakeld | Wordt niet ondersteund.

\* Zorg ervoor dat de MARS-agent toegang heeft tot de vereiste certificaten voor toegang tot de versleutelde bestanden. Ontoegankelijke bestanden worden overgeslagen.

## <a name="supported-drives-or-volumes-for-backup"></a>Ondersteunde stations of volumes voor back-up

**Station/volume** | **Ondersteuning** | **Details**
--- | --- | ---
Alleen-lezenvolumes| Niet ondersteund | Volume Copy Shadow Service (VSS) werkt alleen als het volume beschrijfbaar is.
Offlinevolumes| Niet ondersteund |VSS werkt alleen als het volume online is.
Netwerk delen| Niet ondersteund |Het volume moet lokaal zijn op de server.
Met BitLocker vergrendelde volumes| Niet ondersteund |Het volume moet worden ontgrendeld voordat de back-up wordt gestart.
Bestandssysteemidentificatie| Niet ondersteund |Alleen NTFS wordt ondersteund.
Verwisselbare media| Niet ondersteund |Alle back-upitembronnen moeten een *vaste* status hebben.
Ontdubbelde stations | Ondersteund | Azure Backup converteert ontdubbelde gegevens naar normale gegevens. De gegevens worden geoptimaliseerd, versleuteld, opgeslagen en verzonden naar de kluis.

## <a name="support-for-initial-offline-backup"></a>Ondersteuning voor eerste offline back-up

Azure Backup ondersteunt *offline seeding om* initiële back-upgegevens over te dragen naar Azure met behulp van schijven. Deze ondersteuning is handig als uw eerste back-up waarschijnlijk het groottebereik van terabytes (TB's) heeft. Offline back-up wordt ondersteund voor:

- Directe back-up van bestanden en mappen op on-premises machines met de MARS-agent.
- Back-up van workloads en bestanden van een DPM-server of MABS.

Offline back-up kan niet worden gebruikt voor systeemtoestandsbestanden.

## <a name="support-for-data-restoration"></a>Ondersteuning voor gegevensherstel

Met de [functie Direct](backup-instant-restore-capability.md) herstellen van Azure Backup kunt u gegevens herstellen voordat deze naar de kluis worden gekopieerd. Op de computer waar u een back-up van wilt maken, moet .NET Framework 4.5.2 of hoger worden uitgevoerd.

Back-ups kunnen niet worden hersteld naar een doelmachine met een eerdere versie van het besturingssysteem. Een back-up van een computer met Windows 7 kan bijvoorbeeld op een Windows 8 of hoger worden hersteld. Maar een back-up van een computer met Windows 8 kan niet worden hersteld op een computer met Windows 7.

## <a name="previous-mars-agent-versions"></a>Vorige versies van MARS-agent

De volgende tabel bevat de vorige versies van de agent met hun downloadkoppelingen. U wordt aangeraden de versie van de agent bij te werken naar de nieuwste versie, zodat u gebruik kunt maken van de nieuwste functies en optimale prestaties.

**Versies** | **KB-artikelen**
--- | ---
[2.0.9145.0](https://download.microsoft.com/download/4/5/E/45EB38B4-2DA7-45FA-92E1-5CA1E23D18D1/MARSAgentInstaller.exe) | Niet beschikbaar
[2.0.9151.0](https://download.microsoft.com/download/7/1/7/7177B70A-51E8-434D-BDF2-FA3A09E917D6/MARSAgentInstaller.exe) | Niet beschikbaar
[2.0.9153.0](https://download.microsoft.com/download/3/D/D/3DD8A2FF-AC48-4A62-8566-B2C05F0BCCD0/MARSAgentInstaller.exe) | Niet beschikbaar
[2.0.9162.0](https://download.microsoft.com/download/0/1/0/010E598E-6289-47DB-872A-FFAF5030E6BE/MARSAgentInstaller.exe) | Niet beschikbaar
[2.0.9169.0](https://download.microsoft.com/download/f/7/1/f716c719-24bc-4337-af48-113baddc14d8/MARSAgentInstaller.exe) | [4515971](https://support.microsoft.com/help/4538314)
[2.0.9170.0](https://download.microsoft.com/download/1/8/7/187ca9a9-a6e5-45f0-928f-9a843d84aed5/MARSAgentInstaller.exe) | Niet beschikbaar
[2.0.9173.0](https://download.microsoft.com/download/7/9/2/79263a35-de87-4ba6-9732-65563a4274b6/MARSAgentInstaller.exe) | [4538314](https://support.microsoft.com/help/4538314)
[2.0.9177.0](https://download.microsoft.com/download/3/0/4/304d3cdf-b123-42ee-ad03-98fb895bc38f/MARSAgentInstaller.exe) | Niet beschikbaar
[2.0.9181.0](https://download.microsoft.com/download/6/6/9/6698bc49-e30b-4a3e-a1f4-5c859beafdcc/MARSAgentInstaller.exe) | Niet beschikbaar
[2.0.9190.0](https://download.microsoft.com/download/a/c/e/aceffec0-794e-4259-8107-92a3f6c10f55/MARSAgentInstaller.exe) | [4575948](https://support.microsoft.com/help/4575948)
[2.0.9195.0](https://download.microsoft.com/download/6/1/3/613b70a7-f400-4806-9d98-ae26aeb70be9/MARSAgentInstaller.exe) | [4582474](https://support.microsoft.com/help/4582474)
[2.0.9197.0](https://download.microsoft.com/download/2/7/5/27531ace-3100-43bc-b4af-7367680ea66b/MARSAgentInstaller.exe) | [4589598](https://support.microsoft.com/help/4589598)
[2.0.9207.0](https://download.microsoft.com/download/b/5/a/b5a29638-1cef-4906-b704-4d3d914af76e/MARSAgentInstaller.exe) | [5001305](https://support.microsoft.com/help/5001305)

>[!NOTE]
>Mars-agentversies met kleine verbeteringen in de betrouwbaarheid en prestaties hebben geen KB-artikel.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [back-uparchitectuur die gebruikmaakt van de MARS-agent](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Ontdek wat er wordt ondersteund wanneer u de MARS-agent op MABS of [een DPM-server kunt uitvoeren.](backup-support-matrix-mabs-dpm.md)
