---
title: StorSimple 8000 Series update 2,2 release opmerkingen | Microsoft Docs
description: Hierin worden de nieuwe functies, problemen en tijdelijke oplossingen voor de StorSimple 8000 Series update 2,2 beschreven.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 5cf03ea8-2a0f-4552-b6dc-7ea517783d7b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 73b9ecd03875b60ed2d9b9d4c8e8a3a0c8de3cfa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94956599"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>Release opmerkingen bij StorSimple 8000 Series update 2,2

## <a name="overview"></a>Overzicht
In de volgende release opmerkingen worden de nieuwe functies beschreven en worden de essentiële open problemen voor de StorSimple 8000 Series update 2,2 geïdentificeerd. Ze bevatten ook een lijst met de StorSimple software-updates die zijn opgenomen in deze release.

Update 2,2 kan worden toegepast op alle StorSimple-apparaten met release (GA) of update 0,1 via update 2,1. De versie van het apparaat die is gekoppeld aan update 2,2 is 6.3.9600.17708.

Raadpleeg de informatie in de release opmerkingen voordat u de update in uw StorSimple-oplossing implementeert.

> [!IMPORTANT]
> * Update 2,2 heeft alleen software-updates. Het duurt ongeveer 1,5 2 uur om deze update te installeren. 
> * Als u update 2,1 uitvoert, raden we u aan om update 2,2 zo snel mogelijk toe te passen.
> * Voor nieuwe releases worden updates mogelijk niet onmiddellijk weer geven omdat er een gefaseerde implementatie van de updates wordt uitgevoerd. Wacht enkele dagen en zoek opnieuw naar updates, aangezien deze binnenkort beschikbaar worden.
> 
> 

## <a name="whats-new-in-update-22"></a>Wat is er nieuw in update 2,2
De volgende belang rijke verbeteringen zijn aangebracht in update 2,2.

* **Optimalisatie van automatische ruimte winning** : wanneer gegevens worden verwijderd op thin provisioned volumes, moeten de ongebruikte opslag blokken worden vrijgemaakt. Met deze versie is het proces voor het vrijmaken van ruimte in de Cloud verbeterd, waardoor de ongebruikte ruimte sneller beschikbaar is in vergelijking met de vorige versies.
* Verbeteringen in de prestaties van de **moment opname** : update 2,2 heeft de tijd verbeterd voor het verwerken van een Cloud momentopname in bepaalde scenario's waarbij grote volumes worden gebruikt en er mini maal geen gegevens verloop zijn. Een scenario dat van deze uitbrei ding zou kunnen profiteren, zijn de archief volumes.
* Het beveiligen **van het pakket met ondersteunings pakketten** : er zijn verbeteringen aangebracht in de manier waarop het ondersteunings pakket is verzameld en geüpload in deze release. 
* **Verbeteringen in de betrouw baarheid bijwerken** : deze release bevat oplossingen voor fouten die resulteren in een verbeterde betrouw baarheid van de update.

## <a name="issues-fixed-in-update-22"></a>Problemen die zijn opgelost in update 2,2
De volgende tabellen bevatten een samen vatting van problemen die zijn opgelost in update 2,2 en 2,1.    

| Nee | Functie | Probleem | Is van toepassing op het fysieke apparaat | Is van toepassing op virtueel apparaat |
| --- | --- | --- | --- | --- |
| 1 |Prestaties van host |In de eerdere versie werden problemen met de prestaties van de host waargenomen tijdens het maken van een lokaal vastgemaakt volume en tijdens de conversie van een gelaagd volume naar een lokaal vastgemaakt volume. Deze problemen worden opgelost in deze release, waardoor de prestaties van de host worden verbeterd tijdens het maken en omzetten van volumes. |Ja |Nee |
| 2 |Lokaal vastgemaakte volumes |In zeldzame gevallen loopt het systeem vast bij het maken van een lokaal vastgemaakt volume. Deze fout is opgelost in deze release. |Ja |Nee |
| 3 |Lagen |Er zijn sporadische Crashes wanneer de meta gegevens voor de StorSimple-Cloud apparaten (8010 en 8020) naar de Cloud zijn gelaagd. Dit probleem is opgelost in deze release. |Nee |Ja |
| 4 |Maken van momentopnamen |Er zijn problemen met het maken van incrementele moment opnamen in scenario's met grote volumes en minimale waarde voor geen gegevens verloop. Deze problemen zijn opgelost in deze release. |Ja |Ja |
| 5 |Open stack-verificatie |Wanneer u openstack gebruikt als Cloud serviceprovider, wordt de gebruiker uitgevoerd in een niet-frequente fout die betrekking heeft op de authenticatie waarbij de JSON-parser heeft geresulteerd in een crash. Deze fout is opgelost in deze release. |Ja |Nee |
| 6 |Kopie aan de host zijde |In eerdere versies van software werd een niet-frequente fout met betrekking tot de timing van de ODX ontdekt bij het kopiëren van de gegevens van het ene volume naar een ander volume. Dit resulteert in een failover van een controller en het systeem kan mogelijk de herstel modus activeren. Deze fout is opgelost in deze release. |Ja |Nee |
| 7 |Windows Management Instrumentation (WMI) |In de vorige versies van software waren er meerdere exemplaren van een webproxy fout met de uitzonde ring ' fout bij het laden van de \<ManagementException> provider '. Deze fout is geschreven naar een WMI-geheugenlek en is nu opgelost. |Ja |Nee |
| 8 |Bijwerken |In bepaalde zeldzame gevallen heeft de gebruiker in de vorige versies van software een "CisPowershellHcsscripterror" ontvangen tijdens het scannen of installeren van updates. Dit probleem is opgelost in deze release. |Ja |Ja |
| 9 |Ondersteunings pakket |In deze release zijn er verbeteringen aangebracht in de manier waarop het ondersteunings pakket is verzameld en geüpload. |Ja |Ja |

## <a name="known-issues-in-update-22"></a>Bekende problemen in update 2,2
De volgende tabel bevat een samen vatting van bekende problemen in deze release.

| Nee. | Functie | Probleem | Opmerkingen/tijdelijke oplossing | Is van toepassing op het fysieke apparaat | Is van toepassing op virtueel apparaat |
| --- | --- | --- | --- | --- | --- |
| 1 |Schijf quorum |Als het meren deel van de schijven in de EBOD Enclosure van een 8600-apparaat niet is verbonden met een niet-schijf quorum, wordt de opslag groep in zeldzame gevallen offline gezet. Het blijft offline, zelfs als de schijven opnieuw zijn verbonden. |U moet het apparaat opnieuw opstarten. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning om de volgende stappen uit te voeren. |Ja |Nee |
| 2 |Onjuiste controller-ID |Wanneer een controller wordt vervangen, kan controller 0 als controller 1 worden weer gegeven. Wanneer de installatie kopie wordt geladen vanuit het knoop punt van de peer, kan de controller-ID in eerste instantie als de ID van de peer-controller worden weer gegeven tijdens het vervangen van de controller. Dit gedrag kan in zeldzame gevallen ook worden gezien nadat het systeem opnieuw is opgestart. |Er is geen gebruikers actie vereist. Deze situatie wordt vanzelf opgelost nadat de vervanging van de controller is voltooid. |Ja |Nee |
| 3 |Opslagaccounts |Het gebruik van de opslag service om het opslag account te verwijderen, is een niet-ondersteund scenario. Dit leidt tot een situatie waarin gebruikers gegevens niet kunnen worden opgehaald. | |Ja |Ja |
| 4 |Failover van apparaat |Meerdere failovers van een volume container van hetzelfde bron apparaat naar verschillende doel apparaten worden niet ondersteund. Failover van één Dead-apparaat naar meerdere apparaten zorgt ervoor dat de volume containers op het eerste mislukte apparaat gegevens eigendom verloren gaan. Na een dergelijke failover worden deze volume containers weer gegeven of werken ze anders wanneer u ze in de klassieke Azure-Portal bekijkt. | |Ja |Nee |
| 5 |Installatie |Tijdens de installatie van de StorSimple-adapter voor share point moet u een IP-adres van het apparaat opgeven, zodat de installatie kan worden voltooid. | |Ja |Nee |
| 6 |Webproxy |Als uw webproxy-configuratie HTTPS heeft als het opgegeven protocol, wordt de communicatie van uw apparaat naar service beïnvloed en wordt het apparaat offline gezet. Er worden ook ondersteunings pakketten gegenereerd in het proces, waarbij aanzienlijke bronnen op uw apparaat worden gebruikt. |Zorg ervoor dat de web proxy-URL HTTP heeft als het opgegeven protocol. Zie [Configure web proxy for your device](./storsimple-8000-configure-web-proxy.md) (Webproxy voor uw apparaat configureren) voor meer informatie. |Ja |Nee |
| 7 |Webproxy |Als u webproxy configureert en inschakelt op een geregistreerd apparaat, moet u de actieve controller opnieuw opstarten op het apparaat. | |Ja |Nee |
| 8 |Hoge Cloud latentie en hoge I/O-werk belasting |Wanneer uw StorSimple-apparaat een combi natie van zeer hoge Cloud latenties (volg orde van seconden) en een hoge I/O-werk belasting tegen komt, worden de volumes van het apparaat gedegradeerd en kan het I/O's mislukken met de fout ' apparaat is niet gereed '. |U moet de controllers van het apparaat hand matig opnieuw opstarten of een failover voor het apparaat uitvoeren om deze situatie te herstellen. |Ja |Nee |
| 9 |Azure PowerShell |Wanneer u de StorSimple-cmdlet **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object-eerste 1-wacht** met het selecteren van het eerste object, zodat u een nieuw **upopslagaccount** -object kunt maken, retourneert de cmdlet alle objecten. |Wikkel de cmdlet tussen haakjes als volgt: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object-eerste 1-wacht** |Ja |Ja |
| 10 |Migratie |Wanneer meerdere volume containers worden door gegeven voor migratie, is de ETA voor de meest recente back-up alleen nauw keurig voor de eerste volume container. Daarnaast wordt parallelle migratie gestart nadat de eerste vier back-ups in de eerste volume container zijn gemigreerd. |U wordt aangeraden één volume container tegelijk te migreren. |Ja |Nee |
| 11 |Migratie |Na het terugzetten worden er geen volumes meer toegevoegd aan het back-upbeleid of de virtuele-schijf groep. |Als u back-ups wilt maken, moet u deze volumes aan een back-upbeleid toevoegen. |Ja |Ja |
| 12 |Migratie |Nadat de migratie is voltooid, mag het apparaat van de 5000/7000-serie geen toegang hebben tot de gemigreerde gegevens containers. |U wordt aangeraden de gemigreerde gegevens containers te verwijderen nadat de migratie is voltooid en doorgevoerd. |Ja |Nee |
| 13 |Klonen en DR |Een StorSimple-apparaat met Update 1 kan geen nood herstel klonen of uitvoeren op een apparaat waarop de software van vóór update 1 wordt uitgevoerd. |U moet het doel apparaat bijwerken naar Update 1 om deze bewerkingen toe te staan |Ja |Ja |
| 14 |Migratie |Configuratie back-up voor migratie mislukt mogelijk op een 5000-7000 series-apparaat wanneer er volume groepen zonder gekoppelde volumes zijn. |Verwijder alle lege volume groepen zonder gekoppelde volumes en voer de configuratie back-up opnieuw uit. |Ja |Nee |
| 15 |Azure PowerShell-cmdlets en lokaal vastgemaakte volumes |U kunt geen lokaal vastgemaakt volume maken via Azure PowerShell-cmdlets. (Elk volume dat u maakt via Azure PowerShell wordt getierd.) |Gebruik altijd de StorSimple Manager-service voor het configureren van lokaal vastgemaakte volumes. |Ja |Nee |
| 16 |Beschik bare ruimte voor lokaal vastgemaakte volumes |Als u een lokaal vastgemaakt volume verwijdert, wordt de beschik bare ruimte voor nieuwe volumes mogelijk niet onmiddellijk bijgewerkt. De StorSimple Manager-service werkt de lokale ruimte die ongeveer elk uur beschikbaar is, bij. |Wacht een uur voordat u het nieuwe volume probeert te maken. |Ja |Nee |
| 17 |Lokaal vastgemaakte volumes |Met de herstel taak wordt de tijdelijke back-up van de moment opname in de back-upcatalogus weer gegeven, maar alleen voor de duur van de herstel taak. Daarnaast wordt er een virtuele schijf groep met prefix **tmpCollection** beschikbaar gemaakt op de pagina **back-upbeleiden** , maar alleen voor de duur van de herstel taak. |Dit gedrag kan optreden als uw herstel taak alleen lokaal vastgemaakte volumes of een combi natie van lokaal vastgemaakte en gelaagde volumes heeft. Als de herstel taak alleen gelaagde volumes bevat, wordt dit gedrag niet uitgevoerd. Er is geen tussenkomst van de gebruiker is vereist. |Ja |Nee |
| 18 |Lokaal vastgemaakte volumes |Als u een herstel taak annuleert en de failover van de controller onmiddellijk daarna plaatsvindt, wordt de herstel **taak weer gegeven** in plaats van **geannuleerd**. Als een herstel taak mislukt en een failover van een controller onmiddellijk wordt uitgevoerd, worden in de herstel taak **geannuleerd** weer gegeven in plaats van **mislukt**. |Dit gedrag kan optreden als uw herstel taak alleen lokaal vastgemaakte volumes of een combi natie van lokaal vastgemaakte en gelaagde volumes heeft. Als de herstel taak alleen gelaagde volumes bevat, wordt dit gedrag niet uitgevoerd. Er is geen tussenkomst van de gebruiker is vereist. |Ja |Nee |
| 19 |Lokaal vastgemaakte volumes |Als u een herstel taak annuleert of als een herstel mislukt en vervolgens een failover van een controller plaatsvindt, wordt er een extra herstel taak weer gegeven op de pagina **taken** . |Dit gedrag kan optreden als uw herstel taak alleen lokaal vastgemaakte volumes of een combi natie van lokaal vastgemaakte en gelaagde volumes heeft. Als de herstel taak alleen gelaagde volumes bevat, wordt dit gedrag niet uitgevoerd. Er is geen tussenkomst van de gebruiker is vereist. |Ja |Nee |
| 20 |Lokaal vastgemaakte volumes |Als u probeert een gelaagd volume te converteren (gemaakt en gekloond met Update 1,2 of eerder) naar een lokaal vastgemaakt volume en er onvoldoende ruimte is op het apparaat of als er sprake is van een Cloud storing, kunnen de kloon (s) beschadigd zijn. |Dit probleem treedt alleen op bij volumes die zijn gemaakt en gekloond met software van vóór de update 2,1. Dit moet een incidenteel scenario zijn. | | |
| 21 |Volume conversie |Werk het ACRs-object dat is gekoppeld aan een volume niet bij terwijl er een volume conversie wordt uitgevoerd (naar lokaal vastgemaakte of vice versa). Het bijwerken van het ACRs kan leiden tot beschadigde gegevens. |Indien nodig werkt u de ACRs bij vóór de volume conversie en maakt u geen verdere ACR-updates tijdens de conversie. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Controller-en firmware-updates in update 2,2
Deze release heeft alleen software-updates. Als u echter een versie van vóór update 2 bijwerkt, moet u de firmware-updates voor het stuur programma, Storport, Spaceport en (in sommige gevallen) installeren op het apparaat.

Zie [Update 2,2](./storsimple-8000-install-update-5.md) op uw StorSimple-apparaat installeren voor meer informatie over het installeren van de updates voor het stuur programma, Storport, Spaceport en de schijf firmware.

## <a name="virtual-device-updates-in-update-22"></a>Updates van het virtuele apparaat in update 2,2
Deze update kan niet worden toegepast op het virtuele apparaat. Er moeten nieuwe virtuele apparaten worden gemaakt. 

## <a name="next-step"></a>Volgende stap
Meer informatie over het [installeren van Update 2,2](./storsimple-8000-install-update-5.md) op uw StorSimple-apparaat.