---
title: bestand opnemen
description: Include-bestand
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c538c510eeafacd1596fdeb1b4593919e6236cf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92487054"
---
1. Typ n [Azure Portal](https://portal.azure.com) in **Resources, services en documenten zoeken(G+/)** **lokale netwerkgateway**. Zoek **lokale netwerkgateway** onder **Marketplace** in de zoekresultaten en selecteer deze. Hiermee opent u de pagina **Lokale netwerkgateway maken**.
1. Op de **pagina Lokale netwerkgateway maken** geeft u de waarden voor uw lokale netwerkgateway op.

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-ip-empty/create-ip.png" alt-text="Een lokale netwerkgateway met IP-adres maken":::

   * **Naam:** geef een naam op voor uw lokale netwerkgateway.
   * **Eindpunt:** Selecteer het eindpunttype voor het on-premises VPN-apparaat - **IP-adres** of **FQDN (Fully Qualified Domain Name)** .
      * **IP-adres**: Als u een statisch openbaar IP-adres hebt toegewezen van uw internetprovider voor uw VPN-apparaat, selecteert u de IP-adresoptie en vult u het IP-adres in zoals wordt weergegeven in het voorbeeld. Dit is het openbare IP-adres van het VPN-apparaat waarmee u via Azure VPN-gateway verbinding wilt maken. Als u op dit moment niet beschikt over het IP-adres, kunt u de waarden uit de schermafbeelding gebruiken. U moet dan wel later terugkeren om uw tijdelijke IP-adres te vervangen door het openbare IP-adres van uw VPN-apparaat. Anders kan er geen verbinding worden gemaakt vanuit Azure.
   * **Adresruimte** verwijst naar de adresbereiken voor het netwerk dat dit lokale netwerk vertegenwoordigt. U kunt meerdere adresruimtebereiken toevoegen. Zorg ervoor dat de bereiken die u hier opgeeft, niet overlappen met bereiken van andere netwerken waarmee u verbinding wilt maken. Azure stuurt het adresbereik dat u opgeeft, door naar het IP-adres van het on-premises VPN-apparaat. *Gebruik hier uw eigen waarden als u verbinding wilt maken met uw on-premises site, niet de waarden die worden weergegeven in het voorbeeld*.
   * **BGP-instellingen configureren:** gebruik deze alleen bij het configureren van BGP. Selecteer deze anders niet.
   * **Abonnement:** controleer of het juiste abonnement wordt weergegeven.
   * **Resourcegroep:** selecteer de resourcegroep die u wilt gebruiken. U kunt een nieuwe resourcegroep maken of een resourcegroep selecteren die u al hebt gemaakt.
   * **Locatie:** De locatie is hetzelfde als **Regio** in andere instellingen. Selecteer de locatie waarin dit object wordt gemaakt. U kunt dezelfde locatie selecteren als die van uw VNet, maar dat is niet vereist.

1. Wanneer u klaar bent met het opgeven van de waarden selecteert u **Maken** onderaan de pagina om de lokale netwerkgateway te maken.
