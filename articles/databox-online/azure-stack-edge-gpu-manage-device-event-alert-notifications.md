---
title: Meldingen voor gebeurtenis waarschuwingen voor uw Azure Stack Edge Pro-resources beheren | Microsoft Docs
description: Hierin wordt beschreven hoe u de Azure Portal gebruikt voor het beheren van waarschuwingen voor faxgebeurtenissen op uw Azure Stack Edge Pro-resources.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 585343137a4a8fd8a1fb591c640e1183d71c0fd3
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443094"
---
# <a name="manage-device-event-alert-notifications-on-azure-stack-edge-pro-resources"></a>Meldingen voor gebeurtenis waarschuwingen voor apparaten beheren op Azure Stack Edge Pro-resources

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u actie regels maakt in de Azure Portal voor het activeren of onderdrukken van waarschuwings meldingen voor gebeurtenis gebeurtenissen die optreden in een resource groep, een Azure-abonnement of een individuele Azure Stack Edge-resource. Dit artikel is van toepassing op alle modellen van Azure Stack Edge.  

## <a name="about-action-rules"></a>Over actie regels

Een actie regel kan waarschuwings meldingen activeren of onderdrukken. De actie regel wordt toegevoegd aan een *actie groep* : een set meldings voorkeuren die wordt gebruikt om gebruikers op de hoogte te stellen die moeten reageren op waarschuwingen die zijn geactiveerd in verschillende contexten voor een resource of set resources.

Zie [een actie regel configureren](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule)voor meer informatie over actie regels. Zie voor meer informatie over actie groepen [actie groepen maken en beheren in de Azure Portal](../azure-monitor/alerts/action-groups.md).

> [!NOTE]
> De functie voor actie regels is beschikbaar als preview-versie. Sommige schermen en stappen kunnen veranderen naarmate het proces wordt verfijnd.


## <a name="create-an-action-rule"></a>Een actie regel maken

Voer de volgende stappen uit in de Azure Portal om een actie regel te maken voor uw Azure Stack edge-apparaat.

> [!NOTE]
> Met deze stappen maakt u een actie regel die meldingen naar een actie groep verzendt. Zie [een actie regel configureren](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule)voor meer informatie over het maken van een actie regel om meldingen te onderdrukken.

1. Ga naar het Azure Stack edge-apparaat in de Azure Portal en ga vervolgens naar **bewaking > waarschuwingen**. Selecteer **acties beheren**.

   ![Bewakings waarschuwingen, beheer acties weer geven](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-01.png)

2. Selecteer **actie regels (preview)** en selecteer vervolgens **+ nieuwe actie regel**.

   ![Acties beheren, optie actie regels](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-02.png)

3. In het scherm **actie regel maken** gebruikt u **bereik** om een Azure-abonnement, resource groep of doel resource te selecteren. De actie regel wordt toegepast op alle waarschuwingen die binnen dat bereik worden gegenereerd.

   1. Kies **selecteren** op basis van het **bereik**.

      ![Een bereik voor een nieuwe actie regel selecteren](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-01.png)

   2. Selecteer het **abonnement** voor de actie regel, eventueel filteren op een **resource** type. Als u wilt filteren op Azure Stack rand bronnen, selecteert u **Data Box edge apparaten (dataBoxEdge)**.

      Het **resource** gebied bevat een lijst met de beschik bare resources op basis van uw selecties.
  
      ![Beschik bare resources op het scherm bereik selecteren](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-02.png)

   3. Schakel het selectie vakje in voor elke resource waarop u de regel wilt Toep assen. U kunt het abonnement, de resource groepen of afzonderlijke resources selecteren. Selecteer vervolgens **Done**.

      ![Voorbeeld instellingen voor een actie regel bereik](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-03.png)

      In het scherm **actie regel maken** wordt het geselecteerde bereik weer gegeven.

      ![Voltooid bereik voor een actie regel Azure Stack rand](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-04.png)

4. **Filter** opties gebruiken om de toepassing van de regel te beperken tot subset van waarschuwingen binnen het geselecteerde bereik.

   1. Selecteer **toevoegen** om het deel venster **filters toevoegen** te openen.

      ![Optie voor het toevoegen van filters aan een actie regel](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-01.png)

   2. Voeg onder **filters** elk filter toe dat u wilt Toep assen. Selecteer voor elk filter het filter type, de **operator** en de **waarde**.
   
      Zie [filter criteria](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#filter-criteria)voor een lijst met filter opties.

      De onderstaande voorbeeld filters zijn van toepassing op alle waarschuwingen op Ernst niveau 2, 3 en 4 die de bewakende service voor Azure Stack rand bronnen heeft gegenereerd.

      Wanneer u klaar bent met het toevoegen van filters, selecteert u **gereed**.
   
      ![Voorbeeld filter voor een actie regel](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-02.png)

5. Selecteer in het scherm **actie regel maken** de **actie groep** om een regel te maken waarmee meldingen worden verzonden. Klik vervolgens op **acties** en kies **selecteren**.

   ![Actie groeps optie voor het maken van een actie regel die meldingen verzendt](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-01.png)

   > [!NOTE]
   > Als u een regel wilt maken die meldingen onderdrukt, kiest u **onderdrukken**. Zie [een actie regel configureren](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule)voor meer informatie.

6. Selecteer de actie groep die u met deze actie regel wilt gebruiken. Kies dan de optie **Selecteren**. Uw nieuwe actie regel wordt toegevoegd aan de meldings voorkeuren van de geselecteerde actie groep.

   Als u een nieuwe actie groep wilt maken, selecteert u **+ actie groep maken** en volgt u de stappen in [een actie groep maken met behulp van de Azure Portal](../azure-monitor/alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal).

   ![Selecteer een actie groep die u met de regel wilt gebruiken en kies vervolgens selecteren.](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-02.png)

7. Geef een **naam** en **Beschrijving** op voor de nieuwe actie regel en wijs de regel toe aan een resource groep.

9. De nieuwe regel is standaard ingeschakeld. Als u de regel niet onmiddellijk wilt gebruiken, selecteert u **Nee** om het **maken van de regel bijwerken in te scha kelen**.

10. Wanneer u klaar bent met de instellingen, selecteert u **maken**.

    ![Voltooide instellingen voor een actie regel waarmee waarschuwings meldingen worden verzonden](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-completed-settings.png)

    Het scherm **actie regels (preview-versie)** wordt geopend, maar mogelijk wordt de nieuwe actie regel niet onmiddellijk weer gegeven. De focus is **alle** resource groepen.

11. Als u de nieuwe actie regel wilt zien, selecteert u de resource groep voor de regel.

    ![Het scherm actie regels met de nieuwe regel wordt weer gegeven](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-displayed.png)


## <a name="view-notifications"></a>Meldingen weergeven

Meldingen worden uitgevoerd wanneer een nieuwe gebeurtenis een waarschuwing activeert voor een resource die binnen het bereik van een actie regel valt.

De actie groep voor een regel sets die een melding ontvangen en het type melding dat een e-mail, een bericht van een korte bericht service (SMS) of beide wordt verzonden.

Het kan enkele minuten duren om meldingen te ontvangen nadat een waarschuwing is geactiveerd.

De e-mail melding ziet er ongeveer als volgt uit.

![Voor beeld van een e-mail melding voor een actie regel](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/sample-action-rule-email-notification.png)


## <a name="next-steps"></a>Volgende stappen

<!-- - See [Create and manage action groups in the Azure portal](../azure-monitor/alerts/action-groups.md) for guidance on creating a new action group.
- See [Configure an action rule](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule) for more info about creating action rules that send or suppress alert notifications. -2 bullets referenced above. Making room for local tasks in "Next Steps." --> 
- Zie [uw Azure stack Edge Pro bewaken](azure-stack-edge-monitor.md) voor meer informatie over het controleren van Apparaatinstellingen, de status van de hardware en de metrische grafieken. 
- Zie [Azure monitor gebruiken](azure-stack-edge-gpu-enable-azure-monitor.md) voor informatie over het optimaliseren van Azure Monitor voor Azure stack Edge Pro GPU-apparaten.
- Zie [metrische waarschuwingen maken, weer geven en beheren met behulp van Azure monitor koppelings doel](../azure-monitor/alerts/alerts-metric.md) voor informatie over het beheren van afzonderlijke waarschuwingen.