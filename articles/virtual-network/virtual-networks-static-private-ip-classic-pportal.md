---
title: Privé IP-adressen voor virtuele machines (klassiek) configureren-Azure Portal | Microsoft Docs
description: Meer informatie over het configureren van privé-IP-adressen voor virtuele machines (klassiek) met behulp van de Azure Portal.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6986f6f16cbd32d44223bba4f4be4577fa11258c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222901"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Privé IP-adressen configureren voor een virtuele machine (klassiek) met behulp van de Azure Portal

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Dit artikel is van toepassing op het klassieke implementatiemodel. U kunt ook [een statisch privé-IP-adres beheren in het Resource Manager-implementatie model](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

De voorbeeld stappen die volgen, verwachten een eenvoudige omgeving die al is gemaakt. Als u de stappen wilt uitvoeren zoals ze worden weer gegeven in dit document, bouwt u eerst de test omgeving op die wordt beschreven in [een vnet maken](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Een statisch privé-IP-adres opgeven bij het maken van een VM
Als u een virtuele machine met de naam *DNS01* in het *frontend* -subnet van een VNet met de naam *TestVNet* met een statisch privé-IP van *192.168.1.101* wilt maken, voert u de volgende stappen uit:

1. Navigeer in een browser naar https://portal.azure.com en meld u, indien nodig, aan met uw Azure-account.
2. Selecteer **Nieuw**  >  **Compute**  >  **Windows Server 2012 R2 Data Center**, Let op dat de lijst **een implementatie model selecteren** reeds **klassiek** bevat en selecteer vervolgens **maken**.
   
    ![Scherm opname van de Azure Portal met de nieuwe > Compute > Windows Server 2012 R2 Data Center-tegel gemarkeerd.](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. Voer onder **VM maken** de naam in van de virtuele machine die moet worden gemaakt (*DNS01* in het scenario), het lokale beheerders account en het wacht woord.
   
    ![Scherm afbeelding die laat zien hoe u een virtuele machine maakt door de naam van de virtuele machine, de gebruikers naam van de lokale beheerder en het wacht woord in te voeren.](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Selecteer **optioneel configuratie**  >  **netwerk**  >  **Virtual Network** en selecteer vervolgens **TestVNet**. Als **TestVNet** niet beschikbaar is, moet u ervoor zorgen dat u de centrale locatie van de *VS* gebruikt en de test omgeving hebt gemaakt die aan het begin van dit artikel is beschreven.
   
    ![Scherm opname van de optionele configuratie > netwerk > Virtual Network > TestVNet-optie gemarkeerd.](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. Zorg ervoor dat het momenteel geselecteerde subnet een front- *End* is en selecteer vervolgens **IP-adressen** onder **IP-adres toewijzing** , selecteer **statisch** en voer *192.168.1.101* in voor het **IP-adres** zoals hieronder wordt weer gegeven.
   
    ![Scherm opname van het veld IP-adressen waar u het vaste IP-adres typt.](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Selecteer **OK** onder **IP-adressen**, selecteer **OK** onder **netwerk** en selecteer **OK** onder **optionele configuratie**.
7. Selecteer bij **virtuele machine maken** de optie **maken**. De onderstaande tegel wordt weer gegeven in het dash board:
   
    ![Scherm afbeelding met de tegel Windows Server 2012 R2 Data Center maken.](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Informatie over statisch privé-IP-adres ophalen voor een virtuele machine
Voer de volgende stappen uit om de gegevens van het statische privé-IP-adres te bekijken voor de virtuele machine die is gemaakt met de bovenstaande stappen.

1. Selecteer in de Azure Portal **Bladeren alle**  >  **virtuele machines (klassiek)**  >  **DNS01**  >  **alle instellingen**  >  **IP-adressen** en noteer de IP-adres toewijzing en het IP-adres zoals hieronder wordt weer gegeven.
   
    ![Een VM maken in Azure Portal](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Een statisch privé-IP-adres uit een virtuele machine verwijderen

Onder **IP-adressen** selecteert u **dynamische** , rechts van de **toewijzing van IP-** adressen, selecteert u **Opslaan** en selecteert u vervolgens **Ja**, zoals wordt weer gegeven in de volgende afbeelding:
   
![Scherm afbeelding die laat zien hoe het statische privé-IP-adres uit een virtuele machine moet worden verwijderd door dynamische te selecteren rechts van het label voor de toewijzing van IP-adressen.](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Een statisch privé-IP-adres toevoegen aan een bestaande virtuele machine

1. Onder **IP-adressen**, zoals eerder weer gegeven, selecteert u **statisch** aan de rechter kant van de **toewijzing van IP-adressen**.
2. Typ *192.168.1.101* voor **IP-adres**, selecteer **Opslaan** en selecteer vervolgens **Ja**.

## <a name="set-ip-addresses-within-the-operating-system"></a>IP-adressen in het besturings systeem instellen

Het is raadzaam dat u het privé-IP-adres dat is toegewezen aan de virtuele machine van Azure niet statisch toewijst in het besturings systeem van een VM, tenzij dat nodig is. Als u het privé-IP-adres hand matig instelt in het besturings systeem, moet u ervoor zorgen dat het hetzelfde adres is als het privé-IP-adres dat is toegewezen aan de Azure VM, of u kunt de verbinding met de virtuele machine verliezen. U moet het openbare IP-adres dat is toegewezen aan een virtuele machine van Azure in het besturingssysteem van de virtuele machine nooit handmatig toewijzen.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [gereserveerde open bare IP-](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) adressen.
* Meer informatie over [open bare IP-adressen op exemplaar niveau (ILPIP)](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) .
* Raadpleeg de [gereserveerd IP rest-api's](/previous-versions/azure/reference/dn722420(v=azure.100)).