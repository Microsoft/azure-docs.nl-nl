---
title: Een virtuele machine in de zone maken met de Azure Portal
description: Een virtuele Windows-machine maken in een beschikbaarheids zone met de Azure Portal
documentationcenter: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 7c7f135d4033a31f855342c172d73f51478931ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102501682"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-the-azure-portal"></a>Een virtuele machine maken in een beschikbaarheids zone met behulp van de Azure Portal

In dit artikel wordt beschreven hoe u de Azure Portal gebruikt voor het maken van een virtuele machine in een Azure-beschikbaarheids zone. Een [beschikbaarheidszone](../../availability-zones/az-overview.md) is een fysiek afgescheiden zone in een Azure-regio. Gebruik beschikbaarheidszones om uw apps en gegevens te beschermen tegen het onwaarschijnlijke risico van een storing of het verloren gaan van een heel datacenter.

Als u een beschikbaarheidszone wilt gebruiken, maakt u een virtuele machine in een [ondersteunde Azure-regio](../../availability-zones/az-region.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure 

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-virtual-machine"></a>Virtuele machine maken

1. Klik in de linkerbovenhoek van Azure Portal op **Een resource maken**.

2. Selecteer **Compute** en vervolgens **Windows Server 2016 Datacenter**. 

3. Geef de informatie van de virtuele machine op. De gebruikersnaam en het wachtwoord die u hier opgeeft, worden gebruikt voor aanmelding bij de virtuele machine. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](faq.md#what-are-the-password-requirements-when-creating-a-vm). Kies een locatie zoals VS-Oost 2 die beschikbaarheids zones ondersteunt. Na het voltooien klikt u op **OK**.

    ![Voer basisinformatie over uw virtuele machine in op de portalblade](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Kies een grootte voor de virtuele machine. Selecteer een aanbevolen grootte of filter op basis van de functies. Bevestig dat de grootte beschikbaar is in de zone die u wilt gebruiken.

    ![Een VM-grootte selecteren](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. Selecteer onder **instellingen**  >  **hoge Beschik baarheid** een van de genummerde zones in de vervolg keuzelijst **beschikbaarheids zone** , behoud de resterende standaard waarden en klik op **OK**.

    ![Een beschikbaarheids zone selecteren](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Klik op de pagina samen vatting op **maken** om de implementatie van de virtuele machine te starten.

7. De VM wordt aan het dashboard van de Azure Portal vastgemaakt. Zodra de implementatie is voltooid, wordt de samenvatting van de VM automatisch geopend.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Zone voor beheerde schijf en IP-adres bevestigen

Wanneer de virtuele machine in een beschikbaarheids zone is geïmplementeerd, wordt een beheerde schijf voor de virtuele machine in dezelfde beschikbaarheids zone gemaakt. Een openbaar IP-adres wordt standaard ook in die zone gemaakt.

U kunt de zone-instellingen voor deze resources in de portal bevestigen.  

1. Klik op **resource groepen** en vervolgens op de naam van de resource groep voor de virtuele machine, zoals *myResourceGroup*.

2. Klik op de naam van de schijf bron. De pagina **overzicht** bevat details over de locatie en beschikbaarheids zone van de resource.

    ![Beschikbaarheids zone voor beheerde schijf](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Klik op de naam van de resource met het open bare IP-adres. De pagina **overzicht** bevat details over de locatie en beschikbaarheids zone van de resource.

    ![Beschikbaarheids zone voor IP-adres](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een VM maakt in een beschikbaarheidszone. Meer informatie over [Beschik baarheid](../availability.md) voor virtuele Azure-machines.