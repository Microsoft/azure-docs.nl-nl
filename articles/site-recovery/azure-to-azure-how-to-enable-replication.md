---
title: Replicatie configureren voor virtuele Azure-machines in Azure Site Recovery
description: Meer informatie over het configureren van replicatie naar een andere regio voor Azure-Vm's met behulp van Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2018
ms.openlocfilehash: 427b471158e89b2b3ae4ea6477133f1e69247078
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100518841"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Virtuele Azure-machines repliceren naar een andere Azure-regio


In dit artikel wordt beschreven hoe u de replicatie van virtuele Azure-machines van de ene Azure-regio naar de andere kunt inschakelen.

## <a name="before-you-start"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u de implementatie van Site Recovery hebt voor bereid, zoals beschreven in de [zelf studie Azure voor nood herstel](azure-to-azure-tutorial-enable-replication.md)voor Azure.

Vereisten moeten aanwezig zijn en u moet een Recovery Services kluis hebben gemaakt.


## <a name="enable-replication"></a>Replicatie inschakelen

Schakel replicatie in. Bij deze procedure wordt ervan uitgegaan dat de primaire Azure-regio is Azië-oost en dat de secundaire regio Zuid Azië-oost is.

1. Klik in de kluis op **+ repliceren**.
2. Houd rekening met de volgende velden:
   - **Bron**: het herkomst punt van de virtuele machines, in dit geval **Azure**.
   - **Bron locatie**: de Azure-regio van waaruit u uw vm's wilt beveiligen. Voor deze afbeelding is de bron locatie Azië-oost
   - **Implementatie model**: Azure-implementatie model van de bron machines.
   - **Bron abonnement**: het abonnement waartoe de bron-vm's behoren. Dit kan elk abonnement zijn binnen dezelfde Azure Active Directory-tenant waar uw Recovery Services-kluis zich bevindt.
   - **Resource groep**: de resource groep waartoe de virtuele bron machine behoort. Alle virtuele machines onder de geselecteerde resource groep worden in de volgende stap weer gegeven voor beveiliging.

     ![Scherm opname van de velden die nodig zijn voor het configureren van de replicatie.](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. Klik in **Virtual Machines > virtuele machines selecteren** op elke virtuele machine die u wilt repliceren en selecteer deze. U kunt alleen machines selecteren waarvoor replicatie kan worden ingeschakeld. Klik vervolgens op **OK**.
    ![Scherm afbeelding van waaruit u virtuele machines selecteert.](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. In **instellingen** kunt u de instellingen van de doel site eventueel configureren:

   - **Doel locatie**: de locatie waar de gegevens van de virtuele bron machine worden gerepliceerd. Afhankelijk van de locatie van de geselecteerde machines, geeft Site Recovery u de lijst met geschikte doel regio's. U wordt aangeraden de doel locatie hetzelfde te laten als de locatie van de Recovery Services kluis.
   - **Doelabonnement**: het doelabonnement dat wordt gebruikt voor herstel na noodgevallen. Standaard is het doelabonnement niet hetzelfde als het bronabonnement.
   - **Doel resource groep**: de resource groep waarvan al uw gerepliceerde virtuele machines deel uitmaken.
       - Standaard Site Recovery maakt een nieuwe resource groep in de doel regio met het achtervoegsel ' ASR ' in de naam.
       - Als de resource groep die is gemaakt door Site Recovery al bestaat, wordt deze opnieuw gebruikt.
       - U kunt de instellingen voor de resource groep aanpassen.
       - De locatie van de doel resource groep kan een wille keurige Azure-regio zijn, met uitzonde ring van de regio waarin de bron-Vm's worden gehost.
   - **Virtueel netwerk van doel**: standaard maakt site Recovery een nieuw virtueel netwerk in de doel regio met het achtervoegsel ' ASR ' in de naam. Deze is toegewezen aan uw bron netwerk en wordt gebruikt voor toekomstige beveiliging. [Meer informatie](./azure-to-azure-network-mapping.md) over netwerk toewijzing.
   - **Doel opslag accounts (bron-VM gebruikt geen beheerde schijven)**: standaard maakt site Recovery een nieuw doel-opslag account mimicking de opslag configuratie van de bron-VM. Als het opslag account al bestaat, wordt dit opnieuw gebruikt.
   - **Replica-Managed disks (bron-VM maakt gebruik van beheerde schijven)**: site Recovery maakt nieuwe replica-beheerde schijven in de doel regio om de beheerde schijven van de bron-VM te spie gelen met hetzelfde opslag type (Standard of Premium) als de beheerde schijf van de bron-VM.
   - **Cache opslag accounts**: site Recovery hebt extra opslag account met de naam cache opslag in de bron regio nodig. Alle wijzigingen die zich voordoen op de bron-Vm's, worden bijgehouden en verzonden naar het cache-opslag account voordat ze naar de doel locatie worden gerepliceerd. Dit opslag account moet standaard zijn.
   - **Doel beschikbaarheids sets**: standaard maakt site Recovery een nieuwe beschikbaarheidsset in de doel regio met het achtervoegsel ' ASR ' in de naam, voor virtuele machines die deel uitmaken van een beschikbaarheidsset in de bron regio. Als de beschikbaarheidsset die is gemaakt door Site Recovery al bestaat, wordt deze opnieuw gebruikt.
     >[!NOTE]
     >Configureer tijdens het configureren van de doel beschikbaarheids sets een andere beschikbaarheidsset voor virtuele machines met verschillende groottes. 
     >
   - **Doelbeschikbaarheidszones**: in Site Recovery wordt standaard hetzelfde zonenummer toegewezen als de bronregio in de doelregio, als de doelregio ondersteuning biedt voor beschikbaarheidszones.

     Als de doelregio geen ondersteuning biedt voor beschikbaarheidszones, worden de doel-VM's standaard geconfigureerd als enkele exemplaren. Indien vereist, kunt u dergelijke VM's configureren als onderdeel van beschikbaarheidssets in de doelregio door op Aanpassen te klikken.

     >[!NOTE]
     >Nadat u replicatie hebt ingeschakeld, kunt u het type beschikbaarheid - enkel exemplaar, beschikbaarheidsset of beschikbaarheidszone - niet meer wijzigen. Als u het type beschikbaarheid wilt wijzigen, moet u replicatie uitschakelen en opnieuw inschakelen.
     >

   - **Replicatie beleid**: Hiermee worden de instellingen voor de Bewaar geschiedenis voor herstel punten en de frequentie van consistente moment opnamen voor apps gedefinieerd. Azure Site Recovery maakt standaard een nieuw replicatie beleid met de standaard instellingen van 24 uur voor retentie van herstel punten en 4 uur voor de frequentie van de consistente moment opname van de app.

     ![Replicatie inschakelen](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Replicatie inschakelen voor toegevoegde schijven

Als u schijven toevoegt aan een virtuele machine van Azure waarvoor replicatie is ingeschakeld, gebeurt het volgende:
-   Bij de replicatie status voor de VM wordt een waarschuwing weer gegeven en een opmerking met de melding dat er een of meer schijven beschikbaar zijn voor beveiliging.
-   Als u beveiliging inschakelt voor de toegevoegde schijven, verdwijnt de waarschuwing na de initiële replicatie van de schijf.
-   Als u ervoor kiest geen replicatie voor de schijf in te scha kelen, kunt u de waarschuwing negeren.


    ![Nieuwe schijf toegevoegd](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Ga als volgt te werk om replicatie in te scha kelen voor een toegevoegde schijf:

1.  Klik in de kluis > **gerepliceerde items** op de virtuele machine waaraan u de schijf hebt toegevoegd.
2.  Klik op **schijven** en selecteer vervolgens de gegevens schijf waarvoor u replicatie wilt inschakelen (deze schijven hebben een niet- **beveiligde** status).
3.  Klik in **schijf Details** op **replicatie inschakelen**.

    ![Replicatie inschakelen voor toegevoegde schijf](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

Nadat de taak voor het inschakelen van de replicatie is uitgevoerd en de initiële replicatie is voltooid, wordt de replicatie status waarschuwing voor het probleem van de schijf verwijderd.



## <a name="customize-target-resources"></a>Doel resources aanpassen

U kunt de standaard doel instellingen wijzigen die door Site Recovery worden gebruikt.

1. Klik op **aanpassen:** naast het doel abonnement om het standaard doel abonnement te wijzigen. Selecteer het abonnement in de lijst met alle abonnementen die beschikbaar zijn in dezelfde Azure Active Directory (AAD)-Tenant.

2. Klik op **aanpassen:** als u de standaard instellingen wilt wijzigen:
    - Selecteer de resource groep in de **doel resource groep** in de lijst met alle resource groepen op de doel locatie van het abonnement.
    - Selecteer in **doel-virtueel netwerk** het netwerk uit een lijst met alle virtuele netwerken op de doel locatie.
    - In de **beschikbaarheidsset** kunt u instellingen voor beschikbaarheids sets toevoegen aan de VM als deze deel uitmaken van een beschikbaarheidsset in de bron regio.
    - Selecteer in **doel opslag accounts** het account dat u wilt gebruiken.

        ![Scherm afbeelding die laat zien hoe u de instellingen voor het doel abonnement kunt aanpassen.](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Klik op **aanpassen:** als u de replicatie-instellingen wilt wijzigen.
4. In **multi-VM-consistentie** selecteert u de virtuele machines die u samen wilt repliceren.
    - Alle machines in een replicatiegroep hebben gedeelde crash-consistente en app-consistente herstelpunten bij een failover.
    - Het inschakelen van multi-VM-consistentie kan de prestaties van de werk belasting beïnvloeden (omdat dit CPU-intensief is). De service mag alleen worden ingeschakeld als op computers dezelfde werk belasting wordt uitgevoerd en u consistentie op meerdere computers nodig hebt.
    - Als een toepassing bijvoorbeeld 2 SQL Server virtuele machines en twee webservers bevat, moet u alleen de SQL Server virtuele machines toevoegen aan een replicatie groep.
    - U kunt Maxi maal 16 Vm's in een replicatie groep kiezen.
    - Als u multi-VM-consistentie inschakelt, communiceren machines in de replicatiegroep met elkaar via poort 20004.
    - Zorg ervoor dat er geen firewall apparaat is die de interne communicatie tussen de Vm's via poort 20004 blokkeert.
    - Als u wilt dat Linux-Vm's deel uitmaken van een replicatie groep, zorgt u ervoor dat het uitgaande verkeer op poort 20004 hand matig wordt geopend volgens de richt lijnen voor de specifieke Linux-versie.
![Scherm opname van de instellingen voor de consistentie van meerdere VM'S.](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)

5. Klik op **doel resource maken**  >  **replicatie inschakelen**.
6. Nadat de Vm's zijn ingeschakeld voor replicatie, kunt u de status van de VM controleren onder **gerepliceerde items**

>[!NOTE]
>
> - Tijdens de eerste replicatie kan het enige tijd duren voordat de status wordt vernieuwd, zonder dat er een voortgang wordt gemaakt. Klik op de knop **vernieuwen** om de meest recente status op te halen.
> - Als er in de afgelopen 60 minuten geen herstel punt is gegenereerd, wordt de replicatie status van de virtuele machine kritiek.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie](site-recovery-test-failover-to-azure.md) over het uitvoeren van een testfailover.
