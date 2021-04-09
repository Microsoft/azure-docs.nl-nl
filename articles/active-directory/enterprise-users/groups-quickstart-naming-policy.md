---
title: Quickstart voor naamgevingsbeleid voor groepen - Azure Active Directory | Microsoft Docs
description: Legt uit hoe u nieuwe gebruikers toevoegt of bestaande gebruikers verwijdert in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11eec374d9f465dc68fc0d17fafc9bd3aa93b27e
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056926"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Snelstart: naambeleid voor groepen in Azure Active Directory

In deze snelstart stelt u naamgevingsbeleid in uw Azure AD-organisatie (Azure Active Directory) in voor Microsoft 365-groepen die door gebruikers zijn gemaakt. Hierdoor kunt u de groepen van uw organisatie sorteren en doorzoeken. U kunt het naambeleid onder meer gebruiken voor:

* Het communiceren van de functie van een groep, het lidmaatschap, de geografische regio of de maker van een groep.
* Het classificeren van groepen in het adresboek.
* Zorgen dat het gebruik van bepaalde woorden voor groepsnamen en -aliassen wordt geblokkeerd.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="configure-the-group-naming-policy-in-the-azure-portal"></a>Het groepsnaamgevingsbeleid configureren in Azure Portal

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com) met een gebruikersbeheerdersaccount.
1. Selecteer **Groepen** en vervolgens **Naamgevingsbeleid** om de pagina Naamgevingsbeleid te openen.

    ![open de pagina Naamgevingsbeleid in het beheercentrum](./media/groups-quickstart-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Bekijk of bewerk het naamgevingsbeleid voor voorvoegsels en achtervoegsels

1. Selecteer op de pagina **Naamgevingsbeleid** de optie **Naamgevingsbeleid voor groepen**.
1. U kunt het huidige naamgevingsbeleid voor voorvoegsels of achtervoegsels afzonderlijk weergeven of bewerken door de kenmerken of tekenreeksen te selecteren die u wilt afdwingen als onderdeel van het naamgevingsbeleid.
1. Als u een voorvoegsel of achtervoegsel uit de lijst wilt verwijderen, selecteert u het voorvoegsel of achtervoegsel en selecteert u **Verwijderen**. U kunt meerdere items tegelijkertijd verwijderen.
1. Selecteer **Opslaan** om de wijzigingen in het beleid van kracht te laten worden.

### <a name="view-or-edit-the-custom-blocked-words"></a>Bekijk of bewerk de aangepaste geblokkeerde woorden

1. Selecteer op de pagina **Naamgevingsbeleid** de optie **Geblokkeerde woorden**.

    ![lijst met geblokkeerde woorden bewerken en uploaden voor naamgevingsbeleid](./media/groups-quickstart-naming-policy/blockedwords.png)

1. De huidige lijst met aangepaste geblokkeerde woorden weergeven of bewerken door **Downloaden** te selecteren.
1. Upload de nieuwe lijst met aangepaste geblokkeerde woorden door het bestandspictogram te selecteren.
1. Selecteer **Opslaan** om de wijzigingen in het beleid van kracht te laten worden.

Dat is alles. U hebt het naambeleid ingesteld en uw aangepaste, geblokkeerde woorden toegevoegd.

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="remove-the-naming-policy-using-azure-portal"></a>Verwijder het naamgevingsbeleid met behulp van Azure Portal

1. Selecteer op de pagina **Naamgevingsbeleid** de optie **Beleid verwijderen**.
1. Nadat u de verwijdering hebt bevestigd, wordt het naamgevingsbeleid verwijderd, met inbegrip van het naamgevingsbeleid voor voorvoegsels en achtervoegsel en eventuele aangepaste geblokkeerde woorden.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u het naamgevingsbeleid kunt instellen voor uw Azure AD-organisatie via Azure Portal.

Ga door naar het volgende artikel voor meer informatie, waaronder de PowerShell cmdlets voor het naamgevingsbeleid, technische beperkingen, het toevoegen van een lijst met aangepaste geblokkeerde woorden en eindgebruikerservaringen in Microsoft 365-apps.
> [!div class="nextstepaction"]
> [Naamgevingsbeleid voor PowerShell](groups-naming-policy.md)