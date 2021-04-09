---
title: Waarschuwingen configureren-grootschalige (Citus)-Azure Database for PostgreSQL
description: In dit artikel wordt beschreven hoe u metrische waarschuwingen kunt configureren en benaderen voor Azure Database for PostgreSQL-grootschalige (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 3/16/2020
ms.openlocfilehash: f5557140d77865a6d4c44316cecd512f877736e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577074"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Gebruik de Azure Portal om waarschuwingen in te stellen voor de Azure Database for PostgreSQL-grootschalige (Citus)

In dit artikel wordt beschreven hoe u Azure Database for PostgreSQL waarschuwingen instelt met behulp van de Azure Portal. U kunt een waarschuwing ontvangen op basis van [metrische bewakings gegevens](concepts-hyperscale-monitoring.md) voor uw Azure-Services.

We stellen een waarschuwing in om te activeren wanneer de waarde van een opgegeven metriek een drempel overschrijdt. De waarschuwing wordt geactiveerd wanneer aan de voor waarde wordt voldaan en blijft daarna verder.

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

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/2-alert-rules.png" alt-text="Waarschuwings regels selecteren":::

3. Selecteer **nieuwe waarschuwings regel** (+ pictogram).

4. De pagina **regel maken** wordt geopend, zoals hieronder wordt weer gegeven. Vul de vereiste gegevens in:

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png" alt-text="Formulier voor metrische waarschuwing toevoegen":::

5. Selecteer in de sectie **voor waarde** de optie **toevoegen**.

6. Selecteer een waarde in de lijst met signalen waarop u wilt worden gewaarschuwd. In dit voor beeld selecteert u ' opslag percentage '.
   
   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png" alt-text="Scherm afbeelding toont de logica pagina voor signalen configureren, waar u verschillende signalen kunt weer geven.":::

7. De waarschuwings logica configureren:

    * **Operator** (bijvoorbeeld ' Groter dan ')
    * **Drempel waarde** (bijvoorbeeld 85 procent)
    * De **mate waarin** de metrische regel moet worden voldaan voordat de waarschuwing wordt geactiveerd (bijvoorbeeld "In de afgelopen 30 minuten")
    * en **frequentie van de evaluatie** (bijvoorbeeld "1 minuut")
   
   Selecteer **gereed** wanneer u klaar bent.

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png" alt-text="Scherm afbeelding toont het deel venster waarin u waarschuwings logica kunt configureren.":::

8. Selecteer in de sectie **actie groepen** de optie **nieuwe maken** om een nieuwe groep te maken voor het ontvangen van meldingen over de waarschuwing.

9. Vul het formulier actie groep toevoegen in met een naam, korte naam, abonnement en resource groep.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/9-add-action-group.png" alt-text="Scherm afbeelding toont het formulier actie groep toevoegen waarin u de beschreven waarden kunt invoeren.":::

10. Een actie type voor **e-mail/SMS/push/Voice** configureren.
    
    Kies e-mail Azure Resource Manager rol om meldingen te verzenden naar eigen aars, mede werkers en lezers van het abonnement.
   
    Selecteer **OK** wanneer u klaar bent.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/10-action-group-type.png" alt-text="Scherm afbeelding toont het deel venster E-mail/sec. M S/push/Voice.":::

11. Geef een naam, beschrijving en ernst voor de waarschuwings regel op.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png" alt-text="Scherm afbeelding toont het deel venster waarschuwings Details."::: 

12. Selecteer **waarschuwings regel maken** om de waarschuwing te maken.

    Binnen een paar minuten is de waarschuwing actief en worden triggers zoals eerder beschreven.

### <a name="managing-alerts"></a>Meldingen beheren

Zodra u een waarschuwing hebt gemaakt, kunt u deze selecteren en de volgende acties uitvoeren:

* Een grafiek weer geven met de metrische drempel waarde en de werkelijke waarden van de vorige dag die relevant is voor deze waarschuwing.
* **Bewerk** of **Verwijder** de waarschuwings regel.
* **Schakel** de waarschuwing **in** of uit als u het ontvangen van meldingen tijdelijk wilt stoppen of hervatten.

## <a name="suggested-alerts"></a>Aanbevolen waarschuwingen

### <a name="disk-space"></a>Schijfruimte

Bewaking en waarschuwingen zijn belang rijk voor elke Citus-Server groep (Production grootschalige). Voor de onderliggende PostgreSQL-data base is vrije schijf ruimte vereist om goed te kunnen functioneren. Als de schijf vol raakt, wordt het knoop punt van de database server offline gezet en wordt de computer niet meer opgestart totdat er ruimte beschikbaar is. Op dat moment is er een ondersteunings aanvraag van micro soft om de situatie op te lossen.

We raden u aan om op elk knoop punt in elke server groep schijf ruimte waarschuwingen in te stellen, zelfs voor niet-productie gebruik. Waarschuwingen voor het gebruik van schijf ruimte bieden de vooraf geplaatste waarschuwing die nodig is voor het bewaken van knoop punten. Voer voor de beste resultaten een reeks waarschuwingen uit op 75%, 85% en 95% gebruik. Het percentage dat u kiest, is afhankelijk van de snelheid van de gegevens opname, omdat snelle gegevens opname de schijf sneller opvult.

Wanneer de schijf de schijfruimte limiet nadert, kunt u met deze technieken meer vrije ruimte krijgen:

* Controleer het Bewaar beleid voor gegevens. Verplaats zo mogelijk oudere gegevens naar koude opslag.
* Overweeg om [knoop punten toe te voegen](howto-hyperscale-scale-grow.md#add-worker-nodes) aan de Server groep en Shards te herverdelen. Met herverdeling worden de gegevens verdeeld over meer computers.
* Overweeg [de capaciteit](howto-hyperscale-scale-grow.md#increase-or-decrease-vcores-on-nodes) van worker-knoop punten te groeien. Elke werk nemer kan Maxi maal 2 TiB aan opslag ruimte hebben. Het toevoegen van knoop punten moet echter worden uitgevoerd voordat het formaat van knoop punten wordt gewijzigd, omdat het toevoegen van knoop punten sneller is voltooid.

### <a name="cpu-usage"></a>CPU-gebruik

Bewaking van CPU-gebruik is nuttig om een basis lijn voor prestaties te maken. Het is bijvoorbeeld mogelijk dat het CPU-gebruik doorgaans ongeveer 40-60% is. Als het CPU-gebruik plotseling begint met het zweven van 95%, kunt u een afwijkingen herkennen. Het CPU-gebruik kan de biologische groei weer spie gelen, maar kan ook een losse query onthullen. Wanneer u een CPU-waarschuwing maakt, moet u een lange aggregatie granulatie instellen om langdurige verhogingen te ondervangen en tijdelijke pieken te negeren.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [configureren van webhooks in waarschuwingen](../azure-monitor/alerts/alerts-webhooks.md).
* Bekijk een [overzicht van de verzameling metrische gegevens](../azure-monitor/data-platform.md) om te controleren of uw service beschikbaar is en reageert.
