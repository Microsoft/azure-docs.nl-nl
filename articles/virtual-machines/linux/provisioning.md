---
title: Overzicht van Linux-inrichting
description: Overzicht van hoe uw eigen Linux VM-installatiekopieën gebruikt of nieuwe installatiekopieën maakt voor gebruik in Azure.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 6dafa400f2ce2421db6775084befc0abeab70a04
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563214"
---
# <a name="azure-linux-vm-provisioning"></a>Azure Linux VM inrichten
Wanneer u een virtuele machine maakt op basis van een gegeneraliseerde installatiekopie (gedeelde installatiekopiegalerie of beheerde installatiekopie), kunt u met het besturingselement een virtuele machine maken en parameters en instellingen doorgeven aan de virtuele machine. Dit heet de VM *inrichten*. Tijdens het inrichten maakt het platform vereiste parameterwaarden voor de virtuele machine (hostname, gebruikersnaam, wachtwoord, SSH-sleutels, customData) die tijdens het opstarten beschikbaar zijn voor de virtuele machine. 

Een geïntegreerde inrichtingsagent in de installatiekopie krijgt een interface met het platform, waarbij verbinding wordt gemaakt met meerdere onafhankelijke inrichtingsinterfaces, de eigenschappen worden ingesteld en het platform een seintje krijgt dat alles is voltooid. 

De inrichtingsagenten kunnen de [Azure Linux agent](../extensions/agent-linux.md)of [cloud-init](./using-cloud-init.md) zijn. Dit zijn [vereisten](create-upload-generic.md) voor het maken van gegeneraliseerde installatiekopieën.

De inrichtingsagenten bieden ondersteuning voor alle geviseerde [Azure Linux-distributies](./endorsed-distros.md) en u vindt de geviseerde distributie-installatiekopieën in veel gevallen die worden geleverd met cloud-init en de Linux-agent. Dit geeft u de mogelijkheid om cloud-init de inrichting te laten afhandelen, waarna de Linux-agent ondersteuning biedt voor het verwerken van [Azure-extensies](../extensions/features-windows.md). Ondersteuning voor extensies betekent dat de virtuele machine vervolgens in aanmerking komt voor extra Azure-services, zoals het opnieuw instellen van VM-wachtwoorden, Azure-bewaking, Azure Backup, Azure Disk-versleuteling, enzovoort.

Nadat het inrichten is voltooid, wordt de cloud-init elke keer dat de computer wordt opgestart uitgevoerd. Cloud-init bewaakt de wijzigingen aan de virtuele machine, zoals netwerkwijzigingen, koppelen en formatteren van de tijdelijke schijf, en het starten van de Linux-agent. De Linux-agent wordt doorlopend op de server uitgevoerd en zoekt een 'doelstatus' (nieuwe configuratie) van het Azure-platform, dus wanneer u extensies installeert, kan de agent deze verwerken.

Er zijn momenteel twee inrichtingsagenten, maar u moet cloud-init kiezen, en de Linux-agent moet worden geïnstalleerd voor ondersteuning van extensies. Zo kunt u gebruikmaken van platformoptimalisaties en kunt u de Linux-agent uitschakelen of verwijderen. Voor meer informatie over het maken van installatiekopieën zonder de agent en hoe u deze kunt verwijderen, raadpleegt u deze [documentatie](disable-provisioning.md).

Als u een Linux-kernel hebt die geen van beide agents ondersteunt, maar u een aantal van de VM-Create-eigenschappen wilt instellen, zoals hostname, customData, userName, password, SSH-sleutels, wordt in dit document besproken hoe u [Algemene installatie kopieën (kunt) maken zonder agent](no-agent.md) en kunt voldoen aan de platformvereisten.


## <a name="provisioning-agent-responsibilities"></a>Verantwoordelijkheden van de inrichtingsagent

**Installatiekopie inrichten**
  
- Een gebruikersaccount maken
- SSH-verificatietypen configureren
- Implementatie van openbare SSH-sleutels en sleutelparen
- De hostnaam instellen
- De hostnaam publiceren naar het platform-DNS
- De vingerafdruk van de SSH-hostwaarde rapporteren aan het platform
- Resourceschijven beheren
- De resourceschijf formatteren en koppelen
- `customData` implementeren en verwerken
 
**Netwerken**
  
- Beheert routes om de compatibiliteit met DHCP-platformservers te verbeteren
- Zorgt voor de stabiliteit van de naam van de netwerkinterface

**Kernel**
  
- Hiermee configureert u virtuele NUMA (uitschakelen voor kernel <`2.6.37`)
- Verbruikt Hyper-V-entropie voor `/dev/random`
- Hiermee configureert u SCSI-time-outs voor het hoofdapparaat (dit kan extern zijn)

**Diagnostics**
  
- Console-omleiding naar de seriële poort

## <a name="communication"></a>Communicatie
De informatiestroom van het platform naar de agent vindt plaats via twee kanalen:

- Een DVD die is gekoppeld aan het opstarttijdstip voor IaaS-implementaties. De DVD bevat een configuratiebestand met OVF dat alle inrichtingsgegevens bevat, met uitzondering van de werkelijke SSH-sleutelparen.
- Een TCP-eindpunt geeft een REST API die wordt gebruikt voor het verkrijgen van implementatie en topologie-configuratie.


## <a name="azure-provisioning-agent-requirements"></a>Vereisten voor de inrichtingsagent van Azure
De Linux-agent en cloud-init zijn afhankelijk van sommige systeempakketten om goed te kunnen functioneren:
- Python 2.6 of later
- OpenSSL 1.0 of later
- OpenSSH 5.3 of later
- Hulpprogramma's voor bestandssysteem: `sfdisk`, `fdisk`, `mkfs`, `parted`
- Hulpmiddelen voor wachtwoord: chpasswd, sudo
- Hulpmiddelen voor tekstverwerking: sed, grep
- Hulpprogramma's voor netwerk: ip-route
- Kernelondersteuning voor het koppelen van UDF-bestandssysteem.

## <a name="next-steps"></a>Volgende stappen

Als dat nodig is, kunt u het [Inrichten uitschakelen en de Linux-agent verwijderen](disable-provisioning.md).
