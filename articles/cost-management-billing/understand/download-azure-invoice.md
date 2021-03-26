---
title: Uw Azure-factuur weergeven en downloaden
description: Meer informatie over het weer geven en downloaden van uw Azure-factuur. U kunt uw factuur downloaden in de Azure Portal of deze in een e-mail bericht laten verzenden.
keywords: factuur,factuur downloaden,azure-factuur,azure-gebruik
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 37ce1a292b6ff2efe0abecdb2ab934f096689f87
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "105560661"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Uw Microsoft Azure-factuur weergeven en downloaden

U kunt uw factuur downloaden via de [Azure-portal](https://portal.azure.com/) of naar uw e-mailadres laten verzenden. Als u een Azure-klant bent met een Enterprise Agreement (EA-klant), kunt u de facturen van uw organisatie niet downloaden. Facturen worden verzonden naar degene die is ingesteld voor ontvangst van facturen voor de inschrijving.

## <a name="when-invoices-are-generated"></a>Wanneer facturen worden gegenereerd

Er wordt een factuur gegenereerd op basis van uw type factureringsrekening. Er worden facturen gemaakt voor factureringsrekeningen van Microsoft Online-serviceprogramma (MOSP), Microsoft-klantovereenkomst (MCA) en Microsoft Partner-overeenkomst (MPA). Er worden ook facturen gegenereerd voor factureringsrekeningen van Enterprise Agreement (EA). Facturen voor EA-factureringsrekeningen worden echter niet weergegeven in de Azure-portal.

Zie [Uw factureringsrekeningen weergeven in de Azure-portal](../manage/view-all-accounts.md) voor meer informatie over factureringsrekeningen en het bepalen van uw type factureringsrekening.

### <a name="invoice-status"></a>Factuur status

Wanneer u de status van uw factuur in het Azure Portal controleert, heeft elke factuur een van de volgende status symbolen.

|  Status symbool | Description  |
|---|---|
| ![Symbool voor eind status](./media/download-azure-invoice/due.svg) | De *verval datum* wordt weer gegeven wanneer er een factuur wordt gegenereerd, maar deze nog niet is betaald. |
| ![Symbool voor de verval status](./media/download-azure-invoice/past-due.svg)  | *Achterstallig* wordt weer gegeven wanneer Azure heeft geprobeerd om uw betalings methode af te schrijven, maar de betaling is afgewezen. |
| ![Symbool voor betaalde status](./media/download-azure-invoice/paid.svg)  | De *betaalde* status wordt weer gegeven wanneer Azure uw betalings wijze heeft gefactureerd. |

Wanneer een factuur wordt gemaakt, wordt deze weer gegeven in de Azure Portal met de status *verval* . De status verval is normaal en verwacht.  

Wanneer er geen factuur is betaald, wordt de status weer gegeven als *verval datum*. Een achterstallige abonnement wordt uitgeschakeld als de factuur niet wordt betaald.

## <a name="invoices-for-mosp-billing-accounts"></a>Facturen voor de MOSP-factureringsrekeningen

Een MSOP-factureringsrekening wordt gemaakt wanneer u zich aanmeldt voor Azure via de Azure-website. Bijvoorbeeld als u zich aanmeldt voor een [gratis Azure-account](https://azure.microsoft.com/offers/ms-azr-0044p/), een [account met tarieven op gebruiksbasis](https://azure.microsoft.com/offers/ms-azr-0003p/) of een [Visual Studio-abonnement](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

Klanten in bepaalde regio's, die zich via de Azure-website aanmelden voor een [account met betalen-naar-gebruik-tarieven](https://azure.microsoft.com/offers/ms-azr-0003p/) of een [gratis Azure-account](https://azure.microsoft.com/offers/ms-azr-0044p/), kunnen ook een factureringsrekening voor een MCA hebben.

Zie [Uw type factureringsrekening controleren](../manage/view-all-accounts.md#check-the-type-of-your-account) als u niet zeker weet welk type factureringsrekening u hebt, voordat u de instructies in dit artikel volgt. 

Een MOSP-factureringsrekening kan de volgende facturen hebben:

**Azure-servicekosten**: er wordt een factuur gegenereerd voor elk Azure-abonnement met Azure-resources die worden gebruikt door het abonnement. De factuur bevat kosten voor een factureringsperiode. De factureringsperiode wordt vastgesteld op basis van de dag van de maand waarop het abonnement is gemaakt.

Jeroen maakt bijvoorbeeld *Azure-sub 01* op 5 maart en *Azure-sub 02* op 10 maart. De factuur voor *Azure-sub 01* bevat de kosten vanaf de vijfde dag van de maand tot en met de vierde dag van de volgende maand. De factuur voor *Azure-sub 02* bevat kosten vanaf de tiende dag van de maand tot de negende dag van de volgende maand. De facturen voor alle Azure-abonnementen worden normaal gesproken gegenereerd op de dag van de maand dat het account is gemaakt, maar dat kan ook tot maximaal twee dagen later zijn. Als Jeroen bijvoorbeeld een account heeft gemaakt op 2 februari, worden de facturen voor zowel *Azure-sub 01* als *Azure-sub 02* normaal gesproken gegenereerd op de tweede dag van elke maand, maar dat kan ook tot maximaal twee dagen later zijn.

**Azure Marketplace, reserveringen en spot-VM's**: er wordt een factuur gegenereerd voor reserveringen, Marketplace-producten en spot-VM's die zijn gekocht via een abonnement. De factuur bevat de respectievelijke kosten over de vorige maand. Jeroen heeft bijvoorbeeld een reservering aangeschaft op 1 maart en een andere reservering op 30 maart. Er wordt in april één factuur gegenereerd voor beide reserveringen. De factuur voor Azure Marketplace, reserveringen en spot-VM's wordt altijd gegenereerd rond de negende dag van de maand.

Als u voor Azure betaalt met een creditcard en u de reservering koopt, genereert Azure onmiddellijk een factuur. Als er echter wordt gefactureerd op basis van een factuur, worden er op uw volgende maandelijkse factuur kosten in rekening gebracht voor de reservering.

**Azure-ondersteuningsplan**: er wordt elke maand een factuur gegenereerd voor het abonnement op uw ondersteuningsplan. De eerste factuur wordt gegenereerd op de dag van aankoop of tot maximaal twee dagen later. Volgende facturen voor ondersteuningsplannen worden normaal gesproken gegenereerd op dezelfde dag van de maand dat het account is gemaakt, maar dat kan ook tot twee dagen later zijn.

## <a name="download-your-mosp-azure-subscription-invoice"></a>De factuur voor uw MOSP Azure-abonnement downloaden

Een factuur wordt alleen gegenereerd voor een abonnement dat deel uitmaakt van een factureringsrekening voor een MOSP. [Controleer uw toegang tot een MOSP-rekening](../manage/view-all-accounts.md#check-the-type-of-your-account). 

U moet voor een abonnement de rol van accountbeheerder hebben om de factuur te kunnen downloaden. Gebruikers met de rol van eigenaar, inzender of lezer kunnen de factuur downloaden als de accountbeheerder hen daartoe toestemming heeft gegeven. Zie [Gebruikers toestaan facturen te downloaden](../manage/manage-billing-access.md#opt-in)voor meer informatie.

1. Selecteer uw abonnement op de [pagina Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in de Azure-portal.
1. Selecteer **Facturen** in de factureringssectie.  
    ![Schermopname van een gebruiker die de optie Facturen voor een abonnement selecteert](./media/download-azure-invoice/select-subscription-invoice.png)
1. Selecteer de factuur die u wilt downloaden en klik vervolgens op **facturen downloaden**.  
    ![Scherm opname van de download optie voor een MOSP-factuur](./media/download-azure-invoice/downloadinvoice-subscription.png)
1. U kunt ook een dagelijkse uitsplitsing van verbruikte hoeveel heden en kosten downloaden door te klikken op het Download pictogram en vervolgens te klikken op de knop **Azure-gebruiks bestand voorbereiden** in het gedeelte gebruiks gegevens. Het kan enkele minuten duren om het CSV-bestand voor te bereiden.  
    ![Schermopname met de pagina voor factuur en gebruik downloaden](./media/download-azure-invoice/usage-and-invoice-subscription.png)

Zie [Meer informatie over uw factuur voor Microsoft Azure](../understand/review-individual-bill.md) voor meer informatie over uw factuur. Zie [onverwachte kosten analyseren](analyze-unexpected-charges.md)voor hulp bij het identificeren van ongebruikelijke kosten.

## <a name="download-your-mosp-support-plan-invoice"></a>De factuur voor uw MOSP-ondersteuningsplan downloaden

Een factuur wordt alleen gegenereerd voor een ondersteuningsplanabonnement dat deel uitmaakt van een factureringsrekening voor een MOSP. [Controleer uw toegang tot een MOSP-rekening](../manage/view-all-accounts.md#check-the-type-of-your-account).

U moet voor het ondersteuningsplanabonnement de rol van accountbeheerder hebben om de factuur ervoor te kunnen downloaden.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar **Kostenbeheer en facturering**.  
    ![Schermopname van de zoekopdracht naar kostenbeheer en facturering in de Azure-portal](./media/download-azure-invoice/search-cmb.png)
1. Selecteer **Facturen** aan de linkerkant.
1. Selecteer uw abonnement voor het ondersteunings abonnement.  
    [![Scherm opname van de facturerings profiel lijst voor het ondersteunings plan MOSP](./media/download-azure-invoice/cmb-invoices.png)](./media/download-azure-invoice/cmb-invoices-zoomed-in.png#lightbox)
1. Selecteer de factuur die u wilt downloaden en klik vervolgens op **facturen downloaden**.  
    ![Scherm opname van de download optie voor een MOSP-ondersteunings plan ](./media/download-azure-invoice/download-invoice-support-plan.png)

## <a name="allow-others-to-download-your-subscription-invoice"></a>Anderen toestaan uw abonnements factuur te downloaden

Een factuur downloaden:

1.  Meld u als accountbeheerder voor het abonnement aan bij de [Azure-portal](https://portal.azure.com).

2.  Zoek naar **Kostenbeheer en facturering**.

    ![Schermopname van de zoekopdracht naar kostenbeheer en facturering in de Azure-portal](./media/download-azure-invoice/search-cmb.png)

3.  Selecteer **Facturen** aan de linkerkant.

4.  Selecteer uw Azure-abonnement en klik op **Anderen toestemming geven om factuur te downloaden**.

    [![Schermopname van de selectie van Toegang tot factuur](./media/download-azure-invoice/cmb-select-access-to-invoice.png)](./media/download-azure-invoice/cmb-select-access-to-invoice-zoomed-in.png#lightbox)

5.  Selecteer **Aan** en **Opslaan** boven aan de pagina.  
    ![Schermopname waarop Aan voor Toegang tot factuur wordt geselecteerd](./media/download-azure-invoice/cmb-access-to-invoice.png)
    
> [!NOTE]
> Micro soft raadt u niet aan om uw vertrouwelijke of persoonlijke gegevens met derden te delen. Deze aanbeveling is van toepassing op het delen van uw Azure-factuur of factuur met een derde partij voor kosten optimalisaties. Zie voor meer informatie https://azure.microsoft.com/support/legal/ en https://www.microsoft.com/trust-center.

## <a name="get-mosp-subscription-invoice-in-email"></a>Factuur voor MOSP-abonnement via e-mail ontvangen

U moet beschikken over de rol van accountbeheerder voor een abonnement of een ondersteuningsplan om te kunnen aangeven dat u de factuur ervoor per e-mail wilt ontvangen. E-mail facturen zijn alleen beschikbaar voor abonnementen en ondersteuningsplannen, niet voor reserveringen of Azure Marketplace-aankopen. Zodra u zich hebt aangemeld, kunt u extra ontvangers toevoegen die de factuur ook per e-mail ontvangen.

1.  Meld u aan bij de [Azure-portal](https://portal.azure.com).
2.  Zoek naar **Kostenbeheer en facturering**.  
3.  Selecteer **Facturen** aan de linkerkant.
4.  Selecteer uw Azure-abonnement of ondersteuningsplanabonnement en selecteer vervolgens **Factuur ontvangen per e-mail**.  
    [![Scherm afbeelding met de optie factuur per e-mail ontvangen](./media/download-azure-invoice/cmb-email-invoice.png)](./media/download-azure-invoice/cmb-email-invoice-zoomed-in.png#lightbox)
5. Klik op **E-mailfactuur** en accepteer de voorwaarden.  
    ![Schermopname met stap 2 van de aanmelding](./media/download-azure-invoice/invoicearticlestep02.png)
6. De factuur wordt verzonden naar het e-mailadres van uw voorkeur. Selecteer **Profiel bijwerken** om het e-mailadres bij te werken.  
    ![Schermopname met stap 3 van de aanmelding](./media/download-azure-invoice/invoicearticlestep03-verifyemail.png)

## <a name="share-subscription-and-support-plan-invoice"></a>Factuur voor abonnements-en ondersteunings abonnement delen

U kunt de factuur voor uw abonnement en het ondersteunings plan elke maand met uw boekhoud team delen of verzenden naar een van uw andere e-mail adressen.

1. Volg de stappen in [De facturen van uw abonnement en ondersteuningsplan per e-mail ontvangen](#get-mosp-subscription-invoice-in-email) en selecteer **Ontvangers configureren**.  
    [![Scherm opname van het selecteren van ontvangers configureren](./media/download-azure-invoice/invoice-article-step03.png)](./media/download-azure-invoice/invoice-article-step03-zoomed.png#lightbox)
1. Voer een e-mailadres in en selecteer **Ontvanger toevoegen**. U kunt meerdere e-mailadressen toevoegen.  
    ![Schermopname van een gebruiker die extra ontvangers toevoegt](./media/download-azure-invoice/invoice-article-step04.png)
1. Nadat u alle e-mailadressen hebt toegevoegd, selecteert u **Gereed** onder aan het scherm.

## <a name="invoices-for-mca-and-mpa-billing-accounts"></a>Facturen voor MCA- en MPA-factureringsrekeningen

Er wordt een MCA-factureringsrekening gemaakt wanneer uw organisatie samenwerkt met een medewerker van Microsoft om een MCA te ondertekenen. Sommige klanten in bepaalde regio's, die zich via de Azure-website aanmelden voor een [account met betalen-naar-gebruik-tarieven](https://azure.microsoft.com/offers/ms-azr-0003p/) of een [gratis Azure-account](https://azure.microsoft.com/offers/ms-azr-0044p/), hebben mogelijk ook een factureringsrekening voor een MCA. Zie [Aan de slag met uw MCA-factureringsrekening](../understand/mca-overview.md) voor meer informatie.

Er wordt een MPA-factureringsaccount voor CSP-partners (Cloud Solution Provider) gemaakt zodat zij hun klanten kunnen beheren in de nieuwe Commerce-versie. Partners moeten minstens één klant met een [Azure-abonnement](/partner-center/purchase-azure-plan) hebben om hun factureringsaccount te kunnen beheren in de Azure-portal. Zie [Aan de slag met uw MPA-factureringsrekening](../understand/mpa-overview.md) voor meer informatie.

Aan het begin van de maand wordt voor elk factureringsprofiel in uw account een maandelijkse factuur gegenereerd. De factuur bevat de respectievelijke kosten voor alle Azure-abonnementen en andere aankopen van de vorige maand. Jeroen heeft bijvoorbeeld *Azure-sub 01* op 5 maart en *Azure-sub 02* op 10 maart gemaakt. Hij heeft abonnement *Azure support 01* op 28 maart aangeschaft via *Billing profile 01*. Jeroen krijgt aan het begin van april één enkele factuur die de kosten voor zowel Azure-abonnementen als het ondersteuningsplan bevat.

##  <a name="download-an-mca-or-mpa-billing-profile-invoice"></a>Een factuur voor een MCA- of MPA-factureringsprofiel downloaden

U moet de rol van eigenaar, inzender, lezer of factuurbeheerder van een factureringsprofiel hebben om de factuur ervoor te kunnen downloaden in de Azure-portal. Gebruikers met de rol van eigenaar, Inzender of lezer van een factureringsrekening kunnen facturen downloaden voor alle factureringsprofielen in het account.

1.  Meld u aan bij de [Azure-portal](https://portal.azure.com).

2.  Zoek naar **Kostenbeheer en facturering**.

    ![Schermopname van de zoekopdracht naar kostenbeheer en facturering in de Azure-portal](./media/download-azure-invoice/search-cmb.png)

3. Selecteer **Facturen** aan de linkerkant.

    [![Schermopname van de pagina met facturen voor een MCA-factureringsrekening](./media/download-azure-invoice/mca-billingprofile-invoices.png)](./media/download-azure-invoice/mca-billingprofile-invoices-zoomed-in.png#lightbox)

4. Selecteer in de tabel met facturen de factuur die u wilt downloaden.

5. Klik bovenaan de pagina op de knop **PDF met factuur downloaden**.

    [![Schermopname van het downloaden van de PDF met de factuur](./media/download-azure-invoice/mca-billingprofile-download-invoice.png)](./media/download-azure-invoice/mca-billingprofile-download-invoice-zoomed-in.png#lightbox)

6. U kunt ook een dagelijkse uitsplitsing van de verbruikte hoeveelheden en geschatte kosten downloaden door op **Azure-gebruiksgegevens downloaden** te klikken. Het kan enkele minuten duren om het CSV-bestand voor te bereiden.

## <a name="get-your-billing-profiles-invoice-in-email"></a>De factuur voor uw factureringsprofiel via e-mail ontvangen

U moet de rol van eigenaar of Inzender voor het factureringsprofiel of de factureringsrekening hebben om de voorkeur voor het ontvangen van facturen via e-mail bij te werken. Zodra u zich daarvoor hebt aangemeld, ontvangen alle gebruikers met de rol van eigenaar, inzender, lezer of factuurbeheerder in een factureringsprofiel de factuur via e-mail. 

1.  Meld u aan bij de [Azure-portal](https://portal.azure.com).
1.  Zoek naar **Kostenbeheer en facturering**.  
1.  Selecteer **facturen** aan de linkerkant en selecteer vervolgens **factuur e-mail voor keur** boven aan de pagina.  
    [![Scherm afbeelding van de optie voor de e-mail factuur voor facturen](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  Als u meerdere facturerings profielen hebt, selecteert u een facturerings profiel en selecteert u vervolgens **Ja**.  
    [![Scherm opname van de optie voor opt-in](./media/download-azure-invoice/mca-billing-profile-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  Selecteer **Opslaan**.

U kunt anderen toegang geven om facturen te bekijken, te downloaden en te betalen door hen de rol van factuurbeheerder toe te wijzen voor een MCA- of MPA-factureringsprofiel. Als u ervoor hebt gekozen om facturen via e-mail te ontvangen, krijgen de gebruikers de facturen ook via e-mail.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar **Kostenbeheer en facturering**.  
1. Selecteer aan de linkerkant de optie **Factureringsprofielen**. Selecteer in de lijst factureringsprofielen een factureringsprofiel waarvoor u de rol van factuurbeheerder wilt toewijzen.  
   ![Scherm opname van de lijst met facturerings profielen waar u een facturerings profiel selecteert](./media/download-azure-invoice/mca-select-profile-zoomed-in.png)
1. Selecteer **Toegangsbeheer (IAM)** aan de linkerkant en selecteer vervolgens **Toevoegen** bovenaan de pagina.  
    ![Schermopname van de pagina Toegangsbeheer](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png)
1. Selecteer **Factuurbeheerder** in de vervolgkeuzelijst Rol. Voer het e-mailadres in van de gebruiker die u toegang wilt verlenen. Selecteer **Opslaan** om de rol toe te wijzen.  
    [![Schermopname van het toevoegen van een gebruiker als een factuurbeheerder](./media/download-azure-invoice/mca-added-invoice-manager.png)](./media/download-azure-invoice/mca-added-invoice-manager.png#lightbox)
   

## <a name="share-your-billing-profiles-invoice"></a>De factuur van uw facturerings profiel delen

U kunt uw factuur elke maand met uw boekhoud team delen of ze verzenden naar een van uw andere e-mail adressen zonder uw boekhoud team of de andere e-mail machtigingen voor uw facturerings profiel te geven.

1.  Meld u aan bij de [Azure-portal](https://portal.azure.com).
1.  Zoek naar **Kostenbeheer en facturering**.  
1.  Selecteer **facturen** aan de linkerkant en selecteer vervolgens **factuur e-mail voor keur** boven aan de pagina.  
    [![Scherm afbeelding van de optie voor de e-mail factuur voor facturen](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  Als u meerdere facturerings profielen hebt, selecteert u een facturerings profiel.
1.  Voeg in de sectie extra ontvangers de e-mail adressen toe om facturen te ontvangen.
    [![Scherm opname van de extra ontvangers voor het factuur bericht](./media/download-azure-invoice/mca-billing-profile-add-invoice-recipients.png)](./media/download-azure-invoice/mca-billing-profile-add-invoice-recipients-zoomed.png#lightbox)
1.  Selecteer **Opslaan**.

##  <a name="why-you-might-not-see-an-invoice"></a>Waarom u mogelijk geen factuur ziet

<a name="noinvoice"></a>

Er kunnen verschillende redenen voor zijn dat u geen factuur ziet:

- De factuur is nog niet klaar
    
    - Het is minder dan 30 dagen geleden dat u zich geabonneerd hebt op Azure. 

    - Azure factureert u een paar dagen na het einde van de factureringsperiode. Het is dus mogelijk dat er nog geen factuur is gegenereerd.

- U bent niet gemachtigd om facturen te bekijken. 
    
    - Als u een MCA- of MPA-factureringsrekening hebt, moet u de rol van eigenaar, inzender, lezer of factuurbeheerder voor het factureringsprofiel of de rol van eigenaar, inzender of lezer voor de factureringsrekening hebben om facturen te kunnen bekijken. 
    
    - Voor andere factureringsaccounts ziet u mogelijk geen facturen als u niet de accountbeheerder bent.

- Uw account biedt geen ondersteuning voor facturen.

    - Als u een factureringsaccount voor Microsoft Online Services Program (MOSP) hebt en u zich hebt geregistreerd voor een gratis Azure-account of een abonnement met een maandelijks tegoed, ontvangt u alleen een factuur wanneer u het maandelijkse tegoed overschrijdt.

    - Als u een factureringsaccount voor een Microsoft-klantovereenkomst of Microsoft Partner-overeenkomst hebt, ontvangt u altijd een factuur.

- U hebt toegang tot de factuur via een van uw andere accounts.

    - Dit gebeurt meestal wanneer u op een koppeling in het e-mailbericht klikt, waarin u wordt gevraagd uw factuur weer te geven in de portal. U klikt op de koppeling en ziet een foutbericht: `We can't display your invoices. Please try again`. Controleer of u bent aangemeld met het e-mailadres dat is gemachtigd om de facturen te bekijken.

- U hebt toegang tot de factuur via een andere identiteit. 

    - Sommige klanten hebben twee identiteiten met hetzelfde e-mailadres: een werkaccount en een Microsoft-account. Normaal gesproken heeft slechts een van de identiteiten machtigingen voor het weergeven van facturen. Als de gebruiker zich aanmeldt met de identiteit die niet is gemachtigd, worden de facturen niet weergegeven. Controleer of u de juiste identiteit gebruikt om u aan te melden.

- U bent aangemeld bij de verkeerde Azure Active Directory-Tenant (Azure AD). 

    - Uw facturerings account is gekoppeld aan een Azure AD-Tenant. Als u bent aangemeld bij een onjuiste tenant, wordt de factuur voor abonnementen niet weergegeven in op factureringsaccount. Controleer of u bent aangemeld bij de juiste Azure AD-Tenant. Als u niet bent aangemeld bij de juiste tenant, gaar u als volgt te werk om van tenant te wisselen in de Azure-portal:

        1. Selecteer uw e-mailadres rechtsboven op de pagina.

        2. Selecteer **Schakelen tussen directory's**.

           ![Schermopname van het selecteren van Schakelen tussen directory's in de portal](./media/download-azure-invoice/select-switch-directory.png)

        3. Selecteer een directory in de sectie **Alle directory's**.

           ![Schermopname van het selecteren van een directory in de portal](./media/download-azure-invoice/select-directory.png)

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over uw factuur en kosten:

- [Uw Microsoft Azure-gebruik en -kosten weergeven en downloaden](download-azure-daily-usage.md)
- [Meer informatie over uw factuur voor Microsoft Azure](review-individual-bill.md)
- [Informatie over begrippen op uw Azure-factuur](understand-invoice.md)

Als u een MCA hebt, zie:

- [Informatie over de kosten op de factuur voor uw factureringsprofiel](review-customer-agreement-bill.md)
- [Informatie over begrippen op de factuur voor uw factureringsprofiel](mca-understand-your-invoice.md)
- [Informatie over het bestand voor Azure-gebruik en -kosten voor uw factureringsprofiel](mca-understand-your-usage.md)