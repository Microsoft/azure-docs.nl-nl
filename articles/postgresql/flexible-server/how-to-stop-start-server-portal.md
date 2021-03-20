---
title: Stoppen/starten-Azure Portal-Azure Database for PostgreSQL flexibele server
description: In dit artikel wordt beschreven hoe u bewerkingen in Azure Database for PostgreSQL kunt stoppen/starten via de Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 4c393e0048a0058ebe0fbf2b0ee65f6ae2e184c5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90935888"
---
# <a name="stopstart-an-azure-database-for-postgresql---flexible-server-preview"></a>Een Azure Database for PostgreSQL-flexibele server stoppen/starten (preview-versie)

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibele server is momenteel beschikbaar als preview-versie.

Dit artikel bevat stapsgewijze instructies om een flexibele server te stoppen en te starten.

## <a name="pre-requisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

-   U moet een Azure Database for PostgreSQL flexibele server hebben.

## <a name="stop-a-running-server"></a>Een actieve server stoppen

1.  Kies in het [Azure Portal](https://portal.azure.com/)de flexibele server die u wilt stoppen.

2.  Klik op de pagina **overzicht** op de knop **stoppen** op de werk balk.

> [!NOTE]
> Zodra de server is gestopt, zijn andere beheer bewerkingen niet beschikbaar voor de flexibele server.

Houd er rekening mee dat gestopt servers na zeven dagen automatisch opnieuw worden gestart. Eventuele onderhouds updates worden toegepast wanneer de server de volgende keer wordt gestart.

## <a name="start-a-stopped-server"></a>Een gestopt server starten

1.  Kies in het [Azure Portal](https://portal.azure.com/)de flexibele server die u wilt starten.

2.  Klik op de pagina **overzicht** op de knop **Start** op de werk balk.

> [!NOTE]
> Zodra de server is gestart, zijn alle beheer bewerkingen nu beschikbaar voor de flexibele server.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [berekenings-en opslag opties in azure database for PostgreSQL flexibele server](./concepts-compute-storage.md).
