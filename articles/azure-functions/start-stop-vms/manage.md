---
title: Vm's voor starten/stoppen v2 beheren (preview-versie)
description: In dit artikel leest u hoe u de status van uw virtuele Azure-machines kunt controleren die worden beheerd door de functie Vm's starten/stoppen v2 (preview) en andere beheer taken uit te voeren.
services: azure-functions
ms.subservice: ''
ms.date: 03/16/2021
ms.topic: conceptual
ms.openlocfilehash: 477e3fe51f05bb4323642f56233f1995e6394eec
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111713"
---
# <a name="how-to-manage-startstop-vms-v2-preview"></a>Start-en stop Vm's beheren v2 (preview-versie)

## <a name="azure-dashboard"></a>Azure-dashboard

Start/Stop Vm's v2 (preview) bevat een [dash board](../../azure-monitor/visualizations.md#azure-dashboards) waarmee u inzicht krijgt in het beheer bereik en recente bewerkingen op uw vm's. Het is een snelle en eenvoudige manier om de status te controleren van elke bewerking die wordt uitgevoerd op uw Azure-Vm's. De visualisatie in elke tegel is gebaseerd op een logboek query en u kunt de query bekijken door in de rechter bovenhoek van de tegel de optie **Blade openen in Logboeken** te selecteren. Hiermee opent u het hulp programma [log Analytics](../../azure-monitor/logs/log-analytics-overview.md#starting-log-analytics) in het Azure Portal en kunt u de query evalueren en aanpassen om uw behoeften te ondersteunen, zoals aangepaste [logboek waarschuwingen](../../azure-monitor/alerts/alerts-log.md), een aangepaste [werkmap](../../azure-monitor/visualize/workbooks-overview.md), enzovoort.

De logboek gegevens die elke tegel in het dash board weergeeft, worden elk uur vernieuwd, met een optie voor hand matige vernieuwing op aanvraag door te klikken op het pictogram **vernieuwen** op een bepaalde visualisatie of door het volledige dash board te vernieuwen.

Zie de volgende [zelf studie](../../azure-monitor/visualize/tutorial-logs-dashboards.md)voor meer informatie over het werken met een dash board op basis van een logboek.

## <a name="configure-email-notifications"></a>E-mailmeldingen configureren

E-mail meldingen wijzigen na het starten/stoppen van Vm's v2 (preview) is geïmplementeerd, kunt u de actie groep wijzigen die tijdens de implementatie is gemaakt.

1. Ga in het Azure Portal naar **controle** en vervolgens op **waarschuwingen**. Selecteer **acties beheren**.

1. Selecteer op de pagina **acties beheren** de actie groep met de naam **StartStopV2_VM_Notication**.

    :::image type="content" source="media/manage/alerts-action-groups.png" alt-text="Scherm afbeelding van de pagina actie groepen.":::

1. Op de pagina **StartStopV2_VM_Notification** kunt u de opties voor E-mail/SMS/push/Voice-meldingen wijzigen. Voeg andere acties toe of werk uw bestaande configuratie bij in deze actie groep en klik vervolgens op **OK** om uw wijzigingen op te slaan.

    :::image type="content" source="media/manage/email-notification-type-example.png" alt-text="Scherm afbeelding van de pagina E-mail/SMS/push/Voice met een voor beeld van een e-mail adres bijgewerkt.":::

    Zie [actie groepen](../../azure-monitor/alerts/action-groups.md) voor meer informatie over actie groepen

De volgende scherm afbeelding is een voor beeld van een e-mail bericht dat wordt verzonden wanneer virtuele machines worden afgesloten met de functie.

:::image type="content" source="media/manage/email-notification-example.png" alt-text="Scherm afbeelding van een voor beeld van een e-mail bericht dat wordt verzonden wanneer virtuele machines worden afgesloten met het onderdeel.":::

## <a name="next-steps"></a>Volgende stappen

Zie problemen [oplossen met het starten/stoppen van vm's v2](troubleshoot.md) (preview) voor meer informatie over het afhandelen van problemen tijdens het beheer van vm's.