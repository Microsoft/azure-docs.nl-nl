---
title: Back-up en herstel van versleutelde virtuele Azure-machines
description: Hierin wordt beschreven hoe u back-ups van versleutelde virtuele Azure-machines maakt en herstelt met de Azure Backup-service.
ms.topic: conceptual
ms.date: 08/18/2020
ms.openlocfilehash: db06b64fba203fb3d2ed54d34235504ac6aa4e2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99223454"
---
# <a name="back-up-and-restore-encrypted-azure-virtual-machines"></a>Back-up en herstel van versleutelde virtuele Azure-machines

In dit artikel wordt beschreven hoe u back-ups maakt van Windows of Linux Azure virtual machines (Vm's) met versleutelde schijven met behulp van de [Azure backup](backup-overview.md) -service. Zie [versleuteling van back-ups van virtuele Azure-machines](backup-azure-vms-introduction.md#encryption-of-azure-vm-backups)voor meer informatie.

## <a name="encryption-using-platform-managed-keys"></a>Versleuteling met door het platform beheerde sleutels

Standaard worden alle schijven in uw virtuele machines automatisch versleuteld met behulp van door het systeem beheerde sleutels (PMK) die gebruikmaken van [opslag service versleuteling](../storage/common/storage-service-encryption.md). U kunt een back-up maken van deze virtuele machines met behulp van Azure Backup zonder specifieke acties die nodig zijn om versleuteling aan uw end te ondersteunen. [Zie dit artikel](../virtual-machines/disk-encryption.md#platform-managed-keys)voor meer informatie over versleuteling met door het platform beheerde sleutels.

![Versleutelde schijven](./media/backup-encryption/encrypted-disks.png)

## <a name="encryption-using-customer-managed-keys"></a>Versleuteling met door de klant beheerde sleutels

Wanneer u schijven versleutelt met door de klant beheerde sleutels (CMK), wordt de sleutel die wordt gebruikt voor het versleutelen van de schijven opgeslagen in de Azure Key Vault en door u beheerd. Storage Service Encryption (SSE) met CMK wijkt af van de versleuteling van Azure Disk Encryption (ADE). ADE maakt gebruik van de versleutelings hulpprogramma's van het besturings systeem. Met SSE worden gegevens in de opslag service versleuteld, zodat u elk besturings systeem of installatie kopieën voor uw virtuele machines kunt gebruiken.

U hoeft geen expliciete acties uit te voeren voor back-up of herstel van Vm's die door de klant beheerde sleutels gebruiken voor het versleutelen van de schijven. De back-upgegevens voor deze virtuele machines die zijn opgeslagen in de kluis, worden versleuteld met dezelfde methoden als de [versleuteling die wordt gebruikt voor de kluis](encryption-at-rest-with-cmk.md).

Zie [dit artikel](../virtual-machines/disk-encryption.md#customer-managed-keys)voor meer informatie over het versleutelen van Managed disks met door de klant beheerde sleutels.

## <a name="encryption-support-using-ade"></a>Ondersteuning voor versleuteling met behulp van ADE

Azure Backup ondersteunt back-ups van virtuele Azure-machines waarvan het besturings systeem/de gegevens schijven zijn versleuteld met Azure Disk Encryption (ADE). ADE gebruikt BitLocker voor versleuteling van Windows-Vm's en de DM-cryptografie functie voor Linux Vm's. ADE kan worden geïntegreerd met Azure Key Vault voor het beheren van sleutels en geheimen voor het versleutelen van schijven. Key Vault Key Encryption Keys (KEKs) kunnen worden gebruikt om een extra beveiligingslaag toe te voegen, versleuteling van versleutelings geheimen te versleutelen voordat u ze naar Key Vault schrijft.

Azure Backup kunt back-ups maken van virtuele Azure-machines met en zonder de Azure AD-app, zoals beschreven in de volgende tabel.

**VM-schijf type** | **ADE (BEK/DM-cryptografie)** | **ADE en KEK**
--- | --- | ---
**Niet-beheerd** | Ja | Ja
**Beheerd**  | Ja | Ja

- Meer informatie over [ADE](../security/fundamentals/azure-disk-encryption-vms-vmss.md), [Key Vault](../key-vault/general/overview.md)en [KEKs](../virtual-machine-scale-sets/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek).
- Lees de [Veelgestelde vragen](../security/fundamentals/azure-disk-encryption-vms-vmss.md) over de schijf versleuteling van Azure VM.

### <a name="limitations"></a>Beperkingen

- U kunt back-ups maken en herstellen van met ADE versleutelde Vm's binnen hetzelfde abonnement en dezelfde regio.
- Azure Backup ondersteunt Vm's die zijn versleuteld met behulp van zelfstandige sleutels. Een sleutel die deel uitmaakt van een certificaat dat wordt gebruikt om een virtuele machine te versleutelen, wordt momenteel niet ondersteund.
- U kunt back-ups van met ADE versleutelde Vm's in hetzelfde abonnement en dezelfde regio als de Recovery Services back-upkluis herstellen.
- Met ADE versleutelde Vm's kunnen niet worden hersteld op het niveau van de bestands-of map. U moet de volledige VM herstellen om bestanden en mappen te herstellen.
- Bij het herstellen van een virtuele machine kunt u de optie [bestaande VM vervangen](backup-azure-arm-restore-vms.md#restore-options) niet gebruiken voor met ade versleutelde vm's. Deze optie wordt alleen ondersteund voor niet-versleutelde beheerde schijven.

## <a name="before-you-start"></a>Voordat u begint

Doe voordat u begint het volgende:

1. Zorg ervoor dat u een of meer virtuele [Windows](../virtual-machines/linux/disk-encryption-overview.md) -of [Linux](../virtual-machines/linux/disk-encryption-overview.md) -vm's met ade ingeschakeld hebt.
2. [De ondersteunings matrix](backup-support-matrix-iaas.md) voor Azure VM backup bekijken
3. [Maak](backup-create-rs-vault.md) een Recovery Services back-upkluis als u er nog geen hebt.
4. Als u versleuteling inschakelt voor virtuele machines die al zijn ingeschakeld voor back-up, hoeft u alleen maar een back-up te maken met machtigingen voor toegang tot de Key Vault zodat back-ups zonder onderbrekingen kunnen worden voortgezet. Meer [informatie](#provide-permissions) over het toewijzen van deze machtigingen.

Daarnaast zijn er een aantal dingen die u in bepaalde omstandigheden mogelijk moet doen:

- **Installeer de VM-agent op de VM**: Azure backup maakt back-ups van virtuele Azure-machines door een uitbrei ding te installeren in de Azure VM-agent die op de computer wordt uitgevoerd. Als uw virtuele machine is gemaakt op basis van een installatie kopie van Azure Marketplace, wordt de agent geïnstalleerd en uitgevoerd. Als u een aangepaste VM maakt of een on-premises machine migreert, moet u [de agent mogelijk hand matig installeren](backup-azure-arm-vms-prepare.md#install-the-vm-agent).

## <a name="configure-a-backup-policy"></a>Een back-upbeleid configureren

1. Als u nog geen Recovery Services back-upkluis hebt gemaakt, volgt u [deze instructies](backup-create-rs-vault.md).
1. Open de kluis in de portal en selecteer **+ Backup** in de sectie **overzicht** .

    ![Deel venster back-up](./media/backup-azure-vms-encryption/select-backup.png)

1.   >  **Waar wordt uw werk belasting uitgevoerd?** Selecteer **Azure**.
1. In **waarvan wilt u een back-up maken? Selecteer een** **virtuele machine**. Selecteer vervolgens **back-up**.

      ![Scenario venster](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

1. Kies in **back-upbeleid**  >  **back-upbeleid**, selecteer het beleid dat u aan de kluis wilt koppelen. Selecteer vervolgens **OK**.
    - Een back-upbeleid geeft aan wanneer er back-ups worden gemaakt en hoe lang ze worden opgeslagen.
    - De details van het standaardbeleid worden onder de vervolgkeuzelijst weergegeven.

    ![Back-upbeleid kiezen](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

1. Als u het standaard beleid niet wilt gebruiken, selecteert u **nieuwe maken** en [maakt u een aangepast beleid](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

1. Selecteer onder **Virtuele machines** de optie **Toevoegen**.

    ![Virtuele machines toevoegen](./media/backup-azure-vms-encryption/add-virtual-machines.png)

1. Kies de versleutelde Vm's waarvan u een back-up wilt maken met het beleid selecteren en selecteer **OK**.

      ![Versleutelde Vm's selecteren](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

1. Als u Azure Key Vault gebruikt, ziet u op de pagina kluis een bericht dat Azure Backup alleen-lezen toegang nodig heeft tot de sleutels en geheimen in de Key Vault.

    - Als dit bericht wordt weer gegeven, is geen actie vereist.

        ![Toegang OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Als dit bericht wordt weer gegeven, moet u machtigingen instellen, zoals beschreven in de [onderstaande procedure](#provide-permissions).

        ![Toegangs waarschuwing](./media/backup-azure-vms-encryption/access-warning.png)

1. Selecteer **back-up inschakelen** om het back-upbeleid in de kluis te implementeren en back-up voor de geselecteerde vm's in te scha kelen.

## <a name="trigger-a-backup-job"></a>Een back-uptaak activeren

De eerste back-up wordt uitgevoerd volgens de planning, maar u kunt deze als volgt direct uitvoeren:

1. Selecteer **Back-upitems** in het menu kluis.
2. Selecteer in **Back-upitems** de optie **Azure virtual machine**.
3. Selecteer in de lijst **Back-upitems** de weglatings tekens (...).
4. Selecteer **Nu back-up**.
5. In **Nu back-up** kunt u het besturings element kalender gebruiken om de laatste dag te selecteren dat het herstel punt moet worden bewaard. Selecteer vervolgens **OK**.
6. De portal meldingen bewaken. U kunt de voortgang van de taak in het kluis dashboard controleren > **back-uptaken** worden  >  **uitgevoerd**. Afhankelijk van de grootte van de virtuele machine kan het maken van de eerste back-up even duren.

## <a name="provide-permissions"></a>Machtigingen opgeven

Azure Backup heeft alleen-lezen toegang nodig om een back-up te maken van de sleutels en geheimen, samen met de bijbehorende Vm's.

- Uw Key Vault is gekoppeld aan de Azure AD-Tenant van het Azure-abonnement. Als u lid bent van een **gebruiker**, verkrijgt Azure Backup toegang tot de Key Vault zonder verdere actie.
- Als u een **gast gebruiker** bent, moet u machtigingen voor Azure backup geven voor toegang tot de sleutel kluis.

Machtigingen instellen:

1. Selecteer in het Azure Portal **alle services** en zoek naar **sleutel kluizen**.
1. Selecteer de sleutel kluis die is gekoppeld aan de versleutelde virtuele machine waarvan u een back-up maakt.

    >[!TIP]
    >Gebruik de volgende Power shell-opdracht om de bijbehorende sleutel kluis van een virtuele machine te identificeren. Vervang de naam van de resource groep en de VM-naam:
    >
    >`Get-AzVm -ResourceGroupName "MyResourceGroup001" -VMName "VM001" -Status`
    >
    > Zoek naar de naam van de sleutel kluis op deze regel:
    >
    >`SecretUrl            : https://<keyVaultName>.vault.azure.net`
    >

1. Selecteer **toegangs** beleid toegangs  >  **beleid toevoegen**.

    ![Toegangsbeleid toevoegen](./media/backup-azure-vms-encryption/add-access-policy.png)

1. Selecteer in **toegangs beleid**  >  **configureren via sjabloon (optioneel)** **Azure backup**.
    - De vereiste machtigingen zijn vooraf ingevuld voor **sleutel machtigingen** en **geheime machtigingen**.
    - Als uw virtuele machine is versleuteld met **alleen bek**, verwijdert u de selectie voor **sleutel machtigingen** , omdat u alleen machtigingen voor geheimen nodig hebt.

    ![Azure Backup selectie](./media/backup-azure-vms-encryption/select-backup-template.png)

1. Selecteer **Toevoegen**. **Backup Management-service** is toegevoegd aan het **toegangs beleid**.

    ![Toegangsbeleid](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

1. Selecteer **Opslaan** om Azure backup met de machtigingen op te geven.

## <a name="restore-an-encrypted-vm"></a>Een versleutelde VM herstellen

Versleutelde Vm's kunnen alleen worden hersteld door de VM-schijf te herstellen, zoals hieronder wordt uitgelegd. **Vervangen bestaande** en **herstel-VM** worden niet ondersteund.

Herstel de versleutelde Vm's als volgt:

1. [Herstel de VM-schijf](backup-azure-arm-restore-vms.md#restore-disks).
2. Maak het exemplaar van de virtuele machine opnieuw door een van de volgende acties uit te voeren:
    1. Gebruik de sjabloon die tijdens de herstel bewerking wordt gegenereerd om de VM-instellingen aan te passen en VM-implementatie te activeren. [Meer informatie](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    2. Maak een nieuwe virtuele machine op basis van de herstelde schijven met behulp van Power shell. [Meer informatie](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
3. Voor Linux Vm's installeert u de ADE-extensie opnieuw zodat de gegevens schijven zijn geopend en gekoppeld.

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt, raadpleegt u de volgende artikelen:

- [Veelvoorkomende fouten](backup-azure-vms-troubleshoot.md) bij het maken van back-ups en herstellen van versleutelde virtuele Azure-machines.
- Problemen met de [Azure-VM-agent/back-upextensie](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) .
