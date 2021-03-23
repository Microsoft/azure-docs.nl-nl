---
title: Azure Portal Dash boards delen met behulp van Azure op rollen gebaseerd toegangs beheer
description: In dit artikel wordt uitgelegd hoe u een dash board kunt delen in de Azure Portal met behulp van Azure op rollen gebaseerd toegangs beheer.
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 336bfe9792c5ba4246458368e008a8c50833ed03
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771534"
---
# <a name="share-azure-dashboards-by-using-azure-role-based-access-control"></a>Azure-Dash boards delen met behulp van Azure op rollen gebaseerd toegangs beheer

Nadat u een dash board hebt geconfigureerd, kunt u het publiceren en delen met andere gebruikers in uw organisatie. U kunt anderen uw dash board weer geven met behulp van [Azure RBAC (op rollen gebaseerd toegangs beheer)](../role-based-access-control/role-assignments-portal.md). Wijs één gebruiker of een groep gebruikers toe aan een rol. Deze rol bepaalt of gebruikers het gepubliceerde dash board kunnen weer geven of wijzigen.

Alle gepubliceerde Dash boards worden geïmplementeerd als Azure-resources. Ze bestaan als beheer bare items binnen uw abonnement en zijn opgenomen in een resource groep. Vanuit een perspectief voor toegangs beheer zijn Dash boards niet anders dan andere resources, zoals een virtuele machine of een opslag account. Afzonderlijke tegels op het dash board afdwingen hun eigen vereisten voor toegangs beheer op basis van de resources die ze weer geven. U kunt een dash board breed delen tijdens het beveiligen van de gegevens op afzonderlijke tegels.

## <a name="understanding-access-control-for-dashboards"></a>Meer informatie over toegangs beheer voor dash boards

Met op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure kunt u gebruikers toewijzen aan rollen op drie verschillende niveaus van bereik:

* abonnement
* resourcegroep
* resource

De machtigingen die u toewijst, nemen toe van het abonnement tot de resource. Het gepubliceerde dash board is een resource. Mogelijk hebt u al gebruikers die zijn toegewezen aan rollen voor het abonnement dat van toepassing is op het gepubliceerde dash board.

Stel dat u een Azure-abonnement hebt en dat er aan verschillende leden van uw team de rollen van *eigenaar*, *bijdrager* of *lezer* voor het abonnement zijn toegewezen. Gebruikers die eigen aars of mede werkers zijn, kunnen Dash boards in het abonnement weer geven, bekijken, maken, wijzigen of verwijderen. Gebruikers die lezers kunnen weer geven en bekijken, maar deze niet wijzigen of verwijderen. Gebruikers met een lees toegang kunnen lokale bewerkingen op een gepubliceerd dash board maken, zoals bij het oplossen van een probleem, maar ze kunnen deze wijzigingen niet naar de server publiceren. Ze kunnen zelf een persoonlijk exemplaar van het dash board maken.

U kunt machtigingen toewijzen aan de resource groep die verschillende Dash boards of aan een afzonderlijk dash board bevat. U kunt bijvoorbeeld besluiten dat een groep gebruikers beperkte machtigingen moet hebben voor het abonnement, maar wel meer toegang tot een bepaald dash board. Wijs deze gebruikers toe aan een rol voor dat dash board.

## <a name="publish-a-dashboard"></a>Een dash board publiceren

Stel dat u een dash board configureert dat u wilt delen met een groep gebruikers in uw abonnement. De volgende stappen laten zien hoe u een dash board deelt met een groep met de naam Storage-beheerders. U kunt uw groep een naam die u wilt. Zie [groepen beheren in azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)voor meer informatie.

Voordat u toegang toewijst, moet u het dash board publiceren.

1. Selecteer **delen** in het dash board.

    ![share voor uw dash board selecteren](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. Selecteer in **delen en toegang beheren** de optie **publiceren**.

    ![uw dash board publiceren](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Standaard publiceert het delen van uw dash board naar een resource groep met de naam **Dash boards**. Als u een andere resource groep wilt selecteren, schakelt u het selectie vakje uit.

Het dash board is nu gepubliceerd. Als de machtigingen die zijn overgenomen van het abonnement, geschikt zijn, hoeft u verder niets te doen. Andere gebruikers in uw organisatie kunnen het dash board openen en aanpassen op basis van de rol van de abonnements niveau.

## <a name="assign-access-to-a-dashboard"></a>Toegang tot een dash board toewijzen

U kunt een groep gebruikers toewijzen aan een rol voor dat dash board.

1. Wanneer u het dash board hebt gepubliceerd, selecteert u **delen beheren**.

1. In **Access Control** **roltoewijzingen** selecteren om bestaande gebruikers weer te geven waaraan al een rol is toegewezen voor dit dash board.

1. Selecteer **toevoegen** en **roltoewijzing toevoegen** om een nieuwe gebruiker of groep toe te voegen.

    ![een gebruiker toevoegen voor toegang tot het dash board](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. Selecteer de rol die de machtigingen vertegenwoordigt die moeten worden verleend, zoals **Inzender**.

1. Selecteer de gebruiker of groep die u aan de rol wilt toewijzen. Als de gebruiker of groep die u zoekt, niet in de lijst wordt weer geven, gebruikt u het zoekvak. De lijst met beschik bare groepen is afhankelijk van de groepen die u hebt gemaakt in Active Directory.

1. Wanneer u klaar bent met het toevoegen van gebruikers of groepen, selecteert u **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

* Zie [ingebouwde rollen in azure](../role-based-access-control/built-in-roles.md)voor een lijst met rollen.
* Zie [Azure-resources beheren met behulp van de Azure Portal](../azure-resource-manager/management/manage-resources-portal.md)voor meer informatie over het beheren van resources.