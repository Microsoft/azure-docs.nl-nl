---
title: Een galerie met gedeelde Azure-afbeeldingen maken met behulp van de portal
description: Meer informatie over het gebruik van Azure Portal voor het maken en delen van installatie kopieën van virtuele machines.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.openlocfilehash: e28a1412fa417b9f15ab7a6f79305d07ca6d9fec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556091"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>Een galerie met gedeelde Azure-afbeeldingen maken met behulp van de portal

Met een [Shared Image Gallery](../shared-image-galleries.md) kunt u het delen van aangepaste installatiekopieën in uw organisatie vereenvoudigen. Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatie kopieën kunnen worden gebruikt om implementatie taken te Boots trapn, zoals het vooraf laden van toepassingen, toepassings configuraties en andere besturingssysteem configuraties. 

Met de galerie voor gedeelde afbeeldingen kunt u uw aangepaste VM-installatie kopieën delen met anderen in uw organisatie, binnen of tussen verschillende regio's binnen een AAD-Tenant. U kunt kiezen welke installatiekopieën u wilt delen, in welke regio’s u ze beschikbaar wilt maken en met wie u ze wilt delen. U kunt meerdere galerieën maken zodat u gedeelde installatie kopieën logisch kunt groeperen. 

De galerie is een resource op het hoogste niveau die volledige toegangs beheer op basis van rollen (Azure RBAC) biedt. Installatie kopieën kunnen versie nummer hebben en u kunt ervoor kiezen om elke installatie kopie versie te repliceren naar een andere set Azure-regio's. De galerie werkt alleen met beheerde installatie kopieën.

De functie gedeelde installatie kopie galerie heeft meerdere bron typen. We gebruiken of maken deze in dit artikel:


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

<br>


Wanneer u dit artikel doorwerkt, moet u de namen van de resource groep en de VM vervangen, indien nodig.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms"></a>Virtuele machines maken

U kunt nu een of meer nieuwe virtuele machines maken. In dit voor beeld wordt een virtuele machine gemaakt met de naam *myVM*, in de *myResourceGroup*, in het *VS-Oost* -Data Center.

1. Ga naar de definitie van uw installatie kopie. U kunt het resource filter gebruiken om alle beschik bare afbeeldings definities weer te geven.
1. Selecteer op de pagina voor de definitie van de installatie kopie de optie **VM maken** in het menu boven aan de pagina.
1. Selecteer voor **resource groep** de optie **nieuwe maken** en typ *myResourceGroup* voor de naam.
1. Typ *myVM* in de naam van de **virtuele machine**.
1. Selecteer bij **Regio** *VS - oost*.
1. Voor **beschikbaarheids opties** moet u de standaard waarde voor de *redundantie van de infra structuur niet* behouden.
1. De waarde voor de **installatie kopie** wordt automatisch gevuld met de versie van de `latest` installatie kopie als u bent begonnen met de pagina voor de definitie van de installatie kopie.
1. Kies bij **grootte** een VM-grootte in de lijst met beschik bare grootten en kies vervolgens **selecteren**.
1. Als de installatie kopie is gegeneraliseerd onder **Administrator-account**, moet u een gebruikers naam opgeven, zoals *azureuser* en een wacht woord. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](faq.md#what-are-the-password-requirements-when-creating-a-vm). Als uw installatie kopie gespecialiseerd was, worden de velden gebruikers naam en wacht woord grijs weer gegeven, omdat de gebruikers naam en het wacht woord voor de bron-VM worden gebruikt.
1. Als u externe toegang tot de virtuele machine wilt toestaan, klikt u onder **open bare binnenkomende poorten** op **geselecteerde poorten toestaan** en selecteert u vervolgens **RDP (3389)** in de vervolg keuzelijst. Als u externe toegang tot de virtuele machine niet wilt toestaan, laat u **niets** geselecteerd voor **open bare binnenkomende poorten**.
1. Wanneer u klaar bent, selecteert u de knop **controleren + maken** onder aan de pagina.
1. Nadat de validatie van de virtuele machine is geslaagd, selecteert u onder aan de pagina **maken** om de implementatie te starten.


## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de VM niet meer nodig hebt, kunt u de resourcegroep, de machine zelf én alle gerelateerde resources verwijderen. Wanneer u dit wilt doen, selecteert u de resourcegroep voor de virtuele machine en selecteert u **Verwijderen**. Vervolgens bevestigt u de naam van de resourcegroep die u wilt verwijderen.

Als u afzonderlijke resources wilt verwijderen, moet u deze in omgekeerde volg orde verwijderen. Als u bijvoorbeeld een definitie van een installatie kopie wilt verwijderen, moet u alle installatie kopieën verwijderen die zijn gemaakt op basis van die installatie kopie.

## <a name="next-steps"></a>Volgende stappen

U kunt ook een resource voor de galerie met gedeelde afbeeldingen maken met behulp van sjablonen. Er zijn verschillende Azure-quickstart-sjablonen beschikbaar: 

- [Een gedeelde installatiekopiegalerie maken](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Een installatiekopiedefinitie maken in een gedeelde installatiekopiegalerie](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Een installatiekopieversie maken in een gedeelde installatiekopiegalerie](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Een VM maken van een installatiekopieversie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Zie het [overzicht](../shared-image-galleries.md)voor meer informatie over gedeelde afbeeldings galerieën. Als u problemen ondervindt, raadpleegt u [problemen met de galerie met gedeelde afbeeldingen oplossen](../troubleshooting-shared-images.md).