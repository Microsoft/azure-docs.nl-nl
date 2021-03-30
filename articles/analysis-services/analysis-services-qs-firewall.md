---
title: 'Quickstart: Een Analysis Services-serverfirewall configureren in Azure | Microsoft Docs'
description: Deze quickstart helpt u een firewall te configureren voor uw Azure Analysis Services-server met behulp van de Azure-portal.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: e4953137cf939c35c6ac73fe51ca43eca6e99edc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88192431"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Quickstart: Een serverfirewall configureren - Portal

Deze snelstart helpt u bij het configureren van een firewall voor uw Azure Analysis Services-server. Een firewall inschakelen en het configureren van IP-adresbereiken voor uitsluitend de computers die toegang tot de server hebben, speelt een belangrijke rol bij het beveiligen van uw server en uw gegevens.

## <a name="prerequisites"></a>Vereisten

- Een Azure Analysis Services-server in uw abonnement. Raadpleeg voor meer informatie [Snelstart: Een server maken - Portal](analysis-services-create-server.md) of [Snelstart: Een server maken - PowerShell](analysis-services-create-powershell.md)
- Een of meer IP-adresbereiken voor clientcomputers (indien nodig).

> [!NOTE]
> Gegevensimport (vernieuwen) en verbindingen voor gepagineerde rapporten vanuit Power BI Premium in Microsoft Cloud Duitsland worden momenteel niet ondersteund wanneer een firewall is ingeschakeld, zelfs wanneer de instelling Toegang vanaf Power BI toestaan is ingesteld op Aan.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal 

[Aanmelden bij de portal](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Een firewall configureren

1. Klik op de server om de overzichtspagina te openen. 
2. Selecteer **Aan** in **INSTELLINGEN** > **Firewall** > **Firewall inschakelen**.
3. Als u verbindingen vanuit Power BI en Power BI Premium wilt inschakelen, selecteert u **Aan** in **Toegang toestaan vanuit Power BI**.  
4. (Optioneel) Geef een of meer IP-adresbereiken op. Typ een naam, begin- en eind-IP-adressen voor elk bereik. De naam van de firewallregel mag uit maximaal 128 tekens bestaan, en kan alleen hoofdletters, kleine letters, cijfers, onderstrepingstekens en afbreekstreepjes bevatten. Spaties en andere speciale tekens zijn niet toegestaan.
5. Klik op **Opslaan**.

     ![Firewallinstellingen](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer deze niet langer nodig zijn, verwijdert u de IP-adresbereiken of schakelt u de firewall uit.

## <a name="next-steps"></a>Volgende stappen
In deze snelstart hebt u geleerd hoe u een firewall configureert voor uw server. Nu dat u een server hebt en deze hebt beveiligd met een firewall, kunt u een eenvoudig voorbeeldgegevensmodel vanuit de portal toevoegen. Een voorbeeldmodel is handig als u meer wilt weten over het configureren van modeldatabaserollen en het testen van clientverbindingen. Als u meer wilt weten, gaat u verder met de zelfstudie waarin u leert een voorbeeldmodel toe te voegen.

> [!div class="nextstepaction"]
> [Zelfstudie: Een voorbeeldmodel toevoegen aan uw server](analysis-services-create-sample-model.md)
