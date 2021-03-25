---
title: Azure-kosten toewijzen
description: In dit artikel wordt uitgelegd hoe u regels voor kostentoewijzing maakt om de kosten van abonnementen, resourcegroepen of tags over anderen te verdelen.
author: bandersmsft
ms.author: banders
ms.date: 03/23/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: 414ce626d76c9b7a7d073d6cbfa5a5f4446c3073
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025523"
---
# <a name="create-and-manage-azure-cost-allocation-rules-preview"></a>Azure-regels voor kostentoewijzing maken en beheren (preview-versie)

Grote ondernemingen hebben vaak Azure-services of -resources die centraal worden beheerd, maar die worden gebruikt door verschillende interne afdelingen of bedrijfseenheden. Doorgaans wil het centrale beheerteam de kosten van de gedeelde services opnieuw toewijzen aan de interne afdelingen of organisatorische bedrijfseenheden die de services actief gebruiken. Dit artikel helpt u bij het begrijpen en gebruiken van kostentoewijzing in Azure Cost Management.

Met kostentoewijzing kunt u de kosten van gedeelde services van abonnementen, resourcegroepen of tags naar andere abonnementen, resourcegroepen of tags in uw organisatie opnieuw toewijzen of verdelen. Kostentoewijzing verschuift de kosten van de gedeelde services naar een ander abonnement, andere resourcegroepen of tags die eigendom zijn van de verbruikende interne afdelingen of bedrijfseenheden. Met andere woorden, kostentoewijzing helpt bij het beheren en weergeven van _kostenverantwoordelijkheid_ van de ene plaats naar de andere.

Kostentoewijzing heeft geen invloed op uw factuur. De factureringsverantwoordelijkheden zijn niet veranderd. Het primaire doel van kostentoewijzing is om u te helpen kosten door te berekenen aan anderen. Alle terugvorderingsprocessen vinden plaats in uw organisatie buiten Azure. Met kostentoewijzing kunt u kosten terugboeken door ze weer te geven wanneer ze opnieuw worden toegewezen of gedistribueerd.

De toegewezen kosten worden weergegeven in de kostenanalyse. Ze worden weergegeven als aanvullende items die zijn gekoppeld aan de doelabonnementen, resourcegroepen of tags die u opgeeft wanneer u een kostentoewijzingsregel maakt.

> [!NOTE]
> De functie voor kostentoewijzing van Azure Cost Management is momenteel beschikbaar als openbare preview. Sommige functies in Cost Management worden mogelijk niet ondersteund of hebben een beperkte functionaliteit.

## <a name="prerequisites"></a>Vereisten

- Kostentoewijzing biedt momenteel alleen ondersteuning voor klanten met een [Microsoft-klantovereenkomst](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) of een [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/).
- Om een ​​kostentoewijzingsregel te maken of te beheren, moet u een Enterprise Administrator-account gebruiken voor [Enterprise Agreements](../manage/understand-ea-roles.md). U moet een [factureringsaccount](../manage/understand-mca-roles.md) hebben voor een Microsoft-klantovereenkomst.

## <a name="create-a-cost-allocation-rule"></a>Een regel voor kostentoewijzing maken

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com/).
2. Navigeer naar **Kostenbeheer en facturering** > **Kostenbeheer**.
3. Selecteer onder **Instellingen** > **Configuratie** **Kostentoewijzing (preview)** .
4. Zorg ervoor dat de juiste EA-inschrijving of factureringsaccount is geselecteerd.
5. Selecteer **+Toevoegen**.
6. Voer een beschrijvende tekst in voor de naam van de kostentoewijzingsregel.

:::image type="content" source="./media/allocate-costs/rule-name.png" alt-text="Voorbeeld van het maken van een regelnaam" lightbox="./media/allocate-costs/rule-name.png" :::

De begindatum van de evaluatie van de regel wordt gebruikt om de vooraf ingevulde kostentoewijzingspercentages te genereren.

1. Selecteer **Rescources toevoegen** en selecteer vervolgens abonnementen, resourcegroepen of tags om te kiezen welke kosten u wilt verdelen.
2. Selecteer **Doelen toevoegen** en selecteer vervolgens abonnementen, resourcegroepen of tags waar de kosten aan moeten worden toegewezen.
3. Als u extra regels voor kostentoewijzing wilt maken, herhaalt u dit proces.

## <a name="configure-the-allocation-percentage"></a>Het toewijzingspercentage configureren

Configureer het toewijzingspercentage om te definiëren hoe de kosten proportioneel worden verdeeld over de opgegeven doelen. U kunt handmatig gehele getalpercentages definiëren om een ​​toewijzingsregel te maken. Of u kunt de kosten proportioneel splitsen op basis van het huidige gebruik van de berekening, opslag of het netwerk over de opgegeven doelen.

Wanneer kosten worden gedistribueerd op basis van de berekeningskosten, opslagkosten of netwerkkosten, wordt het evenredige percentage afgeleid door de kosten van het geselecteerde doel te evalueren. De kosten zijn gekoppeld aan het resourcetype voor de huidige factureringsmaand.

Wanneer kosten evenredig worden verdeeld over de totale kosten, wordt het evenredige percentage toegewezen door de som of de totale kosten van de geselecteerde doelen voor de huidige factureringsmaand.

:::image type="content" source="./media/allocate-costs/cost-distribution.png" alt-text="Voorbeeld van een toewijzingspercentage" lightbox="./media/allocate-costs/cost-distribution.png" :::

Eenmaal ingesteld, staan ​​de vooraf ingevulde percentages vast. Ze worden gebruikt voor alle actieve toewijzingen. De percentages worden alleen gewijzigd wanneer de regel handmatig wordt bijgewerkt.

1. Selecteer een van de volgende opties in de lijst **Percentage vooraf aanvullen tot**.
    - **Gelijkmatig verdelen**: elk van de doelen ontvangt een gelijk percentage van de totale kosten.
    - **Totale kosten**: maakt een verhouding die evenredig is met de doelen op basis van de totale kosten. De verhouding wordt gebruikt om de kosten van de geselecteerde bronnen te verdelen.
    - **Berekeningskosten**: maakt een verhouding tot de doelen op basis van hun Azure-berekeningskosten (resourcetypen in de [Microsoft.Compute](/azure/templates/microsoft.compute/allversions)-naamruimte. De verhouding wordt gebruikt om de kosten te verdelen over de geselecteerde bronnen.
    - **Opslagkosten**: maakt een verhouding die evenredig is met de doelen op basis van hun kosten voor Azure Storage (resourcetypen in de [Microsoft.Storage](/azure/templates/microsoft.storage/allversions)-naamruimte). De verhouding wordt gebruikt om de kosten van de geselecteerde bronnen te verdelen.
    - **Netwerkkosten**: maakt een verhouding die evenredig is met de doelen op basis van hun netwerkkosten voor Azure (resourcetypen in de [Microsoft.Network](/azure/templates/microsoft.network/allversions)-naamruimte). De verhouding wordt gebruikt om de kosten van de geselecteerde bronnen te verdelen.
    - **Aangepast**: hiermee kan handmatig een geheel getalpercentage worden opgegeven. Het opgegeven totaal moet gelijk zijn aan 100%.
1. Wanneer de regel is geconfigureerd, selecteert u **Maken**.

De verwerking van de toewijzingsregel wordt gestart. Wanneer de regel actief is, worden alle kosten van de geselecteerde bron toegewezen aan de opgegeven doelen.

> [!NOTE] 
> De verwerking van nieuwe regels kan tot twee uur duren voordat deze is voltooid en actief is.

## <a name="verify-the-cost-allocation-rule"></a>Regel voor kostentoewijzing verifiëren

Als de kostentoewijzingsregel actief is, worden de kosten van de geselecteerde bronnen verdeeld over de opgegeven toewijzingsdoelen. Gebruik de volgende informatie om te controleren of de kosten correct zijn toegewezen aan de doelen.

### <a name="view-cost-allocation-for-a-subscription"></a>Kostentoewijzing voor een abonnement weergeven

U bekijkt de impact van de toewijzingsregel in de kostenanalyse. Ga in de Azure-portal naar [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Selecteer een abonnement in de lijst die is gericht op een actieve kostentoewijzingsregel. Selecteer vervolgens **Kostenanalyse** in het menu. Selecteer in kostenanalyse **Groeperen op** en selecteer vervolgens **Kostentoewijzing**. In de resulterende weergave ziet u een snelle kostenanalyse die door het abonnement is gegenereerd. De kosten die aan het abonnement zijn toegewezen, worden ook weergegeven, zoals in de volgende afbeelding.

:::image type="content" source="./media/allocate-costs/cost-breakdown.png" alt-text="Voorbeeld van kostenuitsplitsing" lightbox="./media/allocate-costs/cost-breakdown.png" :::

### <a name="view-cost-allocation-for-a-resource-group"></a>Kostentoewijzing voor een resourcegroep weergeven

Gebruik een soortgelijk proces voor de impact van een kostentoewijzingsregel voor een resourcegroep. Ga in Azure Portal naar [Resourcegroepen](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups). Selecteer een resourcegroep in de lijst die is gericht op een actieve kostentoewijzingsregel. Selecteer vervolgens **Kostenanalyse** in het menu. Selecteer in kostenanalyse **Groeperen op** en selecteer vervolgens **Kostentoewijzing**. In de weergave ziet u een snelle kostenverdeling die wordt gegenereerd door de resourcegroep. De kosten die aan de resourcegroep zijn toegewezen, worden ook weergegeven.

### <a name="view-cost-allocation-for-tags"></a>Kostentoewijzing voor tags weergeven

Navigeer in Azure Portal naar **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**. Selecteer in Kostenanalyse **Filter toevoegen**. Selecteer **Tag**, kies de tagsleutel en tagwaarden waaraan kosten zijn toegewezen.

:::image type="content" source="./media/allocate-costs/tagged-costs.png" alt-text="Voorbeeld van kosten voor items met tags" lightbox="./media/allocate-costs/tagged-costs.png" :::

Hier volgt een video waarin wordt getoond hoe u een kosten toewijzings regel maakt.

>[!VIDEO https://www.youtube.com/embed/nYzIIs2mx9Q]


## <a name="edit-an-existing-cost-allocation-rule"></a>Een bestaande regel voor kostentoewijzing bewerken

U kunt een kostentoewijzingsregel bewerken om de bron of het doel te wijzigen of als u het vooraf ingevulde percentage wilt bijwerken voor reken-, opslag- of netwerkopties. Bewerk de regels op dezelfde manier als u ze maakt. Het kan tot twee uur duren voordat de bestaande regels zijn gewijzigd.

## <a name="frequently-asked-questions-faq"></a>Veelgestelde vragen

De volgende secties bevatten algemene vragen over kostentoewijzing.

### <a name="what-are-the-current-limitations-with-cost-allocation-in-public-preview"></a>Wat zijn de huidige beperkingen met betrekking tot kostentoewijzing in openbare preview?
<a name="limitations"></a>

Op dit moment wordt kostentoewijzing ondersteund in Cost Management voor kostenanalyse, budgetten en prognoseweergaven. Toegewezen kosten worden ook weergegeven in de abonnementenlijst en op de overzichtspagina Abonnementen.

De volgende items worden momenteel niet ondersteund door de openbare preview van kostentoewijzing:

- Geplande [export](tutorial-export-acm-data.md)
- Gegevens die worden weergegeven door de API [Gebruiksdetails](/rest/api/consumption/usagedetails/list)
- Gebied voor factureringsabonnementen
- [Cost Management Power BI-app](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)
- [Power BI Desktop-connector](/power-bi/connect-data/desktop-connect-azure-cost-management)

### <a name="are-costs-factored-into-budgets-and-forecast-views"></a>Worden kosten in rekening gebracht voor budgetten en prognoseweergaven?
<a name="budgets-forecast"></a>

Ja. De toegewezen kosten worden in rekening gebracht en worden ondersteund door budgetten en prognoses. Budget- en prognoseweergaven tonen de kosten die hieraan zijn toegewezen, zoals geconfigureerd door kostentoewijzingsregels.

### <a name="if-a-cost-allocation-rule-is-deleted-what-happens"></a>Wat gebeurt er als een kostentoewijzingsregel wordt verwijderd?
<a name="delete-rule"></a>

Wanneer een kostentoewijzingsregel wordt verwijderd, worden alle openstaande en huidige aan de doelen toegewezen maandelijkse factureringskosten verwijderd. Als de kostentoewijzingsregel al meerdere maanden bestaat, blijven de historische voorgaande maanden van toewijzingsgegevens zoals oorspronkelijk ingesteld door de toewijzingsregel.

### <a name="why-is-an-enrollment-admin-or-a-billing-account-admin-needed-to-create-cost-allocation-rules"></a>Waarom is een inschrijvingsbeheerder of een factureringsaccountbeheerder nodig om regels voor kostentoewijzing te maken?
<a name="why-admin"></a>

Regels voor kostentoewijzing worden gemaakt op basis van het inschrijvingsbereik (Enterprise Agreement) of het factureringsaccount (Microsoft-klantovereenkomst). Voor machtigingen om wijzigingen aan te brengen binnen deze bereiken, zijn beheerdersrechten voor facturering vereist.

### <a name="why-are-sources-and-targets-limited-to-25-per-rule"></a>Waarom zijn bronnen en doelen beperkt tot 25 per regel?
<a name="source-target-rule-limit"></a>

De limiet is een beperking van de preview-versie om te zorgen voor optimale prestaties en schaalbaarheid van de kosten. De limieten zullen waarschijnlijk toenemen of worden verwijderd als kostentoewijzing wordt overgezet naar algemene beschikbaarheid (GA).

### <a name="what-can-happen-if-cost-allocation-rules-sourcestargets-overlap"></a>Wat kan er gebeuren als de kostentoewijzingsregels (bronnen/doelen) elkaar overlappen?
<a name="rule-overlap"></a>

Regels die overlappende bronnen of overlappende doelen hebben, worden niet aanbevolen. Regels voor kostentoewijzing worden toegepast op hun aanmaakdatum, dus als kostentoewijzingsregels elkaar overlappen, krijgt de toewijzingsregel met de eerste aanmaakdatum voorrang.

## <a name="next-steps"></a>Volgende stappen

- Toewijzingsregels maken of bijwerken met behulp van de [Rest API voor kostentoewijzing](/rest/api/cost-management/costallocationrules)
- Meer informatie over [hoe u uw cloudinvesteringen kunt optimaliseren met Azure Cost Management](cost-mgt-best-practices.md)