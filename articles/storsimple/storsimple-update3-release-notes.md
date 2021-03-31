---
title: Release opmerkingen bij StorSimple 8000 Series update 3
description: Hierin worden de nieuwe functies, problemen en tijdelijke oplossingen voor de StorSimple 8000 Series update 3 beschreven.
author: alkohli
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e081acc357c1b8872dd3a25c552d4cbe9785e212
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96007066"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Update 3 release opmerkingen voor uw StorSimple 8000 Series-apparaat

## <a name="overview"></a>Overzicht
In de volgende release opmerkingen worden de nieuwe functies beschreven en worden de essentiële openstaande problemen voor de StorSimple 8000 Series update 3 geïdentificeerd. Ze bevatten ook een lijst met de StorSimple software-updates die zijn opgenomen in deze release. 

Update 3 kan worden toegepast op elk StorSimple-apparaat met release (GA) of update 0,1 via update 2,2. De versie van het apparaat die is gekoppeld aan update 3 is 6.3.9600.17759.

Raadpleeg de informatie in de release opmerkingen voordat u de update in uw StorSimple-oplossing implementeert.

> [!IMPORTANT]
> * Update 3 heeft apparaatsoftware, het stuur programma en de firmware van het LSI en de updates voor Storport en Spaceport. Het duurt ongeveer 1,5 2 uur om deze update te installeren. 
> * Voor nieuwe releases worden updates mogelijk niet onmiddellijk weer geven omdat er een gefaseerde implementatie van de updates wordt uitgevoerd. Wacht enkele dagen en zoek opnieuw naar updates, aangezien deze binnenkort beschikbaar worden.
> 
> 

## <a name="whats-new-in-update-3"></a>Wat is er nieuw in update 3
De volgende belang rijke verbeteringen en oplossingen voor fouten zijn aangebracht in update 3.

* **Wijzigingen in automatische ruimte vrijmaken** : starten van update 3, de algoritmen voor het vrijmaken van ruimte worden uitgevoerd op de standby-controller van het systeem, wat resulteert in een snellere uitvoering. Raadpleeg de [StorSimple-netwerk vereisten](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device)voor meer informatie over de poorten die nodig zijn om te werken met ruimte vrijmaken.
* **Verbeteringen in prestaties** : update 3 heeft betere Lees-en schrijf prestaties naar de Cloud.
* **Verbeteringen** op het gebied van migratie: in deze release zijn verschillende oplossingen voor fouten en verbeteringen uitgevoerd voor de migratie functie van 5000/7000 serie apparaten naar 8000 Series-apparaten. Ga voor meer informatie over het gebruik van de migratie functie naar [migratie van een apparaat met een 5000/7000-serie naar een 8000-serie](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Bewaking van gerelateerde oplossingen** : in deze release zijn fouten met betrekking tot bewakings grafieken, service dashboard en dash board voor apparaten opgelost.

## <a name="issues-fixed-in-update-3"></a>Problemen die zijn opgelost in update 3
De volgende tabellen bevatten een samen vatting van problemen die zijn opgelost in update 3.    

| Nee | Functie | Probleem | Is van toepassing op het fysieke apparaat | Is van toepassing op virtueel apparaat |
| --- | --- | --- | --- | --- |
| 1 |Gegevens migratie aan de host-zijde |In de eerdere versie werd de StorSimple Cloud Appliance offline gezet tijdens de migratie van gegevens van een host. Dit probleem is opgelost in deze release. |Nee |Ja |
| 2 |Lokaal vastgemaakte volumes |In de vorige versie zijn er problemen met I/O-fouten, storingen in volume conversie en DataPath-fouten voor lokaal vastgemaakte volumes. Deze problemen bevinden zich in de hoofdmap en zijn opgelost in deze release. |Ja |Nee |
| 3 |Bewaking |Er zijn meerdere problemen met de rapportage-eenheden en bewaking, evenals de dashboard grafieken van het dash board waarin onjuiste informatie wordt weer gegeven voor lokaal vastgemaakte volumes. Deze problemen zijn opgelost in deze release. |Ja |Nee |
| 4 |Zware schrijf bewerkingen I/O |Wanneer u StorSimple gebruikt voor workloads met zware schrijf bewerkingen, wordt de gebruiker uitgevoerd in een niet-frequente bug waarbij de werkset werd getierd in de Cloud. Deze fout is opgelost in deze release. |Ja |Ja |
| 5 |Backup |In bepaalde zeldzame gevallen, in de vorige versies van software, wanneer de gebruiker een back-up van een externe kloon heeft gemaakt, zouden ze in de Cloud fouten zouden uitvoeren en zou de bewerking fout zouden veroorzaken. In deze release is het probleem opgelost en de bewerking is voltooid. |Ja |Ja |
| 6 |Back-upbeleid |In bepaalde zeldzame gevallen is er een fout opgetreden bij het verwijderen van het back-upbeleid in de eerdere versies van software. Dit probleem is opgelost in deze release. |Ja |Ja |

## <a name="known-issues-in-update-3"></a>Bekende problemen in update 3
De volgende tabel bevat een samen vatting van bekende problemen in deze release.

| Nee. | Functie | Probleem | Opmerkingen/tijdelijke oplossing | Is van toepassing op het fysieke apparaat | Is van toepassing op virtueel apparaat |
| --- | --- | --- | --- | --- | --- |
| 1 |Schijf quorum |Als het meren deel van de schijven in de EBOD Enclosure van een 8600-apparaat niet is verbonden met een niet-schijf quorum, wordt de opslag groep in zeldzame gevallen offline gezet. Het blijft offline, zelfs als de schijven opnieuw zijn verbonden. |U moet het apparaat opnieuw opstarten. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning om de volgende stappen uit te voeren. |Ja |Nee |
| 2 |Onjuiste controller-ID |Wanneer een controller wordt vervangen, kan controller 0 als controller 1 worden weer gegeven. Wanneer de installatie kopie wordt geladen vanuit het knoop punt van de peer, kan de controller-ID in eerste instantie als de ID van de peer-controller worden weer gegeven tijdens het vervangen van de controller. Dit gedrag kan in zeldzame gevallen ook worden gezien nadat het systeem opnieuw is opgestart. |Er is geen gebruikers actie vereist. Deze situatie wordt vanzelf opgelost nadat de vervanging van de controller is voltooid. |Ja |Nee |
| 3 |Opslagaccounts |Het gebruik van de opslag service om het opslag account te verwijderen, is een niet-ondersteund scenario. Dit leidt tot een situatie waarin gebruikers gegevens niet kunnen worden opgehaald. | |Ja |Ja |
| 4 |Failover van apparaat |Meerdere failovers van een volume container van hetzelfde bron apparaat naar verschillende doel apparaten worden niet ondersteund. Failover van één Dead-apparaat naar meerdere apparaten zorgt ervoor dat de volume containers op het eerste mislukte apparaat gegevens eigendom verloren gaan. Na een dergelijke failover worden deze volume containers weer gegeven of werken ze anders wanneer u ze in de klassieke Azure-Portal bekijkt. | |Ja |Nee |
| 5 |Installatie |Tijdens de installatie van de StorSimple-adapter voor share point moet u een IP-adres van het apparaat opgeven, zodat de installatie kan worden voltooid. | |Ja |Nee |
| 6 |Webproxy |Als uw webproxy-configuratie HTTPS heeft als het opgegeven protocol, wordt de communicatie van uw apparaat naar service beïnvloed en wordt het apparaat offline gezet. Er worden ook ondersteunings pakketten gegenereerd in het proces, waarbij aanzienlijke bronnen op uw apparaat worden gebruikt. |Zorg ervoor dat de web proxy-URL HTTP heeft als het opgegeven protocol. Zie [Configure web proxy for your device](storsimple-8000-configure-web-proxy.md) (Webproxy voor uw apparaat configureren) voor meer informatie. |Ja |Nee |
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
| 22 |Updates |Wanneer u update 3 toepast, wordt op de pagina **onderhoud** in de klassieke Azure-Portal het volgende bericht weer gegeven met betrekking tot update 2-"StorSimple 8000 Series update 2 bevat de mogelijkheid van micro soft om proactief logboek gegevens van uw apparaat te verzamelen wanneer er mogelijke problemen worden gedetecteerd. Dit is misleidend, omdat het aangeeft dat het apparaat wordt bijgewerkt naar Update 2. Zodra het apparaat is bijgewerkt naar Update 3, verdwijnt dit bericht. |Dit gedrag wordt in een toekomstige release opgelost. |Ja |Nee |

## <a name="controller-and-firmware-updates-in-update-3"></a>Controller-en firmware-updates in update 3
Deze release heeft stuur programma-en firmware-updates voor LSI. Zie [Update 3 installeren](./index.yml) op uw StorSimple-apparaat voor meer informatie over het installeren van het LSI-stuur programma en de firmware-updates.

## <a name="virtual-device-updates-in-update-3"></a>Updates van het virtuele apparaat in update 3
Deze update kan niet worden toegepast op de StorSimple Cloud Appliance (ook wel bekend als het virtuele apparaat). Er moeten nieuwe virtuele apparaten worden gemaakt. 

## <a name="next-step"></a>Volgende stap
Meer informatie over het [installeren van update 3](./index.yml) op uw StorSimple-apparaat.