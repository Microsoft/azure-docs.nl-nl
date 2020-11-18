---
title: Aanbevolen beveiligings procedures voor IaaS-workloads in azure | Microsoft Docs
description: " De migratie van werk belastingen naar Azure IaaS biedt mogelijkheden om onze ontwerpen opnieuw te evalueren "
services: security
documentationcenter: na
author: terrylanfear
manager: rkarlin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 9b9a83cf71dfa7658c34c3c98f8d12a056adad0c
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698781"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Best practices voor beveiliging voor IaaS-workloads in Azure
In dit artikel worden de aanbevolen beveiligings procedures voor Vm's en besturings systemen beschreven.

De aanbevolen procedures zijn gebaseerd op een advies van de mening en ze werken met de huidige mogelijkheden en functie sets van het Azure-platform. Omdat meningen en technologieën in de loop van de tijd kunnen veranderen, wordt dit artikel bijgewerkt om deze wijzigingen weer te geven.

[Azure virtual machines (vm's)](../../virtual-machines/index.yml) zijn in de meeste IaaS-scenario's (Infrastructure as a Service) de belangrijkste werk belasting voor organisaties die gebruikmaken van Cloud Computing. Dit feit is duidelijk in [hybride scenario's](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) waarin organisaties langzaam werk belastingen naar de Cloud willen migreren. In dergelijke scenario's volgt u de [algemene beveiligings overwegingen voor IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)en past u aanbevolen beveiligings procedures toe op al uw virtuele machines.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Vm's beveiligen met behulp van verificatie en toegangs beheer
De eerste stap bij het beveiligen van uw Vm's is om ervoor te zorgen dat alleen geautoriseerde gebruikers nieuwe Vm's kunnen instellen en virtuele machines voor toegang krijgen.

> [!NOTE]
> Als u de beveiliging van Linux-Vm's in azure wilt verbeteren, kunt u integreren met Azure AD-verificatie. Wanneer u [Azure AD-verificatie voor Linux-vm's](../../virtual-machines/linux/login-using-aad.md)gebruikt, beheert en afdwingt u de mogelijkheid om toegang tot de vm's toe te staan of te weigeren.
>
>

**Best Practice**: beheer de toegang tot de virtuele machine.   
**Details**: gebruik [Azure-beleids regels](../../governance/policy/overview.md) om conventies voor resources in uw organisatie in te stellen en aangepaste beleids regels te maken. Deze beleids regels Toep assen op resources, zoals [resource groepen](../../azure-resource-manager/management/overview.md). Virtuele machines die deel uitmaken van een resource groep, nemen het beleid over.

Als uw organisatie veel abonnementen heeft, wilt u mogelijk de toegang, het beleid en de naleving voor die abonnementen op efficiënte wijze beheren. [Azure-beheer groepen](../../governance/management-groups/overview.md) bieden een niveau van scope boven abonnementen. U organiseert abonnementen in beheer groepen (containers) en past uw governance-voor waarden toe op deze groepen. Alle abonnementen binnen een beheer groep nemen automatisch de voor waarden over die zijn toegepast op de groep. Beheergroepen bieden u beheer van bedrijfskwaliteit op grote schaal, ongeacht de typen abonnementen die u hebt.

**Best Practice**: Verminder het aantal variaties in uw installatie en implementatie van vm's.   
**Details**: gebruik [Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) sjablonen om uw implementatie mogelijkheden te verbeteren en de virtuele machines in uw omgeving beter te begrijpen en te inventariseren.

**Best Practice**: beveiligde toegang.   
**Details**: gebruik een [minimale bevoegdheids benadering](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) en ingebouwde Azure-rollen om gebruikers in staat te stellen vm's te openen en in te stellen:

- [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor): kan vm's beheren, maar niet het virtuele netwerk of opslag account waarmee ze zijn verbonden.
- [Inzender voor klassieke virtuele machines](../../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): kan vm's beheren die zijn gemaakt met behulp van het klassieke implementatie model, maar niet het virtuele netwerk of het opslag account waarmee de vm's zijn verbonden.
- [Beveiligings beheerder](../../role-based-access-control/built-in-roles.md#security-admin): In alleen Security Center: kan beveiligings beleid weer geven, beveiligings status weer geven, beveiligings beleid bewerken, waarschuwingen en aanbevelingen weer geven, waarschuwingen en aanbevelingen negeren.
- [DevTest Labs-gebruiker](../../role-based-access-control/built-in-roles.md#devtest-labs-user): kan alles weer geven en er verbinding mee maken, virtuele machines starten, opnieuw starten en afsluiten.

Uw abonnements beheerders en mede beheerders kunnen deze instelling wijzigen, zodat deze beheerders van alle virtuele machines in een abonnement. Zorg ervoor dat u alle abonnements beheerders en-beheerders vertrouwt om zich aan te melden bij een van uw computers.

> [!NOTE]
> U wordt aangeraden Vm's met dezelfde levens cyclus te consolideren in dezelfde resource groep. Met resource groepen kunt u de facturerings kosten voor uw resources implementeren, bewaken en samen vouwen.
>
>

Organisaties die de toegang tot de virtuele machine beheren en Setup verbeteren hun totale VM-beveiliging.

## <a name="use-multiple-vms-for-better-availability"></a>Gebruik meerdere Vm's voor een betere Beschik baarheid
Als uw virtuele machine essentiële toepassingen uitvoert waarvoor hoge Beschik baarheid nodig is, raden we u ten zeerste aan meerdere Vm's te gebruiken. Gebruik een [beschikbaarheidsset](../../virtual-machines/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) of beschikbaarheids [zones](../../availability-zones/az-overview.md)voor een betere Beschik baarheid.

Een beschikbaarheidsset is een logische groepering die u in azure kunt gebruiken om ervoor te zorgen dat de VM-resources die u in de groep plaatst, van elkaar zijn geïsoleerd wanneer ze in een Azure-Data Center worden geïmplementeerd. Azure zorgt ervoor dat de Vm's die u in een beschikbaarheidsset plaatst, worden uitgevoerd op meerdere fysieke servers, reken rekken, opslag eenheden en netwerk switches. Als er sprake is van een hardware-of Azure-software fout, is dit alleen van invloed op een subset van uw virtuele machines en blijft uw volledige toepassing beschikbaar voor uw klanten. Beschikbaarheids sets vormen een essentiële mogelijkheid wanneer u betrouw bare cloud oplossingen wilt bouwen.

## <a name="protect-against-malware"></a>Beveiliging tegen malware
U moet antimalware Protection installeren om virussen, spyware en andere schadelijke software te identificeren en verwijderen. U kunt [micro soft antimalware](antimalware.md) of een Endpoint Protection-oplossing van micro soft partner ([Trend Micro](https://help.deepsecurity.trendmicro.com/Welcome.html), [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/windows/comprehensive-security)en [System Center Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)) installeren.

Micro soft antimalware bevat functies als realtime-beveiliging, geplande scans, malware-herstel, handtekening updates, engine-updates, voor beelden van rapporten en uitsluitings gebeurtenissen verzamelen. Voor omgevingen die afzonderlijk worden gehost vanuit uw productie omgeving, kunt u een antimalware-extensie gebruiken om uw Vm's en Cloud Services te beveiligen.

U kunt micro soft antimalware en partner oplossingen integreren met [Azure Security Center](../../security-center/index.yml) voor een gemakkelijke implementatie en ingebouwde detecties (waarschuwingen en incidenten).

**Best Practice**: Installeer een antimalware-oplossing om te beveiligen tegen malware.   
**Details**: [Installeer een micro soft-partner oplossing of micro soft antimalware](../../security-center/security-center-services.md#supported-endpoint-protection-solutions-)

**Aanbevolen procedure**: Integreer uw antimalware-oplossing met Security Center om de status van uw beveiliging te controleren.   
**Details**: [Endpoint Protection-problemen met Security Center beheren](../../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>Uw VM-updates beheren
Virtuele Azure-machines, zoals alle on-premises Vm's, zijn bedoeld om door de gebruiker te worden beheerd. Er worden dan ook geen Windows-updates naar VM's gepusht door Azure. U moet uw VM-updates beheren.

**Aanbevolen procedure**: behoud uw vm's actueel.   
**Details**: gebruik de [Updatebeheer](../../automation/update-management/overview.md) oplossing in azure Automation om updates van besturings systemen te beheren voor uw Windows-en Linux-computers die zijn geïmplementeerd in azure, in on-premises omgevingen of in andere cloud providers. U kunt snel de status van de beschikbare updates op alle agentcomputers beoordelen en de procedure voor het installeren van vereiste updates voor servers beheren.

Computers die worden beheerd door Updatebeheer gebruiken de volgende configuraties voor het uitvoeren van evaluatie- en update-implementaties:

- Microsoft Monitoring Agent (MMA) voor Windows of Linux
- PowerShell Desired State Configuration (DSC) voor Linux
- Automation Hybrid Runbook Worker
- Microsoft Update of Windows Server Update Services (WSUS) voor Windows-computers

Als u Windows Update gebruikt, moet u de instelling voor automatische Windows Update ingeschakeld laten.

**Best Practice**: Zorg ervoor dat bij de implementatie van installatie kopieën die u hebt gemaakt, de meest recente Round of Windows-updates zijn opgenomen.   
**Details**: alle Windows-updates controleren en installeren als eerste stap van elke implementatie. Deze maat regel is vooral belang rijk wanneer u installatie kopieën implementeert die afkomstig zijn van uw eigen bibliotheek of van uw eigen tape wisselaar. Hoewel installatie kopieën van Azure Marketplace standaard automatisch worden bijgewerkt, kan er een vertragings tijd (tot een paar weken) na een open bare versie zijn.

**Best Practice**: regel matig uw vm's opnieuw implementeren om een nieuwe versie van het besturings systeem af te dwingen.   
**Details**: DEFINIEER uw VM met een [Azure Resource Manager sjabloon](../../azure-resource-manager/templates/template-syntax.md) , zodat u deze eenvoudig opnieuw kunt implementeren. Als u een sjabloon gebruikt, beschikt u over een patch en beveiligde virtuele machine wanneer u deze nodig hebt.

**Best Practice**: Installeer snel beveiligings updates op vm's.   
**Details**: Schakel Azure Security Center (gratis laag of Standard-laag) in om [ontbrekende beveiligings updates te identificeren en toe te passen](../../security-center/asset-inventory.md).

**Aanbevolen procedure**: Installeer de meest recente beveiligings updates.   
**Details**: enkele van de eerste workloads die klanten naar Azure verplaatsen, zijn Labs en externe systemen. Als uw Azure Vm's host-toepassingen of-services die toegankelijk moeten zijn met internet, Vigilant over patches. Patch buiten het besturings systeem. Niet-opgeloste beveiligings problemen op partner toepassingen kunnen ook leiden tot problemen die kunnen worden vermeden als er een goed patch beheer wordt uitgevoerd.

**Aanbevolen procedure**: een back-upoplossing implementeren en testen.   
**Details**: een back-up moet worden afgehandeld op dezelfde manier als elke andere bewerking. Dit geldt voor systemen die deel uitmaken van uw productie omgeving en die worden uitgebreid naar de Cloud.

Test-en ontwikkelings systemen moeten gebruikmaken van back-upstrategieen die herstel mogelijkheden bieden die vergelijkbaar zijn met wat gebruikers hebben gewend, op basis van hun ervaring met on-premises omgevingen. Productie werkbelastingen die naar Azure worden verplaatst, moeten indien mogelijk worden geïntegreerd met bestaande back-upoplossingen. U kunt ook [Azure backup](../../backup/backup-azure-vms-first-look-arm.md) gebruiken om uw back-upvereisten te helpen aanpakken.

Organisaties die geen software-update beleid afdwingen, zijn meer blootgesteld aan bedreigingen die bekende, eerder vastgestelde beveiligings problemen misbruiken. Om te voldoen aan de industriële voor schriften moeten bedrijven bewijzen dat ze met behulp van de juiste beveiligings controles worden gebruikt om de beveiliging van hun werk belastingen in de cloud te waarborgen.

Software-aanbevolen procedures voor een traditioneel Data Center en Azure IaaS hebben veel overeenkomsten. We raden u aan uw huidige software-update beleid te evalueren om Vm's op te nemen die zich in azure bevinden.

## <a name="manage-your-vm-security-posture"></a>Uw VM-beveiligings postuur beheren
Cyber dreigingen zijn in ontwikkeling. Voor het beveiligen van uw Vm's is een bewakings functie vereist die snel bedreigingen kan detecteren, ongeoorloofde toegang tot uw resources kunt voor komen, waarschuwingen kunt activeren en valse positieven kan verlagen.

Gebruik [Azure Security Center](../../security-center/security-center-introduction.md)om de beveiligings postuur van uw [virtuele machines](../../security-center/security-center-introduction.md)met [Windows](../../security-center/security-center-introduction.md) en Linux te controleren. In Security Center beschermt u uw Vm's door gebruik te maken van de volgende mogelijkheden:

- Beveiligings instellingen van het besturings systeem Toep assen met aanbevolen configuratie regels.
- Systeem beveiliging en essentiële updates identificeren en downloaden die mogelijk ontbreken.
- Aanbevelingen implementeren voor endpoint antimalware-beveiliging.
- Valideer schijf versleuteling.
- Beveiligings problemen beoordelen en oplossen.
- Bedreigingen detecteren.

Security Center kunt actief controleren op bedreigingen en mogelijke bedreigingen worden weer gegeven in beveiligings waarschuwingen. Gecorreleerde bedreigingen worden geaggregeerd in één weer gave die een beveiligings incident wordt genoemd.

Security Center slaat gegevens op in [Azure monitor logboeken](../../azure-monitor/log-query/log-query-overview.md). Azure Monitor-logboeken bieden een query taal en analyse-engine waarmee u inzicht krijgt in de werking van uw toepassingen en resources. Gegevens worden ook verzameld van [Azure monitor](../../batch/monitoring-overview.md), beheer oplossingen en agents die zijn geïnstalleerd op virtuele machines in de Cloud of on-premises. Deze gedeelde functionaliteit helpt u een volledig overzicht van uw omgeving te vormen.

Organisaties die geen sterke beveiliging afdwingen voor hun Vm's, blijven niet op de hoogte van potentiële pogingen door onbevoegde gebruikers om beveiligings controles te omzeilen.

## <a name="monitor-vm-performance"></a>VM-prestaties bewaken
Misbruik van bronnen kan een probleem zijn wanneer VM-processen meer resources gebruiken dan ze zouden moeten doen. Prestatie problemen met een virtuele machine kunnen leiden tot onderbrekingen in de service, waardoor het beveiligings principe van Beschik baarheid wordt geschonden. Dit is met name belang rijk voor Vm's die IIS of andere webservers hosten, omdat hoge CPU-of geheugen gebruik mogelijk duidt op een DoS-aanval (Denial of service). Het is essentieel dat u de VM-toegang niet alleen opnieuw kunt bewaken terwijl er een probleem optreedt, maar ook proactief voor de basislijn prestaties, zoals gemeten tijdens de normale werking.

U wordt aangeraden [Azure monitor](../../azure-monitor/platform/data-platform.md) te gebruiken om inzicht te krijgen in de status van uw resource. Azure Monitor functies:

- [Diagnostische logboek bestanden](../../azure-monitor/platform/platform-logs-overview.md)van de resource: bewaakt uw VM-resources en identificeert mogelijke problemen die de prestaties en beschik baarheid kunnen beschadigen.
- [Azure Diagnostics extensie](../../azure-monitor/platform/diagnostics-extension-overview.md): biedt mogelijkheden voor bewaking en diagnostiek op Windows-vm's. U kunt deze mogelijkheden inschakelen door de uitbrei ding op te nemen als onderdeel van de [Azure Resource Manager sjabloon](../../virtual-machines/extensions/diagnostics-template.md).

Organisaties die de prestaties van de virtuele machine niet controleren, kunnen niet bepalen of bepaalde wijzigingen in prestatie patronen normaal of abnormaal zijn. Een virtuele machine die meer resources verbruikt dan normaal, kan duiden op een aanval van een externe bron of een aangetast proces dat wordt uitgevoerd in de virtuele machine.

## <a name="encrypt-your-virtual-hard-disk-files"></a>De bestanden van de virtuele harde schijf versleutelen
We raden u aan om uw virtuele harde schijven (Vhd's) te versleutelen om uw opstart volume en gegevens volumes in de rest van de opslag te helpen beveiligen, samen met uw versleutelings sleutels en geheimen.

[Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) helpt u bij het versleutelen van de schijven van de virtuele Windows-en Linux IaaS-machines. Azure Disk Encryption maakt gebruik van de industrie standaard [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) -functie van Windows en de [DM-cryptografie](https://en.wikipedia.org/wiki/Dm-crypt) functie van Linux om volume versleuteling voor het besturings systeem en de gegevens schijven te bieden. De oplossing is geïntegreerd met [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) om u te helpen de schijf versleutelings sleutels en geheimen in uw sleutel kluis abonnement te controleren en te beheren. De oplossing zorgt er ook voor dat alle gegevens op de schijven van de virtuele machine op rest worden versleuteld in Azure Storage.

Hieronder vindt u aanbevolen procedures voor het gebruik van Azure Disk Encryption:

**Best Practice**: versleuteling inschakelen op vm's.   
**Details**: Azure Disk Encryption genereert en schrijft de versleutelings sleutels naar uw sleutel kluis. Voor het beheren van versleutelings sleutels in uw sleutel kluis is Azure AD-verificatie vereist. Maak een Azure AD-toepassing voor dit doel einde. Voor verificatie doeleinden kunt u verificatie op basis van client geheim of [Azure AD-verificatie op basis van client certificaten](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)gebruiken.

**Best Practice**: gebruik een sleutel versleutelings sleutel (KEK) voor een extra beveiligingslaag voor de versleutelings sleutels. Voeg een KEK toe aan uw sleutel kluis.   
**Details**: gebruik de cmdlet [add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) om een sleutel versleutelings sleutel te maken in de sleutel kluis. U kunt ook een KEK importeren uit uw on-premises Hardware Security module (HSM) voor sleutel beheer. Zie de [Key Vault-documentatie](../../key-vault/keys/hsm-protected-keys.md)voor meer informatie. Wanneer er een KEK is opgegeven, gebruikt Azure Disk Encryption die sleutel om de versleutelingsgeheimen te verpakken voordat er naar Key Vault wordt geschreven. Het bewaren van een borg kopie van deze sleutel in een on-premises sleutel beheer HSM biedt extra beveiliging tegen onbedoeld verwijderen van sleutels.

**Best Practice**: Maak een [moment opname](../../virtual-machines/windows/snapshot-copy-managed-disk.md) en/of een back-up voordat de schijven worden versleuteld. Back-ups bieden een herstel optie als er een onverwachte fout optreedt tijdens het versleutelen.   
**Details**: voor vm's met Managed disks is een back-up vereist voordat versleuteling wordt uitgevoerd. Nadat er een back-up is gemaakt, kunt u de cmdlet **set-AzVMDiskEncryptionExtension** gebruiken om beheerde schijven te versleutelen door de para meter *-skipVmBackup* op te geven. Zie het [Azure backup](../../backup/backup-azure-vms-encryption.md) -artikel voor meer informatie over het maken van back-ups en het herstellen van versleutelde vm's.

**Aanbevolen procedure**: om ervoor te zorgen dat de versleutelings geheimen geen regionale grenzen overschrijden, Azure Disk Encryption moet de sleutel kluis en de virtuele machines zich in dezelfde regio bevinden.   
**Details**: Maak en gebruik een sleutel kluis die zich in dezelfde regio bevindt als de virtuele machine die moet worden versleuteld.

Wanneer u Azure Disk Encryption toepast, kunt u voldoen aan de volgende bedrijfs behoeften:

- IaaS-VM's worden 'at rest' beveiligd met industriestandaard-versleutelingstechnologie om te voorzien in de beveiligings- en nalevingsbehoeften van organisaties.
- IaaS Vm's worden gestart onder door de klant beheerde sleutels en beleids regels en u kunt het gebruik controleren in uw sleutel kluis.

## <a name="restrict-direct-internet-connectivity"></a>Directe Internet connectiviteit beperken
Bewaak en beperk de directe Internet connectiviteit van de VM. Aanvallers scannen de IP-adresbereiken van de open bare Cloud voortdurend op open beheer poorten en proberen eenvoudige aanvallen zoals algemene wacht woorden en bekende problemen met niet-patches. De volgende tabel bevat de aanbevolen procedures om u te helpen beschermen tegen deze aanvallen:

**Aanbevolen procedure**: onbedoelde bloot stelling aan netwerk Routering en beveiliging voor komen.   
**Details**: gebruik Azure RBAC om ervoor te zorgen dat alleen de centrale netwerk groep toestemming heeft om netwerk bronnen te gebruiken.

**Aanbevolen procedure**: beschik bare vm's identificeren en herstellen waarmee toegang vanaf een wille keurig bron-IP-adres is toegestaan.   
**Details**: gebruik Azure Security Center. Security Center wordt aangeraden de toegang via Internet gerichte eind punten te beperken als een van uw netwerk beveiligings groepen een of meer regels voor binnenkomende verbindingen heeft waarmee toegang vanaf een bron-IP-adres is toegestaan. Security Center wordt aangeraden deze regels voor binnenkomende verbindingen te bewerken om de toegang tot IP-bron adressen te [beperken](../../security-center/security-center-network-recommendations.md) die werkelijk toegang nodig hebben.

**Best Practice**: beheer poorten (RDP, SSH) beperken.   
**Details**: Just [-in-time-VM-toegang](../../security-center/security-center-just-in-time.md) kan worden gebruikt om inkomend verkeer naar uw Azure-vm's te vergren delen, waardoor de bloot stelling aan aanvallen wordt verkleind en zo snel mogelijk toegang tot virtuele machines kan worden gemaakt. Wanneer JIT is ingeschakeld, wordt door Security Center het inkomende verkeer naar uw Azure-Vm's vergrendeld door een regel voor een netwerk beveiligings groep te maken. U selecteert de poorten op de VM waarop het inkomende verkeer wordt vergrendeld. Deze poorten worden bepaald door de JIT-oplossing.

## <a name="next-steps"></a>Volgende stappen
Zie [Aanbevolen procedures en patronen voor Azure-beveiliging](best-practices-and-patterns.md) voor meer aanbevolen procedures voor beveiliging bij het ontwerpen, implementeren en beheren van uw cloud oplossingen met behulp van Azure.

De volgende resources zijn beschikbaar om meer algemene informatie te geven over Azure-beveiliging en gerelateerde micro soft-Services:
* [Blog van het Azure-beveiligings team](/archive/blogs/azuresecurity/) : voor actuele informatie over de nieuwste Azure-beveiliging
* [Micro soft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) : waar micro soft-beveiligings problemen, met inbegrip van problemen met Azure, kunnen worden gerapporteerd of via e-mail worden verzonden naar secure@microsoft.com