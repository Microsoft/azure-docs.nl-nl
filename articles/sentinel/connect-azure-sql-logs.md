---
title: Diagnostische gegevens van Azure SQL database en controle logboeken verbinden met Azure Sentinel
description: Meer informatie over het verbinden van Azure SQL database Diagnostische logboeken en beveiligings controle logboeken voor Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/06/2021
ms.author: yelevin
ms.openlocfilehash: df132c35ebb04596d91720431f5b08cb88e2abd9
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98104188"
---
# <a name="connect-azure-sql-database-diagnostics-and-auditing-logs"></a>Diagnostische gegevens en controle logboeken van Azure SQL database verbinden

Azure SQL is een volledig beheerde PaaS-data base-engine (platform-as-a-Service) die de meeste database beheer functies, zoals upgrades, patches, back-ups en bewaking, afhandelt, zonder tussen komst van de gebruiker. 

Met de Azure SQL database-connector kunt u de controle-en Diagnostische logboeken van uw data bases streamen naar Sentinel, zodat u de activiteiten in al uw instanties voortdurend kunt bewaken.

- Met de verbinding met Diagnostische logboeken kunt u Diagnostische logboeken voor data bases van verschillende gegevens typen naar uw Sentinel-werk ruimte verzenden.

- Door controle logboeken te verbinden kunt u beveiligingscontrole logboeken streamen vanuit alle Azure SQL-data bases op server niveau.

Meer informatie over het [bewaken van Azure SQL-data bases](../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor de Azure Sentinel-werk ruimte.

- Als u controle logboeken wilt verbinden, moet u lees-en schrijf machtigingen hebben voor de controle-instellingen van Azure SQL Server.

## <a name="connect-to-azure-sql-database"></a>Verbinding maken met Azure SQL-database
    
1. Selecteer in het navigatie menu van de Azure-Sentinel **Data connectors**.

1. Selecteer **Azure SQL database** in de galerie met gegevens connectors en selecteer vervolgens **pagina connector openen**  in het voorbeeld venster.

1. In de sectie **configuratie** van de pagina connector ziet u de twee soorten logboeken waarmee u verbinding kunt maken.

### <a name="connect-diagnostics-logs"></a>Diagnostische logboeken verbinden

1. Onder **Diagnostische logboeken** vouwt **u Diagnostische logboeken inschakelen in elk van uw Azure SQL-data bases hand matig in**.

1. Selecteer de koppeling **Azure sql >openen** om de Blade **Azure SQL** -resources te openen.

1. **(Optioneel)** Als u uw database Resource eenvoudig wilt vinden, selecteert u **filter toevoegen** op de balk filters bovenaan.
    1. Selecteer in de vervolg keuzelijst **filter** de optie **resource type**.
    1. Schakel in de vervolg keuzelijst **waarde** het selectie **vakje alles selecteren** uit en selecteer vervolgens **SQL database**.
    1. Klik op **Toepassen**.
    
1. Selecteer de database resource waarvan u de diagnostische logboeken wilt verzenden naar Azure Sentinel.

    > [!NOTE]
    > Voor elke database resource waarvan u de logboeken wilt verzamelen, moet u dit proces herhalen, te beginnen met deze stap.

1. Ga naar de pagina resource van de data base die u hebt geselecteerd en selecteer **Diagnostische instellingen** onder **bewaking** in het navigatie menu.

    1. Selecteer de koppeling met de **Diagnostische instelling toevoegen** onder aan de tabel.

    1. Voer in het scherm **Diagnostische instelling** een naam in het veld naam van de  **Diagnostische instelling** in.
    
    1. Schakel in de kolom **doel Details** het selectie vakje **verzenden naar log Analytics werk ruimte** in. Er worden twee nieuwe velden weer gegeven. Kies het relevante **abonnement** en de **log Analytics werk ruimte** (waar Azure Sentinel zich bevindt).

    1. Schakel in de kolom **categorie Details** de selectie vakjes in van de logboek-en metrische typen die u wilt opnemen. U kunt het beste alle beschik bare typen selecteren onder **logboek** en **metrische gegevens**.

    1. Selecteer **Opslaan** boven aan het scherm.

- U kunt ook het meegeleverde **Power shell-script** gebruiken om uw Diagnostische logboeken te verbinden.
    1. Vouw onder **Diagnostische logboeken** de **optie inschakelen op basis van Power shell-script** uit.

    1. Kopieer het code blok en plak in Power shell.

### <a name="connect-audit-logs"></a>Controle logboeken verbinden

1. Onder **controle Logboeken (preview)** vouwt u **audit logboeken inschakelen in alle Azure SQL-data bases (op server niveau)**.

1. Selecteer de koppeling **Azure sql >openen** om de Blade **SQL Server** -resource te openen.

1. Selecteer de SQL-server waarvan u de controle logboeken wilt verzenden naar Azure Sentinel.

    > [!NOTE]
    > Voor elke server bron waarvan u de logboeken wilt verzamelen, moet u dit proces herhalen, te beginnen met deze stap.

1. Selecteer op de pagina resource van de server die u hebt geselecteerd onder **beveiliging** in het navigatie menu de optie **controle**.

    1. Verplaats de **Schakel optie Azure SQL-controle inschakelen** **in**.

    1. Onder **controle logboek bestemming** selecteert u **log Analytics (preview-versie)**.
    
    1. In de lijst met werk ruimten die wordt weer gegeven, kiest u uw werk ruimte (waar Azure Sentinel zich bevindt).

    1. Selecteer **Opslaan** boven aan het scherm.

- U kunt ook het meegeleverde **Power shell-script** gebruiken om uw Diagnostische logboeken te verbinden.
    1. Vouw onder **Logboeken controleren** de **optie inschakelen via Power shell-script** uit.

    1. Kopieer het code blok en plak in Power shell.


> [!NOTE]
>
> Met deze specifieke gegevens connector wordt de verbindings status indicator (een kleur balk in de galerie met gegevens connectors en verbindings pictogrammen naast de namen van gegevens typen) alleen weer gegeven als *verbonden* (groen) als de gegevens op een bepaald moment in de afgelopen twee weken zijn opgenomen. Als er twee weken zijn geslaagd zonder opname van gegevens, wordt de connector weer gegeven als niet-verbonden. Op het moment dat er meer gegevens worden opgehaald, wordt de status *verbonden* geretourneerd.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Azure SQL database diagnostische gegevens en controle Logboeken kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).