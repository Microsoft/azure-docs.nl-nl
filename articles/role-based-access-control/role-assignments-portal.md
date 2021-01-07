---
title: Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure Portal-Azure RBAC
description: Meer informatie over het verlenen van toegang tot Azure-resources voor gebruikers, groepen, service-principals of beheerde identiteiten met behulp van de Azure Portal en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 7c58641f0039982f05be14d0f24ba89c62273d4b
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964300"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure Portal

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] In dit artikel wordt beschreven hoe u rollen toewijst met behulp van de Azure Portal.

Zie [beheerders rollen weer geven en toewijzen in azure Active Directory](../active-directory/roles/manage-roles-portal.md)als u beheerders rollen wilt toewijzen in azure Active Directory.

## <a name="prerequisites"></a>Vereisten

Om roltoewijzingen toe te voegen of te verwijderen, hebt u het volgende nodig:

- Machtigingen voor `Microsoft.Authorization/roleAssignments/write` en `Microsoft.Authorization/roleAssignments/delete`, zoals [Beheerder van gebruikerstoegang](built-in-roles.md#user-access-administrator) of [Eigenaar](built-in-roles.md#owner)

## <a name="access-control-iam"></a>Toegangsbeheer (IAM)

**Toegangs beheer (IAM)** is de pagina die u doorgaans gebruikt om rollen toe te wijzen om toegang te verlenen tot Azure-resources. Het is ook bekend als identiteits-en toegangs beheer en wordt weer gegeven op verschillende locaties in de Azure Portal. Hieronder ziet u een voor beeld van de pagina toegangs beheer (IAM) voor een abonnement.

![De pagina toegangs beheer (IAM) voor een abonnement](./media/role-assignments-portal/access-control-subscription.png)

Om de pagina toegangs beheer (IAM) het meest effectief te maken, kunt u deze stappen volgen om een rol toe te wijzen.

1. Bepaal wie toegang nodig heeft. U kunt een rol toewijzen aan een gebruiker, groep, Service-Principal of beheerde identiteit.

1. Zoek de juiste rol. Machtigingen worden samen in rollen gegroepeerd. U kunt kiezen uit een lijst met verschillende [ingebouwde rollen van Azure](built-in-roles.md) of u kunt uw eigen aangepaste rollen gebruiken.

1. Identificeer het benodigde bereik. Azure biedt vier niveaus van bereik: [beheer groep](../governance/management-groups/overview.md), abonnement, [resource groep](../azure-resource-manager/management/overview.md#resource-groups)en resource. Zie [Bereik](scope-overview.md) voor meer informatie over het bereik.

1. Voer de stappen in een van de volgende secties uit om een rol toe te wijzen.

## <a name="add-a-role-assignment"></a>Een roltoewijzing toevoegen

In azure RBAC kunt u een roltoewijzing toevoegen om toegang te verlenen aan een Azure-resource. Volg deze stappen om een rol toe te wijzen.

1. Klik in de Azure Portal op **alle services** en selecteer vervolgens het bereik waaraan u toegang wilt verlenen. U kunt bijvoorbeeld **beheer groepen**, **abonnementen**, **resource groepen** of een resource selecteren.

1. Klik op de specifieke resource voor dat bereik.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op **het tabblad roltoewijzingen om de roltoewijzingen** in dit bereik weer te geven.

    ![Het tabblad toegangs beheer (IAM) en roltoewijzingen](./media/role-assignments-portal/role-assignments.png)

1. Klik **op**  >  **toewijzing van roltoewijzing** toevoegen.

   Als u niet bent gemachtigd voor het toewijzen van rollen, is de optie Roltoewijzing toevoegen uitgeschakeld.

   ![Menu Roltoewijzing toevoegen](./media/shared/add-role-assignment-menu.png)

    Het deelvenster Roltoewijzing toevoegen wordt geopend.

   ![Deelvenster Roltoewijzing toevoegen](./media/role-assignments-portal/add-role-assignment.png)

1. Selecteer in de vervolgkeuzelijst **Rol** een rol, zoals **Inzender voor virtuele machines**.

1. Selecteer een gebruiker, groep, Service-Principal of beheerde identiteit in de **selectie** lijst. Als u de beveiligings-principal niet in de lijst ziet staan, kunt u tekst typen in het vak **Selecteren** om te zoeken naar weergavenamen, e-mailadressen en object-id's.

1. Klik op **Opslaan** om de rol toe te wijzen.

   Na enkele ogen blikken wordt de rol bij de geselecteerde scope toegewezen aan de beveiligingsprincipal.

    ![Roltoewijzing van rol toevoegen is opgeslagen](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Een gebruiker toewijzen als beheerder van een abonnement

Als u een gebruiker een beheerder van een Azure-abonnement wilt maken, wijst u de rol [Eigenaar](built-in-roles.md#owner) aan deze persoon toe voor het abonnementsbereik. De rol eigenaar geeft de gebruiker volledige toegang tot alle resources in het abonnement, met inbegrip van de machtiging om toegang tot anderen te verlenen. Deze stappen zijn hetzelfde als die voor elke andere functietoewijzing.

1. Klik in de Azure-portal op de optie **Alle services** en vervolgens op **Abonnementen**.

1. Klik op het abonnement waartoe u toegang wilt verlenen.

1. Klik op **Toegangsbeheer (IAM)** .

1. Klik op **het tabblad roltoewijzingen om de roltoewijzingen** voor dit abonnement weer te geven.

    ![Het tabblad toegangs beheer (IAM) en roltoewijzingen](./media/role-assignments-portal/role-assignments.png)

1. Klik **op**  >  **toewijzing van roltoewijzing** toevoegen.

   Als u niet bent gemachtigd voor het toewijzen van rollen, is de optie Roltoewijzing toevoegen uitgeschakeld.

   ![Het menu voor het toewijzen van rollen toevoegen voor een abonnement](./media/shared/add-role-assignment-menu.png)

    Het deelvenster Roltoewijzing toevoegen wordt geopend.

   ![Deel venster roltoewijzing voor een abonnement toevoegen](./media/role-assignments-portal/add-role-assignment.png)

1. Selecteer in de vervolgkeuzelijst **Rol** de rol **Eigenaar**.

1. Selecteer een gebruiker in de lijst **Selecteren**. Als u de gebruiker niet in de lijst ziet staan, kunt u tekst typen in het vak **Selecteren** om in de map te zoeken naar weergavenamen en e-mailadressen.

1. Klik op **Opslaan** om de rol toe te wijzen.

   Na enkele ogenblikken wordt de rol van eigenaar op abonnementsniveau toegewezen aan de gebruiker.

## <a name="add-a-role-assignment-for-a-managed-identity-preview"></a>Een roltoewijzing voor een beheerde identiteit toevoegen (preview)

U kunt roltoewijzingen voor een beheerde identiteit toevoegen met behulp van de pagina **toegangs beheer (IAM)** zoals eerder in dit artikel is beschreven. Wanneer u de pagina toegangs beheer (IAM) gebruikt, begint u met het bereik en selecteert u vervolgens de beheerde identiteit en rol. In deze sectie wordt een alternatieve manier beschreven om roltoewijzingen toe te voegen voor een beheerde identiteit. Met deze stappen begint u met de beheerde identiteit en selecteert u vervolgens het bereik en de rol.

> [!IMPORTANT]
> Als u een roltoewijzing voor een beheerde identiteit toevoegt met behulp van deze alternatieve stappen, is momenteel een preview-versie beschikbaar.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

### <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

Volg deze stappen om een rol toe te wijzen aan een door het systeem toegewezen beheerde identiteit door te beginnen met de beheerde identiteit.

1. Open een door het systeem toegewezen beheerde identiteit in het Azure Portal.

1. Klik in het linkermenu op **identiteit**.

    ![Door het systeem toegewezen beheerde identiteit](./media/shared/identity-system-assigned.png)

1. Klik onder **machtigingen** op **Azure-roltoewijzingen**.

    Als er al rollen zijn toegewezen aan de geselecteerde door het systeem toegewezen beheerde identiteit, ziet u de lijst met roltoewijzingen. Deze lijst bevat alle roltoewijzingen waarvoor u lees machtigingen hebt.

    ![Roltoewijzingen voor een door het systeem toegewezen beheerde identiteit](./media/shared/role-assignments-system-assigned.png)

1. Als u het abonnement wilt wijzigen, klikt u op de lijst met **abonnementen** .

1. Klik op **roltoewijzing toevoegen (preview)**.

1. Gebruik de vervolg keuzelijsten om de set resources te selecteren waarop de roltoewijzing van toepassing is, zoals **abonnement**, **resource groep** of resource.

    Als u geen schrijf machtigingen voor de roltoewijzing voor het geselecteerde bereik hebt, wordt een inline-bericht weer gegeven. 

1. Selecteer in de vervolgkeuzelijst **Rol** een rol, zoals **Inzender voor virtuele machines**.

   ![Deel venster roltoewijzing toevoegen voor door het systeem toegewezen beheerde identiteit](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. Klik op **Opslaan** om de rol toe te wijzen.

   Na enkele ogen blikken is de beheerde identiteit toegewezen aan de rol bij de geselecteerde scope.

### <a name="user-assigned-managed-identity"></a>Door de gebruiker toegewezen beheerde identiteit

Volg deze stappen om een rol toe te wijzen aan een door de gebruiker toegewezen beheerde identiteit door te beginnen met de beheerde identiteit.

1. Open een door de gebruiker toegewezen beheerde identiteit in het Azure Portal.

1. Klik in het linkermenu op **Azure Role Assignments**.

    Als er al rollen zijn toegewezen aan de geselecteerde door de gebruiker toegewezen beheerde identiteit, ziet u de lijst met roltoewijzingen. Deze lijst bevat alle roltoewijzingen waarvoor u lees machtigingen hebt.

    ![Roltoewijzingen voor een door de gebruiker toegewezen beheerde identiteit](./media/shared/role-assignments-user-assigned.png)

1. Als u het abonnement wilt wijzigen, klikt u op de lijst met **abonnementen** .

1. Klik op **roltoewijzing toevoegen (preview)**.

1. Gebruik de vervolg keuzelijsten om de set resources te selecteren waarop de roltoewijzing van toepassing is, zoals **abonnement**, **resource groep** of resource.

    Als u geen schrijf machtigingen voor de roltoewijzing voor het geselecteerde bereik hebt, wordt een inline-bericht weer gegeven. 

1. Selecteer in de vervolgkeuzelijst **Rol** een rol, zoals **Inzender voor virtuele machines**.

   ![Deel venster roltoewijzing toevoegen voor een door de gebruiker toegewezen beheerde identiteit](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. Klik op **Opslaan** om de rol toe te wijzen.

   Na enkele ogen blikken is de beheerde identiteit toegewezen aan de rol bij de geselecteerde scope.

## <a name="remove-a-role-assignment"></a>Roltoewijzing verwijderen

In azure RBAC kunt u de toegang van een Azure-resource verwijderen door een roltoewijzing te verwijderen. Volg deze stappen om een roltoewijzing te verwijderen.

1. Open **toegangs beheer (IAM)** in een bereik, zoals de beheer groep, het abonnement, de resource groep of de resource, waar u de toegang wilt verwijderen.

1. Klik op het tabblad **Roltoewijzingen** om alle roltoewijzingen voor dit abonnement weer te geven.

1. Schakel in de lijst met roltoewijzingen het selectievakje in naast de beveiligings-principal met de roltoewijzing die u wilt verwijderen.

   ![Roltoewijzing geselecteerd om te worden verwijderd](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Klik op **Verwijderen**.

   ![Bericht bij verwijderen van roltoewijzing](./media/role-assignments-portal/remove-role-assignment.png)

1. Klik op **Ja** om te bevestigen dat u de roltoewijzing inderdaad wilt verwijderen.

    Als u een bericht ziet dat overgenomen roltoewijzingen niet kunnen worden verwijderd, probeert u een roltoewijzing te verwijderen uit een onderliggend bereik. Open toegangs beheer (IAM) bij het bereik waaraan de rol is toegewezen en probeer het opnieuw. Een snelle manier om toegangs beheer (IAM) in het juiste bereik te openen, is door de kolom **bereik** te bekijken en op de koppeling naast **(overgenomen)** te klikken.

   ![Bericht voor toewijzing van rol verwijderen voor overgenomen roltoewijzingen](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure-roltoewijzingen vermelden met behulp van Azure Portal](role-assignments-list-portal.md)
- [Zelfstudie: Toegang tot Azure-resources verlenen aan een gebruiker met behulp van de Azure-portal](quickstart-assign-role-user-portal.md)
- [Problemen met Azure RBAC oplossen](troubleshooting.md)
- [Uw resources organiseren met Azure-beheergroepen](../governance/management-groups/overview.md)
