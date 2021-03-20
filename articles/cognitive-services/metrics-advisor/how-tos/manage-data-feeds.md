---
title: Gegevensfeeds beheren met metrische gegevens Advisor
titleSuffix: Azure Cognitive Services
description: Meer informatie over het beheren van gegevensfeeds die u hebt toegevoegd aan de Advisor voor metrische gegevens.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: mbullwin
ms.openlocfilehash: fb6eaf44967732d3a41ea92b0896540a40f694e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184719"
---
# <a name="how-to-manage-your-data-feeds"></a>Procedure: uw gegevensfeeds beheren

Meer informatie over het beheren van uw onboarded gegevensfeeds in metrische gegevens Advisor. In dit artikel vindt u instructies voor het beheren van gegevensfeeds in metrische gegevens Advisor.

## <a name="edit-a-data-feed"></a>Een gegevensfeed bewerken

> [!NOTE]
> De volgende gegevens kunnen niet worden gewijzigd nadat een gegevensfeed is gemaakt. 
> * Gegevensfeed-ID
> * Gemaakt om
> * Dimensie
> * Brontype
> * Granulariteit

Alleen de beheerder van een gegevensfeed mag wijzigingen aanbrengen. 

Een gegevensfeed onderbreken of opnieuw activeren:

1. Klik op de pagina lijst gegevensfeed op de bewerking die u wilt uitvoeren op de gegevensfeed.

2. Klik op de pagina Details van gegevensfeed op de knop voor het wijzigen van de **status** .

Een gegevensfeed verwijderen: 

1. Klik op de pagina lijst gegevensfeed op **verwijderen** in de gegevensfeed.

2. Klik op de pagina Details van gegevensfeed op **verwijderen**.

Als u de begin tijd wijzigt, moet u het schema opnieuw controleren. U kunt deze wijzigen met behulp van **para meters bewerken**.

##  <a name="backfill-your-data-feed"></a>Backfill uw gegevensfeed

Selecteer de knop  **backfill** om een onmiddellijke opname op een tijds tempel te activeren, om een mislukte opname te herstellen of de bestaande gegevens te overschrijven.
- De begin tijd is inclusief.
- De eind tijd is exclusief.
- Anomalie detectie wordt alleen opnieuw geactiveerd voor het geselecteerde bereik.

:::image type="content" source="../media/datafeeds/backfill-datafeed.png" alt-text="Backfill-gegevens invoer":::

## <a name="manage-permission-of-a-data-feed"></a>Machtigingen voor een gegevensfeed beheren

De toegang tot de werk ruimte wordt bepaald door de resources van de gegevens adviseur die Azure Active Directory voor verificatie gebruikt. Een andere Layer of permission Control wordt toegepast op metrische gegevens.

Met metrische gegevens adviseur kunt u machtigingen verlenen aan verschillende groepen mensen op verschillende gegevensfeeds. Er zijn twee soorten rollen: 

- Beheerder: wie heeft volledige machtigingen voor het beheren van een gegevensfeed, met inbegrip van wijzigen en verwijderen.
- Viewer: wie heeft toegang tot een alleen-lezen weer gave van de gegevensfeed.
 

## <a name="advanced-settings"></a>Geavanceerde instellingen

Er zijn verschillende optionele geavanceerde instellingen voor het maken van een nieuwe gegevensfeed, die kunnen worden gewijzigd in de detail pagina gegevensfeed.

### <a name="ingestion-options"></a>Opname opties

* **Offset van opname tijd**: standaard worden gegevens opgenomen op basis van de opgegeven granulariteit. Een metriek met een *dagelijks* tijds tempel wordt bijvoorbeeld één dag na de tijds tempel opgenomen. U kunt de offset gebruiken om de opname tijd met een *positief* getal te vertragen of door te gaan met een *negatief* getal.

* **Max. gelijktijdigheid**: Stel deze para meter in als uw gegevens bron beperkte gelijktijdigheid ondersteunt. Geef anders de standaard instelling op.

* **Nieuwe poging stoppen na**: als de gegevens opname is mislukt, wordt deze automatisch opnieuw geprobeerd binnen een bepaalde periode. Het begin van de periode is het tijdstip waarop de eerste gegevens opname plaatsvond. De lengte van de periode wordt gedefinieerd op basis van de granulariteit. Als de standaard waarde (-1) wordt verlaten, wordt de waarde bepaald aan de hand van de granulatie die hieronder wordt weer gegeven.
    
    | Granulariteit       | Nieuwe poging stoppen na           |
    | :------------ | :--------------- |
    | Dagelijks, aangepast (>= 1 dag), wekelijks, maandelijks, jaarlijks     | 7 dagen          |
    | Elk uur, aangepast (< 1 dag)       | 72 uur |

* **Min. interval voor opnieuw proberen**: u kunt het minimale interval opgeven wanneer u opnieuw probeert gegevens uit de bron te halen. Als de standaard waarde (-1) wordt verlaten, wordt het interval voor nieuwe pogingen bepaald aan de hand van de granulatie die hieronder wordt weer gegeven.
    
    | Granulariteit       | Mini maal interval voor opnieuw proberen           |
    | :------------ | :--------------- |
    | Dagelijks, aangepast (>= 1 dag), wekelijks, maandelijks     | 30 minuten          |
    | Elk uur, aangepast (< 1 dag)      | 10 minuten |
    | Jaar | 1 dag          |
 
### <a name="fill-gap-when-detecting"></a>Tussen ruimte door voeren bij detecteren van: 

> [!NOTE]
> Deze instelling heeft geen invloed op de gegevens bron en heeft geen invloed op de gegevens diagrammen die worden weer gegeven op de portal. De automatische vulling vindt alleen plaats tijdens de afwijkings detectie.

Sommige tijd reeksen zijn niet doorlopend. Wanneer er gegevens punten ontbreken, wordt de opgegeven waarde in de Advisor gebruikt voor het opvullen van deze voor afwijkende detectie voor een betere nauw keurigheid.
De opties zijn: 

* De waarde van het vorige werkelijke gegevens punt gebruiken. Dit wordt standaard gebruikt.
* Een specifieke waarde gebruiken.

### <a name="action-link-template"></a>Sjabloon actie koppeling: 

Actie koppelings sjablonen worden gebruikt om vooraf bewaarde http-url's te definiëren, die bestaan uit de tijdelijke aanduidingen,,, `%datafeed` `%metric` `%timestamp` `%detect_config` en `%tagset` . U kunt de sjabloon gebruiken om te omleiden van een afwijkend of incident naar een specifieke URL om in te zoomen.

:::image type="content" source="../media/action-link-template.png" alt-text="Sjabloon actie koppeling" lightbox="../media/action-link-template.png":::

Wanneer u de actie koppeling hebt ingevuld, klikt u op **Ga naar actie koppeling** op de actie optie van de incidenten lijst en klikt u op het snelmenu van de boom structuur. Vervang de tijdelijke aanduidingen in de sjabloon actie koppeling door de overeenkomstige waarden van de anomalie of het incident.

| Tijdelijke aanduiding | Voorbeelden | Opmerking |
| ---------- | -------- | ------- |
| `%datafeed` | - | Gegevensfeed-ID |
| `%metric` | - | Metrische ID |
| `%detect_config` | - | Configuratie-ID detecteren |
| `%timestamp` | - | Tijds tempel van een afwijkende of eind tijd van een persistent incident |
| `%tagset` | `%tagset`, <br> `[%tagset.get("Dim1")]`, <br> `[ %tagset.get("Dim1", "filterVal")]` | Dimensie waarden van een afwijkende of meest afwijkende van een incident.   <br> De `filterVal` wordt gebruikt voor het filteren van overeenkomende waarden binnen de vier Kante haken.   |

Vindt

* Als de sjabloon actie koppeling is `https://action-link/metric/%metric?detectConfigId=%detect_config` ,
  * De actie koppeling `https://action-link/metric/1234?detectConfigId=2345` gaat naar afwijkingen of incidenten onder metrische gegevens `1234` en configuratie detectie `2345` .

* Als de sjabloon actie koppeling is `https://action-link?[Dim1=%tagset.get('Dim1','')&][Dim2=%tagset.get('Dim2','')]` , 
    * De actie koppeling zou zijn `https://action-link?Dim1=Val1&Dim2=Val2` wanneer de afwijking is `{ "Dim1": "Val1", "Dim2": "Val2" }` . 
    * De actie koppeling zou zijn `https://action-link?Dim2=Val2` wanneer de afwijkend is `{ "Dim1": "", "Dim2": "Val2" } ` , omdat deze wordt `[Dim1=***&]` overgeslagen voor de lege teken reeks van de dimensie waarde. 

* Als de sjabloon actie koppeling is `https://action-link?filter=[Name/Dim1 eq '%tagset.get('Dim1','')' and ][Name/Dim2 eq '%tagset.get('Dim2','')']` , 
    * De actie koppeling zou zijn `https://action-link?filter=Name/Dim1 eq 'Val1' and Name/Dim2 eq 'Val2'` wanneer de afwijking is `{ "Dim1": "Val1", "Dim2": "Val2" }` , 
    * De actie koppeling zou zijn `https://action-link?filter=Name/Dim2 eq 'Val2'` Wanneer afwijkend is, `{ "Dim1": "", "Dim2": "Val2" }` omdat `[Name/Dim1 eq '***' and ]` deze is overgeslagen voor de lege teken reeks van de dimensie waarde. 
   
### <a name="data-feed-not-available-alert-settings"></a>Waarschuwings instellingen voor gegevens invoer niet beschikbaar

Een gegevensfeed wordt als niet beschikbaar beschouwd als er geen gegevens uit de bron worden opgenomen binnen de respijt periode die is opgegeven vanaf het moment dat de gegevensfeed opname wordt gestart. In dit geval wordt een waarschuwing geactiveerd.

Als u een waarschuwing wilt configureren, moet u eerst [een hook maken](alerts.md#create-a-hook) . Waarschuwingen worden via de geconfigureerde Hook verzonden.

* **Respijt periode**: de instelling voor de respijt periode wordt gebruikt om te bepalen wanneer een waarschuwing moet worden verzonden als er geen gegevens punten worden opgenomen. Het referentie punt is de tijd van de eerste opname. Als een opname mislukt, blijft de metrics Advisor een regel matig interval proberen dat is opgegeven door de granulariteit. Als de respijt periode niet actief is, wordt er een waarschuwing verzonden.

* **Automatisch uitstellen**: wanneer deze optie is ingesteld op nul, wordt met elke tijds tempel en *niet beschikbaar* een waarschuwing geactiveerd. Wanneer een andere instelling dan nul is opgegeven, worden doorlopende tijds tempels na de eerste tijds tempel die *niet beschikbaar* is, niet geactiveerd volgens de opgegeven instelling.

## <a name="next-steps"></a>Volgende stappen
- [Metrische gegevens configureren en detectieconfiguratie afstemmen](configure-metrics.md)
- [Anomaliedetectie aanpassen met feedback](anomaly-feedback.md)
- [Een incident diagnosticeren](diagnose-incident.md).
