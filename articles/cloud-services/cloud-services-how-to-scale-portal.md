---
title: Een Cloud service (klassiek) automatisch schalen in de portal | Microsoft Docs
description: Meer informatie over het gebruik van de portal voor het configureren van regels voor automatisch schalen voor een Cloud service (klassieke) rollen in Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ba7b2279969acab93ac96c42e0033e76e9d0542d
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980827"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-classic-in-the-portal"></a>Automatisch schalen configureren voor een Cloud service (klassiek) in de portal

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Er kunnen voor waarden worden ingesteld voor een werk rollen van de Cloud service die een schaal-of uitvoer bewerking activeren. De voor waarden voor de rol kunnen worden gebaseerd op de CPU, schijf of netwerk belasting van de rol. U kunt ook een voor waarde instellen op basis van een berichten wachtrij of de metriek van een andere Azure-resource die aan uw abonnement is gekoppeld.

> [!NOTE]
> Dit artikel is gericht op de Cloud service (klassiek). Wanneer u een virtuele machine (klassiek) rechtstreeks maakt, wordt deze gehost in een Cloud service. U kunt een standaard virtuele machine schalen door deze te koppelen aan een [beschikbaarheidsset](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic) en ze hand matig in of uit te scha kelen.

## <a name="considerations"></a>Overwegingen
U moet rekening houden met de volgende informatie voordat u schaling voor uw toepassing configureert:

* Schalen wordt beïnvloed door het gebruik van kernen.

    Grotere rolinstanties gebruiken meer kernen. U kunt een toepassing alleen schalen binnen de limiet van kernen voor uw abonnement. Stel bijvoorbeeld dat uw abonnement een limiet heeft van 20 kernen. Als u een toepassing uitvoert met twee middel grote Cloud Services (in totaal 4 kernen), kunt u alleen andere Cloud service-implementaties in uw abonnement opschalen door de resterende 16 kernen. Zie [grootten van Cloud Services](cloud-services-sizes-specs.md)voor meer informatie over grootten.

* U kunt schalen op basis van een drempel waarde voor een wachtrij bericht. Zie [How to use the Queue Storage service](../storage/queues/storage-dotnet-how-to-use-queues.md)(Engelstalig) voor meer informatie over het gebruik van wacht rijen.

* U kunt ook andere resources die zijn gekoppeld aan uw abonnement, schalen.

* Als u een hoge Beschik baarheid van uw toepassing wilt inschakelen, moet u ervoor zorgen dat deze wordt geïmplementeerd met twee of meer rolinstanties. Zie [Service overeenkomst](https://azure.microsoft.com/support/legal/sla/)voor meer informatie.

* Automatisch schalen gebeurt alleen als alle rollen de status **gereed** hebben.  


## <a name="where-scale-is-located"></a>Waar de schaal zich bevindt
Nadat u uw Cloud service hebt geselecteerd, moet de Blade Cloud service zichtbaar zijn.

1. Selecteer op de Blade Cloud service, op de tegel **rollen en instanties** , de naam van de Cloud service.   
   **Belang rijk**: Zorg ervoor dat u op de Cloud service functie klikt, niet de rolinstantie die zich onder de rol bevindt.

    ![Scherm afbeelding van de tegel rollen en instanties met de rol worker met S B wachtrij 1, in rood beschreven.](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Selecteer de tegel **schalen** .

    ![Scherm afbeelding van de pagina bewerkingen met de tegel verkopen die rood wordt beschreven.](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Automatisch schalen
U kunt schaal instellingen configureren voor een functie met twee modi **hand matig** of **automatisch**. Hand matig is net zoals u verwacht, stelt u het absolute aantal exemplaren in. Met automatisch kunt u regels instellen die bepalen hoe en hoeveel u moet schalen.

Stel de optie **schalen op** in voor het **plannen en uitvoeren van regels**.

![afbeelding Cloud Services schaal instellingen met profiel en regel](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Een bestaand profiel.
2. Voeg een regel voor het bovenliggende profiel toe.
3. Voeg nog een profiel toe.

Selecteer **profiel toevoegen**. Het profiel bepaalt welke modus u wilt gebruiken voor de schaal: **altijd**, **terugkeer patroon**, **vaste datum**.

Nadat u het profiel en de regels hebt geconfigureerd, selecteert u het pictogram **Opslaan** bovenaan.

#### <a name="profile"></a>Profiel
Het profiel stelt het minimum-en maximum aantal exemplaren voor de schaal en ook wanneer dit schaal bereik actief is.

* **Altijd**

    Bewaar dit bereik van instanties altijd beschikbaar.  

    ![Cloud service die altijd wordt geschaald](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Terugkeerpatroon**

    Kies een set dagen van de week om te schalen.

    ![Cloud service schalen met een terugkeer patroon](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Vaste datum**

    Een vast datum bereik voor het schalen van de rol.

    ![CLoud service schalen met een vaste datum](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Nadat u het profiel hebt geconfigureerd, selecteert u de knop **OK** onder aan de Blade profiel.

#### <a name="rule"></a>Regel
Regels worden toegevoegd aan een profiel en vertegenwoordigen een voor waarde waarmee de schaal wordt geactiveerd.

De regel trigger is gebaseerd op de metrische gegevens van de Cloud service (CPU-gebruik, schijf activiteit of netwerk activiteit) waaraan u een voorwaardelijke waarde kunt toevoegen. Daarnaast kunt u de trigger hebben op basis van een berichten wachtrij of de metriek van een andere Azure-resource die aan uw abonnement is gekoppeld.

![Scherm afbeelding van het dialoog venster regel met de optie voor de metrische naam in het rood.](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Nadat u de regel hebt geconfigureerd, selecteert u de knop **OK** onder aan de Blade regel.

## <a name="back-to-manual-scale"></a>Terug naar hand matig schalen
Ga naar de [schaal instellingen](#where-scale-is-located) en stel de optie **schalen op** in op het **aantal exemplaren dat ik hand matig heb ingevoerd**.

![Instellingen voor de schaal van Cloud Services met het profiel en de regel](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Met deze instelling wordt automatisch schalen van de rol verwijderd en kunt u het aantal instanties rechtstreeks instellen.

1. De optie schaal (hand matig of automatisch).
2. De schuif regelaar van een rolinstantie om de instanties in te stellen waarop moet worden geschaald.
3. De exemplaren van de rol waarop moet worden geschaald.

Nadat u de schaal instellingen hebt geconfigureerd, selecteert u het pictogram **Opslaan** bovenaan.
