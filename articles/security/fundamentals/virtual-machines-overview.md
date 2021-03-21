---
title: Beveiligings functies die worden gebruikt met virtuele Azure-machines
titleSuffix: Azure security
description: Dit artikel bevat een overzicht van de belangrijkste Azure-beveiligings functies die kunnen worden gebruikt met Azure Virtual Machines.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/2/2019
ms.author: terrylan
ms.openlocfilehash: 036a1d3f8cad4ba9cda03a9001cfec1bb7de86b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715034"
---
# <a name="azure-virtual-machines-security-overview"></a>Overzicht van Azure Virtual Machines-beveiliging
Dit artikel bevat een overzicht van de belangrijkste Azure-beveiligings functies die kunnen worden gebruikt met virtuele machines.

U kunt Azure Virtual Machines gebruiken om een breed scala aan computer oplossingen op flexibele wijze te implementeren. De service ondersteunt micro soft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP en Azure BizTalk Services. U kunt elke workload en elke taal op vrijwel elk besturings systeem implementeren.

Een virtuele machine in Azure biedt u de flexibiliteit van virtualisatie zonder dat u de fysieke hardware hoeft te kopen en te beheren waarop de virtuele machine wordt uitgevoerd. U kunt uw toepassingen bouwen en implementeren met de zekerheid dat uw gegevens worden beschermd en veilig zijn in zeer beveiligde data centers.

Met Azure kunt u aan beveiliging verhoogde, compatibele oplossingen bouwen die:

* Bescherm uw virtuele machines tegen virussen en malware.
* Versleutel uw gevoelige gegevens.
* Netwerk verkeer beveiligen.
* Identificeer en Detecteer bedreigingen.
* Voldoen aan nalevings vereisten.  

## <a name="antimalware"></a>Antimalware

Met Azure kunt u antimalware-software gebruiken van beveiligings leveranciers zoals micro soft, Symantec, Trend Micro en Kaspersky. Deze software helpt bij het beschermen van uw virtuele machines tegen schadelijke bestanden, adware en andere bedreigingen.

Micro soft antimalware voor Azure Cloud Services en Virtual Machines is een real-time beschermings functie waarmee u virussen, spyware en andere schadelijke software kunt identificeren en verwijderen.  Micro soft antimalware voor Azure biedt Configureer bare waarschuwingen wanneer bekende schadelijke of ongewenste software probeert zichzelf te installeren of uit te voeren op uw Azure-systemen.

Micro soft antimalware voor Azure is een oplossing voor toepassingen en Tenant omgevingen met één agent. Het is ontworpen om op de achtergrond te worden uitgevoerd zonder menselijke tussen komst. U kunt beveiliging implementeren op basis van de behoeften van uw toepassings werkbelastingen, met een basis beveiliging van standaard instellingen of geavanceerde aangepaste configuratie, waaronder bewaking van anti-malware.

Meer informatie over [micro soft antimalware voor Azure](antimalware.md) en de belangrijkste beschik bare functies.

Meer informatie over antimalware-software om uw virtuele machines te beschermen:

* [Deploying Antimalware Solutions on Azure Virtual Machines](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/) (Antimalware-oplossingen implementeren op virtuele machines van Azure)
* [Trend Micro diepe Security als een service op een Windows-VM installeren en configureren](/previous-versions/azure/virtual-machines/extensions/trend)
* [Symantec-Endpoint Protection installeren en configureren op een Windows-VM](../../virtual-machines/extensions/symantec.md)
* [Beveiligings oplossingen in azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

Voor nog krachtigere beveiliging kunt u het gebruik van [Windows Defender Advanced Threat Protection](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)gebruiken. Met Windows Defender ATP krijgt u het volgende:

* [Kwetsbaarheid voor aanvallen verminderen](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Beveiliging van de volgende generatie](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Endpoint Protection en respons](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Geautomatiseerd onderzoek en herstel](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Beveiligingsscore](/windows/security/threat-protection/microsoft-defender-atp/tvm-microsoft-secure-score-devices)
* [Geavanceerde jacht](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Beheer en Api's](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Microsoft Threat Protection](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Meer informatie:

* [Aan de slag met WDATP](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [Overzicht van WDATP-mogelijkheden](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Hardware Security module

Het verbeteren van de sleutel beveiliging kan versleuteling en verificatie beveiliging verbeteren. U kunt het beheer en de beveiliging van uw essentiële geheimen en sleutels vereenvoudigen door ze op te slaan in Azure Key Vault.

Key Vault biedt de mogelijkheid om uw sleutels op te slaan in Hardware Security modules (Hsm's) die zijn gecertificeerd voor FIPS 140-2 level 2-standaarden. Uw SQL Server versleutelings sleutels voor back-up of [transparante gegevens versleuteling](/sql/relational-databases/security/encryption/transparent-data-encryption) kunnen allemaal worden opgeslagen in Key Vault met alle sleutels of geheimen van uw toepassingen. Machtigingen en toegang tot deze beveiligde items worden beheerd via [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Meer informatie:

* [Wat is Azure Key Vault?](../../key-vault/general/overview.md)
* [Azure Key Vault blog](/archive/blogs/kv/)

## <a name="virtual-machine-disk-encryption"></a>Schijf versleuteling van virtuele machine

Azure Disk Encryption is een nieuwe mogelijkheid voor het versleutelen van de schijven van de virtuele Windows-en Linux-machines. Azure Disk Encryption maakt gebruik van de industrie standaard [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) -functie van Windows en de [DM-cryptografie](https://en.wikipedia.org/wiki/Dm-crypt) functie van Linux om volume versleuteling voor het besturings systeem en de gegevens schijven te bieden.

De oplossing is geïntegreerd met Azure Key Vault om u te helpen de schijf versleutelings sleutels en geheimen in uw sleutel kluis abonnement te controleren en te beheren. Zo zorgt u ervoor dat alle gegevens in de virtuele-machine schijven op rest worden versleuteld in Azure Storage.

Meer informatie:

* [Azure Disk Encryption voor IaaS-Vm's](./azure-disk-encryption-vms-vmss.md)
* [Snelstart: Een Windows Iaas-VM versleutelen met Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Back-up van virtuele machine

Azure Backup is een schaal bare oplossing die helpt bij het beschermen van uw toepassings gegevens met een kapitaal investering van nul en minimale bedrijfs kosten. Toepassings fouten kunnen uw gegevens beschadigen en menselijke fouten kunnen leiden tot fouten in uw toepassingen. Met Azure Backup worden uw virtuele machines met Windows en Linux beveiligd.

Meer informatie:

* [Wat is Azure Backup?](../../backup/backup-overview.md)
* [Veelgestelde vragen over de Azure Backup-Service](../../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery

Een belang rijk onderdeel van de BCDR-strategie van uw organisatie is het bepalen van de manier waarop bedrijfs werkbelastingen en apps worden uitgevoerd wanneer geplande en ongeplande storingen optreden. Azure Site Recovery helpt de replicatie, failover en herstel van workloads en apps te organiseren, zodat deze beschikbaar zijn vanaf een secundaire locatie als uw primaire locatie uitvalt.

Site Recovery:

* **Vereenvoudigt uw BCDR-strategie**: site Recovery maakt het eenvoudig om de replicatie, failover en het herstel van meerdere werk belastingen en apps vanaf één locatie te verwerken. Site Recovery organiseert de replicatie en failover, maar onderschept geen toepassings gegevens of bevat er informatie over.
* **Voorziet in flexibele replicatie**: door site Recovery te gebruiken, kunt u workloads repliceren die worden uitgevoerd op virtuele Hyper-V-machines, virtuele VMware-machines en fysieke Windows/Linux-servers.
* **Biedt ondersteuning voor failover en herstel**: site Recovery voorziet in testfailover voor het ondersteunen van herstel na nood gevallen zonder dat dit van invloed is op productie omgevingen. Bovendien kunt u bij verwachte uitval geplande failovers uitvoeren zonder gegevensverlies, en bij onverwachte noodsituaties ongeplande failovers met minimaal gegevensverlies (afhankelijk van de replicatiefrequentie). Na een failover kunt u weer een failback uitvoeren naar uw primaire sites. De herstelplannen van Site Recovery kunnen scripts en Azure Automation-werkmappen bevatten, zodat u failovers en het herstel van toepassingen met meerdere lagen naar behoefte kunt aanpassen.
* **Elimineert secundaire data centers**: u kunt repliceren naar een secundaire on-premises site of naar Azure. Als u Azure als doel gebruikt voor herstel na nood gevallen, elimineert u de kosten en complexiteit van het onderhouden van een secundaire site. Gerepliceerde gegevens worden opgeslagen in Azure Storage.
* Kan worden **geïntegreerd met bestaande BCDR-technologieën**: site Recovery partners met de BCDR-functies van andere toepassingen. U kunt bijvoorbeeld Site Recovery gebruiken om de SQL Server back-end van zakelijke workloads te helpen beveiligen. Dit omvat systeem eigen ondersteuning voor SQL Server altijd aan om de failover van beschikbaarheids groepen te beheren.

Meer informatie:

* [Wat is Azure Site Recovery?](../../site-recovery/site-recovery-overview.md)
* [Hoe werkt Azure Site Recovery?](../../site-recovery/azure-to-azure-architecture.md)
* [Welke workloads worden beveiligd door Azure Site Recovery?](../../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Virtueel netwerk

Virtuele machines hebben een netwerk verbinding nodig. Voor de ondersteuning van deze vereiste vereist Azure dat virtuele machines zijn verbonden met een virtueel Azure-netwerk.

Een virtueel Azure-netwerk is een logische constructie die boven op de fysieke Azure-netwerk infrastructuur is gebouwd. Elk logisch Azure Virtual Network is geïsoleerd van alle andere virtuele netwerken van Azure. Deze isolatie helpt ervoor te zorgen dat het netwerk verkeer in uw implementaties niet toegankelijk is voor andere Microsoft Azure-klanten.

Meer informatie:

* [Overzicht van Azure-netwerkbeveiliging](network-overview.md)
* [Overzicht van Virtual Network](../../virtual-network/virtual-networks-overview.md)
* [Netwerk functies en partnerschappen voor bedrijfs scenario's](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Beheer en rapportage van beveiligings beleid

Azure Security Center helpt u bedreigingen te voorkomen, te detecteren en erop te reageren. Security Center biedt u meer inzicht in en controle over de beveiliging van uw Azure-resources. Het biedt geïntegreerde beveiligings bewaking en beleids beheer in uw Azure-abonnementen. Het helpt bedreigingen te detecteren die anders niet kunnen worden opgemerkt en die werken met een breed ecosysteem van beveiligings oplossingen.

Security Center helpt u de beveiliging van uw virtuele machines te optimaliseren en te controleren door:

* [Beveiligings aanbevelingen](../../security-center/security-center-recommendations.md) voor de virtuele machines opgeven. Voor beelden van aanbevelingen zijn: systeem updates Toep assen, Acl's-eind punten configureren, antimalware inschakelen, netwerk beveiligings groepen inschakelen en schijf versleuteling Toep assen.
* Bewaken van de status van uw virtuele machines.

Meer informatie:

* [Inleiding tot Azure Security Center](../../security-center/security-center-introduction.md)
* [Veelgestelde vragen over Azure Security Center](../../security-center/faq-general.md)
* [Planning en bewerkingen Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Naleving

Azure Virtual Machines is gecertificeerd voor FISMA, FedRAMP, HIPAA, PCI DSS Level 1 en andere belang rijke nalevings Programma's. Deze certificering maakt het gemakkelijker voor uw eigen Azure-toepassingen om te voldoen aan nalevings vereisten en voor uw bedrijf om een breed scala aan nationale en internationale regelgeving te behandelen.

Meer informatie:

* [Vertrouwens centrum van micro soft: naleving](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Vertrouwde Cloud: Microsoft Azure beveiliging, privacy en naleving](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Confidential Computing

Hoewel vertrouwelijke computing geen technisch onderdeel is van de beveiliging van de virtuele machine, behoort het onderwerp van de beveiliging van de virtuele machine tot het hogere niveau van "Compute"-beveiliging. Vertrouwelijk computing behoort tot de categorie "Compute"-beveiliging.

Met vertrouwelijke computing zorgt u ervoor dat wanneer gegevens ' in de heldere ' zijn, die is vereist voor een efficiënte verwerking, de gegevens worden beschermd binnen een vertrouwde uitvoerings omgeving  https://en.wikipedia.org/wiki/Trusted_execution_environment (Tee, ook wel een enclave genoemd), een voor beeld van dat wordt weer gegeven in de afbeelding hieronder.  

TEEs zorgt ervoor dat er geen manier is om gegevens of de bewerkingen in de buiten kant weer te geven, zelfs met een fout opsporingsprogramma. Ze kunnen er ook voor zorgen dat alleen geautoriseerde code toegang heeft tot gegevens. Als de code wordt gewijzigd of gewijzigd, worden de bewerkingen geweigerd en wordt de omgeving uitgeschakeld. De TEE dwingt deze beveiligingen af tijdens het uitvoeren van code in het bestand.

Meer informatie:

* [Introductie van Azure vertrouwelijk computing](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Azure Confidential Computing](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [aanbevolen beveiligings procedures](iaas.md) voor vm's en besturings systemen.