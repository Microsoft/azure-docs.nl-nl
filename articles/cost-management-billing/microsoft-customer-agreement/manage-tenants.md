---
title: Tenants in uw Microsoft-klantovereenkomst beheren - Azure
description: Het artikel helpt u bij het begrijpen en beheren van tenants die zijn gekoppeld aan Microsoft-klantovereenkomst factureringsrekening.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: banders
ms.reviewer: baolcsva
ms.openlocfilehash: dc34d0f12430838be29897ccc5cbeee382ecaa2b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107493079"
---
# <a name="manage-tenants-in-your-microsoft-customer-agreement-billing-account"></a>Tenants in uw Microsoft-klantovereenkomst beheren

Het artikel helpt u bij het begrijpen en beheren van tenants die zijn gekoppeld aan Microsoft-klantovereenkomst factureringsrekening. Gebruik de informatie voor het beheren van tenants, het overdragen van abonnementen en het beheren van het eigendom van facturering, terwijl u veilige toegang tot uw factureringsomgeving kunt garanderen.

## <a name="whats-a-tenant"></a>Wat is een tenant?

Een tenant is een digitale weergave van uw organisatie en is voornamelijk gekoppeld aan een domein, zoals Microsoft.com. Het is een omgeving die wordt beheerd via Azure Active Directory waarmee u gebruikersmachtigingen kunt toewijzen voor het beheren van Azure-resources en -facturering.

Elke tenant is verschillend en gescheiden van andere tenants, maar u kunt gastgebruikers van andere tenants toegang geven tot uw tenant om uw kosten bij te houden en facturering te beheren.

## <a name="how-tenants-and-subscriptions-relate-to-billing-account"></a>Hoe tenants en abonnementen zich verhouden tot de factureringsrekening

U gebruikt uw Microsoft-klantovereenkomst (factureringsrekening) om kosten bij te houden en facturering te beheren. Elk factureringsaccount heeft ten minste één factureringsprofiel. Met het factureringsprofiel kunt u uw factuur en betalingswijze beheren. Elk factureringsprofiel bevat standaard één factuursectie. U kunt meer factuursecties maken om kosten zo nodig op een gedetailleerder niveau te groeperen, bij te houden en te beheren.

- Uw factureringsaccount is gekoppeld aan één tenant. Dit betekent dat alleen gebruikers die deel uitmaken van de tenant toegang hebben tot uw factureringsrekening.
- Wanneer u een nieuw Azure-abonnement voor uw factureringsaccount maakt, wordt dit altijd gemaakt in de tenant van uw factureringsaccount. U kunt echter abonnementen verplaatsen naar andere tenants. U kunt ook bestaande abonnementen van andere tenants koppelen aan uw factureringsaccount. Hiermee kunt u de facturering centraal beheren via één tenant en tegelijkertijd resources en abonnementen in andere tenants houden op basis van uw behoeften.

In het volgende diagram ziet u hoe factureringsrekening en abonnementen worden gekoppeld aan tenants. De Contoso MCA-factureringsrekening is gekoppeld aan Tenant 1, terwijl het Contoso PAYG-account is gekoppeld aan Tenant 2. Stel dat Contoso wil betalen voor het betalen per gebruik via hun MCA-factureringsaccount. Ze kunnen een overdracht van het eigendom van facturering gebruiken om het abonnement te koppelen aan hun MCA-factureringsrekening. Het abonnement en de bijbehorende resources worden nog steeds gekoppeld aan Tenant 2, maar ze worden betaald met behulp van de MCA-factureringsrekening.

:::image type="content" source="./media/manage-tenants/billing-hierarchy-example.png" alt-text="Diagram met een voorbeeld van een factureringshiërarchie." border="false" lightbox="./media/manage-tenants/billing-hierarchy-example.png":::

## <a name="manage-subscriptions-under-multiple-tenants-in-a-single-microsoft-customer-agreement"></a>Abonnementen beheren onder meerdere tenants in één Microsoft-klantovereenkomst

Factureringseigenaren kunnen abonnementen maken wanneer ze de juiste machtigingen voor de [factureringsrekening](../manage/understand-mca-roles.md#subscription-billing-roles-and-tasks) hebben. Alle nieuwe abonnementen die zijn gemaakt onder de Microsoft-klantovereenkomst zich standaard in de Microsoft-klantovereenkomst tenant.

- U kunt abonnementen van andere tenants koppelen aan uw Microsoft-klantovereenkomst factureringsrekening. Als u het eigendom van de facturering van een abonnement neemt, verandert alleen de factureringsindeling. Dit heeft geen invloed op de servicetenator of Azure RBAC-rollen.
- Als u de eigenaar van het abonnement in de service-tenant wilt wijzigen, moet u het abonnement overdragen naar een [andere Azure Active Directory directory](../../role-based-access-control/transfer-subscription.md).

## <a name="add-guest-users-to-your-microsoft-customer-agreement-tenant"></a>Gastgebruikers toevoegen aan uw Microsoft-klantovereenkomst tenant

Gebruikers die worden toegevoegd aan uw Microsoft-klantovereenkomst facturerings-tenant, voor het beheren van factureringsverantwoordelijkheden van een andere tenant, moeten als gast worden uitgenodigd.

Als u iemand als gast wilt uitnodigen, moet de gebruiker een bestaand e-mailadres hebben met een ander domein dan uw Azure Active Directory (AD)-domein. Azure AD stuurt de gastgebruiker een e-mail met een koppeling voor verificatie.

:::image type="content" source="./media/manage-tenants/guest-invitation-email.png" alt-text="Schermopname van een voorbeeld van een e-mailuitnodiging." lightbox="./media/manage-tenants/guest-invitation-email.png" :::

Wanneer een gebruiker wordt toegevoegd aan de Microsoft-klantovereenkomst tenant, moet deze [de uitnodiging accepteren.](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)

Wanneer ze de koppeling **Uitnodiging accepteren selecteren,** wordt ze gevraagd om zich te verifiëren bij Azure.

:::image type="content" source="./media/manage-tenants/authenticate.png" alt-text="Schermopname met een prompt voor verificatie bij Azure." lightbox="./media/manage-tenants/authenticate.png" :::

Vervolgens selecteren ze **Accepteren.**

:::image type="content" source="./media/manage-tenants/accept-invitation.png" alt-text="Schermopname met een prompt om de uitnodiging te accepteren." lightbox="./media/manage-tenants/accept-invitation.png" :::

Nadat ze akkoord zijn gaan, kunnen [ze het Microsoft-klantovereenkomst weergeven onder Cost Management + Billing](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement).

:::image type="content" source="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" alt-text="Schermopname van de Microsoft-klantovereenkomst in de lijst met factureringsrekeningen." lightbox="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" :::

Autorisatie voor het uitnodigen van gastgebruikers wordt beheerd door uw Azure AD-instellingen. De waarde van de instellingen wordt weergegeven onder **Instellingen op** de **pagina Organisatierelaties.** Zorg ervoor dat de instelling is geselecteerd, anders mislukt de uitnodiging. Zie Toegangsmachtigingen voor [gastgebruikers beperken voor meer informatie.](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)

:::image type="content" source="./media/manage-tenants/external-collaboration-settings.png" alt-text="Schermopname met instellingen voor externe samenwerking." lightbox="./media/manage-tenants/external-collaboration-settings.png" :::

> [!IMPORTANT]
> Gastgebruikers krijgen toegang tot de Microsoft-klantovereenkomst tenant, die mogelijk een beveiligingsrisico vormt. Zie Meer informatie over het beperken van de standaardmachtigingen [van gastgebruikers voor meer informatie.](../../active-directory/fundamentals/users-default-permissions.md#restrict-member-users-default-permissions)

## <a name="manage-multiple-microsoft-cloud-services-under-an-azure-ad-tenant"></a>Meerdere Microsoft-cloudservices beheren onder een Azure AD-tenant

U kunt meerdere cloudservices voor uw organisatie beheren onder één Azure AD-tenant. Gebruikersaccounts voor alle cloudaanbiedingen van Microsoft worden opgeslagen in de Azure AD-tenant, die gebruikersaccounts en -groepen bevat. In het volgende diagram ziet u een voorbeeld van een organisatie met meerdere services die gebruikmaken van een algemene Azure AD-tenant met accounts. Elke service heeft een eigen portal, in blauwe tekst, waarin gebruikers hun services beheren.

:::image type="content" source="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png" alt-text="Diagram met een voorbeeld van een organisatie met meerdere services die gebruikmaken van een algemene Azure AD-tenant met accounts." border="false" lightbox="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png":::

## <a name="next-steps"></a>Volgende stappen

Lees de volgende artikelen voor meer informatie over het beheren van flexibel eigendom van facturering en veilige toegang tot uw Microsoft-klantovereenkomst.

- [Een tenant instellen](../../active-directory/develop/quickstart-create-new-tenant.md)
- [Ingebouwde Azure-rollen](../../role-based-access-control/built-in-roles.md)
- [Een Azure-abonnement overdragen naar een andere Azure AD-directory](../../role-based-access-control/transfer-subscription.md)
- [Machtigingen voor gasttoegang beperken (preview) in Azure Active Directory](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)
- [Gastgebruikers toevoegen aan uw directory in de Azure-portal](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Wat zijn de standaardgebruikersmachtigingen in Azure Active Directory?](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Wat is Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)