---
title: Een verwijderd dashboard in de Azure-portal herstellen
description: Als u een gepubliceerd dash board in de Azure Portal verwijdert, kunt u het dash board herstellen.
ms.date: 01/21/2020
ms.topic: troubleshooting
ms.openlocfilehash: 095964691a3cb22f8a805af2e8fe37af4c47cb28
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745618"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Een verwijderd dashboard in de Azure-portal herstellen

Als u zich in de open bare Azure-Cloud bevindt en u een _gepubliceerd_ dash board in de Azure Portal verwijdert, kunt u dat dash board binnen 14 dagen na de verwijdering herstellen. Als u zich in een Azure Government-Cloud bevindt of als het dash board niet is gepubliceerd, kunt u het niet herstellen en moet u het opnieuw bouwen. Zie [dash board publiceren](azure-portal-dashboard-share-access.md#publish-dashboard)voor meer informatie over het publiceren van een dash board. Volg deze stappen om een gepubliceerd dash board te herstellen:

1. Selecteer **resource groepen** in het menu Azure Portal en selecteer vervolgens de resource groep waar u het dash board hebt gepubliceerd (standaard **Dash boards** genoemd).

1. Vouw onder **activiteiten logboek** de bewerking **dash board verwijderen** uit. Selecteer het tabblad **wijzigings overzicht** en selecteer **\<deleted resource\>** .

    ![Scherm afbeelding van het tabblad wijzigings overzicht](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Selecteer en kopieer de inhoud van het linkerdeel venster en sla deze op in een tekst bestand met de extensie _. json_ . De portal maakt gebruik van het JSON-bestand om het dash board opnieuw te maken.

    ![Scherm afbeelding van het verschil met wijzigings geschiedenis](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. Selecteer in het menu Azure Portal de optie **Dash boards** en selecteer vervolgens **uploaden**.

    ![Scherm afbeelding van het uploaden van het dash board](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Selecteer het JSON-bestand dat u hebt opgeslagen. De portal maakt het dash board opnieuw met dezelfde naam en elementen als het verwijderde dash board.

1. Selecteer **delen** om het dash board te publiceren en het juiste toegangs beheer te herstellen.

    ![Scherm opname van de dashboard share](media/recover-shared-deleted-dashboard/dashboard-share.png)
