---
title: Uw StorSimple 8000 Series-apparaat bewaken
description: Hierin wordt beschreven hoe u de StorSimple Apparaatbeheer-service gebruikt om het gebruik, de I/O-prestaties en het capaciteits gebruik te bewaken.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: de4cae90f2cc1223271672d9fc5ba5b42e4409e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94962430"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>De StorSimple Apparaatbeheer-service gebruiken om uw StorSimple-apparaat te bewaken

## <a name="overview"></a>Overzicht
U kunt de StorSimple Apparaatbeheer-service gebruiken voor het bewaken van specifieke apparaten in uw StorSimple-oplossing. U kunt aangepaste grafieken maken op basis van I/O-prestaties, capaciteits gebruik, netwerk doorvoer en metrische prestatie gegevens van apparaten en deze aan het dash board vastmaken. Ga naar [uw portal-dash board aanpassen](../azure-portal/azure-portal-dashboards.md)voor meer informatie.

Als u de controle-informatie voor een specifiek apparaat wilt weer geven, selecteert u in de Azure Portal de StorSimple Apparaatbeheer-service. Selecteer uw apparaat in de lijst met apparaten en ga vervolgens naar **monitor**. U kunt vervolgens de grafieken **capaciteit**, **gebruik** en **prestaties** zien voor het geselecteerde apparaat.

## <a name="capacity"></a>Capaciteit
**Capaciteit** traceert de ingerichte ruimte en de resterende ruimte op het apparaat. De resterende capaciteit wordt vervolgens weer gegeven als lokaal vastgemaakt of gelaagd.

De ingerichte en resterende capaciteit wordt verder onderverdeeld in gelaagde en lokaal vastgemaakte volumes. Voor elk volume worden de ingerichte capaciteit en de resterende capaciteit op het apparaat weer gegeven.

![I/o-capaciteit](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Gebruik
Het **gebruik** houdt de metrische gegevens bij die betrekking hebben op de hoeveelheid opslag ruimte die wordt gebruikt door de volumes, volume containers of het apparaat. U kunt rapporten maken op basis van het capaciteits gebruik van uw primaire opslag, uw Cloud opslag of uw opslag van uw apparaten. Het capaciteits gebruik kan worden gemeten op een specifiek volume, een specifieke volume container of alle volume containers.
Het gebruik van de afgelopen 24 uur wordt standaard gerapporteerd. U kunt de grafiek bewerken om de duur te wijzigen waarover het gebruik wordt gerapporteerd door een van de volgende opties te selecteren:
* Afgelopen 24 uur
* Afgelopen 7 dagen
* Afgelopen 30 dagen
* Afgelopen 90 dagen
* Afgelopen jaar

Er worden twee belang rijke metrische gegevens, groei en bereik gerapporteerd voor de gebruiks grafieken. Het bereik verwijst naar de maximum waarde en de minimum waarden van het gebruik dat is gerapporteerd gedurende de geselecteerde duur (fo-instantie, in de afgelopen 7 dagen).

De groei verwijst naar de toename van het gebruik van de eerste dag tot de laatste dag voor de geselecteerde duur. 

Groei en bereik kunnen ook worden weer gegeven met de volgende vergelijkingen:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

De primaire, Cloud en lokale opslag die worden gebruikt, kunnen als volgt worden beschreven:

### <a name="primary-storage-usage"></a>Primair opslag gebruik
Deze grafieken tonen de hoeveelheid gegevens die is geschreven naar StorSimple-volumes voordat de gegevens worden ontdubbeld en gecomprimeerd. U kunt de primaire opslag weer geven die wordt gebruikt door alle volumes in een volume container of voor één volume. De gebruikte primaire opslag wordt verder onderverdeeld op basis van de primaire gelaagde opslag en de gebruikte primaire lokaal vastgemaakte opslag.

In de volgende grafieken ziet u de primaire opslag die wordt gebruikt voor een StorSimple-apparaat vóór en nadat een Cloud momentopname is gemaakt. Aangezien dit alleen volume gegevens zijn, mag een Cloud momentopname de primaire opslag niet wijzigen. Zoals u ziet, toont het diagram geen verschil in de primaire of lokaal vastgemaakte opslag die wordt gebruikt als resultaat van het maken van een Cloud momentopname. De Cloud momentopname is gestart om 11:50 uur op dat apparaat.

![Primair capaciteits gebruik na de Cloud momentopname](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Als u nu i/o uitvoert op de host die is verbonden met uw StorSimple-apparaat, ziet u een toename van de primaire gelaagde opslag of primaire lokaal vastgemaakte opslag die wordt gebruikt, afhankelijk van welke volumes (gelaagd of lokaal vastgemaakt) u de gegevens schrijft. Hier vindt u de primaire gebruiks grafieken voor opslag voor een StorSimple-apparaat. Op dit apparaat is de StorSimple-host begonnen met het schrijven van 2:30 uur op een gelaagd volume op het apparaat. U kunt de piek in de geschreven bytes/s zien die overeenkomen met de i/o-bewerkingen die worden uitgevoerd op de host.

![Prestaties bij het uitvoeren van i/o op gelaagde volumes](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Als u de primaire gelaagde opslag gebruikt, wordt er een fout weer gegeven terwijl het primaire lokaal vastgemaakte gebruik ongewijzigd blijft omdat er geen schrijf bewerkingen worden uitgevoerd naar de lokaal vastgemaakte volumes op het apparaat.

![Primair capaciteits gebruik wanneer IO wordt uitgevoerd op gelaagde volumes](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Als u update 3 of hoger uitvoert, kunt u het gebruik van de primaire opslag capaciteit opdelen door een afzonderlijk volume, alle volumes, alle gelaagde volumes en alle lokaal vastgemaakte volumes, zoals hieronder wordt weer gegeven. Met alle lokaal vastgemaakte volumes kunt u snel bepalen hoeveel van de lokale laag er wordt gebruikt.

![Primair capaciteits gebruik voor alle gelaagde volumes](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Primair capaciteits gebruik voor alle lokaal vastgemaakte volumes](./media/storsimple-8000-monitor-device/monitor-usage4.png)

U kunt verder klikken op elk van de volumes in de lijst en het bijbehorende gebruik bekijken.

![Primair capaciteits gebruik voor alle lokaal vastgemaakte volumes 2](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Gebruik van Cloud opslag
Deze grafieken tonen de hoeveelheid gebruikte Cloud opslag. Deze gegevens worden ontdubbeld en gecomprimeerd. Dit bedrag omvat Cloud momentopnamen die gegevens kunnen bevatten die niet worden weer gegeven op een primair volume en die worden bewaard voor verouderde of vereiste Bewaar doeleinden. U kunt de gegevens voor het gebruik van de primaire en de Cloud opslag vergelijken om een idee te krijgen van het verkleinings tempo, hoewel het getal niet exact is.

In de volgende grafieken wordt het gebruik van de Cloud opslag van een StorSimple-apparaat weer gegeven wanneer er een Cloud momentopname is gemaakt.

* De Cloud momentopname is gestart om ongeveer 11:50 uur op dat apparaat en u kunt zien dat er geen Cloud opslag is gebruikt voordat de moment opname van de Cloud wordt gemaakt. 
* Nadat de moment opname van de Cloud is voltooid, is het gebruik van de Cloud opslag 0,89 GB afgenomen. 
* Terwijl de moment opname van de Cloud werd uitgevoerd, is er ook een corresponderende piek in de i/o van het apparaat naar de Cloud.

    ![Gebruik van Cloud opslag vóór de Cloud momentopname](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Gebruik van Cloud opslag na de Cloud momentopname](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![I/o van apparaat naar Cloud tijdens een Cloud momentopname](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Gebruik van lokale opslag
In deze grafieken wordt het totale gebruik van het apparaat weer gegeven. Dit is meer dan het gebruik van primaire opslag omdat het de lineaire laag SSD bevat. Deze laag bevat een hoeveelheid gegevens die ook op de andere lagen van het apparaat aanwezig is. De capaciteit van de lineaire laag SSD wordt gerecycled, zodat de oude gegevens worden verplaatst naar de HDD-laag (op het moment dat deze wordt ontdubbeld en gecomprimeerd) en vervolgens naar de Cloud.

In de loop van de tijd worden de gebruikte primaire opslag en de gebruikte lokale opslag meestal verhoogd totdat de gegevens naar de cloud worden gelaagd. Op dat moment wordt het gebruik van de gebruikte lokale opslag waarschijnlijk gestart, maar wordt de primaire opslag verhoogd naarmate er meer gegevens worden geschreven.

In de volgende grafieken ziet u de primaire opslag die wordt gebruikt voor een StorSimple-apparaat wanneer er een Cloud momentopname is gemaakt. De Cloud momentopname is gestart om 11:50 uur en de lokale opslag is op dat moment aflopend. De gebruikte lokale opslag is van 1,445 GB tot 1,09 GB. Dit geeft aan dat de niet-gecomprimeerde gegevens in de laag met lineaire SSD zijn ontdubbeld, gecomprimeerd en verplaatst naar de HDD-laag. Houd er rekening mee dat als het apparaat al een grote hoeveelheid gegevens in de lagen SSD en HDD heeft, dit afnemen mogelijk niet wordt weer gegeven. In dit voor beeld heeft het apparaat een kleine hoeveelheid gegevens.

![Gebruik van lokale opslag na de Cloud momentopname](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Prestaties
**Prestaties** traceren de metrische gegevens met betrekking tot het aantal lees-en schrijf bewerkingen tussen de iSCSI-initiator-interfaces op de hostserver en het apparaat of het apparaat en de Cloud. Deze prestaties kunnen worden gemeten voor een specifiek volume, een specifieke volume container of alle volume containers. Prestaties omvatten ook CPU-gebruik en netwerk doorvoer voor de verschillende netwerk interfaces op het apparaat.

### <a name="io-performance-for-initiator-to-device"></a>I/O-prestaties van de initiator op het apparaat
In de volgende grafiek ziet u de I/O voor de initiator op het apparaat voor alle volumes van een productie apparaat. De metrische gegevens die zijn uitgezet, zijn gelezen en geschreven bytes per seconde. U kunt ook de grafiek Lees-, schrijf-en openstaande i/o-en lees-en schrijf latenties.

![I/o-prestaties van de initiator op het apparaat](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>I/O-prestaties van het apparaat naar de Cloud
Voor hetzelfde apparaat worden de I/O-bewerkingen uitgezet voor de gegevens van het apparaat naar de Cloud voor alle volume containers. De gegevens op dit apparaat bevindt zich alleen in de lineaire laag en er is niets overgelopen naar de Cloud. Er zijn geen lees-en schrijf bewerkingen van het apparaat naar de Cloud. De pieken in de grafiek hebben daarom een interval van 5 minuten die overeenkomt met de frequentie waarmee de heartbeat wordt gecontroleerd tussen het apparaat en de service.

Voor hetzelfde apparaat werd een Cloud momentopname gemaakt voor volume gegevens vanaf 11:50 uur. Dit heeft tot gevolg dat gegevens stromen van het apparaat naar de Cloud. Er zijn tijdens deze duur schrijf bewerkingen naar de Cloud verzonden. In de i/o-grafiek ziet u een piek in de geschreven bytes/s die overeenkomt met het tijdstip waarop de moment opname is gemaakt.

![I/o van apparaat naar Cloud tijdens een Cloud momentopname](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Netwerk doorvoer voor netwerk interfaces van apparaat
Met **netwerk doorvoer** worden metrische gegevens bijgehouden die betrekking hebben op de hoeveelheid van de iSCSI-initiator netwerk interfaces op de hostserver en op het apparaat en tussen het apparaat en de Cloud. U kunt deze metrische gegevens controleren voor elk van de iSCSI-netwerk interfaces op het apparaat.

In de volgende grafieken ziet u de netwerk doorvoer voor het data 0, 1 1 GbE-netwerk op uw apparaat, dat zowel voor de Cloud als voor iSCSI is ingeschakeld. Omdat de gegevens op dit apparaat op 14 juni worden gelaagd in de Cloud (er zijn op dat moment geen moment opnamen van de Cloud gemaakt, waardoor het niveau wordt gelaagd als het mechanisme voor het verplaatsen van de gegevens naar de Cloud), waardoor IO werd geleverd aan de Cloud. Er is een overeenkomende piek in de netwerk doorvoer grafiek voor dezelfde tijd en het grootste deel van het netwerk verkeer is uitgaande van de Cloud.

![Netwerk doorvoer voor de gegevens 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Als we het doorvoer diagram van de data 1-interface bekijken, een andere 1 GbE-netwerk interface waarvoor alleen iSCSI is ingeschakeld, was er bijna geen netwerk verkeer tijdens deze periode.

![Netwerk doorvoer voor gegevens 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>CPU-gebruik voor apparaat
Met **CPU-gebruik** worden metrische gegevens bijgehouden die zijn gerelateerd aan de CPU die op het apparaat wordt gebruikt. In het volgende diagram ziet u de statistieken voor het CPU-gebruik van een apparaat in de productie omgeving.

![CPU-gebruik voor apparaat](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het gebruik van het StorSimple Apparaatbeheer service-dash board](./storsimple-8000-device-dashboard.md).
* Meer informatie over [het gebruik van de StorSimple Apparaatbeheer-service voor het beheren van uw StorSimple-apparaat](./storsimple-8000-manager-service-administration.md).