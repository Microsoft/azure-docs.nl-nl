---
title: Active Directory-verificatie-Azure Database for PostgreSQL-één server
description: Meer informatie over de concepten van Azure Active Directory voor verificatie met een Azure Database for PostgreSQL-enkele server
author: rothja
ms.author: jroth
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: c2265e0c134f3608830088e2a6754b6d3c88d0d6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935751"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Azure Active Directory gebruiken voor verificatie met PostgreSQL

Microsoft Azure Active Directory-verificatie (Azure AD) is een mechanisme om verbinding te maken met Azure Database for PostgreSQL met behulp van identiteiten gedefinieerd in azure AD.
Met Azure AD-verificatie kunt u gebruikers identiteiten van data bases en andere micro soft-Services beheren op een centrale locatie, waardoor het beheer van machtigingen wordt vereenvoudigd.

Voor delen van het gebruik van Azure AD zijn:

- Verificatie van gebruikers op een uniforme manier door Azure-Services
- Beheer van wachtwoord beleid en het draaien van wacht woorden op één plek
- Meerdere vormen van verificatie die door Azure Active Directory worden ondersteund, waarmee de nood zaak om wacht woorden op te slaan, kan worden voor komen
- Klanten kunnen database machtigingen beheren met externe groepen (Azure AD).
- Azure AD-verificatie maakt gebruik van PostgreSQL-database rollen om identiteiten op database niveau te verifiëren
- Ondersteuning van verificatie op basis van tokens voor toepassingen die verbinding maken met Azure Database for PostgreSQL

Gebruik het volgende proces om Azure Active Directory-verificatie te configureren en gebruiken:

1. Maak en vul zo nodig Azure Active Directory met gebruikers identiteiten in.
2. U kunt eventueel de Active Directory koppelen of wijzigen die momenteel is gekoppeld aan uw Azure-abonnement.
3. Maak een Azure AD-beheerder voor de Azure Database for PostgreSQL-server.
4. Maak database gebruikers in uw data base die zijn toegewezen aan Azure AD-identiteiten.
5. Maak verbinding met uw data base door een token op te halen voor een Azure AD-identiteit en u aan te melden.

> [!NOTE]
> Zie [configureren en aanmelden met Azure AD voor Azure database for PostgreSQL](howto-configure-sign-in-aad-authentication.md)voor meer informatie over het maken en vullen van Azure AD en het configureren van Azure ad met Azure database for PostgreSQL.

## <a name="architecture"></a>Architectuur

In het volgende diagram op hoog niveau wordt een overzicht gegeven van hoe verificatie werkt met Azure AD-verificatie met Azure Database for PostgreSQL. De pijlen geven communicatiepaden aan.

![verificatie stroom][1]

## <a name="administrator-structure"></a>Beheerdersstructuur

Wanneer u Azure AD-verificatie gebruikt, zijn er twee beheerders accounts voor de PostgreSQL-server. de oorspronkelijke PostgreSQL-beheerder en de Azure AD-beheerder. Alleen de beheerder op basis van een Azure AD-account kan de eerste Azure AD-databasegebruiker in een gebruikersdatabase maken. De aanmelding van de Azure AD-beheerder kan een Azure AD-gebruiker of een Azure AD-groep zijn. Wanneer de beheerder een groeps account is, kan dit worden gebruikt door elk groepslid om meerdere Azure AD-beheerders voor de PostgreSQL-server in te scha kelen. Het gebruik van een groeps account als beheerder verbetert de beheer baarheid, zodat u groeps leden in azure AD centraal kunt toevoegen en verwijderen zonder de gebruikers of machtigingen in de PostgreSQL-server te wijzigen. Er kan maar één Azure AD-beheerder (een gebruiker of groep) tegelijk worden geconfigureerd.

![beheerdersstructuur][2]

## <a name="permissions"></a>Machtigingen

Als u nieuwe gebruikers wilt maken die kunnen worden geverifieerd met Azure AD, moet u de `azure_ad_admin` rol hebben in de-data base. Deze rol wordt toegewezen door het Azure AD-beheerders account voor een specifieke Azure Database for PostgreSQL server te configureren.

Als u een nieuwe Azure AD-database gebruiker wilt maken, moet u verbinding maken als de Azure AD-beheerder. Dit wordt uitgelegd in [configureren en aanmelden met Azure AD voor Azure database for PostgreSQL](howto-configure-sign-in-aad-authentication.md).

Een Azure AD-verificatie is alleen mogelijk als de Azure AD-beheerder is gemaakt voor Azure Database for PostgreSQL. Als de Azure Active Directory-beheerder van de server is verwijderd, kunnen bestaande Azure Active Directory gebruikers die eerder zijn gemaakt, geen verbinding meer maken met de data base met behulp van hun Azure Active Directory referenties.

## <a name="connecting-using-azure-ad-identities"></a>Verbinding maken met Azure AD-identiteiten

Azure Active Directory-verificatie ondersteunt de volgende methoden om verbinding te maken met een database met behulp van Azure AD-identiteiten:

- Azure Active Directory – wachtwoord
- Azure Active Directory – geïntegreerd
- Azure Active Directory – Universal met MFA
- Active Directory toepassings certificaten of client geheimen gebruiken
- [Beheerde identiteit](howto-connect-with-managed-identity.md)

Zodra u hebt geverifieerd op basis van de Active Directory, haalt u een token op. Dit token is uw wacht woord voor het aanmelden.

Houd er rekening mee dat beheer bewerkingen, zoals het toevoegen van nieuwe gebruikers, alleen worden ondersteund voor Azure AD-gebruikers rollen op dit moment.

> [!NOTE]
> Zie voor meer informatie over het maken van verbinding met een Active Directory-token [configureren en aanmelden met Azure AD voor Azure database for PostgreSQL](howto-configure-sign-in-aad-authentication.md).

## <a name="additional-considerations"></a>Aanvullende overwegingen

- Voor een betere beheerbaarheid raden wij u aan een toegewezen Azure AD-groep in te richten als beheerder.
- Er kan slechts één Azure AD-beheerder (een gebruiker of groep) op elk gewenst moment worden geconfigureerd voor een Azure Database for PostgreSQL-server.
- Alleen een Azure AD-beheerder voor PostgreSQL kan eerst verbinding maken met de Azure Database for PostgreSQL met behulp van een Azure Active Directory-account. De Active Directory-beheerder kan vervolgens nieuwe Azure AD-databasegebruikers configureren.
- Als een gebruiker uit Azure AD wordt verwijderd, kan die gebruiker niet langer worden geverifieerd bij Azure AD, waardoor het niet langer mogelijk is om een toegangs token voor die gebruiker te verkrijgen. In dit geval, hoewel de overeenkomende rol nog steeds in de data base aanwezig is, is het niet mogelijk om met die rol verbinding te maken met de server.
> [!NOTE]
> Aanmelding met de verwijderde Azure AD-gebruiker kan nog steeds worden uitgevoerd, totdat het token verloopt (Maxi maal 60 minuten van het uitgeven van tokens).  Als u de gebruiker ook verwijdert uit Azure Database for PostgreSQL wordt deze toegang onmiddellijk ingetrokken.
- Als de Azure AD-beheerder wordt verwijderd van de server, wordt de server niet meer gekoppeld aan een Azure AD-Tenant en daarom worden alle Azure AD-aanmeldingen voor de server uitgeschakeld. Als u een nieuwe Azure AD-beheerder toevoegt vanuit dezelfde Tenant, worden de Azure AD-aanmeldingen opnieuw ingeschakeld.
- Azure Database for PostgreSQL overeenkomt met de toegangs Azure Database for PostgreSQL tokens van de gebruiker met de unieke Azure AD-gebruikers-ID, in tegens telling tot het gebruik van de gebruikers naam. Dit betekent dat als een Azure AD-gebruiker wordt verwijderd in azure AD en een nieuwe gebruiker is gemaakt met dezelfde naam, Azure Database for PostgreSQL van mening is dat een andere gebruiker. Als een gebruiker wordt verwijderd uit Azure AD en vervolgens een nieuwe gebruiker met dezelfde naam wordt toegevoegd, kan de nieuwe gebruiker geen verbinding maken met de bestaande rol. Als u dit wilt toestaan, moet de Azure Database for PostgreSQL Azure AD-beheerder de rol ' azure_ad_user ' intrekken en vervolgens aan de gebruiker toekennen om de gebruikers-ID van Azure AD te vernieuwen.

## <a name="next-steps"></a>Volgende stappen

- Zie [configureren en aanmelden met Azure AD voor Azure database for PostgreSQL](howto-configure-sign-in-aad-authentication.md)voor meer informatie over het maken en vullen van Azure AD en het configureren van Azure ad met Azure database for PostgreSQL.
- Zie voor een overzicht van aanmeldingen, gebruikers en database rollen Azure Database for PostgreSQL [gebruikers maken in azure database for PostgreSQL-één server](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/admin-structure.png
