---
title: 'Quick Start: een basislaag Server groep maken-grootschalige (Citus)-Azure Database for PostgreSQL'
description: Ga aan de slag met de Azure Database for PostgreSQL grootschalige (Citus) Basic-laag.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 04/07/2021
ms.openlocfilehash: 07ace217e5299662a1c3145a225abc25f4f1f337
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024067"
---
# <a name="create-a-hyperscale-citus-basic-tier-server-group-in-the-azure-portal"></a>Maak een grootschalige (Citus) Basic-laag Server groep in de Azure Portal

Azure Database for PostgreSQL-grootschalige (Citus) is een beheerde service waarmee u Maxi maal beschik bare PostgreSQL-data bases in de cloud kunt uitvoeren, beheren en schalen. De [Basic-laag](concepts-hyperscale-tiers.md) is een handige implementatie optie voor de eerste ontwikkeling en tests.

In deze Quick start ziet u hoe u een grootschalige (Citus) Basic-laag groep maakt met behulp van de Azure Portal. U gaat de Server groep inrichten en controleren of u er verbinding mee kunt maken om query's uit te voeren.

> [!IMPORTANT]
> De grootschalige (Citus) Basic-laag is momenteel beschikbaar als preview-versie.  Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
>
> U kunt een volledige lijst met andere nieuwe functies bekijken in [Preview-functies voor grootschalige (Citus)](hyperscale-preview-features.md).

[!INCLUDE [azure-postgresql-hyperscale-create-basic-tier](../../includes/azure-postgresql-hyperscale-create-basic-tier.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Hyperscale (Citus)-servergroep inricht. U hebt psql gebruikt om hiermee verbinding te maken, u hebt een schema gemaakt en u hebt gegevens gedistribueerd.

- Volg hierna een [zelfstudie voor het bouwen van schaalbare toepassingen met meerdere tenants](./tutorial-design-database-hyperscale-multi-tenant.md)
- Bepaal de beste [begingrootte](howto-hyperscale-scale-initial.md) voor uw servergroep
