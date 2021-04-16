---
title: Gebruik van Azure-reserveringen weergeven
description: Meer informatie over het gebruik van reserveringen en details.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: 28b61781f0b22e79d10d79c1a46e757737a401cf
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568943"
---
# <a name="view-reservation-utilization-after-purchase"></a>Reserveringsgebruik na aankoop weergeven

U kunt het percentage reserveringsgebruik en de resources die de reservering hebben gebruikt, bekijken in de Azure Portal en in de Cost Management Power BI app.

## <a name="view-utilization-in-the-azure-portal-with-azure-rbac-access"></a>Gebruik in de Azure Portal met Azure RBAC-toegang

Als u het reserveringsgebruik wilt weergeven, moet u Azure RBAC-toegang tot de reservering hebben of moet u verhoogde toegang hebben om alle Azure-abonnementen en beheergroepen te beheren.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Ga naar [Reserveringen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
1. De lijst bevat alle reserveringen waarvoor u de rol Eigenaar of Lezer hebt. Voor elke reservering ziet u het laatst bekende gebruikspercentage.
1. Selecteer het gebruikspercentage om de geschiedenis en de details van het gebruik te bekijken. In de volgende video ziet u een voorbeeld.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="view-utilization-as-billing-administrator"></a>Gebruik weergeven als factureringsbeheerder

Een Enterprise Agreement (EA)-beheerder of een Microsoft-klantovereenkomst-factureringsbeheerder (MCA) kan het gebruik van **Cost Management + Billing.**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Ga naar **Cost Management + Billing**  >  **Reserveringen.**
1. Selecteer het gebruikspercentage om de geschiedenis en de details van het gebruik te bekijken.

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>Reserveringen en gebruik opvragen met behulp van API's, PowerShell en CLI

U kunt het [reserveringsgebruik op halen met](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) behulp van de API voor het gebruik van gereserveerde instanties.

## <a name="see-reservations-and-utilization-in-power-bi"></a>Reserveringen en gebruik weergeven in Power BI

Er zijn twee opties voor Power BI gebruikers:

- Azure Cost Management-connector voor Power BI Desktop: reserveringsaankoopdatum en gebruiksgegevens zijn beschikbaar in de [Azure Cost Management-connector voor Power BI Desktop](/power-bi/desktop-connect-azure-cost-management). Maak de rapporten die u wilt maken met behulp van de connector.
- Azure Cost Management Power BI App: gebruik de [Azure Cost Management Power BI-app](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) voor vooraf gemaakte rapporten die u verder kunt aanpassen.

## <a name="next-steps"></a>Volgende stappen

- [Azure-reserveringen beheren](manage-reserved-vm-instance.md).
- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](understand-reserved-instance-usage.md).
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md).
- [Inzicht in het gebruik van reserveringen voor CSP-abonnementen](/partner-center/azure-reservations).
