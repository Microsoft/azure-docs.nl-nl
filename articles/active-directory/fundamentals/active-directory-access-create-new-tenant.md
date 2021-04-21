---
title: Quickstart - Toegang verkrijgen en een nieuwe tenant maken - Azure AD
description: Instructies over het zoeken van Azure Active Directory en het maken van een nieuwe tenant voor uw organisatie.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc51c645c470f2b5b0a009eaf831db2f1957617e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780134"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Quickstart: Een nieuwe tenant maken in Azure Active Directory

U kunt al uw administratieve taken uitvoeren met behulp van de portal van Azure Active Directory (Azure AD), met inbegrip van het maken van een nieuwe tenant voor uw organisatie. 

In deze Snelstartgids leert u hoe u bij de Azure Portal en Azure Active Directory komt en hoe u een eenvoudige tenant voor uw organisatie maakt.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="create-a-new-tenant-for-your-organization"></a>Een nieuwe tenant maken voor uw organisatie

Nadat u zich bij Azure Portal aanmeldt, kunt u een nieuwe tenant maken voor uw organisatie. De nieuwe tenant vertegenwoordigt uw organisatie en helpt u bij het beheren van een specifiek exemplaar van Microsoft cloudservices voor uw interne en externe gebruikers.

### <a name="to-create-a-new-tenant"></a>Een nieuwe tenant maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) van uw organisatie.

1. In het Azure Portal-menu selecteert u **Azure Active Directory**.  

    <kbd>![Azure Active Directory - Overzichtspagina - Een tenant maken](media/active-directory-access-create-new-tenant/azure-ad-portal.png)</kbd>  

1. Selecteer **Een tenant maken**.

1. Selecteer op het tabblad Basis het type tenant dat u wilt maken, dat is tenzij **Azure Active Directory** of **Azure Active Directory (B2C)** .

1. Selecteer **Volgende: Configuratie** om naar het tabblad Configuratie te gaan.

    <kbd>![Azure Active Directory - pagina Een tenant maken - tabblad Configuratie ](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)</kbd>

1.  Geef op het tabblad Configuratie de volgende gegevens op:
    
    - Typ _Contoso-organisatie_ in het vak **Organisatienaam**.

    - Typ _Contosoorg_ in het vak **Initiële domeinnaam**.

    - Laat de optie _Verenigde Staten_ in het vak **Land of regio**.

1. Selecteer **Volgende: Beoordelen en maken**. Controleer de informatie die u hebt ingevoerd en selecteer **Maken** als de gegevens juist zijn.

    <kbd>![Azure Active Directory - pagina Tenant controleren en maken](media/active-directory-access-create-new-tenant/azure-ad-review.png)</kbd>

De nieuwe tenant wordt gemaakt met het domein contoso.onmicrosoft.com.

## <a name="your-user-account-in-the-new-tenant"></a>Uw gebruikersaccount in de nieuwe tenant

Wanneer u een nieuwe Azure AD-tenant maakt, wordt u de eerste gebruiker van die tenant. Als eerste gebruiker krijgt u automatisch de rol [Globale](https://docs.microsoft.com/azure/active-directory/roles/permissions-reference#global-administrator) beheerder toegewezen. Bekijk uw gebruikersaccount door naar de pagina [**Gebruikers te**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/MsGraphUsers) gaan.

Standaard wordt u ook vermeld als de technische [contactpersoon](https://docs.microsoft.com/microsoft-365/admin/manage/change-address-contact-and-more?view=o365-worldwide#what-do-these-fields-mean) voor de tenant. Technische contactgegevens kunt u wijzigen in [**Eigenschappen**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

> [!WARNING]
> Zorg ervoor dat aan uw directory ten minste twee accounts met globale beheerdersbevoegdheden zijn toegewezen. Dit helpt in het geval dat één globale beheerder is vergrendeld. Zie het artikel Accounts voor noodtoegang beheren [in Azure AD voor meer informatie.](../roles/security-emergency-access.md)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze app niet meer gebruikt, kunt u de tenant met de volgende stappen verwijderen:

- Zorg ervoor dat u bent aangemeld bij de map die u wilt verwijderen via het filter **Map en** abonnement in de Azure Portal. Schakel indien nodig over naar de doelmap.
- Selecteer **Azure Active Directory**, en klik vervolgens op de pagina **Contoso - Overzicht** en selecteer **Map verwijderen**.

    De tenant en de bijbehorende informatie wordt verwijderd.

    <kbd>![Overzichtspagina, waarop de knop Map verwijderen is gemarkeerd](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)</kbd>

## <a name="next-steps"></a>Volgende stappen

- Zie [Een aangepaste domeinnaam toevoegen aan Azure Active Directory](add-custom-domain.md) voor het wijzigen of toevoegen van meer informatie over extra domeinnamen

- Gebruikers toevoegen, zie [Een nieuwe gebruiker toevoegen of verwijderen](add-users-azure-active-directory.md)

- Groepen en leden toevoegen, zie [Een basisgroep maken en leden toevoegen](active-directory-groups-create-azure-portal.md)

- Meer informatie over [op rollen gebaseerde toegang met behulp van Privileged Identity Management](../../role-based-access-control/best-practices.md) en [voorwaardelijke toegang](../../role-based-access-control/conditional-access-azure-management.md) voor het beheren van de app en de toegang tot resources van uw organisatie.

- Informatie over Azure Active Directory, met inbegrip van [basisinformatie over licenties, terminologie en bijbehorende functies](active-directory-whatis.md).
