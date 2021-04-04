---
title: 'Quickstart: een Azure Analysis Services-server maken met behulp van PowerShell | Microsoft Docs'
description: In deze quickstart wordt beschreven hoe u een Azure Analysis Services-server maakt met behulp van PowerShell
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 08/31/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions , devx-track-azurepowershell
ms.openlocfilehash: 737649538aaf82352e27aec6220b13ba355a7a82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89229324"
---
# <a name="quickstart-create-a-server---powershell"></a>Quickstart: Een server maken - PowerShell

In deze snelstart wordt beschreven hoe u PowerShell vanaf de opdrachtregel kunt gebruiken om een Azure Analysis Services-server te maken in uw Azure-abonnement.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-abonnement**: Ga naar [gratis proefversie van Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) om een account te maken.
- **Azure Active Directory**: Uw abonnement moet zijn gekoppeld aan een Azure Active Directory-tenant en u moet een account hebben in de betreffende map. Raadpleeg voor meer informatie [Verificatie en gebruikersmachtigingen](analysis-services-manage-users.md).
- **Azure PowerShell**. Voer `Get-Module -ListAvailable Az` uit om na te gaan welke versie er is geïnstalleerd. Zie [Azure PowerShell-module installeren](/powershell/azure/install-Az-ps) om de module te installeren of te upgraden.

## <a name="import-azanalysisservices-module"></a>Az.AnalysisServices-module importeren

U gebruikt de [Az.AnalysisServices](/powershell/module/az.analysisservices)-module om een server in het abonnement te maken. Laad de Az.AnalysisServices-module in de PowerShell-sessie.

```powershell
Import-Module Az.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij uw Azure-abonnement met behulp van de opdracht [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Volg de aanwijzingen op het scherm.

```powershell
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) is een logische container waarin Azure-resources worden geïmplementeerd en als groep beheerd. Wanneer u de server maakt, moet u een resourcegroep opgeven in uw abonnement. Als u nog geen resourcegroep hebt, maakt u een nieuwe met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voorbeeld wordt een resourcegroep met de naam `myResourceGroup` gemaakt in de regio VS - west.

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>Een server maken

Maak een nieuwe server met behulp van de opdracht [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver). In het volgende voorbeeld wordt een server met de naam myServer gemaakt in myResourceGroup, in de regio VS - West, in de (gratis) D1-laag, en wordt philipc@adventureworks.com opgegeven als serverbeheerder.

```powershell
New-AzAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de server uit het abonnement verwijderen met behulp van de opdracht [Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver). Verwijder de server niet als u met andere snelstartgidsen en zelfstudies in deze verzameling doorgaat. In het volgende voorbeeld wordt de server die u in de vorige stap hebt gemaakt, verwijderd.


```powershell
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u een server in uw Azure-abonnement maakt met behulp van PowerShell. Nu u een server hebt gemaakt, kunt u deze beveiligen door een serverfirewall te configureren. (Optioneel) U kunt ook rechtstreeks vanuit de portal een eenvoudig voorbeeldgegevensmodel toevoegen aan de server. Een voorbeeldmodel is handig als u meer wilt weten over het configureren van modeldatabaserollen en het testen van clientverbindingen. Als u meer wilt weten, gaat u verder met de zelfstudie waarin u leert een voorbeeldmodel toe te voegen.

> [!div class="nextstepaction"]
> [Snelstartgids: Een serverfirewall configureren - Portal](analysis-services-qs-firewall.md)      