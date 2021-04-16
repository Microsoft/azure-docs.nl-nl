---
title: Azure-Managed Disks
description: Leer hoe u Azure-Managed Disks vanuit de Azure Portal.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: c57d60047a5bcef58c721ee25bd8a0b3ed523aa4
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517195"
---
# <a name="restore-azure-managed-disks"></a>Azure-Managed Disks

In dit artikel wordt uitgelegd hoe u [Azure-Managed Disks](../virtual-machines/managed-disks-overview.md) herstellen vanaf een herstelpunt dat is gemaakt door Azure Backup.

Momenteel wordt de optie Original-Location Recovery (OLR) voor herstel door de bestaande bronschijf te vervangen van waar de back-ups zijn gemaakt, niet ondersteund. U kunt vanaf een herstelpunt herstellen om een nieuwe schijf te maken in dezelfde resourcegroep als die van de bronschijf van waar de back-ups zijn gemaakt of in een andere resourcegroep. Dit staat bekend als Alternate-Location Recovery (ALR) en hiermee kunt u zowel de bronschijf als de herstelde (nieuwe) schijf behouden.

In dit artikel leert u het volgende:

- Herstellen om een nieuwe schijf te maken

- De status van de herstelbewerking bijhouden

## <a name="restore-to-create-a-new-disk"></a>Herstellen om een nieuwe schijf te maken

Backup Vault maakt gebruik van beheerde identiteit voor toegang tot andere Azure-resources. Als u wilt herstellen vanuit een back-up, vereist de beheerde identiteit van de Backup-kluis een set machtigingen voor de resourcegroep waarin de schijf moet worden hersteld.

Back-upkluis maakt gebruik van een door het systeem toegewezen beheerde identiteit, die beperkt is tot één per resource en is gekoppeld aan de levenscyclus van deze resource. U kunt machtigingen verlenen aan de beheerde identiteit met behulp van op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC). Beheerde identiteit is een service-principal van een speciaal type die alleen kan worden gebruikt met Azure-resources. Meer informatie over [beheerde identiteiten.](../active-directory/managed-identities-azure-resources/overview.md)

De volgende vereisten zijn vereist om een herstelbewerking uit te voeren:

1. Wijs **de rol Operator voor schijfherstel** toe aan de beheerde identiteit van de Backup Vault in de resourcegroep waar de schijf wordt hersteld door de Azure Backup service.

    >[!NOTE]
    > U kunt dezelfde resourcegroep kiezen als die van de bronschijf van waaruit back-ups worden gemaakt of naar een andere resourcegroep binnen hetzelfde of een ander abonnement.

    1. Ga naar de resourcegroep waarin de schijf moet worden hersteld. De resourcegroep is bijvoorbeeld *TargetRG.*

    1. Ga naar **Toegangsbeheer (IAM)** en selecteer **Roltoewijzingen toevoegen**

    1. Selecteer in het rechtercontextdeelvenster **Schijfhersteloperator** in **de vervolgkeuzelijst** Rol. Selecteer de beheerde identiteit van de back-upkluis en klik op **Opslaan.**

        >[!TIP]
        >Typ de naam van de back-upkluis om de beheerde identiteit van de kluis te selecteren.

        ![Operatorrol voor schijfherstel selecteren](./media/restore-managed-disks/disk-restore-operator-role.png)

1. Controleer of de beheerde identiteit van de back-upkluis de juiste set roltoewijzingen heeft in de resourcegroep waarin de schijf wordt hersteld.

    1. Ga naar **Backup Vault - > Identity en** selecteer **Azure-roltoewijzingen**

        ![Azure-roltoewijzingen selecteren](./media/restore-managed-disks/azure-role-assignments.png)

    1. Controleer of de rol, de resourcenaam en het resourcetype correct worden weergegeven.

        ![Rol, resourcenaam en resourcetype controleren](./media/restore-managed-disks/verify-role.png)

    >[!NOTE]
    >Hoewel de roltoewijzingen correct worden weergegeven in de portal, kan het ongeveer 15 minuten duren voordat de machtiging is toegepast op de beheerde identiteit van de back-upkluis.
    >
    >Tijdens geplande back-ups of een back-upbewerking op aanvraag slaat Azure Backup de incrementele momentopnamen van de schijf op in de momentopnameresourcegroep die is opgegeven tijdens het configureren van de back-up van de schijf. Azure Backup maakt gebruik van deze incrementele momentopnamen tijdens de herstelbewerking. Als de momentopnamen worden verwijderd of verplaatst uit de momentopnameresourcegroep of als de roltoewijzingen van de Backup-kluis zijn ingetrokken voor de momentopnameresourcegroep, mislukt de herstelbewerking.

1. Als de schijf die moet worden hersteld, is versleuteld met door de klant beheerde sleutels [(CMK)](../virtual-machines/disks-enable-customer-managed-keys-portal.md) of met behulp van dubbele versleuteling met behulp van door het [platform](../virtual-machines/disks-enable-double-encryption-at-rest-portal.md)beheerde sleutels en door de klant beheerde sleutels, wijst u de rollezermachtiging toe aan de beheerde identiteit van de Backup Vault op de resource  Schijfversleutelingsset. 

Zodra aan de vereisten is voldaan, volgt u deze stappen om de herstelbewerking uit te voeren.

1. Ga in [Azure Portal](https://portal.azure.com/)naar **Back-upcentrum.** Selecteer **Back-up-exemplaren** in **de sectie** Beheren. Selecteer in de lijst met back-up-exemplaren het exemplaar van de schijfback-up waarvoor u de herstelbewerking wilt uitvoeren.

    ![Lijst met back-up-exemplaren](./media/restore-managed-disks/backup-instances.png)

    U kunt deze bewerking ook uitvoeren vanuit de Backup-kluis die u hebt gebruikt om back-ups voor de schijf te configureren.

1. Selecteer in **het scherm Back-up-exemplaar** het herstelpunt dat u wilt gebruiken om de herstelbewerking uit te voeren en selecteer **Herstellen.**

    ![Herstelpunt selecteren](./media/restore-managed-disks/select-restore-point.png)

1. Bekijk in **de werkstroom** Herstellen de tabbladgegevens **Basisbeginselen** **en** Herstelpunt selecteren en selecteer **Volgende: Parameters herstellen.**

    ![Basisbeginselen bekijken en Informatie over herstelpunt selecteren](./media/restore-managed-disks/review-information.png)

1. Selecteer op **het tabblad Parameters** herstellen het **doelabonnement** en de **doelresourcegroep** waarin u de back-up wilt herstellen. Geef de naam op van de schijf die moet worden hersteld. Selecteer **Volgende: Controleren en herstellen.**

    ![Parameters herstellen](./media/restore-managed-disks/restore-parameters.png)

    >[!TIP]
    >Van schijven die met behulp van Azure Backup schijfback-upoplossing worden back-ups gemaakt, kan ook een back-up worden gemaakt door Azure Backup met behulp van de Azure VM-back-upoplossing met de Recovery Services-kluis. Als u de beveiliging hebt geconfigureerd van de Azure-VM waaraan deze schijf is gekoppeld, kunt u ook de herstelbewerking voor azure-VM's gebruiken. U kunt ervoor kiezen om de VM te herstellen, of schijven en bestanden of mappen vanaf het herstelpunt van het bijbehorende azure-VM-back-up-exemplaar. Zie Azure VM Backup [voor meer informatie.](./about-azure-vm-restore.md)

1. Zodra de validatie is geslaagd, selecteert **u Herstellen om** de herstelbewerking te starten.

    ![Herstelbewerking initiëren](./media/restore-managed-disks/initiate-restore.png)

    >[!NOTE]
    > Het kan enkele minuten duren voordat de validatiebewerking is voltooid. Validatie kan mislukken als:
    >
    > - een schijf met dezelfde naam die is opgegeven in **Naam herstelde schijf bestaat** al in de **doelresourcegroep**
    > - De beheerde identiteit van de Back-upkluis heeft geen geldige roltoewijzingen voor de **doelresourcegroep**
    > - De roltoewijzingen van de beheerde identiteit van de Backup-kluis worden ingetrokken voor de **resourcegroep Momentopname** waarin incrementele momentopnamen worden opgeslagen
    > - Als incrementele momentopnamen worden verwijderd of verplaatst uit de momentopnameresourcegroep

Herstellen maakt een nieuwe schijf van het geselecteerde herstelpunt in de doelresourcegroep die is opgegeven tijdens de herstelbewerking. Als u de herstelde schijf op een bestaande virtuele machine wilt gebruiken, moet u meer stappen uitvoeren:

- Als de herstelde schijf een gegevensschijf is, kunt u een bestaande schijf koppelen aan een virtuele machine. Als de herstelde schijf een besturingssysteemschijf is, kunt u de besturingssysteemschijf van een virtuele machine wisselen van de Azure Portal onder het deelvenster Virtuele **machine** - > **Schijven** in de sectie **Instellingen.**

    ![Besturingssysteemschijven wisselen](./media/restore-managed-disks/swap-os-disks.png)

- Als de herstelde schijf voor virtuele Windows-machines een gegevensschijf is, volgt u de instructies om de oorspronkelijke gegevensschijf los [tekoppelen](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-the-portal) van de virtuele machine. Koppel [vervolgens de herstelde schijf](../virtual-machines/windows/attach-managed-disk-portal.md) aan de virtuele machine. Volg de instructies om de [besturingssysteemschijf van](../virtual-machines/windows/os-disk-swap.md) de virtuele machine te vervangen door de herstelde schijf.

- Als de herstelde schijf voor virtuele Linux-machines een gegevensschijf is, volgt u de instructies om de oorspronkelijke gegevensschijf los [tekoppelen](../virtual-machines/linux/detach-disk.md#detach-a-data-disk-using-the-portal) van de virtuele machine. Koppel [vervolgens de herstelde schijf](../virtual-machines/linux/attach-disk-portal.md#attach-an-existing-disk) aan de virtuele machine. Volg de instructies om de [besturingssysteemschijf van](../virtual-machines/linux/os-disk-swap.md) de virtuele machine te vervangen door de herstelde schijf.

Het is raadzaam om  de roltoewijzing Operator voor schijfherstel in  te trekken uit de beheerde identiteit van de Back-upkluis in de doelresourcegroep nadat de herstelbewerking is voltooid.

## <a name="track-a-restore-operation"></a>Een herstelbewerking bijhouden

Nadat u de herstelbewerking hebt activeren, maakt de back-upservice een taak voor tracering. Azure Backup geeft meldingen weer over de taak in de portal. De voortgang van de herstelklus weergeven:

1. Ga naar het scherm **Back-up-exemplaar.** U ziet het takendashboard met de bewerking en de status van de afgelopen zeven dagen.

    ![Takendashboard](./media/restore-managed-disks/jobs-dashboard.png)

1. Als u de status van de herstelbewerking wilt weergeven, selecteert **Alles weergeven** om lopende en eerdere taken van dit back-up-exemplaar weer te geven.

    ![Selecteer Alles weergeven](./media/restore-managed-disks/view-all.png)

1. Bekijk de lijst met back-up- en hersteltaken en hun status. Selecteer een taak in de lijst met taken om taakdetails weer te geven.

    ![Lijst met taken](./media/restore-managed-disks/list-of-jobs.png)

## <a name="next-steps"></a>Volgende stappen

- [Veelgestelde vragen over Azure Disk Backup](disk-backup-faq.yml)