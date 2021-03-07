---
title: 'Quick Start: een wille keurige configuratie maken met een ARM-sjabloon'
titleSuffix: Azure Virtual WAN
description: In deze Quick start ziet u hoe u een wille keurige configuratie kunt maken met behulp van een Azure Resource Manager sjabloon (ARM-sjabloon).
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: quickstart
ms.date: 02/02/2021
ms.author: cherylmc
ms.custom: subject-armqs
ms.openlocfilehash: d31f490baec49e8e0b6fcf89caa8c19202fdf763
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431377"
---
# <a name="quickstart-create-an-any-to-any-configuration-using-an-arm-template"></a>Quick Start: een wille keurige configuratie maken met een ARM-sjabloon

In deze Quick Start wordt beschreven hoe u een Azure Resource Manager sjabloon (ARM-sjabloon) kunt gebruiken om een wille keurig scenario te maken waarbij elke spoke een andere spoke kan bereiken.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f201-virtual-wan-with-all-gateways%2fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
* De certificaat gegevens van de open bare sleutel zijn vereist voor deze configuratie. Voorbeeld gegevens vindt u in het artikel. De voorbeeld gegevens worden echter alleen opgenomen om te voldoen aan de sjabloon vereisten om een P2S-gateway te maken. Nadat de sjabloon is voltooid en de resources zijn geïmplementeerd, moet u dit veld bijwerken met uw eigen certificaat gegevens om de configuratie te laten werken. Zie [VPN-certificaten voor gebruikers](certificates-point-to-site.md#cer).

## <a name="review-the-template"></a><a name="review"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/201-virtual-wan-with-all-gateways). De sjabloon voor dit artikel is te lang om hier weer te geven. Als u de sjabloon wilt bekijken, raadpleegt u [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-virtual-wan-with-all-gateways/azuredeploy.json).

In deze Quick Start maakt u een Azure Virtual WAN-implementatie voor meerdere hubs, met inbegrip van alle gateways en VNet-verbindingen. De lijst met invoer parameters is in het algemeen beperkt bewaard. Het schema voor IP-adres Sering kan worden gewijzigd door de variabelen in de sjabloon te wijzigen. Het scenario wordt verder uitgelegd in [een wille keurig scenario-](scenario-any-to-any.md) artikel.

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="Implementatie architectuur":::

Met deze sjabloon maakt u een volledig functionele Azure Virtual WAN-omgeving met de volgende bronnen:

* Twee afzonderlijke hubs in verschillende regio's
* Vier virtuele netwerken van Azure (VNet)
* Twee VNet-verbindingen voor elke VWAN-hub
* Eén punt-naar-site-VPN-gateway (P2S) in elke hub
* Eén site-naar-site (S2S) VPN-gateway in elke hub
* Eén ExpressRoute-gateway in elke hub

Er worden meerdere Azure-resources gedefinieerd in de sjabloon:

* [**Micro soft. Network/virtualwans**](/azure/templates/microsoft.network/virtualwans)
* [**Micro soft. Network/virtualhubs**](/azure/templates/microsoft.network/virtualhubs)
* [**Micro soft. Network/virtualnetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Micro soft. Network/hubvirtualnetworkconnections**](/azure/templates/microsoft.network/virtualhubs/hubvirtualnetworkconnections)
* [**Micro soft. Network/p2svpngateways**](/azure/templates/microsoft.network/p2svpngateways)
* [**Micro soft. Network/vpngateways**](/azure/templates/microsoft.network/vpngateways) 
* [**Micro soft. Network/expressroutegateways**](/azure/templates/microsoft.network/expressroutegateways)
* [**Micro soft. Network/vpnserverconfigurations**](/azure/templates/microsoft.network/vpnserverconfigurations)

>[!NOTE]
> Deze ARM-sjabloon maakt geen resources aan de klanten zijde die vereist zijn voor hybride verbindingen. Nadat u de sjabloon hebt geïmplementeerd, moet u nog steeds de P2S VPN-clients, de VPN-vertakkingen (lokale sites) maken en configureren, en verbinding maken met de ExpressRoute-circuits.
>

Zie [Azure Quick](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)start-sjablonen voor meer sjablonen.

## <a name="deploy-the-template"></a><a name="deploy"></a>De sjabloon implementeren

Als u deze sjabloon correct wilt implementeren, moet u, om de volgende redenen, de knop implementeren op Azure en de Azure Portal, in plaats van op andere manieren:

* Als u de P2S-configuratie wilt maken, moet u de gegevens van het basis certificaat uploaden. In het gegevens veld worden de certificaat gegevens niet geaccepteerd bij gebruik van Power shell of CLI.
* Deze sjabloon werkt niet goed met Cloud Shell als gevolg van het uploaden van de certificaat gegevens.
* Daarnaast kunt u de sjabloon en de para meters in de portal eenvoudig wijzigen om IP-adresbereiken en andere waarden te kunnen aanpassen.

1. Klik op **Implementeren in Azure**.

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f201-virtual-wan-with-all-gateways%2fazuredeploy.json)
1. Als u de sjabloon wilt weer geven, klikt u op **sjabloon bewerken**. Op deze pagina kunt u enkele waarden aanpassen, zoals de adres ruimte of de naam van bepaalde resources. **Sla** op om uw wijzigingen op te slaan of te **verwijderen**.
1. Voer op de pagina sjabloon de waarden in. Voor deze sjabloon zijn de P2S open bare certificaat gegevens vereist. Als u dit artikel gebruikt als oefening, kunt u de volgende gegevens uit dit CER-bestand als voorbeeld gegevens voor beide hubs gebruiken. Zodra de sjabloon is uitgevoerd en de implementatie is voltooid, moet u deze informatie vervangen door de [certificaat gegevens](certificates-point-to-site.md#cer) van de open bare sleutel voor uw eigen implementatie.

   ```certificate-data
   MIIC5zCCAc+gAwIBAgIQGxd3Av1q6LJDZ71e3TzqcTANBgkqhkiG9w0BAQsFADAW
   MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0yMDExMDkyMjMxNTVaFw0yMTExMDky
   MjUxNTVaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
   AAOCAQ8AMIIBCgKCAQEA33fFra/E0YmGuXLKmYcdvjsYpKwQmw8DjjDkbwhE9jcc
   Dp50e7F1P6Rxo1T6Hm3dIhEji+0QkP4Ie0XPpw0eW77+RWUiG9XJxGqtJ3Q4tyRy
   vBfsHORcqMlpV3VZOXIxrk+L/1sSm2xAc2QGuOqKaDNNoKmjrSGNVAeQHigxbTQg
   zCcyeuhFxHxAaxpW0bslK2hEZ9PhuAe22c2SHht6fOIDeXkadzqTFeV8wEZdltLr
   6Per0krxf7N2hFo5Cfz0KgWlvgdKLL7dUc9cjHo6b6BL2pNbLh8YofwHQOQbwt6H
   miAkEnx1EJ5N8AWuruUTByR2jcWyCnEAUSH41+nk4QIDAQABozEwLzAOBgNVHQ8B
   Af8EBAMCAgQwHQYDVR0OBBYEFJMgnJSYHH5AJ+9XB11usKRwjbjNMA0GCSqGSIb3
   DQEBCwUAA4IBAQBOy8Z5FBd/nvgDcjvAwNCw9h5RHzgtgQqDP0qUjEqeQv3ALeC+
   k/F2Tz0OWiPEzX5N+MMrf/jiYsL2exXuaPWCF5U9fu8bvs89GabHma8MGU3Qua2x
   Imvt0whWExQMjoyU8SNUi2S13fnRie9ZlSwNh8B/OIUUEtVhQsd4OfuZZFVH4xGp
   ibJMSMe5JBbZJC2tCdSdTLYfYJqrLkVuTjynXOjmz2JXfwnDNqEMdIMMjXzlNavR
   J8SNtAoptMOK5vAvlySg4LYtFyXkl0W0vLKIbbHf+2UszuSCijTUa3o/Y1FoYSfi
   eJH431YTnVLuwdd6fXkXFBrXDhjNsU866+hE
   ```

1. Wanneer u klaar bent met het invoeren van waarden, selecteert u **controleren + maken**.
1. Selecteer op de pagina **controleren en maken** na validatie de optie **maken**.
1. Het duurt ongeveer 75 minuten voordat de implementatie is voltooid. U kunt de voortgang bekijken op de **overzichts** pagina van de sjabloon.  Als u de portal sluit, wordt de implementatie voortgezet.

   :::image type="content" source="./media/quickstart-any-to-any-template/template.png" alt-text="Voor beeld van implementatie voltooid":::

## <a name="validate-the-deployment"></a><a name="validate"></a>De implementatie valideren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen** in het linkerdeelvenster.
1. Selecteer de resourcegroep die u in de vorige sectie hebt gemaakt. Op de pagina **overzicht** ziet u iets dat vergelijkbaar is met dit voor beeld: :::image type="content" source="./media/quickstart-any-to-any-template/resources.png" alt-text="voor beeld van resources" lightbox="./media/quickstart-any-to-any-template/resources.png":::

1. Klik op het virtuele WAN om de hubs weer te geven. Klik op de virtuele WAN-pagina op elke hub om verbindingen en andere informatie over de hub weer te geven.
   :::image type="content" source="./media/quickstart-any-to-any-template/hub.png" alt-text="Voor beeld van hubs" lightbox="./media/quickstart-any-to-any-template/hub.png":::

## <a name="complete-the-hybrid-configuration"></a><a name="complete"></a>De hybride configuratie volt ooien

Met de sjabloon worden niet alle instellingen geconfigureerd die nodig zijn voor een hybride netwerk. U moet de volgende configuraties en instellingen volt ooien, afhankelijk van uw vereisten.

* [De VPN-filialen configureren-lokale sites](virtual-wan-site-to-site-portal.md#site)
* [De P2S VPN-configuratie volt ooien](virtual-wan-point-to-site-portal.md)
* [Verbinding maken met de ExpressRoute-circuits](virtual-wan-expressroute-portal.md)

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u hebt gemaakt, niet meer nodig hebt, verwijdert u deze. Sommige virtuele WAN-resources moeten in een bepaalde volg orde worden verwijderd vanwege afhankelijkheden. Het duurt ongeveer 30 minuten om het verwijderen te volt ooien.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [De P2S VPN-configuratie volt ooien](virtual-wan-point-to-site-portal.md)

> [!div class="nextstepaction"]
> [De VPN-filialen configureren-lokale sites](virtual-wan-site-to-site-portal.md#site)

> [!div class="nextstepaction"]
> [Verbinding maken met de ExpressRoute-circuits](virtual-wan-expressroute-portal.md)