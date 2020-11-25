---
title: Uw aangepaste domein Azure Active Directory toevoegen | Microsoft Docs
description: Instructies voor het toevoegen van een aangepast domein met behulp van Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28847ece3c08e93d14d381b35fc93fb16dad95a1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973895"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Uw aangepaste domein naam toevoegen met behulp van de Azure Active Directory Portal

Elke nieuwe Azure AD-Tenant wordt geleverd met een initiële domein naam, *\<domainname> . onmicrosoft.com*. U kunt de initiële domein naam niet wijzigen of verwijderen, maar de namen van uw organisatie toevoegen. Door aangepaste domein namen toe te voegen, kunt u gebruikers namen maken die bekend zijn bij uw gebruikers, zoals *alain \@ contoso.com*.

## <a name="before-you-begin"></a>Voordat u begint

Voordat u een aangepaste domein naam kunt toevoegen, maakt u uw domein naam met een domein registratie service. Zie voor een geaccrediteerd domein registratie [ICANN-accreditatie registraties](https://www.icann.org/registrar-reports/accredited-list.html).

## <a name="create-your-directory-in-azure-ad"></a>Uw Directory maken in azure AD

Nadat u uw domein naam hebt opgehaald, kunt u uw eerste Azure AD-Directory maken. Meld u aan bij de Azure Portal voor uw directory met een account met de rol **eigenaar** voor het abonnement.

Maak een nieuwe map door de stappen in [een nieuwe Tenant voor uw organisatie maken](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization)te volgen.

>[!IMPORTANT]
>De persoon die de Tenant maakt, wordt automatisch de globale beheerder voor die Tenant. De globale beheerder kan extra beheerders toevoegen aan de Tenant.

Zie [Azure roles](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)(Engelstalig) voor meer informatie over abonnements rollen.

>[!TIP]
> Als u van plan bent om uw on-premises Windows Server AD te hosten met Azure AD, moet u de optie **Ik wil dit domein configureren voor eenmalige aanmelding met mijn lokale Active Directory** wanneer u het Azure AD Connect-hulp programma uitvoert om uw mappen te synchroniseren.
>
> Daarnaast moet u dezelfde domeinnaam registreren die u selecteert voor het federeren met uw on-premises directory in de stap **Azure AD-domein** in de wizard. Zie [controleren of het Azure AD-domein is geselecteerd voor Federatie](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation)om te zien wat de installatie lijkt te zijn. Als u het hulp programma Azure AD Connect niet hebt, kunt u [het hier downloaden](https://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Uw aangepaste domein naam toevoegen aan Azure AD

Nadat u uw adres lijst hebt gemaakt, kunt u uw aangepaste domein naam toevoegen.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met het account van een globale administrator voor de map.

1. Zoek en selecteer *Azure Active Directory* op elke pagina. Selecteer vervolgens **aangepaste domein namen**  >  **aangepaste domein toevoegen**.

    ![Pagina aangepaste domein namen, met weer gegeven aangepast domein toevoegen](media/add-custom-domain/add-custom-domain.png)

1. Voer in **aangepaste domein naam** de nieuwe naam van uw organisatie in, in dit voor beeld *contoso.com*. Selecteer **Domein toevoegen**.

    ![Pagina aangepaste domein namen, met de pagina aangepaste domein toevoegen](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >U moet *. com*, *.net* of een andere extensie op het hoogste niveau gebruiken om dit goed te laten werken.

    Het niet-geverifieerde domein is toegevoegd. De pagina **contoso.com** wordt weer gegeven met uw DNS-gegevens. Sla deze informatie op. U hebt deze later nodig om een TXT-record te maken om DNS te configureren.

    ![Contoso-pagina met informatie over DNS-vermelding](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>Uw DNS-informatie toevoegen aan de domeinregistrar

Nadat u uw aangepaste domein naam aan Azure AD hebt toegevoegd, moet u terugkeren naar uw domein registratie service en de Azure AD DNS-informatie toevoegen uit uw gekopieerde TXT-bestand. Als u deze TXT-record voor uw domein maakt, wordt het eigendom van uw domein naam gecontroleerd.

Ga terug naar uw domein registratie service en maak een nieuwe TXT-record voor uw domein op basis van de gekopieerde DNS-gegevens. Stel de TTL (time to Live) in op 3600 seconden (60 minuten) en sla de record op.

>[!IMPORTANT]
>U kunt zoveel domein namen als u wilt registreren. Elk domein krijgt echter een eigen TXT-record van Azure AD. Wees voorzichtig wanneer u de gegevens van het TXT-bestand invoert bij het domein registratie service. Als u de verkeerde of dubbele gegevens per ongeluk opgeeft, moet u wachten tot de TTL-tijd (60 minuten) voordat u het opnieuw kunt proberen.

## <a name="verify-your-custom-domain-name"></a>Uw aangepaste domein naam verifiëren

Nadat u uw aangepaste domein naam hebt geregistreerd, moet u ervoor zorgen dat deze geldig is in azure AD. De doorgifte van uw domein registratie voor Azure AD kan onmiddellijk worden uitgevoerd, maar het kan enkele dagen duren, afhankelijk van uw domein registratie.

Voer de volgende stappen uit om uw aangepaste domein naam te verifiëren:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met het account van een globale administrator voor de map.

1. Zoek en selecteer *Azure Active Directory* op een wille keurige pagina en selecteer vervolgens **aangepaste domein namen**.

1. In **aangepaste domein namen** selecteert u de naam van het aangepaste domein. In dit voor beeld selecteert u **contoso.com**.

    ![Pagina fabrikam-aangepaste domein namen, met Contoso gemarkeerd](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. Op de pagina **contoso.com** selecteert u **verifiëren** om er zeker van te zijn dat uw aangepaste domein juist is geregistreerd en geldig is voor Azure AD.

    ![Contoso-pagina met informatie over DNS-vermelding en de knop verifiëren](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

Nadat u uw aangepaste domein naam hebt geverifieerd, kunt u uw TXT-of MX-verificatie bestand verwijderen.

## <a name="common-verification-issues"></a>Veelvoorkomende verificatie problemen

Als een aangepaste domein naam niet kan worden geverifieerd met Azure AD, kunt u de volgende suggesties proberen:

- **Wacht minstens een uur en probeer het opnieuw**. DNS-records moeten zijn doorgegeven voordat Azure AD het domein kan verifiëren. Dit proces kan een uur of langer duren.

- **Controleer of de DNS-record juist is.** Ga terug naar de registratie site van de domein naam. Zorg ervoor dat de vermelding daar staat en dat deze overeenkomt met de DNS-invoer gegevens die door Azure AD worden verstrekt.

  Als u de record op de registratie site niet kunt bijwerken, deelt u de vermelding met iemand die gemachtigd is om de vermelding toe te voegen en controleert u of deze juist is.

- **Zorg ervoor dat de domein naam niet al in gebruik is in een andere map.** Een domein naam kan alleen in één Directory worden geverifieerd. Als uw domein naam momenteel in een andere Directory is geverifieerd, kan deze ook niet worden geverifieerd in de nieuwe map. Als u dit duplicatie probleem wilt oplossen, moet u de domein naam uit de oude map verwijderen. Zie [aangepaste domein namen beheren](../enterprise-users/domains-manage.md)voor meer informatie over het verwijderen van domein namen.

- **Zorg ervoor dat u geen niet-beheerde Power BI tenants hebt.** Als uw gebruikers Power BI hebben geactiveerd via self-service-aanmelding en een niet-beheerde Tenant voor uw organisatie hebben gemaakt, moet u het beheer overnemen als een interne of externe beheerder met behulp van Power shell. Zie [Een niet-beheerde directory overnemen als beheerder in Azure Active Directory](../enterprise-users/domains-admin-takeover.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Voeg nog een globale beheerder toe aan uw Directory. Zie [rollen en beheerders toewijzen](active-directory-users-assign-role-azure-portal.md)voor meer informatie.

- Gebruikers toevoegen aan uw domein. Zie [hoe gebruikers toevoegen of verwijderen](add-users-azure-active-directory.md)voor meer informatie.

- Beheer uw domein naam gegevens in azure AD. Zie [aangepaste domein namen beheren](../enterprise-users/domains-manage.md)voor meer informatie.

- Zie [uw on-premises Directory's integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)als u on-premises versies van Windows Server hebt die u naast Azure Active Directory wilt gebruiken.