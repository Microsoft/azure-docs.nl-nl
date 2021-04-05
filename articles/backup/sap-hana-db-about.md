---
title: Over SAP HANA back-up van de data base in azure Vm's
description: In dit artikel vindt u informatie over het maken van back-ups van SAP HANA-data bases die worden uitgevoerd op virtuele machines van Azure.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: efb9c3f786e429df404e261f053a9c9a9b032e11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96296451"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Over SAP HANA back-up van de data base in azure Vm's

SAP HANA data bases zijn bedrijfskritische workloads waarvoor een lage Recovery Point Objective (RPO) en een doel stelling voor snel herstel (RTO) zijn vereist. U kunt nu [back-ups maken van SAP Hana-data bases die worden uitgevoerd op virtuele Azure-machines](./tutorial-backup-sap-hana-db.md) met [Azure backup](./backup-overview.md).

Azure Backup is [Backint gecertificeerd](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) door SAP, om systeem eigen back-upondersteuning te bieden door gebruik te maken van de systeem eigen api's van SAP Hana. Deze aanbieding van Azure Backup wordt afgestemd met de mantra van de back-ups van de **infra structuur** van Azure backup, waardoor er geen back-upinfrastructuur hoeft te worden geïmplementeerd en beheerd. U kunt nu zonder problemen back-ups maken en herstellen van SAP HANA-data bases die worden uitgevoerd op virtuele Azure-machines (Vm's uit de[M-serie](../virtual-machines/m-series.md) worden ook nu ondersteund) en gebruikmaken van de mogelijkheden voor bedrijfs beheer die Azure backup bieden.

## <a name="added-value"></a>Toegevoegde waarde

Het gebruik van Azure Backup voor het maken van back-ups en het herstellen van SAP HANA data bases biedt de volgende voor delen:

* **herstel punt doelstelling van 15 minuten (RPO)**: het herstellen van kritieke gegevens van Maxi maal 15 minuten is nu mogelijk.
* Herstel naar **een bepaald tijdstip**: het herstellen van productie gegevens naar alternatieve Hana-servers wordt eenvoudig gemaakt. Het koppelen van back-ups en catalogi voor het uitvoeren van herstel bewerkingen wordt door Azure achter de schermen beheerd.
* **Lange termijn retentie**: voor strenge naleving en controle behoeften. Bewaar uw back-ups voor jaren, op basis van de Bewaar duur, waarna de herstel punten automatisch worden verwijderd door de ingebouwde levenscyclus beheer functie.
* **Back-upbeheer van Azure**: gebruik de beheer-en bewakings mogelijkheden van Azure backup om de beheer ervaring te verbeteren. Azure CLI wordt ook ondersteund.

Raadpleeg de [ondersteunings matrix van SAP Hana scenario](./sap-hana-backup-support-matrix.md#scenario-support)voor een overzicht van de scenario's voor back-up en herstel die nu worden ondersteund.

## <a name="backup-architecture"></a>Back-up maken van architectuur

![Diagram van back-uparchitectuur](./media/sap-hana-db-about/backup-architecture.png)

* Het back-upproces begint met het [maken van een Recovery Services kluis](./tutorial-backup-sap-hana-db.md#create-a-recovery-services-vault) in Azure. Deze kluis wordt gebruikt voor het opslaan van de back-ups en herstel punten die in de loop van de tijd zijn gemaakt.
* De Azure-VM met SAP HANA server is geregistreerd bij de kluis en de data bases waarvan een back-up moet worden gemaakt, worden [gedetecteerd](./tutorial-backup-sap-hana-db.md#discover-the-databases). Om de Azure Backup-service in te scha kelen om data bases te detecteren, moet een [preregistratie-script](https://aka.ms/scriptforpermsonhana) worden uitgevoerd op de Hana-server als hoofd gebruiker.
* Met dit script maakt u een **AZUREWLBACKUPHANAUSER** db-gebruiker en een bijbehorende sleutel met dezelfde naam in **hdbuserstore**. Raadpleeg de sectie  [Wat is het script voor voorafgaande registratie](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) ? voor meer informatie over de werking van het script.
* Azure Backup-service installeert nu de **Azure backup-invoeg toepassing voor Hana** op de geregistreerde SAP Hana-server.
* De **AZUREWLBACKUPHANAUSER** db-gebruiker die door het script voor voor registratie wordt gemaakt, wordt gebruikt door de **Azure backup-INVOEG toepassing voor Hana** om alle back-up-en herstel bewerkingen uit te voeren. Als u probeert om een back-up te configureren voor SAP HANA Db's zonder dit script uit te voeren, wordt mogelijk de volgende fout weer gegeven: **UserErrorHanaScriptNotRun**.
* Kies het vereiste back-upbeleid en schakel back-ups in om de [back-up te configureren](./tutorial-backup-sap-hana-db.md#configure-backup) voor de gedetecteerde data bases.

* Nadat de back-up is geconfigureerd, stelt Azure Backup-service de volgende Backint-para meters in op DATABASE niveau op de beveiligde SAP HANA-server:
  * [catalog_backup_using_backint: True]
  * [enable_accumulated_catalog_backup: False]
  * [parallel_data_backup_backint_channels: 1]
  * [log_backup_timeout_s: 900)]
  * [backint_response_timeout: 7200]

>[!NOTE]
>Zorg ervoor dat deze para meters *niet* aanwezig zijn op het niveau van de host. Met para meters op hostniveau worden deze para meters overschreven en kan dit leiden tot onverwacht gedrag.
>

* De **Azure backup-invoeg toepassing voor Hana** houdt alle back-upscheman en de details van het beleid bij. Het activeert de geplande back-ups en communiceert met de **Hana-back-upengine** via de Backint-api's.
* De **Hana-back-upengine** retourneert een Backint-stroom met de gegevens waarvan een back-up moet worden gemaakt.
* Alle geplande back-ups en back-ups op aanvraag (geactiveerd van de Azure Portal) die volledig of differentieel zijn, worden geïnitieerd door de **Azure backup-invoeg toepassing voor Hana**. Logboek back-ups worden echter beheerd en geactiveerd door de **Hana-back-upengine** zelf.
* Azure Backup voor SAP HANA is een gecertificeerde BackInt-oplossing, is niet afhankelijk van de onderliggende schijf-of VM-typen. De back-up wordt uitgevoerd door stromen die zijn gegenereerd door HANA.

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>Azure VM Backup gebruiken met Azure SAP HANA backup

Naast het gebruik van de SAP HANA back-up in azure die back-up en herstel op database niveau biedt, kunt u de back-upoplossing van Azure VM gebruiken om een back-up te maken van het besturings systeem en niet-database schijven.

De [Backint Certified Azure SAP Hana backup-oplossing](#backup-architecture) kan worden gebruikt voor back-up en herstel van de data base.

[Back-ups van Azure-vm's](backup-azure-vms-introduction.md) kunnen worden gebruikt voor het maken van een back-up van het besturings systeem en andere niet-database schijven. De back-up van de virtuele machine wordt één keer per dag gemaakt en er wordt een back-up gemaakt van alle schijven (met uitzonde ring van **Write Accelerator (WA)** en **Ultra schijven**). Omdat er een back-up van de data base wordt gemaakt met behulp van de back-upoplossing van Azure SAP HANA, kunt u een bestands consistente back-up maken van alleen de besturings systemen en niet-database schijven met behulp van de functie [selectief schijf back-up en herstel voor Azure vm's](selective-disk-backup-restore.md) .

Voer de volgende stappen uit om een VM met SAP HANA te herstellen:

* [Een nieuwe VM herstellen vanaf het laatste herstel punt van een Azure VM-back-up](backup-azure-arm-restore-vms.md) . Of maak een nieuwe lege VM en koppel de schijven van het meest recente herstel punt.
* Als WA-schijven worden uitgesloten, worden ze niet hersteld. In dit geval maakt u lege WA-schijven en logboek gebieden.
* Nadat alle andere configuraties (zoals IP, systeem naam, enzovoort) zijn ingesteld, wordt de VM ingesteld om database gegevens van Azure Backup te ontvangen.
* Zet de data base nu terug naar de virtuele machine van de [Azure SAP Hana DB-back-up](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point) naar het gewenste tijdstip.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [herstellen van een SAP Hana-data base die wordt uitgevoerd op een virtuele Azure-machine](./sap-hana-db-restore.md)
* Meer informatie over [het beheren van SAP HANA-databases waarvan een back-up wordt gemaakt met behulp van Azure Backup](./sap-hana-db-manage.md)
