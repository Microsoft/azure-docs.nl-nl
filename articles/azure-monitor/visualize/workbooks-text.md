---
title: Tekst parameters van Azure Monitor werkmappen
description: Vereenvoudig complexe rapportage met vooraf samengestelde en aangepaste werkmappen met para meters. Meer informatie over para meters voor werkmap tekst.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: d064b69f25ada4edf478f9c8b70d7aaad83754a1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609236"
---
# <a name="workbook-text-parameters"></a>Werkmap tekst parameters

TextBox-para meters bieden een eenvoudige manier om tekst invoer van werkmap gebruikers te verzamelen. Ze worden gebruikt wanneer het niet praktisch is om een vervolg keuzelijst te gebruiken voor het verzamelen van de invoer (bijvoorbeeld een wille keurige drempel waarde of algemene filters). Met werkmappen kunnen auteurs de standaard waarde van het tekstvak uit een query ophalen. Zo kunt u interessante scenario's zoals het instellen van de standaard drempel waarde op basis van de P95 van de metriek.

Een veelvoorkomend gebruik van tekst vakken is de interne variabelen die worden gebruikt door andere besturings elementen van de werkmap. Dit wordt gedaan door gebruik te maken van een query voor standaard waarden en het besturings element in Lees modus onzichtbaar in te stellen. Een gebruiker kan bijvoorbeeld een drempel waarde uit een formule (geen gebruiker) laten bestaan en vervolgens de drempel waarde in volgende query's gebruiken.

## <a name="creating-a-text-parameter"></a>Een tekst parameter maken
1. Beginnen met een lege werkmap in de bewerkings modus.
2. Kies _para meters toevoegen_ uit de koppelingen in de werkmap.
3. Klik op de knop Blue _para meter toevoegen_ .
4. Typ in het deel venster nieuwe para meters dat verschijnt:
    1. Parameter naam: `SlowRequestThreshold`
    2. Parameter type: `Text`
    3. Vereist: `checked`
    4. Standaard waarde ophalen uit query: `unchecked`
5. Kies opslaan op de werk balk om de para meter te maken.

    ![Afbeelding van het maken van een tekst parameter](./media/workbooks-text/text-create.png)

Zo ziet de werkmap eruit als Lees modus.

![Afbeelding waarin een tekst parameter wordt weer gegeven in Lees modus](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>Verwijzen naar een tekst parameter
1. Voeg een besturings element query toe aan de werkmap door de blauwe koppeling te selecteren `Add query` en een Application Insights resource te selecteren.
2. Voeg in het vak KQL dit fragment toe:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. Met de para meter text met de waarde 500, gekoppeld aan het besturings element query, voert u de volgende query effectief uit:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. Query uitvoeren om de resultaten te bekijken

    ![Afbeelding met een tekst parameter waarnaar wordt verwezen in KQL](./media/workbooks-text/text-reference.png)

> [!NOTE]
> In het bovenstaande voor beeld `{SlowRequestThreshold}` vertegenwoordigt een geheel getal. Als u een query uitvoert voor een teken reeks zoals `{ComputerName}` u zou moeten uw Kusto-query wijzigen om aanhalings tekens toe te voegen, moet u `"{ComputerName}"` voor het parameter veld een invoer accepteren zonder aanhalings tekens.

## <a name="setting-default-values"></a>Standaard waarden instellen
1. Beginnen met een lege werkmap in de bewerkings modus.
2. Kies _para meters toevoegen_ uit de koppelingen in de werkmap.
3. Klik op de knop Blue _para meter toevoegen_ .
4. Typ in het deel venster nieuwe para meters dat verschijnt:
    1. Parameter naam: `SlowRequestThreshold`
    2. Parameter type: `Text`
    3. Vereist: `checked`
    4. Standaard waarde ophalen uit query: `checked`
5. Voeg in het vak KQL dit fragment toe:
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    Met deze query wordt de standaard waarde van het tekstvak ingesteld op de 95e percentiel duur voor alle aanvragen in de app.
6. Query uitvoeren om het resultaat te bekijken
7. Kies opslaan op de werk balk om de para meter te maken.

    ![Afbeelding met een tekst parameter met de standaard waarde van KQL](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> Hoewel in dit voor beeld Application Insights gegevens worden opgevraagd, kan de benadering worden gebruikt voor elke gegevens bron op basis van het logboek, Log Analytics Azure-resource grafiek, enzovoort.

## <a name="next-steps"></a>Volgende stappen

* [Ga](../platform/workbooks-overview.md#visualizations) voor meer informatie over werkmappen veel uitgebreide visualisaties opties.
* De toegang tot uw werkmap resources [beheren](../platform/workbooks-access-control.md) en delen.