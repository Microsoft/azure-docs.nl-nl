---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/20/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a66596ecb926c1bf3c6b61cc99e1eb1b56e99158
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92328425"
---
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. In **Resources, services en documenten (G+/) zoeken** typt u *virtueel netwerk*.

   ![Pagina 'naar de resource Virtueel netwerk zoeken'](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Pagina 'naar de resource Virtueel netwerk zoeken'")
1. Selecteer **Virtueel netwerk** uit de resultaten van **Marketplace**.

   ![Virtueel netwerk selecteren](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Pagina 'naar de resource Virtueel netwerk zoeken'")
1. Op de pagina **Virtueel netwerk** selecteert u **Maken**.

   ![pagina Virtueel netwerk](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Selecteer Maken")
1. Nadat u **Maken** hebt geselecteerd, wordt de pagina **Virtueel netwerk maken** geopend.
1. In het tabblad **Basics** configureert u **Projectdetails** en de VNet-instellingen bij **Exemplaardetails**.

   ![Tabblad Basics](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "Tabblad Basisbeginselen") Wanneer u de velden invult, ziet u een groen vinkje als de tekens die u in het veld invoert, gevalideerd zijn. Sommige waarden worden automatisch ingevuld. Deze kunt u door uw eigen waarden vervangen:

   - **Abonnement**: controleer of het weergegeven abonnement het juiste is. U kunt abonnementen wijzigen met behulp van de vervolgkeuzelijst.
   - **Resourcegroep**: Selecteer een bestaande resourcegroep of klik op **Nieuwe maken** om er een te maken. Zie [Overzicht van Azure Resource Manager](../articles/azure-resource-manager/management/overview.md#resource-groups) voor meer informatie over resourcegroepen.
   - **Naam**: Voer de naam in van het virtuele netwerk.
   - **Regio**: selecteer de locatie voor uw VNet. De locatie bepaalt waar de resources die u naar dit VNet implementeert, zich bevinden.

1. Configureer de waarden in het tabblad **IP-adressen**. De waarden die in de onderstaande voorbeelden worden weergegeven, zijn bedoeld voor demonstratiedoeleinden. U kunt deze waarden aanpassen op basis van de instellingen die u nodig hebt.

   ![Tabblad IP-adressen](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "Tabblad IP-adressen")  
   - **IPv4-adresruimte**: Standaard wordt er automatisch een adresruimte gemaakt. U kunt op de adresruimte klikken om deze aan te passen aan uw eigen waarden. U kunt ook extra adresruimten toevoegen.
   - **Subnet**: Als u de standaard adresruimte gebruikt, wordt er automatisch een standaard subnet gemaakt. Als u de adresruimte wijzigt, moet u een subnet toevoegen. Selecteer **+ Subnet toevoegen** om het venster **Subnet toevoegen** te openen. Configureer de volgende instellingen en selecteer vervolgens **Toevoegen** om de waarden toe te voegen:
      - **Subnetnaam**: In dit voorbeeld hebben we het subnet 'FrontEnd' genoemd.
      - **Subnetadresbereik**: Het adresbereik voor dit subnet.

1. Laat in het tabblad **Beveiliging** voor nu de standaardwaarden staan:

   - **DDoS-beveiliging**: Basic
   - **Firewall**: Uitgeschakeld
1. Selecteer **Beoordelen en maken** om de instellingen voor het virtuele netwerk te valideren.
1. Nadat de instellingen zijn gevalideerd, selecteert u **Maken**.
