---
title: Veelgestelde vragen over back-ups maken van SAP HANA-databases in virtuele Azure-machines
description: In dit artikel vindt u antwoorden op veelgestelde vragen over het maken van back-ups van SAP HANA-data bases met behulp van de Azure Backup-service.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 24eb4abaaabe166ceb3e6bdb99f9446d398d03a1
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686103"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Veelgestelde vragen: back-ups maken van SAP HANA-data bases op virtuele Azure-machines

In dit artikel vindt u antwoorden op veelgestelde vragen over het maken van back-ups van SAP HANA-data bases via de Azure Backup-Service

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Hoeveel volledige back-ups worden per dag ondersteund?

Er wordt slechts één volledige back-up per dag ondersteund. U kunt geen differentiële back-up en volledige back-up op dezelfde dag activeren.

### <a name="do-successful-backup-jobs-create-alerts"></a>Maken succesvolle back-uptaken waarschuwingen?

Nee. Succesvolle back-uptaken maken geen waarschuwingen. Er worden alleen waarschuwingen verzonden voor mislukte back-uptaken. Gedetailleerd gedrag voor portal waarschuwingen wordt [hier](./backup-azure-monitoring-built-in-monitor.md)beschreven. Als u echter geïnteresseerd bent over waarschuwingen, zelfs voor geslaagde taken, kunt u [Azure monitor](./backup-azure-monitoring-use-azuremonitor.md)gebruiken.

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Kan ik geplande back-uptaken weer geven in het menu back-uptaken?

In het menu back-uptaak worden alleen back-uptaken op aanvraag weer gegeven. Gebruik [Azure monitor](./backup-azure-monitoring-use-azuremonitor.md)voor geplande taken.

### <a name="are-future-databases-automatically-added-for-backup"></a>Worden toekomstige databases automatisch voor back-ups toegevoegd?

Nee, dit wordt momenteel niet ondersteund.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Wat gebeurt er met de back-ups als ik een Data Base uit een exemplaar Verwijder?

Als een Data Base uit een SAP HANA-exemplaar wordt verwijderd, worden er nog steeds back-ups van de data base geprobeerd. Dit betekent dat de verwijderde data base wordt weer gegeven als beschadigd onder **Back-upitems** en nog steeds is beveiligd.
De juiste manier om de beveiliging van deze data base te stoppen, is het stoppen van de **back-up met het verwijderen van gegevens** op deze data base.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Als ik de naam van de data base Wijzig nadat deze is beveiligd, wat is het gedrag?

Een hernoemde data base wordt behandeld als een nieuwe data base. Daarom zal de service deze situatie behandelen alsof de data base niet is gevonden en de back-ups mislukken. De hernoemde data base wordt weer gegeven als een nieuwe data base en moet worden geconfigureerd voor beveiliging.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Wat zijn de vereisten voor het maken van een back-up van SAP HANA-data bases op een virtuele machine van Azure?

Raadpleeg de [vereisten](tutorial-backup-sap-hana-db.md#prerequisites) en [wat het script dat voorafgaat aan het registratie](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) gedeelte.

### <a name="what-permissions-should-be-set-so-azure-can-back-up-sap-hana-databases"></a>Welke machtigingen moeten worden ingesteld zodat Azure back-ups kan maken van SAP HANA-data bases?

Als u het script voor voorafgaande registratie uitvoert, worden de vereiste machtigingen ingesteld om Azure back-ups te maken van SAP HANA-data bases. U vindt [hier](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)meer informatie over het script dat vooraf wordt geregistreerd.

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>Werkt back-ups nadat SAP HANA van dit SDC naar MDC zijn gemigreerd?

Raadpleeg [deze sectie](./backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid) van de hand leiding voor het oplossen van problemen.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>Kan Azure HANA-back-ups worden ingesteld voor een virtueel IP-adres (load balancer) en niet op een virtuele machine?

Momenteel beschikken we niet over de mogelijkheid om de oplossing alleen voor een virtueel IP-adres in te stellen. Er is een virtuele machine nodig om de oplossing uit te voeren.

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>Hoe kan ik een back-up op aanvraag verplaatsen naar het lokale bestands systeem in plaats van naar de Azure-kluis?

1. Wacht totdat de back-up op dat moment wordt uitgevoerd, om de gewenste data base te volt ooien (Controleer vanaf Studio voor voltooiing).
1. Schakel logboek back-ups uit en stel de catalogus back-up in op het **Bestands systeem** voor de gewenste data base met behulp van de volgende stappen:
1. Dubbel klik op **SYSTEMDB**  ->  -**configuratie**  ->  **database**  ->  **filter selecteren (logboek)**
    1. Stel enable_auto_log_backup in op **Nee**.
    1. Stel catalog_backup_using_backint in op **False**.
1. Maak een back-up op aanvraag (volledig/differentieel/incrementeel) op de gewenste data base en wacht totdat de back-up en catalogus back-up zijn voltooid.
1. Als u de logboek back-ups ook wilt verplaatsen naar het bestands systeem, stelt u enable_auto_log_backup in op **Ja**.
1. Terugkeren naar de vorige instellingen zodat back-ups naar de Azure-kluis kunnen stromen:
    1. Stel enable_auto_log_backup in op **Ja**.
    1. Stel catalog_backup_using_backint in op **waar**.

>[!NOTE]
>Door back-ups naar het lokale bestands systeem te verplaatsen en terug te scha kelen naar de Azure-kluis, kan dit leiden tot een logboek keten van de logboek back-ups in de kluis. Hiermee wordt een volledige back-up geactiveerd, die na voltooiing een back-up van de logboeken start.

### <a name="how-can-i-use-sap-hana-backup-with-my-hana-replication-set-up"></a>Hoe kan ik SAP HANA back-up gebruiken met mijn HANA-replicatie-instellingen?

Momenteel heeft Azure Backup niet de mogelijkheid om een HSR-configuratie te begrijpen. Dit betekent dat de primaire en secundaire knoop punten van de HSR worden behandeld als twee afzonderlijke, niet-gerelateerde Vm's. U moet eerst een back-up configureren op het primaire knoop punt. Wanneer een failover optreedt, moet de back-up worden geconfigureerd op het secundaire knoop punt (dit wordt nu het primaire knoop punt). Er is geen automatische failover van een back-up naar het andere knoop punt.

Als u op een bepaald moment een back-up wilt maken van gegevens van het actieve knoop punt (primair), kunt u de **beveiliging overschakelen** naar het secundaire knoop punt, dat nu wordt ingesteld als primair na een failover.

Voer de volgende stappen uit om deze **Switch beveiliging** uit te voeren:

- [Beveiliging stoppen](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) (met behoud van gegevens) op de primaire
- Het [pre-registratie script](https://aka.ms/scriptforpermsonhana) uitvoeren op het secundaire knoop punt
- [De data bases](tutorial-backup-sap-hana-db.md#discover-the-databases) op het secundaire knoop punt detecteren en er [back-ups op configureren](tutorial-backup-sap-hana-db.md#configure-backup)

Deze stappen moeten hand matig worden uitgevoerd na elke failover. U kunt deze stappen uitvoeren via de opdracht regel/HTTP, naast de Azure Portal. Als u deze stappen wilt automatiseren, kunt u een Azure-runbook gebruiken.

Hier volgt een gedetailleerd voor beeld van hoe de **beveiliging van switches** moet worden uitgevoerd:

In dit voor beeld hebt u twee knoop punten: knoop punt 1 (primair) en knoop punt 2 (secundair) in de HSR-configuratie.  Back-ups worden geconfigureerd op knoop punt 1. Zoals hierboven vermeld, probeert u nog geen back-ups te configureren op knoop punt 2.

Wanneer de eerste failover plaatsvindt, wordt knoop punt 2 de primaire. Kies

1. Stop de beveiliging van knoop punt 1 (vorig primair) met de optie gegevens behouden.
1. Voer het pre-registratie script uit op knoop punt 2 (dit is nu de primaire).
1. Data bases op knoop punt 2 detecteren, back-upbeleid toewijzen en back-ups configureren.

Vervolgens wordt een eerste volledige back-up geactiveerd op knoop punt 2 en daarna worden logboek back-ups gestart.

Wanneer de volgende failover plaatsvindt, wordt knoop punt 1 primair weer en wordt knoop punt 2 secundair. Herhaal het proces nu:

1. Stop de beveiliging van knoop punt 2 met de optie gegevens behouden.
1. Het pre-registratie script uitvoeren op het knoop punt 1 (dit is de primaire opnieuw)
1. Hervat vervolgens de [back-up](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) op het knoop punt 1 met het vereiste beleid (omdat de back-ups eerder zijn gestopt op het knoop punt 1).

Vervolgens wordt de volledige back-up opnieuw geactiveerd op het knoop punt 1 en nadat deze is voltooid, worden logboek back-ups gestart.

## <a name="restore"></a>Herstellen

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Waarom kan ik het HANA-systeem waarin ik mijn data base wil herstellen niet zien?

Controleer of aan alle vereisten voor het terugzetten naar het doel SAP HANA exemplaar wordt voldaan. Zie voor meer informatie [vereisten: Restore SAP Hana data bases in azure VM](./sap-hana-db-restore.md#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Waarom wordt het terugzetten van de overschrijvings database mislukt voor mijn data base?

Zorg ervoor dat de optie voor **overschrijven afdwingen** is geselecteerd tijdens het herstellen.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Waarom zie ik de fout ' bron-en doel systemen voor herstel zijn niet compatibel '?

Raadpleeg de SAP HANA nota [1642148](https://launchpad.support.sap.com/#/notes/1642148) om te zien welke typen herstel bewerkingen momenteel worden ondersteund.

### <a name="can-i-use-a-backup-of-a-database-running-on-sles-to-restore-to-an-rhel-hana-system-or-vice-versa"></a>Kan ik een back-up van een Data Base die wordt uitgevoerd op SLES gebruiken om een RHEL HANA-systeem of andersom te herstellen?

Ja, u kunt streaming-back-ups die zijn geactiveerd in een HANA-data base die wordt uitgevoerd op SLES, gebruiken om deze te herstellen naar een RHEL HANA-systeem en vice versa. Dat wil zeggen dat het terugzetten van meerdere besturings systemen mogelijk is met behulp van streaming-back-ups. U moet er echter voor zorgen dat het HANA-systeem waarnaar u wilt herstellen en het HANA-systeem dat wordt gebruikt voor herstel, beide compatibel zijn voor herstel volgens SAP. Raadpleeg SAP HANA nota [1642148](https://launchpad.support.sap.com/#/notes/1642148) om te zien welke typen herstel compatibel zijn.

## <a name="policy"></a>Beleid

### <a name="different-options-available-during-creation-of-a-new-policy-for-sap-hana-backup"></a>Verschillende opties beschikbaar tijdens het maken van een nieuw beleid voor SAP HANA back-up

Voordat u een beleid maakt, moet u duidelijk nadoen wat de vereisten zijn van RPO en RTO en de bijbehorende kosten gevolgen.

RPO (herstel punt-doel stelling) geeft aan hoeveel gegevens verlies acceptabel is voor de gebruiker/klant. Dit wordt bepaald door de back-upfrequentie van het logboek. Meer frequente logboek back-ups geven een lagere RPO aan en de minimale waarde die wordt ondersteund door de Azure Backup-service is 15 minuten. De frequentie van het logboek back-up kan vijf tien minuten of hoger zijn.

RTO (Recovery-Time-doelstelling) geeft aan hoe snel de gegevens moeten worden hersteld naar het laatst beschik bare tijdstip na een scenario voor gegevens verlies. Dit is afhankelijk van de herstel strategie die wordt gebruikt door HANA, wat doorgaans afhankelijk is van het aantal bestanden dat nodig is voor herstel. Dit heeft ook gevolgen voor de kosten en de volgende tabel moet helpen bij het beter over alle scenario's en hun implicaties.

|Back-upbeleid  |RTO  |Kosten  |
|---------|---------|---------|
|Dagelijkse volledige en Logboeken     |   Het snelst, omdat er slechts één volledige kopie van de logboeken vereist is voor herstel naar een bepaald tijdstip      |    Costliest optie omdat een volledige kopie dagelijks wordt gemaakt en er meer en meer gegevens worden verzameld in de back-end tot de Bewaar tijd   |
|Wekelijks volledig en dagelijks differentieel + logboeken     |   Langzamer dan de bovenstaande optie, maar sneller dan de volgende optie, omdat er één volledige kopie + een differentiële kopie en logboeken voor herstel naar een bepaald tijdstip vereist zijn.      |    Goedkopere optie omdat de dagelijkse differentiële doorgaans kleiner is dan vol en een volledige kopie slechts één keer per week wordt uitgevoerd      |
|Wekelijks volledig en dagelijks incrementeel + logboeken     |  Langzaam omdat er één volledige kopie + ' n ' stappen en logboeken voor herstel naar een bepaald tijdstip zijn vereist       |     Goedkoopste optie omdat de dagelijkse incrementeel kleiner is dan differentieel en een volledige kopie alleen wekelijks wordt gemaakt    |

> [!NOTE]
> De bovenstaande opties zijn het meest voorkomende, maar niet de enige opties. U kunt bijvoorbeeld een wekelijks volledige back-up maken + verschillen tussen twee weken en Logboeken.

Daarom kunt u de beleids variant selecteren op basis van RPO-en RTO-doel stellingen en kosten overwegingen.

### <a name="impact-of-modifying-a-policy"></a>Gevolgen van het wijzigen van een beleid

Er moeten enkele principes in acht worden genomen bij het bepalen van de impact van het overschakelen van het beleid van een back-upitem van beleid 1 (P1) naar Policy 2 (P2) of van het bewerkings beleid 1 (P1).

- Alle wijzigingen worden ook met terugwerkende kracht toegepast. Het meest recente back-upbeleid wordt toegepast op de eerder gemaakte herstel punten. Stel bijvoorbeeld dat de dagelijkse volledige Bewaar periode 30 dagen is en 10 herstel punten zijn genomen op basis van het huidige actieve beleid. Als de duur van de dagelijkse volledige Bewaar periode wordt gewijzigd in 10 dagen, wordt de verloop tijd van het vorige punt ook opnieuw berekend als begin tijd + 10 dagen en verwijderd als deze zijn verlopen.
- Het bereik van de wijziging omvat ook de dag van de back-up, het type back-up en de retentie. Bijvoorbeeld: als een beleid wordt gewijzigd van dagelijks volledig naar wekelijks, worden alle eerdere volledige versies die niet op zondagen zijn, gemarkeerd voor verwijdering.
- Een bovenliggend item wordt niet verwijderd totdat het onderliggende element actief/niet is verlopen. Elk back-uptype heeft een verloop tijd volgens het beleid dat momenteel actief is. Een volledig back-uptype wordt echter beschouwd als bovenliggend item voor de volgende ' verschillen ', ' Increments ' en ' logs '. Een differentieel en een ' logboek ' zijn geen bovenliggende items voor iemand anders. Een ' incrementeel ' kan een bovenliggend item van het niveau ' incremental ' zijn. Zelfs als een bovenliggend item is gemarkeerd voor verwijdering, wordt het niet werkelijk verwijderd als de onderliggende verschillen of logboeken niet zijn verlopen. Als een beleid bijvoorbeeld wordt gewijzigd van dagelijks volledig in wekelijks, worden alle eerdere volledige versies die niet op zondagen zijn, gemarkeerd voor verwijdering. Maar ze worden niet daad werkelijk verwijderd totdat de logboeken die u dagelijks hebt gemaakt, zijn verlopen. Met andere woorden, ze worden bewaard op basis van de meest recente duur van het logboek. Zodra de logboeken zijn verlopen, worden zowel de logboeken als deze volledige items verwijderd.

Met deze principes kunt u de volgende tabel lezen om inzicht te krijgen in de gevolgen van een beleids wijziging.

|Oud beleid/nieuw beleid  |Dagelijkse volledige en Logboeken  | Wekelijkse volledige en dagelijkse verschillen en Logboeken  |Wekelijkse volledige en dagelijkse stappen en Logboeken  |
|---------|---------|---------|---------|
|Dagelijkse volledige en Logboeken     |   -      |    De vorige volledigten die zich niet op dezelfde dag van de week bevinden, worden gemarkeerd voor verwijdering, maar bewaard tot de Bewaar periode voor het logboek     |    De vorige volledigten die zich niet op dezelfde dag van de week bevinden, worden gemarkeerd voor verwijdering, maar bewaard tot de Bewaar periode voor het logboek     |
|Wekelijkse volledige en dagelijkse verschillen en Logboeken     |   De vorige wekelijkse Bewaar periode wordt opnieuw berekend conform het meest recente beleid. De vorige verschillen worden onmiddellijk verwijderd      |    -     |    De vorige verschillen worden onmiddellijk verwijderd     |
|Wekelijkse volledige en dagelijkse stappen en Logboeken     |     De vorige wekelijkse Bewaar periode wordt opnieuw berekend conform het meest recente beleid. De vorige stappen worden direct verwijderd    |     De vorige stappen worden direct verwijderd    |    -     |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het maken van een back-up van [SAP Hana-data bases](./backup-azure-sap-hana-database.md) die worden uitgevoerd op Azure
