---
title: Metrische waarschuwingen configureren-Azure Portal-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u metrische waarschuwingen voor Azure Database for MySQL kunt configureren en benaderen vanuit de Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 5b9e7938f404a37672fe8d13725b1e64d665bf18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100576265"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql"></a>Gebruik de Azure Portal om waarschuwingen in te stellen voor Azure Database for MySQL 

In dit artikel wordt beschreven hoe u Azure Database for MySQL waarschuwingen instelt met behulp van de Azure Portal. U kunt een waarschuwing ontvangen op basis van metrische bewakings gegevens voor uw Azure-Services.

De waarschuwing wordt geactiveerd wanneer de waarde van een opgegeven metriek een drempel overschrijdt die u toewijst. De waarschuwing wordt geactiveerd wanneer aan de voor waarde wordt voldaan en daarna wordt niet meer voldaan aan de voor waarde. 

U kunt een waarschuwing configureren om de volgende acties uit te voeren wanneer deze wordt geactiveerd:
* E-mail meldingen verzenden naar de service beheerder en mede beheerders
* E-mail verzenden naar extra e-mail berichten die u opgeeft.
* Een webhook aanroepen

U kunt informatie over waarschuwings regels configureren en ophalen met behulp van:
* [Azure-portal](../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure-CLI](../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [Azure Monitor REST API](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Een waarschuwingsregel maken op een metrische waarde vanuit Azure Portal
1. Selecteer in de [Azure Portal](https://portal.azure.com/)de Azure database for mysql server die u wilt bewaken.

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

   :::image type="content" source="./media/howto-alert-on-metric/7-set-threshold-time.png" alt-text="Selecteer metrische waarde 2":::

8. Selecteer in de sectie **actie groepen** de optie **nieuwe maken** om een nieuwe groep te maken voor het ontvangen van meldingen over de waarschuwing.

9. Vul het formulier actie groep toevoegen in met een naam, korte naam, abonnement en resource groep.

10. Een actie type voor **e-mail/SMS/push/Voice** configureren.
    
    Kies e-mail Azure Resource Manager rol om abonnements eigenaren, mede werkers en lezers te selecteren voor het ontvangen van meldingen.
   
    Geef eventueel een geldige URI op in het veld **webhook** als u wilt dat deze wordt aangeroepen wanneer de waarschuwing wordt geactiveerd.

    Selecteer **OK** wanneer u klaar bent.

    :::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Actiegroep":::

11. Geef een naam, beschrijving en ernst voor de waarschuwings regel op.

    :::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Actie groep 2"::: 

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