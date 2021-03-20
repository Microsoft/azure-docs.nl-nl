---
title: Herstel na nood gevallen met Azure Site Recovery instellen Active Directory/DNS
description: In dit artikel wordt beschreven hoe u een nood herstel oplossing implementeert voor Active Directory en DNS met Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mayg
ms.openlocfilehash: 528a24bb64aa8d323b5d63a27af0a52ccdf1abb6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86132319"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Herstel na nood geval instellen voor Active Directory en DNS

Zakelijke toepassingen, zoals share point, Dynamics AX en SAP, zijn afhankelijk van Active Directory en een DNS-infra structuur die goed werkt. Wanneer u herstel na nood gevallen instelt voor toepassingen, moet u vaak Active Directory en Domain Name System (DNS) herstellen voordat u andere toepassings onderdelen herstelt, om ervoor te zorgen dat de functionaliteit van de toepassing correct werkt.

U kunt [site Recovery](site-recovery-overview.md) gebruiken om een herstel plan voor nood gevallen voor Active Directory te maken. Als er een onderbreking optreedt, kunt u een failover initiëren. U kunt Active Directory al in enkele minuten operationeel krijgen. Als u Active Directory hebt geïmplementeerd voor meerdere toepassingen op uw primaire site, bijvoorbeeld voor share point en SAP, wilt u mogelijk een failover uitvoeren voor de volledige site. U kunt een failover uitvoeren voor Active Directory met behulp van Site Recovery. Failover de andere toepassingen vervolgens met behulp van toepassingsspecifieke herstel plannen.

In dit artikel wordt uitgelegd hoe u een nood herstel oplossing voor Active Directory maakt. Het bevat vereisten en failover-instructies. Voordat u begint, moet u bekend zijn met Active Directory en Site Recovery.

## <a name="prerequisites"></a>Vereisten

- Als u repliceert naar Azure, [bereidt u Azure-resources](tutorial-prepare-azure.md)voor, met inbegrip van een abonnement, een Azure Virtual Network, een opslag account en een Recovery Services kluis.
- Raadpleeg de [ondersteuningsvereisten](./vmware-physical-azure-support-matrix.md) voor alle onderdelen.

## <a name="replicate-the-domain-controller"></a>De domein controller repliceren

- U moet Site Recovery replicatie instellen op ten minste één virtuele machine (VM) die als host fungeert voor een domein controller of DNS.
- Als u meerdere domein controllers in uw omgeving hebt, moet u ook een extra domein controller op de doel site instellen. De extra domein controller kan zich in azure of in een secundair on-premises Data Center bevindt.
- Als u slechts een paar toepassingen en één domein controller hebt, wilt u mogelijk een failover uitvoeren voor de hele site tegelijk. In dit geval kunt u het beste Site Recovery gebruiken om de domein controller te repliceren naar de doel site, hetzij in azure, hetzij in een secundair on-premises Data Center. U kunt dezelfde gerepliceerde domein controller of DNS-virtuele machine gebruiken [voor een testfailover.](#test-failover-considerations)
- Als u veel toepassingen en meer dan één domein controller in uw omgeving hebt, of als u van plan bent om een failover uit te voeren van een aantal toepassingen tegelijk, wordt u aangeraden een extra domein controller in Site Recovery te stellen op de doel site (in azure of in een secundair on-premises Data Center). Voor [een testfailover kunt](#test-failover-considerations)u een domein controller gebruiken die wordt gerepliceerd door site Recovery. Voor failover kunt u de extra domein controller op de doel site gebruiken.

## <a name="enable-protection-with-site-recovery"></a>Beveiliging met Site Recovery inschakelen

U kunt Site Recovery gebruiken om de virtuele machine te beveiligen die als host fungeert voor de domein controller of DNS.

### <a name="protect-the-vm"></a>De virtuele machine beveiligen

De domein controller die wordt gerepliceerd met behulp van Site Recovery wordt [gebruikt voor de testfailover.](#test-failover-considerations) Zorg ervoor dat het voldoet aan de volgende vereisten:

1. De domein controller is een globale catalogus server.
1. De domein controller moet de eigenaar zijn van de FSMO-functie (Flexible Single Master Operations) voor rollen die nodig zijn tijdens een testfailover. Als dat niet het geval is, moeten deze rollen worden [overgenomen](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) na de failover.

### <a name="configure-vm-network-settings"></a>VM-netwerk instellingen configureren

Voor de virtuele machine die als host fungeert voor de domein controller of DNS, in Site Recovery netwerk instellingen configureren onder de **berekenings-en netwerk** instellingen van de gerepliceerde virtuele machine. Dit zorgt ervoor dat de virtuele machine na een failover aan het juiste netwerk is gekoppeld.

## <a name="protect-active-directory"></a>Active Directory beveiligen

### <a name="site-to-site-protection"></a>Site-naar-site-beveiliging

Maak een domein controller op de secundaire site. Wanneer u de server naar een rol domein controller promo veren, geeft u de naam op van het domein dat wordt gebruikt op de primaire site. U kunt de module **Active Directory sites en services** gebruiken om instellingen te configureren voor het site koppelings object waaraan de sites worden toegevoegd. Door instellingen op een site koppeling te configureren, kunt u bepalen wanneer replicatie tussen twee of meer sites plaatsvindt, en hoe vaak deze plaatsvindt. Zie [replicatie plannen tussen sites](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731862(v=ws.11))voor meer informatie.

### <a name="site-to-azure-protection"></a>Beveiliging van site-naar-Azure

Maak eerst een domein controller in een virtueel Azure-netwerk. Wanneer u de server naar een rol domein controller promo veren, geeft u dezelfde domein naam op als voor de primaire site.

Configureer vervolgens de DNS-server voor het virtuele netwerk opnieuw voor het gebruik van de DNS-server in Azure.

:::image type="content" source="./media/site-recovery-active-directory/azure-network.png" alt-text="Azure-netwerk":::

### <a name="azure-to-azure-protection"></a>Azure-naar-Azure-beveiliging

Maak eerst een domein controller in een virtueel Azure-netwerk. Wanneer u de server naar een rol domein controller promo veren, geeft u dezelfde domein naam op als voor de primaire site.

Configureer vervolgens de DNS-server voor het virtuele netwerk opnieuw voor het gebruik van de DNS-server in Azure.

## <a name="test-failover-considerations"></a>Overwegingen voor testfailover

Om te voor komen dat de werk belasting wordt beïnvloed, treedt de testfailover op in een netwerk dat is geïsoleerd van het productie netwerk.

De meeste toepassingen moeten de aanwezigheid van een domein controller of een DNS-server hebben. Daarom moet u voordat de toepassing een failover uitvoeren een domein controller in het geïsoleerde netwerk maken die voor testfailover moet worden gebruikt. De eenvoudigste manier om dit te doen is door Site Recovery te gebruiken voor het repliceren van een virtuele machine die als host fungeert voor een domein controller of DNS. Voer vervolgens een testfailover van de virtuele machine van de domein controller uit voordat u een testfailover uitvoert van het herstel plan voor de toepassing.

1. Gebruik Site Recovery om de virtuele machine te [repliceren](vmware-azure-tutorial.md) die als host fungeert voor de domein controller of DNS.
1. Een geïsoleerd netwerk maken. Elk virtueel netwerk dat u in azure maakt, is standaard geïsoleerd van andere netwerken. U wordt aangeraden hetzelfde IP-adres bereik te gebruiken voor dit netwerk dat u gebruikt in uw productie netwerk. Schakel geen site-naar-site-connectiviteit in op dit netwerk.
1. Geef een DNS IP-adres op in het geïsoleerde netwerk. Gebruik het IP-adres dat u verwacht dat de virtuele DNS-machine wordt opgehaald. Als u repliceert naar Azure, geeft u het IP-adres op voor de virtuele machine die wordt gebruikt voor failover. Als u het IP-adres wilt invoeren, selecteert u in de **berekening-en netwerk** instellingen van de gerepliceerde virtuele machine de **doel-IP-** instellingen.

   :::image type="content" source="./media/site-recovery-active-directory/azure-test-network.png" alt-text="Azure-test netwerk":::

   > [!TIP]
   > Site Recovery probeert virtuele test machines te maken in een subnet met dezelfde naam en met behulp van hetzelfde IP-adres dat is opgenomen in de **reken-en netwerk** instellingen van de virtuele machine. Als een subnet met dezelfde naam niet beschikbaar is in het virtuele netwerk van Azure dat wordt weer gegeven voor testfailover, wordt de virtuele test machine in het alfabetisch eerste subnet gemaakt.
   >
   > Als het doel-IP-adres deel uitmaakt van het geselecteerde subnet, Site Recovery probeert de virtuele testfailover te maken met behulp van het doel-IP-adres. Als het doel-IP-adres niet deel uitmaakt van het geselecteerde subnet, wordt de virtuele test machine gemaakt met behulp van het volgende beschik bare IP-adres in het geselecteerde subnet.

### <a name="test-failover-to-a-secondary-site"></a>Failover naar een secundaire site testen

1. Als u repliceert naar een andere on-premises site en u DHCP gebruikt, [stelt u DNS en DHCP in voor](hyper-v-vmm-test-failover.md#prepare-dhcp)de testfailover.
1. Voer een testfailover uit van de virtuele machine van de domein controller die wordt uitgevoerd in het geïsoleerde netwerk. Gebruik het meest recente beschik bare _toepassings consistente_ herstel punt van de virtuele machine van de domein controller om de testfailover uit te voeren.
1. Voer een testfailover uit voor het herstel plan dat virtuele machines bevat waarop de toepassing wordt uitgevoerd.
1. Wanneer het testen is voltooid, moet u de testfailover op de virtuele machine van de domein controller _opschonen_ . Met deze stap wordt de domein controller verwijderd die is gemaakt voor de testfailover.

### <a name="remove-references-to-other-domain-controllers"></a>Verwijzingen naar andere domein controllers verwijderen

Wanneer u een testfailover initieert, neemt u niet alle domein controllers op in het test netwerk. Als u verwijzingen naar andere domein controllers die in uw productie omgeving bestaan wilt verwijderen, moet u mogelijk [FSMO-Active Directory rollen overnemen](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) en [meta gegevens opruimen](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc816907(v=ws.10)) voor ontbrekende domein controllers.

### <a name="issues-caused-by-virtualization-safeguards"></a>Problemen die worden veroorzaakt door veiligheids maatregelen voor virtualisatie

> [!IMPORTANT]
> Enkele van de configuraties die in deze sectie worden beschreven, zijn geen standaard configuraties voor domein controllers. Als u deze wijzigingen niet wilt aanbrengen in een productie domein controller, kunt u een domein controller maken die is toegewezen voor Site Recovery voor het gebruik van een testfailover. Breng deze wijzigingen alleen aan in die domein controller.

Vanaf Windows Server 2012 [zijn extra beveiligingen ingebouwd in Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Deze beveiligingen helpen bij het beveiligen van gevirtualiseerde domein controllers tegen het terugdraaien van Update Sequence Number (USN) als het onderliggende Hyper Visor **-platform VM-GenerationID** ondersteunt. Azure biedt ondersteuning voor **VM-GenerationID**. Daarom hebben domein controllers met Windows Server 2012 of hoger op virtuele machines van Azure deze extra beveiliging.

Wanneer de **VM-GenerationID** opnieuw wordt ingesteld, wordt de **InvocationID** -waarde van de AD DS data base ook opnieuw ingesteld. Daarnaast wordt de groep relatieve ID (RID) verwijderd en `SYSVOL` is de map gemarkeerd als niet-gemachtigd. Zie [Introduction to Active Directory Domain Services Virtualization](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) and safe [Distributed File System Replication (DFSR)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/safely-virtualizing-dfsr/ba-p/424671)voor meer informatie.

Failover naar Azure kan ertoe leiden dat **VM-GenerationID** opnieuw wordt ingesteld. Als u de virtuele machine opnieuw instelt, worden er extra beveiligingen geactiveerd wanneer de domein controller **-** VM wordt gestart in Azure. Dit kan leiden tot een aanzienlijke vertraging in de mogelijkheid om u aan te melden bij de virtuele machine van de domein controller.

Omdat deze domein controller alleen wordt gebruikt in een testfailover, zijn er geen veiligheids maatregelen voor virtualisatie nodig. Om ervoor te zorgen dat de **VM-GenerationID-** waarde voor de virtuele machine van de domein controller niet verandert, kunt u de waarde van de volgende wijzigen `DWORD` in **4** op de on-premises domein controller:

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`

#### <a name="symptoms-of-virtualization-safeguards"></a>Symptomen van de beveiliging van virtualisatie

Als de beveiliging van de virtualisatie wordt geactiveerd na een testfailover, ziet u mogelijk een of meer van de volgende symptomen:

- De waarde van **GenerationID** wordt gewijzigd:

  :::image type="content" source="./media/site-recovery-active-directory/Event2170.png" alt-text="Wijziging van generatie-ID":::

- De waarde van **InvocationID** wordt gewijzigd:

  :::image type="content" source="./media/site-recovery-active-directory/Event1109.png" alt-text="Wijziging van aanroepen-ID":::

- `SYSVOL` map en `NETLOGON` shares zijn niet beschikbaar.

  :::image type="content" source="./media/site-recovery-active-directory/sysvolshare.png" alt-text="Share SYSVOL-map":::

  :::image type="content" source="./media/site-recovery-active-directory/Event13565.png" alt-text="Map NtFrs SYSVOL":::

- DFSR-data bases worden verwijderd.

  :::image type="content" source="./media/site-recovery-active-directory/Event2208.png" alt-text="DFSR-data bases zijn verwijderd":::

### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Problemen met de domein controller oplossen tijdens een testfailover

> [!IMPORTANT]
> Enkele van de configuraties die in deze sectie worden beschreven, zijn geen standaard configuraties voor domein controllers. Als u deze wijzigingen niet wilt aanbrengen in een productie domein controller, kunt u een domein controller maken die is toegewezen voor Site Recovery testfailover. Breng de gewenste wijzigingen aan in die specifieke domein controller.

1. Voer bij de opdracht prompt de volgende opdracht uit om te controleren of `SYSVOL` map en `NETLOGON` map worden gedeeld:

    `NET SHARE`

1. Voer bij de opdracht prompt de volgende opdracht uit om ervoor te zorgen dat de domein controller goed werkt:

    `dcdiag /v > dcdiag.txt`

1. Zoek in het uitvoer logboek naar de volgende tekst. De tekst bevestigt dat de domein controller goed werkt.

    - `passed test Connectivity`
    - `passed test Advertising`
    - `passed test MachineAccount`

Als aan de voor waarden wordt voldaan, is het waarschijnlijk dat de domein controller goed werkt. Als dat niet het geval is, voert u de volgende stappen uit:

1. Een bindende terugzet bewerking uitvoeren voor de domein controller. Houd de volgende informatie in acht:

    - Hoewel de replicatie niet wordt aangeraden met de [File Replication-service (FRS)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/the-end-is-nigh-for-frs-8211-updated-for-ws2016/ba-p/425379)en u FRS-replicatie gebruikt, volgt u de stappen voor een bindende terugzet bewerking. Het proces wordt beschreven in [met behulp van de register sleutel BurFlags om de File Replication-service opnieuw te initialiseren](https://support.microsoft.com/kb/290762).

      Zie voor meer informatie over BurFlags het blog bericht [D2 en D4: wat is het voor?](/archive/blogs/janelewis/d2-and-d4-what-is-it-for).

    - Als u DFSR-replicatie gebruikt, voltooit u de stappen voor een bindende terugzet bewerking. Het proces wordt beschreven in [een bindende en niet-bindende synchronisatie afdwingen voor de DFSR-gerepliceerde SYSVOL-map (zoals "D4/D2" voor FRS)](https://support.microsoft.com/kb/2218556).

      U kunt ook de Power shell-functies gebruiken. Zie voor meer informatie [DFSR-SYSVOL gezaghebbend/niet-bindende terugzet Power shell-functies](/archive/blogs/thbouche/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions).

1. U kunt de eerste synchronisatie vereiste overs Laan door de volgende register sleutel in te stellen op **0** in de on-premises domein controller. Als het `DWORD` niet bestaat, kunt u deze maken onder het knoop punt **para meters** .

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

   Zie voor meer informatie [problemen met de DNS-gebeurtenis-ID 4013 oplossen: de DNS-server kan AD geïntegreerde DNS-zones niet laden](https://support.microsoft.com/kb/2001093).

1. De vereiste uitschakelen dat een globale catalogus server beschikbaar is voor het valideren van de aanmelding van de gebruiker. Om dit te doen, stelt u op de on-premises domein controller de volgende register sleutel in op **1**. Als het `DWORD` niet bestaat, kunt u deze maken onder het **LSA** -knoop punt.

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

   Zie [How the Global Catalog Works](/previous-versions/windows/it-pro/windows-server-2003/cc737410(v=ws.10))(Engelstalig) voor meer informatie.

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS en domein controller op verschillende computers

Als u de domein controller en DNs op dezelfde VM uitvoert, kunt u deze procedure overs Laan.

Als DNS zich niet op dezelfde VM bevindt als de domein controller, moet u een DNS-VM maken voor de testfailover. U kunt een nieuwe DNS-server gebruiken en alle vereiste zones maken. Als uw Active Directory domein bijvoorbeeld is `contoso.com` , kunt u een DNS-zone met de naam maken `contoso.com` . De vermeldingen die overeenkomen met Active Directory moeten als volgt in DNS worden bijgewerkt:

1. Zorg ervoor dat deze instellingen aanwezig zijn voordat een andere virtuele machine in het herstel plan wordt gestart:

   - De zone moet de naam van de hoofd naam van het forest hebben.
   - De zone moet worden ondersteund door een bestand.
   - De zone moet zijn ingeschakeld voor beveiligde en niet-beveiligde updates.
   - De resolver van de virtuele machine die als host fungeert voor de domein controller moet verwijzen naar het IP-adres van de virtuele DNS-machine.

1. Voer de volgende opdracht uit op de VM die als host fungeert voor de domein controller:

   `nltest /dsregdns`

1. Voer de volgende opdrachten uit om een zone toe te voegen aan de DNS-server, niet-beveiligde updates toe te staan en een vermelding voor de zone toe te voegen aan DNS:

   ```cmd
   dnscmd /zoneadd contoso.com  /Primary

   dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1

   dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>

   dnscmd /config contoso.com /allowupdate 1
   ```

## <a name="next-steps"></a>Volgende stappen

Meer [informatie](site-recovery-workload.md) over het beveiligen van zakelijke workloads met Azure site Recovery.
