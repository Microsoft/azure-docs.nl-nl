---
title: Veelgestelde vragen over lokaal vastgemaakte volumes voor StorSimple | Microsoft Docs
description: Biedt antwoorden op veelgestelde vragen over lokaal vastgemaakte StorSimple-volumes.
services: storsimple
documentationcenter: NA
author: manuaery
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2017
ms.author: manuaery
ms.openlocfilehash: 483fa81b409e1bd740af85b431a86b6c814831e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002711"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>Lokaal vastgemaakte volumes StorSimple: veelgestelde vragen (FAQ)
## <a name="overview"></a>Overzicht
Hieronder vindt u vragen en antwoorden die u mogelijk hebt wanneer u een lokaal vastgemaakte StorSimple-volume maakt, een gelaagd volume converteert naar een lokaal vastgemaakt volume (en omgekeerd), of een back-up maakt en een lokaal vastgemaakt volume herstelt.

Vragen en antwoorden zijn ingedeeld in de volgende categorieën:

* Een lokaal vastgemaakt volume maken
* Back-ups maken van lokaal vastgemaakt
* Een gelaagd volume converteren naar een lokaal vastgemaakt volume
* Een lokaal vastgemaakt volume herstellen
* Failover van een lokaal vastgemaakt volume

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Vragen over het maken van een lokaal vastgemaakt volume
**Nils.** Wat is de maximale grootte van een lokaal vastgemaakt volume dat ik op de 8000-serie apparaten kan maken?

**A** op apparaten met StorSimple 8000 Series update 3,0 kunt u lokaal vastgemaakte volumes inrichten tot maxi maal 8,5 TB of gelaagde volumes tot 200 TB op het apparaat 8100. Op een groter 8600-apparaat kunt u lokaal vastgemaakte volumes inrichten tot 22,5 TB en gelaagde volumes tot maximaal 500 TB.

**Nils.** Ik heb onlangs een upgrade uitgevoerd van mijn 8100-apparaat om 3,0 bij te werken en wanneer ik een lokaal vastgemaakt volume probeer te maken, is de Maxi maal beschik bare grootte slechts 6 TB en niet 8,5 TB. Waarom kan ik geen volume van 8,5 TB maken?

**Als op** uw apparaat update 3,0 wordt uitgevoerd, kunt u lokaal vastgemaakte volumes inrichten tot 8,5 TB of gelaagde volumes tot 200 TB op het 8100-apparaat. Als uw apparaat al gelaagde volumes heeft, wordt de beschik bare ruimte voor het maken van een lokaal vastgemaakt volume proportioneel lager dan deze maximum limiet. Als ongeveer 106 TB gelaagde volumes bijvoorbeeld al zijn ingericht op uw 8100-apparaat (dat de helft van de gelaagde capaciteit is), wordt de maximum grootte van een lokaal volume dat u op het 8100-apparaat kunt maken, dienovereenkomstig verminderd tot 4 TB (ongeveer de helft van de lokaal vastgemaakte volume capaciteit).

Omdat sommige lokale ruimte op het apparaat wordt gebruikt om de werkset van gelaagde volumes te hosten, wordt de beschik bare ruimte voor het maken van een lokaal vastgemaakt volume kleiner als het apparaat gelaagde volumes heeft. Als u daarentegen een lokaal vastgemaakt volume maakt, wordt de beschik bare ruimte voor gelaagde volumes proportioneel verminderd. De volgende tabellen geeft een overzicht van de beschik bare gelaagde capaciteit op de 8100-en 8600-apparaten wanneer lokaal vastgemaakte volumes worden gemaakt.

#### <a name="update-30"></a>Update 3,0 

| Ingerichte capaciteit van lokaal vastgemaakte volumes | Beschik bare capaciteit voor het inrichten van gelaagde volumes-8100 | Beschik bare capaciteit voor het inrichten van gelaagde volumes-8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176,5 TB |477,8 TB |
| 4 TB |105,9 TB |411,1 TB |
| 8,5 TB |0 TB |311,1 TB |
| 10 TB |NA |277,8 TB |
| 15 TB |NA |166,7 TB |
| 22,5 TB |NA |0 TB |

**Nils.** Waarom maakt lokaal vastgemaakt volume een langlopende bewerking?

**Één.** Lokaal vastgemaakte volumes zijn dik ingericht. Voor het maken van ruimte op de lokale lagen van het apparaat, kunnen sommige gegevens van bestaande gelaagde volumes tijdens het inrichtings proces naar de cloud worden gepusht. En omdat dit afhankelijk is van de grootte van het volume dat wordt ingericht, de bestaande gegevens op het apparaat en de beschik bare band breedte naar de Cloud, is de tijd die nodig is om een lokaal volume te maken, mogelijk enkele uren.

**Nils.** Hoe lang duurt het om een lokaal vastgemaakt volume te maken?

**Één.** Omdat lokaal vastgemaakte volumes dik zijn ingericht, kunnen sommige bestaande gegevens van gelaagde volumes tijdens het inrichtings proces naar de cloud worden gepusht. Daarom is de tijd die nodig is voor het maken van een lokaal vastgemaakt volume afhankelijk van meerdere factoren, zoals de grootte van het volume, de gegevens op uw apparaat en de beschik bare band breedte. Op een nieuw geïnstalleerd apparaat zonder volumes is de tijd voor het maken van een lokaal vastgemaakt volume ongeveer 10 minuten per terabyte van de gegevens. Het maken van lokale volumes kan echter enkele uren in beslag nemen op basis van de hierboven beschreven factoren op een apparaat dat in gebruik is.

**Nils.** Ik wil een lokaal vastgemaakt volume maken. Zijn er aanbevolen procedures waar ik rekening mee moet houden?

**Één.** Lokaal vastgemaakte volumes zijn geschikt voor werk belastingen die te allen tijde lokale garanties vereisen en die gevoelig zijn voor Cloud latenties. Houd rekening met het volgende wanneer u het gebruik van lokale volumes voor uw workloads overweegt:

* Lokaal vastgemaakte volumes zijn dik ingericht en het maken van lokale volumes heeft gevolgen voor de beschik bare ruimte voor gelaagde volumes. Daarom raden we u aan om te beginnen met kleinere volumes en te schalen naarmate uw opslag vereiste toeneemt.
* Het inrichten van lokale volumes is een langlopende bewerking waarbij bestaande gegevens van gelaagde volumes naar de Cloud kunnen worden gepusht. Als gevolg hiervan kunnen er gereduceerde prestaties optreden op deze volumes.
* Het inrichten van lokale volumes is een tijdrovende bewerking. De daad werkelijke tijd is afhankelijk van meerdere factoren: de grootte van het volume dat wordt ingericht, gegevens op uw apparaat en beschik bare band breedte. Als u geen back-up hebt gemaakt van uw bestaande volumes naar de Cloud, is het maken van het volume langzamer. U wordt aangeraden om Cloud momentopnamen van uw bestaande volumes te maken voordat u een lokaal volume inricht.
* U kunt bestaande gelaagde volumes converteren naar lokaal vastgemaakte volumes en deze conversie omvat het inrichten van ruimte op het apparaat voor het resulterende lokaal vastgemaakte volume (naast eventuele gelaagde gegevens, indien van toepassing, vanuit de Cloud). Dit is een langlopende bewerking die afhankelijk is van factoren die hierboven zijn besproken. U wordt aangeraden een back-up te maken van uw bestaande volumes vóór de conversie, omdat het proces nog langzamer is als er geen back-up van de bestaande volumes wordt gemaakt. Het kan ook voor komen dat uw apparaat tijdens dit proces gereduceerde prestaties ondervindt.

Meer informatie over het [maken van een lokaal vastgemaakt volume](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**Nils.** Kan ik meerdere lokaal vastgemaakte volumes tegelijk maken?

**Één.** Ja, maar lokaal vastgemaakte volumes maken en uitbreidings taken worden opeenvolgend verwerkt.

Lokaal vastgemaakte volumes zijn dik ingericht en hiervoor is het maken van lokale ruimte op het apparaat vereist. Dit kan ertoe leiden dat bestaande gegevens van gelaagde volumes tijdens het inrichtings proces naar de cloud worden gepusht. Als er een inrichtings taak wordt uitgevoerd, worden andere taken voor het maken van het lokale volume in de wachtrij geplaatst totdat de taak is voltooid.

Als een bestaand lokaal volume wordt uitgebreid of een gelaagd volume wordt geconverteerd naar een lokaal vastgemaakt volume, wordt het maken van een nieuw lokaal vastgemaakt volume in de wachtrij geplaatst totdat de vorige taak is voltooid. Als u de grootte van een lokaal vastgemaakt volume uitbreidt, moet u de bestaande lokale ruimte voor dat volume uitbreiden. De conversie van een laag naar een lokaal vastgemaakt volume omvat ook het maken van lokale ruimte voor het resulterende lokaal vastgemaakte volume. In beide bewerkingen is het maken of uitbreiden van lokale ruimte een langlopende taak.

U kunt deze taken weer geven op de Blade **taken** van de StorSimple Apparaatbeheer service. De taak die momenteel wordt verwerkt, wordt doorlopend bijgewerkt om de voortgang van het inrichten van de ruimte weer te geven. De resterende, lokaal vastgemaakte volume taken worden gemarkeerd als actief, maar de voortgang ervan is vastgelopen en ze worden gekozen in de volg orde waarin ze in de wachtrij zijn geplaatst.

**Nils.** Ik heb een lokaal vastgemaakt volume verwijderd. Waarom zie ik niet de vrijgemaakte ruimte in de beschik bare ruimte wanneer ik een nieuw volume probeer te maken?

**Één.** Als u een lokaal vastgemaakt volume verwijdert, wordt de beschik bare ruimte voor nieuwe volumes mogelijk niet onmiddellijk bijgewerkt. De StorSimple-Apparaatbeheer service werkt de lokale ruimte die ongeveer elk uur beschikbaar is, bij. U wordt aangeraden om een uur te wachten voordat u het nieuwe volume gaat maken.

**Nils.** Worden lokaal vastgemaakte volumes ondersteund op het Cloud apparaat?

**Één.** Lokaal vastgemaakte volumes worden niet ondersteund op het Cloud apparaat (8010 en 8020 apparaten voorheen het virtuele StorSimple-apparaat genoemd).

**Nils.** Kan ik de Azure PowerShell-cmdlets gebruiken voor het maken en beheren van lokaal vastgemaakte volumes?

**Één.** Nee, u kunt geen lokaal vastgemaakte volumes maken via Azure PowerShell-cmdlets (elk volume dat u maakt via Azure PowerShell is trapsgewijs gelaagd). We raden ook aan dat u niet de Azure PowerShell-cmdlets gebruikt om de eigenschappen van een lokaal vastgemaakt volume te wijzigen, omdat het niet-gewenste gevolgen heeft voor het wijzigen van het volume type in gelaagd.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Vragen over het maken van een back-up van een lokaal vastgemaakt volume
**Nils.** Worden lokale moment opnamen van lokaal vastgemaakte volumes ondersteund?

**Één.** Ja, u kunt lokale moment opnamen van uw lokaal vastgemaakte volumes nemen. We raden u echter ten zeerste aan om regel matig een back-up te maken van uw lokaal vastgemaakte volumes met behulp van Cloud momentopnamen om ervoor te zorgen dat uw gegevens worden beschermd in het geval van een nood geval.

Houd er rekening mee dat lokale moment opnamen van lokaal vastgemaakte volumes ook naar de Cloud kunnen worden gelaagd en dat ze niet gegarandeerd blijven in de lokale laag van het apparaat.

**Nils.** Zijn er richt lijnen voor het beheren van lokale moment opnamen voor lokaal vastgemaakte volumes?

**Één.** Frequente lokale moment opnamen in het lokaal vastgemaakte volume in het geval van een hoge mate van gegevens verloop kunnen ertoe leiden dat de lokale ruimte op het apparaat snel wordt verbruikt en dat de gegevens van gelaagde volumes naar de cloud worden gepusht. Daarom wordt u geadviseerd om het aantal lokale moment opnamen te minimaliseren.

**Nils.** Ik heb een waarschuwing ontvangen dat mijn lokale moment opnamen van lokaal vastgemaakte volumes mogelijk ongeldig zijn. Wanneer kan dit gebeuren?

**Één.** Frequente lokale moment opnamen in het lokaal vastgemaakte volume in een hoge mate van gegevens verloop kunnen ertoe leiden dat de lokale ruimte op het apparaat snel wordt verbruikt. Als de lokale lagen van het apparaat intensief worden gebruikt, kan een uitgebreide Cloud storing ertoe leiden dat het apparaat vol is en binnenkomende schrijf bewerkingen naar het volume kunnen leiden tot invalidatie van de moment opnamen (omdat er geen ruimte is om de moment opnamen bij te werken om te verwijzen naar de oudere gegevens blokken die zijn overschreven). In een dergelijke situatie worden de schrijf bewerkingen naar het volume nog steeds uitgevoerd, maar de lokale moment opnamen zijn mogelijk ongeldig. Er is geen invloed op uw bestaande Cloud momentopnamen.

De waarschuwing wordt weer gegeven om u te informeren dat een dergelijke situatie zich kan voordoen en ervoor te zorgen dat u op een tijd hetzelfde adres kunt vinden door de planningen van uw lokale moment opnamen te controleren om minder frequente lokale moment opnamen te maken of om oudere lokale moment opnamen te verwijderen die niet meer nodig zijn.

Als de lokale moment opnamen ongeldig zijn, ontvangt u een melding over een waarschuwing dat de lokale moment opnamen voor het specifieke back-upbeleid ongeldig zijn gemaakt naast de lijst met tijds tempels van de lokale moment opnamen die zijn ongeldig gemaakt. Deze moment opnamen worden automatisch verwijderd en u kunt ze niet meer weer geven op de Blade **back-Upcataloguss** in de Azure Portal.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Vragen over het converteren van een gelaagd volume naar een lokaal vastgemaakt volume
**Nils.** Ik houd een zekere vertraging op het apparaat aan tijdens het converteren van een gelaagd volume naar een lokaal vastgemaakt volume. Waarom gebeurt dit?

**Één.** Het conversie proces bestaat uit twee stappen:

1. Inrichting van de ruimte op het apparaat voor het lokaal vastgemaakte volume dat binnenkort moet worden geconverteerd.
2. U kunt alle gelaagde gegevens uit de cloud downloaden om ervoor te zorgen dat lokale garanties worden gegarandeerd.

Beide stappen zijn langlopende bewerkingen die afhankelijk zijn van de grootte van het volume dat wordt geconverteerd, gegevens op het apparaat en de beschik bare band breedte. Omdat sommige gegevens van bestaande gelaagde volumes naar de Cloud kunnen overlopen als onderdeel van het inrichtings proces, kan het apparaat tijdens deze tijd gebruikmaken van verminderde prestaties. Daarnaast kan het conversie proces langzamer zijn als:

* Er is geen back-up van de bestaande volumes gemaakt naar de Cloud. Daarom wordt u aangeraden een back-up te maken van de volumes voordat u een conversie start.
* Er zijn bandbreedte beperkings beleidsregels toegepast, waardoor de beschik bare band breedte naar de cloud kan worden beperkt. Daarom raden we u aan een speciale 40 Mbps of meer verbinding met de cloud te hebben.
* Het conversie proces kan enkele uren in beslag nemen als gevolg van de hierboven beschreven verschillende factoren; Daarom raden wij aan dat u deze bewerking uitvoert tijdens niet-piek tijden of in een weekend om de impact op eind gebruikers te voor komen.

Meer informatie over het [converteren van een gelaagd volume naar een lokaal vastgemaakt volume](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**Nils.** Kan ik de bewerking voor volume conversie annuleren?

**Één.** Nee, u kunt de conversie bewerking niet annuleren nadat deze is gestart. Zoals beschreven in de vorige vraag, moet u rekening houden met de potentiële prestatie problemen die tijdens het proces kunnen optreden en de aanbevolen procedures volgen die hierboven worden vermeld bij het plannen van de conversie.

**Nils.** Wat gebeurt er met mijn volume als de conversie bewerking mislukt?

**Één.** De volume conversie kan mislukken vanwege problemen met de verbinding met de Cloud. Het apparaat kan uiteindelijk het conversie proces stoppen na een reeks mislukte pogingen om gelaagde gegevens uit de cloud te halen. In een dergelijk scenario blijft het volume type het type bron volume vóór de conversie.:

* Er wordt een kritieke waarschuwing gegeven om u te informeren over de fout bij de conversie van het volume. Meer informatie over [waarschuwingen met betrekking tot lokaal vastgemaakte volumes](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Als u een laag naar een lokaal vastgemaakt volume converteert, blijft het volume eigenschappen van een gelaagd volume vertonen, omdat de gegevens zich mogelijk nog in de cloud bevinden. U wordt aangeraden de verbindings problemen op te lossen en de conversie bewerking opnieuw uit te voeren.
* Ook als de conversie van een lokaal vastgemaakt naar een gelaagd volume mislukt, hoewel het volume wordt gemarkeerd als een lokaal vastgemaakt volume, werkt het als een gelaagd volume (omdat er gegevens kunnen zijn overgelopen naar de Cloud). De lokale laag van het apparaat blijft echter ruimte innemen. Deze ruimte is niet beschikbaar voor andere lokaal vastgemaakte volumes. U wordt aangeraden deze bewerking opnieuw uit te voeren om ervoor te zorgen dat de volume conversie is voltooid en dat de lokale ruimte op het apparaat kan worden vrijgemaakt.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Vragen over het herstellen van een lokaal vastgemaakt volume
**Nils.** Worden lokaal vastgemaakte volumes onmiddellijk teruggezet?

**Één.** Ja, lokaal vastgemaakte volumes worden direct hersteld. Zodra de meta gegevens van het volume worden opgehaald uit de Cloud als onderdeel van de herstel bewerking, wordt het volume online gezet en kan het worden geopend door de host. Lokale garanties voor de volume gegevens zijn echter pas aanwezig als alle gegevens zijn gedownload uit de Cloud, en tijdens de duur van de herstel bewerking voor deze volumes minder prestaties kunnen optreden.

**Nils.** Hoe lang duurt het om een lokaal vastgemaakt volume te herstellen?

**Één.** Lokaal vastgemaakte volumes worden onmiddellijk hersteld en online gebracht zodra de meta gegevens van het volume worden opgehaald uit de Cloud, terwijl de volume gegevens op de achtergrond blijven worden gedownload. Dit laatste deel van de herstel bewerking: Hiermee wordt de lokale garanties voor de volume gegevens teruggestuurd. Dit is een langlopende bewerking. het kan enkele uren duren voordat alle gegevens opnieuw lokaal worden gemaakt. De tijd die nodig is om hetzelfde te volt ooien, is afhankelijk van meerdere factoren, zoals de grootte van het volume dat wordt hersteld en de beschik bare band breedte. Als het oorspronkelijke volume dat wordt hersteld, is verwijderd, wordt er extra tijd in rekening gebracht voor het maken van de lokale ruimte op het apparaat als onderdeel van de herstel bewerking.

**Nils.** Ik moet mijn bestaande lokaal vastgemaakte volume herstellen naar een oudere moment opname (die wordt gebruikt wanneer het volume is gelaagd). Wordt het volume hersteld als gelaagd in dit geval?

**Één.** Nee, het volume wordt hersteld als een lokaal vastgemaakt volume. Hoewel de moment opname datums tot het tijdstip waarop het volume is gelaagd, tijdens het herstellen van de bestaande volumes, gebruikt StorSimple altijd het type volume op de schijf, zoals het zich momenteel bevindt.

**Nils.** Ik heb onlangs mijn lokaal vastgemaakte volume uitgebreid, maar ik wil nu de gegevens herstellen naar een tijdstip waarop het volume kleiner is geworden. Wordt het formaat van het huidige volume hersteld en moet ik de grootte van het volume uitbreiden wanneer het herstel is voltooid?

**Één.** Ja, het formaat van het volume wordt gewijzigd en de grootte van het volume moet worden uitgebreid nadat het herstel is voltooid.

**Nils.** Kan ik het type van een volume wijzigen tijdens het herstellen?

**Één.** Nee, u kunt het volume type niet wijzigen tijdens het terugzetten.

* Volumes die zijn verwijderd, worden hersteld als het type dat is opgeslagen in de moment opname.
* Bestaande volumes worden hersteld op basis van het huidige type, ongeacht het type dat is opgeslagen in de moment opname (Zie de vorige twee vragen).

**Nils.** Ik moet mijn lokaal vastgemaakte volume herstellen, maar ik heb een onjuist moment opname van het tijdstip gekozen. Kan ik de huidige herstel bewerking annuleren?

**Één.** Ja, u kunt een doorlopende terugzet bewerking annuleren. De status van het volume wordt teruggedraaid naar de status aan het begin van het herstel. Schrijf bewerkingen die op het volume werden uitgevoerd terwijl de herstel bewerking werd uitgevoerd, gaan echter verloren.

**Nils.** Ik heb een herstel bewerking gestart op een van mijn lokaal vastgemaakte volumes en nu ziet u een moment opname in mijn achterstands catalogus die ik niet opnieuw verzamel. Waarvoor wordt dit gebruikt?

**Één.** Dit is de tijdelijke moment opname die is gemaakt voordat de herstel bewerking wordt uitgevoerd en wordt gebruikt om te worden teruggedraaid als het herstellen is geannuleerd of mislukt. Deze moment opname niet verwijderen; deze wordt automatisch verwijderd wanneer de herstel bewerking is voltooid. Dit gedrag kan optreden als uw herstel taak alleen lokaal vastgemaakte volumes of een combi natie van lokaal vastgemaakte en gelaagde volumes heeft. Als de herstel taak alleen gelaagde volumes bevat, wordt dit gedrag niet uitgevoerd.

**Nils.** Kan ik een lokaal vastgemaakt volume klonen?

**Één.** Ja, dat kunt u. Het lokaal vastgemaakte volume wordt echter standaard gekloond als een gelaagd volume. Meer informatie over het [klonen van een lokaal vastgemaakt volume](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Vragen over het mislukken van een lokaal vastgemaakt volume
**Nils.** Ik moet een failover van mijn apparaat naar een ander fysiek apparaat uitvoeren. Worden er via mijn lokaal vastgemaakte volumes een failover uitgevoerd als lokaal vastgemaakt of gelaagd?

**Één.** De lokaal vastgemaakte volumes worden failover als lokaal vastgemaakt als op het doel apparaat StorSimple 8000 Series update 3 of hoger wordt uitgevoerd.

Meer informatie over [failover en Dr van lokaal vastgemaakte volumes in versies](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**Nils.** Worden lokaal vastgemaakte volumes onmiddellijk hersteld tijdens nood herstel (DR)?

**Één.** Ja, lokaal vastgemaakte volumes worden direct hersteld tijdens een failover. Zodra de meta gegevens informatie voor het volume wordt opgehaald uit de Cloud als onderdeel van de failoverbewerking, wordt het volume online gezet op het doel apparaat en kan het worden geopend door de host. Ondertussen blijven de volume gegevens op de achtergrond worden gedownload en kunnen de prestaties van deze volumes voor de duur van de failover worden verminderd.

**Nils.** Ik zie de failover-taak is voltooid, hoe kan ik de voortgang van lokaal vastgemaakt volume dat wordt hersteld op het doel apparaat volgen?

**Één.** Tijdens een failoverbewerking wordt de failover-taak als voltooid gemarkeerd zodra alle volumes in de failoverset onmiddellijk zijn hersteld en online worden gezet op het doel apparaat. Dit omvat alle lokaal vastgemaakte volumes waarvoor een failover is uitgevoerd. lokale garanties van de gegevens zijn echter alleen beschikbaar wanneer alle gegevens voor het volume zijn gedownload. U kunt deze voortgang volgen voor elk lokaal vastgemaakt volume waarvoor een failover is uitgevoerd door de bijbehorende herstel taken te controleren die zijn gemaakt als onderdeel van het failovercluster. Deze afzonderlijke herstel taken worden alleen gemaakt voor lokaal vastgemaakte volumes.

**Nils.** Kan ik het type van een volume wijzigen tijdens een failover?

**Één.** Nee, u kunt het volume type niet wijzigen tijdens een failover. Als u een failover uitvoert naar een ander fysiek apparaat waarop StorSimple 8000 Series update 3 wordt uitgevoerd, worden de volumes failover uitgevoerd op basis van het volume type dat is opgeslagen in de moment opname.

**Nils.** Kan ik een failover uitvoeren voor een volume container met lokaal vastgemaakte volumes aan het Cloud apparaat?

**Één.** Ja, dat kunt u. De lokaal vastgemaakte volumes worden failover als gelaagde volumes. Meer informatie over [failover en Dr van lokaal vastgemaakte volumes in versies](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

