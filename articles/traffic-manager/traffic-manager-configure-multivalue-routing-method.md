---
title: Route ring met meerdere waarden configureren-Azure Traffic Manager
description: In dit artikel wordt uitgelegd hoe u Traffic Manager configureert om verkeer naar een/AAAA-eind punten te routeren.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 85e088dda767a6f6c80ac0a9f6eed84e8802e5ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994924"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>De methode voor het routeren van meerdere waarden in Traffic Manager configureren

In dit artikel wordt beschreven hoe u de methode voor het routeren van meerdere waarden kunt configureren. Met de methode voor het routeren van meerdere **waarden** kunt u verschillende gezonde eind punten retour neren en kunt u de betrouw baarheid van uw toepassing verg Roten omdat clients meer opties hebben om opnieuw te proberen zonder een andere DNS-zoek opdracht uit te voeren. Meerdere waarden worden alleen ingeschakeld voor profielen waarvoor alle eind punten zijn opgegeven met IPv4-of IPv6-adressen. Wanneer er een query voor dit profiel wordt ontvangen, worden alle gezonde eind punten geretourneerd op basis van het geconfigureerde maximum aantal retour waarden dat is opgegeven. 

>[!NOTE]
> Op dit moment wordt het toevoegen van eind punten met IPv4-of IPv6-adressen alleen ondersteund voor eind punten van het type **extern** en daarom wordt route ring met meerdere waarden alleen voor dergelijke eind punten ondersteund.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure 

Meld u aan bij Azure Portal op https://portal.azure.com.
## <a name="create-a-resource-group"></a>Een resourcegroep maken
Maak een resource groep voor het Traffic Manager profiel.
1. Selecteer **resource groepen** in het linkerdeel venster van de Azure Portal.
2. Selecteer aan de bovenkant van de pagina in **resource groepen** de optie **toevoegen**.
3. Typ in de naam van de **resource groep** een naam *myResourceGroupTM1*. Voor de locatie van de **resource groep** selecteert u **VS-Oost** en selecteert u **OK**.

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken
Maak een Traffic Manager-profiel dat gebruikersverkeer omleidt door gebruikers naar het eindpunt met de laagste latentie te sturen.

1. Selecteer linksboven in het scherm de optie **Een resource maken** > **Netwerken** > **Traffic Manager-profiel** > **Maken**.
2. Voer in **Traffic Manager profiel maken** de volgende informatie in of Selecteer deze, accepteer de standaard waarden voor de overige instellingen en selecteer vervolgens **maken**:
    
    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Naam                   | Deze naam moet uniek zijn binnen de zone trafficmanager.net en resulteert in de DNS-naam, trafficmanager.net, die wordt gebruikt voor het openen van uw Traffic Manager-profiel.                                   |
    | Routeringsmethode          | Selecteer de methode voor het routeren van **meerdere waarden** .                                       |
    | Abonnement            | Selecteer uw abonnement.                          |
    | Resourcegroep          | Selecteer *myResourceGroupTM1*. |
    | Locatie                | Deze instelling verwijst naar de locatie van de resourcegroep en heeft geen invloed op het Traffic Manager-profiel dat wereldwijd wordt geïmplementeerd.                              |
   |        |           | 
  
   ![Een Traffic Manager-profiel maken](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-eindpunten toevoegen

Voeg twee IP-adressen als externe eind punten toe aan het profiel voor meerdere waarden Traffic Manager dat u in de vorige stap hebt gemaakt.

1. Zoek in de zoekbalk van de portal de naam van het Traffic Manager-profiel dat u in de vorige sectie hebt gemaakt en selecteer het profiel in de weergegeven resultaten.
2. Klik in **Traffic Manager-profiel**, in de sectie **Instellingen**, op **Eindpunten** en vervolgens op **Toevoegen**.
3. Voer de volgende informatie in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **OK**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Type                    | Extern eind punt                                   |
    | Name           | myEndpoint1                                        |
    | FQDN-naam (Fully Qualified Domain Name) of IP           | Typ het open bare IP-adres van het eind punt dat u wilt toevoegen aan dit Traffic Manager profiel                         |
    |        |           |

4. Herhaal stap 2 en 3 om een ander eind punt met de naam *myEndpoint2* toe te voegen voor Fully **Qualified Domain Name (FQDN) of IP**, voer het open bare IP-adres van het tweede eind punt in.
5. Als beide eindpunten zijn toegevoegd, worden ze weergegeven in **Traffic Manager-profiel**, samen met de controlestatus **Online**.

   ![Traffic Manager-eindpunt toevoegen](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [gewogen verkeersrouteringsmethode](traffic-manager-configure-weighted-routing-method.md).
- Meer informatie over [de routeringsmethode met prioriteit](traffic-manager-configure-priority-routing-method.md).
- Meer informatie over de methode voor de [route ring van prestaties](traffic-manager-configure-performance-routing-method.md)
- Meer informatie over [geografische verkeersrouteringsmethode](traffic-manager-configure-geographic-routing-method.md).


