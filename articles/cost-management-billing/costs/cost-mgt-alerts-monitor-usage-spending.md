---
title: Gebruik en uitgaven bewaken met kostenwaarschuwingen
description: In dit artikel wordt beschreven hoe u met kostenwaarschuwingen het gebruik en de uitgaven in Azure Cost Management kunt bewaken.
author: bandersmsft
ms.author: banders
ms.date: 09/03/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: d1546ef92d923b9d72c4aca0ddf61d2b93646656
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602348"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Kostenwaarschuwingen gebruiken om gebruik en uitgaven te controleren

In dit artikel leert u hoe u met waarschuwingen van Cost Management uw Azure-gebruik en -uitgaven kunt controleren. Kostenwaarschuwingen worden automatisch gegenereerd op basis van wanneer Azure-resources worden verbruikt. In waarschuwingen worden alle actieve waarschuwingen voor kostenbeheer en facturering samen op één plek weergegeven. Wanneer uw verbruik een bepaalde drempelwaarde heeft bereikt, worden er waarschuwingen gegenereerd door Cost Management. Er zijn drie soorten kostenwaarschuwingen: budgetwaarschuwingen, tegoedwaarschuwingen en waarschuwingen voor het bestedingsquota van afdelingen.

## <a name="budget-alerts"></a>Budgetwaarschuwingen

Met budgetwaarschuwingen wordt u gewaarschuwd wanneer uitgaven, op basis van het gebruik of de kosten, de hoeveelheid overschrijden die is gedefinieerd in de [Waarschuwingsvoorwaarde van het budget](tutorial-acm-create-budgets.md). Cost Management-budgetten worden gemaakt met behulp van de Azure-portal of de [Azure-verbruiks](/rest/api/consumption)-API.

In de Azure-portal worden budgetten gedefinieerd op basis van de kosten. Met behulp van de Azure-verbruiks-API worden budgetten gedefinieerd op basis van kosten of verbruik. Budgetwaarschuwingen ondersteunen zowel op kosten gebaseerde als op gebruik gebaseerde budgetten. Budgetwaarschuwingen worden automatisch gegenereerd wanneer wordt voldaan aan de voorwaarden voor budgetwaarschuwingen. U kunt alle kostenwaarschuwingen weergeven in de Azure-portal. Wanneer er een waarschuwing wordt gegenereerd, wordt deze weergegeven in kostenmeldingen. Er wordt ook een e-mailbericht verzonden naar de personen in de lijst met ontvangers van het budget.

U kunt de Budget-API gebruiken om e-mailmeldingen in een andere taal te verzenden. Raadpleeg [Ondersteunde talen voor e-mails voor budgetwaarschuwingen](manage-automation.md#supported-locales-for-budget-alert-emails) voor meer informatie.

## <a name="credit-alerts"></a>Tegoedwaarschuwingen

Met tegoedwaarschuwingen wordt u op de hoogte gesteld van het verbruik van uw Azure-vooruitbetaling (voorheen financiële toezegging). Azure-vooruitbetaling is voor organisaties met een Enterprise Agreement. Tegoedwaarschuwingen worden automatisch gegenereerd bij 90% en 100% van uw Azure-vooruitbetalingstegoed. Wanneer er een waarschuwing wordt gegenereerd, wordt deze weergegeven in de kostenmeldingen en in het e-mailbericht dat naar de eigenaren van het account wordt verzonden.

## <a name="department-spending-quota-alerts"></a>Waarschuwingen voor bestedingsquota van afdelingen

Bij waarschuwingen voor bestedingsquota van afdelingen krijgt u een melding wanneer de uitgaven van een afdeling een vaste drempelwaarde van het quotum bereiken. Uitgavenquota worden in EA Portal geconfigureerd. Wanneer een drempelwaarde wordt bereikt, wordt een e-mailbericht gegenereerd, naar de afdelingseigenaren verzonden en weergegeven in de kostenmeldingen. Bijvoorbeeld 50% of 75% van het quotum.

## <a name="supported-alert-features-by-offer-categories"></a>Ondersteunde waarschuwingsfuncties per aanbiedingscategorie

Ondersteuning voor waarschuwingstypen is afhankelijk van het type Azure-account dat u hebt (Microsoft-aanbieding). In de volgende tabel worden de waarschuwingsfuncties vermeld die worden ondersteund door de verschillende Microsoft-aanbiedingen. U kunt de volledige lijst met Microsoft-aanbiedingen bekijken op [Inzicht in Cost Management-gegevens](understand-cost-mgt-data.md).

| Waarschuwingstype | Enterprise Agreement | Microsoft-klantovereenkomst | Web direct/betalen per gebruik |
|---|---|---|---|
| Budget | ✔ | ✔ | ✔ |
| Tegoed | ✔ |✘ | ✘ |
| Bestedingsquotum van afdeling | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Kostenwaarschuwingen bekijken

Als u kostenwaarschuwingen wilt bekijken, opent u het gewenste bereik in Azure Portal en selecteert u in het menu de optie **Budgetten**. Gebruik de pill **Bereik** om naar een ander bereik over te schakelen. Selecteer in het menu de optie **Kostenwaarschuwingen**. Zie [Understand and work with scopes](understand-work-scopes.md) (Engelstalig) voor meer informatie over bereiken.

![Voorbeeldafbeelding van waarschuwingen die worden weergegeven in Cost Management](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Het totale aantal actieve en genegeerde waarschuwingen wordt weergegeven op de pagina met kostenwaarschuwingen.

Voor alle waarschuwingen wordt het waarschuwingstype weergegeven. Voor een budgetwaarschuwing wordt vermeld waarom deze is gegenereerd en wat de naam is van het budget waarop deze van toepassing is. Elke waarschuwing toont de datum waarop deze is gegenereerd, de status en het bereik (abonnement of beheergroep) waarop de waarschuwing van toepassing is.

Mogelijke statussen zijn **Actief** en **Genegeerd**. De status Actief geeft aan dat de waarschuwing nog steeds van toepassing is. De status Genegeerd geeft aan dat iemand de waarschuwing heeft gemarkeerd om aan te geven dat deze niet meer van toepassing is.

Selecteer een waarschuwing in de lijst om de details hiervan te bekijken. Met de waarschuwingsdetails wordt meer informatie gegeven over de waarschuwing. Budgetwaarschuwingen bevatten een koppeling naar het budget. Als er een aanbeveling beschikbaar is voor een budgetwaarschuwing, wordt er ook een koppeling naar de aanbeveling weergegeven. Budgetwaarschuwingen, kredietwaarschuwingen en waarschuwingen voor bestedingsquota van afdelingen bevatten een koppeling naar de kostenanalyse, waar u de kosten voor het bereik van de waarschuwing kunt bekijken. In het volgende voorbeeld ziet u uitgaven voor een afdeling met waarschuwingsdetails.

![Voorbeeldafbeelding met uitgaven voor een afdeling met waarschuwingsdetails](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Wanneer u de details van een genegeerde waarschuwing bekijkt, kunt u deze opnieuw activeren als handmatige actie vereist is. In de volgende afbeelding ziet u een voorbeeld.

![Voorbeeldafbeelding met opties voor negeren en opnieuw activeren](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Zie ook

- Als u nog geen budget hebt gemaakt of waarschuwingsvoorwaarden voor een budget hebt ingesteld, voltooit u de zelfstudie [Budgetten maken en beheren](tutorial-acm-create-budgets.md).