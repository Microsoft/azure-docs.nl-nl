---
title: Factureringsaccounts in de Azure-portal weergeven
description: Ontdek hoe u uw factureringsaccounts kunt weergeven in de Azure-portal. Bekijk bereikinformatie voor Enterprise, Microsoft Customer en Microsoft Partner Agreements.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: banders
ms.openlocfilehash: 3df6b694f3a40cef17c6e0114464ad4aa2fa30ce
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96344331"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Factureringsaccounts en -bereiken in de Azure-portal

Er wordt een factureringsaccount gemaakt wanneer u zich aanmeldt om Azure te gebruiken. U gebruikt uw factureringsaccount om uw facturen en betalingen te beheren en kosten bij te houden. U kunt toegang hebben tot meerdere factureringsaccounts. Stel dat u zich voor uw persoonlijke projecten hebt geregistreerd voor Azure. U kunt ook toegang hebben via de Enterprise-overeenkomst van uw organisatie of de Microsoft-klantovereenkomst. Voor elk van deze scenario's hebt u een afzonderlijk factureringsaccount.

De Azure-portal biedt momenteel ondersteuning voor de volgende typen factureringsaccounts:

- **Microsoft Online Services-programma**: Er wordt een factureringsaccount voor een Microsoft Online Services-programma gemaakt wanneer u zich bij de Azure-website aanmeldt om Azure te gebruiken. Bijvoorbeeld als u zich aanmeldt voor een [gratis Azure-account](https://azure.microsoft.com/offers/ms-azr-0044p/), een [account met tarieven op gebruiksbasis](https://azure.microsoft.com/offers/ms-azr-0003p/) of een [Visual Studio-abonnement](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Enterprise Overeenkomst**: Er wordt een factureringsaccount voor een Enterprise-overeenkomst gemaakt wanneer uw organisatie een [Enterprise-overeenkomst (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) ondertekent om Azure te gebruiken. Een Enterprise-overeenkomst mag maximaal 2000 abonnementen bevatten.

- **Microsoft-klantovereenkomst**: Er wordt een factureringsaccount voor een Microsoft-klantovereenkomst aangemaakt, wanneer uw organisatie samenwerkt met een Microsoft-vertegenwoordiger om een Microsoft-klantovereenkomst te ondertekenen. Sommige klanten in bepaalde regio's, die zich via de Azure-website aanmelden voor een [account met betalen-naar-gebruik-tarieven](https://azure.microsoft.com/offers/ms-azr-0003p/) of een [gratis Azure-account](https://azure.microsoft.com/offers/ms-azr-0044p/), hebben mogelijk ook een factureringsaccount voor een Microsoft-klantovereenkomst. Een Microsoft-klantovereenkomst mag maximaal 20 abonnementen bevatten. Voor meer informatie, zie [Aan de slag met uw factureringsaccount voor een Microsoft-klantovereenkomst](../understand/mca-overview.md).

- **Microsoft Partner-overeenkomst**: Er wordt een factureringsaccount voor een Microsoft-klantovereenkomst gemaakt voor CSP-partners (Cloud Solution Provider) om klanten te beheren in de nieuwe Commerce-versie. Partners moeten minstens één klant met een [Azure-abonnement](/partner-center/purchase-azure-plan) hebben om hun factureringsaccount te kunnen beheren in de Azure-portal. Raadpleeg [Aan de slag met uw factureringsaccount voor een Microsoft-klantovereenkomst](../understand/mpa-overview.md) voor meer informatie.

Zie [Het type van uw factureringsaccount controleren](#check-the-type-of-your-account) om het type van uw factureringsaccount te bepalen.

## <a name="scopes-for-billing-accounts"></a>Bereiken voor factureringsaccounts
Een bereik is een knooppunt binnen een factureringsaccount waar u facturen kunt weergeven en beheren. Dit de plek waar u factureringsgegevens, betalingen en facturen beheert en algemeen accountbeheer uitvoert.

Als u geen toegang hebt om factureringsaccounts weer te geven of te beheren, hebt u waarschijnlijk geen toegang. U kunt de beheerder van uw factureringsaccount vragen om u toegang te geven. Raadpleeg voor meer informatie de volgende artikelen:

- [Toegang tot Microsoft Online Services Program](manage-billing-access.md)
- [Toegang tot Microsoft-klantovereenkomst](understand-mca-roles.md)
- [Toegang tot Enterprise Agreement](understand-ea-roles.md)

### <a name="microsoft-online-services-program"></a>Microsoft Online Services-programma

![Schermopname van de MOSP-hiërarchie](./media/view-all-accounts/mosp-hierarchy.png)

|Bereik  |Definitie  |
|---------|---------|
|Factureringsaccount     | Vertegenwoordigt een overeenkomst dat een klant akkoord gaat met het gebruik van Azure. Dit account bevat een of meer abonnementen.  |
|Abonnement     |  Verwijst naar een groepering van Azure-resources. De factuur wordt gegenereerd in dit bereik. Andere factureringsgegevens, zoals betalingswijzen en gebruiksadres, worden aan dit bereik gekoppeld.|

### <a name="enterprise-agreement"></a>Enterprise Agreement

![Schermopname van de EA-hiërarchie](./media/view-all-accounts/ea-hierarchy.png)

|Bereik  |Definitie  |
|---------|---------|
|Factureringsaccount    | Verwijst naar de registratie van een Enterprise-overeenkomst. Bevat een of meer afdelingen en accounts. De factuur wordt gegenereerd in dit bereik. |
|Afdeling     |  Optionele groepering van accounts voor het segmenteren van kosten in logische groepen en het instellen van budgetten.     |
|Account     |  Verwijst naar een enkele accounteigenaar. Accounteigenaren hebben machtigingen voor het maken en beheren van Azure-abonnementen die worden gefactureerd voor de inschrijving. |

### <a name="microsoft-customer-agreement"></a>Microsoft-klantovereenkomst

![Schermopname van de MCA-hiërarchie](./media/view-all-accounts/mca-hierarchy.png)

|Bereik  |Taken  |
|---------|---------|
|Factureringsaccount     |   Vertegenwoordigt een overeenkomst dat een klant akkoord gaat met het gebruik van Microsoft -producten en -services. Bevat een of meer factureringsprofielen. |
|Factureringsprofiel     |   Vertegenwoordigt een factuur en de gerelateerde factureringsgegevens, zoals betalingswijzen en factureringsadres. Bevat standaard een of meer factuursecties. |
|Factuursectie     |   Verwijst naar een kostengroepering op een factuur. Azure-abonnementen en andere aankopen, zoals Azure Marketplace en app-bronproducten, zijn gekoppeld aan dit bereik.    |

### <a name="microsoft-partner-agreement"></a>Microsoft Partner-overeenkomst

![Schermopname van de MPA-hiërarchie](./media/view-all-accounts/mpa-hierarchy.png)

|Bereik  |Taken  |
|---------|---------|
|Factureringsaccount     |   Vertegenwoordigt een partnerovereenkomst voor het beheren van Microsoft-producten en -services van klanten in de nieuwe Commerce-versie. Bevat een of meer factureringsprofielen en klanten.   |
|Factureringsprofiel     |   Vertegenwoordigt een factuur voor een valuta.     |
|Klant    |   Vertegenwoordigt een klant voor een CSP-partner (Cloud Solution Provider).  Azure-abonnementen en andere aankopen, zoals Azure Marketplace en app-bronproducten, zijn gekoppeld aan dit bereik.  |
|Reseller    |   Reseller die services biedt aan een klant. Een optioneel veld voor een abonnement dat alleen van toepassing is op indirecte providers in het CSP-model met twee lagen.     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>Schakelen tussen factureringsbereiken in de Azure-portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Zoek naar **Kostenbeheer en facturering**.

   ![Schermopname van zoekopdracht in Azure Portal naar kostenbeheer en facturering.](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. Selecteer op de overzichtspagina de optie **Schakelen tussen bereiken**.

   ![Schermopname van factureringsbereiken.](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > U ziet Schakelen tussen bereiken niet als u toegang hebt tot slechts één bereik.

4. Selecteer een bereik om meer details weer te geven.

   ![Schermopname van factureringsbereiken, die u kunt selecteren voor meer details.](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>Controleer uw accounttype
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [analyseren van uw kosten](../costs/quick-acm-cost-analysis.md).