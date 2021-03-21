---
title: Waarschuwingen configureren-Azure Portal-Azure Database for PostgreSQL-één-server
description: In dit artikel wordt beschreven hoe u metrische waarschuwingen kunt configureren en openen voor Azure Database for PostgreSQL-één server van de Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 1b4c10d152afba708eb176e89355ed31ec809168
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586750"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---single-server"></a>Gebruik de Azure Portal om waarschuwingen in te stellen voor de Azure Database for PostgreSQL-één-server

In dit artikel wordt beschreven hoe u Azure Database for PostgreSQL waarschuwingen instelt met behulp van de Azure Portal. U kunt een waarschuwing ontvangen op basis van metrische bewakings gegevens voor uw Azure-Services.

De waarschuwing wordt geactiveerd wanneer de waarde van een opgegeven metriek een drempel overschrijdt die u toewijst. De waarschuwing wordt geactiveerd wanneer aan de voor waarde wordt voldaan en daarna wordt niet meer voldaan aan de voor waarde. 

U kunt een waarschuwing configureren om de volgende acties uit te voeren wanneer deze wordt geactiveerd:
* E-mail meldingen verzenden naar de service beheerder en mede beheerders.
* E-mail verzenden naar extra e-mail berichten die u opgeeft.
* Een webhook aanroepen.

U kunt informatie over waarschuwings regels configureren en ophalen met behulp van:
* [Azure-portal](../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure-CLI](../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [Azure Monitor REST API](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Een waarschuwingsregel maken op een metrische waarde vanuit Azure Portal
1. Selecteer in de [Azure Portal](https://portal.azure.com/)de Azure database for postgresql server die u wilt bewaken.

2. Selecteer in de sectie **bewaking** van de zijbalk **waarschuwingen** zoals wordt weer gegeven:

   :::image type="content" source="./media/howto-alert-on-metric/2-alert-rules.png" alt-text="Waarschuwings regels selecteren":::

3. Selecteer **metrische waarschuwing toevoegen** (+ pictogram).

4. De pagina **regel maken** wordt geopend, zoals hieronder wordt weer gegeven. Vul de vereiste gegevens in:

   :::image type="content" source="./media/howto-alert-on-metric/4-add-rule-form.png" alt-text="Formulier voor metrische waarschuwing toevoegen":::

5. Selecteer **voor waarde toevoegen** in de sectie **voor waarde** .

6. Selecteer een waarde in de lijst met signalen waarop u wilt worden gewaarschuwd. In dit voor beeld selecteert u ' opslag percentage '.
   
   :::image type="content" source="./media/howto-alert-on-metric/6-configure-signal-logic.png" alt-text="Metrische waarde selecteren":::

7. Configureer de waarschuwings logica met inbegrip van de **voor waarde** (bijvoorbeeld ' Groter dan '), **drempel waarde** (bijvoorbeeld 85 procent), **tijd aggregatie**, **periode waarbinnen** de metrische regel moet worden voldaan voordat de waarschuwingen worden geactiveerd (bijvoorbeeld ' In de afgelopen 30 minuten ') en **frequentie**.
   
   Selecteer **gereed** wanneer u klaar bent.

   :::image type="content" source="./media/howto-alert-on-metric/7-set-threshold-time.png" alt-text="Scherm afbeelding die de sectie waarschuwings logica en de knop gereed markeert.":::

8. Selecteer in de sectie **actie groepen** de optie **nieuwe maken** om een nieuwe groep te maken voor het ontvangen van meldingen over de waarschuwing.

9. Vul het formulier actie groep toevoegen in met een naam, korte naam, abonnement en resource groep.

10. Een actie type voor **e-mail/SMS/push/Voice** configureren.
    
    Kies e-mail Azure Resource Manager rol om abonnements eigenaren, mede werkers en lezers te selecteren voor het ontvangen van meldingen.
   
    Geef eventueel een geldige URI op in het veld **webhook** als u wilt dat deze wordt aangeroepen wanneer de waarschuwing wordt geactiveerd.

    Selecteer **OK** wanneer u klaar bent.

    :::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Scherm afbeelding die laat zien hoe u een nieuwe actie groep kunt toevoegen.":::

11. Geef een naam, beschrijving en ernst voor de waarschuwings regel op.

    :::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Actiegroep"::: 

12. Selecteer **waarschuwings regel maken** om de waarschuwing te maken.

    Binnen een paar minuten is de waarschuwing actief en worden triggers zoals eerder beschreven.

## <a name="manage-your-alerts"></a>De waarschuwingen beheren
Zodra u een waarschuwing hebt gemaakt, kunt u deze selecteren en de volgende acties uitvoeren:

* Een grafiek weer geven met de metrische drempel waarde en de werkelijke waarden van de vorige dag die relevant is voor deze waarschuwing.
* **Bewerk** of **Verwijder** de waarschuwings regel.
* **Schakel** de waarschuwing **in** of uit als u het ontvangen van meldingen tijdelijk wilt stoppen of hervatten.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [configureren van webhooks in waarschuwingen](../azure-monitor/alerts/alerts-webhooks.md).
* Bekijk een [overzicht van de verzameling metrische gegevens](../azure-monitor/data-platform.md) om te controleren of uw service beschikbaar is en reageert.