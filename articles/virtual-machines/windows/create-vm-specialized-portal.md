---
title: Een Windows-VM maken op basis van een gespecialiseerde VHD in de Azure Portal
description: Maak een nieuwe Windows-VM op basis van een VHD in het Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/18/2019
ms.author: cynthn
ms.openlocfilehash: a93ab207960133ecbf5a68927c5d16a6daf94fd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562211"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Een virtuele machine maken op basis van een VHD met behulp van de Azure Portal

Er zijn verschillende manieren om een virtuele machine (VM) in azure te maken: 

- Als u al een virtuele harde schijf (VHD) hebt die u kunt gebruiken of als u de VHD wilt kopiëren van een bestaande virtuele machine om te gebruiken, maakt u een nieuwe virtuele machine door de VHD aan de nieuwe virtuele machine toe te *voegen* als een besturingssysteem schijf. 

- U kunt een nieuwe virtuele machine maken op basis van de VHD van een virtuele machine die is verwijderd. Als u bijvoorbeeld een virtuele machine van Azure hebt die niet correct werkt, kunt u de virtuele machine verwijderen en de VHD gebruiken om een nieuwe virtuele machine te maken. U kunt dezelfde VHD opnieuw gebruiken of een kopie van de VHD maken door een moment opname te maken en vervolgens een nieuwe beheerde schijf te maken op basis van de moment opname. Hoewel het maken van een moment opname een paar stappen onderneemt, wordt de oorspronkelijke VHD bewaard en krijgt u een terugval.

- Maak een klassieke virtuele machine en gebruik de VHD om een nieuwe VM te maken die gebruikmaakt van het Resource Manager-implementatie model en Managed disks. Voor de beste resultaten **stopt** u de klassieke vm in de Azure Portal voordat u de moment opname maakt.
 
- U kunt een virtuele machine van Azure maken op basis van een on-premises VHD door de on-premises VHD te uploaden en deze te koppelen aan een nieuwe virtuele machine. U kunt Power shell of een ander hulp programma gebruiken om de VHD te uploaden naar een opslag account en vervolgens een beheerde schijf te maken op basis van de VHD. Zie [een speciale VHD uploaden](create-vm-specialized.md#option-2-upload-a-specialized-vhd)voor meer informatie. 

> [!IMPORTANT]
> 
> Wanneer u een speciale schijf gebruikt om een nieuwe virtuele machine te maken, behoudt de nieuwe VM de computer naam van de oorspronkelijke VM. Andere computerspecifieke informatie (bijvoorbeeld CMID) wordt ook bewaard en, in sommige gevallen, kan deze dubbele informatie problemen veroorzaken. Wanneer u een virtuele machine kopieert, moet u zich bewust zijn van de typen computerspecifieke informatie die uw toepassingen gebruiken.  
> Gebruik daarom geen gespecialiseerde schijf als u meerdere Vm's wilt maken. Maak in plaats daarvan [een installatie kopie](capture-image-resource.md) voor grotere implementaties en [gebruik vervolgens de installatie kopie om meerdere vm's te maken](create-vm-generalized-managed.md).

We raden u aan om het aantal gelijktijdige implementaties te beperken tot 20 virtuele machines van één moment opname of VHD. 

## <a name="copy-a-disk"></a>Een schijf kopiëren

Maak een moment opname en maak een schijf van de moment opname. Met deze strategie kunt u de oorspronkelijke VHD als terugval gebruiken:

1. Selecteer in de [Azure Portal](https://portal.azure.com)in het linkermenu **alle services**.
2. In het zoekvak **alle services** voert u **schijven** in en selecteert u vervolgens **schijven** om de lijst met beschik bare schijven weer te geven.
3. Selecteer de schijf die u wilt gebruiken. De **schijf** pagina voor die schijf wordt weer gegeven.
4. Selecteer in het menu bovenaan de optie **moment opname maken**. 
5. Voer een **naam** in voor de moment opname.
6. Kies een **resource groep** voor de moment opname. U kunt een bestaande resource groep gebruiken of een nieuwe maken.
7. Voor **account type** kiest u **Standard-(HDD)** of **Premium-opslag (SSD)** .
8. Wanneer u klaar bent, selecteert u **maken** om de moment opname te maken.
9. Nadat de moment opname is gemaakt, selecteert u **een resource maken** in het menu links.
10. In het zoekvak voert u **Managed Disk** in en selecteert u **Managed disks** in de lijst.
11. Selecteer op de pagina **Managed disks** **maken**.
12. Voer een **naam** in voor de schijf.
13. Kies een **resource groep** voor de schijf. U kunt een bestaande resource groep gebruiken of een nieuwe maken. Deze selectie wordt ook gebruikt als de resource groep waar u de virtuele machine maakt op basis van de schijf.
14. Voor **account type** kiest u **Standard-(HDD)** of **Premium-opslag (SSD)** .
15. Zorg ervoor dat in **bron type** **moment opname** is geselecteerd.
16. Selecteer in de vervolg keuzelijst **bron momentopname** de moment opname die u wilt gebruiken.
17. Breng eventuele andere aanpassingen aan en selecteer vervolgens **maken** om de schijf te maken.

## <a name="create-a-vm-from-a-disk"></a>Een virtuele machine maken op basis van een schijf

Nadat u de virtuele harde schijf hebt die u wilt gebruiken, kunt u de virtuele machine maken in de portal:

1. Selecteer in de [Azure Portal](https://portal.azure.com)in het linkermenu **alle services**.
2. In het zoekvak **alle services** voert u **schijven** in en selecteert u vervolgens **schijven** om de lijst met beschik bare schijven weer te geven.
3. Selecteer de schijf die u wilt gebruiken. De **schijf** pagina voor die schijf wordt geopend.
4. Controleer op de pagina **overzicht** of de **status** van de schijf niet- **gekoppeld** is. Als dat niet het geval is, moet u mogelijk de schijf loskoppelen van de virtuele machine of de virtuele machine verwijderen om de schijf vrij te maken.
4. Selecteer in het menu boven aan de pagina **VM maken**.
5. Voer op de pagina **basis beginselen** voor de nieuwe virtuele **machine de naam** van een VM in en selecteer een bestaande **resource groep** of maak een nieuwe.
6. Voor **grootte** selecteert u **grootte wijzigen** voor toegang tot de pagina **grootte** .
7. Selecteer een rij met de VM-grootte en kies vervolgens **selecteren**.
8. Op de pagina **netwerk** kunt u de portal alle nieuwe resources laten maken of u kunt een bestaand **virtueel netwerk** en een **netwerk beveiligings groep** selecteren. De portal maakt altijd een nieuwe netwerk interface en een openbaar IP-adres voor de nieuwe virtuele machine. 
9. Breng wijzigingen aan in de bewakings opties op de pagina **beheer** .
10. Voeg op de pagina **gast configuratie** alle gewenste uitbrei dingen toe.
11. Selecteer als u klaar bent de optie **Beoordelen en maken**. 
12. Als de VM-configuratie wordt gevalideerd, selecteert u **maken** om de implementatie te starten.


## <a name="next-steps"></a>Volgende stappen

U kunt ook Power shell gebruiken om [een VHD te uploaden naar Azure en een gespecialiseerde virtuele machine te maken](create-vm-specialized.md).


