---
title: Release opmerkingen voor StorSimple 8000 Series update 2 | Microsoft Docs
description: Beschrijft de nieuwe functies, problemen en tijdelijke oplossingen voor StorSimple 8000 Series update 2.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: b68e338c2675a3aeefda390d12ab2d82ea1c7a9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94956650"
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>Release opmerkingen voor StorSimple 8000 Series update 2

## <a name="overview"></a>Overzicht
In de volgende release opmerkingen worden de nieuwe functies beschreven en worden de essentiële openstaande problemen voor de StorSimple 8000 Series update 2 geïdentificeerd. Ze bevatten ook een lijst met de StorSimple-software, stuur Programma's en firmware-updates die zijn opgenomen in deze release. 

Update 2 kan worden toegepast op elk StorSimple-apparaat waarop release (GA) of update 0,1 via update 1,2. De versie van het apparaat die is gekoppeld aan update 2 is 6.3.9600.17673.

Raadpleeg de informatie in de release opmerkingen voordat u de update in uw StorSimple-oplossing implementeert.

> [!IMPORTANT]
> * Het duurt ongeveer 4-7 uur om deze update te installeren (inclusief de Windows-updates). 
> * Update 2 bevat software, LSI driver en SSD firmware-updates.
> * Voor nieuwe releases worden updates mogelijk niet onmiddellijk weer geven omdat er een gefaseerde implementatie van de updates wordt uitgevoerd. Wacht enkele dagen en zoek opnieuw naar updates, aangezien deze binnenkort beschikbaar worden.
> 
> 

## <a name="whats-new-in-update-2"></a>Wat is er nieuw in update 2
Update 2 introduceert de volgende nieuwe functies.

* **Lokaal vastgemaakte volumes** : in eerdere releases van de StorSimple 8000-serie zijn blokken gegevens in de Cloud gelaagd op basis van het gebruik. Er is geen manier om te garanderen dat blokken op de lokale locatie blijven. Wanneer u in update 2 een volume maakt, kunt u een volume aanwijzen als lokaal vastgemaakt, en primaire gegevens van dat volume worden niet in de Cloud gelaagd. Moment opnamen van lokaal vastgemaakte volumes worden nog steeds naar de Cloud gekopieerd voor back-up, zodat de cloud kan worden gebruikt voor gegevens mobiliteit en herstel na een nood geval. Daarnaast kunt u het volume type wijzigen (dat wil zeggen, gelaagde volumes converteren naar lokaal vastgemaakte volumes en lokaal vastgemaakte volumes converteren naar gelaagd). 
* **Verbeteringen voor virtuele StorSimple-apparaten** : voorheen bevindt de StorSimple 8000-serie het virtuele apparaat als nood herstel-of ontwikkelings-en test oplossing. Er is slechts één model van het virtuele apparaat (model 1100). Met update 2 worden twee modellen voor virtuele apparaten geïntroduceerd: 
  
  * 8010 (voorheen de 1100): geen wijziging; heeft een capaciteit van 30 TB en maakt gebruik van Azure Standard-opslag.
  * 8020: heeft een capaciteit van 64 TB en maakt gebruik van Azure Premium Storage voor betere prestaties.
    
    Er is één VHD voor beide modellen voor virtuele apparaten (8010/8020). Wanneer u het virtuele apparaat voor het eerst start, detecteert het de platform parameters en past de juiste model versie toe.
* **Netwerk verbeteringen** : update 2 bevat de volgende netwerk verbeteringen:
  
  * Er kunnen meerdere Nic's worden ingeschakeld voor de Cloud, zodat failover kan optreden als een NIC mislukt.
  * Verbeteringen van de route ring, met vaste metrische gegevens voor Cloud-blokken.
  * Online opnieuw proberen van mislukte resources voor een failover.
  * Nieuwe waarschuwingen voor service fouten.
* **Verbeteringen** voor het bijwerken: In update 1,2 en eerder is de StorSimple 8000-serie bijgewerkt via twee kanalen: Windows Update voor clustering, iSCSI, enzovoort, en Microsoft Update voor binaire bestanden en firmware.
    Update 2 maakt gebruik van Microsoft Update voor alle update pakketten. Dit moet leiden tot minder tijdrovende patches of failovers uitvoeren. 
* **Firmware-updates** : de volgende firmware-updates zijn opgenomen:
  
  * LSI: lsi_sas2.sys product versie 2.00.72.10
  * Alleen SSD (geen HDD-updates): XMGG, XGEG, KZ50, F6C2 en VR08
* **Proactieve ondersteuning** – update 2 stelt micro soft in staat extra diagnostische gegevens van het apparaat op te halen. Wanneer het operationele team apparaten identificeert die problemen ondervinden, zijn we beter uitgerust met het verzamelen van informatie van het apparaat en het vaststellen van problemen. **Door update 2 te accepteren, verleent u ons de mogelijkheid deze proactieve ondersteuning te bieden**.    

## <a name="issues-fixed-in-update-2"></a>Problemen die zijn opgelost in update 2
De volgende tabellen bevatten een samen vatting van problemen die zijn opgelost in updates 2.    

| Nee. | Functie | Probleem | Is van toepassing op het fysieke apparaat | Is van toepassing op virtueel apparaat |
| --- | --- | --- | --- | --- |
| 1 |Netwerkinterfaces |Na een upgrade naar Update 1 heeft de StorSimple Manager-service gerapporteerd dat de Bestand2-en DATA3-poorten op één controller zijn mislukt. Dit probleem is opgelost. |Ja |Nee |
| 2 |Updates |Na een upgrade naar Update 1 zijn hoorbare waarschuwings waarschuwingen opgetreden in de klassieke Azure-Portal op meerdere apparaten. Dit probleem is opgelost. |Ja |Nee |
| 3 |Open stack-verificatie |Wanneer u openstack gebruikt als uw Cloud serviceprovider, kunt u een fout melding ontvangen dat de Cloud authenticatie reeks te lang is. Dit probleem is opgelost. |Ja |Nee |

## <a name="known-issues-in-update-2"></a>Bekende problemen in update 2
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
| 20 |Lokaal vastgemaakte volumes |Als u probeert een gelaagd volume te converteren (gemaakt en gekloond met Update 1,2 of eerder) naar een lokaal vastgemaakt volume en er onvoldoende ruimte is op het apparaat of als er sprake is van een Cloud storing, kunnen de kloon (s) beschadigd zijn. |Dit probleem treedt alleen op bij volumes die zijn gemaakt en gekloond met software van vóór de update 2. Dit moet een incidenteel scenario zijn. | | |
| 21 |Volume conversie |Werk het ACRs-object dat is gekoppeld aan een volume niet bij terwijl er een volume conversie wordt uitgevoerd (naar lokaal vastgemaakte of vice versa). Het bijwerken van het ACRs kan leiden tot beschadigde gegevens. |Indien nodig werkt u de ACRs bij vóór de volume conversie en maakt u geen verdere ACR-updates tijdens de conversie. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Controller-en firmware-updates in update 2
Met deze versie wordt het stuur programma en de schijf firmware op het apparaat bijgewerkt.

* Zie micro soft Knowledge Base-artikel 3121900 voor meer informatie over de LSI firmware-update. 
* Zie micro soft Knowledge Base-artikel 3121899 voor meer informatie over de update van de schijf firmware.

## <a name="virtual-device-updates-in-update-2"></a>Updates van het virtuele apparaat in update 2
Deze update kan niet worden toegepast op het virtuele apparaat. Er moeten nieuwe virtuele apparaten worden gemaakt. 

## <a name="next-step"></a>Volgende stap
Meer informatie over het [installeren van update 2](./storsimple-8000-install-update-5.md) op uw StorSimple-apparaat.