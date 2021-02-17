---
title: Een App Service Environment maken
description: Meer informatie over het maken van een App Service Environment.
author: ccompy
ms.assetid: 7690d846-8da3-4692-8647-0bf5adfd862a
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 021c85037ea105ff7d8ff642e3a9f28ed3ebe991
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594097"
---
# <a name="create-an-app-service-environment"></a>Een App Service Environment maken

> [!NOTE]
> Dit artikel heeft betrekking op de App Service Environment v3 (preview-versie)
> 

De [app service Environment (ASE)][Intro] is een implementatie van één Tenant van de app service die in uw Azure Virtual Network (VNet) wordt ingevoerd.  ASEv3 biedt alleen ondersteuning voor het beschikbaar maken van apps op een privé adres in uw Vnet. Wanneer een ASEv3 wordt gemaakt tijdens de preview, worden deze resources toegevoegd aan uw abonnement.

- App Service-omgeving
- Privé-eindpunt

Voor een implementatie van een ASE is het gebruik van twee subnetten vereist.  Het persoonlijke eind punt bevindt zich in één subnet.  Dit subnet kan worden gebruikt voor andere zaken, zoals Vm's.  Het andere subnet wordt gebruikt voor uitgaande oproepen van de ASE.  Dit subnet kan niet worden gebruikt voor iets anders dan de ASE. 

## <a name="before-you-create-your-ase"></a>Voordat u uw ASE maakt

Nadat uw ASE is gemaakt, kunt u deze niet meer wijzigen:

- Locatie
- Abonnement
- Resourcegroep
- Azure Virtual Network (VNet) gebruikt
- Gebruikte subnetten
- Grootte van subnet
- De naam van uw ASE

Het uitgaande subnet moet groot genoeg zijn om de maximale grootte van uw ASE te kunnen bevatten. Kies een groot genoeg subnet ter ondersteuning van uw maximale schaal behoeften, omdat deze niet kan worden gewijzigd nadat het is gemaakt. De aanbevolen grootte is a/24 met 256 adressen.

Nadat de ASE is gemaakt, kunt u er apps aan toevoegen. Als uw ASEv3 geen App Service-abonnementen bevat, worden er kosten in rekening gebracht alsof u één exemplaar van een I1v2-abonnement hebt App Service in uw ASE.  

De ASEv3 wordt alleen aangeboden in regio's selecteren. Er worden meer regio's toegevoegd, omdat de preview bij GA. 

## <a name="creating-an-ase-in-the-portal"></a>Een ASE maken in de portal

1. Als u een ASEv3 wilt maken, zoekt u op de Marketplace naar **app service Environment (preview)**.  
2. Basis beginselen: Selecteer het abonnement, selecteer of maak de resource groep en voer de naam van uw ASE in.  De naam van de ASE wordt ook gebruikt voor het domein achtervoegsel van uw ASE.  Als uw ASE-naam *Contoso* is, wordt het domein achtervoegsel *contoso.appserviceenvironment.net*.  Deze naam wordt automatisch ingesteld in uw Azure DNS particuliere zone die wordt gebruikt door het Vnet waarin de ASE wordt geïmplementeerd. 

    ![App Service Environment tabblad basis principes maken](./media/creation/creation-basics.png)

3. Hosten: Selecteer besturings systeem voorkeur, implementatie van hostgroep. De besturingssysteem voorkeur geeft aan welk besturings systeem u in eerste instantie wilt gebruiken voor uw apps in deze ASE. U kunt apps van het andere besturings systeem toevoegen nadat de ASE is gemaakt. Implementatie van hostgroep wordt gebruikt voor het selecteren van specifieke hardware. Met ASEv3 kunt u ingeschakeld en vervolgens land op toegewezen hardware selecteren. Er worden kosten in rekening gebracht voor de hele exclusieve host met het maken van ASE en vervolgens een gereduceerde prijs voor de exemplaren van uw App Service-abonnement. 

    ![App Service Environment hosting selecties](./media/creation/creation-hosting.png)

4. Netwerken: Selecteer of maak uw Virtual Network, selecteer of maak een binnenkomend subnet, selecteer of maak een uitgaand subnet. Elk subnet dat voor uitgaand verkeer wordt gebruikt, moet leeg zijn en worden gedelegeerd naar micro soft. Web/hostingEnvironments. Als u het subnet via de portal maakt, wordt het subnet automatisch voor u gedelegeerd.

    ![App Service Environment netwerk selecties](./media/creation/creation-networking.png)

5. Controleren en maken: Controleer of de configuratie juist is en selecteer maken. Het duurt ongeveer een uur voordat uw ASE is gemaakt. 

    ![App Service Environment controleren en maken](./media/creation/creation-review.png)

Nadat uw ASE is gemaakt, kunt u deze als locatie selecteren bij het maken van uw apps. Lees voor meer informatie over het maken van apps in uw nieuwe ASE [met behulp van een app service Environment][UsingASE]

## <a name="os-preference"></a>BESTURINGSSYSTEEM voorkeur
In een ASE kunt u Windows-apps, Linux-apps of beide hebben. In ASEv2 wordt tijdens het maken de initiële voor keur geselecteerd tijdens het maken van de infra structuur voor hoge Beschik baarheid voor dat besturings systeem. Als u apps van het andere besturings systeem wilt toevoegen, maakt u de apps normaal en selecteert u het gewenste besturings systeem. In ASEv3 heeft dit geen invloed op de werking van het back-end appreciatively.  

## <a name="dedicated-hosts"></a>Toegewezen hosts
De ASE wordt normaal gesp roken geïmplementeerd op virtuele machines die zijn ingericht op een multi tenant-Hyper Visor. Als u op speciale systemen wilt implementeren, met inbegrip van de hardware, kunt u uw ASE inrichten op toegewezen hosts. In de eerste ASEv3 preview zijn toegewezen hosts in een paar. Elke toegewezen host bevindt zich in een afzonderlijke beschikbaarheids zone, indien deze door de regio wordt toegestaan. Toegewijde ASE-implementaties op basis van een host hebben een andere prijs dan normaal. Er worden kosten in rekening gebracht voor de toegewezen host en vervolgens een andere kosten voor elk exemplaar van de App Service abonnement.  

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
