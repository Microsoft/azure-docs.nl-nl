---
title: Back-ups van SAP HANA data bases op virtuele machines van Azure beheren
description: In dit artikel leert u algemene taken voor het beheren en bewaken van SAP HANA-data bases die worden uitgevoerd op virtuele machines van Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 54d3341a83873ad3cc50815f04a0b252bb44438e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101703763"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Back-ups van SAP HANA-databases beheren en bewaken

In dit artikel worden algemene taken beschreven voor het beheren en bewaken van SAP HANA-data bases die worden uitgevoerd op een virtuele Azure-machine (VM) en waarvan een back-up is gemaakt naar een Azure Backup Recovery Services kluis door de [Azure backup](./backup-overview.md) -service. U leert hoe u taken en waarschuwingen bewaken, een back-up op aanvraag kunt activeren, beleids regels kunt bewerken, de database beveiliging wilt stoppen en hervatten en de registratie van een VM kunt verwijderen uit back-ups.

Als u nog geen back-ups hebt geconfigureerd voor uw SAP HANA-data bases, raadpleegt u back-ups [maken SAP Hana data bases op Azure-vm's](./backup-azure-sap-hana-database.md).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Hand matige back-uptaken controleren in de portal

Azure Backup worden alle hand matig geactiveerde taken weer gegeven in de sectie **back-uptaken** op Azure Portal.

![Sectie back-uptaken](./media/sap-hana-db-manage/backup-jobs.png)

De taken die u in deze Portal ziet, omvatten database detectie en-registratie, en back-up-en herstel bewerkingen. Geplande taken, waaronder logboek back-ups, worden niet in deze sectie weer gegeven. Hand matig geactiveerde back-ups van de SAP HANA systeem eigen clients (Studio/cockpit/DBA cockpit) worden hier ook niet weer gegeven.

![Lijst met back-uptaken](./media/sap-hana-db-manage/backup-jobs-list.png)

Ga voor meer informatie over bewaking naar [bewaking in het Azure Portal](./backup-azure-monitoring-built-in-monitor.md) en [bewaking met behulp van Azure monitor](./backup-azure-monitoring-use-azuremonitor.md).

## <a name="view-backup-alerts"></a>Waarschuwingen voor back-ups weergeven

Waarschuwingen zijn een eenvoudige manier om back-ups van SAP HANA-data bases te bewaken. Waarschuwingen zorgen ervoor dat u zich kunt concentreren op de gebeurtenissen die u het meest vindt, zonder dat u in het grootst aan gebeurtenissen gaat die door een back-up worden gegenereerd. Met Azure Backup kunt u waarschuwingen instellen en ze kunnen als volgt worden bewaakt:

* Meld u aan bij [Azure Portal](https://portal.azure.com/).
* Selecteer **back-upwaarschuwingen** op het kluis dashboard.

  ![Back-upwaarschuwingen op het kluis dashboard](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* U kunt de volgende waarschuwingen weer geven:

  ![Lijst met back-upwaarschuwingen](./media/sap-hana-db-manage/backup-alerts-list.png)

* Selecteer de waarschuwingen om meer details te bekijken:

  ![Meldingsdetails](./media/sap-hana-db-manage/alert-details.png)

Vandaag kunt Azure Backup de verzen ding van waarschuwingen via e-mail toestaan. Deze waarschuwingen zijn:

* Geactiveerd voor alle back-upfouten.
* Geconsolideerd op database niveau op fout code.
* Alleen verzonden voor de eerste back-upfout van een Data Base.

Ga voor meer informatie over bewaking naar [bewaking in het Azure Portal](./backup-azure-monitoring-built-in-monitor.md) en [bewaking met behulp van Azure monitor](./backup-azure-monitoring-use-azuremonitor.md).

## <a name="management-operations"></a>Beheerbewerkingen

Azure Backup maakt het beheer van een back-up van SAP HANA data base eenvoudig met een overvloed aan beheer bewerkingen die worden ondersteund. Deze bewerkingen worden uitgebreid beschreven in de volgende secties.

### <a name="run-an-on-demand-backup"></a>Een on-demand back-up uitvoeren

Back-ups worden uitgevoerd volgens het beleids schema. U kunt als volgt een back-up op aanvraag uitvoeren:

1. Selecteer **Back-upitems** in het menu kluis.
2. Selecteer in **Back-upitems** de virtuele machine waarop de SAP Hana-data base wordt uitgevoerd en selecteer **Nu back-up maken**.
3. Kies in **Nu back-up** het type back-up dat u wilt uitvoeren. Selecteer vervolgens **OK**. Deze back-up wordt bewaard op basis van het beleid dat aan dit back-upitem is gekoppeld.
4. De portal meldingen bewaken. U kunt de voortgang van de taak in het kluis dashboard controleren > **back-uptaken** worden  >  **uitgevoerd**. Afhankelijk van de grootte van de data base kan het maken van de eerste back-up enige tijd duren.

Standaard is het bewaren van back-ups op aanvraag 45 dagen.

### <a name="hana-native-client-integration"></a>HANA native client-integratie

#### <a name="backup"></a>Backup

Back-ups op aanvraag die vanuit een van de HANA-systeem eigen clients (naar **Backint**) worden geactiveerd, worden weer gegeven in de lijst back-up op de pagina **Back-upitems** .

![Laatste back-ups worden uitgevoerd](./media/sap-hana-db-manage/last-backups.png)

U kunt [deze back-ups ook bewaken](#monitor-manual-backup-jobs-in-the-portal) op de pagina **back-uptaken** .

Deze back-ups op aanvraag worden ook weer gegeven in de lijst met herstel punten voor herstel.

![Lijst met herstel punten](./media/sap-hana-db-manage/list-restore-points.png)

#### <a name="restore"></a>Herstellen

Herstel bewerkingen die zijn geactiveerd vanuit HANA native clients (met behulp van **Backint**) om op dezelfde computer te herstellen, kunnen worden [gecontroleerd](#monitor-manual-backup-jobs-in-the-portal) vanaf de pagina **back-uptaken** .

### <a name="run-sap-hana-native-client-backup-to-local-disk-on-a-database-with-azure-backup-enabled"></a>SAP HANA systeem eigen client back-up uitvoeren op een lokale schijf in een Data Base waarvoor Azure Backup is ingeschakeld

Ga als volgt te werk als u een lokale back-up wilt maken (met behulp van HANA Studio/cockpit) van een Data Base waarvan een back-up wordt gemaakt met Azure Backup:

1. Wacht totdat alle volledige of logboek back-ups voor de Data Base zijn voltooid. Controleer de status in SAP HANA Studio/cockpit.
2. voor de relevante data base
    1. De backint-para meters uitschakelen. U doet dit door te dubbel klikken op **SystemDB**-  >  **configuratie**  >  **database**  >  **filter (logboek)**.
        * enable_auto_log_backup: Nee
        * log_backup_using_backint: onwaar
        * catalog_backup_using_backint: onwaar
3. Een volledige back-up op aanvraag maken van de data base
4. Keer vervolgens de stappen om. Voor dezelfde relevante, hierboven genoemde data base
    1. de backint-para meters opnieuw inschakelen
        1. catalog_backup_using_backint: True
        1. log_backup_using_backint: True
        1. enable_auto_log_backup: Ja

### <a name="manage-or-clean-up-the-hana-catalog-for-a-database-with-azure-backup-enabled"></a>De HANA-Catalogus beheren of opschonen voor een Data Base waarvoor Azure Backup is ingeschakeld

Als u de back-catalogus wilt bewerken of opschonen, gaat u als volgt te werk:

1. Wacht totdat alle volledige of logboek back-ups voor de Data Base zijn voltooid. Controleer de status in SAP HANA Studio/cockpit.
2. voor de relevante data base
    1. De backint-para meters uitschakelen. U doet dit door te dubbel klikken op **SystemDB**-  >  **configuratie**  >  **database**  >  **filter (logboek)**.
        * enable_auto_log_backup: Nee
        * log_backup_using_backint: onwaar
        * catalog_backup_using_backint: onwaar
3. De catalogus bewerken en de oudere vermeldingen verwijderen
4. Keer vervolgens de stappen om. Voor dezelfde relevante, hierboven genoemde data base
    1. de backint-para meters opnieuw inschakelen
        1. catalog_backup_using_backint: True
        1. log_backup_using_backint: True
        1. enable_auto_log_backup: Ja

### <a name="change-policy"></a>Beleid wijzigen

U kunt het onderliggende beleid voor een SAP HANA back-upitem wijzigen.

* Ga in het kluis dashboard naar **Back-upitems**:

  ![Back-upitems selecteren](./media/sap-hana-db-manage/backup-items.png)

* **SAP Hana kiezen in een Azure-VM**

  ![SAP HANA kiezen in een Azure-VM](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Kies het back-upitem waarvan u het onderliggende beleid wilt wijzigen
* Selecteer het bestaande back-upbeleid.

  ![Bestaand back-upbeleid selecteren](./media/sap-hana-db-manage/existing-backup-policy.png)

* Wijzig het beleid en kies in de lijst. [Maak indien nodig een nieuw back-upbeleid](./backup-azure-sap-hana-database.md#create-a-backup-policy) .

  ![Beleid kiezen uit vervolg keuzelijst](./media/sap-hana-db-manage/choose-backup-policy.png)

* De wijzigingen opslaan

  ![De wijzigingen opslaan](./media/sap-hana-db-manage/save-changes.png)

* Beleids wijzigingen zijn van invloed op alle gekoppelde back-upitems en triggers die overeenkomen met het **configureren van beveiligings** taken.

>[!NOTE]
> Elke wijziging in de retentie periode wordt retro actief toegepast op alle oudere herstel punten, naast de nieuwe.

### <a name="modify-policy"></a>Beleid wijzigen

Wijzig het beleid om de back-uptypen, frequenties en bewaar termijn te wijzigen.

>[!NOTE]
>Elke wijziging in de retentie periode wordt met terugwerkende kracht toegepast op alle oudere herstel punten, naast de nieuwe.

1. Ga in het kluis dashboard naar **beheer > back-upbeleid** en kies het beleid dat u wilt bewerken.

   ![Kies het beleid dat u wilt bewerken](./media/sap-hana-db-manage/manage-backup-policies.png)

1. Selecteer **wijzigen**.

   ![Selecteer Wijzigen](./media/sap-hana-db-manage/modify-policy.png)

1. Kies de frequentie voor de back-uptypen.

   ![Back-upfrequentie kiezen](./media/sap-hana-db-manage/choose-frequency.png)

Beleids wijzigingen zijn van invloed op alle gekoppelde back-upitems en triggers die overeenkomen met het **configureren van beveiligings** taken.

### <a name="inconsistent-policy"></a>Inconsistent beleid

Af en toe kan een bewerking voor het wijzigen van beleid leiden tot een **inconsistente** beleids versie voor sommige back-upitems. Dit gebeurt wanneer de bijbehorende taak **Beveiliging configureren** mislukt voor het back-upitem nadat een bewerking voor het wijzigen van beleid is geactiveerd. Dit ziet er als volgt uit in de weer gave back-upitem:

![Inconsistent beleid](./media/sap-hana-db-manage/inconsistent-policy.png)

U kunt de beleids versie voor alle betrokken items met één klik oplossen:

![Beleids versie herstellen](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>Beveiliging van een SAP HANA-database stoppen

U kunt de beveiliging van een SAP HANA-database op een aantal manieren stoppen:

* Alle toekomstige back-uptaken stoppen en alle herstelpunten verwijderen.
* Alle toekomstige back-uptaken stoppen en de herstelpunten intact laten.

Houd rekening met het volgende als u ervoor kiest de herstelpunten intact te laten:

* Alle herstelpunten blijven voor onbepaalde tijd intact en alle verwijderbewerkingen zullen stoppen bij het stoppen van de beveiliging met behoud van gegevens.
* Er worden kosten in rekening gebracht voor het beveiligde exemplaar en de verbruikte opslag. Zie [Azure backup prijzen](https://azure.microsoft.com/pricing/details/backup/)voor meer informatie.
* Als u een gegevensbron verwijdert zonder back-ups te stoppen, mislukken nieuwe back-ups.

Ga als volgt te werk om de beveiliging van een database te stoppen:

* Selecteer **Back-upitems** op het kluis dashboard.
* Onder **type back-upbeheer** selecteert u **SAP Hana in azure VM**

  ![SAP HANA selecteren in azure VM](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Selecteer de data base waarvoor u de beveiliging wilt stoppen:

  ![Data base selecteren om beveiliging te stoppen](./media/sap-hana-db-manage/select-database.png)

* Selecteer **back-up stoppen** in het menu Data Base.

  ![Back-up stoppen selecteren](./media/sap-hana-db-manage/stop-backup.png)

* Selecteer in het menu **back-up stoppen** of u gegevens wilt behouden of verwijderen. Als u wilt, kunt u een reden en opmerking opgeven.

  ![Selecteer gegevens behouden of verwijderen](./media/sap-hana-db-manage/retain-backup-data.png)

* Selecteer **back-up stoppen**.

### <a name="resume-protection-for-an-sap-hana-database"></a>De beveiliging hervatten voor een SAP HANA data base

Wanneer u de beveiliging voor de SAP HANA-data base stopt en u de optie **back-upgegevens behouden** selecteert, kunt u de beveiliging later hervatten. Als u de gegevens van de back-up niet behoudt, kunt u de beveiliging niet hervatten.

De beveiliging van een SAP HANA-data base hervatten:

* Open het back-upitem en selecteer **back-up hervatten**.

   ![Back-up hervatten selecteren](./media/sap-hana-db-manage/resume-backup.png)

* Selecteer een beleid in het menu **Back-upbeleid** en selecteer vervolgens **Opslaan**.

### <a name="upgrading-from-sdc-to-mdc"></a>Upgraden van dit SDC naar MDC

Meer informatie over het maken van back-ups voor een SAP HANA-Data Base [na een upgrade van dit SDC naar MDC](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid).

### <a name="upgrading-from-sdc-to-mdc-without-a-sid-change"></a>Upgraden van dit SDC naar MDC zonder SID-wijziging

Meer informatie over het maken van een back-up van een SAP HANA-data base waarvan de [sid na een upgrade van dit SDC naar MDC niet is gewijzigd](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-no-change-in-sid).

### <a name="upgrading-to-a-new-version-in-either-sdc-or-mdc"></a>Een upgrade uitvoeren naar een nieuwe versie in dit SDC of MDC

Meer informatie over het maken van een back-up van een SAP HANA-Data Base [waarvan de versie wordt bijgewerkt](backup-azure-sap-hana-database-troubleshoot.md#sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm).

### <a name="unregister-an-sap-hana-instance"></a>Registratie van een SAP HANA-exemplaar opheffen

Hef de registratie van een SAP HANA-exemplaar op nadat u de beveiliging hebt uitgeschakeld, maar voordat u de kluis verwijdert:

* Selecteer op het kluis dashboard onder **beheren** de optie **back-upinfrastructuur**.

   ![Back-upinfrastructuur selecteren](./media/sap-hana-db-manage/backup-infrastructure.png)

* Selecteer het **type back-upbeheer** als **werk belasting in azure VM**

   ![Selecteer het type back-upbeheer als werk belasting in azure VM](./media/sap-hana-db-manage/backup-management-type.png)

* Selecteer in **beveiligde servers** het exemplaar dat u wilt verwijderen. Als u de kluis wilt verwijderen, moet u de registratie van alle servers/exemplaren ongedaan maken.

* Klik met de rechter muisknop op het beveiligde exemplaar en selecteer **verwijderen ongedaan maken**.

   ![Selecteer registratie ongedaan maken](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>De extensie opnieuw registreren op de SAP HANA Server-VM

Soms kan de uitbrei ding van de werk belasting op de VM één reden of een andere worden beïnvloed. In dergelijke gevallen zullen alle bewerkingen die op de virtuele machine worden geactiveerd, mislukken. Mogelijk moet u de extensie opnieuw registreren op de VM. Met de bewerking opnieuw registreren wordt de back-upextensie van de werk belasting op de VM opnieuw geïnstalleerd zodat bewerkingen kunnen worden voortgezet.

Gebruik deze optie met de volgende waarschuwing: wanneer een virtuele machine wordt geactiveerd met een al een goede extensie, wordt de extensie door deze bewerking opnieuw gestart. Dit kan ertoe leiden dat alle taken die in uitvoering zijn, mislukken. Controleer op een of meer van de [symptomen](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures) voordat u de bewerking opnieuw registreren start.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [oplossen van veelvoorkomende problemen bij het maken van back-ups van SAP Hana-data bases.](./backup-azure-sap-hana-database-troubleshoot.md)
