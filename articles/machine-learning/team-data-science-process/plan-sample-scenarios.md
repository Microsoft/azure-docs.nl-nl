---
title: Scenario's identificeren voor Azure Machine Learning-team data Science process
description: Selecteer de juiste scenario's voor het uitvoeren van geavanceerde predictive analytics met het team data Science process.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 2d589d6c3394556499daf033c4c1d528a214b0e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93319301"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Scenario's voor geavanceerde analyses in Azure Machine Learning
In dit artikel vindt u een overzicht van de verschillende voorbeeld gegevens bronnen en doel scenario's die kunnen worden verwerkt door het [team data Science process (TDSP)](overview.md). De TDSP biedt een systematische aanpak voor teams om samen te werken aan het bouwen van intelligente toepassingen. De scenario's die hier worden weer gegeven, illustreren de beschik bare opties in de werk stroom voor gegevens verwerking die afhankelijk is van de gegevens kenmerken, bron locaties en doel opslagplaatsen in Azure.

De **beslissings structuur** voor het selecteren van de voorbeeld scenario's die geschikt zijn voor uw gegevens en doel stellingen wordt weer gegeven in de laatste sectie.

In elk van de volgende secties wordt een voorbeeld scenario weer gegeven. Voor elk scenario worden een mogelijke gegevens Science of geavanceerde analyse stroom en ondersteunende Azure-resources weer gegeven.

> [!NOTE]
> **Voor alle volgende scenario's moet u:**
> <br/>
> 
> * [Een opslagaccount maken](../../storage/common/storage-account-create.md)
>   <br/>
> * [Een Azure Machine Learning-werk ruimte maken](../classic/create-workspace.md)
> 
> 

## <a name="scenario-1-small-to-medium-tabular-dataset-in-local-files"></a><a name="smalllocal"></a>Scenario \# 1: gegevensset in tabel vorm van klein naar gemiddeld in lokale bestanden
![Lokale bestanden van kleine tot middel groot][1]

#### <a name="additional-azure-resources-none"></a>Extra Azure-resources: geen
1. Meld u aan bij de [Azure machine learning Studio](https://studio.azureml.net/).
1. Upload een gegevensset.
1. Bouw een Azure Machine Learning-experiment stroom op basis van geüploade gegevensset (s).

## <a name="scenario-2-small-to-medium-dataset-of-local-files-that-require-processing"></a><a name="smalllocalprocess"></a>Scenario \# 2: kleine tot gemiddelde gegevensset van lokale bestanden die moeten worden verwerkt
![Kleine tot middel grote lokale bestanden met verwerking][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Aanvullende Azure-resources: Azure virtual machine (IPython notebook server)
1. Maak een virtuele Azure-machine met IPython-notebook.
1. Gegevens uploaden naar een Azure Storage-container.
1. Gegevens vooraf verwerken en opschonen in IPython notebook, toegang krijgen tot gegevens uit Azure Storage container.
1. Gegevens transformeren naar een gereinigd tabellaire formulier.
1. Getransformeerde gegevens opslaan in azure-blobs.
1. Meld u aan bij de [Azure machine learning Studio](https://studio.azureml.net/).
1. De gegevens uit Azure-blobs lezen met de module [gegevens importeren][import-data] .
1. Bouw een Azure Machine Learning-experiment flow die begint met opgenomen gegevensset (s).

## <a name="scenario-3-large-dataset-of-local-files-targeting-azure-blobs"></a><a name="largelocal"></a>Scenario \# 3: grote gegevensset van lokale bestanden, met als doel Azure blobs
![Grote lokale bestanden][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Aanvullende Azure-resources: Azure virtual machine (IPython notebook server)
1. Maak een virtuele Azure-machine met IPython-notebook.
1. Gegevens uploaden naar een Azure Storage-container.
1. Gegevens vooraf verwerken en opschonen in IPython notebook, toegang krijgen tot gegevens uit Azure-blobs.
1. Transformeer gegevens naar een gereinigd tabellaire formulier, indien nodig.
1. Gegevens verkennen en zo nodig functies maken.
1. Pak een voor beeld van kleine tot middel grote gegevens uit.
1. Sla de voorbeeld gegevens op in azure-blobs.
1. Meld u aan bij de [Azure machine learning Studio](https://studio.azureml.net/).
1. De gegevens uit Azure-blobs lezen met de module [gegevens importeren][import-data] .
1. Bouw Azure Machine Learning-experiment flow die begint met opgenomen gegevensset (s).

## <a name="scenario-4-small-to-medium-dataset-of-local-files-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="smalllocaltodb"></a>Scenario \# 4: kleine tot middel grote gegevensset van lokale bestanden, gericht SQL Server op een virtuele machine van Azure
![Kleine tot middel grote lokale bestanden naar SQL data base in azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Aanvullende Azure-resources: Azure virtual machine (SQL Server/IPython notebook server)
1. Maak een virtuele machine van Azure met SQL Server + IPython-notebook.
1. Gegevens uploaden naar een Azure Storage-container.
1. Gegevens vooraf verwerken en opschonen in Azure Storage container met behulp van IPython notebook.
1. Transformeer gegevens naar een gereinigd tabellaire formulier, indien nodig.
1. Gegevens opslaan naar VM-lokale bestanden (IPython-notebook wordt uitgevoerd op de VM, lokale schijven verwijzen naar VM-stations).
1. Gegevens laden naar SQL Server Data Base die wordt uitgevoerd op een virtuele Azure-machine.
   
   Optie \# 1: gebruiken van SQL Server Management Studio.
   
   * Meld u aan bij SQL Server VM
   * Voer SQL Server Management Studio uit.
   * Maak data base-en doel tabellen.
   * Gebruik een van de methoden voor bulk import voor het laden van de gegevens van virtuele machine-lokale bestanden.
   
   Optie \# 2: het gebruik van IPython notebook: niet aanbevolen voor middel grote en grotere gegevens sets
   
   <!-- -->    
   * Gebruik ODBC-connection string om toegang te krijgen tot SQL Server op VM.
   * Maak data base-en doel tabellen.
   * Gebruik een van de methoden voor bulk import voor het laden van de gegevens van virtuele machine-lokale bestanden.
1. Verken gegevens, Maak onderdelen waar nodig. De functies hoeven niet te worden gerealiseerd in de database tabellen. Let alleen op de benodigde query om ze te maken.
1. Bepaal indien nodig en/of gewenst een steekproef grootte.
1. Meld u aan bij de [Azure machine learning Studio](https://studio.azureml.net/).
1. Lees de gegevens rechtstreeks vanuit het SQL Server met behulp van de module [gegevens importeren][import-data] . Plak de benodigde query waarmee velden worden geëxtraheerd, maakt functies en voor beelden van gegevens, indien dit rechtstreeks nodig is in de query [gegevens importeren][import-data] .
1. Bouw Azure Machine Learning-experiment flow die begint met opgenomen gegevensset (s).

## <a name="scenario-5-large-dataset-in-local-files-target-sql-server-in-azure-vm"></a><a name="largelocaltodb"></a>Scenario \# 5: grote gegevensset in lokale bestanden, doel SQL Server in azure VM
![Grote lokale bestanden naar SQL data base in azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Aanvullende Azure-resources: Azure virtual machine (SQL Server/IPython notebook server)
1. Maak een virtuele machine van Azure met SQL Server-en IPython-notebook server.
1. Gegevens uploaden naar een Azure Storage-container.
1. Beschrijving Gegevens vooraf verwerken en opschonen.
   
    a.  Gegevens vooraf verwerken en opschonen in IPython notebook, toegang krijgen tot gegevens uit Azure-blobs.
   
    b.  Transformeer gegevens naar een gereinigd tabellaire formulier, indien nodig.
   
    c.  Gegevens opslaan naar VM-lokale bestanden (IPython-notebook wordt uitgevoerd op de VM, lokale schijven verwijzen naar VM-stations).
1. Gegevens laden naar SQL Server Data Base die wordt uitgevoerd op een virtuele Azure-machine.
   
    a.  Meld u aan bij SQL Server VM.
   
    b.  Als er nog geen gegevens zijn opgeslagen, downloadt u de gegevens bestanden van de Azure-opslag container naar de map Local-VM.
   
    c.  Voer SQL Server Management Studio uit.
   
    d.  Maak data base-en doel tabellen.
   
    e.  Gebruik een van de methoden voor bulk import om de gegevens te laden.
   
    f.  Als er verbinding moet worden gemaakt met een tabel, maakt u indexen om samen voegingen te versnellen.
   
   > [!NOTE]
   > Voor een snellere belasting van grote gegevens grootten is het raadzaam om gepartitioneerde tabellen te maken en de gegevens parallel te importeren. Zie voor meer informatie [parallelle gegevens importeren naar SQL-gepartitioneerde tabellen](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Verken gegevens, Maak onderdelen waar nodig. De functies hoeven niet te worden gerealiseerd in de database tabellen. Let alleen op de benodigde query om ze te maken.
1. Bepaal indien nodig en/of gewenst een steekproef grootte.
1. Meld u aan bij de [Azure machine learning Studio](https://studio.azureml.net/).
1. Lees de gegevens rechtstreeks vanuit het SQL Server met behulp van de module [gegevens importeren][import-data] . Plak de benodigde query waarmee velden worden geëxtraheerd, maakt functies en voor beelden van gegevens, indien dit rechtstreeks nodig is in de query [gegevens importeren][import-data] .
1. Eenvoudige Azure Machine Learning experiment flow, te beginnen met geüploade gegevensset

## <a name="scenario-6-large-dataset-in-a-sql-server-database-on-premises-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="largedbtodb"></a>Scenario \# 6: grote gegevensset in een SQL server data base on-premises, gericht op SQL Server op een virtuele machine van Azure
![Grote SQL-data base on-premises naar SQL data base in azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Aanvullende Azure-resources: Azure virtual machine (SQL Server/IPython notebook server)
1. Maak een virtuele machine van Azure met SQL Server-en IPython-notebook server.
1. Gebruik een van de methoden voor het exporteren van gegevens om de gegevens van SQL Server naar dump bestanden te exporteren.
   
   > [!NOTE]
   > Als u besluit alle gegevens van de on-premises data base te verplaatsen, een alternatieve (snellere) methode om de volledige data base te verplaatsen naar het SQL Server-exemplaar in Azure. Sla de stappen voor het exporteren van gegevens, het maken van de data base en het laden/importeren van gegevens naar de doel database over en volg de alternatieve methode.
   > 
   > 
1. Upload dump bestanden naar Azure Storage-container.
1. Laad de gegevens naar een SQL Server-Data Base die wordt uitgevoerd op een virtuele Azure-machine.
   
   a.  Meld u aan bij de SQL Server VM.
   
   b.  Down load gegevens bestanden van een Azure Storage-container naar de map Local-VM.
   
   c.  Voer SQL Server Management Studio uit.
   
   d.  Maak data base-en doel tabellen.
   
   e.  Gebruik een van de methoden voor bulk import om de gegevens te laden.
   
   f.  Als er verbinding moet worden gemaakt met een tabel, maakt u indexen om samen voegingen te versnellen.
   
   > [!NOTE]
   > Voor een snellere belasting van grote gegevens grootten, maakt u gepartitioneerde tabellen en kunt u de gegevens parallel bulksgewijs importeren. Zie voor meer informatie [parallelle gegevens importeren naar SQL-gepartitioneerde tabellen](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Verken gegevens, Maak onderdelen waar nodig. De functies hoeven niet te worden gerealiseerd in de database tabellen. Let alleen op de benodigde query om ze te maken.
1. Bepaal indien nodig en/of gewenst een steekproef grootte.
1. Meld u aan bij de [Azure machine learning Studio](https://studio.azureml.net/).
1. Lees de gegevens rechtstreeks vanuit het SQL Server met behulp van de module [gegevens importeren][import-data] . Plak de benodigde query waarmee velden worden geëxtraheerd, maakt functies en voor beelden van gegevens, indien dit rechtstreeks nodig is in de query [gegevens importeren][import-data] .
1. Eenvoudige Azure Machine Learning experiment flow, te beginnen met geüploade gegevensset.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Alternatieve methode voor het kopiëren van een volledige data base van een on-premises SQL Server naar Azure SQL Database
![Lokale data base loskoppelen en koppelen aan SQL-data base in azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Aanvullende Azure-resources: Azure virtual machine (SQL Server/IPython notebook server)
Als u de hele SQL Server data base in uw SQL Server virtuele machine wilt repliceren, moet u een Data Base van de ene locatie/server naar een andere kopiëren, ervan uitgaande dat de data base tijdelijk offline kan worden gezet. U kunt SQL Server Management Studio Objectverkenner gebruiken of gebruikmaken van de equivalente Transact-SQL-opdrachten.

1. Ontkoppel de Data Base op de bron locatie. Zie [een Data Base loskoppelen](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx)voor meer informatie.
1. Kopieer in het Windows Verkenner-of Windows-opdracht prompt venster het losgekoppelde database bestand of bestanden en logboek bestand of-bestanden naar de doel locatie op de SQL Server VM in Azure.
1. Koppel de gekopieerde bestanden aan het doel SQL Server exemplaar. Zie [een Data Base koppelen](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx)voor meer informatie.

[Een Data Base verplaatsen met Detach en attach (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="scenario-7-big-data-in-local-files-target-hive-database-in-azure-hdinsight-hadoop-clusters"></a><a name="largedbtohive"></a>Scenario \# 7: Big data in lokale bestanden, target Hive data base in azure HDInsight Hadoop clusters
![Big data in lokale doel Hive][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Aanvullende Azure-resources: Azure HDInsight Hadoop cluster en virtuele Azure-machine (IPython notebook server)
1. Maak een virtuele machine van Azure met IPython notebook server.
1. Een Azure HDInsight Hadoop-cluster maken.
1. Beschrijving Gegevens vooraf verwerken en opschonen.
   
   a.  Gegevens vooraf verwerken en opschonen in IPython notebook, toegang krijgen tot gegevens uit Azure-blobs.
   
   b.  Transformeer gegevens naar een gereinigd tabellaire formulier, indien nodig.
   
   c.  Gegevens opslaan naar VM-lokale bestanden (IPython-notebook wordt uitgevoerd op de VM, lokale schijven verwijzen naar VM-stations).
1. Gegevens uploaden naar de standaard container van het Hadoop-cluster dat is geselecteerd in stap 2.
1. Gegevens laden naar Hive-data base in Azure HDInsight Hadoop cluster.
   
   a.  Meld u aan bij het hoofd knooppunt van het Hadoop-cluster
   
   b.  Open de Hadoop-opdracht regel.
   
   c.  Voer de Hive-hoofd directory in met de opdracht `cd %hive_home%\bin` in de Hadoop-opdracht regel.
   
   d.  Voer de Hive-query's uit voor het maken van data bases en tabellen en het laden van gegevens uit Blob Storage naar Hive-tabellen.
   
   > [!NOTE]
   > Als de gegevens groot zijn, kunnen gebruikers de Hive-tabel maken met partities. Vervolgens kunnen gebruikers een `for` lus in de Hadoop-opdracht regel op het hoofd knooppunt gebruiken om gegevens te laden in de Hive-tabel die is gepartitioneerd per partitie.
   > 
   > 
1. Verken gegevens en maak indien nodig functies in de Hadoop-opdracht regel. De functies hoeven niet te worden gerealiseerd in de database tabellen. Let alleen op de benodigde query om ze te maken.
   
   a.  Meld u aan bij het hoofd knooppunt van het Hadoop-cluster
   
   b.  Open de Hadoop-opdracht regel.
   
   c.  Voer de Hive-hoofd directory in met de opdracht `cd %hive_home%\bin` in de Hadoop-opdracht regel.
   
   d.  Voer de Hive-query's uit op de Hadoop-opdracht regel op het hoofd knooppunt van het Hadoop-cluster om de gegevens te verkennen en zo nodig functies te maken.
1. Als dat nodig is en/of gewenst, worden de gegevens in de Azure Machine Learning Studio aangepast.
1. Meld u aan bij de [Azure machine learning Studio](https://studio.azureml.net/).
1. Lees de gegevens rechtstreeks vanuit de `Hive Queries` module [gegevens importeren][import-data] . Plak de benodigde query waarmee velden worden geëxtraheerd, maakt functies en voor beelden van gegevens, indien dit rechtstreeks nodig is in de query [gegevens importeren][import-data] .
1. Eenvoudige Azure Machine Learning experiment flow, te beginnen met geüploade gegevensset.

## <a name="decision-tree-for-scenario-selection"></a><a name="decisiontree"></a>Beslissings structuur voor scenario selectie
---
In het volgende diagram ziet u een overzicht van de scenario's die hierboven worden beschreven en de opties voor geavanceerd analyse proces en technologie die u hebt gemaakt voor elk van de scenario's waarin u zich kunt voordoen. Gegevens verwerking, exploratie, functie techniek en-bemonstering kunnen plaatsvinden in een of meer methoden/omgevingen, op bron-, tussen-en/of doel omgevingen, en kan zo nodig worden voortgezet. Het diagram fungeert alleen als een illustratie van een aantal mogelijke stromen en biedt geen uitgebreide opsomming.

![Scenario's voor voor beeld van een DS-proces scenario][8]

### <a name="advanced-analytics-in-action-examples"></a>Geavanceerde analyses in voor beelden van acties
Zie voor end-to-end Azure Machine Learning-scenario's die gebruikmaken van het geavanceerde analyse proces en technologie met open bare gegevens sets:

* [Team data Science process in actie: met behulp van SQL Server](sql-walkthrough.md).
* [Proces voor team data Science in actie: HDInsight Hadoop-clusters gebruiken](hive-walkthrough.md).

[1]: ./media/plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data