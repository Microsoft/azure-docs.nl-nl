---
title: Azure Managed Disks herstellen
description: Meer informatie over het herstellen van Azure Managed Disks vanuit de Azure Portal.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 94adc8512987b50a8df07d295215ffcff873162f
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108584"
---
# <a name="restore-azure-managed-disks"></a>Azure Managed Disks herstellen

In dit artikel wordt uitgelegd hoe u [Azure Managed disks](../virtual-machines/managed-disks-overview.md) kunt herstellen vanaf een herstel punt dat is gemaakt door Azure backup.

Op dit moment wordt de optie voor het Original-Location herstellen (herstellen) van het herstellen door de bestaande bron schijf te vervangen van waar de back-ups werden gemaakt, niet ondersteund. U kunt herstellen vanaf een herstel punt om een nieuwe schijf te maken in dezelfde resource groep als die van de bron schijf waar de back-ups zijn gemaakt of in een andere resource groep. Dit staat bekend als Alternate-Location herstel (ALR) en dit helpt de bron schijf en de herstelde (nieuwe) schijf te blijven gebruiken.

In dit artikel leert u het volgende:

- Herstellen om een nieuwe schijf te maken

- De status van de herstel bewerking bijhouden

## <a name="restore-to-create-a-new-disk"></a>Herstellen om een nieuwe schijf te maken

Backup-kluis maakt gebruik van beheerde identiteit voor toegang tot andere Azure-resources. Als u wilt herstellen vanuit een back-up, moet de beheerde identiteit van de back-upkluis een set machtigingen hebben voor de resource groep waarin de schijf moet worden hersteld.

Back-upkluis maakt gebruik van een door het systeem toegewezen beheerde identiteit die is beperkt tot één per resource en is verbonden met de levens cyclus van deze resource. U kunt machtigingen verlenen aan de beheerde identiteit door gebruik te maken van Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Beheerde identiteit is een service-principal van een speciaal type dat alleen kan worden gebruikt met Azure-resources. Meer informatie over [beheerde identiteiten](../active-directory/managed-identities-azure-resources/overview.md).

De volgende vereisten zijn vereist voor het uitvoeren van een herstel bewerking:

1. Wijs de rol **schijf herstel operator** toe aan de beheerde identiteit van de back-upkluis in de resource groep waar de schijf wordt hersteld door de Azure backup-service.

    >[!NOTE]
    > U kunt dezelfde resource groep kiezen als die van de bron schijf waarvan back-ups worden gemaakt of voor een andere resource groep binnen hetzelfde of een ander abonnement.

    1. Ga naar de resource groep waar de schijf moet worden hersteld. De resource groep is bijvoorbeeld *TargetRG*.

    1. Ga naar **toegangs beheer (IAM)** en selecteer **roltoewijzingen toevoegen**

    1. Selecteer in het rechterdeel venster met de optie **Disk restore-operator** in de vervolg keuzelijst voor **rollen** . Selecteer de beheerde identiteit van de back-upkluis en **Sla** deze op.

        >[!TIP]
        >Typ de naam van de back-upkluis om de beheerde identiteit van de kluis te selecteren.

        ![De rol voor schijf herstel operator selecteren](./media/restore-managed-disks/disk-restore-operator-role.png)

1. Controleer of de beheerde identiteit van de back-upkluis de juiste set roltoewijzingen heeft voor de resource groep waar de schijf wordt hersteld.

    1. Ga naar **back-upkluis-> identiteit** en selecteer **Azure-roltoewijzingen**

        ![Azure-roltoewijzingen selecteren](./media/restore-managed-disks/azure-role-assignments.png)

    1. Controleer of de rol, de resource naam en het resource type correct worden weer gegeven.

        ![De rol, resource naam en het resource type controleren](./media/restore-managed-disks/verify-role.png)

    >[!NOTE]
    >Terwijl de roltoewijzingen op de juiste manier worden weer gegeven in de portal, kan het ongeveer 15 minuten duren voordat de machtiging wordt toegepast op de beheerde identiteit van de back-upkluis.
    >
    >Tijdens geplande back-ups of een back-upbewerking op aanvraag, Azure Backup slaat de incrementele moment opnamen van de schijf op in de momentopname resource groep die is gegeven tijdens het configureren van de back-up van de schijf. Azure Backup maakt gebruik van deze incrementele moment opnamen tijdens de herstel bewerking. Als de moment opnamen worden verwijderd of verplaatst van de resource groep voor moment opnamen of als de functie toewijzingen van de back-upkluis zijn ingetrokken voor de resource groep voor moment opnamen, mislukt de herstel bewerking.

1. Als de schijf die moet worden hersteld, is versleuteld met door de [klant beheerde sleutels (CMK)](../virtual-machines/disks-enable-customer-managed-keys-portal.md) of met [dubbele versleuteling met door het platform beheerde sleutels en door de klant beheerde sleutels](../virtual-machines/disks-enable-double-encryption-at-rest-portal.md), wijst u de machtiging **Lees** functie toe aan de beheerde identiteit van de back-upkluis op de **schijf Encryption set** resource.

Als aan de vereisten wordt voldaan, voert u de volgende stappen uit om de herstel bewerking uit te voeren.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar **Back-upcentrum**. Selecteer **back-exemplaren** onder de sectie **beheren** . Selecteer in de lijst met back-exemplaren het back-upexemplaar van de schijf waarvoor u de herstel bewerking wilt uitvoeren.

    ![Lijst met back-upinstanties](./media/restore-managed-disks/backup-instances.png)

    U kunt deze bewerking ook uitvoeren vanuit de back-upkluis die u hebt gebruikt voor het configureren van back-ups voor de schijf.

1. Selecteer in het scherm **back-upexemplaar** het herstel punt dat u wilt gebruiken om de herstel bewerking uit te voeren en selecteer **herstellen**.

    ![Herstel punt selecteren](./media/restore-managed-disks/select-restore-point.png)

1. Bekijk in de werk stroom **terugzetten** de **basis beginselen** en selecteer het tabblad **herstel punt** gegevens en selecteer **volgende: herstel parameters**.

    ![Basis beginselen bekijken en herstel punt gegevens selecteren](./media/restore-managed-disks/review-information.png)

1. Selecteer op het tabblad **para meters herstellen** het **doel abonnement** en de **doel resource groep** waarnaar u de back-up wilt terugzetten. Geef de naam op van de schijf die moet worden hersteld. Selecteer **volgende: controleren en herstellen**.

    ![Para meters herstellen](./media/restore-managed-disks/restore-parameters.png)

    >[!TIP]
    >Schijven waarvan een back-up wordt gemaakt, kunnen ook worden gebruikt Azure Backup om een back-up te maken van Azure Backup met behulp van de back-upoplossing van Azure VM met de Recovery Services-kluis. Als u de beveiliging hebt geconfigureerd van de Azure-VM waaraan deze schijf is gekoppeld, kunt u ook de Azure VM-herstel bewerking gebruiken. U kunt ervoor kiezen om de virtuele machine, of schijven en bestanden of mappen, te herstellen vanaf het herstel punt van het bijbehorende back-upexemplaar van Azure VM. Zie [Azure VM backup](./about-azure-vm-restore.md)voor meer informatie.

1. Zodra de validatie is voltooid, selecteert u **herstellen** om de herstel bewerking te starten.

    ![Herstel bewerking initiëren](./media/restore-managed-disks/initiate-restore.png)

    >[!NOTE]
    > Het volt ooien van de validatie kan enkele minuten duren voordat u de herstel bewerking kunt activeren. De validatie kan mislukken als:
    >
    > - Er bestaat al een schijf met dezelfde naam als de naam van de **herstelde schijf** in de **doel resource groep**
    > - de beheerde identiteit van de back-upkluis heeft geen geldige roltoewijzingen voor de **doel resource groep**
    > - de rollen toewijzingen van de beheerde identiteit van de back-upkluis worden ingetrokken voor de **resource groep voor moment opnamen** waar incrementele moment opnamen worden opgeslagen
    > - Als incrementele moment opnamen worden verwijderd of verplaatst uit de resource groep voor moment opnamen

Met herstellen wordt een nieuwe schijf gemaakt op basis van het geselecteerde herstel punt in de doel resource groep die is opgegeven tijdens de herstel bewerking. Als u de herstelde schijf op een bestaande virtuele machine wilt gebruiken, moet u meer stappen uitvoeren:

- Als de herstelde schijf een gegevens schijf is, kunt u een bestaande schijf koppelen aan een virtuele machine. Als de herstelde schijf een besturingssysteem schijf is, kunt u de besturingssysteem schijf van een virtuele machine uit de Azure Portal onder het deel venster **virtuele machine** >- **schijven** in het gedeelte **instellingen** .

    ![BESTURINGSSYSTEEM schijven wisselen](./media/restore-managed-disks/swap-os-disks.png)

- Voor virtuele Windows-machines, als de herstelde schijf een gegevens schijf is, volgt u de instructies voor [het loskoppelen van de oorspronkelijke gegevens schijf](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-the-portal) van de virtuele machine. [Koppel vervolgens de herstelde schijf](../virtual-machines/windows/attach-managed-disk-portal.md) aan de virtuele machine. Volg de instructies voor het [wisselen van de besturingssysteem schijf](../virtual-machines/windows/os-disk-swap.md) van de virtuele machine met de herstelde schijf.

- Voor virtuele Linux-machines, als de herstelde schijf een gegevens schijf is, volgt u de instructies voor [het loskoppelen van de oorspronkelijke gegevens schijf](../virtual-machines/linux/detach-disk.md#detach-a-data-disk-using-the-portal) van de virtuele machine. [Koppel vervolgens de herstelde schijf](../virtual-machines/linux/attach-disk-portal.md#attach-an-existing-disk) aan de virtuele machine. Volg de instructies voor het [wisselen van de besturingssysteem schijf](../virtual-machines/linux/os-disk-swap.md) van de virtuele machine met de herstelde schijf.

Het is raadzaam om de roltoewijzing van de **operator voor schijf herstel** van de back-upkluis in de **doel resource groep** in te trekken nadat de herstel bewerking is voltooid.

## <a name="track-a-restore-operation"></a>Een terugzet bewerking volgen

Nadat u de herstel bewerking hebt geactiveerd, maakt de back-upservice een taak voor tracering. Azure Backup worden meldingen over de taak weer gegeven in de portal. De voortgang van de herstel taak weer geven:

1. Ga naar het scherm voor het maken van een **back-upexemplaar** . Het dash board taken wordt weer gegeven met de werking en status van de afgelopen zeven dagen.

    ![Taken dashboard](./media/restore-managed-disks/jobs-dashboard.png)

1. Als u de status van de herstel bewerking wilt weer geven, selecteert u **alles weer** geven om de lopende en eerdere taken van deze back-upinstantie weer te geven.

    ![Selecteer alles weer geven](./media/restore-managed-disks/view-all.png)

1. Bekijk de lijst met back-up-en herstel taken en hun status. Selecteer een taak in de lijst met taken om de taak details weer te geven.

    ![Lijst met taken](./media/restore-managed-disks/list-of-jobs.png)

## <a name="next-steps"></a>Volgende stappen

- [Veelgestelde vragen over Azure Disk Backup](disk-backup-faq.md)