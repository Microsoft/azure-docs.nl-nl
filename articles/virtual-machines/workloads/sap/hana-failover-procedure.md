---
title: HANA-failover-procedure voor een nood site voor SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Failover uitvoeren naar een nood herstel site voor SAP HANA op Azure (grote exemplaren)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2a33340524556f5da1703cae3532f053fbe8ba13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670979"
---
# <a name="disaster-recovery-failover-procedure"></a>Procedure bij failover van het noodherstel


>[!IMPORTANT]
>Dit artikel is geen vervanging voor de documentatie over SAP HANA beheer of SAP-opmerkingen. Het is belang rijk dat u beschikt over een solide kennis van en ervaring op het SAP HANA beheer en bewerkingen, met name voor back-up, herstel, hoge Beschik baarheid en herstel na nood gevallen (DR). In dit artikel worden scherm afbeeldingen van SAP HANA Studio weer gegeven. De inhoud, de structuur en de aard van de schermen van SAP-beheer Programma's en de hulpprogram ma's zelf kunnen veranderen van SAP HANA release naar release.

Er zijn twee gevallen waarin u rekening moet houden bij een failover naar een DR-site:

- U hebt de SAP HANA-data base nodig om terug te gaan naar de meest recente status van de gegevens. In dit geval is er een self-service script waarmee u de failover kunt uitvoeren zonder dat u contact hoeft op te nemen met micro soft. Voor de failback moet u samen werken met micro soft.
- U wilt herstellen naar een opslag momentopname die niet de meest recente gerepliceerde moment opname is. In dit geval moet u samen werken met micro soft. 

>[!NOTE]
>De volgende stappen moeten worden uitgevoerd in de HANA-eenheid voor grote instanties, waarmee de DR-eenheid wordt aangeduid. 
 
Als u de meest recente back-upmomentopnamen wilt herstellen, volgt u de stappen in ' Full DR failover-azure_hana_dr_failover uitvoeren ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf). 

Als u meerdere exemplaren van SAP HANA failover wilt uitvoeren, voert u de azure_hana_dr_failover opdracht verschillende keren uit. Geef desgevraagd de SAP HANA SID op waarvoor u een failover wilt uitvoeren en die u wilt herstellen. 


U kunt de DR failover ook testen zonder dat dit van invloed is op de daad werkelijke replicatie relatie. Als u een testfailover wilt uitvoeren, volgt u de stappen in ' test DR failover-azure_hana_test_dr_failover uitvoeren ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf). 

>[!IMPORTANT]
>Voer *geen* productie transacties uit op het exemplaar dat u hebt gemaakt in de Dr-site via het proces van het **testen van een failover**. De opdracht azure_hana_test_dr_failover een set volumes maakt die geen relatie hebben met de primaire site. Als gevolg hiervan is de synchronisatie van de primaire site *niet* mogelijk. 

Als u meerdere exemplaren van SAP HANA wilt testen, voert u het script meerdere keren uit. Voer desgevraagd de SAP HANA SID in van het exemplaar dat u wilt testen voor failover. 

>[!NOTE]
>Als u een failover naar de DR-site moet uitvoeren om een aantal gegevens te redden die zijn verwijderd en de nood herstel volumes moeten worden ingesteld op een eerdere moment opname, is deze procedure van toepassing. 

1. Sluit het niet-productie-exemplaar van HANA af op de nood herstel eenheid van HANA grote instanties die u uitvoert. Een niet-actieve HANA-productie instantie is vooraf geïnstalleerd.
1. Zorg ervoor dat er geen SAP HANA processen worden uitgevoerd. Gebruik de volgende opdracht voor deze controle:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      In de uitvoer moet het **hdbdaemon** -proces worden weer gegeven met de status gestopt en zijn er geen andere Hana-processen in de actieve of gestarte toestand.
1. Bepaal op welke naam van de moment opname of SAP HANA back-upserver de nood herstel site moet worden hersteld. In het geval van echt nood herstel cases is deze moment opname doorgaans de laatste moment opname. Als u verloren gegevens wilt herstellen, kiest u een eerdere moment opname.
1. Neem contact op met de ondersteuning van Azure via een ondersteunings aanvraag met hoge prioriteit. Vraag naar het herstellen van die moment opname met de naam en datum van de moment opname of de HANA-back-upid op de DR-site. De standaard instelling is dat de bewerkingen kant alleen het/Hana/data-volume herstelt. Als u de/Hana/logbackups-volumes ook wilt hebben, moet u deze specifiek aangeven. *Herstel het/Hana/Shared-volume niet.* Kies in plaats daarvan specifieke bestanden, zoals global.ini van de map **. snap shot** en de bijbehorende submappen, nadat u het/Hana/Shared-volume voor PRD opnieuw hebt gekoppeld. 

   Aan de kant van de bewerkingen worden de volgende stappen uitgevoerd:

   a. De replicatie van moment opnamen van het productie volume naar de herstel volumes voor nood gevallen is gestopt. Deze onderbreking is mogelijk al gebeurd als een storing op de productie site de reden is dat u de nood herstel procedure moet uitvoeren.
   
   b. De naam van de opslag momentopname of de moment opname met de back-upid die u hebt gekozen, wordt hersteld op de volumes voor herstel na nood gevallen.
   
   c. Na het herstellen zijn de volumes voor herstel na nood gevallen beschikbaar om te worden gekoppeld aan de HANA-eenheden voor grote instanties in het gebied voor herstel na nood gevallen.
      
1. Koppel de herstel volumes voor nood gevallen aan de HANA-eenheid voor grote instanties in de nood herstel site. 
1. Start de niet-actieve SAP HANA productie-instantie.
1. Als u ervoor hebt gekozen om back-uplogboeken van transactie logboeken te kopiëren om de RPO-tijd te verminderen, voegt u de back-ups van het transactie logboek samen in de zojuist gekoppelde DR/Hana/logbackups-Directory Bestaande back-ups worden niet overschreven. Nieuwere back-ups kopiëren die niet zijn gerepliceerd met de laatste replicatie van een opslag momentopname.
1. U kunt ook afzonderlijke bestanden herstellen uit de moment opnamen die niet zijn gerepliceerd naar het/hana/shared/PRD-volume in de Azure-regio.

De volgende stappen laten zien hoe u het SAP HANA productie-exemplaar kunt herstellen op basis van de herstelde back-up van de opslag en de beschik bare transactie Logboeken.

1. Wijzig de back-uplocatie in **/Hana/logbackups** met behulp van SAP Hana Studio.

   ![De back-uplocatie voor herstel na nood gevallen wijzigen](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA doorzoekt de locaties van het back-upbestand en suggereert de meest recente transactie logboek back-up om te herstellen naar. Het kan een paar minuten duren voordat een scherm wordt weer gegeven, zoals in de volgende afbeelding:

   ![Lijst met back-ups van transactie logboeken voor herstel na nood gevallen](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Pas een aantal van de standaard instellingen aan:

      - Verwijder **Delta back-ups**.
      - Selecteer **logboek gebied initialiseren**.

   ![Het gebied voor het initialiseren van het logboek instellen](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Selecteer **Finish**.

   ![De herstel bewerking voor nood gevallen volt ooien](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Er moet een voortgangs venster worden weer gegeven, zoals het. Denk eraan dat het voor beeld een herstel na nood geval is van een scale-out-SAP HANA configuratie met drie knoop punten.

![Voortgang van herstellen](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Als de herstel bewerking niet meer reageert op het **eind** scherm en het scherm wordt niet weer gegeven, controleert u of alle SAP Hana exemplaren op de worker-knoop punten worden uitgevoerd. Als dat nodig is, start u de SAP HANA exemplaren hand matig.


## <a name="failback-from-a-dr-to-a-production-site"></a>Failback van een DR-naar-productie site
U kunt een failover uitvoeren van een DR naar een productie site. Laten we eens kijken naar een scenario waarin de failover naar de site voor herstel na nood gevallen is veroorzaakt door problemen in de Azure-regio voor productie en niet door de nood zaak om verloren gegevens te herstellen. 

U hebt uw SAP-productie werk belasting voor een tijdje uitgevoerd op de site voor nood herstel. Wanneer de problemen in de productie site zijn opgelost, wilt u een failback uitvoeren naar uw productie site. Omdat er geen gegevens verloren kunnen gaan, moet de stap in de productie site een aantal stappen uitvoeren en de samen werking met de SAP HANA op het Azure Operations-team sluiten. Het is aan u om het operations-team te activeren om te beginnen met het synchroniseren van de productie site nadat de problemen zijn opgelost.

Volg deze stappen:

1. De SAP HANA op het Azure Operations-Team haalt de trigger op voor het synchroniseren van de productie opslag volumes van de opslag volumes voor herstel na nood gevallen, die nu de productie status vertegenwoordigen. In deze status wordt de HANA-eenheid voor grote instanties in de productie site afgesloten.
1. Het SAP HANA van het Azure-team controleert de replicatie en zorgt ervoor dat deze wordt gedetecteerd voordat ze u op de hoogte stellen.
1. U sluit de toepassingen af die gebruikmaken van het productie HANA-exemplaar in de nood herstel site. Vervolgens voert u een HANA-back-up van het transactie logboek uit. Vervolgens stopt u de HANA-instantie die wordt uitgevoerd op de HANA grote instantie-eenheden op de site voor nood herstel.
1. Nadat het HANA-exemplaar dat wordt uitgevoerd in de HANA-eenheid voor grote instanties in de nood herstel site wordt afgesloten, synchroniseert het operations-team de schijf volumes hand matig opnieuw.
1. De SAP HANA op het Azure Operations-team start de HANA-eenheid voor grote instanties in de productie site opnieuw. Ze leveren het naar u. U moet ervoor zorgen dat het SAP HANA-exemplaar zich in de opstart modus bevindt tijdens het opstarten van de HANA-eenheid voor grote exemplaren.
1. U voert dezelfde stappen voor het herstellen van de data base uit die u eerder hebt uitgevoerd tijdens een eerdere failover naar de nood herstel site.

## <a name="monitor-disaster-recovery-replication"></a>Replicatie voor herstel na nood gevallen controleren

Voer het script uit om de status van de voortgang van de opslag replicatie te controleren `azure_hana_replication_status` . Deze opdracht moet worden uitgevoerd vanuit een eenheid die wordt uitgevoerd op de locatie voor nood herstel, zodat deze werkt zoals verwacht. De opdracht werkt niet ongeacht of de replicatie actief is. De opdracht kan worden uitgevoerd voor elke HANA grote instantie-eenheid van uw Tenant op de locatie voor nood herstel. Het kan niet worden gebruikt om details over het opstart volume op te halen. 

Zie ' Get DR Replication status-azure_hana_replication_status ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf)voor meer informatie over de opdracht en de uitvoer.


## <a name="next-steps"></a>Volgende stappen
- Zie [monitor en probleem oplossing van Hana-zijde](hana-monitor-troubleshoot.md).
