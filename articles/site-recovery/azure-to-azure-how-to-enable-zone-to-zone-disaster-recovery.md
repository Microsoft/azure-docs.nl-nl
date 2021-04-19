---
title: Herstel na noodherstel van zone naar zone inschakelen voor Azure Virtual Machines
description: In dit artikel wordt beschreven wanneer en hoe u Herstel na noodherstel van zone naar zone gebruikt voor virtuele Azure-machines.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: 786d877328b1ab3d0f03a75604b7345dba14aa9d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713404"
---
# <a name="enable-azure-vm-disaster-recovery-between-availability-zones"></a>Herstel na noodherstel van azure-VM's tussen beschikbaarheidszones inschakelen

In dit artikel wordt beschreven hoe u virtuele Azure-machines kunt repliceren, failovers en failbacks kunt uitvoeren van de ene beschikbaarheidszone naar een andere, binnen dezelfde Azure-regio.

>[!NOTE]
>
>- Ondersteuning voor herstel na noodherstel van zone naar zone is momenteel beperkt tot de volgende regio's: Azië - zuidoost, Japan - oost, Australië - oost, JIO India - west, VK - zuid, Europa - west, Europa - noord, VS - centraal, VS - oost, VS - oost 2 en VS - west 2.  
>- Site Recovery verplaatst of opgeslagen klantgegevens niet uit de regio waarin deze zijn geïmplementeerd wanneer de klant herstel na noodherstel van zone naar zone gebruikt. Klanten kunnen een Recovery Services-kluis uit een andere regio selecteren als ze dat willen. De Recovery Services-kluis bevat metagegevens, maar geen actuele klantgegevens.

Site Recovery-service draagt bij aan uw strategie voor bedrijfscontinuïteit en herstel na noodherstel door uw zakelijke apps actief te houden tijdens geplande en ongeplande uitval. Het is de aanbevolen optie voor herstel na noodherstel om uw toepassingen actief te houden als er regionale uitval is.

Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone heeft een of meer datacenters. 

Als u VM's wilt verplaatsen naar een beschikbaarheidszone in een andere regio, lees [dan dit artikel.](../resource-mover/move-region-availability-zone.md)

## <a name="using-availability-zones-for-disaster-recovery"></a>Gebruik Beschikbaarheidszones voor herstel na noodherstel 

Normaal gesproken Beschikbaarheidszones gebruikt voor het implementeren van VM's in een configuratie met hoge beschikbaarheid. Ze zijn mogelijk te dicht bij elkaar om te fungeren als een noodhersteloplossing in geval van een natuurramp.

In sommige scenario's kunnen Beschikbaarheidszones echter worden gebruikt voor herstel na nood gevallen:

- Veel klanten die een strategie voor herstel na noodherstel voor metro's hadden en toepassingen on-premises hosten, willen deze strategie soms nabootsen nadat ze toepassingen naar Azure hebben gemigreerd. Deze klanten erkennen het feit dat de strategie voor herstel na noodgeval van metro's mogelijk niet werkt in het geval van een grootschalige fysieke ramp en accepteren dit risico. Voor dergelijke klanten kan zone-naar-zone-noodherstel worden gebruikt als een optie voor herstel na noodherstel.

- Veel andere klanten hebben een gecompliceerde netwerkinfrastructuur en willen deze vanwege de bijbehorende kosten en complexiteit niet opnieuw maken in een secundaire regio. Herstel na noodherstel van zone naar zone vermindert de complexiteit omdat redundante netwerkconcepten worden gebruikt in Beschikbaarheidszones configuratie veel eenvoudiger te maken. Dergelijke klanten geven de voorkeur aan eenvoud en kunnen ook Beschikbaarheidszones voor herstel na noodherstel.

- In sommige regio's die geen gekoppelde regio binnen dezelfde juridische jurisdictie hebben (bijvoorbeeld Azië - zuidoost), kan herstel na noodherstel van zone naar zone fungeren als de feitelijke oplossing voor herstel na noodherstel, omdat dit helpt om juridische naleving te garanderen, omdat uw toepassingen en gegevens niet over de nationale grenzen heen worden verplaatst. 

- Herstel na noodvallen van zone naar zone impliceert replicatie van gegevens over kortere afstanden in vergelijking met Azure naar Azure Disaster Recovery, waardoor u mogelijk een lagere latentie en daardoor een lagere RPO ziet.

Hoewel dit sterke voordelen zijn, is het mogelijk dat zone-naar-zone-noodherstel mogelijk niet voldoet aan de tolerantievereisten in het geval van een natuurramp in de hele regio.

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>Netwerken voor herstel na noodherstel van zone naar zone

Zoals eerder vermeld, vermindert herstel na noodherstel van zone naar zone de complexiteit, omdat redundante netwerkconcepten worden gebruikt in Beschikbaarheidszones configuratie veel eenvoudiger te maken. Het gedrag van netwerkonderdelen in het scenario voor zone-naar-zone-noodherstel wordt hieronder beschreven: 

- Virtual Network: u kunt hetzelfde virtuele netwerk als het bronnetwerk gebruiken voor werkelijke failovers. Gebruik een ander virtueel netwerk dan het virtuele bronnetwerk voor test-failovers.

- Subnet: Failover naar hetzelfde subnet wordt ondersteund.

- Privé-IP-adres: als u statische IP-adressen gebruikt, kunt u dezelfde IP-adressen gebruiken in de doelzone als u ervoor kiest om deze op een dergelijke manier te configureren.

- Versneld netwerken: net als bij Herstel na noodherstel in Azure kunt u Versneld netwerken inschakelen als de VM-SKU dit ondersteunt.

- Openbaar IP-adres: u kunt een eerder gemaakt standaard openbaar IP-adres in dezelfde regio koppelen aan de doel-VM. Openbare BASIS-IP-adressen bieden geen ondersteuning voor scenario's met betrekking tot beschikbaarheidszone.

- Load balancer: Load balancer is een regionale resource en daarom kan de doel-VM worden gekoppeld aan de back-load balancer. Er is load balancer nieuwe versie vereist.

- Netwerkbeveiligingsgroep: u kunt dezelfde netwerkbeveiligingsgroepen gebruiken als die van toepassing zijn op de bron-VM.

## <a name="pre-requisites"></a>Vereisten

Voordat u Zone implementeert in Zone Disaster Recovery voor uw VM's, is het belangrijk om ervoor te zorgen dat andere functies die zijn ingeschakeld op de VM interoperabel zijn met herstel na noodherstel van zone naar zone.

|Functie  | Ondersteuningsverklaring  |
|---------|---------|
|Klassieke VM's   |     Niet ondersteund    |
|ARM-VM's    |    Ondersteund    |
|Azure Disk Encryption v1 (dual pass, with Azure Active Directory (Azure AD))     |     Ondersteund   |
|Azure Disk Encryption v2 (single pass, without Azure AD)    |    Ondersteund    |
|Onbeheerde schijven    |    Niet ondersteund    |
|Managed Disks    |    Ondersteund    |
|Door klant beheerde sleutels    |    Ondersteund    |
|Nabijheidsplaatsingsgroepen    |    Ondersteund    |
|Interoperabiliteit van back-ups    |    Back-up en herstel op bestandsniveau worden ondersteund. Back-up en herstel op schijf- en VM-niveau en worden niet ondersteund.    |
|Hot toevoegen/verwijderen    |    Schijven kunnen worden toegevoegd na het inschakelen van zone-naar-zonereplicatie. Het verwijderen van schijven na het inschakelen van zone-naar-zonereplicatie wordt niet ondersteund.    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>Herstel na nood Site Recovery zone naar zone instellen

### <a name="log-in"></a>Aanmelden

Meld u aan bij Azure Portal.

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>Replicatie inschakelen voor de virtuele Azure-machine voor de omgeving

1. Selecteer in Azure Portal menu Virtuele machines of zoek en selecteer Virtuele machines op een pagina. Selecteer de VM die u wilt repliceren. Voor herstel na noodherstel van zone naar zone moet deze VM zich al in een beschikbaarheidszone bevindt.

2. Selecteer in Bewerkingen de optie Herstel na noodgeval.

3. Zoals hieronder wordt weergegeven, selecteert u op het tabblad Basisinformatie Ja voor Herstel na nood Beschikbaarheidszones?

    ![Pagina Basisinstellingen](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. Als u alle standaardinstellingen accepteert, klikt u op Replicatie controleren en starten, gevolgd door Replicatie starten.

5. Als u wijzigingen wilt aanbrengen in de replicatie-instellingen, klikt u op Volgende: Geavanceerde instellingen.

6. Wijzig waar nodig de standaardinstellingen. Voor gebruikers van Azure naar Azure Disaster Recovery lijkt deze pagina misschien vertrouwd. Meer informatie over de opties op deze blade vindt u [hier](./azure-to-azure-tutorial-enable-replication.md)

    ![Pagina Geavanceerde instellingen](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. Klik op Volgende: Replicatie controleren en starten en klik vervolgens op Replicatie starten.

## <a name="faqs"></a>Veelgestelde vragen

**1. Hoe werken prijzen voor herstel na noodherstel van zone naar zone?**
Prijzen voor herstel na noodherstel van zone naar zone zijn identiek aan de prijzen van Azure naar Azure Disaster Recovery. U vindt hier en hier meer informatie op [de pagina met](https://azure.microsoft.com/pricing/details/site-recovery/) [prijzen.](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) Houd er rekening mee dat de kosten voor egressie die u in zone-naar-zone-noodherstel ziet, lager zijn dan de kosten voor herstel na noodvallen van regio naar regio.

**2. Wat is de SLA voor RTO en RPO?**
De RTO-SLA is hetzelfde als die voor Site Recovery algemeen. We bieden RTO's van maximaal twee uur. Er is geen gedefinieerde SLA voor RPO.

**3. Is de capaciteit gegarandeerd in de secundaire zone?**
Het Site Recovery en het Azure-capaciteitsbeheerteam plannen voldoende infrastructuurcapaciteit. Wanneer u een failover start, zorgen de teams er ook voor dat VM-exemplaren die worden beveiligd door Site Recovery in de doelzone worden geïmplementeerd.

**4. Welke besturingssystemen worden ondersteund?**
Herstel na noodherstel van zone naar zone ondersteunt dezelfde besturingssystemen als Herstel na noodherstel van Azure naar Azure. Raadpleeg hier de [ondersteuningsmatrix](./azure-to-azure-support-matrix.md).

**5. Kunnen de bron- en doelresourcegroepen hetzelfde zijn?**
Nee, u moet een fail over naar een andere resourcegroep.

## <a name="next-steps"></a>Volgende stappen

De stappen die moeten worden gevolgd om een noodhersteloefening, failback, opnieuw beveiligen en failback uit te voeren, zijn hetzelfde als de stappen in het noodherstelscenario van Azure naar Azure.

Volg de stappen die hier worden beschreven om een noodhersteloefening uit te [voeren.](./azure-to-azure-tutorial-dr-drill.md)

Volg de stappen die hier worden beschreven om een failover uit te voeren en VM's opnieuw te veiligen in de secundaire [zone.](./azure-to-azure-tutorial-failover-failback.md)

Volg de stappen die hier worden beschreven om een failback uit te voeren naar de [primaire zone.](./azure-to-azure-tutorial-failback.md)
