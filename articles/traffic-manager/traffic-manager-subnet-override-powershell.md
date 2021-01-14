---
title: Het subnet van Azure Traffic Manager worden overschreven met behulp van Azure PowerShell | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe Traffic Manager subnet overschrijving wordt gebruikt om de routerings methode van een Traffic Manager profiel te onderdrukken voor het omleiden van verkeer naar een eind punt op basis van het IP-adres van de eind gebruiker via vooraf gedefinieerd IP-bereik voor eindpunt toewijzingen met behulp van Azure PowerShell.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 502533b69058eacd4ad18a3b29a33fbc4a3715a5
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183759"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Traffic Manager subnet overschrijven met Azure Power shell

Met Traffic Manager subnet override kunt u de routerings methode van een profiel wijzigen.  Door het toevoegen van een onderdrukking wordt verkeer doorgestuurd op basis van het IP-adres van de eind gebruiker met een vooraf gedefinieerd IP-bereik voor de toewijzing van eind punten. 

## <a name="how-subnet-override-works"></a>Hoe subnet overschrijving werkt

Wanneer subnet onderdrukkingen worden toegevoegd aan een Traffic Manager-profiel, wordt door Traffic Manager eerst gecontroleerd of er een subnet wordt overschreven voor het IP-adres van de eind gebruiker. Als er een wordt gevonden, wordt de DNS-query van de gebruiker omgeleid naar het bijbehorende eind punt.  Als een toewijzing niet wordt gevonden, wordt Traffic Manager terugvallen op de oorspronkelijke routerings methode van het profiel. 

De IP-adresbereiken kunnen worden opgegeven als CIDR-bereiken (bijvoorbeeld 1.2.3.0/24) of als adresbereiken (bijvoorbeeld 1.2.3.4-5.6.7.8). De IP-bereiken die zijn gekoppeld aan elk eind punt, moeten uniek zijn voor dat eind punt. Elke overlap ping van IP-bereiken tussen verschillende eind punten leidt ertoe dat het profiel wordt geweigerd door Traffic Manager.

Er zijn twee typen routerings profielen die ondersteuning bieden voor subnet onderdrukkingen:

* **Geografisch** : als Traffic Manager een onderdrukking van het subnet voor het IP-adres van de DNS-query vindt, stuurt de query door naar het eind punt, ongeacht de status van het eind punt.
* **Prestaties** : als Traffic Manager een onderdrukking van het subnet voor het IP-adres van de DNS-query vindt, wordt het verkeer alleen doorgestuurd naar het eind punt als dit in orde is.  Traffic Manager gaat terug naar de prestatie routering heuristisch als het subnet onderdrukkings eindpunt niet in orde is.

## <a name="create-a-traffic-manager-subnet-override"></a>Een Traffic Manager subnet overschrijven maken

Als u een Traffic Manager subnet overschrijven wilt maken, kunt u Azure PowerShell gebruiken om de subnetten voor de onderdrukking toe te voegen aan het Traffic Manager-eind punt.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt de opdrachten uitvoeren die volgen in de [Azure Cloud shell](https://shell.azure.com/powershell), of door Power shell uit te voeren vanaf uw computer. De Azure Cloud Shell is een gratis interactieve shell. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u Power shell vanaf uw computer uitvoert, hebt u de Azure PowerShell module 1.0.0 of hoger nodig. U kunt uitvoeren `Get-Module -ListAvailable Az` om de geïnstalleerde versie te vinden. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u Power shell lokaal uitvoert, moet u ook uitvoeren `Login-AzAccount` om u aan te melden bij Azure.


1. **Het Traffic Manager-eind punt ophalen:**

    Als u het overschrijven van het subnet wilt inschakelen, haalt u het eind punt op waaraan u de onderdrukking wilt toevoegen en slaat u het op in een variabele met [Get-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Vervang de naam, PROFILENAME en ResourceGroupName door de waarden van het eind punt dat u wilt wijzigen.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Voeg het IP-adres bereik toe aan het eind punt:**
    
    Als u het IP-adres bereik wilt toevoegen aan het eind punt, gebruikt u [add-AzTrafficManagerIpAddressRange](/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0) om het bereik toe te voegen.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    Zodra de bereiken zijn toegevoegd, gebruikt u [set-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) om het eind punt bij te werken.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
Het verwijderen van het IP-adres bereik kan worden voltooid met behulp van [Remove-AzTrafficManagerIpAddressRange](/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0).

1.  **Het Traffic Manager-eind punt ophalen:**

    Als u het overschrijven van het subnet wilt inschakelen, haalt u het eind punt op waaraan u de onderdrukking wilt toevoegen en slaat u het op in een variabele met [Get-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Vervang de naam, PROFILENAME en ResourceGroupName door de waarden van het eind punt dat u wilt wijzigen.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Het IP-adres bereik uit het eind punt verwijderen:**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     Zodra de bereiken zijn verwijderd, gebruikt u [set-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) om het eind punt bij te werken.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over methoden voor het [routeren](traffic-manager-routing-methods.md)van Traffic Manager verkeer.

Meer informatie over het [subnet Traffic-routerings methode](./traffic-manager-routing-methods.md#subnet-traffic-routing-method)