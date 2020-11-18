---
title: Gids voor het oplossen van problemen met Azure file shares
description: Los problemen met bekende prestaties op met Azure-bestands shares. Ontdek mogelijke oorzaken en bijbehorende tijdelijke oplossingen wanneer deze problemen optreden.
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 6e4eb37477a335ae93b9982692c238d05c81000b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660284"
---
# <a name="troubleshoot-azure-file-shares-performance-issues"></a>Prestatie problemen met Azure file shares oplossen

In dit artikel worden enkele veelvoorkomende problemen met betrekking tot Azure-bestands shares vermeld. Het biedt mogelijke oorzaken en tijdelijke oplossingen voor wanneer u deze problemen ondervindt.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Problemen met hoge latentie, lage door Voer en algemene prestaties

### <a name="cause-1-share-was-throttled"></a>Oorzaak 1: de share is beperkt

Aanvragen worden beperkt wanneer de I/O-bewerkingen per seconde (IOPS), ingangs-of uitstel limieten voor een bestands share zijn bereikt. Zie [Bestands share en bestands schaal doelen](./storage-files-scale-targets.md#file-share-and-file-scale-targets)voor meer informatie over de limieten voor standaard-en Premium-bestands shares.

Als u wilt controleren of uw share wordt beperkt, kunt u Azure-metrische gegevens openen en gebruiken in de portal.

1. Ga in het Azure Portal naar uw opslag account.

1. Selecteer **metrische gegevens** onder **bewaking** in het linkerdeel venster.

1. Selecteer **bestand** als metrische naam ruimte voor het bereik van uw opslag account.

1. Selecteer **trans acties** als metrische gegevens.

1. Voeg een filter toe voor het **antwoord type** en controleer vervolgens of er aanvragen van een van de volgende respons codes zijn:
   * **SuccessWithThrottling**: voor Server Message Block (SMB)
   * **ClientThrottlingError**: voor rest

   ![Scherm afbeelding van de opties voor de metrische gegevens voor Premium-bestands shares, met een eigenschappen Filter van het type antwoord.](media/storage-troubleshooting-premium-fileshares/metrics.png)

   > [!NOTE]
   > Als u een waarschuwing wilt ontvangen, raadpleegt u de sectie [' een waarschuwing maken als een bestands share is beperkt '](#how-to-create-an-alert-if-a-file-share-is-throttled) verderop in dit artikel.

### <a name="solution"></a>Oplossing

- Als u een standaard bestands share gebruikt, schakelt u [grote bestands shares](./storage-files-how-to-create-large-file-share.md?tabs=azure-portal) in voor uw opslag account. Grote bestands shares bieden ondersteuning voor Maxi maal 10.000 IOPS per share.
- Als u een Premium-bestands share gebruikt, verg root u de grootte van de ingerichte bestands share om de limiet voor IOPS te verhogen. Zie de sectie ' wat is inrichten voor Premium file shares ' in de [plannings handleiding van Azure files](./storage-files-planning.md#understanding-provisioning-for-premium-file-shares)voor meer informatie.

### <a name="cause-2-metadata-or-namespace-heavy-workload"></a>Oorzaak 2: meta gegevens of een zware werk belasting van de naam ruimte

Als het meren deel van uw aanvragen meta gegevens georiënteerd is (zoals CreateFile, OPENFILE, closefile, queryinfo of querydirectory), is de latentie erger dan het aantal lees-en schrijf bewerkingen.

Als u wilt bepalen of het meren deel van uw aanvragen meta gegevens georiënteerd is, begint u met stap 1-4 zoals eerder beschreven in oorzaak 1. Voor stap 5 voegt u een eigenschappen filter toe voor de **API-naam** in plaats van een filter toe te voegen voor het **antwoord type**.

![Scherm afbeelding van de opties voor de metrische gegevens voor Premium-bestands shares, met een eigenschappen Filter voor de API-naam.](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Tijdelijke oplossing

- Controleer of de toepassing kan worden aangepast om het aantal bewerkingen voor meta gegevens te verminderen.
- Voeg een virtuele harde schijf (VHD) toe aan de bestands share en koppel de VHD via SMB van de client om bestands bewerkingen uit te voeren op de gegevens. Deze aanpak werkt voor één schrijver en meerdere lezers scenario's, waardoor meta gegevensbewerkingen lokaal kunnen zijn. De installatie biedt prestaties die vergelijkbaar zijn met die van een lokaal rechtstreeks gekoppelde opslag.

### <a name="cause-3-single-threaded-application"></a>Oorzaak 3: toepassing met één thread

Als de toepassing die u gebruikt één thread heeft, kan deze instelling leiden tot een aanzienlijk lagere IOPS-door voer dan de Maxi maal mogelijke door Voer, afhankelijk van uw ingerichte share grootte.

### <a name="solution"></a>Oplossing

- Verg root de parallellisatie van de toepassing door het aantal threads te verhogen.
- Schakel over naar toepassingen waarbij parallellisme mogelijk is. Voor kopieer bewerkingen kunt u bijvoorbeeld AzCopy of RoboCopy van Windows-clients of de **parallelle** opdracht van Linux-clients gebruiken.

## <a name="very-high-latency-for-requests"></a>Zeer hoge latentie voor aanvragen

### <a name="cause"></a>Oorzaak

De virtuele client machine (VM) bevindt zich in een andere regio dan de bestands share.

### <a name="solution"></a>Oplossing

- Voer de toepassing uit vanaf een virtuele machine die zich in dezelfde regio bevindt als de bestands share.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>De client kan geen maximale door voer worden gerealiseerd die door het netwerk wordt ondersteund

### <a name="cause"></a>Oorzaak
Een mogelijke oorzaak is een gebrek aan SMB-ondersteuning voor meerdere kanalen voor standaard bestands shares. Azure Files ondersteunt momenteel slechts één kanaal, dus er is slechts één verbinding tussen de client-VM en de server. Deze enkele verbinding wordt vastgelegd op één kern op de client-VM, zodat de maximale door Voer die uit een virtuele machine kan worden behaald, is gebonden aan één kern.

### <a name="workaround"></a>Tijdelijke oplossing

- Schakel voor Premium-bestands shares [SMB meerdere kanalen in voor een FileStorage-account](storage-files-enable-smb-multichannel.md).
- Het verkrijgen van een virtuele machine met een grotere kern kan helpen de door voer te verbeteren.
- Als de client toepassing vanaf meerdere Vm's wordt uitgevoerd, wordt de door Voer verhoogd.
- Gebruik waar mogelijk REST Api's.

## <a name="throughput-on-linux-clients-is-significantly-lower-than-that-of-windows-clients"></a>De door Voer op Linux-clients is aanzienlijk lager dan die van Windows-clients

### <a name="cause"></a>Oorzaak

Dit is een bekend probleem met de implementatie van de SMB-client op Linux.

### <a name="workaround"></a>Tijdelijke oplossing

- De belasting over meerdere Vm's spreiden.
- Gebruik op dezelfde VM meerdere koppel punten met een **nosharesock** -optie en verspreid de belasting over deze koppel punten.
- Probeer in Linux te koppelen met een **nostrictsync** -optie om te voor komen dat een SMB-leegmaak bewerking wordt afgedwongen bij elke **fsync** -aanroep. Voor Azure Files heeft deze optie geen invloed op de consistentie van gegevens, maar dit kan leiden tot verlopen meta gegevens van bestanden in mapweergaven (**ls-l-** opdracht). Het rechtstreeks opvragen van meta gegevens van bestanden met behulp van de **stat** -opdracht retourneert de meest recente bestands-meta gegevens.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Hoge latentie voor meta gegevens-zware workloads met uitgebreide open/close-bewerkingen

### <a name="cause"></a>Oorzaak

Geen ondersteuning voor Directory-leases.

### <a name="workaround"></a>Tijdelijke oplossing

- Vermijd indien mogelijk het gebruik van een buitensporige opening/afsluit ingang binnen een korte periode.
- Voor Linux Vm's verhoogt u de time-out voor de cachemap door **actimeo \<sec> =** als een koppelings optie op te geven. De time-out is standaard 1 seconde, dus een grotere waarde, zoals 3 of 5 seconden, kan helpen.
- Voor CentOS Linux-of Red Hat Enterprise Linux (RHEL) Vm's, moet u het systeem upgraden naar CentOS Linux 8,2 of RHEL 8,2. Voor andere virtuele Linux-machines moet u de kernel upgraden naar 5,0 of hoger.

## <a name="low-iops-on-centos-linux-or-rhel"></a>Lage IOPS op CentOS Linux of RHEL

### <a name="cause"></a>Oorzaak

Een I/O-diepte van meer dan 1 wordt niet ondersteund op CentOS Linux of RHEL.

### <a name="workaround"></a>Tijdelijke oplossing

- Voer een upgrade uit naar CentOS Linux 8 of RHEL 8.
- Ga naar Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-file-shares-in-linux"></a>Langzaam kopiëren van en naar Azure-bestands shares in Linux

Zie de sectie ' langzaam kopiëren naar en van Azure file shares in Linux ' in de [Linux-probleemoplossings handleiding](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux)als u problemen ondervindt bij het kopiëren van bestanden.

## <a name="jittery-or-sawtooth-pattern-for-iops"></a>Jitter-of zaagtand patroon voor IOPS

### <a name="cause"></a>Oorzaak

De client toepassing is op consistente wijze langer dan de limiet voor de basis lijn. Er is momenteel geen service-side-aanpassing van de aanvraag belasting. Als de client de limiet voor de basis lijn overschrijdt, wordt deze door de service beperkt. De beperking kan ertoe leiden dat de client een jitter-of zaagtand IOPS-patroon ondervindt. In dit geval kan de gemiddelde IOPS die door de client wordt behaald, lager zijn dan de basis lijn voor IOPS.

### <a name="workaround"></a>Tijdelijke oplossing
- Verminder de belasting van de aanvraag van de client toepassing, zodat de share niet wordt beperkt.
- Verhoog het quotum van de share zodat de share niet wordt beperkt.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Buitensporige DirectoryOpen/DirectoryClose-aanroepen

### <a name="cause"></a>Oorzaak

Als het aantal **DirectoryOpen/DirectoryClose-** aanroepen zich bevindt in de top API-aanroepen en u niet verwacht dat de client dat veel aanroepen doet, kan het probleem worden veroorzaakt door de antivirus software die is geïnstalleerd op de Azure-client-VM.

### <a name="workaround"></a>Tijdelijke oplossing

- Er is een oplossing voor dit probleem beschikbaar in de [Update van april platform voor Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Het maken van bestanden verloopt langzamer dan verwacht

### <a name="cause"></a>Oorzaak

Werk belastingen die afhankelijk zijn van het maken van een groot aantal bestanden, zien geen aanzienlijk verschil in de prestaties van Premium-bestands shares en standaard bestands shares.

### <a name="workaround"></a>Tijdelijke oplossing

- Geen.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Trage prestaties van Windows 8,1 of server 2012 R2

### <a name="cause"></a>Oorzaak

Meer dan de verwachte latentie bij het openen van Azure-bestands shares voor I/O-intensieve workloads.

### <a name="workaround"></a>Tijdelijke oplossing

- Installeer de beschik bare [hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).

## <a name="smb-multichannel-option-not-visible-under-file-share-settings"></a>De optie SMB meerdere kanalen wordt niet weer gegeven onder instellingen voor bestands share. 

### <a name="cause"></a>Oorzaak

Het abonnement is niet geregistreerd voor de functie, of de regio en het account type worden niet ondersteund.

### <a name="solution"></a>Oplossing

Zorg ervoor dat uw abonnement is geregistreerd voor de functie SMB meerdere kanalen. Zie [aan](storage-files-enable-smb-multichannel.md#getting-started) de slag om te controleren of het account soort FileStorage is (Premium file-account) op de pagina account overzicht. 

## <a name="smb-multichannel-is-not-being-triggered"></a>SMB meerdere kanalen wordt niet geactiveerd.

### <a name="cause"></a>Oorzaak

Recente wijzigingen in de configuratie-instellingen van SMB meerdere kanalen zonder opnieuw koppelen.

### <a name="solution"></a>Oplossing
 
-   Nadat alle wijzigingen in de Windows SMB-client of de SMB-instellingen voor meerdere kanalen zijn geconfigureerd, moet u de share ontkoppelen, 60 seconden wachten en de share opnieuw koppelen om het multi kanaal te activeren.
-   Voor Windows client-besturings systeem genereren IO-belasting met hoge wachtrij diepte wachtrij diepte = 8, bijvoorbeeld het kopiëren van een bestand om SMB meerdere kanalen te activeren.  Voor het besturings systeem van de server wordt SMB meerdere kanalen geactiveerd met wachtrij diepte = 1. Dit betekent dat zodra u een IO-bewerking naar de share start.

## <a name="high-latency-on-web-sites-hosted-on-file-shares"></a>Hoge latentie op websites die worden gehost op bestands shares 

### <a name="cause"></a>Oorzaak  

Een hoog aantal bericht wijzigingen in bestands shares kan leiden tot aanzienlijke hoge latentie. Dit gebeurt meestal met websites die worden gehost op bestands shares met een diepe geneste mapstructuur. Een typisch scenario is een door IIS gehoste webtoepassing waarbij bestands wijzigings meldingen worden ingesteld voor elke directory in de standaard configuratie. Elke wijziging (ReadDirectoryChangesW) op de share die door de SMB-client wordt geregistreerd, wordt een wijzigings melding van de bestands service naar de client verzonden, waardoor systeem bronnen worden gebruikt. het probleem verloopt met het aantal wijzigingen. Dit kan ertoe leiden dat delen worden beperkt en dus resulteren in een hogere latentie van de client zijde. 

U kunt de metrische gegevens van Azure in de portal gebruiken om te bevestigen. 

1. Ga in het Azure Portal naar uw opslag account. 
1. Selecteer in het linkermenu, onder bewaking, metrische gegevens. 
1. Selecteer bestand als metrische naam ruimte voor het bereik van uw opslag account. 
1. Selecteer trans acties als metrische gegevens. 
1. Voeg een filter toe voor ResponseType en controleer of er aanvragen zijn met een antwoord code van SuccessWithThrottling (voor SMB) of ClientThrottlingError (voor REST).

### <a name="solution"></a>Oplossing 

- Als de melding voor bestands wijzigingen niet wordt gebruikt, schakelt u de bestands wijzigings melding uit (voor keur).
    - [Melding voor bestands wijzigingen uitschakelen](https://support.microsoft.com/help/911272/fix-asp-net-2-0-connected-applications-on-a-web-site-may-appear-to-sto) door FCNMode bij te werken. 
    - Werk het polling interval van het IIS-werk proces (W3WP) bij naar 0 door `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds ` in uw REGI ster in te stellen en het W3wp-proces opnieuw te starten. Zie [algemene register sleutels die worden gebruikt door een groot aantal IIS-onderdelen](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp)voor meer informatie over deze instelling.
- De frequentie verhogen van het polling-interval voor bestands wijzigings meldingen om het volume te verminderen.
    - Werk het polling interval van het W3WP werk proces bij naar een hogere waarde (bijvoorbeeld 10mins of 30mins) op basis van uw vereiste. Stel `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds ` [in het REGI ster in](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp) en start het W3wp-proces opnieuw.
- Als de toegewezen fysieke map van uw website geneste mapstructuur heeft, kunt u proberen het bereik van de bestands wijzigings melding te beperken om het meldings volume te verminderen.
    - IIS gebruikt standaard configuratie van Web.config-bestanden in de fysieke map waaraan de virtuele map is toegewezen, evenals in onderliggende mappen in die fysieke map. Als u geen Web.config-bestanden in onderliggende directory's wilt gebruiken, geeft u False op voor het kenmerk allowSubDirConfig in de virtuele map. Meer informatie vindt u [hier](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories). 

Stel de instelling ' allowSubDirConfig ' van de virtuele IIS-map in Web.Config in op false om toegewezen fysieke onderliggende mappen van het bereik uit te sluiten.  

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Een waarschuwing maken als een bestands share wordt beperkt

1. Ga in het Azure Portal naar uw opslag account.
1. Selecteer in de sectie **controle** de optie **waarschuwingen** en selecteer vervolgens **nieuwe waarschuwings regel**.
1. Selecteer **Resource bewerken**, selecteer het **Bestands bron type** voor het opslag account en selecteer vervolgens **gereed**. Als de naam van het opslag account bijvoorbeeld *Contoso* is, selecteert u de resource contoso/file.
1. Selecteer **voor waarde selecteren** om een voor waarde toe te voegen.
1. Selecteer in de lijst met signalen die worden ondersteund voor het opslag account de metrische gegevens van de **trans acties** .
1. Selecteer in het deel venster **signaal logica configureren** in de vervolg keuzelijst **dimensie naam** de optie **antwoord type**.
1. Selecteer in de vervolg keuzelijst **dimensie waarden** **SUCCESSWITHTHROTTLING** (voor SMB) of **ClientThrottlingError** (voor rest).

   > [!NOTE]
   > Als noch de **SuccessWithThrottling** noch de **ClientThrottlingError** -dimensie waarde wordt weer gegeven, betekent dit dat de resource niet is beperkt. Als u de dimensie waarde wilt toevoegen, klikt u naast de vervolg keuzelijst **dimensie waarden** op **aangepaste waarde toevoegen**, voert u **SuccessWithThrottling** of **ClientThrottlingError** in, selecteert u **OK** en herhaalt u stap 7.

1. Selecteer in de vervolg keuzelijst **dimensie naam** de optie **Bestands share**.
1. Selecteer in de vervolg keuzelijst **dimensie waarden** de bestands share of shares waarop u een waarschuwing wilt ontvangen.

   > [!NOTE]
   > Als de bestands share een standaard bestands share is, selecteert u **alle huidige en toekomstige waarden**. In de vervolg keuzelijst dimensie waarden worden de bestands shares niet weer gegeven omdat er geen metrische gegevens beschikbaar zijn voor standaard bestands shares. Het beperken van waarschuwingen voor standaard bestands shares wordt geactiveerd als een bestands share in het opslag account wordt beperkt en de waarschuwing niet identificeert welke bestands share is beperkt. Omdat metrische gegevens per aandeel niet beschikbaar zijn voor standaard bestands shares, wordt u aangeraden één bestands share per opslag account te gebruiken.

1. Definieer de waarschuwings parameters door de **drempel waarde**, de **operator**, **de granulariteit van de aggregatie** en de **frequentie van de evaluatie** in te voeren en selecteer vervolgens **gereed**.

    > [!TIP]
    > Als u een statische drempel waarde gebruikt, kan het meet diagram u helpen een redelijke drempelwaarde te bepalen als de bestands share momenteel wordt beperkt. Als u een dynamische drempel waarde gebruikt, worden de berekende drempel waarden in de grafiek weer gegeven op basis van recente gegevens.

1. Selecteer **actie groep selecteren** en voeg vervolgens een actie groep (bijvoorbeeld E-mail of SMS) toe aan de waarschuwing door een bestaande actie groep te selecteren of door een nieuwe actie groep te maken.
1. Voer de details van de waarschuwing in, zoals naam, **Beschrijving** en **Ernst** van de **waarschuwings regel**.
1. Selecteer **waarschuwings regel maken** om de waarschuwing te maken.

Zie [overzicht van waarschuwingen in Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)voor meer informatie over het configureren van waarschuwingen in azure monitor.

## <a name="how-to-create-alerts-if-a-premium-file-share-is-trending-toward-being-throttled"></a>Hoe kan ik waarschuwingen maken als een Premium-bestands share wordt getrendd naar een beperkt aantal

1. Ga in het Azure Portal naar uw opslag account.
1. Selecteer in de sectie **controle** de optie **waarschuwingen** en selecteer vervolgens **nieuwe waarschuwings regel**.
1. Selecteer **Resource bewerken**, selecteer het **Bestands bron type** voor het opslag account en selecteer vervolgens **gereed**. Als de naam van het opslag account bijvoorbeeld *Contoso* is, selecteert u de resource contoso/file.
1. Selecteer **voor waarde selecteren** om een voor waarde toe te voegen.
1. Selecteer in de lijst met signalen die worden ondersteund voor het opslag **account de waarde** voor uitgaand verkeer.

   > [!NOTE]
   > U moet drie afzonderlijke waarschuwingen maken om te worden gewaarschuwd wanneer de waarden voor ingang, uitgaand of trans actie groter zijn dan de drempel waarden die u instelt. Dit komt doordat een waarschuwing alleen wordt geactiveerd wanneer aan alle voor waarden wordt voldaan. Als u bijvoorbeeld alle voor waarden in één waarschuwing opneemt, wordt u alleen gewaarschuwd als binnenkomend, uitgaand en trans acties de drempel waarden overschrijden.

1. Schuif omlaag. Selecteer in de vervolg keuzelijst **dimensie naam** de optie **Bestands share**.
1. Selecteer in de vervolg keuzelijst **dimensie waarden** de bestands share of shares waarop u een waarschuwing wilt ontvangen.
1. Definieer de waarschuwings parameters door waarden te selecteren in de **operator**, **drempel waarde**, **aggregatie granulatie** en **frequentie van** vervolg keuzelijsten en selecteer vervolgens **gereed**.

   De metrische gegevens voor uitgang, binnenkomen en trans acties worden per minuut weer gegeven, maar u hebt een uitgangs-, ingangs-en I/O per seconde ingericht. Als uw ingerichte uitvoer bijvoorbeeld 90 &nbsp; mebibytes per seconde (MIB/s) is en u wilt dat uw drempel waarde 80 &nbsp; procent van de ingerichte uitvoer is, selecteert u de volgende waarschuwings parameters: 
   - Voor **drempel waarde**: *75497472* 
   - Voor **operator**: *groter dan of gelijk aan*
   - Voor **aggregatie type**: *gemiddeld*
   
   Afhankelijk van hoe ruis uw waarschuwing moet zijn, kunt u ook waarden voor **aggregatie granulatie** en **frequentie van de evaluatie** selecteren. Als u bijvoorbeeld wilt dat uw waarschuwing de gemiddelde ingang gedurende de periode van één uur bekijkt en u wilt dat uw waarschuwings regel elk uur wordt uitgevoerd, selecteert u het volgende:
   - Voor **granulatie van aggregatie**: *1 uur*
   - Voor de **frequentie van de evaluatie**: *1 uur*

1. Selecteer **actie groep selecteren** en voeg vervolgens een actie groep (bijvoorbeeld E-mail of SMS) toe aan de waarschuwing door een bestaande actie groep te selecteren of door een nieuwe te maken.
1. Voer de details van de waarschuwing in, zoals naam, **Beschrijving** en **Ernst** van de **waarschuwings regel**.
1. Selecteer **waarschuwings regel maken** om de waarschuwing te maken.

    > [!NOTE]
    > - Als u een melding wilt ontvangen dat uw Premium-bestands share bijna wordt beperkt *vanwege de ingerichte* inkomende inkomen, volgt u de voor gaande instructies, maar met de volgende wijziging:
    >    - Selecteer in stap 5 de **ingangs** metriek in **plaats van** uitgaand.
    >
    > - Als u een melding wilt ontvangen dat uw Premium-bestands share bijna wordt beperkt *vanwege ingerichte IOPS*, volgt u de voor gaande instructies, maar met de volgende wijzigingen:
    >    - In stap 5 selecteert u de metrische gegevens van de **trans actie** in **plaats van een** aflopende waarde.
    >    - In stap 10 is de enige optie voor **aggregatie type** *totaal*. De drempel waarde is daarom afhankelijk van de geselecteerde aggregatie granulatie. Als u bijvoorbeeld wilt dat uw drempel 80 &nbsp; procent van de ingerichte basis lijn IOPS is en u *1 uur* selecteert voor de **granulariteit van aggregatie**, is uw **drempel waarde** uw basislijn IOPS (in bytes) &times; &nbsp; 0,8 &times; &nbsp; 3600. 

Zie [overzicht van waarschuwingen in Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)voor meer informatie over het configureren van waarschuwingen in azure monitor.

## <a name="see-also"></a>Zie ook
- [Problemen met Azure Files in Windows oplossen](storage-troubleshoot-windows-file-connection-problems.md)  
- [Problemen met Azure Files in Linux oplossen](storage-troubleshoot-linux-file-connection-problems.md)  
- [Veelgestelde vragen over Azure Files](storage-files-faq.md)
