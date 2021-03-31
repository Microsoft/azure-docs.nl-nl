---
title: Meldingen en e-mail sjablonen configureren
titleSuffix: Azure API Management
description: Meer informatie over het configureren van meldingen en e-mail sjablonen in azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 786a9e26003a7afb98307e0bd7fae94c42a2f00d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84690299"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Meldingen en e-mailsjablonen configureren in Azure API Management

API Management biedt de mogelijkheid om meldingen te configureren voor specifieke gebeurtenissen en om de e-mail sjablonen te configureren die worden gebruikt om te communiceren met de beheerders en ontwikkel aars van een API Management-exemplaar. In dit artikel wordt beschreven hoe u meldingen kunt configureren voor de beschik bare gebeurtenissen en vindt u een overzicht van het configureren van de e-mail sjablonen die worden gebruikt voor deze gebeurtenissen.

## <a name="prerequisites"></a>Vereisten

Als u geen API Management service-exemplaar hebt, voltooit u de volgende Snelstartgids: [een Azure API Management-exemplaar maken](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-notifications"></a><a name="publisher-notifications"> </a>Meldingen configureren

1.  Selecteer uw exemplaar van **API Management** .
2.  Klik op **meldingen** om de beschik bare meldingen weer te geven.

    ![Meldingen van de uitgever][api-management-publisher-notifications]

    De volgende lijst met gebeurtenissen kan worden geconfigureerd voor meldingen.

    -   **Abonnements aanvragen (goed keuring vereist)** : de opgegeven e-mail ontvangers en gebruikers ontvangen e-mail meldingen over abonnements aanvragen voor API-producten waarvoor goed keuring is vereist.
    -   **Nieuwe abonnementen** : de opgegeven e-mail ontvangers en gebruikers ontvangen e-mail meldingen over nieuwe API-product abonnementen.
    -   **Application Gallery-aanvragen** : de opgegeven e-mail ontvangers en gebruikers ontvangen e-mail meldingen wanneer er nieuwe toepassingen worden verzonden naar de toepassings galerie.
    -   **BCC** -de opgegeven e-mail ontvangers en gebruikers ontvangen e-mail blind carbon kopieën van alle e-mails die naar ontwikkel aars worden verzonden.
    -   **Nieuw probleem of opmerking** : de ontvangers van het e-mail bericht en gebruikers ontvangen e-mail meldingen wanneer een nieuw probleem of een opmerking wordt verzonden op de ontwikkelaars Portal.
    -   **Account bericht sluiten** : de opgegeven e-mail ontvangers en gebruikers ontvangen e-mail meldingen wanneer een account wordt gesloten.
    -   **Quota limiet voor abonnementen nadert** : de volgende e-mail ontvangers en gebruikers ontvangen e-mail meldingen wanneer het abonnements gebruik bijna de gebruiks quota wordt bereikt.

        > [!NOTE]
        > Meldingen worden alleen geactiveerd door het [quotum beleid voor abonnementen](api-management-access-restriction-policies.md#SetUsageQuota) . [Quota per sleutel](api-management-access-restriction-policies.md#SetUsageQuotaByKey) beleid genereert geen meldingen.

    Voor elke gebeurtenis kunt u e-mail ontvangers opgeven met behulp van het tekstvak e-mail adres of u kunt gebruikers selecteren in een lijst.

3.  Geef de e-mail adressen op die moeten worden gewaarschuwd in het tekstvak e-mail adres. Als u meerdere e-mail adressen hebt, kunt u deze scheiden met komma's.

    ![Ontvangers van meldingen][api-management-email-addresses]

4.  Klik op **Toevoegen**.

## <a name="configure-notification-templates"></a><a name="email-templates"> </a>Meldings sjablonen configureren

API Management biedt meldings sjablonen voor de e-mail berichten die worden verzonden in de cursus van het beheer en het gebruik van de service. De volgende e-mail sjablonen worden gegeven.

-   Inzending van de toepassings galerie goedgekeurd
-   Farewell-brief voor ontwikkel aars
-   Melding voor de quota limiet voor ontwikkel aars
-   Gebruiker uitnodigen
-   Nieuwe opmerking toegevoegd aan een probleem
-   Nieuw probleem ontvangen
-   Nieuw abonnement geactiveerd
-   Bevestiging van verlenging van abonnement
-   Afwijzingen abonnements aanvraag
-   Ontvangen abonnements aanvraag

Deze sjablonen kunnen naar wens worden gewijzigd.

Klik op **meldingen sjablonen** om de e-mail sjablonen voor uw API Management-exemplaar weer te geven en te configureren.

![E-mail-sjablonen][api-management-email-templates]

Elke e-mail sjabloon heeft een onderwerp in tekst zonder opmaak en een definitie van een hoofd tekst in HTML-indeling. Elk item kan naar wens worden aangepast.

![E-mail sjabloon editor][api-management-email-template]

De lijst met **para meters** bevat een lijst met para meters die worden ingevoegd in het onderwerp of de hoofd tekst. de opgegeven waarde wordt vervangen wanneer het e-mail bericht wordt verzonden. Als u een para meter wilt invoegen, plaatst u de cursor op de locatie waar u de para meter wilt plaatsen en klikt u op de pijl links van de parameter naam.

> [!NOTE]
> De para meters worden niet vervangen door werkelijke waarden bij het weer geven of verzenden van een test.

Als u de wijzigingen in de e-mail sjabloon wilt opslaan, klikt u op **Opslaan** of annuleert u de wijzigingen door op **negeren** te klikken.

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png
[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png
[configure publisher notifications]: #publisher-notifications
[configure email templates]: #email-templates
[how to create and use groups]: api-management-howto-create-groups.md
[how to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[get started with azure api management]: get-started-create-service-instance.md
[create an api management service instance]: get-started-create-service-instance.md
