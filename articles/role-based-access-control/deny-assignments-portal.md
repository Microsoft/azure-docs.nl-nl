---
title: Een lijst met Azure deny-toewijzingen maken met behulp van de Azure Portal-Azure RBAC
description: Meer informatie over het weer geven van de gebruikers, groepen, service-principals en beheerde identiteiten die toegang hebben gekregen tot specifieke Azure-resource acties voor bepaalde bereiken met behulp van de Azure Portal en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 92046b3a944a747ce76d2426855eec7b6bc2cd70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84790243"
---
# <a name="list-azure-deny-assignments-using-the-azure-portal"></a>Azure deny-toewijzingen weer geven met behulp van de Azure Portal

Met [Azure deny-toewijzingen](deny-assignments.md) kunnen gebruikers specifieke Azure-resource acties uitvoeren, zelfs als een roltoewijzing deze toegang verleent. In dit artikel wordt beschreven hoe u toewijzingen voor weigeren kunt weer geven met behulp van de Azure Portal.

> [!NOTE]
> U kunt niet rechtstreeks uw eigen weigerings toewijzingen maken. Zie [Azure deny-toewijzingen](deny-assignments.md)voor meer informatie over hoe weigerings toewijzingen worden gemaakt.

## <a name="prerequisites"></a>Vereisten

Als u informatie wilt ophalen over een weiger toewijzing, hebt u het volgende nodig:

- `Microsoft.Authorization/denyAssignments/read` toestemming, die deel uitmaakt van de meeste [ingebouwde rollen van Azure](built-in-roles.md).

## <a name="list-deny-assignments"></a>Lijst met geweigerde toewijzingen weergeven

Volg deze stappen om toewijzingen weigeren te vermelden in het bereik van het abonnement of de beheer groep.

1. Klik in de Azure Portal op **alle services** en vervolgens op **beheer groepen** of **abonnementen**.

1. Klik op de beheer groep of het abonnement dat u wilt weer geven.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op het tabblad **toewijzingen weigeren** (of klik op de knop **weer geven** op de tegel toewijzingen weigeren weer geven).

    Als er een in dit bereik toewijzingen zijn geweigerd of zijn overgenomen, worden deze weergegeven.

    ![Toegangs beheer-tabblad Toewijzingen weigeren](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. Als u aanvullende kolommen wilt weer geven, klikt u op **kolommen bewerken**.

    ![Toewijzingen weigeren-kolommen](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Naam** | De naam van de weigerings toewijzing. |
    | **Type principal** | Gebruiker, groep, door het systeem gedefinieerde groep of Service-Principal. |
    | **Geweigerd**  | Naam van de beveiligingsprincipal die is opgenomen in de toewijzing weigeren. |
    | **Id** | De unieke id voor de deny-toewijzing. |
    | **Uitgesloten principals** | Of er beveiligings-principals zijn die zijn uitgesloten van de weigerings toewijzing. |
    | **Is niet van toepassing op onderliggende items** | Hiermee wordt aangegeven of de weigerings toewijzing wordt overgenomen door subbereiken. |
    | **Systeem beveiligd** | Hiermee wordt aangegeven of de weigerings toewijzing wordt beheerd door Azure. Op dit moment is altijd ja. |
    | **Scope** | Beheer groep, abonnement, resource groep of resource. |

1. Voeg een vinkje toe aan een van de ingeschakelde items en klik vervolgens op **OK** om de geselecteerde kolommen weer te geven.

## <a name="list-details-about-a-deny-assignment"></a>Details over een weiger toewijzing weer geven

Volg deze stappen om aanvullende informatie over een weiger toewijzing weer te geven.

1. Open het deel venster **toewijzingen weigeren** , zoals beschreven in de vorige sectie.

1. Klik op de naam van de toewijzing weigeren om de Blade **gebruikers** te openen.

    ![Toewijzing weigeren-gebruikers](./media/deny-assignments-portal/deny-assignment-users.png)

    De Blade **gebruikers** bevatten de volgende twee secties.

    |  |  |
    | --- | --- |
    | **Toewijzing weigeren is van toepassing op**  | Beveiligings-principals waarop de weigerings toewijzing van toepassing is. |
    | **Uitsluitingen van toewijzingen weigeren** | Beveiligings-principals die zijn uitgesloten van de weigerings toewijzing. |

    De door **het systeem gedefinieerde Principal** vertegenwoordigt alle gebruikers, groepen, service-principals en beheerde identiteiten in een Azure AD-adres lijst.

1. Klik op **geweigerde machtigingen** om een lijst weer te geven met de machtigingen die worden geweigerd.

    ![Machtigingen voor geweigerde toewijzing weigeren](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Actietype | Beschrijving |
    | --- | --- |
    | **Acties**  | Beheer bewerkingen geweigerd. |
    | **NotActions** | Beheer bewerkingen die zijn uitgesloten van een geweigerde beheer bewerking. |
    | **DataActions**  | Geweigerde gegevens bewerkingen. |
    | **NotDataActions** | Gegevens bewerkingen die zijn uitgesloten van de bewerking voor het weigeren van gegevens. |

    Voor het voor beeld in de vorige scherm afbeelding ziet u de volgende machtigingen:

    - Alle opslag bewerkingen op het gegevens vlak worden geweigerd, behalve voor reken bewerkingen.

1. Klik op **Eigenschappen** om de eigenschappen voor een weiger toewijzing weer te geven.

    ![Toewijzing weigeren-eigenschappen](./media/deny-assignments-portal/deny-assignment-properties.png)

    Op de Blade **Eigenschappen** ziet u de naam van de toewijzing weigeren, de id, de beschrijving en het bereik. De schakel optie **is niet van toepassing op onderliggende** geeft aan of de weigerings toewijzing wordt overgenomen in subbereiken. Met de door het **systeem beveiligde** switch wordt aangegeven of deze deny-toewijzing wordt beheerd door Azure. Dit is momenteel in alle gevallen **Ja** .

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure deny-toewijzingen](deny-assignments.md)
* [Azure deny-toewijzingen weer geven met Azure PowerShell](deny-assignments-powershell.md)
