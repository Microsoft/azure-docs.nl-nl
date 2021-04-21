---
title: Netwerklimiet verhoogd
description: Netwerklimiet verhoogd
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 801f0424e7ec15fbde58f35975f4c7eca4c9a5de
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775561"
---
# <a name="networking-limit-increase"></a>Netwerklimiet verhoogd

Gebruik [de](https://portal.azure.com) Azure Portal om uw netwerkquotum te verhogen.

Als u uw huidige netwerkgebruik en quotum wilt weergeven in Azure Portal, opent u uw abonnement en selecteert **u Vervolgens Gebruik en quota**. U kunt ook de volgende opties gebruiken om uw netwerkgebruik en -limieten weer te geven.

* [GEBRUIKS-CLI](/cli/azure/network#az_network_list_usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [De API voor netwerkgebruik](/rest/api/virtualnetwork/virtualnetworks/listusage)

U kunt een verhoging aanvragen met behulp van **Help en ondersteuning** of in Gebruik en **quota** in de portal.

> [!Note]
> Als u de standaardgrootte van openbare IP-voorvoegsels wilt wijzigen, selecteert u Minimumlengte openbaar **IP-voorvoegsel** voor netwerkwerk in de vervolgkeuzelijst.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Verhoging van netwerkquotum op abonnementsniveau aanvragen met behulp van Help en ondersteuning

Volg de onderstaande instructies om een ondersteuningsaanvraag te maken met **behulp van Help en ondersteuning** in de Azure Portal.

1. Meld u aan [bij Azure Portal](https://portal.azure.com)en selecteer vervolgens **Help en** ondersteuning in het Azure Portal menu of zoek help en ondersteuning en **selecteer**.

    ![Help en ondersteuning](./media/networking-quota-request/help-plus-support.png)

1. Selecteer **Nieuwe ondersteuningsaanvraag**.

    ![Nieuwe ondersteuningsaanvraag](./media/networking-quota-request/new-support-request.png)

1. Kies **voor Probleemtype** de optie **Service- en abonnementslimieten (quota).**

    ![Selecteer abonnementslimieten in de vervolgkeuzekeuze selecteren van het probleemtype](./media/networking-quota-request/select-quota-issue-type.png)

1. Selecteer het abonnement waarvoor het quotum moet worden verhoogd.

    ![Abonnement newSR selecteren](./media/networking-quota-request/select-subscription-support-request.png)

1. Selecteer **onder Quotumtype** de optie **Netwerken.** Selecteer **Volgende: Oplossingen**.

    ![Selecteer het quotumtype](./media/networking-quota-request/select-quota-type-network.png)

1. Selecteer **in PROBLEEMDETAILS** **de optie Details verstrekken** en vul aanvullende informatie in om uw aanvraag te verwerken.

    ![Details verstrekken](./media/networking-quota-request/provide-details-link.png)

1. Selecteer in **het deelvenster Quotadetails** een implementatiemodel, een locatie en de resources die u in uw aanvraag wilt opnemen.

    ![DM voor quotumdetails](./media/networking-quota-request/quota-details-network.png)

1. Voer de nieuwe limieten in die u wilt gebruiken voor het abonnement. Als u een regel wilt verwijderen, verwijdert u de selectie van de resource in het menu **Resources** of selecteert u het pictogram 'x verwijderen'. Nadat u het quotum voor elke resource heeft ingesteld, selecteert u **Opslaan en gaat u** door met het maken van de ondersteuningsaanvraag.

    ![Nieuwe limieten](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Verhoging van netwerkquotum op abonnementsniveau aanvragen met behulp van gebruik en quota

Volg deze instructies om een ondersteuningsaanvraag te maken met behulp **van Gebruik en quotum** in de Azure Portal.

1. Zoek https://portal.azure.com en selecteer **Abonnementen in**.

    ![Abonnementen](./media/networking-quota-request/search-for-suscriptions.png)

1. Selecteer het abonnement waarvoor het quotum moet worden verhoogd.

    ![Abonnement selecteren](./media/networking-quota-request/select-subscription-change-quota.png)

1. Gebruik **en quota selecteren**

    ![Gebruik en quota selecteren](./media/networking-quota-request/select-usage-plus-quotas.png)

1. Selecteer verhoging aanvragen in de **rechterbovenhoek.**

    ![Verhoging aanvragen](./media/networking-quota-request/request-increase-from-subscription.png)

1. Volg de stappen die beginnen met stap 3 in [Verhoging van netwerkquotum aanvragen op abonnementsniveau.](#request-networking-quota-increase-at-subscription-level-using-help--support)

## <a name="about-networking-limits"></a>Over netwerklimieten

Zie de sectie Netwerken van de pagina Limieten [of](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) onze Veelgestelde vragen over netwerklimieten voor meer informatie over netwerklimieten.
