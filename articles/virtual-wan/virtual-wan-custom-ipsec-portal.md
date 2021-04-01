---
title: 'Aangepast IPsec-beleid voor Azure Virtual WAN configureren: Portal | Microsoft Docs'
description: Meer informatie over het configureren van een aangepast IPsec-beleid voor Azure Virtual WAN met behulp van de portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 07eae453441ea8bff81d7cdb60f9c46c08a22829
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91851167"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Een aangepast IPsec-beleid voor virtuele WAN configureren met behulp van de portal

U kunt een aangepast IPsec-beleid configureren voor een virtuele WAN-verbinding in de Azure Portal. Aangepaste beleids regels zijn handig wanneer u wilt dat beide zijden (on-premises en Azure VPN-gateway) dezelfde instellingen gebruiken voor IKE fase 1 en IKE fase 2.

## <a name="working-with-custom-policies"></a>Werken met aangepaste beleids regels

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Een beleid configureren

1. **Zoek de virtuele hub**. Open een browser, ga naar [Azure Portal](https://aka.ms/azurevirtualwanpreviewfeatures) en meld u aan met uw Azure-account. Navigeer naar uw virtuele WAN-resource en zoek de virtuele hub op waarmee uw VPN-site is verbonden.
2. **Selecteer de VPN-site**. Klik op de pagina overzicht van de hub op **VPN (site naar site)** en selecteer de VPN-site waarvoor u een aangepast IPSec-beleid wilt instellen.

   ![Selecteer](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Bewerk de VPN-verbinding**. Selecteer in het **context menu** de optie **VPN-verbinding bewerken**.

   ![bewerken](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Configureer de instellingen**. Op de pagina **VPN-verbinding bewerken** wijzigt u de IPSec-instelling van standaard in aangepast en past u het IPSec-beleid aan. Selecteer **Opslaan** om uw instellingen op te slaan.

   ![configureren en opslaan](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).