---
title: StorSimple 8000 Series update 1,2 release opmerkingen | Microsoft Docs
description: Hierin worden de nieuwe functies, problemen en tijdelijke oplossingen voor de StorSimple 8000 Series update 1,2 beschreven.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6c9aae87-6f77-44b8-b7fa-ebbdc9d8517c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3a5ba4b27ae19999237edb75c50db36ef57a10f1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94956684"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Update 1,2 release opmerkingen voor uw StorSimple 8000 Series-apparaat

## <a name="overview"></a>Overzicht
In de volgende release opmerkingen worden de nieuwe functies beschreven en worden de essentiële open problemen voor de StorSimple 8000 Series update 1,2 geïdentificeerd. Ze bevatten ook een lijst met de StorSimple-software, stuur programma-en schijf firmware-updates die zijn opgenomen in deze release. 

Update 1,2 kan worden toegepast op alle StorSimple-apparaten met release (GA), update 0,1, update 0,2 of update 0,3 software. Update 1,2 is niet beschikbaar als op uw apparaat update 1 of update 1,1 wordt uitgevoerd. Als op uw apparaat release (GA) wordt uitgevoerd, [neemt u contact op met Microsoft ondersteuning](./storsimple-8000-contact-microsoft-support.md) om u te helpen bij het installeren van deze update.

De volgende tabel bevat de software versies van het apparaat die overeenkomen met de updates 1, 1,1 en 1,2.

| Als update uitvoeren... | Dit is de versie van de apparaatsoftware. |
| --- | --- |
| Update 1.2 |6.3.9600.17584 |
| Update 1.1 |6.3.9600.17521 |
| Update 1,0 |6.3.9600.17491 |

Raadpleeg de informatie in de release opmerkingen voordat u de update in uw StorSimple-oplossing implementeert. Zie [Update 1,2 op uw StorSimple-apparaat installeren](./storsimple-8000-install-update-5.md)voor meer informatie. 

> [!IMPORTANT]
> * Het duurt ongeveer 5-10 uur om deze update te installeren (inclusief de Windows-updates). 
> * Update 1,2 heeft updates voor software, LSI Stuur Programma's en de firmware van de schijf. Volg de instructies in [Update 1,2 op uw StorSimple-apparaat installeren](./storsimple-8000-install-update-5.md)om de installatie uit te voeren.
> * Voor nieuwe releases worden updates mogelijk niet onmiddellijk weer geven omdat er een gefaseerde implementatie van de updates wordt uitgevoerd. Scan de updates over een paar dagen opnieuw, aangezien deze binnenkort beschikbaar worden.
> 
> 

## <a name="whats-new-in-update-12"></a>Wat is er nieuw in update 1,2
Deze functies werden voor het eerst uitgebracht met Update 1 die beschikbaar is gesteld aan een beperkt aantal gebruikers. Met de update 1,2-release zien de meeste StorSimple-gebruikers de volgende nieuwe functies en verbeteringen:

* **Migratie van 5000-7000-serie naar 8000-serie apparaten** : deze release introduceert een nieuwe migratie functie waarmee de gebruikers van de StorSimple 5000-7000-serie hun gegevens kunnen migreren naar een fysiek 8000 StorSimple-serie apparaat of een virtueel apparaat. De functie migratie heeft twee belang rijke voor waarden:                                                                  
  
  * **Bedrijfs continuïteit**, door migratie van bestaande gegevens op apparaten uit de 5000-7000-serie naar apparaten uit de 8000-serie in te scha kelen.
  * **Verbeterd onderdeel aanbod van de 8000-serie toestellen**, zoals een efficiënt gecentraliseerd beheer van meerdere apparaten via StorSimple Manager service, een betere klasse van hardware en bijgewerkte firmware, virtuele apparaten, gegevens mobiliteit en functies in het toekomstige plan.
    
    Raadpleeg de [migratie handleiding](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) voor meer informatie over het migreren van een StorSimple 5000-7000-serie naar een 8000 Series-apparaat. 
* **Beschik baarheid in de Azure Government Portal** – StorSimple is nu beschikbaar in de Azure Government Portal. Bekijk hoe u [een StorSimple-apparaat implementeert in de Azure Government Portal](./index.yml).
* **Ondersteuning voor andere Cloud** serviceproviders: de andere Cloud serviceproviders die worden ondersteund, zijn Amazon S3, Amazon S3 met RR'S, HP en open stack (bèta).
* **Bijwerken naar de nieuwste opslag-api's** : met deze versie is StorSimple bijgewerkt naar de nieuwste Azure Storage service-api's. StorSimple 8000-serie apparaten met vooraf-update 1-software versies (release, 0,1, 0,2 en 0,3) maken gebruik van versies van de Azure Storage service-Api's ouder dan 17 juli 2009. Zoals vermeld in de bijgewerkte [aankondiging over het verwijderen van de versies van de opslag service](/archive/blogs/windowsazurestorage/microsoft-azure-storage-service-version-removal-update-extension-to-2016), wordt de api's door 1 augustus 2016 afgeschaft. Het is van belang dat u de StorSimple 8000 Series update 1 toepast vóór 1 augustus 2016. Als u dit niet doet, werken StorSimple-apparaten niet meer correct.
* **Ondersteuning voor zone redundant Storage (ZRS)** : met de upgrade naar de nieuwste versie van de opslag-api's, biedt de StorSimple 8000-serie ondersteuning voor zone redundante opslag (ZRS) naast lokaal redundante opslag (LRS) en geografisch redundante opslag (GRS). Raadpleeg dit [artikel over Azure Storage redundantie opties](../storage/common/storage-redundancy.md) voor ZRS-gegevens.
* **Verbeterde eerste implementatie-en update-ervaring** – in deze release zijn de installatie-en update processen verbeterd. De installatie via de wizard Setup is verbeterd om feedback te geven aan de gebruiker als de netwerk configuratie en Firewall instellingen onjuist zijn. Er zijn aanvullende diagnostische cmdlets gegeven om u te helpen bij het oplossen van problemen met netwerken van het apparaat. Zie het [artikel Troubleshooting Deployment](./storsimple-8000-troubleshoot-deployment.md) (Engelstalig) voor meer informatie over de nieuwe diagnostische cmdlets die worden gebruikt voor het oplossen van problemen.

## <a name="issues-fixed-in-update-12"></a>Problemen die zijn opgelost in update 1,2
De volgende tabel bevat een overzicht van problemen die zijn opgelost in updates 1,2, 1,1 en 1.    

| Nee. | Functie | Probleem | Opgelost in update | Is van toepassing op het fysieke apparaat | Is van toepassing op virtueel apparaat |
| --- | --- | --- | --- | --- | --- |
| 1 |Windows PowerShell voor StorSimple |Wanneer een gebruiker op afstand toegang heeft gekregen tot het StorSimple-apparaat via Windows PowerShell voor StorSimple en vervolgens de installatie wizard startte, is er een crash opgetreden zodra het IP-adres van de gegevens 0 is ingevoerd. Deze bug is nu opgelost in update 1. |Update 1 |Ja |Ja |
| 2 |Fabrieksinstellingen terugzetten |In sommige gevallen is het StorSimple-apparaat vastgelopen en wordt het volgende bericht weer gegeven wanneer u de fabrieks instellingen herstelt: de **fabrieks instellingen worden opnieuw ingesteld (fase 8)**. Dit is gebeurd als u op CTRL + C drukt terwijl de cmdlet werd uitgevoerd. Deze bug is nu opgelost. |Update 1 |Ja |Nee |
| 3 |Fabrieksinstellingen terugzetten |Nadat een mislukte Dual controller-fabriek opnieuw is ingesteld, mag u door gaan met apparaatregistratie. Dit heeft geleid tot een niet-ondersteunde systeem configuratie. In update 1 wordt een fout bericht weer gegeven en wordt de registratie geblokkeerd op een apparaat waarvan de fabrieks instellingen zijn mislukt. |Update 1 |Ja |Nee |
| 4 |Fabrieksinstellingen terugzetten |In sommige gevallen zijn onjuiste waarschuwingen gemeld die niet overeenkomen. Er worden geen fouten met een onjuiste overeenkomst gegenereerd op apparaten waarop update 1 wordt uitgevoerd. |Update 1 |Ja |Nee |
| 5 |Fabrieksinstellingen terugzetten |Als de fabrieks instellingen zijn onderbroken voordat de bewerking is voltooid, heeft het apparaat de herstel modus geactiveerd en hebt u geen toegang meer tot Windows PowerShell voor StorSimple. Deze bug is nu opgelost. |Update 1 |Ja |Nee |
| 6 |Herstel na noodgeval |Er is een oplossing voor herstel na nood gevallen (DR) hersteld waarbij DR zou mislukken tijdens de detectie van back-ups op het doel apparaat. |Update 1 |Ja |Ja |
| 7 |Bewakings-Led's |In bepaalde gevallen heeft het controleren van Led's op de achterkant van het apparaat niet de juiste status. De blauwe LED is uitgeschakeld. DATA 0 en DATA 1-Led's zijn zelfs knipperend wanneer deze interfaces niet zijn geconfigureerd. Het probleem is opgelost en de controle van Led's geeft nu de juiste status. |Update 1 |Ja |Nee |
| 8 |Bewakings-Led's |In bepaalde gevallen, na het Toep assen van update 1, is het blauwe lampje op de actieve controller uitgeschakeld, waardoor het moeilijk is om de actieve controller te identificeren. Dit probleem is opgelost in deze patch release. |Update 1.2 |Ja |Nee |
| 9 |Netwerkinterfaces |In vorige versies kan een StorSimple-apparaat dat is geconfigureerd met een niet-Routeer bare gateway, offline gaan. In deze release is de routerings metriek voor data 0 de laagste waarde. Daarom, zelfs als andere netwerk interfaces in de Cloud zijn ingeschakeld, wordt alle Cloud verkeer van het apparaat via gegevens 0 doorgestuurd. |Update 1 |Ja |Ja |
| 10 |Back-ups |Er is een fout opgetreden in update 1 waardoor back-ups mislukken nadat 24 dagen zijn opgelost in de patch release update 1,1. |Update 1.1 |Ja |Ja |
| 11 |Back-ups |Een bug in vorige versies resulteerde in slechte prestaties voor Cloud momentopnamen met lage wijzigings tarieven. Deze fout is opgelost in deze patch release. |Update 1.2 |Ja |Ja |
| 12 |Updates |Een fout in update 1 die een mislukte upgrade heeft gerapporteerd en de controllers heeft geleid tot de herstel modus, is opgelost in deze patch release. |Update 1.2 |Ja |Ja |

## <a name="known-issues-in-update-12"></a>Bekende problemen in update 1,2
De volgende tabel bevat een samen vatting van bekende problemen in deze release.

| Nee. | Functie | Probleem | Opmerkingen/tijdelijke oplossing | Is van toepassing op het fysieke apparaat | Is van toepassing op virtueel apparaat |
| --- | --- | --- | --- | --- | --- |
| 1 |Schijf quorum |Als het meren deel van de schijven in de EBOD Enclosure van een 8600-apparaat niet is verbonden met een niet-schijf quorum, wordt de opslag groep in zeldzame gevallen offline gezet. Het blijft offline, zelfs als de schijven opnieuw zijn verbonden. |U moet het apparaat opnieuw opstarten. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning om de volgende stappen uit te voeren. |Ja |Nee |
| 2 |Onjuiste controller-ID |Wanneer een controller wordt vervangen, kan controller 0 als controller 1 worden weer gegeven. Wanneer de installatie kopie wordt geladen vanuit het knoop punt van de peer, kan de controller-ID in eerste instantie als de ID van de peer-controller worden weer gegeven tijdens het vervangen van de controller. Dit gedrag kan in zeldzame gevallen ook worden gezien nadat het systeem opnieuw is opgestart. |Er is geen gebruikers actie vereist. Deze situatie wordt vanzelf opgelost nadat de vervanging van de controller is voltooid. |Ja |Nee |
| 3 |Opslagaccounts |Het gebruik van de opslag service om het opslag account te verwijderen, is een niet-ondersteund scenario. Dit leidt tot een situatie waarin gebruikers gegevens niet kunnen worden opgehaald. |Ja |Ja | |
| 4 |Failover van apparaat |Meerdere failovers van een volume container van hetzelfde bron apparaat naar verschillende doel apparaten worden niet ondersteund. Failover van het apparaat vanaf één dood apparaat op meerdere apparaten zorgt ervoor dat de volume containers op het eerste mislukte apparaat gegevens eigendom verloren gaan. Na een dergelijke failover worden deze volume containers weer gegeven of werken ze anders wanneer u ze in de klassieke Azure-Portal bekijkt. | |Ja |Nee |
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

## <a name="physical-device-updates-in-update-12"></a>Updates van het fysieke apparaat in update 1,2
Als patch update 1,2 wordt toegepast op een fysiek apparaat (actieve versies voorafgaand aan update 1), wordt de software versie gewijzigd in 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Controller-en firmware-updates in update 1,2
Met deze versie wordt het stuur programma en de schijf firmware op het apparaat bijgewerkt.

* Zie [Update 1 voor LSI SAS-controllers in Microsoft Azure StorSimple apparaat](https://support.microsoft.com/kb/3043005)voor meer informatie over de update van de SAS-controller. 
* Zie voor meer informatie over de update van de schijf firmware [schijf firmware update 1 voor Microsoft Azure StorSimple apparaat](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>Updates van het virtuele apparaat in update 1,2
Deze update kan niet worden toegepast op het virtuele apparaat. Er moeten nieuwe virtuele apparaten worden gemaakt. 

## <a name="next-steps"></a>Volgende stappen
* [Installeer Update 1,2 op het apparaat](./storsimple-8000-install-update-5.md).