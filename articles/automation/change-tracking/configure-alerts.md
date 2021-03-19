---
title: Waarschuwingen maken voor Azure Automation-Wijzigingen bijhouden en-inventaris
description: In dit artikel leest u hoe u Azure-waarschuwingen kunt configureren om de status van de wijzigingen die worden gedetecteerd door Wijzigingen bijhouden en inventaris te melden.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/15/2020
ms.topic: conceptual
ms.openlocfilehash: 91be2f8641a061d009962cdcd03a8d56048594da
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594496"
---
# <a name="how-to-create-alerts-for-change-tracking-and-inventory"></a>Waarschuwingen voor Wijzigingen bijhouden en inventaris maken

Waarschuwingen in azure geven proactief u op de hoogte van de resultaten van runbook-taken, service status problemen of andere scenario's met betrekking tot uw Automation-account. Azure Automation bevat geen vooraf geconfigureerde waarschuwings regels, maar u kunt uw eigen waarschuwing maken op basis van de gegevens die worden gegenereerd. Dit artikel bevat richt lijnen voor het maken van waarschuwings regels op basis van wijzigingen die door Wijzigingen bijhouden en inventaris worden geïdentificeerd.

Als u niet bekend bent met Azure Monitor waarschuwingen, raadpleegt u [overzicht van waarschuwingen in Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md) voordat u begint. Zie [waarschuwingen in Logboeken vastleggen in azure monitor](../../azure-monitor/alerts/alerts-unified-log.md)voor meer informatie over waarschuwingen die gebruikmaken van logboek query's.

## <a name="create-alert"></a>Waarschuwing maken

In het volgende voor beeld ziet u dat het bestand **c:\Windows\System32\drivers\etc\hosts** op een machine is gewijzigd. Dit bestand is belang rijk omdat Windows het gebruikt om hostnamen om te zetten in IP-adressen. Deze bewerking heeft voor rang op DNS en kan leiden tot verbindings problemen. Het kan ook leiden tot omleiding van verkeer naar kwaad aardige of anderszins gevaarlijke websites.

![Grafiek met de wijziging van het bestand hosts](./media/configure-alerts/changes.png)

We gebruiken dit voor beeld om de stappen voor het maken van waarschuwingen voor een wijziging te bespreken.

1. Selecteer op de pagina **Wijzigingen bijhouden** van uw Automation-account **log Analytics**.

2. Zoek in de logboeken zoeken naar inhouds wijzigingen in het bestand **hosts** met de query `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` . Met deze query wordt gezocht naar wijzigingen in de inhoud voor bestanden met volledig gekwalificeerde padnamen die het woord bevatten `hosts` . U kunt ook een specifiek bestand vragen door het pad naar de volledig gekwalificeerde vorm te wijzigen, bijvoorbeeld met behulp van `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` .

3. Nadat de query de resultaten heeft geretourneerd, selecteert u **nieuwe waarschuwings regel** in de zoek opdracht in Logboeken om de pagina **waarschuwing maken** te openen. U kunt ook naar deze pagina navigeren via **Azure monitor** in de Azure Portal.

4. Controleer de query opnieuw en wijzig de logica van de waarschuwing. In dit geval moet de waarschuwing worden geactiveerd als er zelfs één wijziging wordt gedetecteerd op alle computers in de omgeving.

    ![Wijziging in query voor het bijhouden van wijzigingen in het bestand hosts](./media/configure-alerts/change-query.png)

5. Nadat de waarschuwings logica is ingesteld, wijst u actie groepen toe om acties uit te voeren als reactie op het activeren van de waarschuwing. In dit geval worden e-mail berichten ingesteld om te worden verzonden en wordt een ITSM-ticket (IT Service Management) gemaakt.

Volg de onderstaande stappen om waarschuwingen in te stellen om u de status van een update-implementatie te laten weten. Zie [overzicht van Azure-waarschuwingen](../../azure-monitor/alerts/alerts-overview.md)als u geen ervaring hebt met Azure-waarschuwingen.

## <a name="configure-action-groups-for-your-alerts"></a>Actie groepen voor uw waarschuwingen configureren

Zodra u uw waarschuwingen hebt geconfigureerd, kunt u een actie groep instellen. Dit is een groep acties die in meerdere waarschuwingen moet worden gebruikt. De acties kunnen e-mail meldingen, runbooks, webhooks en nog veel meer zijn. Raadpleeg [Actiegroepen maken en beheren](../../azure-monitor/alerts/action-groups.md) voor meer informatie over actiegroepen.

1. Selecteer een waarschuwing en selecteer vervolgens **nieuwe maken** onder **actie groepen**.

2. Voer een volledige naam en een korte naam in voor de actie groep. Updatebeheer maakt gebruik van de korte naam wanneer u meldingen verzendt met de opgegeven groep.

3. Voer onder **acties** een naam in waarmee de actie wordt opgegeven, bijvoorbeeld **e-mail melding**.

4. Voor **actie type** selecteert u het juiste type, bijvoorbeeld **e-mail/SMS-bericht/push/stem**.

5. Selecteer het potlood pictogram om de actie details te bewerken.

6. Vul het deel venster in voor uw actie type. Als u bijvoorbeeld **e-mail/SMS-bericht/push/Voice** wilt gebruiken om een e-mail te verzenden, voert u een actie naam in, selecteert u het selectie vakje **e-mail** , voert u een geldig e-mail adres in en selecteert u **OK**.

    ![Een e-mailactiegroep configureren](./media/configure-alerts/configure-email-action-group.png)

7. Selecteer OK in het deelvenster **Actiegroep toevoegen**.

8. Voor een waarschuwings-e-mail kunt u het onderwerp van de e-mail aanpassen. Selecteer **acties aanpassen** onder **regel maken** en selecteer vervolgens **e-mail onderwerp**.

9. Wanneer u klaar bent, selecteert u **Waarschuwingsregel maken**.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [waarschuwingen vindt u in azure monitor](../../azure-monitor/alerts/alerts-overview.md).

* Meer informatie over [logboek query's](../../azure-monitor/logs/log-query-overview.md) voor het ophalen en analyseren van gegevens uit een log Analytics-werk ruimte.

* [Gebruik en kosten beheren met Azure monitor logboeken](../../azure-monitor/logs/manage-cost-storage.md) beschrijft hoe u uw kosten kunt bepalen door de Bewaar periode van uw gegevens te wijzigen en hoe u uw gegevens gebruik kunt analyseren en waarschuwen.