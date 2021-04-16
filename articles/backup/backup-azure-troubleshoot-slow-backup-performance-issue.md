---
title: Problemen met langzame back-ups van bestanden en mappen oplossen
description: Biedt richtlijnen voor probleemoplossing om u te helpen de oorzaak van Azure Backup prestatieproblemen te diagnosticeren
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 791f0edf5f50d27147e402f09e7a3e4c2ea7ca43
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518521"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Problemen met langzame back-ups van bestanden en mappen in Azure Backup

Dit artikel bevat richtlijnen voor probleemoplossing om u te helpen bij het vaststellen van de oorzaak van trage back-upprestaties voor bestanden en mappen wanneer u Azure Backup. Wanneer u de agent Azure Backup back-up van bestanden maakt, kan het back-upproces langer duren dan verwacht. Deze vertraging kan worden veroorzaakt door een of meer van de volgende:

* [Er zijn prestatieknelpunten op de computer waar een back-up van wordt maken.](#cause1)
* [Een ander proces of antivirussoftware verstoort het Azure Backup proces.](#cause2)
* [De Backup-agent wordt uitgevoerd op een virtuele Azure-machine (VM).](#cause3)  
* [U een back-up van een groot aantal (miljoenen) bestanden.](#cause4)

Voordat u begint met het oplossen van problemen, raden we u aan om de meest recente Azure Backup [agent te downloaden en installeren.](https://aka.ms/azurebackup_agent) De Backup-agent wordt regelmatig bijgewerkt om verschillende problemen op te lossen, functies toe te voegen en de prestaties te verbeteren.

We raden u ook ten zeerste aan de veelgestelde vragen [over Azure Backup](backup-azure-backup-faq.yml) service te lezen om te controleren of u geen veelvoorkomende configuratieproblemen ondervindt.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>Oorzaak: Back-up van taak wordt uitgevoerd in niet-geoptimiseerde modus

* De MARS-agent kan de back-upfunctie **uitvoeren** in de geoptimaliseerde modus met behulp van USN (update sequence number) change journal of **niet-geoptimaliseerde modus** door te controleren op wijzigingen in mappen of bestanden door het hele volume te scannen.
* De niet-geoptimiseerde modus is traag omdat de agent elk bestand op het volume moet scannen en moet vergelijken met de metagegevens om de gewijzigde bestanden te bepalen.
* Als u dit  wilt controleren, opent u Taakdetails in de console van de MARS-agent en controleert u de status om te zien of het overdragen van gegevens (niet-geoptimaliseerd, kan meer tijd **duren),** zoals hieronder wordt weergegeven:

    ![Wordt uitgevoerd in niet-geoptimiseerde modus](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* De volgende voorwaarden kunnen ertoe leiden dat de back-up job wordt uitgevoerd in de niet-geoptimiseerde modus:
  * De eerste back-up (ook wel initiële replicatie genoemd) wordt altijd uitgevoerd in de niet-geoptimiseerde modus
  * Als de vorige back-up is mislukt, wordt de volgende geplande back-up job uitgevoerd als niet-geoptimaliseerd.

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Oorzaak: Prestatieknelpunten op de computer

Knelpunten op de computer waar een back-up van wordt opgeslagen, kunnen vertragingen veroorzaken. De computer kan bijvoorbeeld lezen of schrijven naar schijf of beschikbare bandbreedte voor het verzenden van gegevens via het netwerk knelpunten veroorzaken.

Windows biedt een ingebouwd hulpprogramma met de naam [Prestatiemeter](https://techcommunity.microsoft.com/t5/ask-the-performance-team/windows-performance-monitor-overview/ba-p/375481) (Perfmon) om deze knelpunten te detecteren.

Hier vindt u enkele prestatiemeters en -reeksen die nuttig kunnen zijn bij het diagnosticeren van knelpunten voor optimale back-ups.

| Prestatiemeteritem | Status |
| --- | --- |
| Logische schijf (fysieke schijf)--%inactief |<li> 100% niet-actief tot 50% inactief = In orde</br><li> 49% niet-actief tot 20% inactief = Waarschuwing of Monitor</br><li> 19% niet-actief tot 0% inactief = Kritiek of Niet-actief |
| Logische schijf (fysieke schijf)--%Gem. Lezen of schrijven per seconde schijf |<li> 0,001 ms tot 0,015 ms = In orde</br><li> 0,015 ms tot 0,025 ms = Waarschuwing of Monitor</br><li> 0,026 ms of langer = Kritiek of niet-specificatie |
| Logische schijf (fysieke schijf)--Wachtrijlengte huidige schijf (voor alle exemplaren) |80 aanvragen voor meer dan 6 minuten |
| Geheugen: niet-gepaginateerde bytes in pool |<li> Minder dan 60% van de verbruikte pool = In orde<br><li> 61% tot 80% van de verbruikte pool = Waarschuwing of Monitor</br><li> Groter dan 80% verbruikte pool = Kritiek of Niet-specificaties |
| Geheugen: bytes met poolpagina's |<li> Minder dan 60% van de verbruikte pool = In orde</br><li> 61% tot 80% van de verbruikte pool = Waarschuwing of Monitor</br><li> Groter dan 80% verbruikte pool = Kritiek of niet-specificatie |
| Geheugen: beschikbare megabytes |<li> 50% van het beschikbare geheugen of meer = In orde</br><li> 25% vrij geheugen beschikbaar = Bewaken</br><li>10% vrij geheugen beschikbaar = Waarschuwing</br><li> Minder dan 100 MB of 5% beschikbaar vrij geheugen = kritiek of niet-specificatie |
| Processor-- \% Processor Time (alle exemplaren) |<li> Minder dan 60% verbruikt = In orde</br><li> 61% tot 90% verbruikt = Bewaken of voorzichtig</br><li> 91% tot 100% verbruikt = Kritiek |

> [!NOTE]
> Als u bepaalt dat de infrastructuur verantwoordelijk is, raden we u aan de schijven regelmatig te defragmenteren voor betere prestaties.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Oorzaak: Een ander proces of antivirussoftware verstoort Azure Backup

We hebben verschillende gevallen gezien waarbij andere processen in het Windows-systeem de prestaties van het proces van de Azure Backup hebben beïnvloed. Als u bijvoorbeeld zowel de Azure Backup-agent als een ander programma gebruikt om een back-up van gegevens te maken, of als er antivirussoftware wordt uitgevoerd en er een vergrendeling is voor bestanden waar een back-up van moet worden gemaakt, kunnen de meerdere vergrendelingen op bestanden leiden tot een probleem. In dit geval kan de back-up mislukken of kan de taak langer duren dan verwacht.

De beste aanbeveling in dit scenario is om het andere back-upprogramma uit te schakelen om te zien of de back-uptijd voor de Azure Backup agent verandert. Meestal is het voldoende om ervoor te zorgen dat meerdere back-uptaken niet tegelijkertijd worden uitgevoerd om te voorkomen dat ze elkaar beïnvloeden.

Voor antivirusprogramma's raden we u aan de volgende bestanden en locaties uit te sluiten:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe as a process
* Mappen C:\Program Files\Microsoft Azure Recovery Services Agent\
* Scratchlocatie (als u niet de standaardlocatie gebruikt)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Oorzaak: Back-upagent die wordt uitgevoerd op een virtuele Azure-machine

Als u de Backup-agent op een virtuele machine gebruikt, zijn de prestaties langzamer dan wanneer u deze op een fysieke computer hebt uitgevoerd. Dit wordt verwacht vanwege IOPS-beperkingen.  U kunt de prestaties echter optimaliseren door de gegevensstations waar een back-up van wordt gemaakt, over te schakelen naar Azure Premium Storage. We werken aan een oplossing voor dit probleem en de oplossing is beschikbaar in een toekomstige release.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Oorzaak: Een back-up maken van een groot aantal (miljoenen) bestanden

Het verplaatsen van een grote hoeveelheid gegevens duurt langer dan het verplaatsen van een kleiner gegevensvolume. In sommige gevallen heeft de back-uptijd niet alleen betrekking op de grootte van de gegevens, maar ook op het aantal bestanden of mappen. Dit geldt met name wanneer er een back-up wordt gemaakt van miljoenen kleine bestanden (enkele bytes tot een paar kilobytes).

Dit gedrag treedt op omdat terwijl u een back-up maakt van de gegevens en deze naar Azure verplaatst, Uw bestanden tegelijkertijd worden gecatalogus door Azure. In sommige zeldzame scenario's kan de catalogusbewerking langer duren dan verwacht.

De volgende indicatoren kunnen u helpen het knelpunt te begrijpen en dienovereenkomstig te werken aan de volgende stappen:

* **De gebruikersinterface toont de voortgang van de gegevensoverdracht.** De gegevens worden nog steeds overgedragen. De netwerkbandbreedte of de grootte van gegevens kan vertragingen veroorzaken.
* **De gebruikersinterface toont geen voortgang voor de gegevensoverdracht.** Open de logboeken in C:\Program Files\Microsoft Azure Recovery Services Agent\Temp en controleer vervolgens op de vermelding FileProvider::EndData in de logboeken. Deze vermelding geeft aan dat de gegevensoverdracht is voltooid en dat de catalogusbewerking wordt uitgevoerd. Annuleer de back-uptaken niet. Wacht in plaats daarvan iets langer totdat de catalogusbewerking is uitgevoerd. Als het probleem zich blijft voordoen, kunt u contact [opnemen ondersteuning voor Azure.](https://portal.azure.com/#create/Microsoft.Support)

Als u een back-up van grote schijven probeert te [](./offline-backup-azure-data-box.md) maken, is het raadzaam om Azure Data Box eerste back-up (initiële replicatie) te gebruiken.  Als u geen gebruik kunt maken Data Box, kunnen eventuele tijdelijke netwerkproblemen in uw omgeving tijdens lange gegevensoverdrachten via het netwerk leiden tot back-upfouten.  Ter bescherming tegen deze fouten kunt u een paar mappen toevoegen aan uw eerste back-up en steeds meer mappen toevoegen totdat er een back-up van alle mappen is gemaakt in Azure.  Volgende incrementele back-ups zijn relatief sneller.

## <a name="next-steps"></a>Volgende stappen

* [Veelvoorkomende vragen over het maken van back-up van bestanden en mappen](backup-azure-file-folder-backup-faq.yml)
