---
title: Machtigingen voor de hele Tenant verlenen en aanvragen in Azure Security Center
description: Meer informatie over het beheren van machtigingen voor de hele Tenant in Azure Security Center
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: 0a24546579df020dcb7c7a9b01ee3d181226d2df
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617485"
---
# <a name="grant-and-request-tenant-wide-visibility"></a>Zicht baarheid van de hele Tenant verlenen en aanvragen

Een gebruiker met de Azure Active Directory (AD) rol van de **globale beheerder** heeft mogelijk verantwoordelijkheden voor de hele Tenant, maar heeft geen toegang tot de Azure-machtigingen voor het weer geven van de organisatie-brede informatie in azure Security Center. Machtigings verhoging is vereist omdat Azure AD-roltoewijzingen geen toegang verlenen tot Azure-resources. 

## <a name="grant-tenant-wide-permissions-to-yourself"></a>Machtigingen voor de hele Tenant verlenen

Machtigingen op Tenant niveau toewijzen:

1. Als uw organisatie toegang tot resources beheert met [Azure AD privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md)of een ander PIM-hulp programma, moet de rol globale beheerder actief zijn voor de gebruiker die volgt op de onderstaande procedure.

1. Als globale beheerder als gebruiker zonder een toewijzing in de hoofd beheer groep van de Tenant, opent u de **overzichts** pagina van Security Center en selecteert u de koppeling voor de **zicht baarheid voor de hele Tenant** in de banner. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Machtigingen op Tenant niveau inschakelen in Azure Security Center":::

1. Selecteer de nieuwe Azure-rol die u wilt toewijzen. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="Formulier voor het definiëren van machtigingen op Tenant niveau die aan uw gebruiker moeten worden toegewezen":::

    > [!TIP]
    > Over het algemeen is de rol beveiligings beheerder vereist voor het Toep assen van beleid op het hoofd niveau, terwijl beveiligings lezers voldoende zijn om zicht baarheid op Tenant niveau te bieden. Voor meer informatie over de machtigingen die door deze rollen worden verleend, raadpleegt u de beschrijving van de [ingebouwde rol beveiligings beheerder](../role-based-access-control/built-in-roles.md#security-admin) of de [ingebouwde rol van beveiligings lezer](../role-based-access-control/built-in-roles.md#security-reader).
    >
    > Zie de tabel in [rollen en toegestane acties](security-center-permissions.md#roles-and-allowed-actions)voor verschillen tussen deze rollen die specifiek zijn voor Security Center.

    De organisatie-brede weer gave wordt bereikt door rollen toe te kennen op het niveau van de hoofd beheer groep van de Tenant.  

1. Meld u af bij de Azure Portal en meld u vervolgens opnieuw aan.

1. Zodra u toegang hebt tot verhoogde bevoegdheden, opent of vernieuwt u Azure Security Center om te controleren of u zicht hebt op alle abonnementen onder uw Azure AD-Tenant. 

Het eenvoudige proces hierboven voert een aantal bewerkingen automatisch uit voor u:

1. De machtigingen van de gebruiker zijn tijdelijk verhoogd.
1. Met de nieuwe machtigingen wordt de gebruiker toegewezen aan de gewenste Azure RBAC-rol in de hoofd beheer groep.
1. De verhoogde machtigingen worden verwijderd.

Zie [toegang verhogen voor het beheer van alle Azure-abonnementen en-beheer groepen](../role-based-access-control/elevate-access-global-admin.md)voor meer informatie over het Azure AD-uitbrei ding van bevoegdheden.


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>Machtigingen voor de hele Tenant aanvragen wanneer u niet voldoende bent

Als u zich aanmeldt bij Security Center en een banner ziet dat uw weer gave beperkt is, kunt u klikken om een aanvraag naar de globale beheerder voor uw organisatie te verzenden. In de aanvraag kunt u de rol toevoegen die u wilt toewijzen en de globale beheerder neemt een beslissing over welke rol moet worden verleend. 

Het is het besluit van de globale beheerder of u deze aanvragen accepteert of weigert. 

> [!IMPORTANT]
> U kunt slechts elke zeven dagen één aanvraag indienen.

Verhoogde machtigingen aanvragen bij de globale beheerder:

1. Open Azure Security Center vanuit het Azure Portal.

1. Als de banner ' u ziet beperkte informatie ' wordt weer gegeven. Selecteer deze.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Vaandel dat een gebruiker informeert, kan de machtigingen voor de hele Tenant aanvragen.":::

1. Selecteer in het formulier voor gedetailleerde aanvragen de gewenste rol en de reden waarom u deze machtigingen nodig hebt.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-details.png" alt-text="Details pagina voor het aanvragen van Tenant machtigingen van uw globale beheerder van Azure":::

1. Selecteer **toegang aanvragen**.

    Er wordt een e-mail bericht verzonden naar de globale beheerder. Het e-mail bericht bevat een koppeling naar Security Center waar ze de aanvraag kunnen goed keuren of afwijzen.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-email.png" alt-text="Een e-mail naar de globale beheerder verzenden voor nieuwe machtigingen":::

    Nadat de globale beheerder **de aanvraag** heeft geselecteerd en het proces heeft voltooid, wordt de beslissing verzonden naar de gebruiker die het verzoek heeft ingediend. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Security Center machtigingen vindt u op de volgende gerelateerde pagina:

- [Machtigingen in Azure Security Center](security-center-permissions.md)