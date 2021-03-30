---
title: Migratie activiteit bewaken-Azure Database Migration Service
description: Meer informatie over het gebruik van de Azure Database Migration Service voor het bewaken van migratie activiteiten.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: dc9123735ef65bec89e99a14d25cba881be306fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91297467"
---
# <a name="monitor-migration-activity-using-the-azure-database-migration-service"></a>Migratie activiteiten bewaken met behulp van de Azure Database Migration Service
In dit artikel leert u hoe u de voortgang van een migratie kunt controleren op database niveau en op tabel niveau.

## <a name="monitor-at-the-database-level"></a>Bewaken op database niveau
Als u de activiteit op database niveau wilt bewaken, bekijkt u de Blade op database niveau:

![Blade op database niveau](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> Als u de database hyperlink selecteert, wordt de lijst met tabellen en de voortgang van de migratie weer gegeven.

De volgende tabel bevat de velden op de Blade op database niveau en beschrijft de verschillende status waarden die zijn gekoppeld aan elk.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>Veldnaam</strong></th>
      <th><strong>Veld substatus</strong></th>
      <th><strong>Beschrijving</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>Activiteitsstatus</strong></td>
      <td>Wordt uitgevoerd</td>
      <td>De migratie activiteit wordt uitgevoerd.</td>
    </tr>
    <tr>
      <td>Geslaagd</td>
      <td>De migratie activiteit is zonder problemen voltooid.</td>
    </tr>
    <tr>
      <td>Mislukte</td>
      <td>De migratie is mislukt. Selecteer de koppeling ' Zie Fout Details ' onder migratie Details voor het volledige fout bericht.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Status</strong></td>
      <td>Initialiseren</td>
      <td>DMS is het instellen van de migratie pijplijn.</td>
    </tr>
    <tr>
      <td>Wordt uitgevoerd</td>
      <td>De DMS-pijp lijn wordt uitgevoerd en er wordt gemigreerd.</td>
    </tr>
    <tr>
      <td>Voltooid</td>
      <td>De migratie is voltooid.</td>
    </tr>
    <tr>
      <td>Mislukt</td>
      <td>De migratie is mislukt. Klik op migratie gegevens voor een overzicht van de migratie fouten.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>Migratie Details</strong></td>
      <td>De migratie pijplijn starten</td>
      <td>DMS is het instellen van de migratie pijplijn.</td>
    </tr>
    <tr>
      <td>Volledige gegevens worden geladen</td>
      <td>Het DMS voert de initiële belasting uit.</td>
    </tr>
    <tr>
      <td>Gereed voor Cutover</td>
      <td>Wanneer de initiële belasting is voltooid, markeert DMS de Data Base als gereed voor cutover. De gebruiker moet controleren of de gegevens zijn gedetecteerd op doorlopende synchronisatie.</td>
    </tr>
    <tr>
      <td>Alle wijzigingen zijn toegepast</td>
      <td>Initiële belasting en doorlopende synchronisatie zijn voltooid. Deze status vindt ook plaats nadat de data base is cutover.</td>
    </tr>
    <tr>
      <td>Fout details weer geven</td>
      <td>Klik op de koppeling om de fout gegevens weer te geven.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>Duur</strong></td>
      <td>N.v.t.</td>
      <td>Totale tijd van de migratie activiteit die wordt geïnitialiseerd voor de migratie is voltooid of de migratie is mislukt.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>Bewaken op tabel niveau – snelle samen vatting
Als u de activiteit op tabel niveau wilt bewaken, bekijkt u de Blade op tabel niveau. In het bovenste gedeelte van de Blade ziet u het gedetailleerde aantal rijen dat is gemigreerd in volledige belasting en incrementele updates. 

Het onderste gedeelte van de Blade bevat de tabellen en toont een snelle samen vatting van de voortgang van de migratie.

![Blade op tabel niveau-snelle samen vatting](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

In de volgende tabel worden de velden beschreven die worden weer gegeven in de details op tabel niveau.

| Veldnaam        | Beschrijving       |
| ------------- | ------------- |
| **Volledige belasting voltooid**      | Het aantal tabellen heeft volledige belasting van de gegevens voltooid. |
| **Volledige belasting in wachtrij**      | Aantal tabellen dat in de wachtrij staat voor volledige belasting.      |
| **Volledige belasting laden** | Aantal tabellen is mislukt.      |
| **Incrementele updates**      | Aantal change data capture-updates (CDC) in rijen die op het doel zijn toegepast. |
| **Incrementele invoeg bladen**      | Aantal CDC-invoeg bewerkingen in rijen die op het doel zijn toegepast.      |
| **Incrementele verwijderingen** | Aantal verwijderde CDC in rijen die op het doel zijn toegepast.      |
| **Wijzigingen in behandeling**      | Het aantal CDC in rijen dat nog steeds wacht om toe te passen op het doel. |
| **Toegepaste wijzigingen**      | Totaal van CDC-updates,-invoegen en-verwijderen in rijen die op het doel zijn toegepast.      |
| **Tabellen met de fout status** | Aantal tabellen met de status ' error ' tijdens de migratie. Sommige voor beelden die tabellen kunnen bevatten in de fout status zijn wanneer er dubbele waarden zijn geïdentificeerd in het doel of omdat gegevens niet compatibel zijn met laden in de doel tabel.      |

## <a name="monitor-at-table-level--detailed-summary"></a>Bewaken op tabel niveau – gedetailleerde samen vatting
Er zijn twee tabbladen waarin de voortgang van de migratie wordt weer gegeven in volledige belasting en incrementele gegevens synchronisatie.
    
![Tabblad volledig laden](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![Het tabblad incrementele gegevens synchronisatie](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

In de volgende tabel worden de velden beschreven die worden weer gegeven in voortgang van migratie op tabel niveau.

| Veldnaam        | Beschrijving       |
| ------------- | ------------- |
| **Status-synchroniseren**      | Doorlopende synchronisatie wordt uitgevoerd. |
| **Invoegen**      | Aantal CDC-invoeg bewerkingen in rijen die op het doel zijn toegepast.      |
| **Bijwerken** | Aantal CDC-updates in rijen die op het doel zijn toegepast.      |
| **Verwijderen**      | Aantal verwijderde CDC in rijen die op het doel zijn toegepast. |
| **Totaal toegepast**      | Totaal van CDC-updates,-invoegen en-verwijderen in rijen die op het doel zijn toegepast. |
| **Gegevens fouten** | Het aantal gegevens fouten in deze tabel is opgetreden. Enkele voor beelden van de fouten zijn *511: kan geen rij van grootte% d maken die groter is dan de toegestane maximale rijgrootte van% d, 8114: fout bij het converteren van het gegevens type% LS naar% ls.*  De klant moet een query uitvoeren uit dms_apply_exceptions tabel in het Azure-doel om de fout gegevens weer te geven.    |

> [!NOTE]
> De CDC-waarden van INSERT, update en DELETE en Total toegepast kunnen afnemen wanneer de data base cutover of de migratie opnieuw wordt gestart.

## <a name="next-steps"></a>Volgende stappen
- Bekijk de richt lijnen voor migratie in de micro soft- [Data Base-migratie handleiding](https://datamigration.microsoft.com/).