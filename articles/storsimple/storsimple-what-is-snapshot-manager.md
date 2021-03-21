---
title: Wat is StorSimple Snapshot Manager? | Microsoft Docs
description: Hierin worden de StorSimple-Snapshot Manager, de architectuur en de functies ervan beschreven.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2214f085c30419cefb3f6f84139d5592873173f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017981"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>Een inleiding tot StorSimple Snapshot Manager

## <a name="overview"></a>Overzicht
StorSimple Snapshot Manager is een MMC-module (micro soft Management Console) waarmee gegevens bescherming en het beheer van back-ups in een Microsoft Azure StorSimple omgeving worden vereenvoudigd. Met StorSimple Snapshot Manager kunt u Microsoft Azure StorSimple gegevens in het Data Center en in de Cloud beheren als één geïntegreerde opslag oplossing, waardoor back-upprocess worden vereenvoudigd en de kosten worden verminderd.

In dit overzicht wordt de StorSimple-Snapshot Manager geïntroduceerd, worden de functies ervan beschreven en wordt de rol ervan in Microsoft Azure StorSimple uitgelegd. 

Voor een overzicht van het hele Microsoft Azure StorSimple systeem, met inbegrip van het StorSimple-apparaat, de StorSimple Manager-service, de StorSimple Snapshot Manager en de StorSimple-adapter voor share point, Zie [StorSimple 8000-serie: een Hybrid Cloud Storage oplossing](storsimple-overview.md). 

> [!NOTE]
> * U kunt StorSimple-Snapshot Manager niet gebruiken voor het beheren van Microsoft Azure StorSimple virtuele matrices (ook wel StorSimple on-premises virtuele apparaten genoemd).
> * Als u van plan bent StorSimple update 2 op uw StorSimple-apparaat te installeren, moet u de nieuwste versie van StorSimple Snapshot Manager downloaden en installeren **voordat u StorSimple update 2 installeert**. De nieuwste versie van StorSimple Snapshot Manager is achterwaarts compatibel en werkt met alle uitgebrachte versies van Microsoft Azure StorSimple. Als u de vorige versie van StorSimple Snapshot Manager gebruikt, moet u deze bijwerken (u hoeft de vorige versie niet te verwijderen voordat u de nieuwe versie installeert).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>StorSimple Snapshot Manager doel en architectuur
StorSimple Snapshot Manager biedt een centrale beheer console die u kunt gebruiken voor het maken van consistente, tijdgebonden back-upkopieën van lokale en Cloud gegevens. U kunt de-console bijvoorbeeld gebruiken voor het volgende:

* Volumes configureren, back-ups maken en verwijderen.
* Configureer volume groepen om ervoor te zorgen dat back-upgegevens toepassings consistent zijn.
* Beheer het back-upbeleid zodat er een back-up van gegevens wordt gemaakt op basis van een vooraf bepaald schema.
* Lokale en Cloud momentopnamen maken, die kunnen worden opgeslagen in de Cloud en worden gebruikt voor herstel na nood gevallen.

De StorSimple-Snapshot Manager haalt de lijst op met toepassingen die zijn geregistreerd bij de VSS-provider op de host. Als u vervolgens toepassings consistente back-ups wilt maken, worden de volumes gecontroleerd die worden gebruikt door een toepassing en worden de volume groepen voorgesteld die moeten worden geconfigureerd. StorSimple Snapshot Manager gebruikt deze volume groepen voor het genereren van back-ups die toepassings consistent zijn. (Er is toepassings consistentie wanneer alle gerelateerde bestanden en data bases worden gesynchroniseerd en de werkelijke status van de toepassing op een bepaald moment worden weer gegeven.) 

StorSimple Snapshot Manager-back-ups nemen de vorm van incrementele moment opnamen, waarbij alleen de wijzigingen worden vastgelegd sinds de laatste back-up. Als gevolg hiervan moeten back-ups minder opslag ruimte hebben en snel kunnen worden gemaakt en hersteld. StorSimple Snapshot Manager maakt gebruik van de Windows-Volume Shadow Copy Service (VSS) om ervoor te zorgen dat moment opnamen toepassings consistente gegevens vastleggen. (Ga naar de sectie integratie met Windows Volume Shadow Copy Service voor meer informatie.) Met StorSimple-Snapshot Manager kunt u back-upschemas maken of direct back-ups nemen als dat nodig is. Als u gegevens wilt herstellen vanuit een back-up, kunt u in StorSimple Snapshot Manager selecteren uit een catalogus met lokale of Cloud momentopnamen. Azure StorSimple herstelt alleen de gegevens die nodig zijn om ervoor te zorgen dat er geen vertragingen in de beschik baarheid van gegevens tijdens herstel bewerkingen worden voor komen.)

![Architectuur van StorSimple-Snapshot Manager](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Architectuur van StorSimple-Snapshot Manager** 

## <a name="support-for-multiple-volume-types"></a>Ondersteuning voor meerdere volume typen
U kunt de StorSimple-Snapshot Manager gebruiken om de volgende typen volumes te configureren en hiervan een back-up te maken: 

* **Basis volumes** : een basis volume is een enkele partitie op een standaard schijf. 
* **Eenvoudige volumes** : een eenvoudig volume is een dynamisch volume dat schijf ruimte van één dynamische schijf bevat. Een eenvoudig volume bestaat uit één regio op een schijf of meerdere regio's die samen aan dezelfde schijf zijn gekoppeld. (U kunt eenvoudige volumes alleen op dynamische schijven maken.) Eenvoudige volumes zijn niet fout tolerant.
* **Dynamische volumes** : een dynamisch volume is een volume dat is gemaakt op een dynamische schijf. Dynamische schijven maken gebruik van een Data Base voor het bijhouden van informatie over volumes die zich op dynamische schijven in een computer bevinden. 
* **Dynamische volumes met mirroring** : dynamische volumes met spiegeling zijn gebaseerd op de RAID 1-architectuur. Bij RAID 1 worden identieke gegevens op twee of meer schijven geschreven, waardoor een gespiegelde set wordt geproduceerd. Een lees aanvraag kan vervolgens worden verwerkt door elke schijf die de aangevraagde gegevens bevat.
* **Gedeelde cluster volumes** : met csv's (cluster Shared volumes) kunnen meerdere knoop punten in een failovercluster tegelijkertijd op dezelfde schijf lezen of schrijven. Failover van het ene knoop punt naar een ander knoop punt kan snel optreden, zonder dat hiervoor een wijziging in het eigendom van het station is vereist of het koppelen, ontkoppelen en verwijderen van een volume. 

> [!IMPORTANT]
> Combi neer Csv's en niet-Csv's niet in dezelfde moment opname. Het combi neren van Csv's en niet-Csv's in een moment opname wordt niet ondersteund. 
> 
> 

U kunt StorSimple Snapshot Manager gebruiken om hele volume groepen te herstellen of afzonderlijke volumes te klonen en afzonderlijke bestanden te herstellen.

* [Volumes en volume groepen](#volumes-and-volume-groups) 
* [Back-uptypen en back-upbeleid](#backup-types-and-backup-policies) 

Zie [StorSimple Snapshot Manager gebruikers interface](storsimple-use-snapshot-manager.md)voor meer informatie over de functies van StorSimple Snapshot Manager en hoe u deze gebruikt.

## <a name="volumes-and-volume-groups"></a>Volumes en volume groepen
Met StorSimple Snapshot Manager maakt u volumes en configureert u ze vervolgens in volume groepen. 

StorSimple Snapshot Manager gebruikt volume groepen om back-ups te maken die toepassings consistent zijn. Toepassings consistentie bestaat wanneer alle gerelateerde bestanden en data bases worden gesynchroniseerd en de werkelijke status van een toepassing op een bepaald moment weer geven. Volume groepen (die ook wel *consistentie groepen* worden genoemd) vormen de basis van een back-up-of herstel taak.

Volume groepen zijn niet hetzelfde als volume containers. Een volume container bevat een of meer volumes die een account voor Cloud opslag en andere kenmerken delen, zoals versleuteling en bandbreedte gebruik. Eén volume container kan Maxi maal 256 Thin provisioned StorSimple-volumes bevatten. Ga voor meer informatie over volume containers naar [uw volume containers beheren](./storsimple-8000-manage-volume-containers.md). Volume groepen zijn verzamelingen van volumes die u configureert om back-upbewerkingen mogelijk te maken. Als u twee volumes selecteert die deel uitmaken van verschillende volume containers, plaatst u deze in één volume groep en maakt u vervolgens een back-upbeleid voor die volume groep. er wordt met behulp van het juiste opslag account een back-up van elk volume in de juiste volume container gemaakt.

> [!NOTE]
> Alle volumes in een volume groep moeten afkomstig zijn van één Cloud serviceprovider.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integratie met Windows Volume Shadow Copy Service
StorSimple Snapshot Manager maakt gebruik van de Windows Volume Shadow Copy Service (VSS) om toepassings consistente gegevens vast te leggen. VSS maakt toepassings consistentie mogelijk door te communiceren met VSS-bewuste toepassingen om het maken van incrementele moment opnamen te coördineren. VSS zorgt ervoor dat de toepassingen tijdelijk inactief zijn, of quiescent, wanneer moment opnamen worden gemaakt. 

De StorSimple-Snapshot Manager implementatie van VSS werkt met SQL Server en algemene NTFS-volumes. Het proces verloopt als volgt: 

1. Een aanvrager, meestal een oplossing voor gegevens beheer en-beveiliging (zoals StorSimple Snapshot Manager) of een back-uptoepassing, roept VSS aan en vraagt om informatie te verzamelen van de schrijver-software in de doel toepassing.
2. VSS neemt contact op met het schrijf onderdeel om een beschrijving van de gegevens op te halen. De schrijver retourneert de beschrijving van de gegevens waarvan een back-up moet worden gemaakt. 
3. VSS signaleert de schrijver om de toepassing voor te bereiden voor back-up. De schrijver bereidt de gegevens voor back-up voor, door het volt ooien van open trans acties, het bijwerken van transactie logboeken, enzovoort. vervolgens stuurt VSS een melding.
4. VSS geeft de schrijver de gegevens archieven van de toepassing tijdelijk te stoppen en ervoor te zorgen dat er geen gegevens naar het volume worden geschreven terwijl de schaduw kopie wordt gemaakt. Deze stap zorgt voor consistentie van de gegevens en duurt niet meer dan 60 seconden.
5. VSS geeft de provider de schaduw kopie te maken. Providers, die software of hardware kunnen zijn, beheren van de volumes die momenteel worden uitgevoerd en op aanvraag schaduw kopieën maken. De provider maakt de schaduw kopie en waarschuwt VSS wanneer deze is voltooid.
6. VSS neemt contact op met de schrijver om de toepassing te informeren dat I/O kan worden hervat en om te bevestigen dat I/O is onderbroken tijdens het maken van een schaduw kopie. 
7. Als het kopiëren is geslaagd, retourneert VSS de locatie van de Kopieer opdracht naar de aanvrager. 
8. Als er gegevens zijn geschreven terwijl de schaduw kopie werd gemaakt, wordt de back-up inconsistent. VSS verwijdert de schaduw kopie en waarschuwt de aanvrager. De aanvrager kan het back-upproces automatisch herhalen of de beheerder op de hoogte stellen om het op een later tijdstip opnieuw uit te voeren.

Zie de volgende afbeelding.

![VSS-proces](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Windows Volume Shadow Copy Service proces** 

## <a name="backup-types-and-backup-policies"></a>Back-uptypen en back-upbeleid
Met StorSimple Snapshot Manager kunt u een back-up maken van gegevens en deze lokaal en in de Cloud opslaan. U kunt StorSimple Snapshot Manager gebruiken om direct een back-up te maken van gegevens, maar u kunt ook een back-upbeleid gebruiken om een planning te creëren voor het automatisch nemen van back-ups. Met het back-upbeleid kunt u ook opgeven hoeveel moment opnamen er moeten worden bewaard. 

### <a name="backup-types"></a>Back-uptypen
U kunt StorSimple Snapshot Manager gebruiken om de volgende typen back-ups te maken:

* **Lokale moment** opnamen: lokale moment opnamen zijn tijdgebonden kopieën van volume gegevens die zijn opgeslagen op het StorSimple-apparaat. Normaal gesp roken kan dit type back-up snel worden gemaakt en hersteld. U kunt een lokale moment opname gebruiken, net zoals u een lokale back-up maakt.
* **Cloud momentopnamen** : Cloud momentopnamen zijn tijdgebonden kopieën van volume gegevens die in de Cloud zijn opgeslagen. Een Cloud momentopname is gelijk aan een moment opname die wordt gerepliceerd op een ander, off-site opslag systeem. Cloud momentopnamen zijn bijzonder handig in scenario's voor herstel na nood gevallen.

### <a name="on-demand-and-scheduled-backups"></a>Back-ups op aanvraag en gepland
Met StorSimple Snapshot Manager kunt u een eenmalige back-up starten die onmiddellijk moet worden gemaakt, of u kunt een back-upbeleid gebruiken om terugkerende back-upbewerkingen te plannen.

Een back-upbeleid is een set automatische regels die u kunt gebruiken voor het plannen van regel matige back-ups. Met een back-upbeleid kunt u de frequentie en para meters definiëren voor het maken van moment opnamen van een specifieke volume groep. U kunt beleids regels gebruiken om begin-en verval datums, tijden, frequenties en bewaar vereisten op te geven voor lokale en Cloud momentopnamen. Er wordt een beleid toegepast onmiddellijk nadat u het hebt gedefinieerd. 

U kunt StorSimple Snapshot Manager gebruiken om indien nodig een back-upbeleid te configureren of opnieuw te configureren. 

U configureert de volgende informatie voor elk back-upbeleid dat u maakt:

* **Naam** : de unieke naam van het geselecteerde back-upbeleid.
* **Type** : het type back-upbeleid; een lokale moment opname of Cloud momentopname.
* **Volume groep** : de volume groep waaraan het geselecteerde back-upbeleid is toegewezen.
* **Bewaren** : het aantal back-ups dat moet worden bewaard. Als **u dit selectie** vakje inschakelt, worden alle back-upkopieën bewaard totdat het maximum aantal back-ups per volume is bereikt, waarbij het beleid mislukt en er een fout bericht wordt gegenereerd. U kunt ook een aantal te bewaren back-ups (tussen 1 en 64) opgeven.
* **Datum** – de datum waarop het back-upbeleid is gemaakt.

Ga voor meer informatie over het configureren van het back-upbeleid naar [StorSimple gebruiken Snapshot Manager voor het maken en beheren van back-upbeleid](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Controle en beheer van de back-uptaak
U kunt de StorSimple-Snapshot Manager gebruiken om geplande, geplande en voltooide back-uptaken te controleren en te beheren. Daarnaast biedt StorSimple Snapshot Manager een catalogus van Maxi maal 64 voltooide back-ups. U kunt de catalogus gebruiken om volumes of afzonderlijke bestanden te zoeken en te herstellen. 

Ga voor meer informatie over het bewaken van back-uptaken naar [gebruik StorSimple Snapshot Manager om back-uptaken weer te geven en te beheren](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het gebruik van StorSimple Snapshot Manager voor het beheren van uw StorSimple-oplossing](storsimple-snapshot-manager-admin.md).
* Down load [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).