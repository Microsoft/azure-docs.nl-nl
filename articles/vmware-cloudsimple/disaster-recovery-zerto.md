---
title: 'Azure VMware-oplossing door CloudSimple: gebruik Privécloud als nood geval voor on-premises workloads'
description: Hierin wordt beschreven hoe u uw CloudSimple-Privécloud instelt als een nood herstel site voor on-premises VMware-workloads
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5dd1f157984cdb300cc3b375d71654e5da54d1ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898127"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>CloudSimple Private Cloud instellen als een nood herstel site voor on-premises VMware-workloads

Uw CloudSimple-Privécloud kan worden ingesteld als een herstel site voor on-premises toepassingen om bedrijfs continuïteit te bieden in het geval van een ramp. De herstel oplossing is gebaseerd op Zerto virtuele replicatie als het replicatie-en Orchestration-platform. Kritieke infra structuur en toepassings-virtuele machines kunnen doorlopend worden gerepliceerd van uw on-premises vCenter naar uw Privécloud. U kunt uw Privécloud gebruiken voor het testen van de failover en om de beschik baarheid van uw toepassing tijdens een nood geval te garanderen. Een soort gelijke aanpak kan worden gevolgd om de Privécloud in te stellen als een primaire site die wordt beveiligd door een herstel site op een andere locatie.

> [!NOTE]
> Raadpleeg de Zerto van de document [grootte voor Zerto Virtual Replication](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) voor richt lijnen over het aanpassen van de grootte van uw omgeving voor herstel na nood gevallen.

De CloudSimple-oplossing:

* Elimineert de nood zaak om een Data Center in te stellen dat specifiek is voor herstel na nood gevallen (DR).
* Met kunt u gebruikmaken van de Azure-locaties waar CloudSimple wordt geïmplementeerd voor wereld wijde geografische tolerantie.
* Biedt u een optie om implementatie kosten en total cost of ownership voor DR te reduceren.

Voor de oplossing moet u het volgende doen:

* Zerto in uw Privécloud installeren, configureren en beheren.
* Geef uw eigen licenties voor Zerto op als de Privécloud de beveiligde site is. U kunt Zerto die op de CloudSimple-site worden uitgevoerd, koppelen met uw on-premises site voor licentie verlening.

In de volgende afbeelding ziet u de architectuur van de Zerto-oplossing.

![Architectuur](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>De oplossing implementeren

In de volgende secties wordt beschreven hoe u een DR-oplossing implementeert met behulp van Zerto Virtual Replication in uw Privécloud.

1. [Vereisten](#prerequisites)
2. [Optionele configuratie op CloudSimple Private Cloud](#optional-configuration-on-your-private-cloud)
3. [ZVM en VRA op CloudSimple Privécloud instellen](#set-up-zvm-and-vra-on-your-private-cloud)
4. [Virtuele beveiligings groep Zerto instellen](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Vereisten

Als u virtuele Zerto-replicatie van uw on-premises omgeving naar uw Privécloud wilt inschakelen, moet u de volgende vereisten volt ooien.

1. [Stel een site-naar-site-VPN-verbinding in tussen uw on-premises netwerk en uw CloudSimple-privécloud](set-up-vpn.md).
2. [Stel de DNS-zoek opdracht zo in dat uw Private Cloud Management-onderdelen worden doorgestuurd naar DNS-servers in de privécloud](on-premises-dns-setup.md).  Als u het door sturen van DNS-lookup wilt inschakelen, maakt u een doorstuur zone vermelding in uw on-premises DNS-server voor `*.cloudsimple.io` CloudSimple DNS-servers.
3. Stel de DNS-zoek opdracht zodanig in dat de on-premises vCenter-onderdelen worden doorgestuurd naar lokale DNS-servers.  De DNS-servers moeten bereikbaar zijn vanuit uw CloudSimple-Privécloud via site-naar-site-VPN. Voor hulp dient u een [ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)in te dienen, waarbij de volgende informatie wordt verstrekt.  

    * Naam van on-premises DNS-domein
    * IP-adressen van on-premises DNS-server

4. Installeer een Windows-Server in uw Privécloud. De server wordt gebruikt om Zerto Virtual Manager te installeren.
5. [Escaleren uw CloudSimple-bevoegdheden](escalate-private-cloud-privileges.md).
6. Maak een nieuwe gebruiker in uw Privécloud met de beheerdersrol die moet worden gebruikt als service account voor Zerto Virtual Manager.

### <a name="optional-configuration-on-your-private-cloud"></a>Optionele configuratie in uw Privécloud

1. Maak een of meer resource groepen in uw Privécloud die als doel resource groepen voor virtuele machines in uw on-premises omgeving worden gebruikt.
2. Maak een of meer mappen in uw Privécloud die u wilt gebruiken als doel mappen voor virtuele machines uit uw on-premises omgeving.
3. Maak VLAN'S voor het failover-netwerk en stel firewall regels in. Open een [ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) voor ondersteuning.
4. Maak gedistribueerde poort groepen voor het failover-netwerk en het test netwerk om de failover van Vm's te testen.
5. Installeer [DHCP-en DNS-servers](dns-dhcp-setup.md) of gebruik een Active Directory domein controller in uw privécloud.

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>ZVM en VRA in uw Privécloud instellen

1. Installeer Zerto Virtual Manager (ZVM) op de Windows-Server in uw Privécloud.
2. Meld u aan bij ZVM met het service account dat u in de vorige stappen hebt gemaakt.
3. Stel de licentie verlening voor Zerto Virtual manager in.
4. Installeer Zerto Virtual Replication Appliance (VRA) op de ESXi-hosts van uw Privécloud.
5. Koppel uw persoonlijke Cloud ZVM aan uw on-premises ZVM.

### <a name="set-up-zerto-virtual-protection-group"></a>Virtuele beveiligings groep Zerto instellen

1. Maak een nieuwe virtuele beveiligings groep (VPG) en geef de prioriteit voor de VPG op.
2. Selecteer de virtuele machines die moeten worden beveiligd voor bedrijfs continuïteit en pas de opstart volgorde zo nodig aan.
3. Selecteer de herstel site als uw Privécloud en de standaard herstel server als het particuliere cloud cluster of de resource groep die u hebt gemaakt. Selecteer **vsanDatastore** voor de herstel gegevens opslag in de privécloud.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > U kunt de optie host voor afzonderlijke Vm's aanpassen onder de optie VM-instellingen.

4. Pas de opslag opties aan zoals vereist.
5. Geef de herstel netwerken op die moeten worden gebruikt voor het failover-netwerk en het test netwerk voor failover als de gedistribueerde poort groepen die eerder zijn gemaakt en pas de herstel scripts indien nodig aan.
6. Pas zo nodig de netwerk instellingen voor afzonderlijke Vm's aan en maak de VPG.
7. Testfailover wanneer de replicatie is voltooid.

## <a name="reference"></a>Naslaginformatie

[Documentatie voor Zerto](https://www.zerto.com/myzerto/technical-documentation/)
