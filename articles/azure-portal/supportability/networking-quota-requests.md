---
title: Netwerklimiet verhoogd
description: Netwerklimiet verhoogd
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e945dec2ce8514c3e4f1edecdecd13c5c43f7c75
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745448"
---
# <a name="networking-limit-increase"></a>Netwerklimiet verhoogd

Gebruik de [Azure Portal](https://portal.azure.com) om uw netwerk quotum te verg Roten.

Als u uw huidige netwerk gebruik en-quotum in Azure Portal wilt weer geven, opent u uw abonnement en selecteert u vervolgens **gebruik en quota's**. U kunt ook de volgende opties gebruiken om uw netwerk gebruik en-limieten te bekijken.

* [Gebruik CLI](/cli/azure/network#az-network-list-usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [De API voor netwerk gebruik](/rest/api/virtualnetwork/virtualnetworks/listusage)

U kunt een verhoging aanvragen met **behulp van Help + ondersteuning** of in **gebruik + quota's** in de portal.

> [!Note]
> Als u de standaard grootte van **open bare IP-voor voegsels** wilt wijzigen, selecteert u **min. duur openbaar IP-Internet** werk in de vervolg keuzelijst.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Netwerk quotum verhoging op abonnements niveau aanvragen met behulp van Help en ondersteuning

Volg de onderstaande instructies om een ondersteunings aanvraag te maken met behulp van **Help en ondersteuning** in de Azure Portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com)en selecteer **Help + ondersteuning** in het menu Azure portal of zoek naar en selecteer **Help + ondersteuning**.

    ![Help en ondersteuning](./media/networking-quota-request/help-plus-support.png)

1. Selecteer **Nieuwe ondersteuningsaanvraag**.

    ![Nieuwe ondersteuningsaanvraag](./media/networking-quota-request/new-support-request.png)

1. Kies voor **probleem type** **service-en abonnements limieten (quota's)**.

    ![Abonnements limieten selecteren in vervolg keuzelijst probleem type](./media/networking-quota-request/select-quota-issue-type.png)

1. Selecteer het abonnement waarvoor het quotum moet worden verhoogd.

    ![Abonnement-Nieuwsset selecteren](./media/networking-quota-request/select-subscription-support-request.png)

1. Onder **quotum type** selecteert u **netwerken**. Selecteer **volgende: oplossingen**.

    ![Quotum type selecteren](./media/networking-quota-request/select-quota-type-network.png)

1. In **Details** van het probleem selecteert u **Details opgeven** en vult u aanvullende informatie in om uw aanvraag te verwerken.

    ![Details opgeven](./media/networking-quota-request/provide-details-link.png)

1. Selecteer in het deel venster **quotum Details** een implementatie model, een locatie en de resources die in uw aanvraag moeten worden meegenomen.

    ![Quotum Details DM](./media/networking-quota-request/quota-details-network.png)

1. Voer de nieuwe limieten in die u wilt voor het abonnement. Als u een regel wilt verwijderen, maakt u de selectie van de resource in het menu **resources** ongedaan of selecteert u het pictogram ' x '. Nadat u het quotum voor elke resource hebt ingevoerd, selecteert u **opslaan en** gaat u verder met het maken van de ondersteunings aanvraag.

    ![Nieuwe limieten](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Netwerk quotum verhoging op abonnements niveau aanvragen met gebruik van + quota

Volg deze instructies om een ondersteunings aanvraag te maken met behulp van **gebruik en quotum** in de Azure Portal.

1. In https://portal.azure.com kunt u **abonnementen** zoeken en selecteren.

    ![Abonnementen](./media/networking-quota-request/search-for-suscriptions.png)

1. Selecteer het abonnement waarvoor het quotum moet worden verhoogd.

    ![Abonnement selecteren](./media/networking-quota-request/select-subscription-change-quota.png)

1. **Gebruik en quota's** selecteren

    ![Gebruik en quota's selecteren](./media/networking-quota-request/select-usage-plus-quotas.png)

1. Selecteer in de rechter bovenhoek **aanvraag verhoging**.

    ![Toename aanvragen](./media/networking-quota-request/request-increase-from-subscription.png)

1. Volg de stappen die beginnen met stap 3 in [netwerk quotum toename aanvragen op abonnements niveau](#request-networking-quota-increase-at-subscription-level-using-help--support).

## <a name="about-networking-limits"></a>Over netwerk limieten

Zie de [sectie netwerken](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) op de pagina limieten of onze veelgestelde vragen over netwerk limieten voor meer informatie over netwerk limieten.
