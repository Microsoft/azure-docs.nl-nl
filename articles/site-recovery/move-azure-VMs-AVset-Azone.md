---
title: VM's verplaatsen naar een Azure-regio met beschikbaarheidszones met Azure Site Recovery
description: Meer informatie over het verplaatsen van virtuele machines naar een beschikbaarheidszone in een andere regio met Site Recovery
services: site-recovery
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: 8224ae4a48bb4915492240c414b90edb86a4c258
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93393129"
---
# <a name="move-azure-vms-into-availability-zones"></a>Virtuele Azure-machines verplaatsen naar beschikbaarheidszones

In deze artikelen wordt beschreven hoe u virtuele Azure-machines verplaatst naar een beschikbaarheidszone in een andere regio. [Raadpleeg dit artikel](./azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md) als u wilt overschakelen naar een andere zone in dezelfde regio.


Beschikbaarheidszones in Azure beschermen uw toepassingen en gegevens tegen storingen in datacentra. Elke beschikbaarheidszone bestaat uit een of meer datacentra die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerk. Om voor tolerantie te zorgen, is er een minimum van drie afzonderlijke zones in alle ingeschakelde regio's. De fysieke scheiding tussen beschikbaarheidszones binnen een Azure-regio beschermt toepassingen en gegevens tegen storingen van het datacentrum. Met beschikbaarheidszones biedt Azure een SLA (Service Level Agreement) van 99,99% voor de uptime van virtuele machines (VM's). Beschikbaarheidszones worden ondersteund in geselecteerde regio's, zoals vermeld in [Regio's die ondersteuning bieden voor beschikbaarheidszones](../availability-zones/az-region.md).

In een scenario waarin u uw virtuele machines als *één exemplaar* in een bepaalde regio hebt vermeld, en u de beschikbaarheid wilt verbeteren door deze te verplaatsen naar een beschikbaarheidszone, kunt u dit doen met behulp van Azure Site Recovery. Deze actie kan verder worden onderverdeeld in:

- Verplaatsen van VM’s met één exemplaar naar beschikbaarheidszones in een doelregio
- Verplaatsen van VM’s naar beschikbaarheidszones in een doelregio

> [!IMPORTANT]
> We adviseren om [Azure Resource Mover](../resource-mover/move-region-availability-zone.md) te gebruiken om virtuele Azure-machines te verplaatsen naar een beschikbaarheidszone in een andere regio. Azure Resource Mover is momenteel beschikbaar in openbare preview en biedt het volgende:
> - Eén hub voor het verplaatsen van resources binnen regio's.
> - Beperkte verplaatsingstijd en complexiteit. Alles wat u nodig hebt, bevindt zich op één locatie.
> - Een eenvoudige en consistente ervaring voor het verplaatsen van verschillende typen Azure-resources.
> - Een eenvoudige manier om afhankelijkheden te identificeren binnen resources die u wilt verplaatsen. Dit helpt om gerelateerde resources samen te verplaatsen zodat alles na de verplaatsing naar verwachting werkt in de doelregio.
> - Automatische opschoning van resources in de bronregio, als u deze na de verplaatsing wilt verwijderen.
> - Testen. U kunt een verplaatsing uitproberen en deze vervolgens verwijderen als u geen volledige verplaatsing wilt uitvoeren.



## <a name="check-prerequisites"></a>Vereisten controleren

- Controleer of de doelregio [ondersteuning biedt voor beschikbaarheidszones](../availability-zones/az-region.md). Controleer of de gewenste combinatie van [bron- en doelregio wordt ondersteund](./azure-to-azure-support-matrix.md#region-support). Maak een weloverwogen beslissing voor de doelregio.
- Zorg ervoor dat u inzicht hebt in de [architectuur en onderdelen voor dit scenario](azure-to-azure-architecture.md).
- Controleer de [beperkingen en vereisten voor ondersteuning](azure-to-azure-support-matrix.md).
- Controleer de accountmachtigingen. Als u pas net uw gratis Azure-account hebt gemaakt, bent u de beheerder van uw abonnement. Als u niet de abonnementsbeheerder bent, neemt u contact op met de beheerder om de machtigingen te krijgen die u nodig hebt. Als u replicatie wilt inschakelen voor een VM en uiteindelijk gegevens wilt kopiëren naar de doelserver met behulp van Azure Site Recovery, moet u het volgende hebben:

    1. Machtigingen voor het maken van een virtuele machine in Azure-resources. De ingebouwde rol *Inzender voor virtuele machines* beschikt over deze machtigingen, waaronder:
        - Machtiging voor het maken van een virtuele machine in de geselecteerde resourcegroep
        - Machtiging voor het maken van een virtuele machine in het geselecteerde virtuele netwerk
        - Machtigingen om naar het geselecteerde opslagaccount te schrijven

    2. Machtiging voor het beheer van Azure Site Recovery-bewerkingen. De rol *Site Recovery-inzender* bevat alle machtigingen die nodig zijn om Site Recovery-acties in een Recovery Services-kluis te kunnen beheren.

## <a name="prepare-the-source-vms"></a>De bron-VM’s voorbereiden

1. Uw virtuele machines moeten beheerde schijven gebruiken als u ze wilt verplaatsen naar een beschikbaarheidszone met Site Recovery. U kunt bestaande Windows-VM's die onbeheerde schijven gebruiken, converteren naar beheerde schijven. Volg de stappen van [Een virtuele Windows-machine van niet-beheerde schijven converteren naar beheerde schijven](../virtual-machines/windows/convert-unmanaged-to-managed-disks.md). Zorg ervoor dat de beschikbaarheidsset is geconfigureerd als *Beheerd*.
2. Controleer of de meest recente basiscertificaten aanwezig zijn op de Azure-VM’s die u wilt verplaatsen. Als de meest recente basiscertificaten niet aanwezig zijn, kan het kopiëren van gegevens naar de doelregio niet worden ingeschakeld vanwege veiligheidsbeperkingen.

3. Voor Windows-VM’s moet u de meest recente Windows-updates op de VM installeren, zodat alle vertrouwde basiscertificaten op de machine aanwezig zijn. In een niet-verbonden omgeving moet u de standaardprocedures van Windows Update en de standaardprocedures voor het bijwerken van de certificaten van uw organisatie volgen.

4. Voor Linux-VM’s volgt u de richtlijnen van de Linux-distributeur voor het verkrijgen van de meest recente basiscertificaten en de certificaatintrekkingslijst op de VM.
5. Zorg ervoor dat u geen verificatieproxy gebruikt om de verbinding met het netwerk te beheren voor de VM’s die u wilt verplaatsen.

6. Controleer [uitgaande connectiviteitsvereisten voor VM's](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity).

7. Identificeer de bronnetwerkindeling en de resources die u momenteel gebruikt voor verificatie, waaronder load balancers, NSG’s en openbare IP-adressen.

## <a name="prepare-the-target-region"></a>De doelregio voorbereiden

1. Controleer of u een Azure-abonnement hebt waarmee u in staat bent om virtuele machines te maken in de doelregio die wordt gebruikt voor herstel na noodgevallen. Neem indien nodig contact op met ondersteuning voor het inschakelen van het vereiste quotum.

2. Zorg ervoor dat uw abonnement voldoende resources heeft die ondersteuning kunnen bieden voor VM’s met grootten die overeenkomen met uw bron-VM’s. Als u Site Recovery gebruikt om gegevens naar de doel te kopiëren, wordt dezelfde grootte of de dichtstbijzijnde grootte voor de doel-VM gekozen.

3. Maak een doelresource voor elk onderdeel dat is geïdentificeerd in de bronindeling voor netwerken. Deze actie zorgt ervoor dat nadat u bent overgestapt naar het doelgebied, uw VM's alle functionaliteit en functies hebben die u in de bron had.

    > [!NOTE]
    > Azure Site Recovery detecteert en maakt automatisch een virtueel netwerk- en opslagaccount wanneer u replicatie voor de bron-VM inschakelt. U deze resources ook vooraf maken en toewijzen aan de VM als onderdeel van de stap replicatie inschakelen. Maar alle andere resources moet u zoals verderop wordt beschreven handmatig maken in de doelregio.

     Raadpleeg de volgende documenten om de meest gebruikte netwerkresources te maken die relevant zijn voor u, op basis van de bron-VM-configuratie.

    - [Netwerkbeveiligingsgroepen](../virtual-network/manage-network-security-group.md)
    - [Load balancers](../load-balancer/index.yml)
    - [Openbare IP](../virtual-network/virtual-network-public-ip-address.md)
    
   Voor andere netwerkonderdelen raadpleegt u de [netwerkondersteuningsdocumentatie.](../index.yml?pivot=products&panel=network)

    > [!IMPORTANT]
    > Zorg ervoor dat u een zone-redundante load balancer in het doel gebruikt. Meer informatie over [Standard Load Balancer en beschikbaarheidszones](../load-balancer/load-balancer-standard-availability-zones.md).

4. [Maak handmatig een niet-productienetwerk](../virtual-network/quick-create-portal.md) in de doelregio als u de configuratie wilt testen voordat u het knippen naar de doelregio uitvoert. Deze aanpak wordt aanbevolen omdat deze minimale interferentie met de productieomgeving veroorzaakt.

## <a name="enable-replication"></a>Replicatie inschakelen
De onderstaande stappen tonen u hoe u met Azure Site Recovery de replicatie van gegevens naar de doelregio inschakelt voordat u ze uiteindelijk naar beschikbaarheidszones verplaatst.

> [!NOTE]
> Deze stappen zijn voor één VM. U kunt hetzelfde uitbreiden naar meerdere VM's. Ga naar de Recovery Services-kluis, selecteer **+ Repliceren** en selecteer samen de relevante VM's.

1. Selecteer in Azure Portal **Virtuele machines** en selecteer de VM die u naar de beschikbaarheidszones wilt verplaatsen.
2. Selecteer in **Bewerkingen** de optie **Herstel na noodgeval**.
3. Selecteer in **Noodherstel configureren** > **Doelregio** de doelregio waarnaar u wilt repliceren. Zorg ervoor dat deze regio beschikbaarheidszones [ondersteunt](../availability-zones/az-region.md).
4. Selecteer **Volgende: Geavanceerde instellingen**.
5. Kies de juiste waarden voor doelabonnement, doel-VM-resourcegroep en virtueel netwerk.
6. In de sectie **Beschikbaarheid** kiest u de beschikbaarheidszone waarnaar u de virtuele machine wilt verplaatsen. 
   > [!NOTE]
   > Als u de optie voor de beschikbaarheidsset of beschikbaarheidszone niet ziet, controleert u of aan de [vereisten](#prepare-the-source-vms) is voldaan en de [voorbereiding](#prepare-the-source-vms) van bron-VM's is voltooid.
  

7. Selecteer **Replicatie inschakelen**. Door deze actie wordt een taak gestart voor het inschakelen van replicatie voor de VM.

## <a name="check-settings"></a>Instellingen controleren

Nadat de replicatietaak is voltooid, kunt u de replicatiestatus controleren, de replicatie-instellingen wijzigen en de implementatie testen.

1. Selecteer in het VM-menu de optie **Herstel na noodgeval**.
2. U kunt de replicatiestatus, de gemaakte herstelpunten, en de bron- en doelregio's op de kaart controleren.


## <a name="test-the-configuration"></a>De configuratie testen

1. Selecteer in het menu van de virtuele machine **Herstel na noodgevallen**.
2. Selecteer het pictogram **Failover testen**.
3. Selecteer in **Failover testen** een herstelpunt om voor de failover te gebruiken:

   - **Laatst verwerkt**: voert een failover van de VM uit naar het laatste herstelpunt dat is verwerkt door de Site Recovery-service. Het tijdstempel wordt weergegeven. Met deze optie wordt er geen tijd besteed aan het verwerken van gegevens, zodat er sprake is van een lage RTO (Recovery Time Objective).
   - **Laatste toepassingsconsistente punt**: met deze optie wordt er een failover uitgevoerd van alle VM's naar het laatste toepassingsconsistente herstelpunt. Het tijdstempel wordt weergegeven.
   - **Aangepast**: selecteer een herstelpunt.

3. Selecteer het Azure-doeltestnetwerk waar u de Azure VM's naartoe wilt verplaatsen om de configuratie te testen. 

    > [!IMPORTANT]
    > U wordt aangeraden een afzonderlijk Azure-VM-netwerk te gebruiken voor de test en niet het productienetwerk in de doelregio waarnaar u uw VM’s wilt verplaatsen.

4. Klik op **OK** om de verplaatsing te testen. Selecteer de VM om de eigenschappen ervan te openen als u de voortgang wilt volgen. U kunt ook de taak **Failover testen** selecteren in kluisnaam > **Instellingen** > **Taken** > **Site Recovery-taken**.
5. Nadat de failover is voltooid, wordt de replica-Azure-VM weergegeven in Azure Portal > **Virtuele machines**. Controleer of de VM draait, de juiste grootte heeft en aangesloten is op het juiste netwerk.
6. Als u de VM wilt verwijderen die is gemaakt als onderdeel van de verplaatsingstest, selecteert u **Failovertest wissen** op het gerepliceerde item. Leg in **Notities** eventuele opmerkingen over de test vast en sla deze op.

## <a name="move-to-the-target-region-and-confirm"></a>Voer de overstap naar de doelregio uit en bevestig dit

1.  Selecteer in het menu van de virtuele machine **Herstel na noodgevallen**.
2. Selecteer het pictogram **Failover**.
3. Bij **Failover** selecteert u **Meest recente**. 
4. Selecteer **Sluit de computer af voordat de failover wordt gestart**. Site Recovery sluit de virtuele bronmachine af voordat de failover wordt geactiveerd. De failover wordt voortgezet zelfs als het afsluiten is mislukt. U kunt de voortgang van de failover volgen op de pagina **Taken**. 
5. Nadat de taak is voltooid, controleert u of de VM zoals verwacht wordt weergegeven in de Azure-doelregio.
6. Klik in **Gerepliceerde items** met de rechtermuisknop op de virtuele machine > **Doorvoeren**. De verplaatsing naar de doelregio wordt voltooid. Wacht tot de doorvoertaak is voltooid.

## <a name="discard-the-resource-in-the-source-region"></a>Verwijder de resource in de bronregio

Ga naar de VM. Selecteer **Replicatie uitschakelen**. Hiermee wordt het proces van het kopiëren van de gegevens voor de VM beëindigd.  

> [!IMPORTANT]
> Voer de voorgaande stap uit om te voorkomen dat na de verplaatsing kosten in rekening worden gebracht voor replicatie van Site Recovery. De bronreplicatie-instellingen worden automatisch opgeschoond. Houd er rekening mee dat de Site Recovery-extensie die is geïnstalleerd als onderdeel van de replicatie, niet wordt verwijderd en handmatig moet worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de beschikbaarheid van een Azure-VM verhoogd door deze te verplaatsen naar een beschikbaarheidsset of een beschikbaarheidszone. U kunt nu herstel na noodgevallen instellen voor de verplaatste VM.

> [!div class="nextstepaction"]
> [Herstel na noodgeval instellen na een migratie](azure-to-azure-quickstart.md)
