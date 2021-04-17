---
title: Hoge beschikbaarheid en herstel na noodherstel voor Oracle op BareMetal
description: Meer informatie over hoge beschikbaarheid en herstel na nood Oracle op Azure BareMetal-infrastructuur.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: ab0337622eaa8c1368760fcbcd28533dacb3adce
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590259"
---
# <a name="high-availability-and-disaster-recovery-for-oracle-on-baremetal"></a>Hoge beschikbaarheid en herstel na noodherstel voor Oracle op BareMetal

In dit artikel worden de basisbeginselen van hoge beschikbaarheid en herstel na noodherstel beschreven. Vervolgens introduceren we hoe u hoge beschikbaarheid en herstel na noodherstel kunt bereiken in een Oracle-omgeving op de BareMetal-infrastructuur.

## <a name="high-availability-vs-disaster-recovery"></a>Hoge beschikbaarheid versus herstel na noodherstel

Zowel hoge beschikbaarheid als herstel na nood gevallen bieden dekking, maar van verschillende typen fouten. Ze gebruiken verschillende functies en opties van de Oracle Database.

Met hoge beschikbaarheid kan een systeem meerdere fouten ondervangen zonder dat dit van invloed is op de gebruikerservaring van de toepassing. Veelvoorkomende kenmerken van een systeem met hoge beschikbaarheid zijn onder andere:

- Redundante hardware zonder single point of failure.
- Automatisch herstel na niet-kritieke storingen, zoals defecte schijfstations of defecte netwerkkabels.
- De mogelijkheid om hardware en software te rolleren zonder merkbare gevolgen voor de verwerking.
- Voldoet aan of overschrijdt doelstellingen voor doelstellingen voor hersteltijd (RTO) en herstelpuntdoelstellingen (RPO).

De meest voorkomende functie van Oracle die wordt gebruikt voor hoge beschikbaarheid is [Oracle Real Application Clusters (RAC).](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92)

Herstel na noodherstel beschermt u tegen onherstelbare gelokaliseerde storingen die uw primaire strategie voor hoge beschikbaarheid zouden aanvallen. In het Oracle-ecosysteem wordt het geleverd via databasereplicatie, ook wel [bekend als Oracle Data Guard.](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over functies voor hoge beschikbaarheid voor Oracle:

> [!div class="nextstepaction"]
> [Functies voor hoge beschikbaarheid voor Oracle op BareMetal-infrastructuur](high-availability-features.md)
