---
title: Ondersteunde versies post gres met Azure Arc enabled PostgreSQL grootschalige
description: Ondersteunde versies post gres met Azure Arc enabled PostgreSQL grootschalige
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0f3f9e7f4566800bdea6871cae1c5bafb3b37b95
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90936086"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>Ondersteunde versies van Postgres met PostgreSQL Hyperscale ingeschakeld op Azure Arc

In dit artikel wordt uitgelegd welke versies van post gres beschikbaar zijn met Azure Arc enabled PostgreSQL grootschalige.
De lijst met ondersteunde versies wordt na verloop van tijd gegroeid. De belangrijkste versies die worden ondersteund, zijn vandaag:
- Post gres 12 (standaard)
- Post gres 11

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>Hoe kan ik kiezen tussen versies?
Het is raadzaam om te kijken naar de versies waarvoor uw toepassingen zijn ontworpen en wat de mogelijkheden van elk van de versies zijn. Lees voor meer informatie over elke versie op de officiële post gres-site:
- [Post gres 12 (standaard)](https://www.postgresql.org/docs/12/index.html)
- [Post gres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>Hoe maak ik een bepaalde versie in azure Arc enabled PostgreSQL grootschalige?
Tijdens het maken hebt u de mogelijkheid om te bepalen welke versie moet worden gemaakt door de para meter _--engine-version_ door te geven. Als u geen versie gegevens opgeeft, wordt standaard een server groep van post gres versie 12 gemaakt.

## <a name="how-do-be-notified-when-other-versions-are-available"></a>Hoe kan ik een melding ontvangen wanneer er andere versies beschikbaar zijn?
Kom terug en lees dit artikel. Het wordt zo nodig bijgewerkt. U kunt ook het soort aangepaste resource definities (CRD) in de Arc-gegevens controller in uw Kubernetes-cluster weer geven.
Voer de volgende opdracht uit:
```console
kubectl get crds
```

Er wordt een uitvoer geretourneerd zoals:
```console
NAME                                            CREATED AT
datacontrollers.arcdata.microsoft.com           2020-08-31T20:15:16Z
postgresql-11s.arcdata.microsoft.com            2020-08-31T20:15:16Z
postgresql-12s.arcdata.microsoft.com            2020-08-31T20:15:16Z
sqlmanagedinstances.sql.arcdata.microsoft.com   2020-08-31T20:15:16Z
```

In dit voor beeld geeft deze uitvoer aan dat er twee soorten CRD zijn die verwant zijn aan post gres:
- postgresql-12s.arcdata.microsoft.com is de CRD voor post gres 12
- postgresql-11s.arcdata.microsoft.com is de CRD voor post gres 11

Dit zijn CRDs, geen Server groepen. De aanwezigheid van een CRD is geen indicatie dat u een server groep met die versie hebt of niet hebt gemaakt.
De CRD is een indicatie van het soort resources dat kan worden gemaakt.

## <a name="next-steps"></a>Volgende stappen:
- [Meer informatie over het maken van Azure Arc enabled PostgreSQL grootschalige](create-postgresql-hyperscale-server-group.md)
- [Meer informatie over het ophalen van een lijst met de Azure-PostgreSQL grootschalige-Server groepen die zijn gemaakt in uw Arc-gegevens controller](list-server-groups-postgres-hyperscale.md)
