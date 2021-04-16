---
title: Back-upfouten SAP HANA databases oplossen
description: Beschrijft hoe u veelvoorkomende fouten kunt oplossen die zich kunnen voordoen wanneer u Azure Backup back-up maakt van SAP HANA databases.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: cdf4c26aa32d65ec63ec84d85e454adaaf2ece8d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517229"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Problemen met back-ups SAP HANA databases in Azure oplossen

Dit artikel bevat informatie over probleemoplossing voor het maken van back-SAP HANA databases op virtuele Azure-machines. Zie Scenario-ondersteuning voor meer SAP HANA back-upscenario's die momenteel [worden ondersteund.](sap-hana-backup-support-matrix.md#scenario-support)

## <a name="prerequisites-and-permissions"></a>Vereisten en machtigingen

Raadpleeg de [secties Vereisten en](tutorial-backup-sap-hana-db.md#prerequisites) Wat doet het script [vóór registratie](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) voordat u back-ups configureert.

## <a name="common-user-errors"></a>Veelvoorkomende gebruikersfouten

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Foutbericht**      | <span style="font-weight:normal">Azure Backup beschikt niet over de vereiste rolbevoegdheden om back-ups uit te voeren</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | De rol is mogelijk overschreven.                          |
| **Aanbevolen actie** | U kunt het probleem oplossen door het script uit te voeren vanuit het **deelvenster Db** ontdekken of het hier te [downloaden.](https://aka.ms/scriptforpermsonhana) U kunt ook de rol 'SAP_INTERNAL_HANA_SUPPORT' toevoegen aan de workloadback-upgebruiker (AZUREWLBACKUPHANAUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Foutbericht      | <span style="font-weight:normal">Kan geen verbinding maken met het HANA-systeem</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | De SAP HANA kan niet zijn.<br/>De vereiste machtigingen voor Azure Backup interactie met de HANA-database zijn niet ingesteld. |
| **Aanbevolen actie** | Controleer of de SAP HANA is. Als de database actief is, controleert u of alle vereiste machtigingen zijn ingesteld. Als een van de machtigingen ontbreekt, moet u het [script voor registratie vooraf uitvoeren om](https://aka.ms/scriptforpermsonhana) de ontbrekende machtigingen toe te voegen. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Foutbericht      | <span style="font-weight:normal">Het opgegeven SAP HANA is ongeldig of kan niet worden gevonden</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | Er kan SAP HANA back-up worden uitgevoerd van meerdere exemplaren op één Azure-VM. |
| **Aanbevolen actie** | Voer het [script voor registratie vooraf uit](https://aka.ms/scriptforpermsonhana) op SAP HANA exemplaar van waar u een back-up van wilt maken. Neem contact op met Microsoft Ondersteuning als het probleem zich blijft voordoen. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Foutbericht      | <span style="font-weight:normal">De opgegeven SAP HANA wordt niet ondersteund</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | Azure Backup voor SAP HANA biedt geen ondersteuning voor incrementele back-ups en acties die worden uitgevoerd op SAP HANA systeemeigen clients (Studio/ Cockpit/ DBA Cockpit) |
| **Aanbevolen actie** | Raadpleeg hier voor [meer informatie.](./sap-hana-backup-support-matrix.md#scenario-support) |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Foutbericht      | <span style="font-weight:normal">Back-uplogboekketen is verbroken</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | De back-upbestemming van het logboek is mogelijk bijgewerkt van backint naar bestandssysteem of het uitvoerbare bestand met backint is mogelijk gewijzigd |
| **Aanbevolen actie** | Een volledige back-up activeren om het probleem op te lossen                   |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Foutbericht      | <span style="font-weight:normal">Upgrade van SDC naar MDC gedetecteerd</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | Het SAP HANA is bijgewerkt van SDC naar MDC. Back-ups mislukken na de update. |
| **Aanbevolen actie** | Volg de stappen in de [upgrade van SDC naar MDC om](#sdc-to-mdc-upgrade-with-a-change-in-sid) het probleem op te lossen |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Foutbericht      | <span style="font-weight:normal">Ongeldige backint-configuratie gedetecteerd</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | De back-parameters zijn onjuist opgegeven voor Azure Backup |
| **Aanbevolen actie** | Controleer of de volgende (backint)-parameters zijn ingesteld:<br/>\* [catalog_backup_using_backint:true]<br/>\* [enable_accumulated_catalog_backup:false]<br/>\* [parallel_data_backup_backint_channels:1]<br/>\* [log_backup_timeout_s:900)]<br/>\* [backint_response_timeout:7200]<br/>Als op backint gebaseerde parameters aanwezig zijn in HOST, verwijdert u deze. Als parameters niet aanwezig zijn op HOST-niveau, maar handmatig zijn gewijzigd op databaseniveau, kunt u deze herstellen naar de juiste waarden zoals eerder beschreven. U kunt ook [stopbeveiliging uitvoeren en back-upgegevens van](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) de Azure Portal en vervolgens **Back-up hervatten selecteren.** |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|Foutbericht  |De bron- en doelsystemen voor herstel zijn incompatibel  |
|---------|---------|
|Mogelijke oorzaken   | De bron- en doelsystemen die zijn geselecteerd voor herstel, zijn incompatibel        |
|Aanbevolen actie   |   Zorg ervoor dat uw herstelscenario zich niet in de volgende lijst met mogelijke incompatibele herstelt: <br><br>   **Case 1:** De naam van SYSTEMDB kan niet worden gewijzigd tijdens het herstellen.  <br><br> **Case 2:** Bron - SDC en doel - MDC: de brondatabase kan niet worden hersteld als SYSTEMDB of tenant DB op het doel. <br><br> **Case 3:** Bron - MDC en doel - SDC: de brondatabase (SYSTEMDB of tenant DB) kan niet worden hersteld naar het doel. <br><br>  Zie opmerking **1642148** in [launchpad voor SAP-ondersteuning voor meer informatie.](https://launchpad.support.sap.com) |

## <a name="restore-checks"></a>Controles herstellen

### <a name="single-container-database-sdc-restore"></a>SDC-herstel (Single Container Database)

Zorg voor invoer tijdens het herstellen van een enkele containerdatabase (SDC) voor HANA naar een andere SDC-computer. De databasenaam moet worden opgegeven met kleine letters en met 'sdc' tussen vierkante haken. Het HANA-exemplaar wordt weergegeven in hoofdletters.

Stel dat er een back-up van een SDC HANA-exemplaar 'H21' is gemaakt. Op de pagina back-upitems wordt de naam van het back-upitem weergegeven als **'h21(sdc)'**. Als u deze database probeert te herstellen naar een andere doel-SDC, bijvoorbeeld H11, moeten de volgende invoergegevens worden opgegeven.

![Naam van herstelde SDC-database](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Houd rekening met de volgende punten:

- Standaard wordt de herstelde db-naam ingevuld met de naam van het back-upitem. In dit geval h21(sdc).
- Als u het doel selecteert als H11, wordt de naam van de herstelde database niet automatisch gewijzigd. **Deze moet worden bewerkt naar h11(sdc)**. Met betrekking tot SDC is de herstelde db-naam de id van het doel-exemplaar met kleine letters en 'sdc' tussen vierkante haken.
- Aangezien SDC slechts één database kan hebben, moet u ook het selectievakje in- om overschrijven van de bestaande databasegegevens met de herstelpuntgegevens toe te staan.
- Linux is case-gevoelig. Zorg er dus voor dat u de case behoudt.

### <a name="multiple-container-database-mdc-restore"></a>Meerdere containerdatabases (MDC) herstellen

In meerdere containerdatabases voor HANA is de standaardconfiguratie SYSTEMDB + 1 of meer tenant-DATABASES. Het herstellen van een SAP HANA-exemplaar betekent dat zowel SYSTEMDB- als tenant-DB's worden hersteld. Eerst wordt SYSTEMDB hersteld en vervolgens voor Tenant DB. System DB betekent in wezen dat de systeemgegevens op het geselecteerde doel worden overschrijven. Deze terugzetten overschrijven ook de aan BackInt gerelateerde informatie in het doelin exemplaar. Dus nadat de systeem-DB is hersteld naar een doelinschrijving, moet u het script vóór registratie opnieuw uitvoeren. Alleen dan slagen de volgende tenant-DB-herstels.

## <a name="back-up-a-replicated-vm"></a>Een back-up maken van een gerepliceerde VM

### <a name="scenario-1"></a>Scenario 1

De oorspronkelijke VM is gerepliceerd met Azure Site Recovery back-up van azure-VM's. De nieuwe VM is gebouwd om de oude VM te simuleren. Dat wil zeggen dat de instellingen precies hetzelfde zijn. (Dit komt doordat de oorspronkelijke VM is verwijderd en de herstel is uitgevoerd vanuit een back-up van de VM of Azure Site Recovery).

Dit scenario kan twee mogelijke gevallen omvatten. In beide gevallen leert u hoe u een back-up van de gerepliceerde VM kunt maken:

1. De nieuwe VM die is gemaakt, heeft dezelfde naam en maakt gebruik van dezelfde resourcegroep en hetzelfde abonnement als de verwijderde VM.

    - De extensie is al aanwezig op de VM, maar is niet zichtbaar voor een van de services
    - Het script vóór registratie uitvoeren
    - Registreer de extensie voor dezelfde machine opnieuw in de Azure Portal **(** Details van  ->   back-upweergave -> Selecteer de relevante Azure-VM -> Opnieuw registreren)
    - De reeds bestaande back-updatabases (van de verwijderde VM) moeten vervolgens worden geback-upt

2. De nieuwe VM die is gemaakt, heeft een van de volgende functies:

    - een andere naam dan de verwijderde VM
    - dezelfde naam als de verwijderde VM, maar zich in een andere resourcegroep of een ander abonnement (in vergelijking met de verwijderde VM)

    Als dit het geval is, moet u de volgende stappen volgen:

    - De extensie is al aanwezig op de VM, maar is niet zichtbaar voor een van de services
    - Het script vóór registratie uitvoeren
    - Als u de nieuwe databases detecteert en bebeveiligen, ziet u dubbele actieve databases in de portal. Om dit te voorkomen, [stopt u de beveiliging met behoud van gegevens](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) voor de oude databases. Ga vervolgens verder met de resterende stappen.
    - De databases ontdekken voor het inschakelen van back-ups
    - Back-ups op deze databases inschakelen
    - De reeds bestaande back-ups van databases (van de verwijderde VM) worden nog steeds opgeslagen in de kluis (met de back-ups die worden bewaard volgens het beleid)

### <a name="scenario-2"></a>Scenario 2

De oorspronkelijke VM is gerepliceerd met behulp Azure Site Recovery back-up van azure-VM's. De nieuwe VM is gebouwd op basis van de inhoud om te worden gebruikt als sjabloon. Dit is een nieuwe VM met een nieuwe SID.

Volg deze stappen om back-ups in teschakelen op de nieuwe VM:

- De extensie is al aanwezig op de VM, maar is niet zichtbaar voor een van de services
- Voer het script vóór registratie uit. Op basis van de SID van de nieuwe VM kunnen er twee scenario's optreden:
  - De oorspronkelijke VM en de nieuwe VM hebben dezelfde SID. Het script vóór registratie wordt uitgevoerd.
  - De oorspronkelijke VM en de nieuwe VM hebben verschillende SID's. Het script vóór registratie mislukt. Neem contact op met de ondersteuning voor hulp in dit scenario.
- De databases ontdekken van wie u een back-up wilt maken
- Back-ups op deze databases inschakelen

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>SDC-versie-upgrade of MDC-versie-upgrade op dezelfde VM

Upgrades naar het besturingssysteem, SDC-versiewijziging of MDC-versiewijziging die geen SID-wijziging veroorzaken, kunnen als volgt worden verwerkt:

- Zorg ervoor dat de nieuwe versie van het besturingssysteem, SDC of MDC momenteel [wordt ondersteund door Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- [Beveiliging stoppen met behoud van gegevens](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) voor de database
- De upgrade of update uitvoeren
- Het script vóór registratie opnieuw uitvoeren. Vaak kunnen tijdens het upgradeproces de [benodigde rollen worden verwijderd.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) Door het script vóór registratie uit te voeren, kunt u alle vereiste rollen controleren.
- De beveiliging voor de database opnieuw hervatten

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>SDC naar MDC upgraden zonder wijziging in SID

Upgrades van SDC naar MDC die geen SID-wijziging veroorzaken, kunnen als volgt worden verwerkt:

- Zorg ervoor dat de nieuwe MDC-versie momenteel [wordt ondersteund door Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- [Beveiliging stoppen met behoud van gegevens](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) voor de oude SDC-database
- Voer de upgrade uit. Na voltooiing is het HANA-systeem nu MDC met een systeem-DB en tenant-DB's
- Het script [vóór registratie opnieuw uitvoeren](https://aka.ms/scriptforpermsonhana)
- Registreer de extensie voor dezelfde machine opnieuw in de Azure Portal **(** Details van  ->   back-upweergave -> Selecteer de relevante Azure-VM -> Opnieuw registreren)
- Selecteer **Herontdekkings-TB's** voor dezelfde VM. Deze actie moet de nieuwe DB's in stap 3 als SYSTEMDB en Tenant DB tonen, niet SDC
- De oudere SDC-database blijft aanwezig in de kluis en er worden oude back-upgegevens bewaard volgens het beleid
- Back-up voor deze databases configureren

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>SDC naar MDC upgraden met een wijziging in SID

Upgrades van SDC naar MDC die een SID-wijziging veroorzaken, kunnen als volgt worden verwerkt:

- Zorg ervoor dat de nieuwe MDC-versie momenteel [wordt ondersteund door Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- **Beveiliging stoppen met behoud van gegevens** voor de oude SDC-database
- Voer de upgrade uit. Na voltooiing is het HANA-systeem nu MDC met een systeem-DB en tenant-DB's
- Het script [vóór registratie opnieuw uitvoeren](https://aka.ms/scriptforpermsonhana) met de juiste gegevens (nieuwe SID en MDC). Als gevolg van een wijziging in de SID, kunt u problemen krijgen met het uitvoeren van het script. Neem contact Azure Backup ondersteuning als u problemen hebt.
- Registreer de extensie voor dezelfde machine opnieuw in de Azure Portal **(** Details van back-upweergave -> Selecteer de relevante  ->   Azure-VM -> Opnieuw registreren)
- Selecteer **DB's opnieuw** ontdekken voor dezelfde VM. Met deze actie worden de nieuwe DB's in stap 3 als SYSTEMDB en Tenant DB, niet als SDC
- De oudere SDC-database blijft bestaan in de kluis en er worden oude back-upgegevens bewaard volgens het beleid
- Back-up configureren voor deze databases

## <a name="re-registration-failures"></a>Fouten bij opnieuw registreren

Controleer op een of meer van de volgende symptomen voordat u de bewerking voor opnieuw registreren activeert:

- Alle bewerkingen (zoals back-up, herstel en back-up configureren) mislukken op de VM met een van de volgende **foutcodes: WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Als in **het gebied Back-upstatus** voor het back-upitem Niet bereikbaar wordt **weergegeven,** sluit u alle andere oorzaken uit die tot dezelfde status kunnen leiden:

  - Geen machtiging om back-upgerelateerde bewerkingen uit te voeren op de VM
  - De VM wordt afgesloten, zodat er geen back-ups kunnen worden gemaakt
  - Netwerkproblemen

Deze symptomen kunnen zich voordoen om een of meer van de volgende redenen:

- Een extensie is verwijderd of verwijderd uit de portal.
- De VM is terug in de tijd hersteld via het terugzetten van de in-place schijf.
- De VM is voor een langere periode afgesloten, dus de configuratie van de extensie is verlopen.
- De VM is verwijderd en er is een andere VM gemaakt met dezelfde naam en in dezelfde resourcegroep als de verwijderde VM.

In de voorgaande scenario's raden we u aan een bewerking voor opnieuw registreren op de VM te activeren.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [veelgestelde vragen over het](./sap-hana-faq-backup-azure-vm.yml) maken van back-SAP HANA databases op virtuele Azure-VM's.
