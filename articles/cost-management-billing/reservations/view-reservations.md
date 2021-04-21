---
title: Machtigingen voor het weergeven en beheren van Azure-reserveringen
description: Meer informatie over het weergeven en beheren van Azure-reserveringen in de Azure Portal.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: fe2f36b08f98ceb2a5f6085510b589a712ff194d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780457"
---
# <a name="permissions-to-view-and-manage-azure-reservations"></a>Machtigingen voor het weergeven en beheren van Azure-reserveringen

In dit artikel wordt uitgelegd hoe reserveringsmachtigingen werken en hoe gebruikers Azure-reserveringen in de Azure Portal.

## <a name="who-can-manage-a-reservation-by-default"></a>Wie kunnen standaard een reservering beheren?

De volgende gebruikers kunnen standaard reserveringen weergeven en beheren:

- De persoon die een reservering koopt, en de accountbeheerder van het factureringsabonnement dat is gebruikt om de reservering te kopen, worden toegevoegd aan de reserveringsorder.
- Factureringsbeheerders van een Enterprise Agreement en Microsoft-klantovereenkomst.
- Gebruikers met verhoogde toegang voor het beheren van alle Azure-abonnementen en -beheergroepen

De levenscyclus van de reservering is onafhankelijk van een Azure-abonnement, dus de reservering is geen resource onder het Azure-abonnement. In plaats daarvan is het een resource op tenantniveau met een eigen Azure RBAC-machtiging, gescheiden van abonnementen. Reserveringen nemen na de aankoop geen machtigingen over van abonnementen.

## <a name="how-billing-administrators-can-view-or-manage-reservations"></a>Hoe factureringsbeheerders reserveringen kunnen bekijken of beheren

Als u een factureringsbeheerder bent, gebruikt u de volgende stappen om alle reserveringen en reserveringstransacties te bekijken en beheren.

1. Meld u aan [bij Azure Portal](https://portal.azure.com) en navigeer **naar Cost Management + Billing**.
    - Als u een EA-beheerder bent, selecteert u in het linkermenu **Factureringsbereiken** en selecteert u er vervolgens een in de lijst met factureringsbereiken.
    - Als u een eigenaar van Microsoft-klantovereenkomst factureringsprofiel bent, selecteert u factureringsprofielen in het menu **aan de linkerkant.** Selecteer een profiel in de lijst met factureringsprofielen.
1. Selecteer producten en **services Reserveringen** in het  >  **menu aan de linkerkant.**
1. De volledige lijst met reserveringen voor uw EA-inschrijving of factureringsprofiel wordt weergegeven.
1. Factureringsbeheerders kunnen eigenaar worden van een reservering door deze te selecteren en vervolgens Toegang verlenen te **selecteren** in het venster dat wordt weergegeven.

### <a name="how-to-add-billing-administrators"></a>Factureringsbeheerders toevoegen

Een gebruiker als factureringsbeheerder toevoegen aan een Enterprise Agreement of Microsoft-klantovereenkomst:

- Voor een Enterprise Agreement voegt u gebruikers toe met de rol _Ondernemingsbeheerder_ om alle reserveringsorders weer te geven en te beheren die van toepassing zijn op de Enterprise Agreement. Ondernemingsbeheerders kunnen reserveringen weergeven en beheren in **Cost Management + Billing.**
    - Gebruikers met de _rol Ondernemingsbeheerder (alleen-lezen)_ kunnen de reservering alleen bekijken vanuit **Cost Management + Billing.** 
    - Afdelingsbeheerders en accounteigenaars kunnen reserveringen niet weergeven, _tenzij_ ze expliciet worden toegevoegd met behulp van IAM (Toegangsbeheer). Zie [Azure Enterprise-rollen beheren](../manage/understand-ea-roles.md) voor meer informatie.
- Voor een Microsoft-klantovereenkomst kunnen gebruikers met de rol Eigenaar van factureringsprofiel of de rol Inzender van factureringsprofiel alle reserveringsaankopen beheren die zijn gedaan via het factureringsprofiel. Factureringsprofiellezers en factuurbeheerders kunnen alle reserveringen weergeven waarvoor is betaald met het factureringsprofiel. Ze kunnen echter geen wijzigingen aanbrengen in reserveringen.
    Zie [Rollen en taken van een factureringsprofiel](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks) voor meer informatie.

## <a name="view-reservations-with-azure-rbac-access"></a>Reserveringen weergeven met Azure RBAC-toegang

Als u de reservering hebt aangeschaft of als u aan een reservering bent toegevoegd, gebruikt u de volgende stappen om reserveringen te bekijken en te beheren.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Alle services** > **Reservering** om de reserveringen te bekijken waartoe u toegang hebt.

## <a name="users-with-elevated-access-can-manage-all-azure-subscriptions-and-management-groups"></a>Gebruikers met verhoogde toegang kunnen alle Azure-abonnementen en -beheergroepen beheren

U kunt de toegang van een gebruiker [verhogen om alle Azure-abonnementen en -beheergroepen te beheren.](../../role-based-access-control/elevate-access-global-admin.md?toc=/azure/cost-management-billing/reservations/toc.json)

Nadat u verhoogde toegang hebt:

1. **Navigeer naar Reservering** voor  >  **alle** services om alle reserveringen in de tenant te bekijken.
1. Als u wijzigingen wilt aanbrengen in de reservering, voegt u uzelf toe als eigenaar van de reserveringsorder met behulp van Toegangsbeheer (IAM).

## <a name="give-users-azure-rbac-access-to-individual-reservations"></a>Gebruikers Azure RBAC toegang geven tot afzonderlijke reserveringen

Gebruikers met eigenaarstoegang tot de reserveringen en factureringsbeheerders kunnen toegangsbeheer delegeren voor een afzonderlijke reserveringsorder.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Alle services** > **Reservering** om de reserveringen te bekijken waartoe u toegang hebt.
1. Selecteer de reservering waarvoor u de toegang wilt delegeren aan andere gebruikers.
1. Selecteer bij Reserveringsdetails de reserveringsorder.
1. Klik op **Toegangsbeheer (IAM)** .
1. Selecteer **Roltoewijzing toevoegen** > **Rol** > **Eigenaar**. Als u beperkte toegang wilt verlenen, selecteert u een andere rol.
1. Typ het e-mailadres van de gebruiker die u als eigenaar wilt toevoegen.
1. Selecteer de gebruiker en selecteer vervolgens **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

- [Azure-reserveringen beheren](manage-reserved-vm-instance.md).