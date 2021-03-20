---
title: Gebruikers maken-grootschalige (Citus)-Azure Database for PostgreSQL
description: In dit artikel wordt beschreven hoe u nieuwe gebruikers accounts kunt maken om te communiceren met een Azure Database for PostgreSQL-grootschalige (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/8/2019
ms.openlocfilehash: 3d23ee6119b625e11ce44bb9ad11ce4b3ee0280d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91295733"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Gebruikers maken in Azure Database for PostgreSQL-grootschalige (Citus)

> [!NOTE]
> De term ' gebruikers ' verwijst naar gebruikers in een grootschalige (Citus)-Server groep. Ga naar het [artikel over toegangs beheer op basis van rollen (Azure RBAC)](../role-based-access-control/built-in-roles.md) van Azure of Lees [hoe u rollen kunt aanpassen](../role-based-access-control/custom-roles.md)voor meer informatie over Azure-abonnements gebruikers en hun bevoegdheden.

## <a name="the-server-admin-account"></a>Het beheerdersaccount voor de server

De PostgreSQL-engine gebruikt [rollen](https://www.postgresql.org/docs/current/sql-createrole.html) om de toegang tot database objecten te beheren en een nieuw gemaakte grootschalige (Citus)-Server groep wordt geleverd met vooraf gedefinieerde rollen:

* De [standaard postgresql-rollen](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Omdat grootschalige (Citus) een beheerde PaaS-service is, kan alleen micro soft zich aanmelden met de `postgres` rol van super gebruiker. Voor beperkte beheerders toegang biedt grootschalige (Citus) de `citus` rol.

Machtigingen voor de `citus` rol:

* Lees alle configuratie variabelen, zelfs variabelen die normaal gesp roken alleen zichtbaar zijn voor supergebruikers.
* Lees alles over alle pagina's \_ \_ \* met statistieken en gebruik verschillende,, zelfs weer gaven of uitbrei dingen die normaal gesp roken alleen zichtbaar zijn voor supergebruikers.
* Voer bewakings functies uit die toegangs vergrendelingen kunnen hebben voor tabellen, mogelijk gedurende een lange periode.
* [Maak postgresql-extensies](concepts-hyperscale-extensions.md) (omdat de rol lid is van `azure_pg_admin` ).

Met name voor de `citus` rol gelden enkele beperkingen:

* Kan geen rollen maken
* Kan geen data bases maken

## <a name="how-to-create-additional-user-roles"></a>Aanvullende gebruikers rollen maken

Zoals vermeld, heeft het `citus` beheerders account geen toestemming om extra gebruikers te maken. Gebruik de Azure Portal-interface om een gebruiker toe te voegen.

1. Ga naar de pagina **rollen** voor uw Citus-Server groep (grootschalige) en klik op **+ toevoegen**:

   :::image type="content" source="media/howto-hyperscale-create-users/1-role-page.png" alt-text="De pagina rollen":::

2. Voer de rolnaam en het wacht woord in. Klik op **Opslaan**.

   :::image type="content" source="media/howto-hyperscale-create-users/2-add-user-fields.png" alt-text="Rol toevoegen":::

De gebruiker wordt gemaakt op het coördinator knooppunt van de Server groep en door gegeven aan alle worker-knoop punten. Rollen die zijn gemaakt via de Azure Portal hebben het `LOGIN` kenmerk, wat betekent dat ze echte gebruikers zijn die zich kunnen aanmelden bij de data base.

## <a name="how-to-modify-privileges-for-user-role"></a>Bevoegdheden voor een gebruikersrol wijzigen

Nieuwe gebruikers rollen worden meestal gebruikt om toegang tot data bases te bieden met beperkte bevoegdheden. Als u gebruikers bevoegdheden wilt wijzigen, gebruikt u de standaard PostgreSQL-opdrachten met een hulp programma zoals PgAdmin of psql. (Zie [verbinding maken met psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) in de grootschalige (Citus) Quick Start.)

Als u bijvoorbeeld het volgende wilt toestaan `db_user` `mytable` , verleent u de machtiging:

```sql
GRANT SELECT ON mytable TO db_user;
```

Met grootschalige (Citus) worden GRANT-instructies met één tabel via het hele cluster door gegeven en toegepast op alle worker-knoop punten. Daarnaast worden er subsidies voor het hele systeem door gegeven (bijvoorbeeld voor alle tabellen in een schema):

```sql
-- applies to the coordinator node and propagates to workers
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Een gebruikersrol verwijderen of het wacht woord wijzigen

Als u een gebruiker wilt bijwerken, gaat u naar de pagina **rollen** voor uw Citus-Server groep (grootschalige) en klikt u op de weglatings tekens **...** naast de gebruiker. De weglatings tekens openen een menu om de gebruiker te verwijderen of hun wacht woord opnieuw in te stellen.

   :::image type="content" source="media/howto-hyperscale-create-users/edit-role.png" alt-text="Een rol bewerken":::

De `citus` rol is privileged en kan niet worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

Open de firewall voor de IP-adressen van de computers van de nieuwe gebruikers zodat ze verbinding kunnen maken: [grootschalige (Citus) firewall regels maken en beheren met behulp van de Azure Portal](howto-hyperscale-manage-firewall-using-portal.md).

Zie PostgreSQL-product documentatie voor meer informatie over het beheer van database gebruikers accounts:

* [Database rollen en-bevoegdheden](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Syntaxis van GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Bevoegdheden](https://www.postgresql.org/docs/current/static/ddl-priv.html)
