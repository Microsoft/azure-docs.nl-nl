---
title: 'Snelstartgids: VMware CloudSimple-service maken'
titleSuffix: Azure VMware Solution by CloudSimple
description: Meer informatie over het maken van de CloudSimple-service, het aanschaffen van knoop punten en het reserveren van knoop punten
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 14df0f131aaef8a4c24e2d1eb242a9b440e7c7b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86507587"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>Quick Start: een Azure VMware-oplossing maken op basis van de CloudSimple-service

Om aan de slag te gaan, maakt u de Azure VMware-oplossing door CloudSimple in de Azure Portal.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware-oplossing per CloudSimple-service-overzicht

Met de CloudSimple-service kunt u Azure VMware-oplossing gebruiken door CloudSimple.  Door de service te maken, kunt u knoop punten inrichten, knoop punten reserveren en persoonlijke clouds maken.  U voegt de CloudSimple-service toe aan elke Azure-regio waar de CloudSimple-service beschikbaar is.  De service definieert het Edge-netwerk van de Azure VMware-oplossing door CloudSimple.  Dit Edge-netwerk wordt gebruikt voor services die VPN, ExpressRoute en Internet connectiviteit met uw persoonlijke Clouds bevatten.

U moet een gateway-subnet maken om de CloudSimple-service toe te voegen. Het gateway-subnet wordt gebruikt bij het maken van het Edge-netwerk en vereist een/28 CIDR-blok. De adres ruimte van het gateway-subnet moet uniek zijn. Het mag niet overlappen met een van uw on-premises netwerk adres ruimten of de adres ruimte van het virtuele Azure-netwerk.

## <a name="before-you-begin"></a>Voordat u begint

Een/28 CIDR-blok voor gateway-subnet toewijzen.  Een gateway-subnet is vereist per CloudSimple-service en is uniek voor de regio waarin het is gemaakt. Het gateway-subnet wordt gebruikt voor de Azure VMware-oplossing door CloudSimple Edge-netwerk services en vereist een/28 CIDR-blok. De adres ruimte van het gateway-subnet moet uniek zijn. Het mag niet overlappen met een netwerk dat communiceert met de CloudSimple-omgeving.  De netwerken die met CloudSimple communiceren, zijn onder andere on-premises netwerken en Azure Virtual Networks.

Controleer de [netwerk vereisten](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>De service maken

1. Selecteer **Alle services**.
2. Zoek naar de **CloudSimple-service**.

    ![CloudSimple-service zoeken](media/create-cloudsimple-service-search.png)

3. Selecteer **CloudSimple Services**.
4. Klik op **toevoegen** om een nieuwe service te maken.

    ![CloudSimple-service toevoegen](media/create-cloudsimple-service-add.png)

5. Selecteer het abonnement waar u de CloudSimple-service wilt maken.
6. Selecteer de resource groep voor de service. Klik op **nieuwe maken** om een nieuwe resource groep toe te voegen.
7. Voer een naam in om de service te identificeren.
8. Voer de CIDR in voor de service gateway. Geef een/28-subnet op dat niet overlapt met een van uw on-premises subnetten, Azure-subnetten of geplande CloudSimple-subnetten. U kunt de CIDR niet wijzigen nadat de service is gemaakt.

    ![De CloudSimple-service maken](media/create-cloudsimple-service.png)

9. Klik op **OK**.

De service wordt gemaakt en toegevoegd aan de lijst met Services.

## <a name="provision-nodes"></a>Knooppunten inrichten

Als u betalen naar gebruik-capaciteit wilt instellen voor een CloudSimple Privécloud, moet u eerst knoop punten inrichten in de Azure Portal.

1. Selecteer **Alle services**.
2. Zoek naar **CloudSimple-knoop punten**.

    ![CloudSimple-knoop punten zoeken](media/create-cloudsimple-node-search.png)

3. Selecteer **CloudSimple-knoop punten**.
4. Klik op **toevoegen** om knoop punten te maken.

    ![CloudSimple-knoop punten toevoegen](media/create-cloudsimple-node-add.png)

5. Selecteer het abonnement waar u de CloudSimple-knoop punten wilt inrichten.
6. Selecteer de resource groep voor de knoop punten. Klik op **nieuwe maken** om een nieuwe resource groep toe te voegen.
7. Voer het voor voegsel in om de knoop punten te identificeren.
8. Selecteer de locatie voor de knooppunt resources.
9. Selecteer de toegewezen locatie om de knooppunt resources te hosten.
10. Selecteer het [knooppunt type](cloudsimple-node.md).
11. Selecteer het aantal knoop punten dat moet worden ingericht.
12. Selecteer **Controleren + maken**.
13. Controleer de instellingen. Als u instellingen wilt wijzigen, klikt u op **vorige**.
14. Selecteer **Maken**.

## <a name="next-steps"></a>Volgende stappen

* [Een Privécloud maken en omgeving configureren](quickstart-create-private-cloud.md)
* Meer informatie over de [CloudSimple-service](./cloudsimple-service.md)
