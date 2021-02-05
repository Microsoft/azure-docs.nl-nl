---
title: Factureringsrollen voor Microsoft-klantovereenkomsten - Azure
description: Meer informatie over factureringsrollen voor factureringsrekeningen in Azure voor Microsoft-klantovereenkomsten.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 02/05/2021
ms.author: banders
ms.openlocfilehash: 00ac61567502984759c5db9837060c86aaee378d
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593583"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Informatie over beheerdersrollen voor Microsoft-klantovereenkomsten in Azure

Als u uw factureringsrekening voor een Microsoft-klantovereenkomst wilt beheren, gebruikt u de rollen die in de volgende secties worden beschreven. Deze rollen zijn een aanvulling op de ingebouwde rollen in Azure voor het beheren van de toegang tot resources. Zie [Ingebouwde rollen in Azure](../../role-based-access-control/built-in-roles.md) voor meer informatie.

Dit artikel is van toepassing op een factureringsrekening voor een Microsoft-klantovereenkomst. Controleer of u toegang hebt tot een Microsoft-klantovereenkomst.

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
|Factuurbeheerder|Facturen voor factureringsprofiel weergeven|
|Eigenaar van factuursectie|Alles beheren voor de factuursectie|
|Inzender van factuursectie|Alles beheren voor de factuursectie, behalve machtigingen|
|Lezer van factuursectie|Alleen-lezen weergave van alles voor de factuursectie|
|Maker van Azure-abonnement|Azure-abonnementen maken|

## <a name="billing-account-roles-and-tasks"></a>Rollen en taken voor factureringsrekening

Met een factureringsrekening kunt u de facturering voor uw organisatie beheren. U gebruikt een factureringsrekening voor het ordenen en bewaken van kosten en facturen en het beheren van de toegang tot facturering voor uw organisatie. Zie [Informatie over factureringsrekening](../understand/mca-overview.md#your-billing-account) voor meer informatie.

In de volgende tabellen ziet u welke rol u nodig hebt om taken uit te voeren in de context van de factureringsrekening.

### <a name="manage-billing-account-permissions-and-properties"></a>Machtigingen en eigenschappen voor factureringsrekeningen beheren

|Taak|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening|
|---|---|---|---|
|Bestaande machtigingen voor de factureringsrekening weergeven|✔|✔|✔|
|Anderen machtigingen geven voor het weergeven en beheren van de factureringsrekening|✔|✘|✘|
|Eigenschappen van de factureringsrekening weergeven, zoals de bedrijfsnaam, het adres, enzovoort|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>Factureringsprofielen voor de factureringsrekening beheren

|Taak|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening|
|---|---|---|---|
|Alle factureringsprofielen voor de rekening weergeven|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>Facturen beheren voor de factureringsrekening

|Taak|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening|
|---|---|---|---|
|Alle facturen voor de rekening weergeven|✔|✔|✔|
|Facturen, bestanden van het gebruik en kosten van Azure, prijzenoverzichten en belastingdocumenten voor de rekening downloaden|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>Factuursecties beheren voor de factureringsrekening

|Taak|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening|
|---|---|---|---|
|Alle factuursecties voor de rekening weergeven|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>Transacties beheren voor de factureringsrekening

|Taak|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening|
|---|---|---|---|
|Alle factureringstransacties voor de rekening weergeven|✔|✔|✔|
|Alle gekochte producten voor de rekening weergeven|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>Abonnementen beheren voor de factureringsrekening

|Taak|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening|
|---|---|---|---|
|Alle Azure-abonnementen voor de factureringsrekening weergeven|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>Rollen en taken voor factureringsprofiel

Met een factureringsprofiel kunt u uw facturen en betalingswijzen beheren. Er wordt een maandelijkse factuur gegenereerd voor de Azure-abonnementen en andere producten die zijn gekocht met behulp van het factureringsprofiel. U gebruikt de betalingswijzen om de factuur te betalen. Zie [Informatie over factureringsprofielen](../understand/mca-overview.md#billing-profiles) voor meer informatie.

In de volgende tabellen ziet u welke rol u nodig hebt om taken uit te voeren in de context van het factureringsprofiel.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Machtigingen, eigenschappen en beleidsregels voor factureringsprofielen beheren

|Taak|Eigenaar van factureringsprofiel|Inzender van factureringsprofiel|Lezer van factureringsprofiel|Factuurbeheerder|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening
|---|---|---|---|---|---|---|---|
|Bestaande machtigingen voor het factureringsprofiel weergeven|✔|✔|✔|✔|✔|✔|✔|
|Anderen machtigingen geven voor het weergeven en beheren van het factureringsprofiel|✔|✘|✘|✘|✘|✘|✘|
|Eigenschappen van het factureringsprofiel weergeven, zoals inkoopordernummer, voorkeur voor facturen per e-mail, enzovoort|✔|✔|✔|✔|✔|✔|✔|
|Eigenschappen van factureringsprofiel bijwerken |✔|✔|✘|✘|✘|✘|✘|
|Beleidsregels weergeven die op het factureringsprofiel zijn toegepast, zoals Azure-reserveringsaankopen inschakelen, Azure Marketplace-aankopen inschakelen, enzovoort|✔|✔|✔|✔|✔|✔|✔|
|Beleidsregels toepassen op het factureringsprofiel |✔|✔|✘|✘|✘|✘|✘|
|Reserverings orders beheren |✔|✔|✘|✘|✘|✘|✘|
|Reserverings orders weer geven |✔|✔|✔|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>Facturen beheren voor het factureringsprofiel

|Taak|Eigenaar van factureringsprofiel|Inzender van factureringsprofiel|Lezer van factureringsprofiel|Factuurbeheerder|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening
|---|---|---|---|---|---|---|---|
|Alle facturen voor het factureringsprofiel weergeven|✔|✔|✔|✔|✔|✔|✔|
|Facturen, bestanden van het gebruik en kosten van Azure, prijzenoverzichten en belastingdocumenten voor het factureringsprofiel downloaden|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Factuursecties beheren voor het factureringsprofiel

|Taak|Eigenaar van factureringsprofiel|Inzender van factureringsprofiel|Lezer van factureringsprofiel|Factuurbeheerder|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening
|---|---|---|---|---|---|---|---|
|Alle factuursecties voor het factureringsprofiel weergeven|✔|✔|✔|✔|✔|✔|✔|
|Nieuwe factuursectie voor het factureringsprofiel maken|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>Transacties beheren voor het factureringsprofiel

|Taak|Eigenaar van factureringsprofiel|Inzender van factureringsprofiel|Lezer van factureringsprofiel|Factuurbeheerder|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening
|---|---|---|---|---|---|---|---|
|Alle factureringstransacties voor het factureringsprofiel weergeven|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>Betaalwijzen beheren voor het factureringsprofiel

|Taak|Eigenaar van factureringsprofiel|Inzender van factureringsprofiel|Lezer van factureringsprofiel|Factuurbeheerder|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening
|---|---|---|---|---|---|---|---|
|Betaalwijzen voor het factureringsprofiel weergeven|✔|✔|✔|✔|✔|✔|✔|
|Saldo van het Azure-tegoed voor het factureringsprofiel bijhouden|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Abonnementen beheren voor het factureringsprofiel

|Taak|Eigenaar van factureringsprofiel|Inzender van factureringsprofiel|Lezer van factureringsprofiel|Factuurbeheerder|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening
|---|---|---|---|---|---|---|---|
|Alle Azure-abonnementen voor het factureringsprofiel weergeven|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>Rollen en taken voor factuursectie

Met een factuursectie kunt u de kosten op uw factuur ordenen. U kunt een sectie maken om uw kosten te ordenen op afdeling, ontwikkelingsomgeving of op basis van de behoeften van uw organisatie. Geef andere machtigingen voor het maken van Azure-abonnementen voor de sectie. Alle gebruikskosten en aankopen voor de abonnementen worden vervolgens weergegeven in de sectie van de factuur. Zie [Informatie over factuursectie](../understand/mca-overview.md#invoice-sections) voor meer informatie.

In de volgende tabellen ziet u welke rol u nodig hebt om taken uit te voeren in de context van factuursecties.

### <a name="manage-invoice-section-permissions-and-properties"></a>Machtigingen en eigenschappen voor factuursecties beheren

|Taken|Eigenaar van factuursectie|Inzender van factuursectie|Lezer van factuursectie|Maker van Azure-abonnement|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening |
|---|---|---|---|---|---|---|---|
|Alle machtigingen voor factuursectie weergeven|✔|✔|✔|✔|✔|✔|✔|
|Anderen machtigingen geven voor het weergeven en beheren van de factuursectie|✔|✘|✘|✘|✘|✘|✘|
|Eigenschappen van factuursectie weergeven|✔|✔|✔|✔|✔|✔|✔|
|Eigenschappen van factuursectie bijwerken|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>Producten beheren voor de factuursectie

|Taken|Eigenaar van factuursectie|Inzender van factuursectie|Lezer van factuursectie|Maker van Azure-abonnement|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening
|---|---|---|---|---|---|---|---|
|Alle producten weergeven die in de factuursectie zijn gekocht|✔|✔|✔|✘|✔|✔|✔|
|Facturering voor producten beheren voor de factuursectie, zoals annuleren, automatische verlenging uitschakelen, enzovoort|✔|✔|✘|✘|✘|✘|✘|
|Factuursectie voor de producten wijzigen|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Abonnementen voor factuursectie beheren

|Taken|Eigenaar van factuursectie|Inzender van factuursectie|Lezer van factuursectie|Maker van Azure-abonnement|Eigenaar van factureringsrekening|Inzender van factureringsrekening|Lezer van factureringsrekening
|---|---|---|---|---|---|---|---|
|Alle Azure-abonnementen voor factuursectie weergeven|✔|✔|✔|✘|✔|✔|✔|
|Factuursectie voor de abonnementen wijzigen|✔|✔|✘|✘|✘|✘|✘|
|Eigendom voor facturering van abonnementen van gebruikers in andere factureringsrekeningen aanvragen|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Rollen en taken voor abonnementsfacturering

In de volgende tabel ziet u welke rol u nodig hebt om taken uit te voeren in de context van een abonnement.

|Taken|Eigenaar van factuursectie|Inzender van factuursectie|Lezer van factuursectie|Maker van Azure-abonnement|
|---|---|---|---|---|
|Azure-abonnementen maken|✔|✔|✘|✔|
|Kostenplaats voor het abonnement bijwerken|✔|✔|✘|✘|
|Factuursectie voor het abonnement wijzigen|✔|✔|✘|✘|

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
