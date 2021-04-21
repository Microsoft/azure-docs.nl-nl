---
title: Verwijderde apps herstellen
description: Meer informatie over het herstellen van een verwijderde app in Azure App Service. Voorkom dat een per ongeluk verwijderde app wordt verwijderd.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e894e0a8bd20d6a1c3c833a4c0a3656c0dcd0f05
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833162"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Verwijderde App Service-apps herstellen met PowerShell

Als u uw app per ongeluk hebt verwijderd in Azure App Service, kunt u deze herstellen met behulp van de opdrachten uit de [Az PowerShell-module](/powershell/azure/).

> [!NOTE]
> - Verwijderde apps worden 30 dagen na de eerste verwijdering uit het systeem verwijderd. Nadat een app is verwijderd, kan deze niet meer worden hersteld.
> - Functionaliteit voor het niet-beschikbaar maken van de functie wordt niet ondersteund voor het verbruiksplan.
> - Apps Service-apps die in een App Service Environment bieden geen ondersteuning voor momentopnamen. Daarom worden functionaliteit en kloonfunctionaliteit niet verwijderen ondersteund voor App Service apps die worden uitgevoerd in een App Service Environment.
>

## <a name="re-register-app-service-resource-provider"></a>Opnieuw registreren App Service resourceprovider

Sommige klanten kunnen een probleem krijgen waarbij het ophalen van de lijst met verwijderde apps mislukt. Voer de volgende opdracht uit om het probleem op te lossen:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Lijst met verwijderde apps

Als u de verzameling verwijderde apps wilt ophalen, kunt u `Get-AzDeletedWebApp` gebruiken.

Voor meer informatie over een specifieke verwijderde app kunt u het volgende gebruiken:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

De gedetailleerde informatie omvat:

- **DeletedSiteId:** unieke id voor de app, gebruikt voor scenario's waarin meerdere apps met dezelfde naam zijn verwijderd
- **SubscriptionID:** abonnement met de verwijderde resource
- **Locatie:** Locatie van de oorspronkelijke app
- **ResourceGroupName:** naam van de oorspronkelijke resourcegroep
- **Naam:** naam van de oorspronkelijke app.
- **Sleuf:** de naam van de sleuf.
- **Verwijderingstijd:** wanneer is de app verwijderd?  

## <a name="restore-deleted-app"></a>Verwijderde app herstellen

>[!NOTE]
> `Restore-AzDeletedWebApp` wordt niet ondersteund voor functie-apps.

Zodra de app die u wilt herstellen is geïdentificeerd, kunt u deze herstellen met behulp van `Restore-AzDeletedWebApp` .

```powershell
Restore-AzDeletedWebApp -TargetResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> Implementatiesleuven worden niet hersteld als onderdeel van uw app. Als u een staging-sleuf wilt herstellen, gebruikt u de `-Slot <slot-name>`  vlag .
>

De invoer voor de opdracht is:

- **Doelresourcegroep:** doelresourcegroep waarin de app wordt hersteld
- **Naam:** de naam van de app moet wereldwijd uniek zijn.
- **TargetAppServicePlanName:** App Service gekoppeld aan de app

Standaard worden `Restore-AzDeletedWebApp` zowel uw app-configuratie als alle inhoud hersteld. Als u alleen inhoud wilt herstellen, gebruikt u de `-RestoreContentOnly` vlag met deze commandlet.

> [!NOTE]
> Als de app is gehost op en vervolgens is verwijderd uit een App Service Environment, kan deze alleen worden hersteld als de bijbehorende App Service Environment nog bestaat.
>

U vindt de volledige naslag hier: [Restore-AzDeletedWebApp.](/powershell/module/az.websites/restore-azdeletedwebapp)
