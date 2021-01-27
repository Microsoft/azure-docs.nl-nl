---
title: Virtuele Hyper-V-machines naar Azure migreren met Azure Migrate Server Migration
description: Ontdek hoe u virtuele Hyper-V-machines naar Azure migreert met Azure Migrate Server Migration
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom:
- MVC
- fasttrack-edit
ms.openlocfilehash: 9d0fa516fefefe4c3d8e67c3e6d592ec4274943c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878169"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Virtuele Hyper-V-machines naar Azure migreren 

In dit artikel wordt beschreven hoe u on-premises virtuele Hyper-V-machines naar Azure migreert met het hulpprogramma [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool).

Deze zelfstudie is de derde in een reeks die laat zien hoe u machines kunt evalueren en migreren naar Azure. 

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een haalbaarheidstest kunt instellen. Waar mogelijk maken zelfstudies gebruik van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. 

 In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Voeg het Azure Migrate: Server Migration-hulpprogramma toe.
> * Ontdekken welke VM's u wilt migreren.
> * Beginnen met repliceren van VM's.
> * Voer een testmigratie uit om te controleren of alles goed werkt.
> * Een volledige VM-migratie uitvoeren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prerequisites"></a>Vereisten


Voordat u aan deze zelfstudie begint, dient u eerst:

1. [Beoordeel](hyper-v-migration-architecture.md) de Hyper-V-migratiearchitectuur.
2. [Beoordeel](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) vereisten voor de Hyper-V-host voor migratie en de Azure-URL's waar Hyper-V-hosts en-clusters toegang nodig hebben voor VM-migratie.
3. [Beoordeel](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) de vereisten voor virtuele Hyper-V-machines die u naar Azure wilt migreren.
4. U wordt aangeraden om [Hyper-V-VM's te beoordelen](tutorial-assess-hyper-v.md) voordat u de VM’s migreert naar Azure, maar dit is niet verplicht.
5. Ga naar het al gemaakte project of [maak een nieuw project](./create-manage-projects.md).
6. Controleer de machtigingen voor uw Azure-account. U hebt voor uw Azure-account machtigingen nodig om een virtuele machine te maken en naar een beheerde Azure-schijf te schrijven.

## <a name="download-and-install-the-provider"></a>De provider downloaden en installeren

Voor de migratie van virtuele Hyper-V-machines installeert Azure Migrate: Server Migration softwareproviders (Microsoft Azure Site Recovery-provider en Microsoft Azure Recovery Service-agent) op Hyper-V-hosts of clusterknooppunten. Merk op dat de [Azure Migrate-toepassing](migrate-appliance.md) niet wordt gebruikt voor Hyper-V-migratie.

1. In het Azure Migrate-project > **Servers**, in **Azure Migrate: Server Migration**, klikt u op **Ontdekken**.
2. In **Machines ontdekken** > **Zijn de machines gevirtualiseerd?** selecteert u **Ja, met Hyper-V**.
3. Selecteer in **Doelregio** de Azure-regio waarnaar u de machines wilt migreren.
6. Selecteer **Bevestig dat de doelregio voor migratie regionaam is**.
7. Klik op **Resources maken**. Hiermee maakt u een Azure Site Recovery-kluis op de achtergrond.
    - Als u migratie al hebt ingesteld met Azure Migrate Server Migration, wordt deze optie niet weergegeven omdat er eerder resources zijn ingesteld.
    - U kunt de doelregio voor dit project niet wijzigen nadat u op deze knop hebt geklikt.
    - Alle volgende migraties zijn naar deze regio.
    
8. In **Hyper-V-hostservers voorbereiden** download u de Hyper-V-replicatieprovider en het registratiesleutelbestand.
    - De registratiesleutel is vereist om de Hyper-V-host te registreren bij Azure Migrate Server Migration.
    - De sleutel blijft vijf dagen na het genereren ervan geldig.

    ![Provider en sleutel downloaden](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Kopieer het installatiebestand van de provider en het registratiesleutelbestand naar elke Hyper-V-host (of elk clusterknooppunt) waarop de VM's worden uitgevoerd die u wilt repliceren.
5. Voer het installatiebestand van de provider uit op elke host, zoals beschreven in de volgende procedure.
6. Nadat u de provider op de hosts hebt geïnstalleerd, klikt u in **Machines ontdekken** op **Registratie voltooien**.

    ![Registratie voltooien](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Het kan tot 15 minuten duren nadat de registratie is voltooid voordat de gedetecteerde VM's worden weergegeven in Azure Migrate Server Migration. Als er VM's worden gedetecteerd, neemt het aantel **Gedetecteerde servers** toe.

![Gedetecteerde servers](./media/tutorial-migrate-hyper-v/discovered-servers.png)


## <a name="replicate-hyper-v-vms"></a>Virtuele Hyper-V-machines repliceren

Als de detectie is voltooid, kunt u de replicatie van virtuele Hyper-V-machines naar Azure beginnen.

> [!NOTE]
> U kunt maximaal 10 machines tegelijk repliceren. Als u meer wilt repliceren, repliceert u ze in batches van 10.

1. In het Azure Migrate-project > **Servers**, **Azure Migrate: Servermigratie** klikt u op **Repliceren**.
2. In **Repliceren** > **Broninstellingen** > **Zijn uw machines gevirtualiseerd?** selecteert u **Ja, met Hyper-V**. Klik vervolgens op **Volgende: Virtuele machines**.
3. Selecteer in **Virtuele machines** de machines die u wilt repliceren.
    - Als u een evaluatie voor de VM's hebt uitgevoerd, kunt u aanbevelingen voor de VM-grootte en het schijftype (premium/standard) toepassen vanuit de resultaten. Als u dit wilt doen, selecteert u in **Wilt u de migratie-instellingen van een Azure Migrate-evaluatie importeren?** de optie **Ja**.
    - Als u geen evaluatie hebt uitgevoerd of als u de instellingen daarvan niet wilt gebruiken, selecteert u de optie **Nee**.
    - Als u hebt geselecteerd dat u de evaluatie wilt gebruiken, selecteert u de VM-groep en vervolgens de naam van de evaluatie.

        ![Evaluatie selecteren](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. Zoek in **Virtuele machines** naar eigen inzicht naar VM's en controleer elke VM die u wilt migreren. Klik vervolgens op **Volgende: Doelinstellingen**.

    ![VM's selecteren](./media/tutorial-migrate-hyper-v/select-vms.png)

5. Selecteer in **Doelinstellingen** de doelregio waarnaar u migreert, het abonnement en de resourcegroep waarin de Azure-VM's na de migratie moeten worden geplaatst.
7. Selecteer in **Opslagaccount voor replicatie** het Azure Storage-account waarin gerepliceerde gegevens worden opgeslagen in Azure.
8. Selecteer in **Virtueel netwerk** het Azure-VNet/subnet waaraan de Azure-VM's na de migratie worden toegevoegd.
9. Selecteer in **Beschikbaarheidsopties**:
    -  Beschikbaarheidszone, om de gemigreerde computer vast te maken aan een specifieke beschikbaarheidszone in de regio. Gebruik deze optie om servers te distribueren die een toepassingslaag met meerdere knooppunten in de beschikbaarheidszones vormen. Als u deze optie selecteert, moet u op het tabblad Compute de beschikbaarheidszone opgeven die moet worden gebruikt voor elk van de geselecteerde computers. Deze optie is alleen beschikbaar als de doelregio die voor de migratie is geselecteerd, ondersteuning biedt voor beschikbaarheidszones
    -  Beschikbaarheidsset, om de gemigreerde machine in een beschikbaarheidsset te plaatsen. De doelresourcegroep die is geselecteerd, moet een of meer beschikbaarheidssets bevatten om deze optie te kunnen gebruiken.
    - Er is geen optie voor infrastructuurredundantie vereist als u geen van deze beschikbaarheidsconfiguraties nodig hebt voor de gemigreerde computers.
10. In **Azure Hybrid Benefit**:

    - Selecteer **Nee** als u Azure Hybrid Benefit niet wilt toepassen. Klik op **Volgende**.
    - Selecteer **Ja** als u Windows Server-computers hebt die worden gedekt met actieve softwareverzekering of Windows Server-abonnementen en u het voordeel wilt toepassen op de machines die u migreert. Klik op **Volgende**.

    ![Doelinstellingen](./media/tutorial-migrate-hyper-v/target-settings.png)

11. Controleer bij **Compute** naam, grootte, type besturingssysteemschijf en beschikbaarheidsconfiguratie van de VM (indien geselecteerd in de vorige stap). VM's moeten voldoen aan de [Azure-vereisten](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).

    - **VM-grootte**: Als u aanbevelingen voor evaluatie gebruikt, bevat het vervolgkeuzemenu VM-grootte de aanbevolen grootte. Anders kiest Azure Migrate een grootte op basis van de dichtstbijzijnde overeenkomst in het Azure-abonnement. U kunt ook handmatig een grootte kiezen in **Azure VM-grootte**. 
    - **Besturingssysteemschijf**: Geef de besturingssysteemschijf (opstarten) voor de VM op. De besturingssysteemschijf is de schijf die de bootloader en het installatieprogramma van het besturingssysteem bevat. 
    - **Beschikbaarheidsset**: Als de VM na de migratie in een Azure-beschikbaarheidsset moet worden geplaatst, geeft u deze set op. De set moet zich bevinden in de doelresourcegroep die u voor de migratie opgeeft.

    ![VM-rekeninstellingen](./media/tutorial-migrate-hyper-v/compute-settings.png)

12. Geef in **Schijven** de VM-schijven op die moeten worden gerepliceerd naar Azure. Klik op **Volgende**.
    - U kunt schijven uitsluiten van replicatie.
    - Als u schijven uitsluit, zijn deze na migratie niet beschikbaar in de Azure-VM. 

    ![Schermopname met het tabblad Schijven van het dialoogvenster Repliceren.](./media/tutorial-migrate-hyper-v/disks.png)

13. Controleer in **Replicatie controleren en beginnen** de instellingen en klik op **Repliceren** om de eerste replicatie van de servers te beginnen.

> [!NOTE]
> U kunt de replicatie-instellingen op elk gewenst moment bijwerken voordat de replicatie begint. U doet dat in **Beheren** > **Machines repliceren**. De instellingen kunnen niet meer worden gewijzigd nadat de replicatie is begonnen.

## <a name="provision-for-the-first-time"></a>Voor de eerste keer inrichten

Als dit de eerste VM is die u repliceert in het Azure Migrate-project, richt Azure Migrate: Server Migration deze resources automatisch in dezelfde resourcegroep in als het project.

- **Service Bus**: Azure Migrate: Server Migrations gebruikt de Service Bus voor het verzenden van berichten voor replicatie-indeling naar het apparaat.
- **Gateway-opslagaccount**: Azure Migrate: Server Migration gebruikt het opslagaccount van de gateway om statusinformatie op te slaan over de virtuele machines die worden gerepliceerd.
- **Logboekopslagaccount**: Het Azure Migrate-apparaat uploadt replicatielogboeken voor VM's naar een logboekopslagaccount. Azure Migrate past de replicatiegegevens toe op door de replica beheerde schijven.
- **Sleutelkluis**: Het Azure Migrate-apparaat gebruikt de sleutelkluis voor het beheren van verbindingsreeksen voor de Service Bus en toegangssleutels voor de opslagaccounts die worden gebruikt voor replicatie. U moet de machtigingen instellen die de sleutelkluis nodig heeft om toegang te krijgen tot het opslagaccount wanneer u [Azure voorbereidt](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) voor de evaluatie en migratie van virtuele Hyper-V-machines. 


## <a name="track-and-monitor"></a>Bijhouden en controleren


- Wanneer u op **Repliceren** klikt, wordt een taak voor het starten van de replicatie gestart. 
- Wanneer deze taak is voltooid, beginnen de machines hun initiële replicatie naar Azure.
- Nadat de initiële replicatie is voltooid, begint de deltareplicatie. Incrementele wijzigingen van on-premises schijven worden periodiek gerepliceerd naar Azure.

U kunt de taakstatus volgen in de portalmeldingen.

U kunt de replicatiestatus controleren door te klikken op **Replicerende servers** in **Azure Migrate: Server Migration**.
![Replicatie bewaken](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Een testmigratie uitvoeren


Wanneer de deltareplicatie begint, kunt u een testmigratie voor de virtuele machines uitvoeren voordat u een volledige migratie naar Azure uitvoert. We raden u ten zeerste aan om dit ten minste één keer te doen voor elke machine voordat u deze migreert.

- Bij het uitvoeren van een testmigratie wordt gecontroleerd of de migratie werkt zoals verwacht, zonder dat dit van invloed is op de on-premises machines - die operationeel blijven - en u door kunt gaan met repliceren. 
- Met een testmigratie wordt de migratie gesimuleerd door een Azure-VM te maken met behulp van gerepliceerde gegevens (die meestal worden gemigreerd naar een Azure-VNet dat niet in productie is in uw Azure-abonnement).
- U kunt de gerepliceerde Azure-VM gebruiken om de migratie te valideren, apps te testen en problemen op te lossen voordat u de volledige migratie uitvoert.

Ga als volgt te werk om een testmigratie uit te voeren:


1. In **Migratiedoelen** > **Servers** > **Azure Migrate: Servermigratie** klikt u op **Gemigreerde servers testen**.

     ![Gemigreerde servers testen](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Klik met de rechtermuisknop op de te testen VM en klik vervolgens op **Migratie testen**.

    ![Migratie testen](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. Selecteer in **Migratie testen** het virtuele Azure-netwerk waarin de Azure-VM zich na migratie bevindt. We raden u aan geen virtueel productienetwerk te gebruiken.
4. De taak **Migratie testen** wordt gestart. Houd de taak in portalmeldingen in de gaten.
5. Nadat de migratie is voltooid, bekijkt u de gemigreerde Azure-VM in **Virtuele machines** in de Azure-portal. De machinenaam heeft het achtervoegsel **-Test**.
6. Nadat de test is afgerond, klikt u met de rechtermuisknop op de Azure-VM in **Machines repliceren** en klikt u op **Testmigratie opschonen**.

    ![Migratie opschonen](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Virtuele machines migreren

Nadat u hebt geverifieerd dat de testmigratie naar verwachting werkt, kunt u de on-premises machines migreren.

1. In het Azure Migrate-project > **Servers** > **Azure Migrate: Servermigratie** klikt u op **Servers repliceren**.

    ![Servers repliceren](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. Klik in **Machines repliceren** met de rechtermuisknop op de VM > **Migreren**.
3. In **Migreren** > **Virtuele machines afsluiten en geplande migratie uitvoeren zonder gegevensverlies** selecteert u **Ja** > **OK**.
    - Azure Migrate sluit standaard de on-premises VM af en voert de replicatie op aanvraag uit om VM-wijzigingen die sinds de laatste replicatie zijn opgetreden, te synchroniseren. Zo gaan er geen gegevens verloren.
    - Als u de VM niet wilt afsluiten, selecteert u **Nee**
4. Er wordt een migratietaak gestart voor de VM. Volg de taak in Azure-meldingen.
5. Nadat de taak is afgerond, kunt u de VM bekijken en beheren vanaf de pagina **Virtuele machines**.

## <a name="complete-the-migration"></a>Migratie voltooien

1. Nadat de migratie is voltooid, klikt u met de rechtermuisknop op de VM > **Migratie stoppen**. Er gebeurt nu het volgende:
    - De replicatie van de on-premises machine wordt gestopt.
    - De machine wordt verwijderd uit het aantal **Replicerende servers** in Azure Migrate: Server Migration.
    - De informatie over de replicatiestatus voor de virtuele machine wordt opgeschoond.
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