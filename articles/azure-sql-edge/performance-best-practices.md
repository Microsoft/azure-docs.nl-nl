---
title: Aanbevolen procedures voor prestaties en configuratie richtlijnen-Azure SQL Edge
description: Meer informatie over best practices voor prestaties en richt lijnen voor configuratie in Azure SQL Edge
keywords: SQL-rand, gegevens retentie
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 02f22883a0989714d8b74f778cacf1ba2c65d0b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392007"
---
# <a name="performance-best-practices-and-configuration-guidelines"></a>Aanbevolen procedures voor prestaties en configuratie richtlijnen

Azure SQL Edge biedt verschillende functies en mogelijkheden die kunnen worden gebruikt voor het verbeteren van de prestaties van de implementatie van SQL Edge. In dit artikel worden enkele aanbevolen procedures en aanbevelingen geboden om de prestaties te maximaliseren. 

## <a name="best-practices"></a>Aanbevolen procedures 

### <a name="configure-multiple-tempdb-data-files"></a>Meerdere TempDB-gegevens bestanden configureren

In Azure SQL Edge wordt standaard slechts één TempDB-gegevens bestand gemaakt als onderdeel van de initialisatie van de container. U kunt overwegen om meerdere TempDB-gegevens bestanden te maken na de implementatie. Zie de richt lijnen in dit artikel voor meer informatie over het [beperken van toewijzings conflicten in SQL Server Data Base TempDB](https://support.microsoft.com/help/2154845/recommendations-to-reduce-allocation-contention-in-sql-server-tempdb-d).

### <a name="use-clustered-columnstore-indexes-where-possible"></a>Waar mogelijk geclusterde column Store-indexen gebruiken

IoT-en edge-apparaten genereren grote hoeveel heden gegevens die doorgaans worden geaggregeerd over een bepaald tijd venster voor analyse. Afzonderlijke gegevens rijen worden zelden gebruikt voor analyse doeleinden. Column Store-indexen zijn ideaal voor het opslaan en opvragen van dergelijke grote gegevens sets. In deze index wordt gebruikgemaakt van gegevens opslag en query verwerking op basis van kolommen om te zorgen voor Maxi maal tien keer de query prestaties op de traditionele rij-georiënteerde opslag. U kunt ook Maxi maal tien keer de gegevens compressie van de niet-gecomprimeerde gegevens grootte behaalt. Zie [Column Store-indexen](/sql/relational-databases/indexes/columnstore-indexes-overview) voor meer informatie.

Daarnaast kunnen andere functies van Azure SQL Edge, zoals het streamen van gegevens en gegevens retentie, profiteren van de column Store-optimalisaties voor het invoegen en verwijderen van gegevens. 

### <a name="simple-recovery-model"></a>Eenvoudig herstel model

Omdat opslag op edge-apparaten kan worden beperkt, gebruiken alle gebruikers databases in Azure SQL Edge standaard het eenvoudige herstel model. Het eenvoudige herstel model herstelt automatisch de logboek ruimte om ruimte vereisten te beperken, waardoor de transactie logboek ruimte niet meer hoeft te worden beheerd. Dit kan handig zijn op edge-apparaten met beperkte opslag ruimte. Zie [herstel modellen](/sql/relational-databases/backup-restore/recovery-models-sql-server) voor meer informatie over het eenvoudige herstel model en andere beschik bare herstel modellen

Bewerkingen zoals logboek verzending en tijdstippen voor herstel, waarvoor transactie logboek back-ups zijn vereist, worden niet ondersteund door het eenvoudige herstel model.  

## <a name="advanced-configuration"></a>Geavanceerde configuratie 

### <a name="setting-memory-limits"></a>Geheugen limieten instellen

Azure SQL Edge ondersteunt Maxi maal 64 GB geheugen voor de buffer groep, terwijl er mogelijk extra geheugen vereist is voor satelliet processen die worden uitgevoerd in de SQL Edge-container. Op kleinere apparaten met beperkt geheugen is het raadzaam om het beschik bare geheugen voor SQL Edge-containers te beperken, zodat de docker-host en andere rand processen of modules goed kunnen functioneren. Het totale geheugen dat beschikbaar is voor SQL Edge kan worden beheerd met behulp van de volgende mechanismen. 

- Geheugen beperken dat beschikbaar is voor de SQL Edge-containers: het totale beschik bare geheugen voor de SQL Edge-container kan worden beperkt met behulp van de container runtime-configuratie optie `--memory` . Zie [runtime options with Memory, cpu's and gpu's](https://docs.docker.com/config/containers/resource_constraints/)(Engelstalig) voor meer informatie over het beperken van geheugen dat beschikbaar is voor de SQL Edge-container.

- Geheugen beperken dat beschikbaar is voor SQL process binnen de container: het SQL-proces in de container gebruikt standaard slechts 80% van het fysieke RAM-geheugen dat beschikbaar is. Voor het meren deel van de implementaties is de standaard configuratie nauw keurig. Er kunnen echter scenario's zijn waarin extra geheugen nodig is voor het streamen van gegevens en de ONNX-processen die in de SQL Edge-containers worden uitgevoerd. In dergelijke scenario's kan het geheugen dat beschikbaar is voor het SQL-proces worden beheerd met behulp `memory.memorylimitmb` van de instelling in het bestand MSSQL-conf. Zie [configure using MSSQL. conf file](configure.md#configure-by-using-an-mssqlconf-file)(Engelstalig) voor meer informatie.

Zorg er bij het instellen van de geheugen limieten voor dat deze waarde niet te laag is ingesteld. Als u niet voldoende geheugen voor het SQL-proces hebt ingesteld, kan dit een ernstige invloed hebben op de SQL-prestaties.

### <a name="delayed-durability"></a>Uitgestelde duurzaamheid

Trans acties in Azure SQL Edge kunnen volledig duurzaam zijn, de SQL Server standaard of een vertraagd duurzaam (ook wel Lazy commit).

Volledig duurzame trans acties worden synchroon en worden door Voer als geslaagd gerapporteerd en de controle terugsturen naar de client pas nadat de logboek records voor de trans actie naar de schijf zijn geschreven. Vertraagde duurzame trans acties worden asynchroon uitgevoerd en het rapport wordt doorgevoerd als geslaagd voordat de logboek records voor de trans actie naar de schijf worden geschreven. Het schrijven van de transactie logboek vermeldingen naar de schijf is vereist voor een duurzame trans actie. Vertraagde duurzame trans acties worden duurzaam wanneer de transactie logboek vermeldingen worden leeg gemaakt op de schijf. 

In implementaties waar **gegevens verlies** kan worden toegestaan of op edge-apparaten met een trage opslag, kan een vertraagde duurzaamheid worden gebruikt om gegevens opname en op basis van gegevens retentie te optimaliseren. Zie voor meer informatie [controle transactie duurzaamheid](/sql/relational-databases/logs/control-transaction-durability).


### <a name="linux-os-configurations"></a>Configuraties van Linux-besturings systeem 

Overweeg de volgende configuratie-instellingen voor [Linux-besturings systeem](/sql/linux/sql-server-linux-performance-best-practices#linux-os-configuration) te gebruiken om de beste prestaties voor een SQL-installatie te ervaren.