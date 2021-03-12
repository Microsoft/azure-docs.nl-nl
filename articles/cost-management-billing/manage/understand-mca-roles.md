---
title: Factureringsrollen voor Microsoft-klantovereenkomsten - Azure
description: Meer informatie over factureringsrollen voor factureringsrekeningen in Azure voor Microsoft-klantovereenkomsten.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: e334a423fd11aa3a357d52099a792dcc905aedeb
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011660"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Informatie over beheerdersrollen voor Microsoft-klantovereenkomsten in Azure

Als u uw factureringsrekening voor een Microsoft-klantovereenkomst wilt beheren, gebruikt u de rollen die in de volgende secties worden beschreven. Deze rollen zijn een aanvulling op de ingebouwde rollen in Azure voor het beheren van de toegang tot resources. Zie [Ingebouwde rollen in Azure](../../role-based-access-control/built-in-roles.md) voor meer informatie.

Dit artikel is van toepassing op een factureringsrekening voor een Microsoft-klantovereenkomst. [Controleer of u toegang hebt tot een Microsoft-klantovereenkomst](#check-access-to-a-microsoft-customer-agreement).

Bekijk de video [toegang tot uw MCA-facturerings account beheren](https://www.youtube.com/watch?v=9sqglBlKkho) voor meer informatie over hoe u de toegang tot uw MCA-facturerings account (micro soft Customer Agreement) kunt controleren.

>[!VIDEO https://www.youtube.com/embed/9sqglBlKkho]

## <a name="billing-role-definitions"></a>Roldefinities voor facturering

In de volgende tabel worden de factureringsrollen beschreven die u gebruikt voor het beheren van uw factureringsrekening, factureringsprofielen en factuursecties.

|Rol|Beschrijving|
|---|---|
|Eigenaar van factureringsrekening|Alles beheren voor de factureringsrekening|
|Inzender van factureringsrekening|Alles beheren voor de factureringsrekening, behalve machtigingen|
|Lezer van factureringsrekening|Alleen-lezen weer gave van alles voor de factureringsrekening|
|Eigenaar van factureringsprofiel|Alles beheren voor het factureringsprofiel|
|Inzender van factureringsprofiel|Alles beheren voor het factureringsprofiel, behalve machtigingen|
|Lezer van factureringsprofiel|Alleen-lezen weer gave van alles voor het factureringsprofiel|
|Factuurbeheerder|Facturen voor het factureringsprofiel weergeven en betalen|
|Eigenaar van factuursectie|Alles beheren voor de factuursectie|
|Inzender van factuursectie|Alles beheren voor de factuursectie, behalve machtigingen|
|Lezer van factuursectie|Alleen-lezen weergave van alles voor de factuursectie|
|Maker van Azure-abonnement|Azure-abonnementen maken|

## <a name="billing-account-roles-and-tasks"></a>Rollen en taken voor factureringsrekening

Er wordt een factureringsaccount gemaakt wanneer u zich aanmeldt om Azure te gebruiken. U gebruikt uw factureringsaccount om facturen en betalingen te beheren en kosten bij te houden. Rollen op het facturerings account hebben het hoogste machtigings niveau en gebruikers in deze rollen krijgen inzicht in de kosten-en facturerings gegevens voor uw hele account. Wijs deze rollen alleen toe aan gebruikers die facturen moeten weer geven en houd de kosten bij voor uw hele account, zoals het lid van de financiële en financiële teams. Zie [Informatie over factureringsrekening](../understand/mca-overview.md#your-billing-account) voor meer informatie.

In de volgende tabellen ziet u welke rol u nodig hebt om taken uit te voeren in de context van de factureringsrekening.

### <a name="manage-billing-account-permissions-and-properties"></a>Machtigingen en eigenschappen voor factureringsrekeningen beheren

|Taak|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening|
|---|---|---|---|
|Roltoewijzingen voor het facturerings account weer geven|✔|✔|✔|
|Anderen machtigingen geven voor het weergeven en beheren van de factureringsrekening|✔|✘|✘|
|Eigenschappen van het facturerings account weer geven, zoals adres, overeenkomsten en meer|✔|✔|✔|
|De eigenschappen van het facturerings account zoals verkocht, de weergave naam en meer bijwerken|✔|✔|✘|

### <a name="manage-billing-profiles-for-billing-account"></a>Factureringsprofielen voor de factureringsrekening beheren

|Taak|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening|
|---|---|---|---|
|Alle facturerings profielen voor het account weer geven|✔|✔|✔|
|Nieuwe facturerings profielen maken|✔|✔|✘|

### <a name="manage-invoices-for-billing-account"></a>Facturen beheren voor de factureringsrekening

|Taak|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening|
|---|---|---|---|
|Alle facturen voor het account weer geven|✔|✔|✔|
|Facturen betalen met een credit card|✔|✔|✘|
|Facturen, Azure-gebruiks bestanden, prijzen overzichten en BTW-documenten downloaden|✔|✔|✔|

### <a name="manage-products-for-billing-account"></a>Producten beheren voor het facturerings account

|Taak|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening|
|---|---|---|---|
|Alle producten weer geven die zijn gekocht voor het account|✔|✔|✔|
|Facturering voor producten als annuleren beheren, automatische verlenging uitschakelen en meer|✔|✔|✘|

### <a name="manage-subscriptions-for-billing-account"></a>Abonnementen beheren voor de factureringsrekening

|Taak|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening|
|---|---|---|---|
|Alle Azure-abonnementen weer geven die zijn gemaakt voor het facturerings account|✔|✔|✔|
|Nieuwe Azure-abonnementen maken|✔|✔|✘|
|Azure-abonnementen annuleren|✘|✘|✘|

## <a name="billing-profile-roles-and-tasks"></a>Rollen en taken voor factureringsprofiel

Elk facturerings account heeft ten minste één facturerings profiel. Uw eerste facturerings profiel wordt ingesteld wanneer u zich aanmeldt om Azure te gebruiken. Er wordt een maandelijkse factuur gegenereerd voor het facturerings profiel en bevat alle bijbehorende kosten van de vorige maand. U kunt meer facturerings profielen instellen op basis van uw behoeften. Gebruikers met rollen in een facturerings profiel kunnen kosten weer geven, budget instellen en de facturen beheren en betalen. Wijs deze rollen toe aan gebruikers die verantwoordelijk zijn voor het beheren van budget en het betalen van facturen voor het facturerings profiel, zoals leden van de bedrijfs beheer teams in uw organisatie. Zie [Informatie over factureringsprofielen](../understand/mca-overview.md#billing-profiles) voor meer informatie.

In de volgende tabellen ziet u welke rol u nodig hebt om taken uit te voeren in de context van het factureringsprofiel.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Machtigingen, eigenschappen en beleidsregels voor factureringsprofielen beheren

|Taak|Eigenaar van factureringsprofiel|Inzender van factureringsprofiel|Lezer van factureringsprofiel|Factuurbeheerder|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening
|---|---|---|---|---|---|---|---|
|Roltoewijzingen voor het facturerings profiel weer geven|✔|✔|✔|✔|✔|✔|✔|
|Anderen machtigingen geven voor het weergeven en beheren van het factureringsprofiel|✔|✘|✘|✘|✔|✘|✘|
|Eigenschappen van het facturerings profiel weer geven, zoals het io-nummer, de factuur en meer|✔|✔|✔|✔|✔|✔|✔|
|Eigenschappen van factureringsprofiel bijwerken |✔|✔|✘|✘|✔|✔|✘|
|Beleids regels weer geven die zijn toegepast op het facturerings profiel zoals aankopen van Azure-reserve ringen, Azure Marketplace-aankopen en meer|✔|✔|✔|✔|✔|✔|✔|
|Beleidsregels toepassen op het factureringsprofiel |✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-invoices-for-billing-profile"></a>Facturen beheren voor het factureringsprofiel

|Taak|Eigenaar van factureringsprofiel|Inzender van factureringsprofiel|Lezer van factureringsprofiel|Factuurbeheerder|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening
|---|---|---|---|---|---|---|---|
|Alle facturen voor het factureringsprofiel weergeven|✔|✔|✔|✔|✔|✔|✔|
|Facturen betalen met een credit card|✔|✔|✘|✔|✔|✘|✘|
|Facturen, bestanden van het gebruik en kosten van Azure, prijzenoverzichten en belastingdocumenten voor het factureringsprofiel downloaden|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Factuursecties beheren voor het factureringsprofiel

|Taak|Eigenaar van factureringsprofiel|Inzender van factureringsprofiel|Lezer van factureringsprofiel|Factuurbeheerder|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening
|---|---|---|---|---|---|---|---|
|Alle factuursecties voor het factureringsprofiel weergeven|✔|✔|✔|✔|✔|✔|✔|
|Nieuwe factuursectie voor het factureringsprofiel maken|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-products-for-billing-profile"></a>Producten voor het facturerings profiel beheren

|Taak|Eigenaar van factureringsprofiel|Inzender van factureringsprofiel|Lezer van factureringsprofiel|Factuurbeheerder|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening
|---|---|---|---|---|---|---|---|
|Alle producten weer geven die zijn gekocht voor het facturerings profiel|✔|✔|✔|✔|✔|✔|✔|
|Facturering voor producten als annuleren beheren, automatische verlenging uitschakelen en meer|✔|✔|✘|✘|✔|✔|✘|
|Facturerings profiel voor de producten wijzigen|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-payment-methods-for-billing-profile"></a>Betaalwijzen beheren voor het factureringsprofiel

|Taak|Eigenaar van factureringsprofiel|Inzender van factureringsprofiel|Lezer van factureringsprofiel|Factuurbeheerder|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening
|---|---|---|---|---|---|---|---|
|Betaalwijzen voor het factureringsprofiel weergeven|✔|✔|✔|✔|✔|✔|✔|
|Betalings wijzen beheren, zoals het vervangen van een credit card, het ontkoppelen van een credit card en meer|✔|✔|✘|✘|✔|✔|✘|
|Saldo van het Azure-tegoed voor het factureringsprofiel bijhouden|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Abonnementen beheren voor het factureringsprofiel

|Taak|Eigenaar van factureringsprofiel|Inzender van factureringsprofiel|Lezer van factureringsprofiel|Factuurbeheerder|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening
|---|---|---|---|---|---|---|---|
|Alle Azure-abonnementen voor het factureringsprofiel weergeven|✔|✔|✔|✔|✔|✔|✔|
|Nieuwe Azure-abonnementen maken|✔|✔|✘|✘|✔|✔|✘|
|Azure-abonnementen annuleren|✘|✘|✘|✘|✘|✘|✘|
|Facturerings profiel voor de Azure-abonnementen wijzigen|✔|✔|✘|✘|✔|✔|✘|

## <a name="invoice-section-roles-and-tasks"></a>Rollen en taken voor factuursectie

Elk facturerings profiel bevat standaard één factuur gedeelte. U kunt meer factuur secties maken om kosten te groeperen op de factuur van het facturerings profiel.  Gebruikers met rollen op een factuur sectie kunnen bepalen wie Azure-abonnementen maakt en andere aankopen doen. Wijs deze rollen toe aan gebruikers die de Azure-omgeving instellen voor teams in onze organisatie, zoals engineering leads en technische architecten. Zie [Informatie over factuursectie](../understand/mca-overview.md#invoice-sections) voor meer informatie.

In de volgende tabellen ziet u welke rol u nodig hebt om taken uit te voeren in de context van factuursecties.

### <a name="manage-invoice-section-permissions-and-properties"></a>Machtigingen en eigenschappen voor factuursecties beheren

|Taken|Eigenaar van factuursectie|Inzender van factuursectie|Lezer van factuursectie|Maker van Azure-abonnement|Eigenaar van factureringsprofiel|Inzender van factureringsprofiel|Lezer van factureringsprofiel |Factuurbeheerder|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening 
|---|---|---|---|---|---|---|---|---|---|---|---|
|De sectie roltoewijzingen voor de factuur weer geven|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Anderen machtigingen geven voor het weergeven en beheren van de factuursectie|✔|✘|✘|✘|✔|✘|✘|✘|✔|✘|✘|
|Eigenschappen van factuursectie weergeven|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Eigenschappen van factuursectie bijwerken|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-products-for-invoice-section"></a>Producten beheren voor de factuursectie

|Taken|Eigenaar van factuursectie|Inzender van factuursectie|Lezer van factuursectie|Maker van Azure-abonnement|Eigenaar van factureringsprofiel|Inzender van factureringsprofiel|Lezer van factureringsprofiel |Factuurbeheerder|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Alle gekochte producten voor de factuur sectie weer geven|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Facturering voor producten als annuleren beheren, automatische verlenging uitschakelen en meer|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Factuursectie voor de producten wijzigen|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Abonnementen voor factuursectie beheren

|Taken|Eigenaar van factuursectie|Inzender van factuursectie|Lezer van factuursectie|Maker van Azure-abonnement|Eigenaar van factureringsprofiel|Inzender van factureringsprofiel|Lezer van factureringsprofiel |Factuurbeheerder|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Alle Azure-abonnementen voor factuursectie weergeven|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Azure-abonnementen maken|✔|✔|✘|✔|✔|✔|✘|✘|✔|✔|✘|
|Azure-abonnementen annuleren|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|
|Factuur sectie voor het Azure-abonnement wijzigen|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Eigendom voor facturering van abonnementen van gebruikers in andere factureringsrekeningen aanvragen|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Rollen en taken voor abonnementsfacturering

In de volgende tabel ziet u welke rol u nodig hebt om taken uit te voeren in de context van een abonnement.

|Taken|Eigenaar van factuursectie|Inzender van factuursectie|Lezer van factuursectie|Maker van Azure-abonnement|Eigenaar van factureringsprofiel|Inzender van factureringsprofiel|Lezer van factureringsprofiel |Factuurbeheerder|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Abonnementen maken|✔|✔|✘|✔|✔|✔|✘|✘|✔|✔|✘|
|Kostenplaats voor het abonnement bijwerken|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Factuursectie voor het abonnement wijzigen|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Facturerings profiel voor het abonnement wijzigen|✘|✘|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Azure-abonnementen annuleren|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Factureringsrollen beheren in de Azure-portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Zoek naar **Kostenbeheer en facturering**.

   ![Schermopname van de zoekopdracht in de Azure-portal](./media/understand-mca-roles/billing-search-cost-management-billing.png)

3. Selecteer **Toegangsbeheer (IAM)** bij een bereik zoals een factureringsrekening, factureringsprofiel of factuursectie waarvoor u toegangsrechten wilt verlenen.

4. Op de pagina Toegangsbeheer (IAM) vindt u de gebruikers en groepen die zijn toegewezen aan elke rol voor dat bereik.

   ![Schermopname van de lijst met beheerders voor de factureringsrekening](./media/understand-mca-roles/billing-list-admins.png)

5. Als u een gebruiker toegang wilt verlenen, selecteert u **Toevoegen** boven aan de pagina. Selecteer een rol in de vervolgkeuzelijst Rol. Voer het e-mailadres in van de gebruiker die u toegang wilt verlenen. Selecteer **Opslaan** om de rol toe te wijzen.

   ![Schermopname van het toevoegen van een beheerder aan een factureringsrekening](./media/understand-mca-roles/billing-add-admin.png)

6. Als u de toegang voor een gebruiker wilt verwijderen, selecteert u de gebruiker met de roltoewijzing die u wilt verwijderen. Selecteer Verwijderen.

   ![Schermopname van het verwijderen van een beheerder uit een factureringsrekening](./media/understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Toegang tot een Microsoft-klantovereenkomst controleren
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Contact opnemen met ondersteuning
Als u hulp nodig hebt, kunt u [contact opnemen met ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over uw factureringsrekening:

- [Aan de slag met uw factureringsrekening voor een Microsoft-klantovereenkomst](../understand/mca-overview.md)
- [Een Azure-abonnement voor uw factureringsrekening voor een Microsoft-klantovereenkomst maken](create-subscription.md)
