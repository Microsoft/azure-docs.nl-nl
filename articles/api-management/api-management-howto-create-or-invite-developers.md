---
title: Gebruikers accounts beheren in azure API Management | Microsoft Docs
description: Meer informatie over het maken of uitnodigen van gebruikers in azure API Management. Aanvullende bronnen weer geven die moeten worden gebruikt na het maken van een ontwikkelaars account.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 92e032eb104835788f515cc7800fe5dacfa8adaa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88566128"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Gebruikersaccounts in Azure API Management beheren

In API Management zijn ontwikkel aars de gebruikers van de Api's die u beschikbaar maakt met behulp van API Management. In deze hand leiding wordt beschreven hoe u ontwikkel aars maakt en uitnodigt om de Api's en producten te gebruiken die u beschikbaar maakt met uw API Management-exemplaar. Zie de documentatie van de [gebruikers entiteit](/rest/api/apimanagement/2019-12-01/user) in de referentie [API Management rest](/rest/api/apimanagement/) voor meer informatie over het programmatisch beheren van gebruikers accounts.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Vereisten

Voltooi de taken in dit artikel: [een Azure API Management-exemplaar maken](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-new-developer"></a><a name="create-developer"> </a>Een nieuwe ontwikkelaar maken

Volg de stappen in deze sectie om een nieuwe gebruiker toe te voegen:

1. Selecteer het tabblad **gebruikers** aan de linkerkant van het scherm.
2. Druk op **+ toevoegen**.
3. Voer de juiste gegevens voor de gebruiker in.
4. Klik op **Toevoegen**.

    ![Een nieuwe gebruiker toevoegen](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Nieuw gemaakte ontwikkelaars accounts zijn standaard **actief** en zijn gekoppeld aan de groep **ontwikkel aars** . Ontwikkelaars accounts die zich in een **actieve** status bevinden, kunnen worden gebruikt voor toegang tot alle api's waarvoor ze abonnementen hebben. Zie [groepen koppelen aan ontwikkel aars][How to associate groups with developers]om de zojuist gemaakte ontwikkelaar te koppelen aan extra groepen.

## <a name="invite-a-developer"></a><a name="invite-developer"> </a>Een ontwikkelaar uitnodigen
Volg de stappen in deze sectie om een ontwikkelaar uit te nodigen:

1. Selecteer het tabblad **gebruikers** aan de linkerkant van het scherm.
2. Druk op **+ uitnodiging**.

Er wordt een bevestigings bericht weer gegeven, maar de zojuist uitgenodigde ontwikkelaar wordt pas weer gegeven in de lijst nadat de uitnodiging is geaccepteerd. 

Wanneer een ontwikkelaar wordt uitgenodigd, wordt een e-mail bericht naar de ontwikkelaar verzonden. Deze e-mail wordt gegenereerd met een sjabloon en kan worden aangepast. Zie [e-mail sjablonen configureren][Configure email templates]voor meer informatie.

Zodra de uitnodiging is geaccepteerd, wordt het account actief.

## <a name="deactivate-or-reactivate-a-developer-account"></a><a name="block-developer"></a> Een ontwikkelaars account deactiveren of opnieuw activeren

Nieuw gemaakte of uitgenodigde ontwikkelaars accounts zijn standaard **actief**. Als u een ontwikkelaars account wilt deactiveren, klikt u op **blok keren**. Als u een geblokkeerd ontwikkelaars account opnieuw wilt activeren, klikt u op **activeren**. Een geblokkeerd ontwikkelaars account heeft geen toegang tot de ontwikkelaars portal of kan geen Api's aanroepen. Als u een gebruikers account wilt verwijderen, klikt u op **verwijderen**.

Voer de volgende stappen uit om een gebruiker te blok keren.

1. Selecteer het tabblad **gebruikers** aan de linkerkant van het scherm.
2. Klik op de gebruiker die u wilt blok keren.
3. Druk op **blok**.

## <a name="reset-a-user-password"></a>Een gebruikerswachtwoord opnieuw instellen

Zie de documentatie van de gebruikers entiteit in de naslag informatie over [API Management rest API](/rest/api/apimanagement/) als u programmatisch wilt werken met gebruikers accounts. Als u een wacht woord voor een gebruikers account opnieuw wilt instellen op een specifieke waarde, kunt u de bewerking [een gebruiker bijwerken](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) gebruiken en het gewenste wacht woord opgeven.

## <a name="next-steps"></a><a name="next-steps"> </a>Volgende stappen
Zodra een ontwikkelaars account is gemaakt, kunt u deze koppelen aan rollen en zich abonneren op producten en Api's. Zie [groepen maken en gebruiken][How to create and use groups]voor meer informatie.

[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png


[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
