---
title: Schaal sets voor virtuele machines automatisch schalen in de Azure Portal
description: Regels voor automatisch schalen maken voor virtuele-machine schaal sets in de Azure Portal
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms:service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 4bb4084a7ee0d9d719a88c077fa8c63803aa6934
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591364"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Schaal sets van virtuele machines automatisch schalen in de Azure Portal
Wanneer u een schaalset maakt, definieert u het aantal VM-exemplaren dat u wilt uitvoeren. Wanneer de vraag van de toepassing verandert, kunt u het aantal VM-exemplaren automatisch vergroten of verkleinen. De mogelijkheid van automatisch schalen stelt u in staat om altijd te voldoen aan de vraag van klanten houden of om gedurende de levenscyclus van uw app te reageren op wijzigingen in de prestaties van de toepassing.

In dit artikel wordt beschreven hoe u regels voor automatisch schalen maakt in de Azure Portal die de prestaties van de VM-exemplaren in uw schaalset bewaken. Deze regels voor automatisch schalen verg Roten of verkleinen het aantal VM-exemplaren als reactie op deze metrische gegevens over prestaties. U kunt deze stappen ook uitvoeren met [Azure PowerShell](tutorial-autoscale-powershell.md) of de [Azure cli](tutorial-autoscale-cli.md).


## <a name="prerequisites"></a>Vereisten
Als u regels voor automatisch schalen wilt maken, hebt u een bestaande schaalset voor virtuele machines nodig. U kunt een schaalset maken met de [Azure Portal](quick-create-portal.md), [Azure POWERSHELL](quick-create-powershell.md)of [Azure cli](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Een regel maken om automatisch uit te schalen
Als de vraag van uw toepassing toeneemt, neemt de belasting van de VM-exemplaren in de schaalset ook toe. Als deze toegenomen belasting consistent is, en geen piekbelasting is, kunt u regels voor automatisch schalen configureren om het aantal VM-exemplaren in de schaalset te verhogen. Wanneer deze VM-exemplaren worden gemaakt en uw toepassingen worden geïmplementeerd, zorgt de schaalset ervoor dat er via de load balancer verkeer wordt gedistribueerd naar de exemplaren. U bepaalt welke meetwaarden gegevens moeten worden bewaakt, zoals CPU of schijf, hoe lang de belasting van de toepassing overeen moet komen met een bepaalde drempelwaarde en hoeveel VM-exemplaren er moeten worden toegevoegd aan de schaalset.

1. Open de Azure Portal en selecteer **resource groepen** in het menu aan de linkerkant van het dash board.
2. Selecteer de resource groep die uw schaalset bevat en kies vervolgens uw schaalset in de lijst met resources.
3. Kies **schalen** in het menu aan de linkerkant van het venster schaal instellen. Selecteer de knop om **automatisch schalen in te scha kelen**:

    ![Automatisch schalen inschakelen in de Azure Portal](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Voer een naam in voor de instellingen, zoals *automatisch schalen*, en selecteer vervolgens de optie om **een regel toe te voegen**.

5. We gaan een regel maken waarmee het aantal VM-exemplaren in een schaalset wordt verhoogd wanneer de gemiddelde CPU-belasting gedurende een periode van tien minuten hoger is dan 70%. Wanneer de regel wordt geactiveerd, wordt het aantal VM-exemplaren verhoogd met 20%. In schaal sets met een klein aantal VM-exemplaren kunt u de **bewerking** zo instellen dat het *aantal wordt verhoogd door* en vervolgens *1* of *2* voor het *aantal exemplaren* opgeven. In schaal sets met een groot aantal VM-exemplaren is het mogelijk dat de verhoging van de VM-exemplaren van 10% of 20% meer geschikt is.

    Geef de volgende instellingen op voor de regel:
    
    | Parameter              | Uitleg                                                                                                         | Waarde          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Tijd aggregatie*     | Hiermee definieert u hoe de verzamelde meetwaarden moeten worden samengevoegd voor analyse.                                                | Average        |
    | *Metrische naam*          | De prestatiemeetwaarde die u wilt bewaken en waarvoor u acties wilt toepassen op de schaalset.                                                   | Percentage CPU |
    | *Tijdsintervalstatistieken* | Hiermee definieert u hoe de verzamelde metrische gegevens in elke tijd korrel moeten worden geaggregeerd voor analyse.                             | Gemiddeld        |
    | *Operator*             | De operator die wordt gebruikt voor het vergelijken van de meetwaarden met de drempelwaarde.                                                     | Groter dan   |
    | *Drempelwaarde*            | Het percentage dat ervoor zorgt dat de regel voor automatisch schalen een actie activeert.                                                 | 70             |
    | *Duur*             | De hoeveelheid tijd waarna de meetwaarde en drempelwaarde met elkaar worden vergeleken. Bevat geen afkoele periode.                                   | 10 minuten     |
    | *Bewerking*            | Hiermee definieert u of de schaalset omhoog of omlaag moet worden geschaald wanneer de regel van toepassing is en door welke verhoging.                        | Percentage verhogen met |
    | *Aantal exemplaren*       | Het percentage VM-exemplaren dat moet worden gewijzigd wanneer de regel wordt geactiveerd.                                            | 20             |
    | *Afkoelen (minuten)*  | De tijd die moet worden gewacht voordat de regel opnieuw wordt toegepast, zodat de acties voor automatisch schalen voldoende tijd hebben om effectief te zijn. | 5 minuten      |

    In de volgende voor beelden ziet u een regel die is gemaakt in de Azure Portal die overeenkomt met deze instellingen:

    ![Een regel voor automatisch schalen maken om het aantal VM-exemplaren te verhogen](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Als u de regel wilt maken, selecteert u **toevoegen**


## <a name="create-a-rule-to-automatically-scale-in"></a>Een regel maken om automatisch in te schalen
In het weekend of 's avonds kan de vraag voor uw toepassing afnemen. Als deze afgenomen belasting consistent is gedurende een bepaalde periode, kunt u regels voor automatisch schalen configureren om het aantal VM-exemplaren in de schaalset te verlagen. Deze inschaalactie reduceert de kosten voor het uitvoeren van uw schaalset, aangezien u alleen het aantal exemplaren uitvoert dat vereist is om te voldoen aan de actuele vraag.

1. Kies opnieuw om **een regel toe te voegen** .
2. Maak een regel waarmee het aantal VM-exemplaren in een schaalset wordt verkleind wanneer de gemiddelde CPU-belasting gedurende een periode van tien minuten kleiner wordt dan 30%. Wanneer de regel wordt geactiveerd, wordt het aantal VM-exemplaren met 20% verlaagd.

    Gebruik dezelfde benadering als bij de vorige regel. Pas de volgende instellingen voor uw regel aan:
    
    | Parameter              | Uitleg                                                                                                          | Waarde          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operator*             | De operator die wordt gebruikt voor het vergelijken van de meetwaarden met de drempelwaarde.                                                      | Kleiner dan   |
    | *Drempelwaarde*            | Het percentage dat ervoor zorgt dat de regel voor automatisch schalen een actie activeert.                                                 | 30             |
    | *Bewerking*            | Hiermee wordt gedefinieerd of de schaalset omhoog of omlaag moet worden geschaald wanneer de regel van toepassing is en door welke toename                         | Percentage verlagen met |
    | *Aantal exemplaren*       | Het percentage VM-exemplaren dat moet worden gewijzigd wanneer de regel wordt geactiveerd.                                             | 20             |

3. Als u de regel wilt maken, selecteert u **toevoegen**


## <a name="define-autoscale-instance-limits"></a>Limieten voor automatisch schalen definiëren
In het profiel voor automatisch schalen moet een minimum, maximum en standaard aantal VM-exemplaren worden gedefinieerd. Wanneer uw regels voor automatisch schalen worden toegepast, wordt door deze instantie limieten gecontroleerd of u niet meer dan het maximum aantal exemplaren uitbreidt, of kunt u niet meer dan de minimum van exemplaren schalen.

1. Stel de volgende instantie limieten in:

    | Minimum | Maximum | Standaard|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Selecteer **Opslaan** om uw regels voor automatisch schalen en instanties toe te passen.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Aantal exemplaren in een schaalset bewaken
Als u het aantal en de status van de VM-exemplaren wilt weer geven, selecteert u **instanties** in het menu aan de linkerkant van het venster Scale set. De status geeft aan of het VM-exemplaar wordt *gemaakt* , omdat de schaalset automatisch wordt uitgeschaald of *verwijderd* als de schaal automatisch wordt geschaald.

![Een lijst met VM-exemplaren van een schaalset weer geven](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Automatisch schalen op basis van een planning
In de vorige voor beelden is een schaalset automatisch in-of uitgeschaald met elementaire metrische gegevens over de host, zoals CPU-gebruik. U kunt ook regels voor automatisch schalen maken op basis van planningen. Met deze regels op basis van een schema kunt u het aantal VM-exemplaren dat vóór een verwachte toename van de vraag van de toepassing wordt geschaald, zoals basis werk uren, automatisch laten opschalen en vervolgens automatisch schalen in het aantal exemplaren op een tijdstip dat u een minder vraag verwacht, zoals het weekend.

1. Kies **schalen** in het menu aan de linkerkant van het venster schaal instellen. Als u de bestaande regels voor automatisch schalen die zijn gemaakt in de vorige voor beelden wilt verwijderen, kiest u het prullenbak pictogram.

    ![De bestaande regels voor automatisch schalen verwijderen](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Kies **een schaal voorwaarde toevoegen**. Selecteer het potlood pictogram naast regel naam en geef een naam op zoals *uitschalen tijdens elke werkdag*.

    ![De naam van de standaard regel voor automatisch schalen wijzigen](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Selecteer het keuze rondje om **naar een specifiek aantal instanties te schalen**.
4. Als u het aantal exemplaren wilt opschalen, voert u *10* in als het aantal exemplaren.
5. Kies **specifieke dagen herhalen** voor het **plannings** type.
6. Selecteer alle werk dagen, maandag tot en met vrijdag.
7. Kies de juiste tijd zone en geef een **begin tijd** van *09:00* op.
8. Kies opnieuw om **een voor waarde voor de schaal toe te voegen** . Herhaal het proces voor het maken van een planning met de naam *schalen in de avond* die schaalt naar *3* instanties, herhaalt elke werkdag en begint om *18:00*.
9. Selecteer **Opslaan** om regels voor automatisch schalen op basis van een schema toe te passen.

    ![Regels voor automatisch schalen maken die volgens een schema worden geschaald](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Als u wilt zien hoe uw regels voor automatisch schalen worden toegepast, selecteert u **uitvoerings geschiedenis** boven in het venster **schalen** . In de lijst grafiek en gebeurtenissen wordt weer gegeven wanneer de regels voor automatisch schalen worden geactiveerd en het aantal VM-exemplaren in de schaalset wordt verhoogd of afneemt.


## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u regels voor automatisch schalen kunt gebruiken om horizon taal te schalen en het *aantal* VM-exemplaren in uw schaalset te verg Roten of verkleinen. U kunt ook verticaal schalen om de *grootte* van VM-exemplaren te vergroten of verkleinen. Zie voor meer informatie [Vertical autoscale with virtual machine scale sets](virtual-machine-scale-sets-vertical-scale-reprovision.md) (Verticaal automatisch schalen met virtuele-machineschaalsets).

Zie [Manage a virtual machine scale set with Azure PowerShell](./virtual-machine-scale-sets-manage-powershell.md) (Virtuele-machineschaalset beheren met Azure PowerShell) voor meer informatie over het beheren van uw VM-exemplaren.

Als u wilt weten hoe u waarschuwingen kunt genereren wanneer uw regels voor automatisch schalen worden geactiveerd, raadpleegt u [Use autoscale actions to send email and webhook alert notifications in Azure Monitor](../azure-monitor/autoscale/autoscale-webhook-email.md) (Acties voor automatisch schalen gebruiken om waarschuwingen per e-mail of webhooks te verzenden in Azure Monitor). U kunt ook [controlelogboeken gebruiken om waarschuwingen per e-mail of webhooks te verzenden in Azure Monitor](../azure-monitor/alerts/alerts-log-webhook.md).
