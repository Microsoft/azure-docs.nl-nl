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
ms.openlocfilehash: 5358bbbca716f5152a943c90cb7a5f735ae12047
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92479559"
---
1. Typ n [Azure Portal](https://portal.azure.com) in **Resources, services en documenten zoeken(G+/)** **lokale netwerkgateway**. Zoek **lokale netwerkgateway** onder **Marketplace** in de zoekresultaten en selecteer deze. Hiermee opent u de pagina **Lokale netwerkgateway maken**.
1. Op de **pagina Lokale netwerkgateway maken** geeft u de waarden voor uw lokale netwerkgateway op.

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-ip.png" alt-text="Een lokale netwerkgateway met IP-adres maken":::

   * **Naam:** geef een naam op voor uw lokale netwerkgateway.
   * **Eindpunt:** Selecteer het eindpunttype voor het on-premises VPN-apparaat - **IP-adres** of **FQDN (Fully Qualified Domain Name)** .
      * **IP-adres**: Als u een statisch openbaar IP-adres hebt toegewezen van uw internetprovider voor uw VPN-apparaat, selecteert u de IP-adresoptie en vult u het IP-adres in zoals wordt weergegeven in het voorbeeld. Dit is het openbare IP-adres van het VPN-apparaat waarmee u via Azure VPN-gateway verbinding wilt maken. Als u op dit moment niet beschikt over het IP-adres, kunt u de waarden uit de schermafbeelding gebruiken. U moet dan wel later terugkeren om uw tijdelijke IP-adres te vervangen door het openbare IP-adres van uw VPN-apparaat. Anders kan er geen verbinding worden gemaakt vanuit Azure.
      * **FQDN**: Als u een dynamisch IP-adres hebt dat na een bepaalde periode verandert, meestal bepaald door uw internetprovider, kunt u een constante DNS-naam gebruiken met een dynamisch DNS-service-naar-punt naar uw huidige openbaar IP-adres van uw VPN-apparaat. Uw Azure VPN-gateway zet de FQDN om, om te bepalen met welk openbaar IP-adres verbinding moet worden gemaakt. 
   * **Adresruimte** verwijst naar de adresbereiken voor het netwerk dat dit lokale netwerk vertegenwoordigt. U kunt meerdere adresruimtebereiken toevoegen. Zorg ervoor dat de bereiken die u hier opgeeft, niet overlappen met bereiken van andere netwerken waarmee u verbinding wilt maken. Azure stuurt het adresbereik dat u opgeeft, door naar het IP-adres van het on-premises VPN-apparaat. *Gebruik hier uw eigen waarden als u verbinding wilt maken met uw on-premises site, niet de waarden die worden weergegeven in het voorbeeld*.
   * **BGP-instellingen configureren:** gebruik deze alleen bij het configureren van BGP. Selecteer deze anders niet.
   * **Abonnement:** controleer of het juiste abonnement wordt weergegeven.
   * **Resourcegroep:** selecteer de resourcegroep die u wilt gebruiken. U kunt een nieuwe resourcegroep maken of een resourcegroep selecteren die u al hebt gemaakt.
   * **Locatie:** De locatie is hetzelfde als **Regio** in andere instellingen. Selecteer de locatie waarin dit object wordt gemaakt. U kunt dezelfde locatie selecteren als die van uw VNet, maar dat is niet vereist.

   > [!NOTE]
   >
   > * Azure VPN ondersteunt slechts een IPv4-adres voor elke FQDN. Als de domeinnaam meerdere IP-adressen omzet, gebruikt Azure VPN Gateway het eerste IP-adres dat door de DNS-servers wordt geretourneerd. Om de onzekerheid te beperken, raden we aan dat uw FQDN altijd een enkel IPv4-adres omzet. IPv6 wordt niet ondersteund.
   > * Azure VPN Gateway behoudt een DNS-cache die elke 5 minuten wordt vernieuwd. De gateway probeert alleen de FQDN's voor onderbroken tunnels om te zetten. Als u de gateway opnieuw instelt, wordt ook de FQDN-omzetting geactiveerd.
   >

1. Wanneer u klaar bent met het opgeven van de waarden selecteert u **Maken** onderaan de pagina om de lokale netwerkgateway te maken.
