---
title: Meerdere IP-adressen voor virtuele Azure-machines - Portal | Microsoft Docs
description: Meer informatie over het toewijzen van meerdere IP-adressen aan een virtuele machine met behulp van de Azure Portal | Resource Manager.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: allensu
ms.openlocfilehash: 050d3ac23562e7822d186a16675d03c1b9dc670b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739202"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Wijs meerdere IP-adressen toe aan virtuele machines met behulp van Azure Portal

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> In dit artikel wordt uitgelegd hoe u een virtuele machine (VM) maakt via het Azure Resource Manager implementatiemodel met behulp van Azure Portal. Meerdere IP-adressen kunnen niet worden toegewezen aan resources die zijn gemaakt via het klassieke implementatiemodel. Lees het artikel Implementatiemodellen begrijpen voor meer informatie over [Azure-implementatiemodellen.](../azure-resource-manager/management/deployment-models.md)

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Een virtuele machine met meerdere IP-adressen maken

Als u een VM met meerdere IP-adressen of een statisch privé-IP-adres wilt maken, moet u deze maken met behulp van PowerShell of de Azure CLI. Klik op de PowerShell- of CLI-opties bovenaan dit artikel voor meer informatie. U kunt een VM maken met één dynamisch privé-IP-adres en (optioneel) één openbaar IP-adres. Volg de stappen in de artikelen Een [windows-VM](../virtual-machines/windows/quick-create-portal.md) maken of Een [linux-VM](../virtual-machines/linux/quick-create-portal.md) maken in de portal. Nadat u de VM hebt gemaakt, kunt u het IP-adrestype wijzigen van dynamisch in statisch en extra IP-adressen toevoegen met behulp van de portal door de stappen in de sectie IP-adressen toevoegen aan een [VM](#add) van dit artikel te volgen.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>IP-adressen toevoegen aan een VM

U kunt privé- en openbare IP-adressen toevoegen aan een Azure-netwerkinterface door de volgende stappen uit te voeren. In de voorbeelden in de volgende secties wordt ervan uitgenomen dat u al een VM hebt met de drie IP-configuraties die in het [scenario](#scenario)worden beschreven, maar dit is niet vereist.

### <a name="core-steps"></a><a name="coreadd"></a>Belangrijkste stappen

1. Blader naar de Azure Portal https://portal.azure.com op en meld u aan, indien nodig.
2. Klik in de portal op **Meer services >** virtuele machines in *het* filtervak en klik vervolgens op **Virtuele machines.**
3. Klik in **het deelvenster Virtuele machines** op de VM waar u IP-adressen aan wilt toevoegen. **Navigeer naar het** tabblad Netwerken. Klik op **Netwerkinterface** op de pagina. Zoals weergegeven in de onderstaande afbeelding: 


    ![Een openbaar IP-adres toevoegen aan een VM](./media/virtual-network-multiple-ip-addresses-portal/figure200319.png)
4. Klik in **het deelvenster** Netwerkinterface op de **IP-configuraties.**

5. Klik in het deelvenster dat wordt weergegeven voor de NIC die u hebt geselecteerd op **IP-configuraties.** Klik **op** Toevoegen, voltooi de stappen in een van de volgende secties, op basis van het type IP-adres dat u wilt toevoegen, en klik vervolgens op **OK.** 

### <a name="add-a-private-ip-address"></a>Een privé-IP-adres toevoegen

Voltooi de volgende stappen om een nieuw privé-IP-adres toe te voegen:

1. Voltooi de stappen in de [sectie Kernstappen](#coreadd) van dit artikel en zorg ervoor dat u zich in de **sectie IP-configuraties** van de VM-netwerkinterface.  Controleer het subnet dat als standaard wordt weergegeven (bijvoorbeeld 10.0.0.0/24).
2. Klik op **Add**. Maak in het **deelvenster IP-configuratie** toevoegen dat wordt weergegeven een IP-configuratie  met de naam *IPConfig-4* met een nieuw statisch privé-IP-adres door een nieuw nummer te kiezen voor het uiteindelijke octet en klik vervolgens op **OK**.  (Voor het subnet 10.0.0.0/24 is *een voorbeeld-IP-adres 10.0.0.7*.)

    > [!NOTE]
    > Wanneer u een statisch IP-adres toevoegt, moet u een niet-gebruikt, geldig adres opgeven op het subnet waar de NIC mee is verbonden. Als het adres dat u selecteert niet beschikbaar is, wordt in de portal een X voor het IP-adres weergegeven en moet u een ander adres selecteren.

3. Nadat u op OK hebt geklikt, wordt het deelvenster gesloten en ziet u de nieuwe IP-configuratie. Klik **op OK** om het deelvenster **IP-configuratie toevoegen te** sluiten.
4. U kunt op **Toevoegen klikken om** extra IP-configuraties toe te voegen of alle geopende blades sluiten om het toevoegen van IP-adressen te voltooien.
5. Voeg de privé-IP-adressen toe aan het besturingssysteem van de VM door de stappen in de sectie IP-adressen toevoegen aan een [VM-besturingssysteem](#os-config) van dit artikel uit te voeren.

### <a name="add-a-public-ip-address"></a>Een openbaar IP-adres toevoegen

Een openbaar IP-adres wordt toegevoegd door een openbare IP-adresresource te koppelen aan een nieuwe IP-configuratie of een bestaande IP-configuratie.

> [!NOTE]
> Openbare IP-adressen hebben een nominaal bedrag. Lees de pagina met prijzen van IP-adressen voor meer informatie over [prijzen voor IP-adressen.](https://azure.microsoft.com/pricing/details/ip-addresses) Er geldt een limiet voor het aantal openbare IP-adressen dat in een abonnement kan worden gebruikt. Lees voor meer informatie over de limieten het artikel [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
> 

### <a name="create-a-public-ip-address-resource"></a><a name="create-public-ip"></a>Een openbare IP-adresresource maken

Een openbaar IP-adres is één instelling voor een resource voor een openbaar IP-adres. Als u een resource voor een openbaar IP-adres hebt die momenteel niet is gekoppeld aan een IP-configuratie die u wilt koppelen aan een IP-configuratie, slaat u de volgende stappen over en voltooit u de stappen in een van de volgende secties, indien nodig. Als u geen beschikbare resource voor een openbaar IP-adres hebt, moet u de volgende stappen voltooien om er een te maken:

1. Blader naar de Azure Portal https://portal.azure.com op en meld u aan, indien nodig.
3. Klik in de portal op **Een resource maken Openbaar**  >  **IP-adres** voor  >  **netwerken.**
4. Voer in het deelvenster Openbaar **IP-adres** maken dat wordt weergegeven een naam in, selecteer een type **IP-adrestoewijzing,** een abonnement, een **resourcegroep** en een locatie en klik vervolgens op **Maken,** zoals wordt weergegeven in de volgende afbeelding:  

    ![Een resource voor een openbaar IP-adres maken](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Voltooi de stappen in een van de volgende secties om de resource van het openbare IP-adres te koppelen aan een IP-configuratie.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>De resource van het openbare IP-adres koppelen aan een nieuwe IP-configuratie

1. Voltooi de stappen in de [sectie Kernstappen](#coreadd) van dit artikel.
2. Klik op **Add**. Maak in **het deelvenster IP-configuratie** toevoegen dat wordt weergegeven een IP-configuratie met de *naam IPConfig-4.* Schakel het **openbare IP-adres in** en selecteer een bestaande, beschikbare openbare IP-adresresource in het deelvenster Openbaar **IP-adres** kiezen dat wordt weergegeven.

    Nadat u de resource voor het openbare IP-adres hebt geselecteerd, klikt u op **OK** en wordt het deelvenster gesloten. Als u geen bestaand openbaar IP-adres hebt, kunt u er een maken door de stappen in de sectie Een resource voor een openbaar [IP-adres](#create-public-ip) maken van dit artikel uit te voeren. 

3. Controleer de nieuwe IP-configuratie. Hoewel een privé-IP-adres niet expliciet is toegewezen, werd er automatisch een toegewezen aan de IP-configuratie, omdat alle IP-configuraties een privé-IP-adres moeten hebben.
4. U kunt op **Toevoegen klikken om** extra IP-configuraties toe te voegen of alle geopende blades sluiten om het toevoegen van IP-adressen te voltooien.
5. Voeg het privé-IP-adres toe aan het VM-besturingssysteem door de stappen voor uw besturingssysteem uit te voeren in de sectie IP-adressen toevoegen aan een [VM-besturingssysteem](#os-config) van dit artikel. Voeg het openbare IP-adres niet toe aan het besturingssysteem.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>De resource van het openbare IP-adres koppelen aan een bestaande IP-configuratie

1. Voltooi de stappen in de [sectie Kernstappen](#coreadd) van dit artikel.
2. Klik op de IP-configuratie waar u de resource voor het openbare IP-adres aan wilt toevoegen.
3. Klik in het deelvenster IPConfig dat wordt weergegeven op **IP-adres.**
4. Selecteer in **het deelvenster Openbaar IP-adres** kiezen dat wordt weergegeven een openbaar IP-adres.
5. Klik **op Opslaan** en de deelvensters worden gesloten. Als u geen bestaand openbaar IP-adres hebt, kunt u er een maken door de stappen in de sectie Een resource voor een openbaar [IP-adres](#create-public-ip) maken van dit artikel uit te voeren.
3. Controleer de nieuwe IP-configuratie.
4. U kunt op **Toevoegen klikken om** extra IP-configuraties toe te voegen of alle geopende blades sluiten om het toevoegen van IP-adressen te voltooien. Voeg het openbare IP-adres niet toe aan het besturingssysteem.

> [!NOTE]
> Nadat u de CONFIGURATIE van het IP-adres hebt gewijzigd, moet u de VM opnieuw opstarten om de wijzigingen door te voeren in de VM.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
