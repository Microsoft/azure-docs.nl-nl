---
title: 'Snelstartgids: route server maken en configureren met behulp van de Azure Portal'
description: In deze Quick Start leert u hoe u een route server maakt en configureert met behulp van de Azure Portal.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/03/2021
ms.author: duau
ms.openlocfilehash: f76c48af4f5ebc8013daad457f9973cf7792c7c6
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102547986"
---
# <a name="quickstart-create-and-configure-route-server-using-the-azure-portal"></a>Snelstartgids: route server maken en configureren met behulp van de Azure Portal

Dit artikel helpt u bij het configureren van Azure route server naar peer met een virtueel netwerk apparaat (NVA) in uw virtuele netwerk met behulp van de Azure Portal. Er worden door Azure route server routes van de NVA en Program ma's op de virtuele machines in het virtuele netwerk beschreven. Azure route server adverteert ook de virtuele netwerk routes naar het NVA. Lees [Azure route server](overview.md)voor meer informatie.

> [!IMPORTANT]
> Azure route server (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Controleer de [service limieten voor Azure route server](route-server-faq.md#limitations).

## <a name="create-a-route-server"></a>Een route server maken

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Meld u aan bij uw Azure-account en selecteer uw abonnement.

Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

### <a name="create-a-route-server"></a>Een route server maken

1. Ga naar https://aka.ms/routeserver.

1. Selecteer **+ nieuwe route server maken**.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-landing-page.png" alt-text="Scherm afbeelding van de landings pagina van de route server."::: 

1. Voer op de pagina **een route server maken** de vereiste gegevens in of Selecteer deze.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/create-route-server-page.png" alt-text="Scherm opname van de pagina route server maken.":::     

    | Instellingen | Waarde |
    |----------|-------|
    | Abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken om de route server te implementeren. |
    | Resourcegroep | Selecteer een resource groep om de route server te maken in. Als u geen bestaande resourcegroep hebt, kunt u een nieuwe maken. |
    | Name | Voer een naam in voor de route server. |
    | Region | Selecteer de regio waarin de route server wordt gemaakt. Selecteer dezelfde regio als het virtuele netwerk dat u eerder hebt gemaakt om het virtuele netwerk in de vervolg keuzelijst weer te geven. |
    | Virtual Network | Selecteer het virtuele netwerk waarin de route server wordt gemaakt. U kunt een nieuw virtueel netwerk maken of een bestaand virtueel netwerk gebruiken. Als u een bestaand virtueel netwerk gebruikt, zorg er dan voor dat het bestaande virtuele netwerk voldoende ruimte heeft voor een minimum van een/27-subnet voor de vereiste route server-subnet. Als uw virtuele netwerk niet in de vervolg keuzelijst wordt weer geven, moet u ervoor zorgen dat u de juiste resource groep of regio hebt geselecteerd. |
    | Subnet | Zodra u een virtueel netwerk hebt gemaakt of geselecteerd, wordt het veld voor het subnet weer gegeven. Dit subnet is uitsluitend bestemd voor route server. Selecteer **subnet-configuratie beheren** en maak het Azure route server-subnet. Selecteer **+ subnet** en maak een subnet met behulp van de volgende richt lijnen:</br><br>-Het subnet moet de naam *RouteServerSubnet*.</br><br>-Het subnet moet mini maal/27 of groter zijn.</br> |

1. Selecteer **controleren + maken**, Bekijk de samen vatting en selecteer vervolgens **maken**. 

    > [!NOTE]
    > De implementatie van de route server duurt ongeveer 20 minuten.

## <a name="set-up-peering-with-nva"></a>Peering instellen met NVA

De sectie helpt u bij het configureren van BGP-peering met uw NVA.

1. Ga naar [route server](https://aka.ms/routeserver) in de Azure Portal en selecteer de route server die u wilt configureren.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/select-route-server.png" alt-text="Scherm opname van de lijst route server."::: 

1. Selecteer **peers** onder *instellingen* in het navigatie venster aan de linkerkant. Selecteer vervolgens **+ toevoegen** om een nieuwe peer toe te voegen.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/peers-landing-page.png" alt-text="Scherm afbeelding van de landings pagina van de peers."::: 

1. Voer de volgende informatie over uw NVA-peer in.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/add-peer-page.png" alt-text="Scherm opname van de pagina peer toevoegen.":::

    | Instellingen | Waarde |
    |----------|-------|
    | Naam | Geef een naam op voor de peering tussen uw route server en de NVA. |
    | ASN |  Voer het autonoom systeem nummer (ASN) van uw NVA in. |
    | IPv4-adres | Voer het IP-adres in van de NVA waarmee de route server communiceert om BGP in te stellen. |

1. Selecteer **toevoegen** om deze peer toe te voegen.

## <a name="complete-the-configuration-on-the-nva"></a>De configuratie op de NVA volt ooien

U hebt de peer Ip's en ASN van de Azure route server nodig om de configuratie van uw NVA te volt ooien om een BGP-sessie tot stand te brengen. U kunt deze informatie op de pagina overzicht van uw route server ophalen.

:::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-overview.png" alt-text="Scherm afbeelding van de pagina overzicht van route server.":::

## <a name="configure-route-exchange"></a>Route uitwisseling configureren

Als u een ExpressRoute-gateway en/of VPN-gateway hebt en u wilt dat deze routes uitwisselen met de route server, kunt u route Exchange inschakelen.

1. Ga naar [route server](https://aka.ms/routeserver) in de Azure Portal en selecteer de route server die u wilt configureren.

1. Selecteer **configuratie** onder *instellingen* in het navigatie venster aan de linkerkant.

1. Selecteer **inschakelen** voor de instelling **vertakking-naar-vertakking** en selecteer vervolgens **Opslaan**.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/enable-route-exchange.png" alt-text="Scherm opname van het inschakelen van route uitwisseling.":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u de Azure route server niet meer nodig hebt, selecteert u **verwijderen** op de pagina overzicht om de inrichting van de route server ongedaan te maken.

:::image type="content" source="./media/quickstart-configure-route-server-portal/delete-route-server.png" alt-text="Scherm opname van het verwijderen van de route server.":::

## <a name="next-steps"></a>Volgende stappen

Nadat u de Azure route server hebt gemaakt, gaat u verder met meer informatie over hoe Azure route server communiceert met ExpressRoute en VPN-gateways: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute-en Azure VPN-ondersteuning](expressroute-vpn-support.md)
