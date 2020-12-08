---
title: Beveiliging configureren om toegang tot gegevens te verlenen-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over het configureren van beveiliging, machtigingen en het beheren van beleid voor gegevens toegang in uw Azure Time Series Insights omgeving.
ms.service: time-series-insights
services: time-series-insights
author: shipra1mishra
ms.author: shmishr
manager: dviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: 84b973dfa016b069b18fda47a4336fe952f73b3c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780855"
---
# <a name="grant-data-access-to-an-environment"></a>Gegevens toegang verlenen tot een omgeving

In dit artikel worden de twee soorten Azure Time Series Insights toegangs beleid beschreven.

## <a name="sign-in-to-azure-time-series-insights"></a>Aanmelden bij Azure Time Series Insights

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Zoek uw Azure Time Series Insights omgeving door `Time Series Insights environments` in het **zoekvak** te typen. Selecteer `Time Series Insights environments` in de zoek resultaten.
1. Selecteer uw Azure Time Series Insights omgeving in de lijst.

## <a name="grant-data-access"></a>Gegevenstoegang verlenen

Volg deze stappen om toegang tot gegevens toe te kennen voor een gebruikers-principal.

1. Selecteer **beleid voor gegevens toegang** en selecteer **+ toevoegen**.

    [![Een beleid voor gegevens toegang selecteren en toevoegen](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Kies **gebruiker selecteren**. Zoek naar de gebruikers naam of het e-mail adres om de gebruiker te zoeken die u wilt toevoegen. Selecteer **selecteren** om de selectie te bevestigen.

    [![Selecteer een toe te voegen gebruiker](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Kies **rol selecteren**. Kies de juiste Access-rol voor de gebruiker:

    * Selecteer **Inzender** als u wilt toestaan dat de gebruiker referentie gegevens kan wijzigen en opgeslagen query's en perspectieven kan delen met andere gebruikers van de omgeving.

    * Als dat niet het geval is, selecteert u **lezer** om de gebruiker in staat te stellen gegevens in de omgeving op te vragen en persoonlijke, niet-gedeelde query's in de omgeving op te slaan.

   Selecteer **OK** om de gewenste rol te bevestigen.

    [![De geselecteerde rol bevestigen](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. Selecteer **OK** op de pagina **gebruikersrol selecteren** .

    [![Selecteer OK op de pagina gebruikersrol selecteren](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Controleer of de pagina **Data Access policies** de gebruikers en de rollen voor elke gebruiker bevat.

    [![De juiste gebruikers en rollen controleren](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Gast toegang bieden vanuit een andere Azure AD-Tenant

De `Guest` rol is geen beheer functie. Het is een term die wordt gebruikt voor een account dat van de ene Tenant naar de andere wordt uitgenodigd. Nadat het gast account is uitgenodigd voor de directory van de Tenant, kan dit hetzelfde toegangs beheer hebben als een ander account. U kunt beheer toegang tot een Azure Time Series Insights omgeving verlenen met behulp van de Blade Access Control (IAM). U kunt ook toegang tot de gegevens in de omgeving verlenen via de Blade Data Access-beleids regels. Lees [Azure Active Directory B2B-samenwerkings gebruikers toevoegen in de Azure Portal](../active-directory/external-identities/add-users-administrator.md)voor meer informatie over Azure Active Directory (Azure AD).

Volg deze stappen om gast toegang tot een Azure Time Series Insights omgeving toe te kennen aan een Azure AD-gebruiker vanuit een andere Tenant.

1. Ga naar Azure Portal, klik op  **Azure Active Directory**, Schuif omlaag op het tabblad **overzicht** en selecteer vervolgens **gast gebruiker**.

    [![Selecteer gegevens toegangs beleid en klik vervolgens op uitnodigen](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Voer het e-mail adres in voor de gebruiker die u wilt uitnodigen. Dit e-mail adres moet worden gekoppeld aan Azure AD. U kunt desgewenst een persoonlijk bericht toevoegen aan de uitnodiging.

    [![Voer het e-mail adres in om de geselecteerde gebruiker te zoeken](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Zoek naar de bevestigings ballon die op het scherm wordt weer gegeven. U kunt ook op **meldingen** klikken om te bevestigen dat de gast gebruiker is toegevoegd.

    [![Zoek naar de bevestigings ballon die moet worden weer gegeven](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Ga terug naar uw Time Series Insights-omgeving om de zojuist gemaakte gast gebruiker toe te voegen. Klik op **Data Access policies** zoals beschreven onder **toegang tot gegevens verlenen**. **Selecteer gebruiker**. Zoek het e-mail adres van de gast gebruiker die u hebt uitgenodigd voor het zoeken van de gebruiker die u wilt toevoegen. **Selecteer** vervolgens de optie om de selectie te bevestigen.

    [![Selecteer de gebruiker en bevestig de selectie](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Kies **rol selecteren**. Kies de juiste Access-rol voor de gast gebruiker:

    * Selecteer **Inzender** als u wilt toestaan dat de gebruiker referentie gegevens kan wijzigen en opgeslagen query's en perspectieven kan delen met andere gebruikers van de omgeving.

    * Als dat niet het geval is, selecteert u **lezer** om de gebruiker in staat te stellen gegevens in de omgeving op te vragen en persoonlijke, niet-gedeelde query's in de omgeving op te slaan.

   Selecteer **OK** om de gewenste rol te bevestigen.

    [![De gewenste rol bevestigen](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. Selecteer **OK** op de pagina **gebruikersrol selecteren** .

1. Controleer of de pagina **Data Access policies** de gast gebruiker en de rollen voor elke gast gebruiker bevat.

    [![Controleren of gebruikers en rollen correct zijn toegewezen](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. De gast gebruiker ontvangt nu een uitnodigings-e-mail op het e-mail adres dat hierboven is opgegeven. De gast gebruiker selecteert aan de **slag** om de acceptatie te bevestigen en verbinding te maken met de Azure-Cloud.

    [![Gast selecteert aan de slag om te accepteren](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. Nadat u **aan de slag** hebt geselecteerd, wordt de gast gebruiker weer gegeven met een machtigings venster dat is gekoppeld aan de organisatie van de beheerder. Wanneer u toestemming verleent door **accepteren** te selecteren, wordt de gebruiker aangemeld.

    [![Machtigingen voor gast beoordelingen en accepteren](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. De beheerder [deelt de omgevings-URL](time-series-insights-parameterized-urls.md) met hun gast.

1. Nadat de gast gebruiker is aangemeld bij het e-mail adres dat u hebt gebruikt om ze te nodigen, worden ze omgeleid naar Azure Portal.

1. De gast heeft nu toegang tot de gedeelde omgeving met behulp van de omgevings-URL die door de beheerder is ingesteld. Ze kunnen deze URL in hun webbrowser invoeren voor directe toegang.

1. De Tenant van de beheerder wordt weer gegeven aan de gast gebruiker nadat het profiel pictogram is geselecteerd in de rechter bovenhoek van de time series Explorer.

    [![Avatar selecteren op insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)

    Nadat de gast gebruiker de Tenant van de beheerder heeft geselecteerd, kunnen ze de gedeelde Azure Time Series Insights omgeving selecteren.

    Ze hebben nu alle mogelijkheden die zijn gekoppeld aan de rol die u hebt gegeven in **stap 5**.

    [![Gast gebruiker selecteert uw Azure-Tenant in de vervolg keuzelijst](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

* Lees [verificatie en autorisatie](time-series-insights-authentication-and-authorization.md) voor de stappen voor het registreren van Azure Active Directory-apps.

* Bekijk [uw omgeving in de Azure time series Insights Explorer](./concepts-ux-panels.md).
