---
title: Audit logboek rapport weer geven voor Azure AD-rollen in azure AD PIM | Microsoft Docs
description: Meer informatie over het weer geven van de controle logboek geschiedenis voor Azure AD-rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/07/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8481c562ecbab1f26e877e55a5a9454695ddf4c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92370828"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>De controle geschiedenis voor Azure AD-rollen in Privileged Identity Management weer geven

U kunt de controle geschiedenis van de Privileged Identity Management (PIM) gebruiken om alle roltoewijzingen en activeringen in de afgelopen 30 dagen voor alle geprivilegieerde rollen weer te geven. Als u de volledige controle geschiedenis van de activiteiten in uw Azure Active Directory-organisatie (Azure AD) wilt zien, met inbegrip van beheerder, eind gebruiker en synchronisatie activiteit, kunt u de [Azure Active Directory beveiligings-en activiteiten rapporten](../reports-monitoring/overview-reports.md)gebruiken.

## <a name="determine-your-version-of-pim"></a>Uw versie van PIM bepalen

Vanaf november 2019 wordt het gedeelte van de Azure AD-functies van Privileged Identity Management bijgewerkt naar een nieuwe versie die overeenkomt met de ervaringen van Azure-resource rollen. Hiermee maakt u aanvullende functies en [wijzigingen in de bestaande API](azure-ad-roles-features.md#api-changes). Terwijl de nieuwe versie wordt geïmplementeerd, zijn de procedures die u in dit artikel volgt, afhankelijk van de versie van Privileged Identity Management die u momenteel hebt. Volg de stappen in deze sectie om te bepalen welke versie van Privileged Identity Management u hebt. Nadat u uw versie van Privileged Identity Management weet, kunt u de procedures in dit artikel selecteren die overeenkomen met die versie.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een gebruiker die zich in de beheerdersrol met [geprivilegieerde rol](../roles/permissions-reference.md#privileged-role-administrator) bevindt.
1. Open **Azure AD privileged Identity Management**. Als u een banner aan de bovenkant van de overzichts pagina hebt, volgt u de instructies op het tabblad **nieuwe versie** van dit artikel. Als dat niet het geval is, volgt u de instructies op het tabblad **vorige versie** .

    [![Scherm opname van de pagina ' Azure AD-rollen-Directory rollen van de controle lijst '.](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Selecteer het tabblad voor uw versie")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

# <a name="new-version"></a>[Nieuwe versie](#tab/new)

Volg deze stappen om de controle geschiedenis voor Azure AD-rollen weer te geven.

## <a name="view-resource-audit-history"></a>Controle geschiedenis van resources weer geven

Resource controle geeft u een overzicht van alle activiteiten die zijn gekoppeld aan uw Azure AD-rollen.

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **resource controle**.

1. Filter de geschiedenis met een vooraf gedefinieerde datum of aangepast bereik.

    ![Resource audit lijst met filters](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Mijn controle weer geven

Met mijn controle kunt u uw activiteiten voor persoonlijke rollen weer geven.

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer de resource waarvoor u de controle geschiedenis wilt weer geven.

1. Selecteer **mijn audit**.

1. Filter de geschiedenis met een vooraf gedefinieerde datum of aangepast bereik.

    ![Controle lijst voor de huidige gebruiker](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-version"></a>[Vorige versie](#tab/previous)

## <a name="view-audit-history"></a>Controlegeschiedenis weergeven

Volg deze stappen om de controle geschiedenis voor Azure AD-rollen weer te geven.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) met een gebruiker die lid is van de beheerdersrol [privileged Role](../roles/permissions-reference.md#privileged-role-administrator) .

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer de **controle geschiedenis van Directory rollen**.

    Afhankelijk van de controle geschiedenis wordt een kolom diagram samen met de totale activeringen, maximum aantal activeringen per dag en gemiddeld aantal activeringen per dag weer gegeven.

    [![Nieuwe versie van Azure AD-rollen](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Controle geschiedenis van Directory rollen weer geven")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Onder aan de pagina wordt een tabel weer gegeven met informatie over elke actie in de beschik bare controle geschiedenis. De kolommen hebben de volgende betekenissen:

    | Kolom | Beschrijving |
    | --- | --- |
    | Tijd | Wanneer de actie is uitgevoerd. |
    | Requestor | Gebruiker die de functie activeert of wijzigt. Als de waarde **Azure System** is, controleert u de controle geschiedenis van Azure voor meer informatie. |
    | Bewerking | Acties die door de aanvrager worden uitgevoerd. Acties kunnen toewijzen, intrekken, activeren, deactiveren of AddedOutsidePIM bevatten. |
    | Lid | Gebruiker die een rol activeert of toewijst. |
    | Rol | De rol die is toegewezen of geactiveerd door de gebruiker. |
    | Redenen geven | Tekst die tijdens de activering is ingevoerd in het veld reden. |
    | Verloopdatum | Wanneer een geactiveerde rol verloopt. Is alleen van toepassing op in aanmerking komende roltoewijzingen. |

1. Als u de controle geschiedenis wilt sorteren, klikt u op de knoppen **tijd**, **actie** en **rol** .

## <a name="filter-audit-history"></a>Controle geschiedenis filteren

1. Klik boven aan de pagina controle geschiedenis op de knop **filteren** .

    Het deel venster **grafiek parameters bijwerken** wordt weer gegeven.

1. Selecteer een tijds bereik in **tijds bereik**.

1. In **rollen** selecteert u de selectie vakjes om de rollen aan te geven die u wilt weer geven.

    ![Deel venster grafiek parameters bijwerken](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Selecteer **gereed** om de gefilterde controle geschiedenis weer te geven.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Reden, goed keurder en ticket nummer voor goedkeurings gebeurtenissen ophalen

1. Meld u aan bij de [Azure Portal](https://aad.portal.azure.com) met privileged Role Administrators en open Azure AD.
1. Selecteer **Auditlogboeken**.
1. Gebruik het **service** filter om alleen controle gebeurtenissen voor de privileged Identity Management-service weer te geven. Op de pagina **controle logboeken** kunt u het volgende doen:

    - Zie de reden voor een controle gebeurtenis in de kolom **status reden** .
    - Zie de goed keurder in de kolom **geïnitieerd door (actor)** voor de gebeurtenis ' lid toevoegen aan een Role-aanvraag goedgekeurd '.

    [![Scherm afbeelding met de pagina controle logboeken waarop het menu gestart door (actor) open en PIM is geselecteerd.](media/pim-how-to-use-audit-log/filter-audit-logs.png "Het controle logboek voor de PIM-service filteren")](media/pim-how-to-use-audit-log/filter-audit-logs.png)

1. Selecteer een gebeurtenis in het controle logboek om het ticket nummer weer te geven op het tabblad **activiteit** van het **detail** venster.
  
    [![Scherm opname van de pagina ' controle logboeken ' met het ticket nummer dat is gemarkeerd in het deel venster Details.](media/pim-how-to-use-audit-log/audit-event-ticket-number.png "Controleer het ticket nummer voor de controle gebeurtenis")](media/pim-how-to-use-audit-log/audit-event-ticket-number.png)

1. U kunt de aanvrager bekijken (persoon die de rol activeert) op het tabblad **doelen** van het **detail** venster voor een controle gebeurtenis. Er zijn twee doel typen voor Azure AD-rollen:

    - De rol (**type** = rol)
    - De aanvrager (**type** = gebruiker)

Normaal gesp roken is de gebeurtenis audit logboek direct boven de goedkeurings gebeurtenis een gebeurtenis voor ' lid toevoegen aan rol is voltooid ' waarbij het **geïnitieerd door (actor)** de aanvrager is. In de meeste gevallen hoeft u de aanvrager niet te vinden in de goedkeurings aanvraag vanuit een controle perspectief.

---

## <a name="next-steps"></a>Volgende stappen

- [Activiteiten en controle geschiedenis voor Azure-resource rollen in Privileged Identity Management weer geven](azure-pim-resource-rbac.md)
