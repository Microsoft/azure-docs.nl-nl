---
title: Problemen met langzame back-ups van bestanden en mappen oplossen
description: Biedt richt lijnen voor probleem oplossing waarmee u de oorzaak van Azure Backup prestatie problemen kunt vaststellen
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: b3f2ac343ef4a703f347ec8a57f242a636bb32d2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88824012"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Problemen met langzame back-ups van bestanden en mappen in Azure Backup

Dit artikel bevat richt lijnen voor probleem oplossing waarmee u de oorzaak van trage back-upprestaties voor bestanden en mappen kunt vaststellen wanneer u Azure Backup gebruikt. Wanneer u de Azure Backup-agent gebruikt voor het maken van back-ups van bestanden, kan het back-upproces langer duren dan verwacht. Deze vertraging kan een of meer van de volgende oorzaken hebben:

* [Er zijn prestatie knelpunten op de computer waarvan een back-up wordt gemaakt.](#cause1)
* [Een ander proces of antivirus software verstoort het Azure Backup proces.](#cause2)
* [De back-upagent wordt uitgevoerd op een virtuele Azure-machine (VM).](#cause3)  
* [U maakt een back-up van een groot aantal (miljoenen) bestanden.](#cause4)

Voordat u problemen gaat oplossen, wordt u aangeraden de [nieuwste Azure backup-agent](https://aka.ms/azurebackup_agent)te downloaden en te installeren. Er worden regel matig updates voor de back-upagent gemaakt om verschillende problemen op te lossen, functies toe te voegen en de prestaties te verbeteren.

We raden u ook ten zeerste aan de [Veelgestelde vragen over de Azure backup-service](backup-azure-backup-faq.md) te controleren om ervoor te zorgen dat u geen veelvoorkomende configuratie problemen ondervindt.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>Oorzaak: de back-uptaak wordt uitgevoerd in niet-geoptimaliseerde modus

* De MARS-agent kan de back-uptaak uitvoeren in de **geoptimaliseerde modus** met USN (Update Sequence Number) wijzigings logboek of niet- **geoptimaliseerde modus** door te controleren op wijzigingen in mappen of bestanden door het hele volume te scannen.
* Niet-geoptimaliseerde modus is traag omdat de agent elk bestand op het volume moet scannen en vergelijken met de meta gegevens om de gewijzigde bestanden te bepalen.
* Als u dit wilt controleren, opent u de **taak Details** van de Mars-agent console en controleert u de status om te zien of er **gegevens worden overgedragen (niet-geoptimaliseerd, kan meer tijd duren)** , zoals hieronder wordt weer gegeven:

    ![Wordt uitgevoerd in niet-geoptimaliseerde modus](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* De volgende voor waarden kunnen ervoor zorgen dat de back-uptaak wordt uitgevoerd in niet-geoptimaliseerde modus:
  * De eerste back-up (ook wel de eerste replicatie genoemd) wordt altijd uitgevoerd in niet-geoptimaliseerde modus
  * Als de vorige back-uptaak mislukt, wordt de volgende geplande back-uptaak uitgevoerd als niet-geoptimaliseerd.

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Oorzaak: prestatie knelpunten op de computer

Knel punten op de computer waarvan een back-up wordt gemaakt, kunnen vertragingen veroorzaken. Bijvoorbeeld: de mogelijkheid van de computer om te lezen van of te schrijven naar de schijf of beschik bare band breedte voor het verzenden van gegevens via het netwerk, kan knel punten veroorzaken.

Windows biedt een ingebouwd hulp programma met de naam [Performance Monitor](https://techcommunity.microsoft.com/t5/ask-the-performance-team/windows-performance-monitor-overview/ba-p/375481) (PerfMon) om deze knel punten te detecteren.

Hier volgen enkele prestatie meter items en bereiken die nuttig kunnen zijn bij het diagnosticeren van knel punten voor optimale back-ups.

| Prestatiemeteritem | Status |
| --- | --- |
| Logische schijf (fysieke schijf)--% inactief |<li> 100% niet-actief tot 50% inactief = in orde</br><li> 49% niet actief tot 20% inactief = waarschuwing of monitor</br><li> 19% niet-actief tot 0% actief = kritiek of van specificatie |
| Logische schijf (fysieke schijf)--% gem. Lees-of schrijf tijd schijf |<li> 0,001 MS tot 0,015 MS = in orde</br><li> 0,015 MS tot 0,025 MS = waarschuwing of monitor</br><li> 0,026 MS of langer = kritiek of van specificatie |
| Logische schijf (fysieke schijf)--huidige wachtrij lengte voor de schijf (voor alle exemplaren) |80 aanvragen langer dan 6 minuten |
| Geheugen--groep niet-wisselbaar bytes |<li> Minder dan 60% van verbruikte groep = in orde<br><li> 61% tot 80% van verbruikte pool = waarschuwing of monitor</br><li> Groter dan 80% groep verbruikt = kritiek of van specificatie |
| Geheugen--groeps wisselbaar bytes |<li> Minder dan 60% van verbruikte groep = in orde</br><li> 61% tot 80% van verbruikte pool = waarschuwing of monitor</br><li> Groter dan 80% groep verbruikt = kritiek of van specificatie |
| Geheugen-beschik bare mega bytes |<li> 50% vrije geheugen beschikbaar of meer = in orde</br><li> 25% vrije beschik bare geheugen = monitor</br><li>10% vrije beschik bare geheugen = waarschuwing</br><li> Minder dan 100 MB of 5% vrije geheugen beschikbaar = kritiek of van specificatie |
| Processor-- \% processor tijd (alle exemplaren) |<li> Minder dan 60% verbruikt = in orde</br><li> 61% tot 90% verbruikt = monitor of waarschuwing</br><li> 91% tot 100% verbruikt = kritiek |

> [!NOTE]
> Als u vaststelt dat de infra structuur de culprit is, raden wij u aan de schijven regel matig te defragmenteren voor betere prestaties.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Oorzaak: een ander proces of antivirus software die met Azure Backup verstoort

We hebben verschillende instanties gezien waarbij andere processen in het Windows-systeem een negatieve invloed hebben op de prestaties van het proces van de Azure Backup Agent. Als u bijvoorbeeld zowel de Azure Backup Agent als een ander programma gebruikt voor het maken van een back-up van gegevens, of als de antivirus software wordt uitgevoerd en er een vergren deling is ingesteld op bestanden waarvan een back-up moet worden gemaakt, kunnen de meerdere vergren delingen op bestanden leiden tot conflicten. In dit geval kan het maken van de back-up mislukken of kan de taak langer duren dan verwacht.

De aanbevolen aanbeveling in dit scenario is om het andere back-upprogramma uit te scha kelen om te zien of de back-uptijd voor de Azure Backup Agent wordt gewijzigd. Normaal gesp roken zorgt ervoor dat meerdere back-uptaken op hetzelfde moment voldoende zijn om te voor komen dat ze elkaar beïnvloeden.

Voor antivirus Programma's wordt u aangeraden de volgende bestanden en locaties uit te sluiten:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe als proces
* C:\Program Files\Microsoft Azure Recovery Services agent \ mappen
* Scratch locatie (als u geen gebruik maakt van de standaard locatie)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Oorzaak: de back-upagent die wordt uitgevoerd op een virtuele machine van Azure

Als u de back-upagent op een virtuele machine uitvoert, zijn de prestaties langzamer dan wanneer u deze op een fysieke computer uitvoert. Dit wordt verwacht als gevolg van IOPS-beperkingen.  U kunt de prestaties echter optimaliseren door te scha kelen tussen de gegevens stations waarvan een back-up wordt gemaakt naar Azure Premium Storage. Er wordt gewerkt aan het oplossen van dit probleem en de oplossing is beschikbaar in een toekomstige release.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Oorzaak: een back-up maken van een groot aantal bestanden (miljoenen)

Het verplaatsen van een grote hoeveelheid gegevens duurt langer dan het verplaatsen van een kleiner gegevens volume. In sommige gevallen is de back-uptijd gerelateerd aan niet alleen de grootte van de gegevens, maar ook het aantal bestanden of mappen. Dit geldt met name wanneer een back-up wordt gemaakt van miljoenen kleine bestanden (enkele bytes van een paar kilo bytes).

Dit probleem treedt op omdat u een back-up maakt van de gegevens en deze verplaatst naar Azure, maar Azure uw bestanden tegelijkertijd catalogiseert. In sommige zeldzame scenario's kan de catalogus bewerking langer duren dan verwacht.

De volgende indica toren kunnen u helpen inzicht te krijgen in het knel punt en u kunt de volgende stappen uitvoeren:

* **De gebruikers interface toont de voortgang van de gegevens overdracht**. De gegevens worden nog overgedragen. De netwerk bandbreedte of de omvang van gegevens kan vertragingen veroorzaken.
* **De gebruikers interface toont geen voortgang voor de gegevens overdracht**. Open de logboeken die zich bevinden in C:\Program Files\Microsoft Azure Recovery Services Agent\Temp en controleer vervolgens het item FileProvider:: EndData in de logboeken. Dit item geeft aan dat de gegevens overdracht is voltooid en dat de catalogus bewerking wordt uitgevoerd. Annuleer de back-uptaken niet. Wacht in plaats daarvan iets langer om de catalogus bewerking te volt ooien. Als het probleem zich blijft voordoen, neemt u contact op met de [ondersteuning van Azure](https://portal.azure.com/#create/Microsoft.Support).

Als u een back-up wilt maken van grote schijven, is het raadzaam om [Azure data Box](./offline-backup-azure-data-box.md) te gebruiken voor de eerste back-up (initiële replicatie).  Als u Data Box niet kunt gebruiken, kunnen eventuele tijdelijke netwerk problemen die zich in uw omgeving voordoen tijdens lange gegevens overdracht via het netwerk, leiden tot back-upfouten.  Als u deze fouten wilt beveiligen, kunt u een paar mappen toevoegen aan de eerste back-up en zo snel mogelijk meer mappen toevoegen totdat de back-up van alle mappen is gemaakt naar Azure.  Volgende incrementele back-ups zullen relatief sneller zijn.

## <a name="next-steps"></a>Volgende stappen

* [Veelgestelde vragen over het maken van back-ups van bestanden en mappen](backup-azure-file-folder-backup-faq.md)
