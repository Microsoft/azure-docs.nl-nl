---
title: Azure Migrate voorbereiden om met een ISV-hulp programma te werken/overzetten
description: In dit artikel wordt beschreven hoe u Azure Migrate voorbereidt op samen werking met een ISV-hulp programma of door te verplaatsen, en hoe u het hulp programma kunt gebruiken.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/10/2020
ms.openlocfilehash: 1716db0476169e12822b3f47f7199bf6e2c4ee92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96753770"
---
# <a name="prepare-to-work-with-an-isv-tool-or-movere"></a>Bereid u voor op het werken met een ISV-hulp programma of-overwerker

Als u een ISV- [hulp programma](migrate-services-overview.md#isv-integration) hebt toegevoegd of overwerkt naar een Azure migrate-project, moet u een paar stappen uitvoeren voordat u het hulp programma koppelt en gegevens naar Azure migrate verzendt. 

## <a name="check-azure-ad-permissions"></a>Azure AD-machtigingen controleren

Uw Azure-gebruikers account heeft de volgende machtigingen nodig:

- Machtiging voor het registreren van een Azure Active Directory-app (Azure AD) bij uw Azure-Tenant
- Machtiging voor het toewijzen van een rol aan de Azure AD-app op abonnements niveau


### <a name="set-permissions-to-register-an-azure-ad-app"></a>Machtigingen instellen voor het registreren van een Azure AD-app

1. Controleer in azure AD de rol voor uw account.
2. Als u de gebruikersrol hebt, selecteert u **gebruikers instellingen** aan de linkerkant en controleert u of gebruikers toepassingen kunnen registreren. Als deze is ingesteld op **Ja**, kunnen gebruikers in de Azure AD-Tenant een app registreren. Als de instelling is ingesteld op **Nee**, kunnen alleen gebruikers met beheerders rechten apps registreren.   
3. Als u geen machtigingen hebt, kan een gebruiker met beheerders rechten uw gebruikers account met de rol [toepassings beheerder](../active-directory/roles/permissions-reference.md#application-administrator) opgeven, zodat u de app kunt registreren.
4. Wanneer het hulp programma is gekoppeld aan Azure Migrate, kan de beheerder de rol verwijderen uit uw account.

### <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Machtigingen instellen om een rol toe te wijzen aan een Azure AD-app
 
Uw account heeft in uw Azure-abonnement **micro soft. Authorization/*/write** Access nodig om een rol toe te wijzen aan een Azure AD-app. 

1. Open in Azure Portal **Abonnementen**.
2. Selecteer het betreffende abonnement. Als u deze niet ziet, selecteert u het **Filter globale abonnementen**. 
3. Selecteer **Mijn machtigingen**. Selecteer vervolgens **Klik hier om de volledige toegangs gegevens voor dit abonnement weer te geven**.
4.   >  Controleer in de **weer gave** roltoewijzingen de machtigingen. Als uw account geen machtigingen heeft, vraagt u de abonnements beheerder om u toe te voegen aan de rol van [beheerder voor gebruikers toegang](../role-based-access-control/built-in-roles.md#user-access-administrator) of de rol [eigenaar](../role-based-access-control/built-in-roles.md#owner) .

## <a name="allow-access-to-urls"></a>Toegang tot URL's toestaan

Toegang tot de open bare Cloud-Url's in de tabel toestaan voor ISV-hulpprogram ma's en Azure data base Migration Assistant. Als u een op URL gebaseerde proxy gebruikt om verbinding te maken met internet, moet u ervoor zorgen dat de proxy alle CNAME-records verhelpt die zijn ontvangen tijdens het opzoeken van de Url's. 

**URL** | **Details**
--- | ---
*.portal.azure.com  | Navigeer naar de Azure Portal. 
*.windows.net<br/> *.msftauth.net<br/> *.msauth.net <br/> *.microsoft.com<br/> *. live.com   | Meld u aan bij uw Azure-abonnement. 
*.microsoftonline.com<br/> *.microsoftonline-p.com | Maak Azure Active Directory (AD)-apps voor het apparaat om te communiceren met Azure Migrate. 
management.azure.com | Azure Resource Manager aanroepen naar het Azure Migrate-project maken.
*.servicebus.windows.net | Communicatie tussen het apparaat en de EventHub voor het verzenden van de berichten.


## <a name="start-using-the-tool"></a>Beginnen met het gebruik van het hulp programma

1. Als u nog geen licentie of gratis proef versie voor het hulp programma hebt, selecteert u in het onderdeel **registreren** in azure migrate **meer informatie**.
2. Volg de instructies in het hulp programma om een koppeling te maken van het hulp programma naar het Azure Migrate project en om gegevens te verzenden naar Azure Migrate.

## <a name="next-steps"></a>Volgende stappen

Volg de instructies van uw ISV of door Voer om gegevens te verzenden naar Azure Migrate.

   
