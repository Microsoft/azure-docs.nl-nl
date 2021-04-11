---
title: Lees replica's beheren-Azure Portal-Azure Database for PostgreSQL-grootschalige (Citus)
description: Meer informatie over het beheren van Lees replica's Azure Database for PostgreSQL-grootschalige (Citus) van de Azure Portal.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 4d3a88692da6d1fc78e96a6261d42d3ca97b0dd0
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023984"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---hyperscale-citus-from-the-azure-portal"></a>Maak en beheer Lees replica's in Azure Database for PostgreSQL-grootschalige (Citus) van de Azure Portal

> [!IMPORTANT]
> Het lezen van replica's in grootschalige (Citus) zijn momenteel beschikbaar als preview-versie. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
>
> U kunt een volledige lijst met andere nieuwe functies bekijken in [Preview-functies voor grootschalige (Citus)](hyperscale-preview-features.md).

In dit artikel leert u hoe u in grootschalige (Citus) Lees replica's maakt en beheert via de Azure Portal. Zie het [overzicht](concepts-hyperscale-read-replicas.md)voor meer informatie over het lezen van replica's.


## <a name="prerequisites"></a>Vereisten

Een [grootschalige (Citus)-Server groep](quickstart-create-hyperscale-portal.md) die als primair moet worden beschouwd.

## <a name="create-a-read-replica"></a>Een leesreplica maken

Voer de volgende stappen uit om een lees replica te maken:

1. Selecteer een bestaande Azure Database for PostgreSQL-Server groep die u als primair wilt gebruiken. 

2. Selecteer op de zijbalk Server groep onder **beheer van Server groep** de optie **replicatie**.

3. Selecteer **replica toevoegen**.

4. Voer een naam in voor de Lees replica. 

5. Selecteer **OK** om te bevestigen dat de replica is gemaakt.

Nadat de Lees replica is gemaakt, kan deze vanuit het venster **replicatie** worden weer gegeven.

> [!IMPORTANT]
>
> Raadpleeg de [sectie overwegingen in het overzicht van het lezen van replica's](concepts-hyperscale-read-replicas.md#considerations).
>
> Voordat de instelling van een primaire server groep is bijgewerkt naar een nieuwe waarde, werkt u de replica-instelling bij naar een gelijke of hogere waarde. Met deze actie wordt de replica zo aangepast dat er wijzigingen in de master worden aangebracht.

## <a name="delete-a-primary-server-group"></a>Een primaire server groep verwijderen

Als u een primaire server groep wilt verwijderen, gebruikt u dezelfde stappen als voor het verwijderen van een zelfstandige grootschalige-Server groep (Citus). 

> [!IMPORTANT]
>
> Wanneer u een primaire server groep verwijdert, wordt replicatie naar alle Lees replica's gestopt. De Lees replica's worden zelfstandige server groepen die nu zowel lees-als schrijf bewerkingen ondersteunen.

Voer de volgende stappen uit om een server groep te verwijderen uit de Azure Portal:

1. Selecteer in de Azure Portal uw primaire Azure Database for PostgreSQL-Server groep.

2. Open de pagina **overzicht** voor de Server groep. Selecteer **Verwijderen**.
 
3. Voer de naam in van de primaire server groep die u wilt verwijderen. Selecteer **verwijderen** om de verwijdering van de primaire server groep te bevestigen.
 

## <a name="delete-a-replica"></a>Een replica verwijderen

U kunt een lees replica verwijderen op dezelfde manier als bij het verwijderen van een primaire server groep.

- Open in de Azure Portal de pagina **overzicht** voor de Lees replica. Selecteer **Verwijderen**.
 
U kunt ook de replica lezen uit het venster **replicatie** verwijderen door de volgende stappen uit te voeren:

1. Selecteer in de Azure Portal uw primaire grootschalige (Citus)-Server groep.

2. Selecteer in het menu Server groep onder **beheer van Server groep** de optie **replicatie**.

3. Selecteer de replica lezen die u wilt verwijderen.
 
4. Selecteer **replica verwijderen**.
 
5. Voer de naam in van de replica die u wilt verwijderen. Selecteer **verwijderen** om te bevestigen dat de replica moet worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het lezen van replica's in azure database for PostgreSQL-grootschalige (Citus)](concepts-hyperscale-read-replicas.md).
