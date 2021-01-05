---
title: Vouw de Azure VMware-oplossing uit via CloudSimple Private Cloud
description: Hierin wordt beschreven hoe u een bestaande CloudSimple-Privécloud uitbreidt om capaciteit toe te voegen aan een bestaand of nieuw cluster
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c469ea3f104e71eb80010d4a5cd421eab6184fcf
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895853"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Een CloudSimple-Privécloud uitbreiden

CloudSimple biedt de flexibiliteit voor het dynamisch uitbreiden van een Privécloud. U kunt beginnen met een kleinere configuratie en vervolgens uitbreiden naarmate u meer capaciteit nodig hebt. U kunt ook een Privécloud maken op basis van huidige behoeften en vervolgens uitvouwen naarmate het verbruik groeit.

Een Privécloud bestaat uit een of meer vSphere-clusters. Elk cluster kan 3 tot 16 knoop punten bevatten.  Wanneer u een Privécloud uitbreidt, voegt u knoop punten toe aan het bestaande cluster of maakt u een nieuw cluster. Als u een bestaand cluster wilt uitbreiden, moeten extra knoop punten van hetzelfde type (SKU) zijn als de bestaande knoop punten. Voor het maken van een nieuw cluster kunnen de knoop punten van een ander type zijn. Zie voor meer informatie over limieten voor Privécloud de sectie limieten in het [overzichts artikel CloudSimple Private Cloud](cloudsimple-private-cloud.md) .

Een privécloud wordt gemaakt met een standaard **datacenter** op vCenter.  Elk Data Center fungeert als een beheer entiteit op het hoogste niveau.  Voor een nieuw cluster biedt CloudSimple de mogelijkheid om toe te voegen aan het bestaande Data Center of een nieuw Data Center te maken.

Als onderdeel van de nieuwe cluster configuratie, configureert CloudSimple de VMware-infra structuur.  De instellingen omvatten opslag instellingen voor vSAN-schijf groepen, VMware-hoge Beschik baarheid en gedistribueerde resource planner (DRS).

Een Privécloud kan meerdere keren worden uitgebreid. Uitbrei ding kan alleen worden uitgevoerd als u binnen de algehele knooppunt limieten blijft. Telkens wanneer u een Privécloud uitbreidt die u toevoegt aan het bestaande cluster of een nieuwe maakt.

## <a name="before-you-begin"></a>Voordat u begint

Knoop punten moeten worden ingericht voordat u uw Privécloud kunt uitbreiden.  Zie voor meer informatie over het inrichten van knoop punten [richt knooppunten inrichten voor VMware-oplossing door CloudSimple-Azure-](create-nodes.md) artikel.  Voor het maken van een nieuw cluster moeten er ten minste drie beschik bare knoop punten van dezelfde SKU zijn.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-a-private-cloud"></a>Een privécloud uitbreiden

1. [Toegang tot de CloudSimple-Portal](access-cloudsimple-portal.md).

2. Open de pagina **resources** en selecteer de privécloud die u wilt uitbreiden.

3. Klik in de sectie samen vatting op **uitvouwen**.

    ![Open de privécloud](media/resources-expand-private-cloud.png)

4. Kies of u uw bestaande cluster wilt uitbreiden of een nieuw vSphere-cluster wilt maken. Wanneer u wijzigingen aanbrengt, wordt de samenvattings informatie op de pagina bijgewerkt.

    * Als u uw bestaande cluster wilt uitbreiden, klikt u op **bestaand cluster uitvouwen**. Selecteer het cluster dat u wilt uitbreiden en voer het aantal knoop punten in dat u wilt toevoegen. Elk cluster kan Maxi maal 16 knoop punten bevatten.
    * Klik op **Nieuw cluster maken** om een nieuw cluster toe te voegen. Voer een naam in voor het cluster. Selecteer een bestaand Data Center of voer een naam in om een nieuw Data Center te maken. Kies het knooppunt type. U kunt een ander type knoop punt kiezen bij het maken van een nieuw vSphere-cluster, maar niet bij het uitbreiden van een bestaand vSphere-cluster. Selecteer het aantal knoop punten. Elk nieuw cluster moet ten minste drie knoop punten hebben.

    ![Open de privécloud en voeg knoop punten toe](media/resources-expand-private-cloud-add-nodes.png)

5. Klik op **verzenden** om de privécloud uit te vouwen.

## <a name="next-steps"></a>Volgende stappen

* [VMware-VM's in Azure gebruiken](quickstart-create-vmware-virtual-machine.md)
* Meer informatie over [persoonlijke Clouds](cloudsimple-private-cloud.md)