---
title: Aan de slag met uw bijgewerkte Azure-factureringsaccount
description: Ga aan de slag met uw bijgewerkte Azure-factureringsaccount om wijzigingen in de nieuwe facturerings- en kostenbeheerervaring te begrijpen
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: banders
ms.openlocfilehash: 4f7179a5ad35b4d3ca9a92119fb7b492e2aff779
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122511"
---
# <a name="get-started-with-your-updated-azure-billing-account"></a>Aan de slag met uw bijgewerkte Azure-factureringsaccount

Het beheren van kosten en facturen is een van de belangrijkste onderdelen van uw cloudervaring. Het helpt u uw uitgaven aan de cloud te beheren en begrijpen. Om het gemakkelijker voor u te maken uw kosten en facturen te beheren, werkt Microsoft uw Azure-factureringsaccount bij met verbeterde kostenbeheer- en factureringsmogelijkheden. In dit artikel worden de updates aan uw factureringsaccount beschreven en wordt u door de nieuwe mogelijkheden geleid.

> [!IMPORTANT]
> Uw account wordt bijgewerkt wanneer u een e-mail van Microsoft ontvangt waarin u op de hoogte wordt gesteld van de updates aan uw account. De e-mail wordt 60 dagen vóór het bijwerken van uw account verzonden.

## <a name="more-flexibility-with-your-new-billing-account"></a>Meer flexibiliteit met uw nieuwe factureringsaccount

In het volgende diagram worden uw oude en nieuwe factureringsaccount met elkaar vergeleken:

:::image type="content" source="./media/mosp-new-customer-experience/comparison-old-new-account.png" alt-text="Diagram van de vergelijking van de facturerings hiërarchie in het oude en het nieuwe account." border="false" lightbox="./media/mosp-new-customer-experience/comparison-old-new-account.png":::

Uw nieuwe factureringsaccount bevat een of meer factureringsprofielen waarmee u uw facturen en betalingsmethoden kunt beheren. Elk factureringsprofiel bevat een of meer factuursecties waarmee u kosten op de factuur van het factureringsprofiel kunt organiseren.

:::image type="content" source="./media/mosp-new-customer-experience/new-billing-account-hierarchy.png" alt-text="Diagram waarin de nieuwe facturerings hiërarchie wordt weer gegeven." border="false" lightbox="./media/mosp-new-customer-experience/new-billing-account-hierarchy.png":::

Rollen op het factureringsaccount hebben het hoogste machtigingsniveau. Deze rollen moeten worden toegewezen aan gebruikers die facturen moeten bekijken en de kosten voor uw hele account moeten bijhouden, zoals financiële medewerkers of IT-beheerders in een organisatie of het individu dat zich heeft geregistreerd voor een account. Zie [Rollen en taken van een factureringsaccount](../manage/understand-mca-roles.md#billing-account-roles-and-tasks) voor meer informatie. Wanneer uw account wordt bijgewerkt, krijgt de gebruiker die een beheerdersrol voor het oude factureringsaccount heeft, een eigenaarsrol voor het nieuwe account.

## <a name="billing-profiles"></a>Factureringsprofielen

Een factureringsprofiel wordt gebruikt om uw factuur en betalingsmethoden te beheren. Aan het begin van de maand wordt voor elk factureringsprofiel in uw account een maandelijkse factuur gegenereerd. De factuur bevat de respectievelijke kosten van de vorige maand voor alle abonnementen die aan het factureringsprofiel zijn gekoppeld.

Wanneer uw account wordt bijgewerkt, wordt er automatisch een factureringsprofiel gemaakt voor elk abonnement. De kosten voor een abonnement worden aan het respectievelijke factureringsprofiel gefactureerd en op de bijbehorende factuur weergegeven.

Rollen in de factureringsprofielen hebben machtigingen om facturen en betalingswijzen weer te geven en te beheren. Deze rollen moeten worden toegewezen aan gebruikers die facturen betalen, zoals leden van het boekhoudteam in een organisatie. Zie [Rollen en taken van een factureringsprofiel](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks) voor meer informatie.

Wanneer uw account wordt bijgewerkt, geldt dat voor elk abonnement waarvoor u anderen toestemming hebt gegeven om [facturen te bekijken](download-azure-invoice.md#allow-others-to-download-your-subscription-invoice), gebruikers met de Azure-rol Eigenaar, Bijdrager, Lezer of Factureringslezer de rol Lezer krijgen voor het respectievelijke factureringsprofiel.

## <a name="invoice-sections"></a>Factuursecties

Een factuursectie wordt gebruikt om de kosten op uw factuur te ordenen. Het is bijvoorbeeld mogelijk dat u één factuur nodig hebt, maar dat u de kosten wilt indelen per afdeling, team of project. Voor dit scenario hebt u één factureringsprofiel waarin u een factuursectie maakt voor elke afdeling, elk team of elk project.

Wanneer uw account wordt bijgewerkt, wordt er voor elk factureringsprofiel een factuursectie gemaakt en wordt het gerelateerde abonnement aan de factuursectie toegewezen. Wanneer u meer abonnementen toevoegt, kunt u extra secties maken en de abonnementen aan de factuursecties toewijzen. U ziet de secties op de factuur van het factureringsprofiel die het gebruik weergeven van elk abonnement dat u eraan hebt toegewezen.

Rollen in de factuursecties hebben machtigingen om te bepalen wie Azure-abonnementen maakt. De rollen moeten worden toegewezen aan gebruikers die de Azure-omgeving instellen voor teams in een organisatie, zoals engineering leads en technische architecten. Zie [Rollen en taken van een factuursectie](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks) voor meer informatie.

## <a name="enhanced-features-in-your-new-experience"></a>Verbeterde functies in uw nieuwe ervaring

Uw nieuwe ervaring omvat de volgende kostenbeheer- en factureringsmogelijkheden die het gemakkelijk voor u maken uw kosten en facturen te beheren:

#### <a name="invoice-management"></a>Factuurbeheer

**Beter voorspelbare maandelijkse factureringsperiode** - In uw nieuwe account loopt de factureringsperiode vanaf de eerste dag van de maand tot de laatste dag van de maand, ongeacht wanneer u zich registreert om Azure te gebruiken. Aan het begin van elke maand wordt een factuur gegenereerd, die alle kosten van de vorige maand bevat.

**Eén maandelijkse factuur voor meerdere abonnementen ophalen** : in uw bestaande account krijgt u een factuur voor elk Azure-abonnement. Wanneer uw account wordt bijgewerkt, wordt het bestaande gedrag gehandhaafd, maar hebt u de flexibiliteit om de kosten van uw abonnementen op één factuur te consolideren. Nadat u het account hebt bijgewerkt, volgt u de onderstaande stappen om uw kosten op één factuur te consolideren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Zoek naar **Kostenbeheer en facturering**.  
   ![Schermopname van de zoekopdracht naar kostenbeheer en facturering in Azure Portal.](./media/mosp-new-customer-experience/billing-search-cost-management-billing.png)
3. Selecteer **Azure-abonnementen** aan de linkerkant van het scherm. 
4. De tabel geeft een lijst van de Azure-abonnementen waarvoor u betaalt. In de kolom facturerings profiel vindt u het facturerings profiel dat voor het abonnement wordt gefactureerd. De abonnements kosten worden weer gegeven op de factuur voor het facturerings profiel. Als u de kosten voor al uw abonnementen op één factuur wilt consolideren, moet u al uw abonnementen aan één facturerings profiel koppelen.  
    :::image type="content" source="./media/mosp-new-customer-experience/list-azure-subscriptions.png" alt-text="Scherm opname van de lijst met Azure-abonnementen." lightbox="./media/mosp-new-customer-experience/list-azure-subscriptions.png" :::
5. Kies een facturerings profiel dat u wilt gebruiken. 
6. Selecteer een abonnement dat niet is gekoppeld aan het facturerings profiel dat u in stap 5 hebt gekozen. Klik op het weglatings teken (drie punten) voor het abonnement. Selecteer **Factuursectie wijzigen**.  
    :::image type="content" source="./media/mosp-new-customer-experience/select-change-invoice-section.png" alt-text="Scherm afbeelding waarin de optie voor het wijzigen van de factuur wordt weer gegeven." lightbox="./media/mosp-new-customer-experience/select-change-invoice-section-zoomed-in.png" :::
7. Selecteer het facturerings profiel dat u in stap #5 hebt gekozen.  
    :::image type="content" source="./media/mosp-new-customer-experience/change-invoice-section.png" alt-text="Scherm afbeelding die laat zien hoe u de sectie factuur wijzigt." lightbox="./media/mosp-new-customer-experience/change-invoice-section-zoomed-in.png" :::
8. Selecteer **wijzigen**.
9. Herhaal stap 6-8 voor alle andere abonnementen. 

**Ontvang één maandelijkse factuur voor Azure-abonnementen, ondersteuningsplannen en Azure Marketplace-producten** - U ontvangt één maandelijkse factuur voor alle kosten, waaronder gebruikskosten voor Azure-abonnementen en kosten voor ondersteuningsplannen en Azure Marketplace-aankopen.

**Groepeer de kosten op de factuur afhankelijk van uw behoeften** - U kunt de kosten op uw factuur groeperen afhankelijk van uw behoeften, bijvoorbeeld per afdeling, project of team.

**Stel een optioneel inkoopordernummer op de factuur in** - Om uw factuur te koppelen aan uw interne financiële systemen, kunt u een inkoopordernummer instellen. U kunt dit op elk gewenst moment beheren en bijwerken in de Azure-portal.

#### <a name="payment-management"></a>Betalingsbeheer

**Betaal facturen onmiddellijk met een creditcard** - U hoeft niet te wachten totdat de automatische betaling wordt afgeschreven van uw creditcard. U kunt in de Azure-portal elke creditcard gebruiken om te betalen voor een actuele of achterstallige factuur.

**Houd alle betalingen bij die worden toegepast op het account** - Bekijk in de Azure-portal alle betalingen die worden toegepast op uw account, waaronder de gebruikte betaalmethode, het betaalde bedrag en de betaaldatum.

#### <a name="cost-management"></a>Kostenbeheer

**Plan maandelijkse exports van gebruiksgegevens naar een opslagaccount** - Publiceer uw kosten en gebruiksgegevens automatisch in een opslagaccount, op dagelijkse, wekelijkse of maandelijkse basis.

#### <a name="account-and-subscription-management"></a>Account- en abonnementsbeheer

**Wijs meerdere beheerders toe om factureringsbewerkingen uit te voeren** - Wijs factureringsmachtigingen aan meerdere gebruikers toe om de facturering voor uw account te beheren. U krijgt flexibiliteit door anderen machtigingen voor lezen, schrijven of beide te geven.

**Maak meer abonnementen rechtstreeks in Azure Portal** - Maak al uw abonnementen via één selectie in Azure Portal.

#### <a name="api-support"></a>API-ondersteuning

**Voer facturerings- en kostenbeheerbewerkingen uit via API's, SDK en PowerShell** - Gebruik API's voor kostenbeheer, facturering en verbruik om factuur- en kostengegevens in uw favoriete hulpprogramma's voor gegevensanalyse te laden.

**Voer alle abonnementsbewerkingen uit via API's, SDK en PowerShell** - Gebruik API's voor Azure-abonnementen om het beheer van uw Azure-abonnementen te automatiseren, waaronder het maken, hernoemen en annuleren van een abonnement.

## <a name="get-prepared-for-your-new-experience"></a>Bereid u voor op uw nieuwe ervaring

We raden het volgende aan om u voor te bereiden op uw nieuwe ervaring:

**Maandelijkse factureringsperiode en andere factuurdatum**

In de nieuwe ervaring wordt uw factuur rond de negende dag van elke maand gegenereerd en bevat deze alle kosten van de vorige maand. Deze datum is mogelijk anders dan de datum waarop uw factuur in het oude account wordt gegenereerd. Als u uw facturen met anderen deelt, stel hen dan op de hoogte van de datumwijziging.


**Facturen in de eerste maand na de migratie**

De dag waarop uw account is bijgewerkt, uw bestaande niet-gefactureerde kosten worden voltooid en u ontvangt de facturen voor deze kosten op de dag waarop u uw facturen doorgaans ontvangt. John heeft bijvoorbeeld twee Azure-abonnementen: Azure-sub 01 met facturerings cyclus van de vijfde dag van de maand tot de vierde dag van de volgende maand en Azure-sub 02 met facturerings cyclus van de tiende dag van een maand tot de negende dag van de volgende maand. John haalt facturen voor beide Azure-abonnementen doorgaans op de vijfde van de maand. Als het account van John wordt bijgewerkt op 4e april, worden de kosten voor Azure-sub 01 van 5 maart tot en met 4 april en de kosten voor Azure-sub 02 van 10 maart tot en met 4 april voltooid. John ontvangt twee facturen, één voor elke sub april. Nadat het account is bijgewerkt, wordt de facturerings cyclus van John gebaseerd op de kalender maand en worden alle kosten in rekening gebracht vanaf het begin van een kalender maand tot het einde van deze kalender maand.  De factuur voor de kosten van de vorige kalender maand is beschikbaar op het negende van elke maand. In het bovenstaande voor beeld ontvangt John een andere factuur op 5 mei voor de facturerings periode van 5 april tot en met 30 april. 


**Nieuwe API’s voor facturering en kostenbeheer**

Als u API's voor kostenbeheer of facturering gebruikt voor het uitvoeren van query’s op en het bijwerken van uw facturerings- of kostengegevens, moet u nieuwe API's gebruiken. In de onderstaande tabel staan de API’s die niet zullen werken met het nieuwe factureringsaccount, en de wijzigingen die u moet aanbrengen in uw nieuwe factureringsaccount.

|API | Wijzigingen  |
|---------|---------|
|[Factureringsaccounts - Weergeven](/rest/api/billing/2019-10-01-preview/billingaccounts/list) | In de API ‘Factureringsaccounts - Weergeven’ heeft uw oude factureringsaccount **MicrosoftOnlineServiceProgram** als agreementType. Uw nieuwe factureringsaccount heeft **MicrosoftCustomerAgreement** als agreementType. Als u een afhankelijkheid van agreementType opneemt, werk deze dan bij. |
|[Facturen - Weergeven per factureringsabonnement](/rest/api/billing/2019-10-01-preview/invoices/listbybillingsubscription)     | Deze API retourneert alleen facturen die waren gegenereerd voordat uw account werd bijgewerkt. U moet de API [Facturen - Weergeven per factureringsaccount](/rest/api/billing/2019-10-01-preview/invoices/listbybillingaccount) gebruiken om facturen op te halen die in uw nieuwe factureringsaccount worden gegenereerd. |


## <a name="cost-management-updates-after-account-update"></a>Cost Management wordt bijgewerkt nadat het account is bijgewerkt

Met uw bijgewerkte Azure-factureringsrekening voor uw Microsoft-klantovereenkomst krijgt u toegang tot nieuwe en uitgebreide Cost Management-functies in Azure Portal die u niet hebt met uw betalen per gebruik-account.

### <a name="new-capabilities"></a>Nieuwe functionaliteit

Met uw Azure-factureringsrekening beschikt u over de volgende nieuwe functionaliteit.

#### <a name="new-billing-scopes"></a>Nieuwe factureringsbereiken

Als onderdeel van uw bijgewerkte account hebt u nieuwe bereiken in Cost Management + Billing. Ze zijn niet alleen handig voor de hiërarchische organisatie en de facturering, maar ook een manier om gecombineerde kosten van meerdere onderliggende abonnementen weer te geven. Zie [Bereiken van de Microsoft-klantovereenkomst](../costs/understand-work-scopes.md#microsoft-customer-agreement-scopes) voor meer informatie over factureringsbereiken.

U kunt ook toegang krijgen tot Cost Management-API's om weergaven van gecombineerde kosten te verkrijgen bij hogere bereiken. Alle Cost Management-API's die gebruikmaken van het abonnementsbereik zijn nog steeds beschikbaar met enkele kleine wijzigingen in het schema. Zie [Azure Cost Management-API's](/rest/api/cost-management/) en [Azure-verbruiks-API's](/rest/api/consumption/) voor meer informatie over de API's.

#### <a name="cost-allocation"></a>Kostentoewijzing

Met uw bijgewerkte account kunt u functionaliteit voor kostentoewijzing gebruiken om kosten van gedeelde services in uw organisatie te verdelen. Zie [Regels voor Azure-kostentoewijzing maken en beheren](../costs/allocate-costs.md) voor meer informatie over het toewijzen van kosten.

#### <a name="power-bi"></a>Power BI

De Azure Cost Management-connector voor Power BI Desktop helpt u bij het bouwen van aangepaste visualisaties en rapporten van uw gebruik en uitgaven in Azure. U hebt toegang tot uw kosten en gebruiksgegevens nadat u verbinding hebt gemaakt met uw bijgewerkte account. Zie [Besturingselementen en rapporten maken met de Azure Cost Management-connector in Power BI Desktop](/power-bi/connect-data/desktop-connect-azure-cost-management) voor meer informatie over de Azure Cost Management-connector voor Power BI Desktop.

### <a name="updated-capabilities"></a>Bijgewerkte functionaliteit

Met uw Azure-factureringsrekening beschikt u over de volgende bijgewerkt functionaliteit.

#### <a name="cost-analysis"></a>Kostenanalyse

U kunt u maandelijkse verbruikskosten blijven weergeven en bijhouden en u kunt nu de reservering en aanschafkosten voor Marketplace bekijken in Kostenanalyse.

Met uw bijgewerkte account ontvangt u één factuur voor alle Azure-kosten. U hebt nu ook een vereenvoudigde, maandelijkse kalenderweergave die de weergave van de factureringsperioden vervangt.

Als voor uw oude account uw factureringsperiode bijvoorbeeld van 24 november tot en met 23 december was, loopt de periode na de upgrade van 1 november tot en met 30 november, van 1 december tot en met 31 december, enzovoort.

:::image type="content" source="./media/mosp-new-customer-experience/billing-periods.png" alt-text="Scherm opname met een vergelijking tussen oude en nieuwe facturerings perioden." lightbox="./media/mosp-new-customer-experience/billing-periods.png" :::

#### <a name="budgets"></a>Budgetten

U kunt nu budgetten voor de factureringsrekening maken, zodat u de kosten voor de verschillende abonnementen kunt bijhouden. U kunt met behulp van budgetten ook op de hoogte blijven van de aanschafkosten. Zie [Azure-budgetten maken en beheren](../costs/tutorial-acm-create-budgets.md) voor meer informatie over budgetten.

#### <a name="exports"></a>Exports

Uw nieuwe factureringsrekening biedt een verbeterde exportfunctionaliteit. U kunt bijvoorbeeld een export maken voor de werkelijke kosten, waarin aankopen of afgeschreven kosten zijn inbegrepen (aanschafkosten voor reserveringen worden verdeeld over de aanschafperiode). U kunt ook een export voor de factureringsrekening maken om verbruiks- en kostengegevens te verkrijgen voor alle abonnementen in de factureringsrekening. Zie [Geëxporteerde gegevens maken en beheren](../costs/tutorial-export-acm-data.md) voor meer informatie over exports.

> [!NOTE]
> Met exports die zijn gemaakt vóór uw account werd bijgewerkt met het type **Maandelijkse export van de kosten voor de afgelopen maand**, worden gegevens van de laatste kalendermaand geëxporteerd, niet van de laatste factureringsperiode.

Bijvoorbeeld: voor een factureringsperiode van 23 december tot en met 22 januari bevat het geëxporteerde CSV-bestand kosten- en verbruiksgegevens voor die periode. Na de update bevat de export gegevens voor de kalendermaand. Bijvoorbeeld 1 januari tot en met 31 januari, enzovoort.

:::image type="content" source="./media/mosp-new-customer-experience/export-amortized-costs.png" alt-text="Scherm afbeeldingen met een vergelijking tussen oude en nieuwe export Details." lightbox="./media/mosp-new-customer-experience/export-amortized-costs.png" :::

## <a name="additional-information"></a>Aanvullende informatie

In de volgende secties vindt u meer informatie over uw nieuwe ervaring.

**Geen servicedowntime** Azure-services in uw abonnement worden zonder onderbreking uitgevoerd. De enige update is aan uw factureringservaring. Dit heeft geen invloed op bestaande resources, resourcegroepen of beheergroepen.

**Geen wijzigingen in Azure-resources** De toegang tot Azure-resources die zijn ingesteld met behulp van op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC), wordt niet beïnvloed door de update.

**Eerdere facturen zijn beschikbaar in de nieuwe ervaring** Facturen die waren gegenereerd voordat uw account werd bijgewerkt, zijn nog steeds beschikbaar in de Azure-portal.

**Facturen voor een account dat halverwege de maand wordt bijgewerkt** Als uw account halverwege de maand wordt bijgewerkt, ontvangt u één factuur voor de kosten die zijn geaccumuleerd tot aan de dag waarop uw account wordt bijgewerkt. U ontvangt nog een factuur voor de rest van de maand. Bijvoorbeeld: uw account heeft één abonnement en wordt bijgewerkt op 15 september. U ontvangt één factuur voor de kosten die zijn opgelopen tot en met 15 september. U ontvangt nog een factuur voor de periode 15 september t/m 30 september. Na september ontvangt u één factuur per maand.

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Neem contact op met ondersteuning.

Als u hulp nodig hebt, neemt u [contact op met ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over uw factureringsaccount.

- [Beheerdersrollen voor uw nieuwe factureringsaccount begrijpen](../manage/understand-mca-roles.md)
- [Een extra Azure-abonnement maken voor uw nieuwe factureringsaccount](../manage/create-subscription.md)
- [Secties maken op uw factuur voor het ordenen van uw kosten](../manage/mca-section-invoice.md)