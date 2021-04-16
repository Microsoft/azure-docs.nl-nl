---
title: Architectuur van BareMetal-infrastructuur voor Oracle
description: Meer informatie over de architectuur van verschillende configuraties van BareMetal Infrastructure for Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 1bdc32c14cfc8986c52a4ea916a6130ee29e6028
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559054"
---
# <a name="architecture-of-baremetal-infrastructure-for-oracle"></a>Architectuur van BareMetal-infrastructuur voor Oracle

In dit artikel kijken we naar de architectuuropties voor de BareMetal-infrastructuur voor Oracle en de functies die elk ondersteunt.

## <a name="single-instance"></a>Eén exemplaar

Deze topologie ondersteunt één exemplaar van Oracle Database Oracle Data Guard voor migratie naar de BareMetal-infrastructuur. Het ondersteunt het gebruik van stand-by-knooppunt voor hoge beschikbaarheid en onderhoudswerkzaamheden.

[![Diagram met de architectuur van één exemplaar van Oracle Database Oracle Data Guard.](media/oracle-baremetal-architecture/single-instance-architecture.png)](media/oracle-baremetal-architecture/single-instance-architecture.png#lightbox)

## <a name="oracle-real-application-clusters-rac-one-node"></a>Oracle Real Application Clusters (RAC) One Node

Deze topologie ondersteunt een RAC-configuratie met gedeelde opslag en GRID-cluster. Database-exemplaren worden slechts op één knooppunt uitgevoerd (actief-passief-configuratie).

Functies zijn onder andere:

- Actief-passief met Oracle RAC One Node

    - Automatische fail-over

    - Snel opnieuw opstarten op tweede knooppunt

- Realtime fail-over en schaalbaarheid met Oracle RAC

- Geen uitvaltijd voor rolling onderhoud

[![Diagram met de architectuur van een Oracle RAC One Node actief-passief-configuratie.](media/oracle-baremetal-architecture/one-node-rac-architecture.png)](media/oracle-baremetal-architecture/one-node-rac-architecture.png#lightbox)

## <a name="rac"></a>Rac

Deze topologie ondersteunt een Oracle RAC-configuratie met gedeelde opslag en een grid-cluster terwijl meerdere exemplaren per database gelijktijdig worden uitgevoerd (actief-actief-configuratie).

- De prestaties zijn eenvoudig te schalen via online inrichten van toegevoegde servers. 
-  Gebruikers zijn actief op alle servers en alle servers hebben toegang tot dezelfde Oracle Database. 
-  Alle typen databaseonderhoud kunnen online of op rolling wijze worden uitgevoerd voor minimale of geen downtime. 
- Stand-bysystemen van Oracle Active Data Guard (ADG) kunnen eenvoudig als testsystemen voor twee doeleinden worden gebruikt. 

Met deze configuratie kunt u alle wijzigingen testen op een exacte kopie van de productiedatabase voordat ze worden toegepast op de productieomgeving.

> [!NOTE]
> Als u van plan bent om Active Data Guard Far Sync (synchrone modus) te gebruiken, moet u rekening houden met de regionale zones waar deze functie wordt ondersteund. Alleen voor geografisch gedistribueerde regio's raden we u aan Data Guard met asynchrone modus te gebruiken.

[![Diagram met de architectuur van een active-active-configuratie van Oracle RAC.](media/oracle-baremetal-architecture/rac-architecture.png)](media/oracle-baremetal-architecture/rac-architecture.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het inrichten van uw BareMetal-exemplaren voor Oracle-workloads.

> [!div class="nextstepaction"]
> [BareMetal-infrastructuur inrichten voor Oracle](oracle-baremetal-provision.md)

