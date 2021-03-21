---
title: Opties voor gegevens migratie van StorSimple 5000-7000 series-apparaten
description: Hierin wordt een overzicht gegeven van de opties voor het migreren van gegevens uit de StorSimple 5000-7000-serie.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 316b513f761aa422f87c83658e6c50efec25efb4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94961138"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Opties voor het migreren van gegevens uit de collectie StorSimple 5000-7000 

> [!IMPORTANT]
> Op 9 juli 2019 de StorSimple-serie van 5000/7000 om de status van het end of support (EOS) te bereiken. We raden aan dat klanten van de StorSimple 5000/7000-serie migreren naar een van de alternatieven die in het document worden beschreven.

De StorSimple 5000-7000-serie bereikt het [einde van de ondersteuning](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) in juli 2019. De klanten die de StorSimple 5000-7000-serie uitvoeren, hebben de mogelijkheid om een upgrade uit te voeren naar andere hybride services van de eerste partij van Azure. In dit artikel worden de hybride opties voor Azure beschreven die beschikbaar zijn voor het migreren van gegevens. 

## <a name="migration-options"></a>Migratieopties

De klanten die gebruikmaken van de StorSimple 5000-7000-serie beschikken over opties van Azure of van derden.

### <a name="azure-options"></a>Azure-opties

#### <a name="upgrade-to-storsimple-8000-series"></a>Upgrade uitvoeren naar de StorSimple 8000-serie

Voer een upgrade uit naar de StorSimple 8000-serie en ga daarom verder op het StorSimple-platform.  Voor dit upgradepad moeten klanten hun 5000-7000-serie apparaten vervangen door een 8000-serie. De gegevens worden gemigreerd van het 5000-7000-serie apparaat met behulp van het hulp programma voor migratie. Zodra de migratie is voltooid, blijven de StorSimple 8000-serie-apparaten de gegevens laag in de Azure-Blob Storage. 

Ga voor meer informatie over het migreren van gegevens met behulp van een StorSimple 8000-serie naar [gegevens migreren van StorSimple 5000-7000-serie naar 8000 Series-apparaat](storsimple-8000-migrate-from-5000-7000.md).

#### <a name="migrate-to-azure-file-sync"></a>Migreren naar Azure File Sync

Met deze gloed nieuwe migratie optie kunnen klanten de bestands shares van hun organisatie opslaan in de Azure Files. Deze bestands shares worden vervolgens gecentraliseerd voor on-premises toegang met behulp van Azure File Sync (AFS). AFS kan worden geïmplementeerd op een Windows Server-host. De daad werkelijke gegevens migratie wordt vervolgens uitgevoerd als een host-kopie of met het hulp programma voor migratie.

Ga voor meer informatie over het migreren van gegevens naar Azure File Sync naar [gegevens migreren van de StorSimple 5000-7000-serie naar Azure file sync](../storage/files/storage-files-migration-storsimple-8000.md).

### <a name="third-party-options"></a>Opties van derden

#### <a name="migrate-to-panzura-freedom-nas"></a>Migreren naar Panzura vrijheid NAS

StorSimple 5000-7000-klanten kunnen ervoor kiezen om te migreren naar Panzura vrijheid NAS om hun gegevens in azure te blijven gebruiken. De oplossing Panzura vrijheid biedt een NAS-oplossing die data centers, kant oren, open bare en persoonlijke Clouds omvat. De oplossing maakt lokale, hybride en in-Cloud gegevens werk stromen mogelijk voor NFS-, SMB-en mobiele clients. 

Deze migratie wordt ondersteund door Panzura en klanten kunnen aan de slag gaan door de ondersteuning voor migratie van de [Panzura-website](https://panzura.com/storsimple-migration/)aan te vragen.

#### <a name="migrate-to-cohesity"></a>Migreren naar Cohesity

Met Cohesity kunt u gegevens van uw huidige StorSimple 5000 – 7000 migreren naar het Cohesity-gegevens platform op Azure. Het Cohesity-gegevens platform is een software-gedefinieerde oplossing voor webschaal die bestanden, back-ups, objecten en virtuele machines consolideert op één Cloud systeem eigen oplossing. Na de migratie naar het gegevens platform kunt u gegevens en apps van Cloud naar kern beheren, beveiligen en inrichten via één glas venster. Met Cohesity begint met slechts drie knoop punten. 

Meer informatie over [migratie naar het Cohesity-gegevens platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### <a name="migrate-to-nasuni"></a>Migreren naar Nasuni

Nasuni maakt het eenvoudig voor StorSimple 5000-7000-klanten om hun gegevens in azure te migreren en te hand haven.  Nasuni is een toonaangevende oplossing voor NAS-opslag op basis van Azure, waardoor klanten de prestaties en beveiliging verwachten van on-premises oplossingen, met economische Clouds en schaal mogelijkheden.  Naast de bestands opslag met hoge prestaties, Nasuni en Azure-handle back-ups en DR, terwijl u uw gegevens overal ter wereld kunt delen en samen werken met gecentraliseerd beheer van bestands opslag. 

Nasuni heeft de ervaring om uw migratie eenvoudig te maken: vandaag kunt u aan de slag. https://info.nasuni.com/nasuni-storsimple-migration

#### <a name="migrate-to-talon-fast"></a>SNEL migreren naar Talon

Talon maakt het eenvoudig voor StorSimple 5000-7000-klanten om de voor delen te blijven benutten die ze aanzienlijk hebben gewaardeerd in het StorSimple-platform (een kleine on-site footprint die wordt ondersteund door onbeperkte Cloud bronnen) met nog meer functies.  Met de Talon FAST-oplossing kunnen klanten hun gegevens in azure migreren en inperken, terwijl ze nu een zelfs kleinere op de werkloze manier bewerkte footprint hebben en voor delen zoals globale bestands vergrendeling, globale naam ruimte en samen werking op meerdere locaties toevoegen.  Talon is een toonaangevende Azure-ecosysteem oplossing, die samenwerkt met wereld wijde klanten om hun on-premises Bestands server werkbelastingen te migreren naar een geconsolideerd, op Azure gebaseerde footprint zonder in te leveren op de werk stroom of ervaring van de gebruiker.  

Meer informatie over hoe u kunt samen werken met een onderneming in de Cloud op https://www.talonstorage.com/alliances/microsoft-storsimple .


## <a name="migration---frequently-asked-questions"></a>Migratie: veelgestelde vragen

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>V. Wanneer zijn de apparaten uit de StorSimple 5000-en 7000-serie het eind van de service? 

A. De StorSimple 5000-7000-serie bebereikt de [service](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) in juli 2019. Het einde van de service impliceert dat micro soft geen ondersteuning meer kan bieden voor hardware en software van deze apparaten na juli 2019. We raden u ten zeerste aan om te beginnen met het formuleren van een plan om de gegevens van uw apparaten nu te migreren.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>V. Wat gebeurt er met de gegevens die ik in azure heb opgeslagen?  

A. U kunt de gegevens in azure blijven gebruiken nadat u deze naar een nieuwere service hebt gemigreerd. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>V. Wat gebeurt er met de gegevens die ik lokaal heb opgeslagen op mijn StorSimple-apparaat? 

A. De gegevens die zich op het lokale apparaat bevindt, kunnen naar de nieuwere service worden gekopieerd, zoals beschreven in de migratie documenten.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>V. Wat gebeurt er als ik mijn StorSimple 5000/7000-serie apparaat wil blijven gebruiken? 

A. Hoewel de services kunnen blijven werken, kan micro soft geen hardware-en software ondersteuning meer bieden. Migratie wordt ten zeerste aanbevolen voor bedrijfs continuïteit.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>V. Welke opties zijn beschikbaar voor het migreren van gegevens van de StorSimple 5000-7000 series-apparaten? 

A. Afhankelijk van het scenario, hebben gebruikers van de StorSimple 5000-7000-serie de volgende migratie opties. 

 - **Upgrade uitvoeren naar 8000-serie**: gebruik deze optie als u wilt door gaan op het StorSimple-platform. 
 - **Migreren naar Azure file sync**: gebruik deze optie als u wilt overschakelen naar de native Azure-indeling. U kunt Azure File Sync gebruiken voor gecentraliseerd beheer van bestands shares. 

U kunt contact opnemen met Microsoft Ondersteuning voor het bespreken van migratie opties die hier niet worden vermeld.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>V. Wordt de migratie naar andere opslag oplossingen ondersteund?

A. Ja. Migratie naar andere opslag oplossingen met behulp van de kopie van de gegevens wordt ondersteund.

### <a name="q-is-migration-supported-by-microsoft"></a>V. Wordt migratie ondersteund door micro soft? 

A. Migratie van 5000-of 7000-serie is een volledig ondersteunde bewerking. Micro soft raadt u aan om ondersteuning te bereiken voordat u de migratie start. Migratie is momenteel een ondersteunings bewerking. Als u van plan bent om gegevens te migreren van uw StorSimple 5000-7000-serie apparaat, [opent u een ondersteunings ticket](storsimple-8000-contact-microsoft-support.md).

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>V. Wat is het prijs model voor beide opties voor migratie?

A. De kosten voor migratie variëren afhankelijk van de optie die u kiest. Hoewel migratie zelf gratis is, zijn er kosten verbonden aan het Hardwareapparaat als u besluit om te upgraden naar een StorSimple 8000-serie. 

Op dezelfde manier geldt dat als u Azure File Sync gebruikt, de abonnements kosten voor de service van toepassing kunnen zijn. In elk geval moeten klanten ook doorlopende opslag kosten betalen. Raadpleeg het volgende voor een schatting: 
- [Prijzen voor StorSimple](https://azure.microsoft.com/pricing/details/storsimple/)  
- [AFS-prijzen]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>V.  Hoe lang duurt het om een migratie te volt ooien?

A. De tijd voor het migreren van gegevens is afhankelijk van de hoeveelheid gegevens en de geselecteerde upgrade-optie. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>V. Wat is het einde van de ondersteunings datum voor de StorSimple 8000-serie?

A. Het einde van de ondersteunings datum voor de StorSimple 8000-serie wordt [hier](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)gepubliceerd.


## <a name="next-steps"></a>Volgende stappen
 - [Gegevens migreren van een StorSimple 5000-7000-serie naar een 8000 Series-apparaat](storsimple-8000-migrate-from-5000-7000.md).
 - [Gegevens migreren van een StorSimple 5000-7000-serie naar Azure File Sync](../storage/files/storage-files-migration-storsimple-8000.md)