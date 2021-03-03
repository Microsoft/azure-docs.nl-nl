---
title: Azure Service Bus-bericht eenheden automatisch bijwerken
description: In dit artikel wordt beschreven hoe u de bericht eenheden van een Service Bus naam ruimte automatisch bijwerken kunt gebruiken.
ms.topic: how-to
ms.date: 09/15/2020
ms.openlocfilehash: 594f9987bfa5a7a439fb862a0345d0004785b189
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720593"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Bericht eenheden van een Azure Service Bus naam ruimte automatisch bijwerken 
Met automatisch schalen kunt u de juiste hoeveelheid resources uitvoeren om de belasting van uw toepassing te verwerken. U kunt hiermee resources toevoegen voor het afhandelen van toename van de belasting en bespaart u geld door resources te verwijderen die niet actief zijn. Zie [overzicht van automatisch schalen in Microsoft Azure](../azure-monitor/autoscale/autoscale-overview.md) voor meer informatie over de functie voor automatisch schalen van Azure monitor. 

Service Bus Premium Messaging biedt isolatie van resources op het niveau van de CPU en het geheugen, zodat elke workload van een klant geïsoleerd wordt uitgevoerd. Deze resource container wordt een **Messa ging-eenheid** genoemd. Zie [Service Bus Premium Messa ging](service-bus-premium-messaging.md)(Engelstalig) voor meer informatie over Messa ging-eenheden. 

Met de functie voor automatisch schalen voor Service Bus Premium-naam ruimten kunt u een minimum-en maximum aantal [Messa ging-eenheden](service-bus-premium-messaging.md) opgeven en berichten eenheden op basis van een set met regels toevoegen of verwijderen. 

U kunt bijvoorbeeld de volgende schaal scenario's voor Service Bus naam ruimten implementeren met behulp van de functie voor automatisch schalen. 

- Verhoog de Messa ging-eenheden voor een Service Bus naam ruimte wanneer het CPU-gebruik van de naam ruimte meer dan 75% overschrijdt. 
- Verminder de Messa ging-eenheden voor een Service Bus naam ruimte wanneer het CPU-gebruik van de naam ruimte lager is dan 25%. 
- Gebruik meer Messa ging-eenheden tijdens kantoor uren en minder tijdens de periode. 

In dit artikel wordt beschreven hoe u een Service Bus naam ruimte (update [Messa ging units](service-bus-premium-messaging.md)) automatisch kunt schalen in de Azure Portal. 

> [!IMPORTANT]
> Dit artikel is alleen van toepassing op de **Premium** -laag van Azure service bus. 

## <a name="autoscale-setting-page"></a>Pagina instellingen voor automatisch schalen
Voer eerst de volgende stappen uit om naar de pagina **instellingen voor automatisch schalen** voor uw service bus naam ruimte te gaan.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
2. Typ **Service Bus** op de zoek balk, selecteer **Service Bus** in de vervolg keuzelijst en druk op **Enter**. 
1. Selecteer uw **Premium-naam ruimte** in de lijst met naam ruimten. 
1. Schakel over naar de pagina voor **schalen** . 

    :::image type="content" source="./media/automate-update-messaging-units/scale-page.png" alt-text="Service Bus pagina naam ruimte-schaal":::

## <a name="manual-scale"></a>Hand matig schalen 
Met deze instelling kunt u een vast aantal Messa ging-eenheden instellen voor de naam ruimte. 

1. Selecteer op de pagina **instellingen voor automatisch schalen** **hand matig schalen** als dit nog niet is geselecteerd. 
1. Selecteer voor de instelling van **Messa ging-eenheden** het aantal Messa ging-eenheden in de vervolg keuzelijst.
1. Selecteer **Opslaan** op de werk balk om de instelling op te slaan. 

    :::image type="content" source="./media/automate-update-messaging-units/manual-scale.png" alt-text="Berichten eenheden hand matig schalen":::       


## <a name="custom-autoscale---default-condition"></a>Aangepaste automatisch schalen-standaard voorwaarde
U kunt automatisch schalen van Messa ging-eenheden configureren door voor waarden te gebruiken. Deze schaal voorwaarde wordt uitgevoerd wanneer geen van de andere schaal voorwaarden overeenkomen. U kunt de standaard voorwaarde op een van de volgende manieren instellen:

- Schalen op basis van metrische gegevens (zoals CPU-of geheugen gebruik)
- Schalen naar een specifiek aantal Messa ging-eenheden

U kunt een planning niet instellen op automatisch schalen op een specifieke dag of in een datum bereik voor een standaard voorwaarde. Deze schaal voorwaarde wordt uitgevoerd wanneer geen van de andere schaal voorwaarden met schema overeenkomt. 

### <a name="scale-based-on-a-metric"></a>Schalen op basis van een metrische waarde
De volgende procedure laat zien hoe u een voor waarde toevoegt om automatisch bericht eenheden te verg Roten (uitschalen) wanneer het CPU-gebruik groter is dan 75% en de bericht eenheden verlagen (schalen in) wanneer het CPU-gebruik lager is dan 25%. Stappen worden uitgevoerd van 1 tot 2, 2 tot 4, 4 tot 8 en 8 tot 16. Op dezelfde manier worden er verlaagt, van 16 tot 8, 8 tot 4, 4 en 2, en 2 tot en met 1. 

1. Selecteer op de pagina **instellingen** voor automatisch schalen **aangepaste schaal aanpassing** voor de optie **kiezen hoe u uw resource wilt schalen** . 
1. In de **standaard** sectie van de pagina geeft u een **naam** op voor de standaard voorwaarde. Selecteer het **Potlood** pictogram om de tekst te bewerken. 
1. Selecteer **schalen op basis van een metrische waarde** voor **schaal modus**. 
1. Selecteer **+ een regel toevoegen**. 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-add-rule-link.png" alt-text="Standaard-schalen op basis van een metrische waarde":::    
1. Voer de volgende stappen uit op de pagina **schaal regel** :
    1. Selecteer een waarde in de vervolg keuzelijst **metrische naam** . In dit voor beeld is het **CPU**. 
    1. Selecteer een operator en drempel waarden. In dit voor beeld zijn ze **groter dan** en **75** voor de **drempel waarde voor metrische gegevens om de schaal actie te activeren**. 
    1. Selecteer een **bewerking** in het gedeelte **actie** . In dit voor beeld is het ingesteld op **verhogen**. 
    1. Selecteer vervolgens **toevoegen**
    
        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-75.png" alt-text="Standaard-uitschalen als het CPU-gebruik groter is dan 75%":::       

        > [!NOTE]
        > De functie voor automatisch schalen verhoogt de Messa ging-eenheden voor de naam ruimte als het totale CPU-gebruik in dit voor beeld meer dan 75% overschrijdt. Stappen worden uitgevoerd van 1 tot 2, 2 tot 4, 4 tot 8 en 8 tot 16. 
1. Selecteer **+ een regel opnieuw toevoegen** en voer de volgende stappen uit op de pagina **schaal regel** :
    1. Selecteer een waarde in de vervolg keuzelijst **metrische naam** . In dit voor beeld is het **CPU**. 
    1. Selecteer een operator en drempel waarden. In dit voor beeld zijn ze **minder dan** en **25** voor **metrische drempel waarden voor het activeren van schaal acties**. 
    1. Selecteer een **bewerking** in het gedeelte **actie** . In dit voor beeld is de instelling ingesteld op **afnemen**. 
    1. Selecteer vervolgens **toevoegen** 

        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-25.png" alt-text="Standaard: schaal in als het CPU-gebruik lager is dan 25%":::       

        > [!NOTE]
        > Met de functie voor automatisch schalen worden de Messa ging-eenheden voor de naam ruimte verminderd als het totale CPU-gebruik in dit voor beeld meer dan 25% overschrijdt. Verlaagt de tussen 16 en 8, 8 tot 4, 4 en 2 en 2 tot en met 1. 
1. Stel het **minimale** en **maximum** en het **standaard** aantal Messa ging-eenheden in.

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-based.png" alt-text="Standaard regel op basis van een metrische waarde":::
1. Selecteer **Opslaan** op de werk balk om de instelling voor automatisch schalen op te slaan. 
        
### <a name="scale-to-specific-number-of-messaging-units"></a>Schalen naar een specifiek aantal Messa ging-eenheden
Volg deze stappen om de regel te configureren voor het schalen van de naam ruimte om een specifiek aantal Messa ging-eenheden te gebruiken. Opnieuw wordt de standaard voorwaarde toegepast wanneer geen van de andere schaal voorwaarden overeenkomen. 

1. Selecteer op de pagina **instellingen** voor automatisch schalen **aangepaste schaal aanpassing** voor de optie **kiezen hoe u uw resource wilt schalen** . 
1. In de **standaard** sectie van de pagina geeft u een **naam** op voor de standaard voorwaarde. 
1. Selecteer **schalen naar specifieke berichten eenheden** voor de **schaal modus**. 
1. Selecteer voor **Messa ging-eenheden** het aantal standaard Messa ging-eenheden. 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-messaging-units.png" alt-text="Standaard-schalen naar specifieke Messa ging-eenheden":::       

## <a name="custom-autoscale---additional-conditions"></a>Aangepaste automatisch schalen-aanvullende voor waarden
In de vorige sectie ziet u hoe u een standaard voorwaarde kunt toevoegen voor de instelling voor automatisch schalen. In deze sectie wordt beschreven hoe u meer voor waarden toevoegt aan de instelling voor automatisch schalen. Voor deze aanvullende niet-standaard omstandigheden kunt u een planning instellen op basis van specifieke dagen van een week of een datum bereik. 

### <a name="scale-based-on-a-metric"></a>Schalen op basis van een metrische waarde
1. Selecteer op de pagina **instellingen** voor automatisch schalen **aangepaste schaal aanpassing** voor de optie **kiezen hoe u uw resource wilt schalen** . 
1. Selecteer **een schaal voorwaarde toevoegen** onder het **standaard** blok. 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="Aangepast: een koppeling voor een schaal voorwaarde toevoegen":::    
1. Geef een **naam** op voor de voor waarde. 
1. Bevestig dat de **schaal op basis van een metrische** optie is geselecteerd. 
1. Selecteer **+ een regel toevoegen** om een regel toe te voegen voor het verbeteren van berichten eenheden wanneer het totale CPU-gebruik meer dan 75% overschrijdt. Volg de stappen in de sectie [standaard voorwaarde](#custom-autoscale---default-condition) . 
5. Stel het **minimale** en **maximum** en het **standaard** aantal Messa ging-eenheden in.
6. U kunt ook een **schema** instellen voor een aangepaste voor waarde (maar niet op de standaard waarde). U kunt de begin-en eind datum opgeven voor de voor waarde (of) Selecteer specifieke dagen (maandag, dinsdag, enzovoort.) van een week. 
    1. Als u **begin-en eind datums opgeven** selecteert, selecteert u de **tijd zone**, de **begin datum en-tijd** en de **eind datum en-tijd** (zoals weer gegeven in de volgende afbeelding) om de voor waarde in werking te laten treden. 

       :::image type="content" source="./media/automate-update-messaging-units/custom-min-max-default.png" alt-text="Minimum-, maximum-en standaard waarden voor het aantal Messa ging-eenheden":::
    1. Als u **specifieke dagen herhalen** selecteert, selecteert u de dagen van de week, de tijd zone, de begin tijd en de eind tijd waarop de voor waarde van toepassing moet zijn. 

        :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days.png" alt-text="Specifieke dagen herhalen":::
  
### <a name="scale-to-specific-number-of-messaging-units"></a>Schalen naar een specifiek aantal Messa ging-eenheden
1. Selecteer op de pagina **instellingen** voor automatisch schalen **aangepaste schaal aanpassing** voor de optie **kiezen hoe u uw resource wilt schalen** . 
1. Selecteer **een schaal voorwaarde toevoegen** onder het **standaard** blok. 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="Aangepast: een koppeling voor een schaal voorwaarde toevoegen":::    
1. Geef een **naam** op voor de voor waarde. 
2. Selecteer de optie **schalen naar specifieke Messa ging-eenheden** voor de **schaal modus**. 
1. Selecteer het aantal **Messa ging-eenheden** in de vervolg keuzelijst. 
6. Voor het **schema** geeft u start-en eind datums op voor de voor waarde (of) selecteert u specifieke dagen (maandag, dinsdag enzovoort.) van een week en tijd. 
    1. Als u **begin-en eind datums opgeven** selecteert, selecteert u de **tijd zone**, de **begin datum en-tijd** en de **eind datum en-tijd** waarop de voor waarde van kracht is. 
    
    :::image type="content" source="./media/automate-update-messaging-units/scale-specific-messaging-units-start-end-dates.png" alt-text="schalen naar specifieke bericht eenheden-begin-en eind datums":::        
    1. Als u **specifieke dagen herhalen** selecteert, selecteert u de dagen van de week, de tijd zone, de begin tijd en de eind tijd waarop de voor waarde van toepassing moet zijn.
    
    :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days-2.png" alt-text="schalen naar specifieke bericht eenheden-specifieke dagen herhalen":::

> [!IMPORTANT]
> Zie [instellingen voor automatisch schalen](../azure-monitor/autoscale/autoscale-understanding-settings.md)voor meer informatie over de werking van instellingen voor automatisch schalen, met name hoe een profiel of voor waarde wordt gekozen en meerdere regels worden geëvalueerd.          

## <a name="next-steps"></a>Volgende stappen
Voor meer informatie over Messa ging-eenheden raadpleegt u de [Premium Messa ging](service-bus-premium-messaging.md)

