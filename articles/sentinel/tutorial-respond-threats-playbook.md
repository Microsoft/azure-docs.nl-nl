---
title: 'Zelf studie: playbooks gebruiken met Automation-regels in azure Sentinel'
description: Gebruik deze zelf studie om playbooks te gebruiken in combi natie met Automation-regels in azure Sentinel om uw incident respons te automatiseren en beveiligings dreigingen op te lossen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2021
ms.author: yelevin
ms.openlocfilehash: 365ba9df39b4b3bd7397e86e6a51b285bf049242
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104600583"
---
# <a name="tutorial-use-playbooks-with-automation-rules-in-azure-sentinel"></a>Zelf studie: playbooks gebruiken met Automation-regels in azure Sentinel

In deze zelf studie leert u hoe u playbooks kunt gebruiken in combi natie met Automation-regels om uw incident respons te automatiseren en beveiligings dreigingen te herstellen die door Azure Sentinel zijn gedetecteerd. Wanneer u deze zelf studie hebt voltooid, kunt u het volgende doen:

> [!div class="checklist"]
>
> * Een Automation-regel maken
> * Een playbook maken
> * Acties toevoegen aan een Playbook
> * Een Playbook koppelen aan een Automation-regel of een analyse regel voor het automatiseren van de reactie van dreigingen

## <a name="what-are-automation-rules-and-playbooks"></a>Wat zijn Automation-regels en playbooks?

Met Automation-regels kunt u incidenten in azure-Sentinel sorteren. U kunt ze gebruiken om incidenten automatisch toe te wijzen aan de juiste mede werkers, incidenten met ruis of bekende valse positieven te sluiten, hun ernst te wijzigen en tags toe te voegen. Ze zijn ook het mechanisme waarmee u playbooks kunt uitvoeren als reactie op incidenten.

Playbooks zijn verzamelingen procedures die vanuit Azure Sentinel kunnen worden uitgevoerd als reactie op een waarschuwing of incident. Een Playbook kan u helpen uw antwoord te automatiseren en te organiseren, en kan worden ingesteld om automatisch te worden uitgevoerd wanneer specifieke waarschuwingen of incidenten worden gegenereerd door respectievelijk te worden gekoppeld aan een analyse regel of een automatiserings regel. Het kan ook hand matig op aanvraag worden uitgevoerd.

Playbooks in azure Sentinel zijn gebaseerd op werk stromen die zijn ingebouwd in [Azure Logic apps](../logic-apps/logic-apps-overview.md), wat betekent dat u alle Power, aanpassings mogelijkheden en ingebouwde sjablonen van Logic apps krijgt. Elke Playbook wordt gemaakt voor het specifieke abonnement waarvan het deel uitmaakt, maar in het **Playbooks** -scherm ziet u alle beschik bare Playbooks voor de geselecteerde abonnementen.

> [!NOTE]
> Omdat playbooks gebruikmaakt van Azure Logic Apps, kunnen er extra kosten in rekening worden gebracht. Ga naar de pagina met [Azure Logic apps](https://azure.microsoft.com/pricing/details/logic-apps/) prijzen voor meer informatie.

Als u bijvoorbeeld wilt voor komen dat gebruikers die niet kunnen worden aangetast door uw netwerk en informatie stelen, kunt u een geautomatiseerde, gemaskeerde reactie maken op incidenten die worden gegenereerd door regels die verdachte gebruikers detecteren. U begint met het maken van een Playbook die de volgende acties onderneemt:

1. Wanneer de Playbook wordt aangeroepen door een Automation-regel waarmee een incident wordt door gegeven, opent de Playbook een ticket in [ServiceNow](/connectors/service-now/) of een ander it-ticket systeem.

1. Er wordt een bericht verzonden naar uw beveiligings kanaal in [micro soft teams](/connectors/teams/) of een [toegestane vertraging](/connectors/slack/) om ervoor te zorgen dat uw beveiligings analisten op de hoogte zijn van het incident.

1. Ook worden alle gegevens in het incident in een e-mail bericht verzonden naar uw senior netwerk beheerder en beveiligings beheerder. Het e-mail bericht bevat de keuze rondjes **blok keren** en gebruikers opties **negeren** .

1. De Playbook wacht totdat er een antwoord is ontvangen van de beheerders en gaat vervolgens verder met de volgende stappen.

1. Als de beheerders **blok keren** kiezen, verzendt het een opdracht naar Azure AD om de gebruiker uit te scha kelen en een naar de firewall om het IP-adres te blok keren.

1. Als de beheerders de optie **negeren** kiezen, sluit het Playbook het incident in azure Sentinel en het ticket in ServiceNow.

Als u de Playbook wilt activeren, maakt u vervolgens een Automation-regel die wordt uitgevoerd wanneer deze incidenten worden gegenereerd. Deze regel voert de volgende stappen uit:

1. Met de regel wordt de incident status gewijzigd in **actief**.

1. Het incident wordt toegewezen aan de analist met het beheer van dit type incident.

1. De tag ' aangetast gebruiker ' wordt toegevoegd.

1. Ten slotte wordt de Playbook aangeroepen die u zojuist hebt gemaakt. ([Voor deze stap zijn speciale machtigingen vereist](automate-responses-with-playbooks.md#incident-creation-automated-response).)

Playbooks kunnen automatisch worden uitgevoerd als reactie op incidenten door het maken van Automation-regels die de Playbooks aanroepen als acties, zoals in het bovenstaande voor beeld. Ze kunnen ook automatisch worden uitgevoerd als reactie op waarschuwingen, door de analyse regel te vertellen dat er automatisch een of meer playbooks moet worden uitgevoerd wanneer de waarschuwing wordt gegenereerd. 

U kunt er ook voor kiezen om een Playbook hand matig op aanvraag uit te voeren als reactie op een geselecteerde waarschuwing.

Maak een volledige en gedetailleerde inleiding voor het automatiseren van bedreigings reacties met [automatiserings regels](automate-incident-handling-with-automation-rules.md) en [Playbooks](automate-responses-with-playbooks.md) in azure Sentinel.

> [!IMPORTANT]
>
> - **Regels voor Automation** en het gebruik van de **incident trigger** voor playbooks zijn momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

## <a name="create-a-playbook"></a>Een playbook maken

Volg deze stappen om een nieuwe Playbook te maken in azure Sentinel:

### <a name="prepare-the-playbook-and-logic-app"></a>De Playbook-en Logic-app voorbereiden

1. Selecteer in het navigatie menu van de **Azure-Sentinel** de optie **Automation**.

1. Selecteer in het bovenste menu de optie **nieuwe Playbook** **maken** en toevoegen.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-playbook.png" alt-text="Een nieuwe Playbook toevoegen":::

    Er wordt een nieuw browser tabblad geopend en u gaat naar de wizard **een logische app maken** .

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-playbook.png" alt-text="Een logische app maken":::

1. Voer uw **abonnement** en **resource groep** in en geef uw Playbook een naam onder de naam van de **logische app**.

1. Selecteer bij **regio** de Azure-regio waar de logische app-informatie moet worden opgeslagen.

1. Als u de activiteiten van deze Playbook wilt bewaken voor diagnostische doel einden, vinkt u het selectie vakje **log Analytics inschakelen** aan en voert u de naam van uw **log Analytics-werk ruimte** in.

1. Als u labels wilt Toep assen op uw Playbook, klikt u op **volgende: labels >** (niet verbonden met labels die worden toegepast door de regels voor Automation. Meer [informatie over Tags](../azure-resource-manager/management/tag-resources.md)). Klik anders op **controleren + maken**. Bevestig de details die u hebt ingevoerd en klik op **maken**.

1. Terwijl uw Playbook wordt gemaakt en geïmplementeerd (dit duurt enkele minuten), wordt u naar een scherm met de naam **micro soft. EmptyWorkflow** geleid. Wanneer het bericht ' uw implementatie is voltooid ' wordt weer gegeven, klikt **u op Ga naar resource.**

1. U wordt naar de nieuwe [Logic apps Designer](../logic-apps/logic-apps-overview.md)van Playbook geleid, waar u de werk stroom kunt ontwerpen. Er wordt een scherm weer gegeven met een korte video en veelgebruikte triggers en sjablonen voor logische apps. Meer [informatie](../logic-apps/logic-apps-create-logic-apps-from-templates.md) over het maken van een playbook met Logic apps.

1. Selecteer de sjabloon voor de **lege logische app** .

   :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-playbook-template.png" alt-text="Galerie met Logic Apps Designer-sjablonen":::

### <a name="choose-the-trigger"></a>De trigger kiezen

Elke Playbook moet beginnen met een trigger. Met de trigger wordt de actie gedefinieerd waarmee de Playbook wordt gestart en het schema dat de Playbook verwacht te ontvangen.

1. Zoek in de zoek balk naar Azure Sentinel. Selecteer **Azure Sentinel** wanneer deze wordt weer gegeven in de resultaten.

1. Op het tabblad resulterende **Triggers** ziet u de twee triggers die worden aangeboden door Azure Sentinel:
    - Wanneer een reactie op een melding van een Azure-Sentinel wordt geactiveerd
    - Wanneer een regel voor het maken van een Azure Sentinel-incident is geactiveerd

   Kies de trigger die overeenkomt met het type Playbook dat u maakt.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-trigger.png" alt-text="Kies een trigger voor uw Playbook":::

### <a name="add-actions"></a>Acties toevoegen

U kunt nu definiëren wat er gebeurt wanneer u de Playbook aanroept. U kunt acties, logische voor waarden, lussen of schakel Case-voor waarden toevoegen door een **nieuwe stap** te selecteren. Met deze selectie wordt een nieuw frame in de ontwerp functie geopend, waarin u een systeem of een toepassing kunt kiezen voor interactie met of een voor waarde die moet worden ingesteld. Voer de naam van het systeem of de toepassing in de zoek balk boven aan het frame in en kies vervolgens een van de beschik bare resultaten.

In elk van deze stappen klikt u op een veld om een deel venster met twee menu's weer te geven: **dynamische inhoud** en **expressie**. Vanuit het menu **dynamische inhoud** kunt u verwijzingen toevoegen aan de kenmerken van de waarschuwing of het incident dat is door gegeven aan de Playbook, met inbegrip van de waarden en kenmerken van alle betrokken entiteiten. Vanuit het menu **expressie** kunt u kiezen uit een groot aantal functies om extra logica toe te voegen aan uw stappen.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/logic-app.png" alt-text="Ontwerper van logische app":::

Deze scherm afbeelding toont de acties en voor waarden die u zou toevoegen bij het maken van de Playbook die wordt beschreven in het voor beeld aan het begin van dit document. Het enige verschil is dat u in de Playbook die hier wordt weer gegeven, de **waarschuwings trigger** gebruikt in plaats van de **incident trigger**. Dit betekent dat u deze Playbook rechtstreeks aanroept vanuit een Analytics-regel, niet vanuit een Automation-regel. Beide manieren om een Playbook aan te roepen, worden hieronder beschreven.

## <a name="automate-threat-responses"></a>Bedreigingsreacties automatiseren

U hebt uw Playbook gemaakt en de trigger gedefinieerd, de voor waarden ingesteld en de gewenste acties opgegeven, en de uitvoer die het gaat produceren. U moet nu de criteria bepalen waaronder deze worden uitgevoerd en het automatiserings mechanisme instellen dat wordt uitgevoerd wanneer aan deze criteria wordt voldaan.

### <a name="respond-to-incidents"></a>Reageren op incidenten

U gebruikt een Playbook om te reageren op een **incident** door een [automatiserings regel](automate-incident-handling-with-automation-rules.md) te maken die wordt uitgevoerd wanneer het incident wordt gegenereerd, en de Playbook vervolgens wordt aangeroepen.

Een Automation-regel maken:

1. Selecteer in de Blade **Automation** in het navigatie menu van Azure de optie **maken** in het bovenste menu en **Voeg vervolgens nieuwe regel toe**.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-rule.png" alt-text="Een nieuwe regel toevoegen":::

1. Het paneel **nieuwe Automation-regel maken** wordt geopend. Voer een naam in voor de regel.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-automation-rule.png" alt-text="Een Automation-regel maken":::

1. Als u wilt dat de automatiserings regel alleen van kracht wordt op bepaalde analyse regels, geeft u op welke items u wilt door de voor waarde voor de **naam analyse regel** te wijzigen.

1. Voeg alle andere voor waarden toe waarvoor de activering van de automatiserings regel afhankelijk moet zijn. Klik op **voor waarde toevoegen** en kies voor waarden in de vervolg keuzelijst. De lijst met voor waarden wordt ingevuld op basis van waarschuwings Details en entiteits-id-velden.

1. Kies de acties die u door deze Automation-regel wilt laten uitvoeren. Beschik bare acties zijn: **eigenaar toewijzen**, **status wijzigen**, **Ernst wijzigen**, **labels toevoegen** en **Playbook uitvoeren**. U kunt zoveel acties toevoegen als u wilt.

1. Als u een actie **Run Playbook** toevoegt, wordt u gevraagd om te kiezen uit de vervolg keuzelijst met beschik bare playbooks. Alleen playbooks die beginnen met de **incident trigger** kunnen worden uitgevoerd vanuit Automation-regels, zodat alleen ze in de lijst worden weer gegeven.

    > [!IMPORTANT]
    > Aan Azure Sentinel moet expliciete machtigingen worden verleend om playbooks uit te voeren vanuit Automation-regels. Als een Playbook in de vervolg keuzelijst ' lichter uit ' wordt weer gegeven, betekent dit dat Sentinel geen machtiging heeft voor de resource groep van die Playbook. Klik op de koppeling **machtigingen voor Playbook beheren** om machtigingen toe te wijzen.
    > In het paneel **machtigingen beheren** dat wordt geopend, schakelt u de selectie vakjes in van de resource groepen met de playbooks die u wilt uitvoeren en klikt u op **Toep assen**.
    > :::image type="content" source="./media/tutorial-respond-threats-playbook/manage-permissions.png" alt-text="Machtigingen beheren":::
    > - U moet **eigenaars** machtigingen hebben voor elke resource groep waaraan u Azure Sentinel-machtigingen wilt verlenen. u moet de rol van de **Logic app-bijdrager** hebben voor een resource groep die playbooks bevat die u wilt uitvoeren.
    > - Als in een multi tenant-implementatie de Playbook die u wilt uitvoeren zich in een andere Tenant bevindt, moet u de Azure Sentinel-machtiging verlenen om de Playbook uit te voeren in de Tenant van de Playbook.
    >    1. Selecteer in het navigatie menu voor de Azure-Sentinel in de Tenant van de playbooks de optie **instellingen**.
    >    1. Selecteer op de Blade **instellingen** het tabblad **instellingen** en vervolgens de **Playbook machtigingen** .
    >    1. Klik op de knop **machtigingen configureren** om het deel venster **machtigingen beheren** te openen dat hierboven wordt vermeld, en ga verder, zoals hier wordt beschreven.

1. Stel een verval datum in voor uw Automation-regel als u deze wilt.

1. Voer een getal in dat volgt op **volg orde** om te bepalen waar de regel wordt uitgevoerd in de volg orde van de automatiserings regels.

1. Klik op **Toepassen**. U bent klaar!

[Ontdek andere manieren](automate-incident-handling-with-automation-rules.md#creating-and-managing-automation-rules) om Automation-regels te maken.

### <a name="respond-to-alerts"></a>Op waarschuwingen reageren

U gebruikt een Playbook om te reageren op een **waarschuwing** door een **analyse regel** te maken of een bestaande te bewerken, die wordt uitgevoerd wanneer de waarschuwing wordt gegenereerd en uw Playbook als een geautomatiseerd antwoord te selecteren in de [wizard Analytics-regel](tutorial-detect-threats-custom.md).

1. Selecteer op de Blade **Analytics** in het navigatie menu van het Azure-Sentinel de analyse regel waarvoor u het antwoord wilt automatiseren en klik op **bewerken** in het detail venster.

1. Selecteer in de **wizard analytische regel-de pagina bestaande regel bewerken** het tabblad **automatisch antwoord** .

   :::image type="content" source="./media/tutorial-respond-threats-playbook/automated-response-tab.png" alt-text="Tabblad automatisch antwoord":::

1. Kies uw Playbook in de vervolg keuzelijst. U kunt meer dan één Playbook kiezen, maar alleen playbooks die gebruikmaken van de **waarschuwings trigger** , zijn beschikbaar.

1. Selecteer in het tabblad **controleren en maken** de optie **Opslaan**.

## <a name="run-a-playbook-on-demand"></a>Een playbook op aanvraag uitvoeren

U kunt ook een Playbook op aanvraag uitvoeren.

 > [!NOTE]
 > Alleen playbooks met de **waarschuwings trigger** kunnen op aanvraag worden uitgevoerd.

Een playbook uitvoeren op aanvraag:

1. Selecteer op de pagina **incidenten** een incident en klik op **volledige details weer geven**.

2. Klik in het tabblad **Waarschuwingen** op de waarschuwing waar u de playbook op wilt uitvoeren, schuif helemaal naar rechts en klik op **Playbooks weergeven** en selecteer een playbook om **uit te voeren** in de lijst met beschikbare playbooks in het abonnement.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u playbooks en Automation-regels in azure Sentinel kunt gebruiken om te reageren op bedreigingen. 
- Meer informatie over hoe u [proactief kunt zoeken naar bedreigingen](hunting.md) met Azure Sentinel.
