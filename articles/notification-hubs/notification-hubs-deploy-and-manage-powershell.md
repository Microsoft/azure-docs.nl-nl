---
title: Notification Hubs implementeren en beheren met behulp van Power shell
description: Notification Hubs maken en beheren met behulp van Power shell voor automatisering
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 4534584144f54618d7f3dd39cf5e40bc0464fb21
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454982"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Notification hubs implementeren en beheren met Power shell

## <a name="overview"></a>Overzicht

In dit artikel leest u hoe u Azure Notification Hubs maken en beheren met behulp van Power shell. In dit artikel worden de volgende algemene automatiserings taken weer gegeven.

- Een Notification Hub maken
- Referenties instellen

Zie [Service Bus beheren met Power shell](../service-bus-messaging/service-bus-manage-with-ps.md)als u ook een nieuwe service bus-naam ruimte wilt maken voor uw notification hubs.

Het beheren van Notification hubs wordt niet rechtstreeks ondersteund door de cmdlets die zijn opgenomen in Azure PowerShell. De beste benadering van Power shell is om te verwijzen naar de Microsoft.Azure.NotificationHubs.dll-assembly. De assembly wordt gedistribueerd met het [Microsoft Azure notification hubs NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Azure is een platform op basis van abonnementen. Zie voor meer informatie over het verkrijgen van een abonnement, [aankoop opties], [leden aanbiedingen]of [gratis proef versie].
- Een computer met Azure PowerShell. Zie [Azure PowerShell installeren en configureren]voor instructies.
- Een algemene uitleg van Power shell-scripts, NuGet-pakketten en de .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Inclusief een verwijzing naar de .NET-assembly voor Service Bus

Het beheren van Azure Notification Hubs is nog niet opgenomen in de Power shell-cmdlets in Azure PowerShell. Als u notification hubs wilt inrichten, kunt u de .NET-client gebruiken die in het [Microsoft Azure notification hubs NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)is opgenomen.

Zorg er eerst voor dat uw script de **Microsoft.Azure.NotificationHubs.dll** -assembly kan vinden die is geïnstalleerd als een NuGet-pakket in een Visual Studio-project. Om het flexibele te maken, voert het script de volgende stappen uit:

1. Bepaalt het pad waarop het is aangeroepen.
2. Hiermee wordt het pad door lopen totdat een map met de naam wordt gevonden `packages` . Deze map wordt gemaakt wanneer u NuGet-pakketten voor Visual Studio-projecten installeert.
3. Recursief zoekt in de `packages` map naar een assembly met de naam `Microsoft.Azure.NotificationHubs.dll` .
4. Verwijst naar de assembly zodat de typen beschikbaar zijn voor later gebruik.

Deze stappen worden in een Power shell-script geïmplementeerd:

``` powershell
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>De `NamespaceManager` klasse maken

Als u Notification Hubs wilt inrichten, maakt u een instantie van de [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) -klasse van de SDK.

U kunt met behulp van de cmdlet [Get-AzureSBAuthorizationRule] die deel uitmaakt van Azure PowerShell, een autorisatie regel ophalen die wordt gebruikt om een Connection String op te geven. Een verwijzing naar het `NamespaceManager` exemplaar wordt opgeslagen in de `$NamespaceManager` variabele. `$NamespaceManager` wordt gebruikt om een notification hub in te richten.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>Een nieuwe notification hub inrichten

Als u een nieuwe notification hub wilt inrichten, gebruikt u de [.net API voor notification hubs].

In dit deel van het script stelt u vier lokale variabelen in.

1. `$Namespace`: Stel dit in op de naam van de naam ruimte waarin u een notification hub wilt maken.
2. `$Path`: Stel dit pad in op de naam van de nieuwe notification hub.  Bijvoorbeeld ' MyHub '.
3. `$WnsPackageSid`: Stel dit in op de pakket-SID voor uw Windows-app in het [Windows-ontwikkelaars centrum](https://developer.microsoft.com/en-us/windows).
4. `$WnsSecretkey`: Stel dit in op de geheime sleutel voor uw Windows-app in het [Windows-ontwikkelaars centrum](https://developer.microsoft.com/en-us/windows).

Deze variabelen worden gebruikt om verbinding te maken met uw naam ruimte en een nieuwe notification hub te creëren die is geconfigureerd voor het afhandelen van Windows Notification Services (WNS)-meldingen met WNS-referenties voor een Windows-app. Zie de zelf studie aan de slag [met Notification hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) voor meer informatie over het verkrijgen van de pakket-sid en de geheime sleutel.

- Het script fragment gebruikt het `NamespaceManager` object om te controleren of de notification hub is geïdentificeerd door `$Path` exists.
- Als deze niet bestaat, wordt het script gemaakt `NotificationHubDescription` met wns-referenties en wordt het door gegeven aan de `NamespaceManager` klasse- `CreateNotificationHub` methode.

``` powershell
$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```

## <a name="additional-resources"></a>Aanvullende resources

- [Service Bus met PowerShell beheren](../service-bus-messaging/service-bus-manage-with-ps.md)
- [Service Bus-wacht rijen,-onderwerpen en-abonnementen maken met een Power shell-script](/archive/blogs/paolos/how-to-create-service-bus-queues-topics-and-subscriptions-using-a-powershell-script)
- [Een Service Bus naam ruimte en een event hub maken met een Power shell-script](/archive/blogs/paolos/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script)

Sommige kant-en-klare scripts zijn ook beschikbaar om te worden gedownload:

- [Service Bus Power shell-scripts](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Aankoop opties]: https://azure.microsoft.com/pricing/purchase-options/
[Aanbiedingen voor leden]: https://azure.microsoft.com/pricing/member-offers/
[Gratis proefversie]: https://azure.microsoft.com/pricing/free-trial/
[Azure PowerShell installeren en configureren]: /powershell/azure/
[.NET-API voor Notification Hubs]: /dotnet/api/overview/azure/notification-hubs
[Get-AzureSBNamespace]: /powershell/module/servicemanagement/azure.service/get-azuresbnamespace
[New-AzureSBNamespace]: /powershell/module/servicemanagement/azure.service/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: /powershell/module/servicemanagement/azure.service/get-azuresbauthorizationrule
