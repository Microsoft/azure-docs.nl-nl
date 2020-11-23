---
title: Back-upfouten van SAP HANA databases oplossen
description: Hierin wordt beschreven hoe u veelvoorkomende fouten oplost die zich kunnen voordoen wanneer u Azure Backup gebruikt om back-ups te maken van SAP HANA-data bases.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: b9fa73ee38e337a547816432212bc68d419f40bb
ms.sourcegitcommit: 1d366d72357db47feaea20c54004dc4467391364
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95411322"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Problemen met back-ups van SAP HANA-data bases in azure oplossen

Dit artikel bevat informatie over het oplossen van problemen met het maken van back-ups van SAP HANA-data bases op Azure virtual machines. Voor meer informatie over de SAP HANA back-upscenario's die momenteel worden ondersteund, raadpleegt u [scenario ondersteuning](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Vereisten en machtigingen

Raadpleeg de [vereisten](tutorial-backup-sap-hana-db.md#prerequisites) en [wat het pre-registratie script doet](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) voordat u back-ups gaat configureren.

## <a name="common-user-errors"></a>Veelvoorkomende gebruikers fouten

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Fout bericht**      | <span style="font-weight:normal">Azure Backup heeft niet de vereiste rolmachtigingen voor het uitvoeren van een back-up</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | De rol is mogelijk overschreven.                          |
| **Aanbevolen actie** | U kunt het probleem oplossen door het script uit te voeren in het deel venster **Discover DB** of [hier](https://aka.ms/scriptforpermsonhana)te downloaden. U kunt ook de rol ' SAP_INTERNAL_HANA_SUPPORT ' toevoegen aan de back-upgebruiker van de werk belasting (AZUREWLBACKUPHANAUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Foutbericht      | <span style="font-weight:normal">Kan geen verbinding maken met het HANA-systeem</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | Het SAP HANA-exemplaar is mogelijk niet actief.<br/>De vereiste machtigingen voor de Azure Backup om te communiceren met de HANA-Data Base zijn niet ingesteld. |
| **Aanbevolen actie** | Controleer of de SAP HANA-data base actief is. Als de data base actief is, controleert u of alle vereiste machtigingen zijn ingesteld. Als een van de machtigingen ontbreekt, voert u het script voor de [voorafgaande registratie](https://aka.ms/scriptforpermsonhana) uit om de ontbrekende machtigingen toe te voegen. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Foutbericht      | <span style="font-weight:normal">Het opgegeven SAP HANA exemplaar is ongeldig of kan niet worden gevonden</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | Er kan geen back-up worden gemaakt van meerdere SAP HANA exemplaren op één virtuele Azure-machine. |
| **Aanbevolen actie** | Voer het [script voor de voorafgaande registratie](https://aka.ms/scriptforpermsonhana) uit op het SAP Hana-exemplaar waarvan u een back-up wilt maken. Neem contact op met micro soft ondersteuning als het probleem zich blijft voordoen. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Foutbericht      | <span style="font-weight:normal">De opgegeven SAP HANA bewerking wordt niet ondersteund</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | Azure Backup voor SAP HANA biedt geen ondersteuning voor incrementele back-up en acties die worden uitgevoerd op SAP HANA systeem eigen clients (Studio/cockpit/DBA-cockpit) |
| **Aanbevolen actie** | Zie [hier](./sap-hana-backup-support-matrix.md#scenario-support)voor meer informatie. |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| Foutbericht      | <span style="font-weight:normal">Deze SAP HANA-Data Base biedt geen ondersteuning voor het aangevraagde back-uptype</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | Azure Backup biedt geen ondersteuning voor incrementele back-ups en back-ups met behulp van moment opnamen |
| **Aanbevolen actie** | Zie [hier](./sap-hana-backup-support-matrix.md#scenario-support)voor meer informatie. |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Foutbericht      | <span style="font-weight:normal">De keten van het back-uplogboek is verbroken</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | Het doel van de logboek back-up is mogelijk bijgewerkt van backint naar File System of het uitvoer bare bestand backint is gewijzigd |
| **Aanbevolen actie** | Een volledige back-up activeren om het probleem op te lossen                   |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Foutbericht      | <span style="font-weight:normal">Upgrade van dit SDC naar MDC is gedetecteerd</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | Het SAP HANA-exemplaar is bijgewerkt van dit SDC naar MDC. Back-ups mislukken na de update. |
| **Aanbevolen actie** | Volg de stappen in de [Dit SDC to MDC upgrade](#sdc-to-mdc-upgrade-with-a-change-in-sid) om het probleem op te lossen |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Foutbericht      | <span style="font-weight:normal">Ongeldige backint-configuratie gedetecteerd</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | De para meters voor de back-up zijn onjuist opgegeven voor Azure Backup |
| **Aanbevolen actie** | Controleer of de volgende (backint)-para meters zijn ingesteld:<br/>\* [catalog_backup_using_backint: True]<br/>\* [enable_accumulated_catalog_backup: False]<br/>\* [parallel_data_backup_backint_channels: 1]<br/>\* [log_backup_timeout_s: 900)]<br/>\* [backint_response_timeout: 7200]<br/>Als de backint-para meters aanwezig zijn op de HOST, verwijdert u deze. Als de para meters niet aanwezig zijn op het niveau van de HOST, maar die hand matig zijn gewijzigd op database niveau, moet u deze herstellen naar de juiste waarden zoals eerder beschreven. Of voer de [beveiliging stoppen uit en behoud back-upgegevens](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) van de Azure Portal en selecteer vervolgens **back-up hervatten**. |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|Foutbericht  |De bron-en doel systemen voor herstel zijn niet compatibel  |
|---------|---------|
|Mogelijke oorzaken   | De bron-en doel systemen die zijn geselecteerd voor herstel zijn niet compatibel        |
|Aanbevolen actie   |   Zorg ervoor dat het herstel scenario zich niet in de volgende lijst met mogelijk incompatibele herstelingen bevindt: <br><br>   Voor **Beeld 1:** De naam van de SYSTEMDB kan niet worden gewijzigd tijdens het herstellen.  <br><br> Voor **Beeld 2:** Bron-dit SDC en doel-MDC: de bron database kan niet worden teruggezet als SYSTEMDB of als Tenant database op het doel. <br><br> Voor **Beeld 3:** Bron-MDC en doel-dit SDC: de bron database (SYSTEMDB of Tenant-data base) kan niet worden hersteld naar het doel. <br><br>  Zie voor meer informatie opmerking **1642148** in de [SAP support launchpad](https://launchpad.support.sap.com). |

## <a name="restore-checks"></a>Controles herstellen

### <a name="single-container-database-sdc-restore"></a>Dit SDC-herstel (single container data base)

Voer de invoer uit tijdens het herstellen van één container database (dit SDC) voor HANA naar een andere dit SDC-computer. De naam van de data base moet worden opgegeven met kleine letters en ' dit SDC ' toegevoegd tussen haakjes. De HANA-instantie wordt weer gegeven in hoofd letters.

Stel dat er een back-up wordt gemaakt van een dit SDC HANA-exemplaar ' h21 '. Op de pagina Back-upitems wordt de naam van het back-upartikel weer gegeven als **' h21 (dit SDC) '**. Als u probeert om deze data base te herstellen naar een andere doel-dit SDC, zegt u H11. vervolgens moet u de volgende invoer invoeren.

![Naam van herstelde dit SDC-data base](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Houd rekening met de volgende punten:

- De naam van de herstelde data base wordt standaard gevuld met de naam van het back-upitem. In dit geval h21 (dit SDC).
- Als u het doel als H11 selecteert, wordt de herstelde database naam niet automatisch gewijzigd. **Deze moet worden bewerkt in H11 (dit SDC)**. Met betrekking tot dit SDC wordt de herstelde database naam de doel exemplaar-ID met kleine letters en ' dit SDC ' toegevoegd tussen haakjes.
- Aangezien dit SDC slechts één data base kan hebben, moet u ook het selectie vakje inschakelen zodat de bestaande database gegevens kunnen worden overschreven door de gegevens van het herstel punt.
- Linux is hoofdletter gevoelig. Zorg er daarom voor dat u de zaak behoudt.

### <a name="multiple-container-database-mdc-restore"></a>Multiple container data base (MDC) Restore

In meerdere container databases voor HANA is de standaard configuratie SYSTEMDB + 1 of meer Tenant Db's. Als u een volledig SAP HANA exemplaar herstelt, moet u zowel de SYSTEMDB als de Tenant Db's herstellen. Eén herstelt SYSTEMDB eerst en gaat vervolgens verder met de Tenant-data base. Systeem-DB betekent in wezen dat de systeem gegevens op het geselecteerde doel worden overschreven. Deze herstel bewerking overschrijft ook de BackInt-gerelateerde informatie in het doel exemplaar. Nadat de systeem database is hersteld naar een doel exemplaar, voert u het pre-registratie script opnieuw uit. Alleen dan zullen de volgende Tenant-DB-herstel bewerkingen slagen.

## <a name="back-up-a-replicated-vm"></a>Back-up maken van een gerepliceerde VM

### <a name="scenario-1"></a>Scenario 1

De oorspronkelijke VM is gerepliceerd met behulp van Azure Site Recovery of een back-up van Azure VM. De nieuwe virtuele machine is gebouwd om de oude virtuele machine te simuleren. Dat wil zeggen dat de instellingen precies hetzelfde zijn. (Dit komt doordat de oorspronkelijke virtuele machine is verwijderd en de herstel bewerking is uitgevoerd vanuit een back-up van de VM of Azure Site Recovery).

Dit scenario kan twee mogelijke gevallen omvatten. Meer informatie over het maken van een back-up van de gerepliceerde VM in beide gevallen:

1. De nieuwe VM die is gemaakt, heeft dezelfde naam en bevindt zich in dezelfde resource groep en hetzelfde abonnement als de verwijderde VM.

    - De uitbrei ding is al aanwezig op de VM, maar is niet zichtbaar voor een van de services
    - Het pre-registratie script uitvoeren
    - De extensie voor dezelfde computer opnieuw registreren in de Azure Portal (Details van de **back-**  ->  **upweergave** -> de relevante Azure-VM selecteren-> opnieuw registreren)
    - De al bestaande back-updatabasen (van de verwijderde virtuele machine) moeten vervolgens worden gestart met een back-up

2. De nieuwe virtuele machine die is gemaakt, heeft:

    - een andere naam dan de verwijderde virtuele machine
    - dezelfde naam als de verwijderde virtuele machine, maar bevindt zich in een andere resource groep of een ander abonnement (ten opzichte van de verwijderde VM)

    Als dit het geval is, voert u de volgende stappen uit:

    - De uitbrei ding is al aanwezig op de VM, maar is niet zichtbaar voor een van de services
    - Het pre-registratie script uitvoeren
    - Als u de nieuwe data bases detecteert en beveiligt, ziet u dat er dubbele actieve data bases in de portal worden weer gegeven. U kunt dit voor komen door de [beveiliging te stoppen met het bewaren van gegevens](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) voor de oude data bases. Ga vervolgens verder met de resterende stappen.
    - De data bases ontdekken voor het inschakelen van back-ups
    - Back-ups inschakelen voor deze data bases
    - De al bestaande back-updatabasen (van de verwijderde virtuele machine) blijven opgeslagen in de kluis (waarbij de back-ups worden bewaard volgens het beleid)

### <a name="scenario-2"></a>Scenario 2

De oorspronkelijke VM is gerepliceerd met behulp van Azure Site Recovery of een back-up van Azure VM. De nieuwe virtuele machine is gebaseerd op de inhoud – om te worden gebruikt als sjabloon. Dit is een nieuwe VM met een nieuwe SID.

Voer de volgende stappen uit om back-ups op de nieuwe virtuele machine in te scha kelen:

- De uitbrei ding is al aanwezig op de VM, maar is niet zichtbaar voor een van de services
- Voer het script voor de voorafgaande registratie uit. Op basis van de SID van de nieuwe VM kunnen er twee scenario's optreden:
  - De oorspronkelijke VM en de nieuwe virtuele machine hebben dezelfde SID. Het script dat voorafgaat aan het registratie programma wordt uitgevoerd.
  - De oorspronkelijke VM en de nieuwe VM hebben verschillende Sid's. Het vooraf registratie script mislukt. Neem contact op met de ondersteuning voor hulp bij dit scenario.
- De data bases detecteren waarvan u een back-up wilt maken
- Back-ups inschakelen voor deze data bases

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>Upgrade van dit SDC-versie of MDC-versie op dezelfde VM

Upgrades naar het besturings systeem, de wijziging van de versie van de dit SDC of de wijziging van de MDC-versie die geen SID-wijziging veroorzaken, kunnen als volgt worden verwerkt:

- Zorg ervoor dat de nieuwe versie van het besturings systeem, de dit SDC of de MDC momenteel wordt [ondersteund door Azure backup](sap-hana-backup-support-matrix.md#scenario-support)
- [Beveiliging stoppen met het bewaren van gegevens](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) voor de data base
- De upgrade of update uitvoeren
- Voer het script voor de voorafgaande registratie opnieuw uit. Het upgrade proces kan vaak [de benodigde rollen](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)verwijderen. Als u het script voor de voorafgaande registratie uitvoert, kunt u alle vereiste rollen controleren.
- De beveiliging voor de data base opnieuw hervatten

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>Dit SDC naar MDC upgrade zonder wijziging in SID

Upgrades van dit SDC naar MDC die geen SID-wijziging veroorzaken, kunnen als volgt worden verwerkt:

- Zorg ervoor dat de nieuwe MDC-versie momenteel wordt [ondersteund door Azure backup](sap-hana-backup-support-matrix.md#scenario-support)
- [Beveiliging stoppen met het bewaren van gegevens](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) voor de oude dit SDC-data base
- Voer de upgrade uit. Na voltooiing is het HANA-systeem nu MDC met een systeem database en Tenant Db's
- Voer het [script voor de voorafgaande registratie](https://aka.ms/scriptforpermsonhana) opnieuw uit
- De extensie voor dezelfde computer opnieuw registreren in de Azure Portal (Details van de **back-**  ->  **upweergave** -> de relevante Azure-VM selecteren-> opnieuw registreren)
- Selecteer **db's opnieuw detecteren** voor dezelfde VM. Deze actie moet de nieuwe Db's weer geven in stap 3 als SYSTEMDB en Tenant database, niet dit SDC
- De oudere dit SDC-data base blijft aanwezig in de kluis en oudere back-upgegevens worden bewaard volgens het beleid
- Back-up configureren voor deze data bases

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>Dit SDC naar MDC upgrade met een wijziging in SID

Upgrades van dit SDC naar MDC die een SID-wijziging veroorzaken, kunnen als volgt worden verwerkt:

- Zorg ervoor dat de nieuwe MDC-versie momenteel wordt [ondersteund door Azure backup](sap-hana-backup-support-matrix.md#scenario-support)
- **Beveiliging stoppen met het bewaren van gegevens** voor de oude dit SDC-data base
- Voer de upgrade uit. Na voltooiing is het HANA-systeem nu MDC met een systeem database en Tenant Db's
- Voer het [script voor de voorafgaande registratie](https://aka.ms/scriptforpermsonhana) opnieuw uit met de juiste Details (nieuwe sid en MDC). Als gevolg van een wijziging in de SID, kunnen er problemen ontstaan met het uitvoeren van het script. Neem contact op met Azure Backup ondersteuning als u problemen ondervindt.
- De extensie voor dezelfde computer opnieuw registreren in de Azure Portal (Details van de **back-**  ->  **upweergave** -> de relevante Azure-VM selecteren-> opnieuw registreren)
- Selecteer **db's opnieuw detecteren** voor dezelfde VM. Deze actie moet de nieuwe Db's weer geven in stap 3 als SYSTEMDB en Tenant database, niet dit SDC
- De oudere dit SDC-data base blijft aanwezig in de kluis en de oude back-upgegevens worden bewaard volgens het beleid
- Back-up configureren voor deze data bases

## <a name="re-registration-failures"></a>Fouten bij opnieuw registreren

Controleer op een of meer van de volgende symptomen voordat u de bewerking opnieuw registreren start:

- Alle bewerkingen (zoals back-up, herstel en configuratie back-up) mislukken op de virtuele machine met een van de volgende fout codes: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Als het gebied voor de **back-upstatus** voor het back-upitem **niet bereikbaar** is, geeft u alle andere oorzaken uit die kunnen resulteren in dezelfde status:

  - Onvoldoende machtigingen voor het uitvoeren van back-upbewerkingen op de VM
  - De virtuele machine wordt afgesloten, waardoor er geen back-ups kunnen worden gemaakt
  - Netwerk problemen

Deze symptomen kunnen om een of meer van de volgende redenen optreden:

- Er is een extensie verwijderd uit de portal.
- De virtuele machine is terug in de tijd teruggezet via een in-place schijf herstel bewerking.
- De virtuele machine is afgesloten gedurende een langere periode, waardoor de configuratie van de uitbrei ding is verlopen.
- De virtuele machine is verwijderd en een andere virtuele machine is gemaakt met dezelfde naam en in dezelfde resource groep als de verwijderde VM.

In de voor gaande scenario's wordt u aangeraden een bewerking voor het opnieuw registreren van de virtuele machine te activeren.

## <a name="next-steps"></a>Volgende stappen

- Lees de [Veelgestelde vragen](./sap-hana-faq-backup-azure-vm.md) over het maken van back-ups van SAP Hana-data bases op Azure-vm's.
