---
title: VM's herstellen met behulp van de Azure Portal
description: Herstel een virtuele Azure-machine vanaf een herstelpunt met behulp van de Azure Portal, inclusief de functie Herstellen tussen regio's.
ms.reviewer: geg
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: 0f3a715f4fef85b90fd8f06558a8cfdab1ca8900
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739040"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Azure VM-gegevens herstellen in Azure Portal

In dit artikel wordt beschreven hoe u Azure VM-gegevens herstelt vanaf de herstelpunten die zijn opgeslagen in [Azure Backup](backup-overview.md) Recovery Services-kluizen.

## <a name="restore-options"></a>Herstelopties

Azure Backup biedt verschillende manieren om een VM te herstellen.

**Optie voor herstellen** | **Details**
--- | ---
**Een nieuwe virtuele machine maken** | Hiermee maakt en krijgt u snel een standaard-VM die vanaf een herstelpunt actief is.<br/><br/> U kunt een naam voor de virtuele machine opgeven, de resourcegroep en het virtuele netwerk (VNet) selecteren waarin deze wordt geplaatst en een opslagaccount opgeven voor de herstelde VM. De nieuwe VM moet in dezelfde regio worden gemaakt als de bron-VM.<br><br>Als het herstellen van een VM mislukt omdat er geen Azure VM-SKU beschikbaar was in de opgegeven regio van Azure of vanwege andere problemen, herstelt Azure Backup nog steeds de schijven in de opgegeven resourcegroep.
**Schijf herstellen** | Hiermee wordt een VM-schijf hersteld, die vervolgens kan worden gebruikt om een nieuwe virtuele machine te maken.<br/><br/> Azure Backup biedt u een sjabloon waarmee u een nieuwe virtuele machine kunt aanpassen en maken. <br/><br> Met de herstel taak wordt een sjabloon gegenereerd die u kunt downloaden en gebruiken om aangepaste VM-instellingen op te geven en een VM te maken.<br/><br/> De schijven worden gekopieerd naar de resourcegroep die u opgeeft.<br/><br/> U kunt de schijf ook koppelen aan een bestaande VM of een nieuwe VM maken met behulp van PowerShell.<br/><br/> Deze optie is handig als u de virtuele machine wilt aanpassen, configuratie-instellingen wilt toevoegen die niet waren geconfigureerd op het moment van de back-up of instellingen wilt toevoegen die moeten worden geconfigureerd met de sjabloon of PowerShell.
**Bestaande schijf vervangen** | U kunt een schijf herstellen en deze gebruiken om een schijf op de bestaande VM te vervangen.<br/><br/> De huidige VM moet bestaan. Als deze is verwijderd, kan deze optie niet worden gebruikt.<br/><br/> Azure Backup maakt een momentopname van de bestaande VM voordat u de schijf vervangt en slaat deze op in de faseringslocatie die u opgeeft. Bestaande schijven die zijn verbonden met de virtuele machine worden vervangen door het geselecteerde herstelpunt.<br/><br/> De momentopname wordt gekopieerd naar de kluis en bewaard in overeenstemming met het bewaarbeleid. <br/><br/> Na de schijfbewerking wordt de oorspronkelijke schijf bewaard in de resourcegroep. U kunt ervoor kiezen om de oorspronkelijke schijven handmatig te verwijderen als ze niet nodig zijn. <br/><br/>Bestaande vervangen wordt ondersteund voor niet-versleutelde beheerde VM's, met inbegrip van VM's die [zijn gemaakt met aangepaste afbeeldingen.](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) Het wordt niet ondersteund voor klassieke VM's, niet-mande VM's en [ge generaliseerde VM's.](../virtual-machines/windows/upload-generalized-managed.md)<br/><br/> Als het herstelpunt meer of minder schijven heeft dan de huidige VM, geeft het aantal schijven in het herstelpunt alleen de VM-configuratie weer.<br><br> Bestaande vervangen wordt ook ondersteund voor VM's met gekoppelde resources, zoals door de gebruiker toegewezen [beheerde](../active-directory/managed-identities-azure-resources/overview.md) identiteit [of Key Vault](../key-vault/general/overview.md).
**In meerdere regio's (secundaire regio)** | Herstel tussen regio's kan worden gebruikt om Azure-VM's te herstellen in de secundaire regio. Dit is een [gekoppelde Azure-regio.](../best-practices-availability-paired-regions.md#what-are-paired-regions)<br><br> U kunt alle Azure-VM's voor het geselecteerde herstelpunt herstellen als de back-up wordt uitgevoerd in de secundaire regio.<br><br> Tijdens de back-up worden momentopnamen niet gerepliceerd naar de secundaire regio. Alleen de gegevens die in de kluis zijn opgeslagen, worden gerepliceerd. Herstel naar secundaire regio's zijn dus alleen herstel [naar de kluislaag.](about-azure-vm-restore.md#concepts) De hersteltijd voor de secundaire regio is bijna hetzelfde als de hersteltijd voor de kluislaag voor de primaire regio.  <br><br> Deze functie is beschikbaar voor de volgende opties:<br> <li> [Een VM maken](#create-a-vm): <br> <li> [Schijven herstellen](#restore-disks) <br><br> De optie Bestaande schijven vervangen wordt [momenteel niet](#replace-existing-disks) ondersteund.<br><br> Machtigingen<br> De herstelbewerking op de secundaire regio kan worden uitgevoerd door back-upbeheerders en app-beheerders.

> [!NOTE]
> U kunt ook specifieke bestanden en mappen op een Azure-VM herstellen. [Meer informatie](backup-azure-restore-files-from-vm.md).

## <a name="storage-accounts"></a>Opslagaccounts

Enkele details over opslagaccounts:

- **VM maken:** wanneer u een nieuwe VM maakt, wordt de VM geplaatst in het opslagaccount dat u opgeeft.
- **Schijf herstellen:** wanneer u een schijf herstelt, wordt de schijf gekopieerd naar het opslagaccount dat u opgeeft. De herstel taak genereert een sjabloon die u kunt downloaden en gebruiken om aangepaste VM-instellingen op te geven. Deze sjabloon wordt in het opgegeven opslagaccount geplaatst.
- **Schijf vervangen:** wanneer u een schijf in een bestaande VM vervangt, maakt Azure Backup momentopname van de bestaande VM voordat u de schijf vervangt. De momentopname wordt ook als achtergrondproces naar de Recovery Services-kluis gekopieerd via gegevensoverdracht. Zodra de fase van de momentopname is voltooid, wordt de bewerking Schijven vervangen echter geactiveerd. Nadat de schijfbewerking is vervangen, worden de schijven van de bron-Azure-VM voor uw bewerking in de opgegeven resourcegroep bewaard en worden de VHD's opgeslagen in het opgegeven opslagaccount. U kunt ervoor kiezen om deze VHD's en schijven te verwijderen of te behouden.
- **Opslagaccountlocatie:** het opslagaccount moet zich in dezelfde regio bevinden als de kluis. Alleen deze accounts worden weergegeven. Als de locatie geen opslagaccounts heeft, moet u er een maken.
- **Opslagtype:** Blob Storage wordt niet ondersteund.
- **Opslag redundantie:** Zone-redundante opslag (ZRS) wordt niet ondersteund. De replicatie- en redundantiegegevens voor het account worden tussen haakjes na de accountnaam weergegeven.
- **Premium-opslag:**
  - Bij het herstellen van niet-Premium-VM's worden Premium Storage-accounts niet ondersteund.
  - Bij het herstellen van beheerde VM's worden Premium Storage-accounts die zijn geconfigureerd met netwerkregels, niet ondersteund.

## <a name="before-you-start"></a>Voordat u begint

Als u een VM wilt herstellen (een nieuwe VM maken), moet u ervoor zorgen dat u over de juiste azure RBAC-machtigingen (op rollen gebaseerd [toegangsbeheer)](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) voor de bewerking VM herstellen hebt.

Als u geen machtigingen hebt, [](#restore-disks)kunt u een schijf herstellen. Nadat [](#use-templates-to-customize-a-restored-vm) de schijf is hersteld, kunt u de sjabloon die is gegenereerd als onderdeel van de herstelbewerking, gebruiken om een nieuwe VM te maken.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="select-a-restore-point"></a>Een herstelpunt selecteren

1. Selecteer Back-upitems Azure Virtual Machine in de kluis die is gekoppeld aan de virtuele machine die u  >  **wilt herstellen.**
1. Selecteer een VM. Op het VM-dashboard worden standaard herstelpunten van de afgelopen 30 dagen weergegeven. U kunt herstelpunten weergeven die ouder zijn dan 30 dagen of filteren om herstelpunten te vinden op basis van datums, tijdsbereiken en verschillende typen consistentie van momentopnamen.
1. Als u de VM wilt herstellen, selecteert **u VM herstellen.**

    ![Herstelpunt](./media/backup-azure-arm-restore-vms/restore-point.png)

1. Selecteer een herstelpunt dat u wilt gebruiken voor het herstel.

## <a name="choose-a-vm-restore-configuration"></a>Een VM-herstelconfiguratie kiezen

1. Selecteer **in Virtuele machine herstellen** een hersteloptie:
    - **Nieuwe maken:** gebruik deze optie als u een nieuwe VM wilt maken. U kunt een VM maken met eenvoudige instellingen of een schijf herstellen en een aangepaste VM maken.
    - **Bestaande vervangen:** gebruik deze optie als u schijven op een bestaande VM wilt vervangen.

        ![Wizard Configuratie van virtuele machine herstellen](./media/backup-azure-arm-restore-vms/restore-configuration.png)

1. Geef instellingen op voor de geselecteerde hersteloptie.

## <a name="create-a-vm"></a>Een virtuele machine maken

Als een van de [herstelopties](#restore-options)kunt u snel een VM maken met basisinstellingen van een herstelpunt.

1. Selecteer **in Virtuele machine herstellen** Nieuw  >    >  **hersteltype maken** de **optie Een virtuele machine maken.**
1. Geef **in Naam van virtuele** machine een virtuele machine op die niet in het abonnement bestaat.
1. Selecteer **in Resourcegroep** een bestaande resourcegroep voor de nieuwe VM of maak een nieuwe met een wereldwijd unieke naam. Als u een naam toewijst die al bestaat, wijst Azure de groep dezelfde naam toe als de VM.
1. Selecteer **in Virtueel** netwerk het VNet waarin de virtuele machine wordt geplaatst. Alle VNets die aan het abonnement zijn gekoppeld, worden weergegeven. Selecteer het subnet. Het eerste subnet is standaard geselecteerd.
1. Geef **in Faseringslocatie** het opslagaccount voor de VM op. [Meer informatie](#storage-accounts).

    ![Configuratiewizard herstellen - opties voor herstellen kiezen](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

1. Selecteer **Herstellen om** de herstelbewerking te activeren.

## <a name="restore-disks"></a>Schijven herstellen

Als een van de [herstelopties](#restore-options)kunt u een schijf maken vanaf een herstelpunt. Vervolgens kunt u met de schijf een van de volgende acties uitvoeren:

- Gebruik de sjabloon die tijdens de herstelbewerking wordt gegenereerd om instellingen aan te passen en VM-implementatie te activeren. U bewerkt de standaardsjablooninstellingen en verstuurt de sjabloon voor VM-implementatie.
- [Koppel herstelde schijven](../virtual-machines/windows/attach-managed-disk-portal.md) aan een bestaande VM.
- [Maak een nieuwe VM op](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) basis van de herstelde schijven met behulp van PowerShell.

1. Selecteer **in Configuratie herstellen** Nieuw  >    >  **hersteltype maken** **de optie Schijven herstellen.**
1. Selecteer **in Resourcegroep** een bestaande resourcegroep voor de herstelde schijven of maak een nieuwe met een wereldwijd unieke naam.
1. Geef **in Faseringslocatie** het opslagaccount op waar de VHD's moeten worden kopieert. [Meer informatie](#storage-accounts).

    ![Selecteer Resourcegroep en Faseringslocatie](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

1. Selecteer **Herstellen om** de herstelbewerking te activeren.

Wanneer uw virtuele machine gebruikmaakt van beheerde schijven en u de optie Virtuele **machine** maken selecteert, gebruikt Azure Backup het opgegeven opslagaccount niet. In het geval van **Schijven herstellen en** Direct **terugzetten** wordt het opslagaccount alleen gebruikt voor het opslaan van de sjabloon. Beheerde schijven worden gemaakt in de opgegeven resourcegroep.
Wanneer uw virtuele machine gebruikmaakt van onmanagede schijven, worden deze als blobs hersteld in het opslagaccount.

### <a name="use-templates-to-customize-a-restored-vm"></a>Sjablonen gebruiken om een herstelde VM aan te passen

Nadat de schijf is hersteld, gebruikt u de sjabloon die is gegenereerd als onderdeel van de herstelbewerking om een nieuwe VM aan te passen en te maken:

1. Selecteer **in Back-uptaken** de relevante hersteltaken.

1. In **Herstellen selecteert** u **Sjabloon implementeren om** de sjabloonimplementatie te starten.

    ![Inzoomen op taak herstellen](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

1. Als u de VM-instelling in de sjabloon wilt aanpassen, selecteert **u Sjabloon bewerken.** Als u meer aanpassingen wilt toevoegen, selecteert u **Parameters bewerken.**
    - [Meer informatie over](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) het implementeren van resources vanuit een aangepaste sjabloon.
    - [Meer informatie over](../azure-resource-manager/templates/template-syntax.md) het maken van sjablonen.

   ![Sjabloonimplementatie laden](./media/backup-azure-arm-restore-vms/edit-template1.png)

1. Voer de aangepaste waarden voor de VM in, accepteer **de voorwaarden** en selecteer **Kopen.**

   ![Sjabloonimplementatie verzenden](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Bestaande schijven vervangen

Als een van de [herstelopties kunt](#restore-options)u een bestaande VM-schijf vervangen door het geselecteerde herstelpunt. [Bekijk](#restore-options) alle herstelopties.

1. Selecteer **in Configuratie herstellen** de optie Bestaande **vervangen.**
1. Selecteer **in Type herstellen** de optie **Schijf/s vervangen.** Dit is het herstelpunt dat wordt gebruikt om bestaande VM-schijven te vervangen.
1. Geef **in Faseringslocatie** op waar momentopnamen van de huidige beheerde schijven moeten worden opgeslagen tijdens het herstelproces. [Meer informatie](#storage-accounts).

   ![Configuratiewizard herstellen Bestaande vervangen](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Herstellen tussen regio's

Als een van de [herstelopties](#restore-options)kunt u met CRR (Cross Region Restore) virtuele Azure-VM's herstellen in een secundaire regio, een gekoppelde Azure-regio.

Als u de functie wilt gaan gebruiken, leest u [de sectie Voordat u begint.](./backup-create-rs-vault.md#set-cross-region-restore)

Volg de instructies in Herstellen tussen regio's configureren om te zien of CRR is [ingeschakeld.](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Back-upitems in secundaire regio weergeven

Als CRR is ingeschakeld, kunt u de back-upitems in de secundaire regio weergeven.

1. Ga vanuit de portal naar **Recovery Services-kluis**  >  **Back-upitems**.
1. Selecteer **Secundaire regio om** de items in de secundaire regio te bekijken.

>[!NOTE]
>Alleen back-upbeheertypen die de CRR-functie ondersteunen, worden weergegeven in de lijst. Momenteel is alleen ondersteuning voor het herstellen van secundaire regiogegevens naar een secundaire regio toegestaan.<br></br>CRR voor Azure-VM's wordt ondersteund voor door Azure beheerde VM's (inclusief versleutelde Azure-VM's).

![Virtuele machines in secundaire regio](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Secundaire regio selecteren](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Herstellen in secundaire regio

De gebruikerservaring voor het herstellen van de secundaire regio is vergelijkbaar met de gebruikerservaring voor het herstellen van de primaire regio. Wanneer u details configureert in het deelvenster Configuratie herstellen om uw herstel te configureren, wordt u gevraagd om alleen secundaire regioparameters op te geven.

Op dit moment ligt de [RPO](azure-backup-glossary.md#rpo-recovery-point-objective) van de secundaire regio maximaal 12 uur van de primaire regio, hoewel de replicatie van geografisch redundante opslag met leestoegang [(RA-GRS)](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) 15 minuten is.

![VM kiezen om te herstellen](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Herstelpunt selecteren](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Configuratie herstellen](./media/backup-azure-arm-restore-vms/rest-config.png)

![Melding herstel wordt uitgevoerd activeren](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Raadpleeg Een VM maken om een VM te herstellen [en te maken.](#create-a-vm)
- Als u wilt herstellen als een schijf, raadpleegt [u Schijven herstellen.](#restore-disks)

>[!NOTE]
>
>- Nadat het herstellen is geactiveerd en in de fase voor gegevensoverdracht, kan de herstel job niet worden geannuleerd.
>- De functie Herstellen tussen regio's herstelt door de klant beheerde Azure-VM's (door de klant beheerde sleutels) die geen back-up maken in een Recovery Services-kluis met CMK-functionaliteit, als niet-CMK-VM's in de secundaire regio.
>- De Azure-rollen die nodig zijn om te herstellen in de secundaire regio zijn hetzelfde als die in de primaire regio.
>- Tijdens het herstellen van een azure-VM Azure Backup de instellingen van het virtuele netwerk in de secundaire regio automatisch geconfigureerd. Als u schijven [herstelt tijdens het](#restore-disks) implementeren van de sjabloon, moet u ervoor zorgen dat u de instellingen voor het virtuele netwerk op geeft die overeenkomen met de secundaire regio.

[Aan Azure-zone vastgemaakte VM's](../virtual-machines/windows/create-portal-availability-zone.md) kunnen worden hersteld in [alle beschikbaarheidszones](../availability-zones/az-overview.md) van dezelfde regio.

In het herstelproces ziet u de optie **Beschikbaarheidszone.** U ziet eerst uw standaardzone. Als u een andere zone wilt kiezen, kiest u het nummer van de zone van uw keuze. Als de vastgemaakte zone niet beschikbaar is, kunt u de gegevens niet herstellen naar een andere zone omdat de back-upgegevens niet zonaal worden gerepliceerd. Herstellen in beschikbaarheidszones is alleen mogelijk vanaf herstelpunten in de kluislaag.

![Beschikbaarheidszone kiezen](./media/backup-azure-arm-restore-vms/cross-zonal-restore.png)

### <a name="monitoring-secondary-region-restore-jobs"></a>Hersteltaken voor secundaire regio's bewaken

1. Ga vanuit de portal naar **Recovery Services-kluis**  >  **Back-uptaken**
1. Selecteer **Secundaire regio om** de items in de secundaire regio te bekijken.

    ![Gefilterde back-uptaken](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restoring-unmanaged-vms-and-disks-as-managed"></a>Niet-beheerde VM's en schijven herstellen als beheerd

Er wordt een optie geboden voor het herstellen van [niet-beheerde](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks) schijven als [beheerde](../virtual-machines/managed-disks-overview.md) schijven tijdens het herstellen. Standaard worden de niet-mande VM's/schijven hersteld als niet-mande VM's/schijven. Als u er echter voor kiest om te herstellen als beheerde VM's/schijven, is het nu mogelijk om dit te doen. Deze herstelingen worden niet geactiveerd vanuit de momentopnamefase, maar alleen vanuit de kluisfase. Deze functie is niet beschikbaar voor niet-mande versleutelde VM's.

![Herstellen als beheerde schijven](./media/backup-azure-arm-restore-vms/restore-as-managed-disks.png)

## <a name="restore-vms-with-special-configurations"></a>VM's met speciale configuraties herstellen

Er zijn veel algemene scenario's waarin u VM's mogelijk moet herstellen.

**Scenario** | **Hulp**
--- | ---
**VM's herstellen met Hybrid Use Benefit** | Als een Windows-VM gebruikmaakt van [HUB-licenties (Hybrid Use Benefit),](../virtual-machines/windows/hybrid-use-benefit-licensing.md)herstelt u  de schijven en maakt u een nieuwe VM met behulp van de opgegeven sjabloon (met Licentietype ingesteld op **Windows_Server ) of** PowerShell.  Deze instelling kan ook worden toegepast na het maken van de VM.
**VM's herstellen tijdens een noodherstel in een Azure-datacenter** | Als de kluis gebruikmaakt van GRS en het primaire datacenter voor de VM uit bedrijf gaat, Azure Backup ondersteuning voor het herstellen van back-up-VM's naar het gekoppelde datacenter. U selecteert een opslagaccount in het gekoppelde datacenter en herstelt op de gebruikelijke manier. Azure Backup maakt gebruik van de rekenservice in de gekoppelde regio om de herstelde VM te maken. [Meer informatie over](/azure/architecture/resiliency/recovery-loss-azure-region) tolerantie van datacenters.<br><br> Als de kluis gebruikmaakt van GRS, kunt u de nieuwe functie Herstellen [tussen regio's kiezen.](#cross-region-restore) Hiermee kunt u herstellen naar een tweede regio in scenario's met volledige of gedeeltelijke uitval, of zelfs als er helemaal geen storing is.
**Bare-metalherstel** | Het belangrijkste verschil tussen Azure-VM's en on-premises hypervisors is dat er geen VM-console beschikbaar is in Azure. Een console is vereist voor bepaalde scenario's, zoals herstellen met behulp van een back-up van het type BMR (bare-metal recovery). VM-herstel vanuit de kluis is echter een volledige vervanging voor BMR.
**VM's met speciale netwerkconfiguraties herstellen** | Speciale netwerkconfiguraties zijn VM's die interne of externe taakverdeling gebruiken, meerdere NIC's of meerdere gereserveerde IP-adressen gebruiken. U herstelt deze VM's met behulp van [de optie voor het herstellen van de schijf.](#restore-disks) Met deze optie maakt u een kopie van de VHD's in het [](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) opgegeven opslagaccount en kunt u vervolgens een virtueleM maken met een interne of externe [load balancer,](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) meerdere [NIC's](../virtual-machines/windows/multiple-nics.md)of meerdere gereserveerde [IP-adressen,](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)in overeenstemming met uw configuratie.
**Netwerkbeveiligingsgroep (NSG) op NIC/subnet** | Azure VM Backup biedt ondersteuning voor back-up en herstel van NSG-gegevens op VNet-, subnet- en NIC-niveau.
**Aan zone vastgemaakte VM's** | Als u een back-up maakt van een Azure-VM die is vastgemaakt aan een zone (met Azure Backup), kunt u deze herstellen in dezelfde zone waar deze is vastgemaakt. [Meer informatie](../availability-zones/az-overview.md)
**Een VM herstellen in een beschikbaarheidsset** | Bij het herstellen van een VM vanuit de portal is er geen optie om een beschikbaarheidsset te kiezen. Een herstelde VM heeft geen beschikbaarheidsset. Als u de optie schijf herstellen [](../virtual-machines/windows/tutorial-availability-sets.md) gebruikt, kunt u een beschikbaarheidsset opgeven wanneer u een VM maakt op basis van de schijf met behulp van de opgegeven sjabloon of PowerShell.
**Speciale VM's herstellen, zoals SQL-VM's** | Als u een back-up maakt van een SQL-VM met behulp van azure-VM-back-up en vervolgens de optie VM herstellen gebruikt of een VM maakt na het herstellen van schijven, moet de zojuist gemaakte VM worden geregistreerd bij de SQL-provider, zoals hier wordt [vermeld.](../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%2cbash) Hiermee wordt de herstelde VM ge converteerd naar een SQL-VM.

### <a name="restore-domain-controller-vms"></a>Domeincontroller-VM's herstellen

**Scenario** | **Hulp**
--- | ---
**Eén domeincontroller-VM in één domein herstellen** | Herstel de VM zoals elke andere VM. Opmerking:<br/><br/> Vanuit het perspectief van Active Directory is de Azure-VM net als elke andere VM.<br/><br/> Directory Services Restore Mode (DSRM) is ook beschikbaar, zodat alle Active Directory-herstelscenario's haalbaar zijn. [Meer informatie over](#post-restore-steps) overwegingen voor back-up en herstel voor gevirtualiseerde domeincontrollers.
**Meerdere domeincontroller-VM's in één domein herstellen** | Als andere domeincontrollers in hetzelfde domein bereikbaar zijn via het netwerk, kan de domeincontroller net als elke VM worden hersteld. Als dit de laatste resterende domeincontroller in het domein is, of als er een herstel in een geïsoleerd netwerk wordt uitgevoerd, gebruikt u [een forestherstel](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Een VM met één domeincontroller herstellen in een configuratie met meerdere domeinen** |  De schijven herstellen en een VM maken met [behulp van PowerShell](backup-azure-vms-automation.md#restore-the-disks)  
**Meerdere domeinen in één forest herstellen** | We raden een [forestherstel aan.](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)

Zie Back-up en herstel van Active Directory-domeincontrollers [voor meer informatie.](active-directory-backup-restore.md)

## <a name="track-the-restore-operation"></a>De herstelbewerking bijhouden

Nadat u de herstelbewerking hebt activeert, maakt de back-upservice een taak voor tracering. Azure Backup geeft meldingen weer over de taak in de portal. Als ze niet zichtbaar zijn, selecteert u **het** symbool Meldingen en selecteert u vervolgens Meer gebeurtenissen **in** het activiteitenlogboek om de Status van het herstelproces te bekijken.

![Geactiveerd herstellen](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Herstel als volgt bijhouden:

1. Als u bewerkingen voor de taak wilt weergeven, selecteert u de hyperlink meldingen. U kunt ook in de kluis **Back-uptaken selecteren** en vervolgens de relevante VM selecteren.

    ![Lijst met VM's in een kluis](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

1. Als u de voortgang van het herstellen wilt controleren, selecteert u een herstel taak met de status **Wordt uitgevoerd.** Hiermee wordt de voortgangsbalk weergegeven, waarin informatie over de voortgang van het herstellen wordt weergegeven:

    - **Geschatte hersteltijd:** geeft in eerste instantie de tijd die nodig is om de herstelbewerking te voltooien. Naarmate de bewerking vordert, wordt de tijd die nodig is, beperkt en is deze nul wanneer de herstelbewerking is uitgevoerd.
    - **Percentage herstel.** Toont het percentage herstelbewerkingen dat is uitgevoerd.
    - **Het aantal bytes** dat is overgedragen: als u herstelt door een nieuwe VM te maken, worden de bytes die zijn overgedragen ten opzichte van het totale aantal bytes dat moet worden overgedragen, weer te zien.

## <a name="post-restore-steps"></a>Stappen na herstel

Er zijn enkele dingen die u moet noteren na het herstellen van een VM:

- Extensies die aanwezig zijn tijdens de back-upconfiguratie worden geïnstalleerd, maar niet ingeschakeld. Als er een probleem is, installeert u de extensies opnieuw.
- Als de back-up van de VM een statisch IP-adres heeft, heeft de herstelde VM een dynamisch IP-adres om conflicten te voorkomen. U kunt [een statisch IP-adres toevoegen aan de herstelde VM.](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description)
- Een herstelde VM heeft geen beschikbaarheidsset. Als u de optie schijf herstellen [](../virtual-machines/windows/tutorial-availability-sets.md) gebruikt, kunt u een beschikbaarheidsset opgeven wanneer u een VM van de schijf maakt met behulp van de opgegeven sjabloon of PowerShell.
- Als u een Linux-distributie op basis van cloud-init, zoals Ubuntu, gebruikt, wordt het wachtwoord uit veiligheidsoverwegingen geblokkeerd na het herstellen. Gebruik de extensie VMAccess op de herstelde VM om het [wachtwoord opnieuw in te stellen.](/troubleshoot/azure/virtual-machines/reset-password) Het is raadzaam om SSH-sleutels te gebruiken voor deze distributies, zodat u het wachtwoord niet opnieuw hoeft in te stellen na het herstellen.
- Als u geen toegang hebt tot een VM nadat deze is hersteld, omdat de VM een verbroken relatie heeft met de domeincontroller, volgt u de onderstaande stappen om de VM weer te geven:
  - Koppel de besturingssysteemschijf als een gegevensschijf aan een herstelde VM.
  - Installeer de VM-agent handmatig als blijkt dat de Azure-agent niet reageert door deze koppeling te [volgen.](/troubleshoot/azure/virtual-machines/install-vm-agent-offline)
  - Seriële consoletoegang op VM inschakelen om opdrachtregeltoegang tot de VM toe te staan

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - Wanneer de VM opnieuw is opgebouwd, gebruikt u Azure Portal om het lokale beheerdersaccount en wachtwoord opnieuw in te stellen
  - Gebruik Seriële console en CMD om de VM los te maken van het domein

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- Zodra de VM is ontjoind en opnieuw is opgestart, kunt u met lokale beheerdersreferenties RDP naar de VM maken en de VM weer aan het domein toevoegen.

## <a name="backing-up-restored-vms"></a>Back-up maken van herstelde VM's

- Als u een VM hebt hersteld naar dezelfde resourcegroep met dezelfde naam als de oorspronkelijke back-up van de VM, wordt de back-up na het herstellen voortgezet op de VM.
- Als u de VM hebt hersteld naar een andere resourcegroep of als u een andere naam hebt opgegeven voor de herstelde VM, moet u een back-up instellen voor de herstelde VM.

## <a name="next-steps"></a>Volgende stappen

- Als u problemen ervaart tijdens het herstelproces, [bekijkt u](backup-azure-vms-troubleshoot.md#restore) veelvoorkomende problemen en fouten.
- Nadat de virtuele machine is hersteld, kunt u meer informatie krijgen [over het beheren van virtuele machines](backup-azure-manage-vms.md)