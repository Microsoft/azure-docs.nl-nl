---
title: Reserveringen voor Azure-resources weergeven
description: Lees hier meer over het weergeven van Azure-reserveringen in de Azure-portal. Bekijk reserveringen en gebruik door API’s, PowerShell, CLI en Power BI te gebruiken.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 02/24/2021
ms.author: banders
ms.openlocfilehash: 477dff9db28672f8231710af34786ac387f43b71
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050338"
---
# <a name="view-azure-reservations"></a>Azure-reserveringen weergeven

In dit artikel wordt uitgelegd hoe u Azure-reserveringen kunt weergeven in de Azure-portal. U kunt een aangeschafte reservering in de Azure-portal weergeven en beheren.

## <a name="who-can-manage-a-reservation-by-default"></a>Wie kunnen standaard een reservering beheren?

De volgende gebruikers kunnen standaard reserveringen weergeven en beheren:

- De persoon die een reservering koopt, en de accountbeheerder van het factureringsabonnement dat is gebruikt om de reservering te kopen, worden toegevoegd aan de reserveringsorder.
- Factureringsbeheerders van een Enterprise Agreement en Microsoft-klantovereenkomst.

U hebt twee opties om toe te staan dat andere personen reserveringen beheren:

- Toegangsbeheer voor een afzonderlijke reserveringsorder delegeren:
    1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
    1. Selecteer **Alle services** > **Reservering** om de reserveringen te bekijken waartoe u toegang hebt.
    1. Selecteer de reservering waarvoor u de toegang wilt delegeren aan andere gebruikers.
    1. Selecteer bij Reserveringsdetails de reserveringsorder.
    1. Klik op **Toegangsbeheer (IAM)** .
    1. Selecteer **Roltoewijzing toevoegen** > **Rol** > **Eigenaar**. Als u beperkte toegang wilt verlenen, selecteert u een andere rol.
    1. Typ het e-mailadres van de gebruiker die u als eigenaar wilt toevoegen.
    1. Selecteer de gebruiker en selecteer vervolgens **Opslaan**.

- Een gebruiker als factureringsbeheerder toevoegen aan een Enterprise Agreement of Microsoft-klantovereenkomst:
    - Voor een Enterprise Agreement voegt u gebruikers toe met de rol _Ondernemingsbeheerder_ om alle reserveringsorders weer te geven en te beheren die van toepassing zijn op de Enterprise Agreement. Gebruikers met de rol _Ondernemingsbeheerder (alleen lezen)_ kunnen de reservering alleen weergeven. Afdelingsbeheerders en accounteigenaars kunnen reserveringen niet weergeven, _tenzij_ ze expliciet worden toegevoegd met behulp van IAM (Toegangsbeheer). Zie [Azure Enterprise-rollen beheren](../manage/understand-ea-roles.md) voor meer informatie.

        _Ondernemingsbeheerders kunnen eigendom van een reserveringsorder overnemen, en ze kunnen andere gebruikers toevoegen aan een reservering met behulp van IAM (Toegangsbeheer)._
    - Voor een Microsoft-klantovereenkomst kunnen gebruikers met de rol Eigenaar van factureringsprofiel of de rol Inzender van factureringsprofiel alle reserveringsaankopen beheren die zijn gedaan via het factureringsprofiel. Factureringsprofiellezers en factuurbeheerders kunnen alle reserveringen weergeven waarvoor is betaald met het factureringsprofiel. Ze kunnen echter geen wijzigingen aanbrengen in reserveringen.
    Zie [Rollen en taken van een factureringsprofiel](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks) voor meer informatie.

### <a name="how-billing-administrators-view-or-manage-reservations"></a>Hoe Factureringsbeheerders reserveringen weergeven of beheren

Als u toegang hebt tot reserve ringen of reserverings orders met toegang tot Azure RBAC, ziet u mogelijk slechts een subset van reserverings transacties of geen wanneer u naar reserve ringen navigeert. Gebruik de volgende stappen om alle reserve ringen en reserverings transacties weer te geven en te beheren.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar **Cost Management en facturering**.
    - Als u een EA-beheerder bent, selecteert u in het linkermenu **facturerings bereik** en selecteert u in de lijst met facturerings bereiken een.
    - Als u de eigenaar van het facturerings Profiel van een micro soft-klant bent, selecteert u in het menu links **facturerings profielen**. Selecteer in de lijst met facturerings profielen een.
1. Selecteer **reserverings transacties** in het linkermenu. De lijst met reserverings transacties wordt weer gegeven.
1. Een banner aan de bovenkant van de pagina leest *nu dat beheerders van de facturering reserve ringen kunnen beheren. Klik hier om de reserve ringen te beheren.* Selecteer de banner.
1. De volledige lijst met reserve ringen voor uw EA-inschrijving of facturerings profiel wordt weer gegeven.
1. Als u eigenaar van een reserve ring wilt worden, selecteert u deze. Selecteer vervolgens op de pagina Machtigingen instellen de optie **toegang verlenen**. U krijgt de eigenaar toegang tot de reserverings-en reserverings volgorde.

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>Reservering en gebruik bekijken in de Azure-portal

Een reservering weergeven als eigenaar of lezer

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Ga naar [Reserveringen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
3. De lijst bevat alle reserveringen waarvoor u de rol Eigenaar of Lezer hebt. Voor elke reservering ziet u het laatst bekende gebruikspercentage.
4. Selecteer het gebruikspercentage om de geschiedenis en de details van het gebruik te bekijken. Zie de onderstaande video voor meer informatie.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>Reserveringen en gebruik opvragen met behulp van API's, PowerShell en CLI

Een lijst met alle reserveringen opvragen met behulp van de volgende resources:

- [API: Reserveringsorder: lijst](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell: Reserveringsorder: lijst](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [CLI: Reserveringsorder: lijst](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

U kunt het [reserveringsgebruik](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) opvragen met behulp van de API voor het gebruik van gereserveerde instanties. 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Reserveringen en gebruik weergeven in Power BI

Er zijn twee opties voor Power BI-gebruikers
- Inhoudspakket: Reserveringsaankoopgegevens en gebruiksgegevens zijn beschikbaar in het [inhoudspakket Microsoft Azure Consumption Insights voor Power BI](/power-bi/desktop-connect-azure-cost-management). Maak de gewenste rapporten met behulp van dit inhoudspakket. 
- Kostenbeheer-app: Gebruik de [Kostenbeheer-app](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) voor vooraf gemaakte rapporten die u verder kunt aanpassen.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- [Azure-reserveringen beheren](manage-reserved-vm-instance.md).
- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](understand-reserved-instance-usage.md).
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md).
- [Inzicht in het gebruik van reserveringen voor CSP-abonnementen](/partner-center/azure-reservations).

