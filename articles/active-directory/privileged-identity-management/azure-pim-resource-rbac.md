---
title: Controle rapport voor Azure-resource rollen weer geven in Privileged Identity Management (PIM)-Azure AD | Microsoft Docs
description: Bekijk de activiteiten-en controle geschiedenis voor Azure-resource rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45144f64789a19390984c3f9f6a660e3c3300215
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002490"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Activiteiten en controle geschiedenis voor Azure-resource rollen in Privileged Identity Management weer geven

Met Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kunt u activiteiten, activeringen en controle geschiedenis voor Azure-resource rollen in uw organisatie weer geven. Dit omvat abonnementen, resource groepen en zelfs virtuele machines. Elke resource in de Azure Portal die gebruikmaakt van de functionaliteit voor toegangs beheer op basis van rollen van Azure kan profiteren van de mogelijkheden voor beveiliging en levenscyclus beheer in Privileged Identity Management.

> [!NOTE]
> Als uw organisatie uitbestede beheer functies heeft voor een service provider die gebruikmaakt van [Azure delegated resource management](../../lighthouse/concepts/azure-delegated-resource-management.md), worden roltoewijzingen die door die service provider worden toegestaan, hier niet weer gegeven.

## <a name="view-activity-and-activations"></a>Activiteiten en activeringen weer geven

Als u wilt zien welke acties een specifieke gebruiker heeft uitgevoerd in verschillende resources, kunt u de Azure-resource activiteit bekijken die is gekoppeld aan een bepaalde activerings periode.

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure-resources**.

1. Selecteer de resource waarvoor u activiteiten en activeringen wilt weer geven.

1. Selecteer **functies** of **leden**.

1. Selecteer een gebruiker.

    U ziet een overzicht van de acties van de gebruiker in azure-resources op datum. U ziet ook de recente functie activeringen over diezelfde periode.

    ![Gebruikers gegevens met overzicht van resource activiteiten en rollen activeringen](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Selecteer een specifieke functie activering om de details en bijbehorende Azure-resource activiteit te bekijken die zijn opgetreden terwijl de gebruiker actief was.

    [![Activering van functie geselecteerd en Details van activiteit](media/azure-pim-resource-rbac/export-membership.png "Activering van functie geselecteerd en Details van activiteit")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>Roltoewijzingen met onderliggende items exporteren

Mogelijk hebt u een nalevings vereiste waarbij u een volledige lijst met roltoewijzingen aan Audi tors moet geven. Met Privileged Identity Management kunt u een query uitvoeren op Roltoewijzingen voor een specifieke resource, die roltoewijzingen bevat voor alle onderliggende resources. Voorheen was het moeilijk voor beheerders om een volledige lijst met roltoewijzingen voor een abonnement te krijgen en ze moest roltoewijzingen voor elke specifieke resource exporteren. Met Privileged Identity Management kunt u een query uitvoeren voor alle actieve en in aanmerking komende roltoewijzingen in een abonnement, inclusief roltoewijzingen voor alle resource groepen en resources.

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure-resources**.

1. Selecteer de resource waarvoor u roltoewijzingen wilt exporteren, zoals een abonnement.

1. Selecteer **Leden**.

1. Selecteer **exporteren** om het deel venster lidmaatschap exporteren te openen.

    [![Deel venster lidmaatschap exporteren om alle leden te exporteren](media/azure-pim-resource-rbac/export-membership.png "Pagina lidmaatschap exporteren om alle leden te exporteren")](media/azure-pim-resource-rbac/export-membership.png)

1. Selecteer **alle leden exporteren** om alle roltoewijzingen in een CSV-bestand te exporteren.

    ![Geëxporteerde roltoewijzingen in een CSV-bestand als weer gave in Excel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Controle geschiedenis van resources weer geven

Resource controle geeft u een overzicht van alle functie activiteiten voor een resource.

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure-resources**.

1. Selecteer de resource waarvoor u de controle geschiedenis wilt weer geven.

1. Selecteer **resource controle**.

1. Filter de geschiedenis met een vooraf gedefinieerde datum of aangepast bereik.

    [![Resource audit lijst met filters](media/azure-pim-resource-rbac/rbac-resource-audit.png "Resource audit lijst met filters")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Selecteer **activeren (toegewezen + geactiveerd)** bij **controle type**.

    [![Resource audit lijst gefilterd op controle type activeren](media/azure-pim-resource-rbac/rbac-audit-activity.png "Resource audit lijst gefilterd op Activate")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![ Resource controle lijst die wordt gefilterd door audit type activeren](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. Klik onder **actie** op **(activiteit)** voor een gebruiker om de activiteit Details van die gebruiker in azure-resources te bekijken.

    ![Details van de gebruikers activiteit voor een bepaalde actie](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Mijn controle weer geven

Met mijn controle kunt u uw activiteiten voor persoonlijke rollen weer geven.

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure-resources**.

1. Selecteer de resource waarvoor u de controle geschiedenis wilt weer geven.

1. Selecteer **mijn audit**.

1. Filter de geschiedenis met een vooraf gedefinieerde datum of aangepast bereik.

    [![Controle lijst voor de huidige gebruiker](media/azure-pim-resource-rbac/my-audit-time.png "Controle lijst voor de huidige gebruiker")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> Voor toegang tot de controle geschiedenis is een rol van globale beheerder of beheerdersrol vereist.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Reden, goed keurder en ticket nummer voor goedkeurings gebeurtenissen ophalen

1. Meld u aan bij de [Azure Portal](https://aad.portal.azure.com) met privileged Role Administrators en open Azure AD.
1. Selecteer **Auditlogboeken**.
1. Gebruik het **service** filter om alleen controle gebeurtenissen voor de privileged Identity Management-service weer te geven. Op de pagina **controle logboeken** kunt u het volgende doen:

    - Zie de reden voor een controle gebeurtenis in de kolom **status reden** .
    - Zie de goed keurder in de kolom **geïnitieerd door (actor)** voor de gebeurtenis ' lid toevoegen aan een Role-aanvraag goedgekeurd '.

    [![Het controle logboek voor de PIM-service filteren](media/azure-pim-resource-rbac/filter-audit-logs.png "Het controle logboek voor de PIM-service filteren")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. Selecteer een gebeurtenis in het controle logboek om het ticket nummer weer te geven op het tabblad **activiteit** van het **detail** venster.
  
    [![Controleer het ticket nummer voor de controle gebeurtenis](media/azure-pim-resource-rbac/audit-event-ticket-number.png "Controleer het ticket nummer voor de controle gebeurtenis")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)]

1. U kunt de aanvrager bekijken (persoon die de rol activeert) op het tabblad **doelen** van het **detail** venster voor een controle gebeurtenis. Er zijn drie doel typen voor Azure-resource rollen:

    - De rol (**type** = rol)
    - De aanvrager (**type** = overige)
    - De goed keurder (**type** = gebruiker)

    [![Controleer het doel type](media/azure-pim-resource-rbac/audit-event-target-type.png "Controleer het doel type")](media/azure-pim-resource-rbac/audit-event-target-type.png)

Normaal gesp roken is de logboek gebeurtenis direct boven de goedkeurings gebeurtenis een gebeurtenis voor ' lid toevoegen aan rol is voltooid ' waarbij het **geïnitieerd door (actor)** de aanvrager is. In de meeste gevallen hoeft u de aanvrager niet te vinden in de goedkeurings aanvraag vanuit een controle perspectief.

## <a name="next-steps"></a>Volgende stappen

- [Azure-resource rollen toewijzen in Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Aanvragen voor Azure-resource rollen in Privileged Identity Management goed keuren of weigeren](pim-resource-roles-approval-workflow.md)
- [De controle geschiedenis voor Azure AD-rollen in Privileged Identity Management weer geven](pim-how-to-use-audit-log.md)
