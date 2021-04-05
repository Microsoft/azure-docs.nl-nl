---
title: Een externe ASE maken
description: Meer informatie over het maken van een App Service-omgeving met een app, of het maken van een zelfstandige (lege) ASE.
author: ccompy
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: c953c31792b8d01199d409cbd91124138a6ebb15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92927444"
---
# <a name="create-an-external-app-service-environment"></a>Een externe App Service omgeving maken

Azure App Service Environment is een implementatie van Azure App Service in een subnet in een virtueel Azure-netwerk (VNet).

> [!NOTE]
> Elk App Service Environment heeft een virtueel IP-adres (VIP) dat kan worden gebruikt om contact op te nemen met de App Service Environment.

Er zijn twee manieren om een Azure App Service-omgeving (ASE) te implementeren:

- Met een VIP-adres op een extern IP-adres, vaak aangeduid als Externe AS-omgeving.
- Met het VIP op een intern IP-adres, ook wel een ILB-ASE genoemd, omdat het interne eind punt een interne Load Balancer (ILB) is.

In dit artikel wordt beschreven hoe u een externe ASE maakt. Zie [een inleiding tot de app service Environment][Intro]voor een overzicht van de ASE. Zie [een ILB ASE maken en gebruiken][MakeILBASE]voor meer informatie over het maken van een ILB-ASE.

## <a name="before-you-create-your-ase"></a>Voordat u uw ASE maakt

Nadat u uw ASE hebt gemaakt, kunt u het volgende niet meer wijzigen:

- Locatie
- Abonnement
- Resourcegroep
- Gebruikte VNet
- Subnet gebruikt
- Grootte van subnet

> [!NOTE]
> Wanneer u een VNet kiest en een subnet opgeeft, moet u ervoor zorgen dat het groot genoeg is om te voldoen aan toekomstige groei-en schaal behoeften. U kunt het beste een grootte van `/24` met 256-adressen aanraden.
>

## <a name="three-ways-to-create-an-ase"></a>Drie manieren om een ASE te maken

Er zijn drie manieren om een ASE te maken:

- **Tijdens het maken van een app service-abonnement**. Met deze methode maakt u de ASE en het App Service plan in één stap.
- **Als een zelfstandige actie**. Met deze methode maakt u een zelfstandige ASE. Dit is een ASE met niets. Deze methode is een geavanceerder proces voor het maken van een ASE. U kunt deze gebruiken om een ASE te maken met een ILB.
- **Vanuit een Azure Resource Manager sjabloon**. Deze methode is voor ervaren gebruikers. Zie [een ASE maken op basis van een sjabloon][MakeASEfromTemplate]voor meer informatie.

Een extern-ASE heeft een openbaar VIP, wat betekent dat alle HTTP/HTTPS-verkeer naar de apps in de ASE een IP-adres is dat toegankelijk is via internet. Een ASE met een ILB heeft een IP-adres uit het subnet dat door de ASE wordt gebruikt. De apps die worden gehost in een ILB-ASE, worden niet rechtstreeks op internet weer gegeven.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Een ASE en een App Service plan samen maken

Het App Service plan is een container met apps. Wanneer u een app maakt in App Service, kiest of maakt u een App Service plan. App Service omgevingen houden App Service plannen en App Service abonnementen op apps.

Een ASE maken tijdens het maken van een App Service-abonnement:

1. Selecteer in de [Azure Portal](https://portal.azure.com/) **een resource maken**  >  **Web en mobiel**  >  **Web-app**.

    ![Scherm opname van de Azure Portal weer geven Web en mobiel geselecteerd in de Azure Marketplace en het scherm voor het maken van een nieuwe web-app aan de rechter kant.][1]

2. Selecteer uw abonnement. De app en de ASE worden gemaakt in dezelfde abonnementen.

3. Selecteer of maak een resourcegroep. Met resource groepen kunt u gerelateerde Azure-resources beheren als een eenheid. Resource groepen zijn ook handig wanneer u Role-Based Access Control regels voor uw apps instelt. Zie [Overzicht van Azure Resource Manager][ARMOverview] voor meer informatie.

4. Selecteer uw besturings systeem (Windows, Linux of docker). 

5. Selecteer het App Service plan en selecteer vervolgens **nieuwe maken**. Linux-web-apps en Windows Web apps kunnen zich niet in hetzelfde App Service plan bevindt, maar kunnen zich in dezelfde App Service Environment. 

    ![Scherm opname van de Azure Portal het deel venster Web-app, het deel venster app service-plan weer te geven en het deel venster Nieuw App Service plan wordt geopend.][2]

6. Selecteer in de vervolg keuzelijst **locatie** de regio waar u de ASE wilt maken. Als u een bestaande ASE selecteert, wordt er geen nieuwe ASE gemaakt. Het App Service plan wordt gemaakt in de ASE die u hebt geselecteerd. 

7. Selecteer **prijs categorie** en kies een van de **afzonderlijke** sku's voor prijzen. Als u kiest voor een **geïsoleerde** SKU-kaart en een locatie die geen ASE is, wordt er een nieuwe ASE op die locatie gemaakt. Selecteer **selecteren** om het proces voor het maken van een ASE te starten. De **geïsoleerde** SKU is alleen beschikbaar in combi natie met een ASE. U kunt ook geen enkele andere prijs-SKU gebruiken in een andere ASE dan **geïsoleerd**. 

    ![Prijs categorie selecteren][3]

8. Voer de naam voor uw ASE in. Deze naam wordt gebruikt in de adresseer bare naam voor uw apps. Als de naam van de ASE _appsvcenvdemo_ is, is de domein naam *. appsvcenvdemo.p.azurewebsites.net*. Als u een app met de naam *mytestapp* maakt, is deze beschikbaar op mytestapp.appsvcenvdemo.p.azurewebsites.net. U kunt geen witruimte gebruiken in de naam. Als u hoofd letters gebruikt, is de domein naam de totale kleine letters versie van die naam.

    ![Naam van nieuw App Service plan][4]

9. Geef de details op van uw virtuele Azure-netwerk. Selecteer **Nieuw maken** of **Selecteer bestaande**. De optie om een bestaand VNet te selecteren, is alleen beschikbaar als u een VNet hebt in de geselecteerde regio. Als u **Nieuw maken** selecteert, voert u een naam in voor het VNet. Er wordt een nieuw Resource Manager VNet met die naam gemaakt. Er wordt gebruikgemaakt van de adres ruimte `192.168.250.0/23` in de geselecteerde regio. Als u **bestaande selecteren** selecteert, moet u het volgende doen:

    a. Selecteer het VNet-adres blok als u meer dan één hebt.

    b. Voer een nieuwe subnetnaam in.

    c. Selecteer de grootte van het subnet. *Vergeet niet om een omvang te selecteren die groot genoeg is voor toekomstige groei van uw ASE.* We raden `/24` aan dat 256-adressen heeft en een ASE met maximale grootte kunnen verwerken. We raden u `/28` aan om bijvoorbeeld alleen 16 adressen beschikbaar te stellen. Infra structuur gebruikt ten minste zeven adressen en Azure-netwerken gebruiken een andere 5. In een `/28` subnet hebt u een maximum schaal van vier app service exemplaren plannen voor een externe ASE en slechts 3 app service plan instanties voor een ILB ASE.

    d. Selecteer het IP-adres bereik van het subnet.

10. Selecteer **maken** om de ASE te maken. Dit proces maakt ook het App Service plan en de app. De ASE, het App Service plan en de app zijn allemaal onder hetzelfde abonnement en ook in dezelfde resource groep. Als uw ASE een afzonderlijke resource groep nodig heeft of als u een ILB ASE nodig hebt, volgt u de stappen om zelf een ASE te maken.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Een ASE en een Linux-web-app maken met behulp van een aangepaste docker-installatie kopie

1. Maak in [](https://portal.azure.com/)de Azure Portal **een resource**  >  **Web en mobiel**  >  **Web App for containers.** 

    ![Scherm afbeelding van de Azure Portal weer geven Web en mobiel geselecteerd in de Azure Marketplace en het Web App for Containers deel venster aan de rechter kant geopend.][7]

1. Selecteer uw abonnement. De app en de ASE worden gemaakt in dezelfde abonnementen.

1. Selecteer of maak een resourcegroep. Met resource groepen kunt u gerelateerde Azure-resources beheren als een eenheid. Resource groepen zijn ook handig wanneer u Role-Based Access Control regels voor uw apps instelt. Zie [Overzicht van Azure Resource Manager][ARMOverview] voor meer informatie.

1. Selecteer het App Service plan en selecteer vervolgens **nieuwe maken**. Linux-web-apps en Windows Web apps kunnen zich niet in hetzelfde App Service plan bevindt, maar kunnen zich in dezelfde App Service Environment. 

    ![Scherm opname van de Azure Portal het deel venster Web App for Containers, het deel venster app service-plan, en het deel venster Nieuw App Service plan worden geopend.][8]

1. Selecteer in de vervolg keuzelijst **locatie** de regio waar u de ASE wilt maken. Als u een bestaande ASE selecteert, wordt er geen nieuwe ASE gemaakt. Het App Service plan wordt gemaakt in de ASE die u hebt geselecteerd. 

1. Selecteer **prijs categorie** en kies een van de **afzonderlijke** sku's voor prijzen. Als u kiest voor een **geïsoleerde** SKU-kaart en een locatie die geen ASE is, wordt er een nieuwe ASE op die locatie gemaakt. Selecteer **selecteren** om het proces voor het maken van een ASE te starten. De **geïsoleerde** SKU is alleen beschikbaar in combi natie met een ASE. U kunt ook geen enkele andere prijs-SKU gebruiken in een andere ASE dan **geïsoleerd**. 

    ![Prijs categorie selecteren][3]

1. Voer de naam voor uw ASE in. Deze naam wordt gebruikt in de adresseer bare naam voor uw apps. Als de naam van de ASE _appsvcenvdemo_ is, is de domein naam *. appsvcenvdemo.p.azurewebsites.net*. Als u een app met de naam *mytestapp* maakt, is deze beschikbaar op mytestapp.appsvcenvdemo.p.azurewebsites.net. U kunt geen witruimte gebruiken in de naam. Als u hoofd letters gebruikt, is de domein naam de totale kleine letters versie van die naam.

    ![Naam van nieuw App Service plan][4]

1. Geef de details op van uw virtuele Azure-netwerk. Selecteer **Nieuw maken** of **Selecteer bestaande**. De optie om een bestaand VNet te selecteren, is alleen beschikbaar als u een VNet hebt in de geselecteerde regio. Als u **Nieuw maken** selecteert, voert u een naam in voor het VNet. Er wordt een nieuw Resource Manager VNet met die naam gemaakt. Er wordt gebruikgemaakt van de adres ruimte `192.168.250.0/23` in de geselecteerde regio. Als u **bestaande selecteren** selecteert, moet u het volgende doen:

    a. Selecteer het VNet-adres blok als u meer dan één hebt.

    b. Voer een nieuwe subnetnaam in.

    c. Selecteer de grootte van het subnet. *Vergeet niet om een omvang te selecteren die groot genoeg is voor toekomstige groei van uw ASE.* We raden `/24` aan dat 128-adressen heeft en een ASE met maximale grootte kunnen verwerken. We raden u `/28` aan om bijvoorbeeld alleen 16 adressen beschikbaar te stellen. Infra structuur gebruikt ten minste zeven adressen en Azure-netwerken gebruiken een andere 5. In een `/28` subnet hebt u een maximum schaal van vier app service exemplaren plannen voor een externe ASE en slechts 3 app service plan instanties voor een ILB ASE.

    d. Selecteer het IP-adres bereik van het subnet.

1.  Selecteer container configureren.
    * Voer de naam van uw aangepaste installatie kopie in (u kunt Azure Container Registry, docker hub en uw eigen persoonlijke REGI ster gebruiken). Als u uw eigen aangepaste container niet wilt gebruiken, kunt u gewoon uw code meenemen en een ingebouwde installatie kopie met App Service op Linux gebruiken met behulp van de bovenstaande instructies. 

    ![Container configureren][9]

1. Selecteer **maken** om de ASE te maken. Dit proces maakt ook het App Service plan en de app. De ASE, het App Service plan en de app zijn allemaal onder hetzelfde abonnement en ook in dezelfde resource groep. Als uw ASE een afzonderlijke resource groep nodig heeft of als u een ILB ASE nodig hebt, volgt u de stappen om zelf een ASE te maken.


## <a name="create-an-ase-by-itself"></a>Zelf een ASE maken

Als u een zelfstandige ASE maakt, heeft deze niets. Een lege ASE maakt nog steeds een maandelijkse kosten voor de infra structuur. Volg deze stappen om een ASE te maken met een ILB of om een ASE in een eigen resource groep te maken. Nadat u uw ASE hebt gemaakt, kunt u er apps in maken met behulp van het normale proces. Selecteer de nieuwe ASE als de locatie.

1. Zoek in de Azure Marketplace naar **app service Environment** of selecteer **een resource maken**  >  **Web mobiele**  >  **app service Environment**. 

1. Voer de naam van uw ASE in. Deze naam wordt gebruikt voor de apps die in de ASE zijn gemaakt. Als de naam *mynewdemoase* is, is de naam van het subdomein *. mynewdemoase.p.azurewebsites.net*. Als u een app met de naam *mytestapp* maakt, is deze beschikbaar op mytestapp.mynewdemoase.p.azurewebsites.net. U kunt geen witruimte gebruiken in de naam. Als u hoofd letters gebruikt, is de domein naam de totale kleine letters versie van de naam. Als u een ILB gebruikt, wordt uw ASE-naam niet gebruikt in uw subdomein, maar wordt in plaats daarvan expliciet aangegeven tijdens het maken van ASE.

    ![Naam van ASE][5]

1. Selecteer uw abonnement. Dit abonnement is ook de versie die door alle apps in het ASE wordt gebruikt. U kunt uw ASE niet in een VNet zetten dat zich in een ander abonnement beneemt.

1. Selecteer een nieuwe resource groep of geef deze op. De resource groep die wordt gebruikt voor uw ASE moet overeenkomen met die voor uw VNet. Als u een bestaand VNet selecteert, wordt de selectie van de resource groep voor uw ASE bijgewerkt zodat deze overeenkomt met die van uw VNet. *Als u een resource manager-sjabloon gebruikt, kunt u een ASE maken met een resource groep die afwijkt van de VNet-resource groep.* Zie [een app service omgeving maken op basis van een sjabloon][MakeASEfromTemplate]om een ASE te maken op basis van een sjabloon.

    ![Resourcegroep selecteren][6]

1. Selecteer uw VNet en locatie. U kunt een nieuw VNet maken of een bestaand VNet selecteren: 

    * Als u een nieuw VNet selecteert, kunt u een naam en locatie opgeven. 
    
    * Het nieuwe VNet heeft het adres bereik 192.168.250.0/23 en een subnet met de naam default. Het subnet is gedefinieerd als 192.168.250.0/24. U kunt alleen een resource manager-VNet selecteren. De selectie van het **VIP-type** bepaalt of uw ASE rechtstreeks toegankelijk is vanaf internet (extern) of dat er een ILB wordt gebruikt. Zie [een interne Load Balancer maken en gebruiken met een app service omgeving][MakeILBASE]voor meer informatie over deze opties. 

      * Als u **extern** selecteert voor het **VIP-type**, kunt u selecteren hoeveel externe IP-adressen het systeem moet maken voor op IP gebaseerde SSL-doel einden. 
    
      * Als u **intern** selecteert voor het **VIP-type**, moet u het domein opgeven dat door uw ASE wordt gebruikt. U kunt een ASE implementeren in een VNet dat gebruikmaakt van open bare of particuliere adresbereiken. Als u een VNet met een openbaar adres bereik wilt gebruiken, moet u het VNet vooraf van de tijd maken. 
    
    * Als u een bestaand VNet selecteert, wordt er een nieuw subnet gemaakt wanneer de ASE wordt gemaakt. *U kunt een vooraf gemaakt subnet niet gebruiken in de portal. Als u een resource manager-sjabloon gebruikt, kunt u een ASE maken met een bestaand subnet.* Zie [een app service Environment maken op basis van een sjabloon][MakeASEfromTemplate]om een ASE te maken op basis van een sjabloon.

## <a name="app-service-environment-v1"></a>App Service-omgeving v1

U kunt nog steeds exemplaren maken van de eerste versie van App Service Environment (ASEv1). Zoek op Marketplace naar **app service Environment v1** om dat proces te starten. U maakt het ASE op dezelfde manier als u de zelfstandige ASE maakt. Wanneer de ASEv1 is voltooid, zijn er twee front-ends en twee werk rollen. Met ASEv1 moet u de front-ends en werk rollen beheren. Ze worden niet automatisch toegevoegd wanneer u uw App Service-abonnementen maakt. De front-ends fungeren als de HTTP/HTTPS-eind punten en verzenden verkeer naar de werk nemers. De werk nemers zijn de rollen die als host fungeren voor uw apps. U kunt de hoeveelheid front-ends en werk nemers aanpassen nadat u uw ASE hebt gemaakt. 

Zie [Introduction to the app service Environment v1][ASEv1Intro](Engelstalig) voor meer informatie over ASEv1. Zie [How to Configure an app service Environment][ConfigureASEv1](Engelstalig) voor meer informatie over het schalen, beheren en bewaken van ASEv1.

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[9]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
