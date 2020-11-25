---
title: Automatisch schalen van een Azure API Management-exemplaar configureren | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u het gedrag voor automatisch schalen instelt voor een Azure API Management-exemplaar.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 41a97c639d9148f2bff3e4f530363ea05a65a7b8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018241"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Exemplaar van Azure API Management automatisch schalen  

Azure API Management service-exemplaar kan automatisch worden geschaald op basis van een set regels. Dit gedrag kan worden ingeschakeld en geconfigureerd via Azure Monitor en wordt alleen ondersteund in de lagen **Standard** en **Premium** van de Azure API Management-service.

In dit artikel wordt stapsgewijs uitgelegd hoe u automatisch schalen configureert en een optimale configuratie van regels voor automatisch schalen stelt.

> [!NOTE]
> API Management service in de **verbruiks** laag wordt automatisch geschaald op basis van het verkeer, zonder dat er aanvullende configuratie nodig is.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt volgen, moet u het volgende doen:

+ Een actief Azure-abonnement.
+ Een Azure API Management-exemplaar hebben. Zie [een Azure API Management-exemplaar maken](get-started-create-service-instance.md)voor meer informatie.
+ Inzicht in het concept van de [capaciteit van een Azure API Management-exemplaar](api-management-capacity.md).
+ Meer informatie [over het hand matig schalen van een Azure API Management-exemplaar](upgrade-and-scale.md), met inbegrip van kosten gevolgen.

[!INCLUDE [premium-standard.md](../../includes/api-management-availability-premium-standard.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Beperkingen voor automatisch schalen van Azure API Management

Er moeten bepaalde beperkingen en gevolgen van schaal beslissingen worden overwogen voordat u gedrag voor automatisch schalen configureert.

+ Automatisch schalen kan alleen worden ingeschakeld voor de **standaard** -en **Premium** -lagen van Azure API Management service.
+ Prijs categorieën geven ook het maximum aantal eenheden voor een service-exemplaar op.
+ Het schaal proces kan ten minste 20 minuten duren.
+ Als de service is vergrendeld door een andere bewerking, mislukt de aanvraag voor schalen en wordt het automatisch opnieuw geprobeerd.
+ In het geval van een service met meerdere regionale implementaties kan alleen eenheden op de **primaire locatie** worden geschaald. Eenheden op andere locaties kunnen niet worden geschaald.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Automatisch schalen voor Azure API Management-service inschakelen en configureren

Volg de onderstaande stappen om automatisch schalen te configureren voor een Azure API Management-service:

1. Navigeer naar instantie **controleren** in het Azure Portal.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. Selecteer **automatisch schalen** in het menu aan de linkerkant.

    ![Azure Monitor resource voor automatisch schalen](media/api-management-howto-autoscale/02.png)

3. Zoek uw Azure API Management-service op basis van de filters in vervolg menu's.
4. Selecteer de gewenste Azure API Management service-instantie.
5. Klik in de sectie Nieuw geopend op de knop **automatisch schalen inschakelen** .

    ![Azure Monitor automatisch schalen inschakelen](media/api-management-howto-autoscale/03.png)

6. Klik in de sectie **regels** op **+ een regel toevoegen**.

    ![Azure Monitor regel voor automatisch schalen toevoegen](media/api-management-howto-autoscale/04.png)

7. Definieer een nieuwe scale-out regel.

   Een scale-out-regel kan bijvoorbeeld een toevoeging van een Azure API Management-eenheid activeren, wanneer de gemiddelde capaciteits metriek in de laatste 30 minuten 80% overschrijdt. De onderstaande tabel bevat een configuratie voor een dergelijke regel.

    | Parameter             | Waarde             | Opmerkingen                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Bron van metrische gegevens         | Huidige resource  | Definieer de regel op basis van de huidige metrische gegevens van Azure API Management resource.                                                                                                                                                                                                     |
    | *Criteria*            |                   |                                                                                                                                                                                                                                                                                 |
    | Tijdverzameling      | Gemiddeld           |                                                                                                                                                                                                                                                                                 |
    | Naam van meetwaarde           | Capaciteit          | De metrische gegevens van de capaciteit zijn een Azure API Management metriek die het gebruik van resources van een Azure API Management instantie weerspiegelt.                                                                                                                                                            |
    | Tijdsintervalstatistieken  | Gemiddeld           |                                                                                                                                                                                                                                                                                 |
    | Operator              | Groter dan      |                                                                                                                                                                                                                                                                                 |
    | Drempelwaarde             | 80%               | De drempel waarde voor de metrische gegevens over de gemiddelde capaciteit.                                                                                                                                                                                                                                 |
    | Duur (in minuten) | 30                | De tijds duur voor het gemiddelde van de capaciteits metriek is specifiek voor gebruiks patronen. Hoe langer de tijds periode is, hoe vloeiender de reactie is-loopt over een minder effect op de uitschaal beslissing. Er wordt echter ook een vertraging voor de scale-out trigger gegenereerd. |
    | *Actie*              |                   |                                                                                                                                                                                                                                                                                 |
    | Bewerking             | Aantal verhogen met |                                                                                                                                                                                                                                                                                 |
    | Aantal exemplaren        | 1                 | Het Azure API Management-exemplaar uitschalen op 1 eenheid.                                                                                                                                                                                                                          |
    | Afkoelen (minuten)   | 60                | Het duurt ten minste 20 minuten voordat de Azure API Management-service is uitgeschaald. In de meeste gevallen voor komt de door koele periode van 60 minuten dat er veel Scale-outs worden geactiveerd.                                                                                                  |

8. Klik op **toevoegen** om de regel op te slaan.

    ![Azure Monitor scale-out-regel](media/api-management-howto-autoscale/05.png)

9. Klik opnieuw op **+ een regel toevoegen**.

    Deze keer moet er een schaal in de regel worden gedefinieerd. Het zorgt ervoor dat resources niet worden verspild wanneer het gebruik van Api's afneemt.

10. Definieer een nieuwe schaal in de regel.

    Een schaal in regel kan bijvoorbeeld een verwijdering van een Azure API Management-eenheid activeren, wanneer de gemiddelde capaciteits metriek in de laatste 30 minuten lager is dan 35%. De onderstaande tabel bevat een configuratie voor een dergelijke regel.

    | Parameter             | Waarde             | Opmerkingen                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Bron van metrische gegevens         | Huidige resource  | Definieer de regel op basis van de huidige metrische gegevens van Azure API Management resource.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Criteria*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Tijdverzameling      | Gemiddeld           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Naam van meetwaarde           | Capaciteit          | Dezelfde metrische waarde als die voor de scale-out-regel.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Tijdsintervalstatistieken  | Gemiddeld           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operator              | Kleiner dan         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Drempelwaarde             | 35%               | Net als bij de scale-out regel is deze waarde sterk afhankelijk van de gebruiks patronen van de Azure-API Management. |
    | Duur (in minuten) | 30                | Dezelfde waarde als die voor de scale-out-regel.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Actie*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Bewerking             | Aantal verlagen met | Tegenovergestelde van wat werd gebruikt voor de scale-out-regel.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Aantal exemplaren        | 1                 | Dezelfde waarde als die voor de scale-out-regel.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Afkoelen (minuten)   | 90                | Schaal in moet beter zijn dan een uitschalen, zodat de onderdruks periode langer moet zijn.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Klik op **toevoegen** om de regel op te slaan.

    ![Schaal Azure Monitor in regel](media/api-management-howto-autoscale/06.png)

12. Stel het **maximum** aantal Azure API Management-eenheden in.

    > [!NOTE]
    > Voor Azure API Management geldt een limiet van eenheden waarmee een exemplaar kan worden uitgeschaald naar. De limiet is afhankelijk van een servicelaag.

    ![Scherm opname van de plaats waar het maximum aantal Azure API Management-eenheden moet worden ingesteld.](media/api-management-howto-autoscale/07.png)

13. Klik op **Opslaan**. Uw automatisch schalen is geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

- [Exemplaar van Azure API Management-service implementeren in meerdere Azure-regio's](api-management-howto-deploy-multi-region.md)
- [Optimaliseer en sla uw Cloud uitgaven op](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)