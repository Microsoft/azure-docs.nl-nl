---
title: Een ASE v1 maken
description: Beschrijving van de aanmaak stroom voor een app service Environment v1. Dit document is alleen bedoeld voor klanten die gebruikmaken van de oudere V1-ASE.
author: ccompy
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 543050bc899c257c4ad5e0d0c399a1de6f0f58f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005199"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Een App Service Environment v1 maken 

> [!NOTE]
> Dit artikel heeft betrekking op de App Service Environment v1. Er is een nieuwere versie van de App Service Environment die eenvoudiger is te gebruiken en wordt uitgevoerd op een krachtigere infra structuur. Begin met de [Inleiding tot de app service Environment](intro.md)voor meer informatie over de nieuwe versie.
> 

### <a name="overview"></a>Overzicht
De App Service Environment (ASE) is een Premium Service-optie van Azure App Service die een verbeterde configuratie mogelijkheid biedt die niet beschikbaar is in de multi tenant-stem pels. De functie ASE implementeert in feite de Azure App Service in het virtuele netwerk van een klant. Als u meer inzicht wilt krijgen in de mogelijkheden die worden geboden door App Service omgevingen, leest u het document [Wat is een app service Environment][WhatisASE] -documentatie.

### <a name="before-you-create-your-ase"></a>Voordat u uw ASE maakt
Het is belang rijk dat u op de hoogte bent van de dingen die u niet kunt wijzigen. Deze aspecten die u niet kunt wijzigen over uw ASE nadat deze is gemaakt, zijn:

* Locatie
* Abonnement
* Resourcegroep
* Gebruikte VNet
* Subnet gebruikt 
* Grootte van subnet

Bij het kiezen van een VNet en het opgeven van een subnet, moet u ervoor zorgen dat het groot genoeg is voor toekomstige groei. 

### <a name="creating-an-app-service-environment-v1"></a>Een App Service Environment v1 maken
Als u een app service Environment v1 wilt maken, kunt u in de Azure Marketplace zoeken naar ***app service Environment v1** _ of door gaan _ *een resource maken**-> **Web en mobiel**  ->  **app service Environment**. Een ASEv1 maken:

1. Geef de naam van uw ASE op. De naam die u opgeeft voor de ASE wordt gebruikt voor de apps die zijn gemaakt in de ASE. Als de naam van de ASE appsvcenvdemo is, is de naam van het subdomein: *appsvcenvdemo.p.azurewebsites.net*. Als u een app hebt gemaakt met de naam *mytestapp*, is deze beschikbaar op *mytestapp.appsvcenvdemo.p.azurewebsites.net*. U kunt geen witruimte gebruiken in de naam van uw ASE. Als u hoofd letters in de naam gebruikt, wordt de domein naam de totale kleine letters versie van die naam. Als u een ILB gebruikt, wordt uw ASE-naam niet gebruikt in uw subdomein, maar wordt in plaats daarvan expliciet aangegeven tijdens het maken van ASE.
   
    ![Scherm afbeelding die laat zien hoe u een App Service Environment maakt (ASE).][1]
2. Selecteer uw abonnement. Het abonnement dat u gebruikt voor uw ASE is ook van toepassing op alle apps die u in die ASE maakt. U kunt uw ASE niet in een VNet plaatsen dat zich in een ander abonnement bevindt.
3. Selecteer een nieuwe resource groep of geef deze op. De resource groep die wordt gebruikt voor uw ASE moet gelijk zijn aan die voor uw VNet. Als u een bestaand VNet selecteert, wordt de selectie van de resource groep voor uw ASE bijgewerkt zodat deze overeenkomt met die van uw VNet.
   
    ![Scherm afbeelding die laat zien hoe u een nieuwe resource groep selecteert of wijzigt.][2]
4. Maak de selecties voor Virtual Network en locatie. U kunt ervoor kiezen om een nieuw VNet te maken of een bestaand VNet te selecteren. Als u een nieuw VNet selecteert, kunt u een naam en locatie opgeven. Het nieuwe VNet heeft het adres bereik 192.168.250.0/23 en een subnet met de naam **standaard** dat is gedefinieerd als 192.168.250.0/24. U kunt ook gewoon een bestaand klassiek of Resource Manager-VNet selecteren. De selectie van het VIP-type bepaalt of uw ASE rechtstreeks toegankelijk is vanaf internet (extern) of dat er een interne Load Balancer (ILB) wordt gebruikt. Voor meer informatie over het lezen van [een interne Load Balancer met een app service Environment][ILBASE]. Als u het VIP-type extern selecteert, kunt u selecteren hoeveel externe IP-adressen het systeem heeft gemaakt met voor IP SSL doeleinden. Als u intern selecteert, moet u het subdomein opgeven dat door uw ASE wordt gebruikt. As kan worden geïmplementeerd in virtuele netwerken *die gebruikmaken van* open bare adresbereiken *of* RFC1918-adres ruimten (dat wil zeggen persoonlijke adressen). Als u een virtueel netwerk met een openbaar adres bereik wilt gebruiken, moet u het VNet vooraf van de tijd maken. Wanneer u een al bestaand VNet selecteert, moet u een nieuw subnet maken tijdens het maken van ASE. **U kunt een vooraf gemaakt subnet niet gebruiken in de portal. U kunt een ASE maken met een reeds bestaand subnet als u uw ASE maakt met behulp van een resource manager-sjabloon.** Als u een ASE wilt maken op basis van een sjabloon, gebruikt u de informatie hier, [maakt u een app service Environment van de sjabloon][ILBAseTemplate] en [maakt u een ILB-app service Environment van een sjabloon][ASEfromTemplate].

### <a name="details"></a>Details
Een ASE wordt gemaakt met twee front-ends en twee werk rollen. De front-ends fungeren als de HTTP/HTTPS-eind punten en verzenden verkeer naar de werk nemers die de rollen zijn die uw apps hosten. U kunt de hoeveelheid aanpassen nadat ASE is gemaakt en u kunt zelfs regels voor automatisch schalen instellen voor deze resource groepen. Voor meer informatie over hand matig schalen, beheer en controle van een App Service Environment gaat u als volgt te werk: [een app service Environment configureren][ASEConfig] 

Er kan slechts één ASE bestaan in het subnet dat door de ASE wordt gebruikt. Het subnet kan niet worden gebruikt voor iets anders dan de ASE

### <a name="after-app-service-environment-v1-creation"></a>Na het maken van App Service Environment v1
Nadat u ASE hebt gemaakt, kunt u het volgende aanpassen:

* Aantal front-ends (minimum: 2)
* Aantal werk nemers (minimum: 2)
* Aantal beschik bare IP-adressen voor IP SSL
* Berekenings resource grootten die worden gebruikt door de front-ends of werk rollen (minimale grootte van front-end is P2)

Hier vindt u meer informatie over het hand matig schalen, beheren en bewaken van App Service omgevingen: [een app service Environment configureren][ASEConfig] 

Voor informatie over automatisch schalen is er hier een hand leiding: automatisch [schalen configureren voor een app service Environment][ASEAutoscale]

Er zijn aanvullende afhankelijkheden die niet beschikbaar zijn voor aanpassing, zoals de data base en opslag. Deze worden verwerkt door Azure en geleverd bij het systeem. De systeem opslag ondersteunt Maxi maal 500 GB voor de hele App Service Environment en de data base wordt aangepast door Azure, zoals nodig is voor de schaal van het systeem.

## <a name="getting-started"></a>Aan de slag
Zie [Inleiding tot de app service Environment v1][WhatisASE] om aan de slag te gaan met app service Environment v1

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
