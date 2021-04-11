---
title: bestand opnemen
description: bestand opnemen
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 4/05/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 57d14ae6e6da7cfa883f1aed74cce390c0185d98
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491668"
---
Snelheid en succes percentage van een bepaalde RoboCopy-uitvoering is afhankelijk van verschillende factoren:

* IOPS op de bron-en doel opslag
* de beschik bare netwerk bandbreedte tussen de bron en het doel
* de mogelijkheid om snel bestanden en mappen in een naam ruimte te verwerken
* het aantal wijzigingen tussen RoboCopy-runs


### <a name="iops-and-bandwidth-considerations"></a>Beschik bare IOPS-en bandbreedte overwegingen

In deze categorie moet u rekening houden met de mogelijkheden van de **bron opslag**, de **doel opslag** en het **netwerk** waarmee ze zijn verbonden. De maximale door Voer wordt bepaald door het langzaamst van deze drie onderdelen. Zorg ervoor dat uw netwerk infrastructuur is geconfigureerd voor de ondersteuning van optimale overdrachts snelheid naar de beste mogelijkheden.

> [!CAUTION]
> Houd er rekening mee dat het gebruik van uw lokale netwerk en een NAS-apparaat voor andere, vaak bedrijfskritische taken, vaak zo snel mogelijk wordt gekopieerd.

Het is mogelijk dat het niet wenselijk is om zo snel mogelijk te kopiëren als er een risico bestaat dat de migratie beschik bare resources in beslag kan nemen.

* Houd rekening met het beste in uw omgeving om migraties uit te voeren: gedurende de dag, buiten kantoor uren of in het weekend.
* Denk ook aan netwerk-QoS op een Windows-Server om de RoboCopy-snelheid te beperken.
* Vermijd onnodig werk voor de migratie hulpprogramma's.

RobCopy kunnen vertragingen tussen pakketten invoegen door de `/IPG:n` Switch op te geven waar `n` wordt gemeten in milliseconden tussen Robocopy-pakketten. Met deze switch kunt u voor komen dat bronnen op zowel i/o-apparaten met IO worden beslagen als grote netwerk koppelingen.

`/IPG:n` kan niet worden gebruikt voor nauw keurige netwerk beperking van een bepaalde Mbps. Gebruik in plaats daarvan Windows Server Network QoS. RoboCopy is volledig afhankelijk van het SMB-protocol voor alle netwerk behoeften. Het gebruik van SMB is de reden waarom RoboCopy geen invloed heeft op de netwerk doorvoer zelf, maar kan het gebruik vertragen. 

Een vergelijk bare regel wordt toegepast op de IOPS die op de NAS wordt waargenomen. De cluster grootte op het NAS-volume, pakket grootten en een reeks andere factoren beïnvloedt de waargenomen IOPS. Het introduceren van een interpakket vertraging is vaak de eenvoudigste manier om de belasting van de NAS te beheren. Test meerdere waarden, bijvoorbeeld van ongeveer 20 milliseconden (n = 20), tot veelvouden van dat getal. Zodra u een vertraging hebt ingevoerd, kunt u evalueren of uw andere apps nu naar behoren kunnen werken. Deze optimalisatie strategie biedt u de mogelijkheid om de optimale snelheid van RoboCopy in uw omgeving te vinden.

### <a name="processing-speed"></a>Verwerkings snelheid

De naam ruimte wordt door RoboCopy door lopen en de bestanden en mappen worden geëvalueerd voor kopiëren. Elk bestand wordt geëvalueerd tijdens een eerste kopie en tijdens het opvangen van kopieën. Bijvoorbeeld herhaalde uitvoeringen van RoboCopy/MIR op dezelfde bron-en doel opslag locatie. Deze herhaalde uitvoeringen zijn handig om de uitval tijd voor gebruikers en apps te minimaliseren en om het totale succes percentage van gemigreerde bestanden te verbeteren.

Het is vaak standaard om band breedte te beschouwen als de meest beperkende factor in een migratie, en dat kan waar zijn. Maar de mogelijkheid om een naam ruimte op te sommen kan invloed hebben op de totale tijd voor het kopiëren van grotere naam ruimten met kleinere bestanden. Houd er rekening mee dat het kopiëren van 1 TiB van kleine bestanden aanzienlijk langer duurt dan het kopiëren van 1 TiB van minder, maar grotere bestanden. Ervan uitgaande dat alle andere variabelen hetzelfde blijven.

De oorzaak van dit verschil is de verwerkings kracht die nodig is om een naam ruimte te door lopen. RoboCopy ondersteunt multi-threaded kopieën via de `/MT:n` para meter waarbij n staat voor het aantal processor-threads. Bij het inrichten van een computer specifiek voor RoboCopy, moet u rekening houden met het aantal processor kernen en hun relatie tot het aantal threads dat ze bieden. De meest voorkomende zijn twee threads per kern. Het aantal kernen en threads van een computer is een belang rijk gegevens punt om te bepalen welke multi-thread waarden `/MT:n` u moet opgeven. Denk ook na over hoeveel RoboCopy-taken u parallel wilt uitvoeren op een bepaalde computer.

Met meer threads wordt ons 1-TiB voor beeld van kleine bestanden aanzienlijk sneller dan minder threads gekopieerd. Terzelfder tijd kan de extra bron investering op onze 1 TiB grotere bestanden geen proportionele voor delen opleveren. Een hoog aantal threads probeert tegelijkertijd meer grote bestanden via het netwerk te kopiëren. Deze extra netwerk activiteit verhoogt de kans op het verkrijgen van beperkingen door door Voer of opslag-IOPS.

### <a name="avoid-unnecessary-work"></a>Vermijd onnodig werk

Vermijd grootschalige wijzigingen in uw naam ruimte. Bijvoorbeeld, het verplaatsen van bestanden tussen directory's, het wijzigen van eigenschappen op een grote schaal of het wijzigen van machtigingen (NTFS-Acl's). Met name ACL'S-wijzigingen kunnen een grote invloed hebben omdat ze vaak een trapsgewijs wijzigings effect hebben op bestanden in de mappen hiërarchie. De gevolgen kunnen zijn:

* uitgebreide RoboCopy-taak uitvoer tijd omdat elk bestand en elke map die van toepassing zijn op een ACL-wijziging, moeten worden bijgewerkt
* het opnieuw gebruiken van gegevens die eerder zijn verplaatst, moet mogelijk opnieuw worden gekopieerd. Zo moeten er meer gegevens worden gekopieerd wanneer de mapstructuren worden gewijzigd nadat de bestanden eerder zijn gekopieerd. Een RoboCopy-taak kan een naam ruimte wijziging niet ' afspelen ' maken. De volgende taak moet de bestanden die eerder naar de oude mappen structuur zijn getransporteerd, leegmaken en de bestanden in de nieuwe mapstructuur opnieuw uploaden.

Een ander belang rijk aspect is het gebruik van het hulp programma RoboCopy effectief. Met het aanbevolen RoboCopy-script maakt en slaat u een logboek bestand op voor fouten. Kopieer fouten kunnen zich voordoen. Dit is normaal. Deze fouten maken het vaak nodig om meerdere ronden uit te voeren op een kopieer programma, zoals RoboCopy. Een eerste uitvoering, zeg van een NAS naar DataBox of een server naar een Azure-bestands share. En een of meer extra uitvoeringen met de/MIR-switch om bestanden te ondervangen en opnieuw op te halen die niet zijn gekopieerd.

U moet voorbereiden op het uitvoeren van meerdere rondes van RoboCopy op een opgegeven naam ruimte bereik. Opeenvolgende uitvoeringen worden sneller uitgevoerd omdat ze minder hoeven te worden gekopieerd, maar die steeds vaker worden beperkt door de snelheid van de verwerking van de naam ruimte. Wanneer u meerdere afrondingen uitvoert, kunt u elke afronding versnellen door geen RoboCopy te hebben om alles in een bepaalde uitvoering te kopiëren. Deze RoboCopy-switches kunnen een aanzienlijk verschil vormen:

* `/R:n` n = hoe vaak u een mislukt bestand probeert te kopiëren en 
* `/W:n` n = aantal seconden dat moet worden gewacht tussen nieuwe pogingen

`/R:5 /W:5` is een redelijke instelling die u kunt aanpassen aan uw eigen smaak. In dit voor beeld wordt een mislukt bestand vijf keer opnieuw geprobeerd, met een wacht tijd van vijf seconden tussen nieuwe pogingen. Als het bestand nog steeds niet kan worden gekopieerd, wordt de volgende RoboCopy-taak opnieuw geprobeerd. Bestanden die zijn mislukt omdat ze in gebruik zijn of omdat de time-outproblemen uiteindelijk op deze manier kunnen worden gekopieerd.
   
