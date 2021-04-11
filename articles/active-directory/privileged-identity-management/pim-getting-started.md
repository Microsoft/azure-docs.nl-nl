---
title: Beginnen met het gebruik van PIM-Azure Active Directory | Microsoft Docs
description: Meer informatie over het inschakelen en aan de slag met Azure AD Privileged Identity Management (PIM) in de Azure Portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: how-to
ms.workload: identity
ms.date: 09/15/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c00824e7fb0529cd14e3d7b757d513ee739fe283
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504410"
---
# <a name="start-using-privileged-identity-management"></a>Aan de slag met Privileged Identity Management

In dit artikel wordt beschreven hoe u Privileged Identity Management (PIM) inschakelt en aan de slag gaat met het gebruik ervan.

Gebruik Privileged Identity Management (PIM) om toegang te beheren, te controleren en te controleren binnen uw Azure Active Directory Azure AD-organisatie. Met PIM kunt u zo nodig en just-in-time toegang bieden tot Azure-resources, Azure AD-resources en andere micro soft-onlineservices, zoals Microsoft 365 of Microsoft Intune.

## <a name="prerequisites"></a>Vereisten

Als u Privileged Identity Management wilt gebruiken, hebt u een van de volgende licenties nodig:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Zie [licentie vereisten voor het gebruik van privileged Identity Management](subscription-requirements.md)voor meer informatie.

> [!Note]
> Wanneer een gebruiker die actief is in een geprivilegieerde rol in een Azure AD-organisatie met een Premium P2-licentie, naar **rollen en beheerders** in azure AD gaat, en een rol selecteert (of zelfs nog bezoekers privileged Identity Management):
>
> - PIM wordt automatisch ingeschakeld voor de organisatie
> - Hun ervaring is nu dat ze een ' regel matige ' roltoewijzing of een in aanmerking komende roltoewijzing kunnen toewijzen
>
> Wanneer PIM is ingeschakeld, heeft dit geen invloed op uw organisatie, waardoor u zich geen zorgen hoeft te maken. Het biedt extra toewijzings opties zoals actief versus in aanmerking komend met de begin-en eind tijd. Met PIM kunt u ook het bereik definiëren voor roltoewijzingen die gebruikmaken van administratieve eenheden en aangepaste rollen. Als u een globale beheerder of beheerder van een bevoorrechte rol bent, kunt u beginnen met het ophalen van enkele extra e-mail berichten zoals het wekelijks samen vatting van PIM. U ziet mogelijk ook MS-PIM-Service-Principal in het controle logboek dat betrekking heeft op de roltoewijzing. Dit is een verwachte wijziging die geen gevolgen zou hebben voor uw werk stroom.

## <a name="prepare-pim-for-azure-ad-roles"></a>PIM voorbereiden voor Azure AD-rollen

Hier volgen de taken die wij u het beste kunnen voorbereiden Privileged Identity Management voor het beheren van Azure AD-rollen:

1. [Instellingen voor Azure AD-functies configureren](pim-how-to-change-default-settings.md).
1. [Geef in aanmerking komende toewijzingen](pim-how-to-add-role-to-user.md)op.
1. [Sta gebruikers in staat om hun Azure AD-rol just-in-time te activeren](pim-how-to-activate-role.md).

## <a name="prepare-pim-for-azure-roles"></a>PIM voorbereiden voor Azure-rollen

Hier volgen de taken die wij u aanraden om Privileged Identity Management voor te bereiden voor het beheren van Azure-rollen voor een abonnement:

1. [Azure-resources detecteren](pim-resource-roles-discover-resources.md)
1. [Instellingen voor Azure-functies configureren](pim-resource-roles-configure-role-settings.md).
1. [Geef in aanmerking komende toewijzingen](pim-resource-roles-assign-roles.md)op.
1. [Sta in aanmerking komende gebruikers toe hun Azure-rollen just-in-time te activeren](pim-resource-roles-activate-your-roles.md).

## <a name="navigate-to-your-tasks"></a>Navigeer naar uw taken

Zodra Privileged Identity Management is ingesteld, kunt u uw weg ontdekken.

![Navigatie venster in Privileged Identity Management taken weer geven en opties beheren](./media/pim-getting-started/pim-quickstart-tasks.png)

| Taak + beheren | Beschrijving |
| --- | --- |
| **Mijn rollen**  | Geeft een lijst weer met in aanmerking komende en actieve rollen die aan u zijn toegewezen. Hier kunt u alle in aanmerking komende toegewezen rollen activeren. |
| **Aanvragen in behandeling** | Hiermee worden de in behandeling zijnde aanvragen voor het activeren van in aanmerking komende roltoewijzingen weer gegeven. |
| **Aanvragen goedkeuren** | Geeft een lijst weer van aanvragen voor het activeren van in aanmerking komende rollen door gebruikers in uw directory die u hebt ingesteld om goed te keuren. |
| **Toegang beoordelen** | Een lijst met actieve toegangs beoordelingen die u hebt toegewezen aan volt ooien, of u nu de toegang bekijkt voor uzelf of iemand anders. |
| **Azure AD-rollen** | Geeft een dash board en instellingen weer voor bevoegde beheerdersrol voor het beheren van toewijzingen van Azure AD-rollen. Dit dashboard is uitgeschakeld voor iedereen die geen beheerder met een bevoorrechte rol is. Deze gebruikers hebben toegang tot een speciaal dashboard met de titel Mijn weergave. In het dash board van mijn weer gave wordt alleen informatie weer gegeven over de gebruiker die toegang heeft tot het dash board, niet de hele organisatie. |
| **Azure-resources** | Geeft een dash board en instellingen weer voor beheerders met verhoogde rollen voor het beheren van toewijzingen van Azure-resource rollen. Dit dashboard is uitgeschakeld voor iedereen die geen beheerder met een bevoorrechte rol is. Deze gebruikers hebben toegang tot een speciaal dashboard met de titel Mijn weergave. In het dash board van mijn weer gave wordt alleen informatie weer gegeven over de gebruiker die toegang heeft tot het dash board, niet de hele organisatie. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Een PIM-tegel toevoegen aan het dash board

Voeg een PIM-tegel toe aan uw Azure Portal dash board om het openen van Privileged Identity Management gemakkelijker te maken.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Selecteer **alle services** en zoek de **Azure AD privileged Identity Management** -service.

    ![Azure AD Privileged Identity Management in alle services](./media/pim-getting-started/pim-all-services-find.png)

1. Selecteer de Privileged Identity Management **snel starten**.

1. Selecteer **Blade vastmaken aan dash board** om de privileged Identity Management **Quick Start** -pagina aan het dash board vast te maken.

    ![Pictogram punaise om Privileged Identity Management pagina aan het dash board vast te maken](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    In het Azure-dash board ziet u een tegel zoals deze:

    ![Privileged Identity Management tegel snel starten op het dash board](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Toegang tot Azure-resources beheren in Privileged Identity Management](pim-resource-roles-discover-resources.md)
