---
title: Problemen met niet-overeenkomende mappen oplossen in Azure AD Domain Services | Microsoft Docs
description: Meer informatie over wat een fout met een niet-overeenkomende map is en hoe u deze kunt oplossen in Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: ee8174114f1b892210e8ee9173ce0eb1d09c7e31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619297"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-active-directory-domain-services-managed-domains"></a>Fouten van niet-overeenkomende mappen oplossen voor bestaande Azure Active Directory Domain Services beheerde domeinen

Als een door een domein beheerd Azure Active Directory Domain Services (Azure AD DS) een niet-overeenkomende Tenant fout toont, kunt u het beheerde domein pas beheren nadat het is opgelost. Deze fout treedt op als het onderliggende virtuele netwerk van Azure wordt verplaatst naar een andere Azure AD-Directory.

In dit artikel wordt uitgelegd waarom de fout optreedt en hoe u deze kunt oplossen.

## <a name="what-causes-this-error"></a>Wat resulteert er in deze fout?

Er treedt een fout op die niet overeenkomt wanneer een door Azure AD DS beheerd domein en een virtueel netwerk deel uitmaken van twee verschillende Azure AD-tenants. U hebt bijvoorbeeld een beheerd domein met de naam *aaddscontoso.com* dat wordt uitgevoerd in de Azure AD-Tenant van contoso. Het virtuele netwerk van Azure voor het beheerde domein maakt echter deel uit van de fabrikam Azure AD-Tenant.

Toegangs beheer op basis van rollen (Azure RBAC) van Azure wordt gebruikt om de toegang tot resources te beperken. Wanneer u Azure AD DS in een Azure AD-Tenant inschakelt, worden referentie-hashes gesynchroniseerd met het beheerde domein. Deze bewerking vereist dat u een Tenant beheerder bent voor de Azure AD-adres lijst en dat toegang tot de referenties moet worden beheerd.

Als u resources wilt implementeren in een virtueel Azure-netwerk en beheer verkeer, moet u over beheerders rechten beschikken voor het virtuele netwerk waarin u het beheerde domein implementeert.

Voor een consistente werking van Azure RBAC en beveiligde toegang tot alle resources die Azure AD DS gebruikt, moeten het beheerde domein en het virtuele netwerk deel uitmaken van dezelfde Azure AD-Tenant.

De volgende regels zijn van toepassing op implementaties:

- Een Azure AD-adres lijst kan meerdere Azure-abonnementen hebben.
- Een Azure-abonnement kan meerdere resources bevatten, zoals virtuele netwerken.
- Eén beheerd domein is ingeschakeld voor een Azure AD-Directory.
- Een beheerd domein kan worden ingeschakeld op een virtueel netwerk dat deel uitmaakt van een van de Azure-abonnementen binnen dezelfde Azure AD-Tenant.

### <a name="valid-configuration"></a>Geldige configuratie

In het volgende voor beeld van een implementatie scenario wordt het beheerde domein contoso ingeschakeld in de contoso Azure AD-Tenant. Het beheerde domein wordt geïmplementeerd in een virtueel netwerk dat hoort bij een Azure-abonnement dat eigendom is van de contoso Azure AD-Tenant.

Zowel het beheerde domein als het virtuele netwerk behoren tot dezelfde Azure AD-Tenant. Deze voorbeeld configuratie is geldig en volledig ondersteund.

![Geldige Tenant configuratie van Azure AD DS met het beheerde domein en het virtuele netwerk deel van dezelfde Azure AD-Tenant](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Niet-overeenkomende Tenant configuratie

In dit voor beeld van een implementatie scenario wordt het beheerde domein contoso ingeschakeld in de contoso Azure AD-Tenant. Het beheerde domein wordt echter geïmplementeerd in een virtueel netwerk dat hoort bij een Azure-abonnement dat eigendom is van de fabrikam Azure AD-Tenant.

Het beheerde domein en het virtuele netwerk behoren tot twee verschillende Azure AD-tenants. Deze voorbeeld configuratie is een niet-overeenkomende Tenant en wordt niet ondersteund. Het virtuele netwerk moet worden verplaatst naar dezelfde Azure AD-Tenant als het beheerde domein.

![Niet-overeenkomende Tenant configuratie](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Niet-overeenkomende Tenant fout oplossen

Met de volgende twee opties wordt de fout met niet-overeenkomende mappen opgelost:

* Verwijder eerst [het beheerde domein](delete-aadds.md) uit uw bestaande Azure AD-adres lijst. Maak vervolgens [een vervangend beheerd domein](tutorial-create-instance.md) in dezelfde Azure AD-Directory als het virtuele netwerk dat u wilt gebruiken. Als u klaar bent, moet u alle computers die eerder zijn toegevoegd aan het verwijderde domein, toevoegen aan het opnieuw gemaakte beheerde domein.
* [Verplaats het Azure-abonnement](../cost-management-billing/manage/billing-subscription-transfer.md) met het virtuele netwerk naar dezelfde Azure AD-Directory als het beheerde domein.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het oplossen van problemen met Azure AD DS raadpleegt u de [hand leiding](troubleshoot.md)voor het oplossen van problemen.
