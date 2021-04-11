---
title: Azure Monitor werkmappen-para meters maken
description: Meer informatie over hoe u met para meters de makers van de gebruikers in staat stelt invoer van de consumenten te verzamelen en ernaar te verwijzen in andere delen van de werkmap.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 95e810373ac5a591c530004e8d1175c3300656f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700555"
---
# <a name="workbook-parameters"></a>Werkmap parameters

Met para meters kunnen auteurs van de werkmap invoer van de consumenten verzamelen en ernaar verwijzen in andere delen van de werkmap. meestal kunt u het bereik van de resultatenset of de juiste Visual instellen. Het is een belang rijke functie waarmee auteurs interactieve rapporten en ervaringen kunnen bouwen. 

Met werkmappen kunt u bepalen hoe uw parameter besturings elementen worden weer gegeven aan consumenten: tekstvak versus vervolg keuzelijst, enkelvoudige en meervoudige selectie, waarden van Text, JSON, KQL of Azure resource Graph, enzovoort.  

Ondersteunde parameter typen zijn:
* [Tijd](workbooks-time.md) : Hiermee kan een gebruiker kiezen uit vooraf ingevulde Peri Oden of een aangepast bereik selecteren
* [Vervolg keuzelijst](workbooks-dropdowns.md) : Hiermee kan een gebruiker kiezen uit een waarde of set waarden
* [Tekst](workbooks-text.md) : Hiermee kan een gebruiker wille keurige tekst invoeren
* [Resource](workbooks-resources.md) : Hiermee kan een gebruiker een of meer Azure-resources selecteren
* [Abonnement](workbooks-resources.md) : Hiermee kan een gebruiker een of meer Azure-abonnements resources selecteren
* Resource type: Hiermee kan een gebruiker een of meer waarden van het Azure-resource type selecteren
* Locatie: Hiermee kan een gebruiker een of meer Azure-locatie waarden selecteren

U kunt naar deze parameter waarden verwijzen in andere delen van werkmappen door middel van bindingen of waarde-uitbrei dingen.

## <a name="creating-a-parameter"></a>Een para meter maken
1. Beginnen met een lege werkmap in de bewerkings modus.
2. Kies _para meters toevoegen_ uit de koppelingen in de werkmap.
3. Klik op de knop Blue _para meter toevoegen_ .
4. Typ in het deel venster nieuwe para meters dat verschijnt:
    1. Parameter naam: `TimeRange` *(de parameter __namen__ **mogen geen** spaties of speciale tekens bevatten)*
    2. Weergave naam: `Time Range` *( __weergave namen__ mogen echter spaties, speciale tekens, Emoji, enzovoort) bevatten.*  
    2. Parameter type: `Time range picker`
    3. Vereist: `checked`
    4. Beschik bare Peri Oden: vorig uur, laatste 12 uur, afgelopen 24 uur, afgelopen 48 uur, afgelopen 3 dagen, afgelopen 7 dagen en aangepast tijds bereik toestaan
5. Kies opslaan op de werk balk om de para meter te maken.

   ![Afbeelding van het maken van een tijds bereik parameter](./media/workbooks-parameters/time-settings.png)

Zo ziet de werkmap eruit als Lees modus, in de stijl "pills".

   ![Afbeelding die een tijds bereik parameter weergeeft in de Lees modus](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>Verwijzen naar een para meter
### <a name="via-bindings"></a>Via bindingen
1. Voeg een besturings element query toe aan de werkmap en selecteer een Application Insights resource.
2. Open de vervolg keuzelijst _tijds bereik_ en selecteer `Time Range` in het gedeelte para meters onderaan de optie.
3. Hiermee wordt de tijds bereik parameter gebonden aan het tijds bereik van de grafiek. Het tijds bereik van de voorbeeld query is nu de afgelopen 24 uur.
4. Query uitvoeren om de resultaten te bekijken

    ![Afbeelding van een tijds bereik parameter waarnaar wordt verwezen via bindingen](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>In KQL
1. Voeg een besturings element query toe aan de werkmap en selecteer een Application Insights resource.
2. Voer in het KQL een tijd bereik filter in met behulp van de para meter: `| where timestamp {TimeRange}`
3. Dit wordt uitgebreid naar de evaluatie tijd `| where timestamp > ago(1d)` van de query tot, dat wil zeggen de waarde voor het tijds bereik van de para meter.
4. Query uitvoeren om de resultaten te bekijken

    ![Afbeelding met een tijd bereik waarnaar wordt verwezen in KQL](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>In tekst 
1. Voeg een besturings element tekst toe aan de werkmap.
2. Voer in de prijs verlaging `The chosen time range is {TimeRange:label}`
3. Kies _gereed bewerken_
4. In het tekst besturings element wordt tekst weer gegeven: _het gekozen tijds bereik is afgelopen 24 uur_

## <a name="parameter-options"></a>Parameter opties
In de sectie _in tekst_ is de `label` para meter gebruikt in plaats van de waarde. Para meters bieden verschillende dergelijke opties, afhankelijk van het type, bijvoorbeeld het tijds bereik dat waarden, labels, query's, begin, einde en korrel toestaan.

Gebruik de `Previews` sectie in het deel venster _para meter bewerken_ om de uitbreidings opties voor uw para meter te bekijken:

![Afbeelding van de opties voor een tijds bereik parameter](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>Volgende stappen

* [Ga](./workbooks-overview.md#visualizations) voor meer informatie over werkmappen veel uitgebreide visualisaties opties.
* De toegang tot uw werkmap resources [beheren](./workbooks-access-control.md) en delen.