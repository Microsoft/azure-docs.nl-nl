---
title: Herstellen - Hyperscale (Citus) - Azure Database for PostgreSQL - Azure Portal
description: In dit artikel wordt beschreven hoe u herstelbewerkingen in Azure Database for PostgreSQL - Hyperscale (Citus) via de Azure Portal.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: aebfeed055fad7c1108620ab494236640285aa1e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495025"
---
# <a name="point-in-time-restore-of-a-hyperscale-citus-server-group"></a>Herstel naar een bepaald tijdstip van een Hyperscale (Citus)servergroep

Dit artikel bevat stapsgewijs procedures voor het uitvoeren van herstel naar een bepaald tijdstip voor een Hyperscale (Citus) servergroep met behulp van [back-ups.](concepts-hyperscale-backup.md#point-in-time-restore-pitr) U kunt binnen uw bewaarperiode herstellen naar de vroegste back-up of naar een aangepast herstelpunt.

## <a name="restoring-to-the-earliest-restore-point"></a>Herstellen naar het eerste herstelpunt

Volg deze stappen om de servergroep Hyperscale (Citus) herstellen naar de vroegste bestaande back-up.

1.  Kies in [Azure Portal](https://portal.azure.com/)de servergroep die u wilt herstellen.

2.  Klik **op Overzicht** in het linkerpaneel en klik op **Herstellen.**

    > [!IMPORTANT]
    > Als de **knop Herstellen** nog niet aanwezig is voor uw servergroep, opent u een ondersteuning voor Azure aanvraag.

3.  Op de herstelpagina wordt u  gevraagd om te kiezen tussen het eerste en het aangepaste **herstelpunt** en wordt de vroegste datum weergegeven.

4.  Selecteer **Vroegste herstelpunt.**

5.  Geef een nieuwe servergroepnaam op in het **veld Herstellen naar nieuwe server.** De andere velden (abonnement, resourcegroep en locatie) worden weergegeven, maar kunnen niet worden bewerkt.

6.  Klik op **OK**.

7.  Er wordt een melding weergegeven dat de herstelbewerking is gestart.

Volg tot slot de [taken na het herstellen.](#post-restore-tasks)

## <a name="restoring-to-a-custom-restore-point"></a>Herstellen naar een aangepast herstelpunt

Volg deze stappen om uw Hyperscale (Citus) te herstellen naar een datum en tijd van uw keuze.

1.  Kies in [Azure Portal](https://portal.azure.com/)de servergroep die u wilt herstellen.

2.  Klik **op Overzicht** in het linkerpaneel en klik op **Herstellen**

    > [!IMPORTANT]
    > Als de **knop Herstellen** nog niet aanwezig is voor uw servergroep, opent u een ondersteuning voor Azure aanvraag.

3.  Op de herstelpagina wordt u gevraagd om te kiezen tussen het **vroegste** en een aangepast **herstelpunt** en wordt de vroegste datum weergegeven.

4.  Kies **Aangepast herstelpunt.**

5.  Selecteer de datum en tijd **voor Herstelpunt (UTC)** en geef een nieuwe servergroepnaam op in het veld **Herstellen naar nieuwe server.** De andere velden (abonnement, resourcegroep en locatie) worden weergegeven, maar kunnen niet worden bewerkt.
 
6.  Klik op **OK**.

7.  Er wordt een melding weergegeven dat de herstelbewerking is gestart.

Volg ten slotte de [taken na het herstellen](#post-restore-tasks).

## <a name="post-restore-tasks"></a>Post-restore tasks

Na het herstellen moet u het volgende doen om uw gebruikers en toepassingen weer actief te maken:

* Als de nieuwe server is bedoeld om de oorspronkelijke server te vervangen, moet u clients en clienttoepassingen omleiden naar de nieuwe server
* Zorg ervoor dat er een geschikte firewall op serverniveau is ingesteld om gebruikers verbinding te laten maken. Deze regels worden niet gekopieerd uit de oorspronkelijke servergroep.
* Pas de PostgreSQL-serverparameters naar behoefte aan. De parameters worden niet gekopieerd uit de oorspronkelijke servergroep.
* Zorg ervoor dat de juiste aanmeldingen en machtigingen op databaseniveau zijn gebruikt.
* Configureer waar nodig waarschuwingen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [back-up en herstel](concepts-hyperscale-backup.md) in Hyperscale (Citus).
* Stel [voorgestelde waarschuwingen](./howto-hyperscale-alert-on-metric.md#suggested-alerts) in Hyperscale (Citus) servergroepen.
