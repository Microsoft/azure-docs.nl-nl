---
title: Voor delen van het gebruik van Azure NetApp Files met Oracle Database | Microsoft Docs
description: Beschrijft de technologie en biedt een prestatie vergelijking tussen Oracle direct NFS (dNFS) en de traditionele NFS-client. Toont de voor delen van het gebruik van dNFS met Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: b-juche
ms.openlocfilehash: 0f431015caf5bb3364fe5628a599f760c6318c47
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91932495"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Voordelen van het gebruik van Azure NetApp Files met Oracle Database

Oracle direct NFS (dNFS) maakt het mogelijk hogere prestaties te verzorgen dan het eigen NFS-stuur programma van het besturings systeem. In dit artikel wordt de technologie uitgelegd en wordt een prestatie vergelijking geboden tussen dNFS en de traditionele NFS-client (kernel NFS). Het bevat ook de voor delen en het gemak van het gebruik van dNFS met Azure NetApp Files.  

## <a name="how-oracle-direct-nfs-works"></a>Hoe Oracle direct NFS werkt

In het volgende overzicht wordt uitgelegd hoe Oracle direct NFS op een hoog niveau werkt:

* Oracle direct NFS omzeilt de buffer cache van het besturings systeem. Gegevens worden slechts eenmaal in de cache opgeslagen in de gebruikers ruimte, waardoor de overhead van geheugen kopieën wordt geëlimineerd.  

* De traditionele NFS-client maakt gebruik van één netwerk stroom zoals hieronder wordt weer gegeven:    

    ![Traditionele NFS-client met één netwerk stroom](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

    Oracle direct NFS verbetert de prestaties verder door netwerk verkeer te verdelen over meerdere netwerk stromen. Zoals hieronder getest en weer gegeven, zijn 650 afzonderlijke netwerk verbindingen dynamisch tot stand gebracht door de Oracle Database:  

    ![Oracle direct NFS verbetert de prestaties](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

[In de veelgestelde vragen over Oracle voor direct NFS](http://www.orafaq.com/wiki/Direct_NFS) ziet u dat Oracle dNFS een GEOPTIMALISEERDe NFS-client is. Het biedt snelle en schaal bare toegang tot NFS-opslag die zich bevindt op NAS-opslag apparaten (toegankelijk via TCP/IP). dNFS is in de data base-kernel ingebouwd, net als ASM, dat voornamelijk wordt gebruikt met DAS-of SAN-opslag. Daarom *is het een richt lijn om dNFS te gebruiken bij het implementeren van NAS-opslag en ASM te gebruiken bij het implementeren van San-opslag.*

dNFS is de standaard optie in Oracle 18c.

dNFS is beschikbaar vanaf Oracle Database 11g. In het onderstaande diagram worden dNFS met systeem eigen NFS vergeleken. Wanneer u dNFS gebruikt, kan een Oracle-data base die wordt uitgevoerd op een virtuele machine van Azure, meer I/O-schijven hebben dan de systeem eigen NFS-client.

![Oracle en Azure NetApp Files vergelijking van dNFS met systeem eigen NFS](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

U kunt dNFS in-of uitschakelen door twee opdrachten uit te voeren en de data base opnieuw op te starten.

In te scha kelen:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

Uitschakelen:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Azure NetApp Files gecombineerd met Oracle direct NFS

U kunt de prestaties van Oracle-dNFS verbeteren met de Azure NetApp Files-service. De service geeft u de volledige controle over de prestaties van uw toepassing. Dit kan voldoen aan zeer veeleisende toepassingen. De combi natie van Oracle dNFS met Azure NetApp Files biedt geweldige voor delen voor uw workloads.

## <a name="next-steps"></a>Volgende stappen

- [Oplossingsarchitecturen op basis van Azure NetApp Files](azure-netapp-files-solution-architectures.md)
- [Overzicht van Oracle-toepassingen en-oplossingen op Azure](../virtual-machines/workloads/oracle/oracle-overview.md)