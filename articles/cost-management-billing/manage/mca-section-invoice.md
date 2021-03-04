---
title: Uw factuur organiseren op basis van uw behoeften - Azure
description: Ontdek hoe u de kosten op uw factuur kunt organiseren. U kunt uw factureringsaccount aanpassen door factureringsprofielen en factuursecties te maken.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: c3ee643d525cb5394369aba3b0e803bd1a94c852
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036231"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>Kosten organiseren door uw factureringsrekening aan te passen

Uw factureringsrekening voor uw Microsoft-klantovereenkomst biedt u de flexibiliteit om uw kosten te organiseren op basis van uw behoeften per afdeling, project of ontwikkelomgeving.

In dit artikel wordt beschreven hoe u kosten kunt organiseren met behulp van de Azure-portal. Dit artikel is van toepassing op een factureringsrekening voor een Microsoft-klantovereenkomst. [Controleer of u toegang hebt tot een Microsoft-klantovereenkomst](#check-access-to-a-microsoft-customer-agreement).

Bekijk de [organisatie kosten door uw micro soft-account voor de facturering van klanten](https://www.youtube.com/watch?v=7RxTfShGHwU) aan te passen om te leren hoe u de kosten voor uw facturerings account kunt indelen.

>[!VIDEO https://www.youtube.com/embed/7RxTfShGHwU]

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>Uw account structureren met factureringsprofielen en factuursecties

In de factureringsrekening voor een Microsoft-klantovereenkomst gebruikt u factureringsprofielen en factuursecties om uw kosten te ordenen.

![Schermopname van de factureringshiërarchie voor een Microsoft-klantovereenkomst](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>Factureringsprofiel

Een factureringsprofiel vertegenwoordigt een factuur en de gerelateerde factureringsgegevens, zoals betalingswijzen en factureringsadres. Aan het begin van de maand wordt voor elk factureringsprofiel in uw account een maandelijkse factuur gegenereerd. De factuur bevat de kosten voor het gebruik van Azure en andere aankopen van de vorige maand.

Wanneer u zich aanmeldt voor Azure, wordt bij uw factureringsrekening ook automatisch een factureringsprofiel gemaakt. U kunt extra factureringsprofielen maken om uw kosten in meerdere maandelijkse facturen te organiseren.

> [!IMPORTANT]
>
> Het maken van extra factureringsprofielen kan van invloed zijn op uw totale kosten. Zie [Dingen waarmee u rekening moet houden bij het toevoegen van nieuwe factureringsprofielen](#things-to-consider-when-adding-new-billing-profiles) voor meer informatie.

### <a name="invoice-section"></a>Factuursectie

Een factuursectie verwijst naar een kostengroepering op een factuur. Er wordt automatisch een factuursectie gemaakt voor elk factureringsprofiel in uw account. U kunt extra secties maken om uw kosten te organiseren op basis van uw behoeften. Elke factuursectie wordt op de factuur weergegeven met de gemaakte kosten voor die maand.

In de onderstaande afbeelding ziet u een factuur met twee factuursecties: Engineering en Marketing. De samengevatte en gedetailleerde kosten voor elke sectie worden in de factuur weergegeven. De prijzen die in de afbeelding worden weergegeven, zijn alleen bedoeld als voorbeeld en vertegenwoordigen niet de werkelijke prijzen van Azure-services.

![Afbeelding van een factuur met secties](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>Structuur van factureringsrekening voor algemene scenario's

In deze sectie worden algemene scenario's voor het organiseren van kosten en de bijbehorende factureringsrekeningstructuren beschreven:

|Scenario  |Structuur  |
|---------|---------|
|Jack meldt zich aan voor Azure en heeft één factuur per maand nodig. | Een factureringsprofiel en een factuursectie. Deze structuur wordt automatisch ingesteld voor Jack wanneer hij zich aanmeldt voor Azure. Hiervoor zijn geen extra stappen vereist. |

![Info-afbeelding van een enkelvoudig factureringsscenario.](./media/mca-section-invoice/organize-billing-scenario1.png)

|Scenario  |Structuur  |
|---------|---------|
|Contoso is een kleine organisatie die één factuur per maand nodig heeft, maar kosten groepeert op hun afdelingen: Marketing en Engineering.  | Een factureringsprofiel voor Contoso met een factuursectie voor de afdeling Marketing en een factuursectie voor de afdeling Engineering. |

![Info-afbeelding van een meervoudig factureringsscenario.](./media/mca-section-invoice/organize-billing-scenario2.png)

|Scenario  |Structuur  |
|---------|---------|
|Fabrikam is een middelgrote organisatie die afzonderlijke facturen nodig heeft voor de afdelingen Engineering en Marketing. Voor de afdeling Engineering willen ze kosten groeperen op hun omgevingen: Productie en Ontwikkeling.  | Een factureringsprofiel voor de afdeling Marketing en een factureringsprofiel voor de afdeling Engineering. Voor de afdeling Engineering: een factuursectie voor de productieomgeving en een factuursectie voor de ontwikkelomgeving. |

![Info-afbeelding van een scenario met meervoudige facturering, met afzonderlijke facturering voor productie- en ontwikkelingsomgevingen.](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>Een nieuwe factuursectie maken

Als u een factuursectie wilt maken, moet u een **factureringsprofiel-eigenaar** of een **factureringsprofiel-inzender** zijn. Zie [Factuursecties beheren voor het factureringsprofiel](understand-mca-roles.md#manage-invoice-sections-for-billing-profile) voor meer informatie.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Zoek naar **Kostenbeheer en facturering**.

   ![Schermopname van de zoekopdracht naar kostenbeheer en facturering in de Azure-portal](./media/mca-section-invoice/search-cmb.png)

3. Selecteer in het linkerdeelvenster de optie **Factureringsprofielen**. Selecteer een factureringsprofiel in de lijst. De nieuwe sectie wordt weergegeven op de factuur van het geselecteerde factureringsprofiel.

   [![Schermopname van de lijst met factureringsprofielen.](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. Selecteer **Factuursecties** in het linkerdeelvenster en selecteer vervolgens **Toevoegen** boven aan de pagina.

   [![Schermopname van het toevoegen van factuursecties](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. Voer een naam voor de factuursectie in.

   [![Schermopname van de pagina voor het maken van een factuursectie](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. Selecteer **Maken**.

## <a name="create-a-new-billing-profile"></a>Een nieuw factureringsprofiel maken

Als u een factureringsprofiel wilt maken, moet u een **factureringsrekening-eigenaar** of een **factureringsrekening-inzender** zijn. Zie [Factureringsprofielen voor de factureringsrekening beheren](understand-mca-roles.md#manage-billing-profiles-for-billing-account) voor meer informatie.

> [!IMPORTANT]
>
> Het maken van extra factureringsprofielen kan van invloed zijn op uw totale kosten. Zie [Dingen waarmee u rekening moet houden bij het toevoegen van nieuwe factureringsprofielen](#things-to-consider-when-adding-new-billing-profiles) voor meer informatie.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Zoek naar **Kostenbeheer en facturering**.

   ![Schermopname van de zoekopdracht naar kostenbeheer en facturering in de Azure-portal](./media/mca-section-invoice/search-cmb.png)

3. Selecteer **Factureringsprofielen** in het linkerdeelvenster en selecteer vervolgens **Toevoegen** boven aan de pagina.

   [![Schermopname van de lijst met factureringsprofielen met Toevoegen geselecteerd.](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > Als u de knop Toevoegen niet ziet op de pagina voor het factureringsprofiel, is de functie niet beschikbaar voor uw account. Deze knop is momenteel alleen beschikbaar voor accounts die zijn ingesteld in samenwerking met een Microsoft-vertegenwoordiger.

4. Vul het formulier in en klik op **Maken**.

   [![Schermopname van de pagina voor het maken van een factureringsprofiel](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |Veld  |Definitie  |
    |---------|---------|
    |Naam     | Een weergavenaam die u helpt om het factureringsprofiel in de Azure-portal gemakkelijk te kunnen identificeren.  |
    |Inkoopordernummer    | Een optioneel inkoopordernummer. Het inkoopordernummer wordt weergegeven op de facturen die voor het factureringsprofiel worden gegenereerd. |
    |Factuuradres   | Het factuuradres wordt weergegeven op de facturen die voor het factureringsprofiel worden gegenereerd. |
    |E-mailfactuur   | Schakel het selectievakje E-mailfactuur in om de facturen voor dit factureringsprofiel per e-mail te ontvangen. Als u zich niet aanmeldt, kunt u de facturen in de Azure-portal bekijken en downloaden.|

5. Selecteer **Maken**.

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>Kosten koppelen aan factuursecties en factureringsprofielen

Zodra u uw factureringsrekening hebt aangepast op basis van uw behoeften, kunt u abonnementen en andere producten koppelen aan uw gewenste factuursectie en factureringsprofiel.

### <a name="link-a-new-subscription"></a>Een nieuw abonnement koppelen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Zoek naar **Abonnementen**.

   [![Schermopname van zoeken naar abonnement in Azure Portal.](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. Selecteer **Toevoegen** bovenaan de pagina.

   ![Schermopname van de knop Toevoegen in de weergave Abonnementen voor een nieuw abonnement.](./media/mca-section-invoice/subscription-add.png)

4. Als u toegang hebt tot meerdere factureringsrekeningen, selecteert u de factureringsrekening voor uw Microsoft-klantovereenkomst.

   ![Schermopname van de pagina Abonnement maken.](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. Selecteer het factureringsprofiel waarmee het gebruik van het abonnement wordt gefactureerd. De kosten voor Azure-gebruik en andere aankopen voor dit abonnement worden in rekening gebracht op de factuur van het geselecteerde factureringsprofiel.

6. Selecteer de factuursectie waaraan u de kosten van het abonnement wilt koppelen. De kosten worden weergegeven onder deze sectie op de factuur van het factureringsprofiel.

7. Selecteer een Azure-plan en voer een beschrijvende naam in voor uw abonnement.

9. Klik op **Create**.  

### <a name="link-existing-subscriptions-and-products"></a>Bestaande abonnementen en producten koppelen

Als u beschikt over bestaande Azure-abonnementen of andere producten, zoals Azure Marketplace en App-bronresources, kunt u ze verplaatsen van de bestaande factuursectie naar een andere factuursectie om uw kosten opnieuw te organiseren.

> [!IMPORTANT]
>
> Abonnementen en andere producten kunnen alleen worden verplaatst tussen factuursecties die behoren tot hetzelfde factureringsprofiel. Het verplaatsen van abonnementen en producten naar factuursecties van andere factureringsprofielen wordt niet ondersteund.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Zoek naar **Kostenbeheer en facturering**.

   ![Schermopname van de zoekopdracht naar kostenbeheer en facturering in Azure Portal.](./media/mca-section-invoice/search-cmb.png)

3. Als u een abonnement wilt koppelen aan een nieuwe factuursectie, selecteert u **Azure-abonnementen** aan de linkerkant van het scherm. Voor andere producten, zoals Azure Marketplace en App-bronresources, selecteert u **Terugkerende kosten**.

   [![Schermopname met de optie voor het wijzigen van een factuursectie](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. Klik op de pagina op het weglatingsteken (drie punten) voor het abonnement of product dat u aan een nieuwe factuursectie wilt koppelen. Selecteer **Factuursectie wijzigen**.

5. Selecteer de nieuwe factuursectie in de vervolgkeuzelijst. In de vervolgkeuzelijst worden alleen factuursecties weergegeven die zijn gekoppeld aan hetzelfde factureringsprofiel als de bestaande factuursectie.

    [![Schermopname van de selectie van een nieuwe factuursectie](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. Selecteer **Opslaan**.

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>Dingen waarmee u rekening moet houden bij het toevoegen van nieuwe factureringsprofielen

### <a name="azure-usage-charges-may-be-impacted"></a>De kosten voor het gebruik van Azure kunnen veranderen

In de factureringsrekening voor een Microsoft-klantovereenkomst wordt elke maand het Azure-gebruik voor elk factureringsprofiel geaggregeerd. De prijzen voor Azure-resources met verschillende prijsniveaus worden afzonderlijk bepaald op basis van het gebruik voor elk factureringsprofiel. Het gebruik wordt niet geaggregeerd over verschillende factureringsprofielen bij het berekenen van de prijs. Dit kan invloed hebben op de totale Azure-gebruikskosten voor accounts met meerdere factureringsprofielen.

Hier volgt een voorbeeld van twee scenario's met verschillende kosten. De prijzen die in de scenario's worden gebruikt, zijn alleen bedoeld als voorbeeld en vertegenwoordigen niet de werkelijke prijzen van Azure-services.

#### <a name="you-only-have-one-billing-profile"></a>U hebt slechts één factureringsprofiel.

We gaan ervan uit dat u Azure-blok-blobopslag gebruikt. De kosten zijn USD 0,00184 per GB voor de eerste 50 terabyte (TB) en vervolgens 0,00177 per GB voor de volgende 450 terabyte (TB). U hebt 100 TB gebruikt in de abonnementen die voor uw factureringsprofiel worden gefactureerd. Hier ziet u hoeveel u in rekening wordt gebracht.

|  Prijscategorie (USD) |Aantal | Bedrag (USD)|
|---------|---------|---------|
|1,84 per TB voor de eerste 50 TB/maand    | 50 TB        | 92,0   |
|1,77 per TB voor de volgende 450 TB/maand    |  50 TB         | 88,5   |
|Totaal     |     100 TB  | 180,5

De totale kosten voor het gebruik van 100 TB aan gegevens in dit scenario bedragen **180,5**

#### <a name="you-have-multiple-billing-profiles"></a>U hebt meerdere factureringsprofielen.

We gaan er nu van uit dat u een ander factureringsprofiel hebt gemaakt en 50 GB hebt gebruikt via abonnementen die worden gefactureerd voor het eerste factureringsprofiel en 50 GB via abonnementen die worden gefactureerd voor het tweede factureringsprofiel. Hier ziet u hoeveel u in rekening wordt gebracht.

`Charges for the first billing profile`

|  Prijscategorie (USD) |Aantal | Bedrag (USD)|
|---------|---------|---------|
|1,84 per TB voor de eerste 50 TB/maand    | 50 TB        | 92,0  |
|1,77 per TB voor de volgende 450 TB/maand    |  0 TB         | 0,0  |
|Totaal     |     50 TB  | 92,0

`Charges for the second billing profile`

|  Prijscategorie (USD) |Aantal | Bedrag (USD)|
|---------|---------|---------|
|1,84 per TB voor de eerste 50 TB/maand    | 50 TB        | 92,0  |
|1,77 per TB voor de volgende 450 TB/maand    |  0 TB         | 0,0  |
|Totaal     |     50 TB  | 92,0

De totale kosten voor het gebruik van 100 TB aan gegevens in dit scenario bedragen **184,0** (92,0 x 2).

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>Voordelen van Azure-reserveringen zijn mogelijk niet van toepassing op alle abonnementen

Azure-reserveringen met een gedeeld bereik worden toegepast op abonnementen in één factureringsprofiel en worden niet gedeeld tussen factureringsprofielen.

![Info-afbeelding van reserveringstoepassing voor een structuur met een andere factureringsrekening](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

In de bovenstaande afbeelding heeft Contoso twee abonnementen. Het voordeel van Azure-reservering wordt anders toegepast, afhankelijk van de manier waarop de factureringsrekening is gestructureerd. In het scenario links wordt het reserveringsvoordeel toegepast op beide abonnementen die worden gefactureerd voor het engineering-factureringsprofiel. In het scenario rechts wordt het reserveringsvoordeel alleen toegepast op het eerste abonnement, omdat alleen dat abonnement wordt gefactureerd voor het engineering-factureringsprofiel.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Toegang tot een Microsoft-klantovereenkomst controleren
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Contact opnemen met ondersteuning

Als u hulp nodig hebt, kunt u [contact opnemen met ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.

## <a name="next-steps"></a>Volgende stappen

- [Een extra Azure-abonnement voor Microsoft-klantovereenkomst maken](create-subscription.md)
- [Factureringsrollen beheren in de Azure-portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Factureringseigenaar worden van Azure-abonnementen van andere gebruikers in andere factureringsaccounts](mca-request-billing-ownership.md)
