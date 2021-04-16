---
title: Schaal van virtuele-machineschaalsets in de Azure Portal
description: Regels voor automatisch schalen maken voor virtuele-machineschaalsets in de Azure Portal
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 2ee2db62cf43dc191da2b92f7d4b67ff775f628f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537517"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Een virtuele-machineschaalset automatisch schalen in de Azure Portal
Wanneer u een schaalset maakt, definieert u het aantal VM-exemplaren dat u wilt uitvoeren. Wanneer de vraag van de toepassing verandert, kunt u het aantal VM-exemplaren automatisch vergroten of verkleinen. De mogelijkheid van automatisch schalen stelt u in staat om altijd te voldoen aan de vraag van klanten houden of om gedurende de levenscyclus van uw app te reageren op wijzigingen in de prestaties van de toepassing.

In dit artikel wordt beschreven hoe u regels voor automatisch schalen maakt in de Azure Portal de prestaties van de VM-exemplaren in uw schaalset bewaken. Met deze regels voor automatisch schalen wordt het aantal VM-exemplaren vergroot of verkleind als reactie op deze metrische prestatiegegevens. U kunt deze stappen ook uitvoeren met [Azure PowerShell](tutorial-autoscale-powershell.md) of de [Azure CLI.](tutorial-autoscale-cli.md)


## <a name="prerequisites"></a>Vereisten
Als u regels voor automatisch schalen wilt maken, hebt u een bestaande virtuele-machineschaalset nodig. U kunt een schaalset maken met de [Azure Portal](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md)of [Azure CLI.](quick-create-cli.md)


## <a name="create-a-rule-to-automatically-scale-out"></a>Een regel maken om automatisch uit te schalen
Als de vraag van uw toepassing toeneemt, neemt de belasting van de VM-exemplaren in de schaalset ook toe. Als deze toegenomen belasting consistent is, en geen piekbelasting is, kunt u regels voor automatisch schalen configureren om het aantal VM-exemplaren in de schaalset te verhogen. Wanneer deze VM-exemplaren worden gemaakt en uw toepassingen worden geïmplementeerd, zorgt de schaalset ervoor dat er via de load balancer verkeer wordt gedistribueerd naar de exemplaren. U bepaalt welke meetwaarden gegevens moeten worden bewaakt, zoals CPU of schijf, hoe lang de belasting van de toepassing overeen moet komen met een bepaalde drempelwaarde en hoeveel VM-exemplaren er moeten worden toegevoegd aan de schaalset.

1. Open het Azure Portal selecteer **Resourcegroepen** in het menu aan de linkerkant van het dashboard.
2. Selecteer de resourcegroep die uw schaalset bevat en kies vervolgens uw schaalset in de lijst met resources.
3. Kies **Schalen** in het menu aan de linkerkant van het venster van de schaalset. Selecteer de knop Automatisch **schalen inschakelen:**

    ![Automatisch schalen inschakelen in de Azure Portal](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Voer een naam in voor uw instellingen, zoals *automatisch schalen,* en selecteer vervolgens de optie **om een regel toe te voegen.**

5. We gaan een regel maken die het aantal VM-exemplaren in een schaalset verhoogt wanneer de gemiddelde CPU-belasting gedurende een periode van 10 minuten hoger is dan 70%. Wanneer de regel wordt triggers, wordt het aantal VM-exemplaren met 20% verhoogd. In schaalsets met een klein aantal VM-exemplaren kunt u de bewerking instellen op *Aantal* verhogen met en vervolgens *1* of *2 opgeven* voor het *aantal exemplaren.*  In schaalsets met een groot aantal VM-exemplaren is een toename van 10% of 20% van VM-exemplaren mogelijk beter geschikt.

    Geef de volgende instellingen voor uw regel op:
    
    | Parameter              | Uitleg                                                                                                         | Waarde          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Tijdaggregatie*     | Hiermee definieert u hoe de verzamelde meetwaarden moeten worden samengevoegd voor analyse.                                                | Average        |
    | *Naam van metrische gegevens*          | De prestatiemeetwaarde die u wilt bewaken en waarvoor u acties wilt toepassen op de schaalset.                                                   | Percentage CPU |
    | *Tijdsintervalstatistieken* | Definieert hoe de verzamelde metrische gegevens in elke tijdsgranen moeten worden geaggregeerd voor analyse.                             | Gemiddeld        |
    | *Operator*             | De operator die wordt gebruikt voor het vergelijken van de meetwaarden met de drempelwaarde.                                                     | Groter dan   |
    | *Drempelwaarde*            | Het percentage dat ervoor zorgt dat de regel voor automatisch schalen een actie activeert.                                                 | 70             |
    | *Duur*             | De hoeveelheid tijd waarna de meetwaarde en drempelwaarde met elkaar worden vergeleken. Bevat geen afkoelperiode.                                   | 10 minuten     |
    | *Bewerking*            | Hiermee definieert u of de schaalset omhoog of omlaag moet worden geschaald wanneer de regel van toepassing is en met welke verhoging.                        | Percentage verhogen met |
    | *Aantal exemplaren*       | Het percentage VM-exemplaren dat moet worden gewijzigd wanneer de regel wordt geactiveerd.                                            | 20             |
    | *Afkoelen (minuten)*  | De tijd die moet worden gewacht voordat de regel opnieuw wordt toegepast, zodat de acties voor automatisch schalen voldoende tijd hebben om effectief te zijn. | 5 minuten      |

    In de volgende voorbeelden wordt een regel in de Azure Portal die overeenkomt met deze instellingen:

    ![Een regel voor automatisch schalen maken om het aantal VM-exemplaren te verhogen](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Selecteer Toevoegen om de regel te **maken**


## <a name="create-a-rule-to-automatically-scale-in"></a>Een regel maken om automatisch in te schalen
In het weekend of 's avonds kan de vraag voor uw toepassing afnemen. Als deze afgenomen belasting consistent is gedurende een bepaalde periode, kunt u regels voor automatisch schalen configureren om het aantal VM-exemplaren in de schaalset te verlagen. Deze inschaalactie reduceert de kosten voor het uitvoeren van uw schaalset, aangezien u alleen het aantal exemplaren uitvoert dat vereist is om te voldoen aan de actuele vraag.

1. Kies opnieuw **om een regel toe te** voegen.
2. Maak een regel die het aantal VM-exemplaren in een schaalset verlaagt wanneer de gemiddelde CPU-belasting gedurende een periode van 10 minuten onder de 30% komt. Wanneer de regel wordt triggers, wordt het aantal VM-exemplaren met 20% verlaagd.

    Gebruik dezelfde methode als bij de vorige regel. Pas de volgende instellingen voor uw regel aan:
    
    | Parameter              | Uitleg                                                                                                          | Waarde          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operator*             | De operator die wordt gebruikt voor het vergelijken van de meetwaarden met de drempelwaarde.                                                      | Kleiner dan   |
    | *Drempelwaarde*            | Het percentage dat ervoor zorgt dat de regel voor automatisch schalen een actie activeert.                                                 | 30             |
    | *Bewerking*            | Hiermee definieert u of de schaalset omhoog of omlaag moet worden geschaald wanneer de regel van toepassing is en met welke verhoging                         | Percentage verlagen met |
    | *Aantal exemplaren*       | Het percentage VM-exemplaren dat moet worden gewijzigd wanneer de regel wordt geactiveerd.                                             | 20             |

3. Selecteer Toevoegen om de regel te **maken**


## <a name="define-autoscale-instance-limits"></a>Limieten voor exemplaren voor automatisch schalen definiëren
Uw profiel voor automatisch schalen moet een minimum-, maximum- en standaardaantal VM-exemplaren definiëren. Wanneer uw regels voor automatisch schalen worden toegepast, zorgen deze instantielimieten ervoor dat u niet verder uitschaalt dan het maximum aantal exemplaren en niet verder inschaalt dan het minimum aan exemplaren.

1. Stel de volgende instantielimieten in:

    | Minimum | Maximum | Standaard|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Als u uw regels voor automatisch schalen en instantielimieten wilt toepassen, selecteert u **Opslaan.**


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Het aantal exemplaren in een schaalset bewaken
Als u het aantal en de status  van VM-exemplaren wilt zien, selecteert u Exemplaren in het menu aan de linkerkant van het venster van de schaalset. De status geeft aan of het VM-exemplaar Maken *is,* omdat  de schaalset automatisch wordt uitschaald of wordt verwijderd wanneer de schaal automatisch wordt inschalen.

![Een lijst met VM-exemplaren van schaalsets weergeven](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Automatisch schalen op basis van een planning
In de vorige voorbeelden is een schaalset automatisch in- of uitschaald met metrische basisgegevens van de host, zoals CPU-gebruik. U kunt ook regels voor automatisch schalen maken op basis van planningen. Met deze regels op basis van een planning kunt u het aantal VM-exemplaren automatisch uitschalen vóór een verwachte toename van de vraag naar toepassingen, zoals kernwerkuren, en vervolgens automatisch het aantal instanties inschalen op een moment dat u verwacht dat er minder vraag is, zoals in het weekend.

1. Kies **Schalen** in het menu aan de linkerkant van het venster van de schaalset. Als u de bestaande regels voor automatisch schalen wilt verwijderen die in de vorige voorbeelden zijn gemaakt, kiest u het prullenbakpictogram.

    ![De bestaande regels voor automatisch schalen verwijderen](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Kies ervoor **om een schaalvoorwaarde toe te voegen.** Selecteer het potloodpictogram naast regelnaam en geef een naam op, zoals Uitschalen *tijdens elke werkdag.*

    ![De naam van de standaardregel voor automatisch schalen wijzigen](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Selecteer het keuzerondje om naar **een specifiek aantal exemplaren te schalen.**
4. Als u het aantal exemplaren wilt opschalen, voert u *10 in* als het aantal exemplaren.
5. Kies **Specifieke dagen herhalen** voor het **schematype.**
6. Selecteer alle werkdagen, maandag tot en met vrijdag.
7. Kies de juiste tijdzone en geef vervolgens een **Begintijd** *van 09:00 op.*
8. Kies opnieuw **om een schaalvoorwaarde toe te** voegen. Herhaal het proces om  's avonds een schema met de naam Inschalen te maken dat wordt geschaald naar *3* exemplaren, elke weekdag wordt herhaald en om *18:00 uur begint.*
9. Als u de regels voor automatisch schalen op basis van een planning wilt toepassen, selecteert u **Opslaan.**

    ![Regels voor automatisch schalen maken die volgens een planning worden geschaald](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Als u wilt zien hoe uw regels voor automatisch schalen worden toegepast, **selecteert** u Uitvoeringsgeschiedenis boven aan **het venster Schalen.** In de lijst met grafieken en gebeurtenissen ziet u wanneer de regels voor automatisch schalen worden triggeren en het aantal VM-exemplaren in uw schaalset toe- of afneemt.


## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u regels voor automatisch schalen kunt gebruiken om horizontaal te schalen en het aantal VM-exemplaren in uw schaalset te vergroten *of* te verlagen. U kunt ook verticaal schalen om de *grootte* van VM-exemplaren te vergroten of verkleinen. Zie voor meer informatie [Vertical autoscale with virtual machine scale sets](virtual-machine-scale-sets-vertical-scale-reprovision.md) (Verticaal automatisch schalen met virtuele-machineschaalsets).

Zie [Manage a virtual machine scale set with Azure PowerShell](./virtual-machine-scale-sets-manage-powershell.md) (Virtuele-machineschaalset beheren met Azure PowerShell) voor meer informatie over het beheren van uw VM-exemplaren.

Als u wilt weten hoe u waarschuwingen kunt genereren wanneer uw regels voor automatisch schalen worden geactiveerd, raadpleegt u [Use autoscale actions to send email and webhook alert notifications in Azure Monitor](../azure-monitor/autoscale/autoscale-webhook-email.md) (Acties voor automatisch schalen gebruiken om waarschuwingen per e-mail of webhooks te verzenden in Azure Monitor). U kunt ook [controlelogboeken gebruiken om waarschuwingen per e-mail of webhooks te verzenden in Azure Monitor](../azure-monitor/alerts/alerts-log-webhook.md).
