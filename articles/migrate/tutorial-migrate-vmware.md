---
title: "Azure Migrate: Server Migration voor het migreren van VMware-VM's zonder agent"
description: Leer hoe u een migratie zonder agent voor VMware-VM's uitvoert met Azure Migrate.
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: mvc
ms.openlocfilehash: 202886bc0a7738f8211cfea6d90a380c7bac82b8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492944"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>VMware-VM's migreren naar Azure (zonder agent)

In dit artikel wordt beschreven hoe u on-premises VMware-VM's migreert naar Azure, met behulp van het hulpprogramma [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool), met migratie zonder agent. U kunt VMware-VM's ook migreren met migratie op basis van een agent. [Vergelijk](server-migrate-overview.md#compare-migration-methods) de methoden.

Deze zelfstudie is de derde in een reeks die laat zien hoe u VMware-VM's kunt evalueren en migreren naar Azure. 

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een haalbaarheidstest kunt instellen. Waar mogelijk maken zelfstudies gebruik van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. 


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Het hulpprogramma Azure Migrate: Servermigratie toevoegen.
> * Ontdekken welke VM's u wilt migreren.
> * Beginnen met repliceren van VM’s.
> * Een testmigratie uitvoeren om te controleren of alles goed werkt.
> * Een volledige VM-migratie uitvoeren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voordat u aan deze zelfstudie begint, dient u eerst:

1. [Voltooi de eerste zelfstudie](./tutorial-discover-vmware.md) om Azure en VMware voor te bereiden voor migratie.
2. U wordt aangeraden de tweede zelfstudie voor het [evalueren van VMware-VM’s](./tutorial-assess-vmware-azure-vm.md) te voltooien voordat u de VM’s migreert naar Azure, maar dit is niet verplicht. 
3. Ga naar het al gemaakte project of [maak een nieuw project](./create-manage-projects.md).
4. Controleer de machtigingen voor uw Azure-account. U hebt voor uw Azure-account machtigingen nodig om een virtuele machine te maken en naar een beheerde Azure-schijf te schrijven.

## <a name="set-up-the-azure-migrate-appliance"></a>Het Azure Migrate-apparaat instellen

Met Azure Migrate: Server Migration wordt een lichtgewicht VMWare-VM-apparaat uitgevoerd dat wordt gebruikt voor ontdekking, evaluatie, en migratie zonder agent voor VMWare-VM’s. Als u de [zelfstudie voor evaluatie](./tutorial-assess-vmware-azure-vm.md) hebt gevolgd, hebt u het apparaat al ingesteld. Als u dit nog niet hebt gedaan, stelt het apparaat nu in met behulp van een van deze methoden:

- **OVA-sjabloon**: [Instellen](how-to-set-up-appliance-vmware.md) op een VMware-VM met behulp van een gedownloade OVA-sjabloon.
- **Script**: [Instellen](deploy-appliance-script.md) op een VMware-VM of fysieke machine met behulp van een PowerShell-installatiescript. Deze methode moet worden gebruikt als u een VM niet kunt instellen met behulp van een OVA-sjabloon, of als u zich in Azure Government bevindt.

Nadat u het apparaat hebt gemaakt, controleert u of er verbinding kan worden gemaakt met Azure Migrate-serverevaluatie, configureert u het voor de eerste keer en registreert u het bij het Azure Migrate-project.

## <a name="replicate-vms"></a>VM's repliceren

Nadat u het apparaat hebt ingesteld en de detectie hebt voltooid, kunt u beginnen met de replicatie van VMware-VM’s in Azure. 

- U kunt maximaal 300 replicaties tegelijk uitvoeren.
- In de portal kunt u maximaal 10 VM's tegelijk selecteren voor migratie. Als u meer machines wilt migreren, voegt u ze toe aan groepen in batches van 10.

Schakel als volgt replicatie in:

1. In het Azure Migrate-project > **Servers**, **Azure Migrate: Servermigratie** klikt u op **Repliceren**.

    ![VM's repliceren](./media/tutorial-migrate-vmware/select-replicate.png)

2. In **Repliceren** > **Broninstellingen** > **Zijn uw machines gevirtualiseerd?** selecteert u **Ja, met VMware vSphere**.
3. In **On-premises apparaat** selecteert u de naam van het Azure Migrate-apparaat dat u instelt > **OK**. 

    ![Broninstellingen](./media/tutorial-migrate-vmware/source-settings.png)

4. Selecteer in **Virtuele machines** de machines die u wilt repliceren. Als u VM-groottes en schijftypen wilt aanpassen vanuit een evaluatie, als u er een hebt uitgevoerd, selecteert u in **Migratie-instellingen importeren vanuit een Azure Migrate-evaluatie?** de optie **Ja**. Selecteer vervolgens de VM-groep en evaluatienaam. Als u geen evaluatie-instellingen gebruikt, selecteert u **Nee**.
   
    ![Evaluatie selecteren](./media/tutorial-migrate-vmware/select-assessment.png)

5. Selecteer in **Virtuele machines** de VM’s die u wilt migreren. Klik vervolgens op **Volgende: Doelinstellingen**.

    ![VM's selecteren](./media/tutorial-migrate-vmware/select-vms.png)

6. Selecteer in **Doelinstellingen** het abonnement en de doelregio. Geef de resourcegroep op waarin de Azure-VM’s zich na de migratie bevinden.
7. Selecteer in **Virtual Network** het Azure-VNet/subnet waaraan de Azure-VM's na de migratie worden gekoppeld.
8. Selecteer in **Beschikbaarheidsopties**:
    -  Beschikbaarheidszone, om de gemigreerde computer vast te maken aan een specifieke beschikbaarheidszone in de regio. Gebruik deze optie om servers te distribueren die een toepassingslaag met meerdere knooppunten in de beschikbaarheidszones vormen. Als u deze optie selecteert, moet u op het tabblad Compute de beschikbaarheidszone opgeven die moet worden gebruikt voor elk van de geselecteerde computers. Deze optie is alleen beschikbaar als de doelregio die voor de migratie is geselecteerd, ondersteuning biedt voor beschikbaarheidszones
    -  Beschikbaarheidsset, om de gemigreerde machine in een beschikbaarheidsset te plaatsen. De doelresourcegroep die is geselecteerd, moet een of meer beschikbaarheidssets bevatten om deze optie te kunnen gebruiken.
    - Er is geen optie voor infrastructuurredundantie vereist als u geen van deze beschikbaarheidsconfiguraties nodig hebt voor de gemigreerde computers.

9. In **Azure Hybrid Benefit**:

    - Selecteer **Nee** als u Azure Hybrid Benefit niet wilt toepassen. Klik op **Volgende**.
    - Selecteer **Ja** als u Windows Server-computers hebt die worden gedekt met actieve softwareverzekering of Windows Server-abonnementen en u het voordeel wilt toepassen op de machines die u migreert. Klik op **Volgende**.

    ![Doelinstellingen](./media/tutorial-migrate-vmware/target-settings.png)

10. Controleer bij **Compute** naam, grootte, type besturingssysteemschijf en beschikbaarheidsconfiguratie van de VM (indien geselecteerd in de vorige stap). VM's moeten voldoen aan de [Azure-vereisten](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

    - **VM-grootte**: Als u evaluatie-aanbevelingen gebruikt, bevat het vervolgkeuzemenu voor de VM-grootte de aanbevolen grootte. Anders kiest Azure Migrate een grootte op basis van de dichtstbijzijnde overeenkomst in het Azure-abonnement. U kunt ook handmatig een grootte kiezen in **Azure VM-grootte**. 
    - **Besturingssysteemschijf**: Geef de besturingssysteemschijf (opstarten) voor de VM op. De besturingssysteemschijf is de schijf die de bootloader en het installatieprogramma van het besturingssysteem bevat. 
    - **Beschikbaarheidszone**: Geef de beschikbaarheidszone op die moet worden gebruikt.
    - **Beschikbaarheidsset**: Geef de beschikbaarheidsset op die moet worden gebruikt.

> [!NOTE]
>Als u een andere beschikbaarheidsoptie wilt selecteren voor een set virtuele machines, gaat u naar stap 1 en herhaalt u de stappen door andere beschikbaarheidsopties te selecteren na het starten van de replicatie voor één set virtuele machines.


 ![VM-rekeninstellingen](./media/tutorial-migrate-vmware/compute-settings.png)

11. Geef in **Schijven** op of de VM-schijven moeten worden gerepliceerd in Azure en selecteer het schijftype (standaard SSD/HDD of premium beheerde schijven) in Azure. Klik op **Volgende**.
   
    ![Schermopname met het tabblad Schijven van het dialoogvenster Repliceren.](./media/tutorial-migrate-vmware/disks.png)

12. Controleer in **Replicatie controleren en beginnen** de instellingen en klik op **Repliceren** om de eerste replicatie van de servers te beginnen.

> [!NOTE]
> U kunt de replicatie-instellingen op elk gewenst moment bijwerken voordat de replicatie begint. (**Beheren** > **Machines repliceren**). U kunt de instellingen niet meer wijzigen nadat de replicatie is begonnen.

### <a name="provisioning-for-the-first-time"></a>Voor de eerste keer inrichten

Als dit het eerste project is dat u in het project repliceert, worden deze resources automatisch ingericht met Server Migration, in dezelfde resourcegroep als het project.

- **Service Bus**: Server Migration maakt gebruik van de Service Bus voor het verzenden van berichten voor replicatie-indeling naar het apparaat.
- **Gateway-opslagaccount**: Server Migration gebruikt het opslagaccount van de gateway om statusinformatie op te slaan over de virtuele machines die worden gerepliceerd.
- **Logboekopslagaccount**: Het Azure Migrate-apparaat uploadt replicatielogboeken voor VM's naar een logboekopslagaccount. Azure Migrate past de replicatiegegevens toe op beheerde replicaschijven.
- **Sleutelkluis**: Het Azure Migrate-apparaat gebruikt de sleutelkluis voor het beheren van verbindingsreeksen voor de Service Bus en toegangssleutels voor de opslagaccounts die worden gebruikt voor replicatie.

## <a name="track-and-monitor"></a>Bijhouden en bewaken

1. Houd de taakstatus bij in de portalmeldingen.
2. Bewaak de replicatiestatus door te klikken op **Replicerende servers** in **Azure Migrate: Server Migration**.

     ![Replicatie bewaken](./media/tutorial-migrate-vmware/replicating-servers.png)

Replicatie vindt als volgt plaats:
- Wanneer deze taak is voltooid, beginnen de machines hun initiële replicatie naar Azure.
- Tijdens de eerste replicatie wordt een VM-momentopname gemaakt. Schijfgegevens uit de momentopname worden gerepliceerd naar beheerde replicaschijven in Azure.
- Nadat de initiële replicatie is voltooid, begint de deltareplicatie. Incrementele wijzigingen van on-premises schijven worden periodiek gerepliceerd naar de replicaschijven in Azure.

## <a name="run-a-test-migration"></a>Een testmigratie uitvoeren


Wanneer de deltareplicatie begint, kunt u een testmigratie voor de virtuele machines uitvoeren voordat u een volledige migratie naar Azure uitvoert. We raden u ten zeerste aan om dit ten minste één keer te doen voor elke machine voordat u deze migreert.

- Bij het uitvoeren van een testmigratie wordt gecontroleerd of de migratie werkt zoals verwacht, zonder dat dit van invloed is op de on-premises machines - die operationeel blijven - en u door kunt gaan met repliceren. 
- Met een testmigratie wordt de migratie gesimuleerd door een Azure-VM te maken met behulp van gerepliceerde gegevens (die meestal worden gemigreerd naar een niet-productie-VNet in uw Azure-abonnement).
- U kunt de gerepliceerde Azure-VM gebruiken om de migratie te valideren, apps te testen en problemen op te lossen voordat u de volledige migratie uitvoert.

Ga als volgt te werk om een testmigratie uit te voeren:


1. In **Migratiedoelen** > **Servers** > **Azure Migrate: Servermigratie** klikt u op **Gemigreerde servers testen**.

     ![Gemigreerde servers testen](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Klik met de rechtermuisknop op de te testen VM en klik vervolgens op **Migratie testen**.

    ![Migratie testen](./media/tutorial-migrate-vmware/test-migrate.png)

3. Selecteer in **Migratie testen** het Azure Vnet waarin de Azure-VM zich na migratie bevindt. We raden u aan geen productie-VNet te gebruiken.
4. De taak **Migratie testen** wordt gestart. Houd de taak in portalmeldingen in de gaten.
5. Nadat de migratie is voltooid, bekijkt u de gemigreerde Azure-VM in **Virtuele machines** in de Azure-portal. De machinenaam heeft het achtervoegsel **-Test**.
6. Nadat de test is afgerond, klikt u met de rechtermuisknop op de Azure-VM in **Machines repliceren** en klikt u op **Testmigratie opschonen**.

    ![Migratie opschonen](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Virtuele machines migreren

Nadat u hebt geverifieerd dat de testmigratie naar verwachting werkt, kunt u de on-premises machines migreren.

1. In het Azure Migrate-project > **Servers** > **Azure Migrate: Servermigratie** klikt u op **Servers repliceren**.

    ![Servers repliceren](./media/tutorial-migrate-vmware/replicate-servers.png)

2. Klik in **Machines repliceren** met de rechtermuisknop op de VM > **Migreren**.
3. In **Migreren** > **Virtuele machines afsluiten en geplande migratie uitvoeren zonder gegevensverlies** selecteert u **Ja** > **OK**.
    - Azure Migrate sluit standaard de on-premises VM af en voert de replicatie op aanvraag uit om VM-wijzigingen die sinds de laatste replicatie zijn opgetreden, te synchroniseren. Zo gaan er geen gegevens verloren.
    - Als u de VM niet wilt afsluiten, selecteert u **Nee**
4. Er wordt een migratietaak gestart voor de VM. Volg de taak in Azure-meldingen.
5. Nadat de taak is afgerond, kunt u de VM bekijken en beheren vanaf de pagina **Virtuele machines**.

## <a name="complete-the-migration"></a>Migratie voltooien

1. Nadat de migratie is voltooid, klikt u met de rechtermuisknop op de VM > **Replicatie stoppen**. Hiermee stopt de replicatie voor de on-premises machine, en worden de gegevens over de replicatiestatus voor de VM opgeschoond.
2. Installeer de Azure VM [Windows](../virtual-machines/extensions/agent-windows.md)- of [Linux](../virtual-machines/extensions/agent-linux.md)-agent op de gemigreerde computers.
3. Voer correcties van de app uit na de migratie, zoals updates van de databaseverbindingsreeksen en webserverconfiguraties.
4. Voer acceptatietesten van de toepassing en de migratie uit op de gemigreerde toepassing die nu wordt uitgevoerd in Azure.
5. Leid het verkeer naar het gemigreerde Azure VM-exemplaar.
6. Verwijder de on-premises VM's uit uw lokale VM-inventaris.
7. Verwijder de on-premises VM's uit de lokale back-ups.
8. Werk eventuele interne documentatie bij met de nieuwe locatie en het nieuwe IP-adres van de Azure VM's. 

## <a name="post-migration-best-practices"></a>Best practices na de migratie

- Voor grotere flexibiliteit:
    - Houd uw gegevens veilig door back-ups van virtuele Azure VM‘s te maken met behulp van de Azure Backup-service. [Meer informatie](../backup/quick-backup-vm-portal.md).
    - Houd workloads continu beschikbaar door Azure VM‘s naar een secundaire regio te repliceren met Site Recovery. [Meer informatie](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Voor betere beveiliging:
    - Vergrendel en beperk de toegang van binnenkomend verkeer met [Just-in-time-beheer van Azure Security Center](../security-center/security-center-just-in-time.md).
    - Beperk het netwerkverkeer naar beheereindpunten met [Netwerkbeveiligingsgroepen](../virtual-network/network-security-groups-overview.md).
    - Implementeer [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) om schijven te beveiligen en gegevens te beschermen tegen diefstal en onbevoegde toegang.
    - Lees meer informatie over [IaaS-resources beveiligen](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) en bezoek het [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Voor controle en beheer:
-  Overweeg de implementatie van [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) om uw resourcegebruik en uitgaven te bewaken.


## <a name="next-steps"></a>Volgende stappen

Onderzoek de [cloudmigratiereis](/azure/architecture/cloud-adoption/getting-started/migrate) in het Azure Cloud Adoption Framework.