---
title: Beveiligingsfuncties die worden gebruikt met Azure-VM's
titleSuffix: Azure security
description: Dit artikel bevat een overzicht van de belangrijkste Azure-beveiligingsfuncties die kunnen worden gebruikt met Azure Virtual Machines.
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
ms.openlocfilehash: 60f67ea618746c9f2b0cd65a9fbc7fb2b5fbfe86
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520000"
---
# <a name="azure-virtual-machines-security-overview"></a>Overzicht van azure Virtual Machines-beveiliging
Dit artikel bevat een overzicht van de belangrijkste Azure-beveiligingsfuncties die kunnen worden gebruikt met virtuele machines.

U kunt Azure Virtual Machines een breed scala aan computingoplossingen op een flexibele manier te implementeren. De service ondersteunt Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP en Azure BizTalk Services. U kunt dus elke workload en elke taal implementeren op vrijwel elk besturingssysteem.

Een virtuele machine in Azure biedt u de flexibiliteit van virtualisatie zonder dat u de fysieke hardware hoeft te kopen en te beheren waarop de virtuele machine wordt uitgevoerd. U kunt uw toepassingen bouwen en implementeren met de zekerheid dat uw gegevens zijn beveiligd en veilig zijn in uiterst beveiligde datacenters.

Met Azure kunt u verbeterde, compatibele oplossingen bouwen die:

* Bescherm uw virtuele machines tegen virussen en malware.
* Versleutel uw gevoelige gegevens.
* Netwerkverkeer beveiligen.
* Bedreigingen identificeren en detecteren.
* Voldoen aan nalevingsvereisten.  

## <a name="antimalware"></a>Antimalware

Met Azure kunt u antimalwaresoftware van beveiligingsleveranciers zoals Microsoft, Symantec, Trend Micro en Symantec gebruiken. Deze software helpt uw virtuele machines te beschermen tegen schadelijke bestanden, schadelijke software en andere bedreigingen.

Microsoft Antimalware voor Azure Cloud Services en Virtual Machines is een realtime-beveiligingsmogelijkheid waarmee virussen, spyware en andere schadelijke software kunnen worden identificeren en verwijderd.  Microsoft Antimalware voor Azure biedt configureerbare waarschuwingen wanneer bekende schadelijke of ongewenste software zichzelf probeert te installeren of uit te voeren op uw Azure-systemen.

Microsoft Antimalware voor Azure is een oplossing met één agent voor toepassingen en tenantomgevingen. Het is ontworpen om op de achtergrond te worden uitgevoerd zonder menselijke tussenkomst. U kunt beveiliging implementeren op basis van de behoeften van uw toepassingsworkloads, met een eenvoudige, veilige of geavanceerde aangepaste configuratie, waaronder antimalwarebewaking.

Meer informatie over [Microsoft Antimalware voor Azure en](antimalware.md) de belangrijkste functies die beschikbaar zijn.

Meer informatie over antimalwaresoftware voor het beveiligen van uw virtuele machines:

* [Deploying Antimalware Solutions on Azure Virtual Machines](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/) (Antimalware-oplossingen implementeren op virtuele machines van Azure)
* [Trend Micro Deep Security installeren en configureren als een service op een Windows-VM](/previous-versions/azure/virtual-machines/extensions/trend)
* [Symantec Endpoint Protection installeren en configureren op een Windows-VM](../../virtual-machines/extensions/symantec.md)
* [Beveiligingsoplossingen in de Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

Voor nog krachtigere beveiliging kunt u overwegen om [Windows Defender Advanced Threat Protection te gebruiken.](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection) Met Windows Defender ATP krijgt u het volgende:

* [Kwetsbaarheid voor aanvallen verminderen](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Beveiliging van de volgende generatie](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Eindpuntbeveiliging en -respons](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Geautomatiseerd onderzoek en herstel](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Beveiligingsscore](/windows/security/threat-protection/microsoft-defender-atp/tvm-microsoft-secure-score-devices)
* [Geavanceerde hunting](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Beheer en API's](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Microsoft Threat Protection](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Meer informatie:

* [Aan de slag met WDATP](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [Overzicht van WDATP-mogelijkheden](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Hardwarebeveiligingsmodule

Het verbeteren van de beveiliging van sleutels kan de beveiliging van versleuteling en verificatie verbeteren. U kunt het beheer en de beveiliging van uw kritieke geheimen en sleutels vereenvoudigen door ze op te slaan in Azure Key Vault.

Key Vault biedt de mogelijkheid om uw sleutels op te slaan in HMS's (Hardware Security Modules) die zijn gecertificeerd voor FIPS 140-2 Level 2-standaarden. Uw SQL Server voor back-up [](/sql/relational-databases/security/encryption/transparent-data-encryption) of transparante gegevensversleuteling kunnen allemaal worden opgeslagen in Key Vault sleutels of geheimen van uw toepassingen. Machtigingen en toegang tot deze beveiligde items worden beheerd [via Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Meer informatie:

* [Wat is Azure Key Vault?](../../key-vault/general/overview.md)
* [Azure Key Vault blog](/archive/blogs/kv/)

## <a name="virtual-machine-disk-encryption"></a>Schijfversleuteling van virtuele machine

Azure Disk Encryption is een nieuwe mogelijkheid voor het versleutelen van de schijven van uw virtuele Windows- en Linux-machines. Azure Disk Encryption maakt gebruik van de standaard [BitLocker-functie](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) van Windows en de [dm-crypt-functie](https://en.wikipedia.org/wiki/Dm-crypt) van Linux om volumeversleuteling te bieden voor het besturingssysteem en de gegevensschijven.

De oplossing is geïntegreerd met Azure Key Vault u de schijfversleutelingssleutels en -geheimen in uw key vault-abonnement kunt beheren en beheren. Het zorgt ervoor dat alle gegevens in de schijven van de virtuele machine at-rest worden versleuteld in Azure Storage.

Meer informatie:

* [Azure Disk Encryption voor IaaS-VM's](./azure-disk-encryption-vms-vmss.md)
* [Snelstart: Een Windows Iaas-VM versleutelen met Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Back-up van virtuele machine

Azure Backup is een schaalbare oplossing waarmee u uw toepassingsgegevens kunt beveiligen met nul kapitaalinvesteringen en minimale operationele kosten. Toepassingsfouten kunnen uw gegevens beschadigd raken en menselijke fouten kunnen fouten in uw toepassingen veroorzaken. Met Azure Backup zijn uw virtuele machines met Windows en Linux beveiligd.

Meer informatie:

* [Wat is Azure Backup?](../../backup/backup-overview.md)
* [veelgestelde Azure Backup over de service](../../backup/backup-azure-backup-faq.yml)

## <a name="azure-site-recovery"></a>Azure Site Recovery

Een belangrijk onderdeel van de BCDR-strategie van uw organisatie is het bepalen hoe u zakelijke workloads en apps kunt blijven uitvoeren wanneer geplande en ongeplande uitval optreedt. Azure Site Recovery helpt bij het ins delen van replicatie, failover en herstel van workloads en apps, zodat ze beschikbaar zijn vanaf een secundaire locatie als uw primaire locatie uitvallen.

Site Recovery:

* **Vereenvoudigt** uw BCDR-strategie: Site Recovery maakt het eenvoudig om replicatie, failover en herstel van meerdere zakelijke workloads en apps vanaf één locatie te verwerken. Site Recovery replicatie en failover in, maar onderschept uw toepassingsgegevens niet en heeft er geen informatie over.
* **Biedt flexibele replicatie:** met Site Recovery kunt u werkbelastingen repliceren die worden uitgevoerd op virtuele Hyper-V-machines, virtuele VMware-machines en fysieke Windows-/Linux-servers.
* **Ondersteunt failover en herstel:** Site Recovery biedt test-failovers ter ondersteuning van noodhersteloefeningen zonder dat dit invloed heeft op productieomgevingen. Bovendien kunt u bij verwachte uitval geplande failovers uitvoeren zonder gegevensverlies, en bij onverwachte noodsituaties ongeplande failovers met minimaal gegevensverlies (afhankelijk van de replicatiefrequentie). Na een failover kunt u een failback naar uw primaire sites maken. De herstelplannen van Site Recovery kunnen scripts en Azure Automation-werkmappen bevatten, zodat u failovers en het herstel van toepassingen met meerdere lagen naar behoefte kunt aanpassen.
* **Elimineert secundaire datacenters:** u kunt repliceren naar een secundaire on-premises site of naar Azure. Als u Azure als bestemming gebruikt voor herstel na noodherstel, worden de kosten en complexiteit van het onderhouden van een secundaire site geëlimineerd. Gerepliceerde gegevens worden opgeslagen in Azure Storage.
* **Kan worden geïntegreerd met bestaande BCDR-technologieën:** Site Recovery partners met BCDR-functies van andere toepassingen. U kunt bijvoorbeeld een Site Recovery om de back-end van SQL Server bedrijfsworkloads te beveiligen. Dit omvat native ondersteuning voor SQL Server Always On voor het beheren van de failover van beschikbaarheidsgroepen.

Meer informatie:

* [Wat is Azure Site Recovery?](../../site-recovery/site-recovery-overview.md)
* [Hoe werkt Azure Site Recovery?](../../site-recovery/azure-to-azure-architecture.md)
* [Welke workloads worden beveiligd door Azure Site Recovery?](../../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Virtueel netwerk

Virtuele machines hebben netwerkconnectiviteit nodig. Ter ondersteuning van deze vereiste vereist Azure dat virtuele machines zijn verbonden met een virtueel Azure-netwerk.

Een virtueel Azure-netwerk is een logische constructie die is gebouwd op de fysieke Azure-netwerk-fabric. Elk logisch virtueel Azure-netwerk is geïsoleerd van alle andere virtuele Azure-netwerken. Deze isolatie helpt ervoor te zorgen dat netwerkverkeer in uw implementaties niet toegankelijk is voor andere Microsoft Azure klanten.

Meer informatie:

* [Overzicht van Azure-netwerkbeveiliging](network-overview.md)
* [Overzicht van Virtual Network](../../virtual-network/virtual-networks-overview.md)
* [Netwerkfuncties en partnerrelaties voor bedrijfsscenario's](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Beheer en rapportage van beveiligingsbeleid

Azure Security Center helpt u bedreigingen te voorkomen, te detecteren en erop te reageren. Security Center biedt u meer inzicht in en controle over de beveiliging van uw Azure-resources. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw Azure-abonnementen. Het helpt bedreigingen te detecteren die anders ongemerkt zouden blijven en werkt met een breed ecosysteem van beveiligingsoplossingen.

Security Center kunt u de beveiliging van uw virtuele machines optimaliseren en bewaken door:

* Het [geven van beveiligingsaanbevelingen](../../security-center/security-center-recommendations.md) voor de virtuele machines. Enkele van de aanbevelingen zijn: systeemupdates toepassen, ACL-eindpunten configureren, antimalware inschakelen, netwerkbeveiligingsgroepen inschakelen en schijfversleuteling toepassen.
* De status van uw virtuele machines bewaken.

Meer informatie:

* [Inleiding tot Azure Security Center](../../security-center/security-center-introduction.md)
* [Azure Security Center veelgestelde vragen](../../security-center/faq-general.md)
* [Azure Security Center en bewerkingen](../../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Naleving

Azure Virtual Machines is gecertificeerd voor FISMA, FedRAMP, HIPAA, PCI DSS Level 1 en andere belangrijke nalevingsprogramma's. Met deze certificering kunnen uw eigen Azure-toepassingen gemakkelijker voldoen aan nalevingsvereisten en kan uw bedrijf voldoen aan een breed scala aan nationale en internationale wettelijke vereisten.

Meer informatie:

* [Microsoft Trust Center: naleving](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Vertrouwde cloud: Microsoft Azure beveiliging, privacy en naleving](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Confidential Computing

Hoewel confidential computing technisch gezien geen deel uitmaakt van de beveiliging van virtuele machines, behoort het onderwerp over beveiliging van virtuele machines tot het onderwerp op een hoger niveau van 'rekenbeveiliging'. Confidential Computing behoort tot de categorie 'rekenbeveiliging'.

Confidential Computing zorgt ervoor dat wanneer gegevens 'in het openbaar' zijn, wat vereist is voor efficiënte verwerking, de gegevens worden beveiligd in een TEE (Trusted Execution Environment, ook wel een enclave genoemd), een voorbeeld hiervan wordt weergegeven in de afbeelding  https://en.wikipedia.org/wiki/Trusted_execution_environment hieronder.  

TEE's zorgen ervoor dat er geen manier is om gegevens of de bewerkingen van buitenaf weer te geven, zelfs niet met een debugger. Ze zorgen er zelfs voor dat alleen geautoriseerde code toegang heeft tot gegevens. Als de code wordt gewijzigd of gemanipuleerd, worden de bewerkingen geweigerd en wordt de omgeving uitgeschakeld. De TEE dwingt deze beveiligingen af tijdens de uitvoering van de code in die beveiliging.

Meer informatie:

* [Introductie van Azure Confidential Computing](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Azure Confidential Computing](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [best practices voor beveiliging](iaas.md) voor VM's en besturingssystemen.