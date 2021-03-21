---
title: Veelgestelde vragen over het controleren van waarschuwingen en rapporten
description: In dit artikel vindt u antwoorden op veelgestelde vragen over de Azure Backup bewakings waarschuwing en Azure Backup rapporten.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 4e2585009b531512e54ffa889a05ec50038dd619
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98805864"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Bewakings waarschuwing Azure Backup-Veelgestelde vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Backup bewaking en rapportage.

## <a name="configure-azure-backup-reports"></a>Azure Backup-rapporten configureren

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>Hoe kan ik controleren of rapport gegevens in een werk ruimte van Log Analytics (LA) zijn gestart?

Ga naar de LA-werk ruimte die u hebt geconfigureerd. Ga naar het menu-item **logs** en voer de query uit `CoreAzureBackup | take 1` . Als u een record ziet die wordt geretourneerd, betekent dit dat gegevens zijn gestart in de werk ruimte. Het pushen van de eerste gegevens kan tot wel 24 uur duren.

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>Wat is de frequentie van het pushen van gegevens naar een LA-werk ruimte?

De diagnostische gegevens van de kluis worden naar de Log Analytics-werk ruimte gepompt met enige vertraging. Elke gebeurtenis arriveert in de Log Analytics werk ruimte van 20 tot 30 minuten nadat deze is gepusht vanuit de Recovery Services kluis. Hier vindt u meer informatie over de vertraging:

* In alle oplossingen worden de ingebouwde waarschuwingen van de back-upservice gepusht zodra ze zijn gemaakt. Deze worden doorgaans weer gegeven in de Log Analytics-werk ruimte na 20 tot 30 minuten.
* In alle oplossingen worden back-uptaken op aanvraag en herstel taken gepusht zodra deze zijn voltooid.
* Voor alle oplossingen behalve SQL backup worden geplande back-uptaken gepusht zodra deze zijn voltooid.
* Voor SQL backup, omdat logboek back-ups om de 15 minuten kunnen worden uitgevoerd, worden de gegevens voor alle voltooide geplande back-uptaken, inclusief logboeken, batches en elke 6 uur gepusht.
* Voor alle oplossingen worden andere gegevens, zoals het back-upitem, het beleid, de herstel punten, de opslag, enzovoort, ten minste één keer per dag gepusht.
* Een wijziging in de back-upconfiguratie (zoals het wijzigen van beleid of het bewerken van beleid) activeert een push van alle gerelateerde back-upgegevens.

### <a name="how-long-can-i-retain-reporting-data"></a>Hoe lang kan ik rapport gegevens behouden?

Nadat u een werk ruimte van LA hebt gemaakt, kunt u ervoor kiezen om Maxi maal 2 jaar gegevens te bewaren. Standaard behoudt een LA-werk ruimte gegevens 31 dagen.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>Worden al mijn gegevens weer gegeven in rapporten nadat ik de werk ruimte LA heb geconfigureerd?

 Alle gegevens die zijn gegenereerd nadat u de diagnostische instellingen hebt geconfigureerd, worden naar de werk ruimte LA gepusht en zijn beschikbaar in rapporten. Taken in uitvoering worden niet gepusht voor rapportage. Nadat de taak is voltooid of mislukt, wordt deze naar rapporten verzonden.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Kan ik rapporten weer geven over kluizen en abonnementen?

Ja, u kunt rapporten weer geven in kluizen en abonnementen, evenals in regio's. Uw gegevens kunnen zich bevinden in een enkele LA-werk ruimte of een groep LA-werk ruimten.

### <a name="can-i-view-reports-across-tenants"></a>Kan ik rapporten weer geven over tenants?

Als u een [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) -gebruiker bent met gedelegeerde toegang tot de abonnementen of La-werk ruimten van uw klanten, kunt u back-uprapporten gebruiken om gegevens in al uw tenants weer te geven.

## <a name="recovery-services-vault"></a>Recovery Services-kluis

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Hoe lang duurt het voordat de taak status van de Azure Backup Agent wordt weer gegeven in de portal?

Het Azure Portal kan Maxi maal 15 minuten duren om de taak status van de Azure Backup Agent weer te geven.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Hoelang duurt het om een waarschuwing te geven als een back-uptaak mislukt?

Er treedt een waarschuwing binnen 20 minuten na de Azure Backup fout.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Is er sprake van een e-mail bericht dat niet wordt verzonden als er meldingen zijn geconfigureerd?

Ja. In de volgende situaties worden er geen meldingen verzonden:

* Als meldingen per uur worden geconfigureerd en er binnen het uur een waarschuwing wordt gegenereerd en opgelost
* Wanneer een taak wordt geannuleerd
* Als een tweede back-uptaak mislukt, omdat de oorspronkelijke back-uptaak wordt uitgevoerd

## <a name="next-steps"></a>Volgende stappen

Lees de andere veelgestelde vragen:

* [Veelgestelde vragen](backup-azure-vm-backup-faq.yml) over Azure VM-back-ups.
* [Veelgestelde vragen](backup-azure-file-folder-backup-faq.md) over de Azure Backup-agent
