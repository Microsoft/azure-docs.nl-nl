---
title: 'Quickstart: Een Analysis Services-serverfirewall configureren in Azure | Microsoft Docs'
description: Deze quickstart helpt u een firewall te configureren voor uw Azure Analysis Services-server met behulp van de Azure-portal.
author: minewiskan
ms.author: owend
ms.reviewer: minewiskan
ms.date: 08/12/2020
ms.topic: quickstart
ms.service: azure-analysis-services
ms.custom:
- mode-portal
ms.openlocfilehash: dd8324607667c6d1f5e397a3266026cf7f2dcc93
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536720"
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
