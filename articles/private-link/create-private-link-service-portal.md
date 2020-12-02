---
title: Snelstart - Een Private Link-service maken met behulp van Azure Portal
titlesuffix: Azure Private Link
description: In deze quickstart leert u hoe u een Private Link-service kunt maken met behulp van de Azure Portal
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: 5b7bc8be89068f0d3cf6722c36ae7fd5cc560736
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96012115"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Snelstart: Een Private Link-service maken met behulp van de Azure-portal

Een Azure Private Link-service verwijst naar uw eigen service die wordt beheerd door Private Link. U kunt Private Link toegang verlenen tot de service of resource die achter Azure Standard Load Balancer werkt. Gebruikers van uw service kunnen deze privé gebruiken vanuit hun eigen virtuele netwerken. In deze quickstart leert u hoe u een Private Link-service kunt maken met behulp van Azure Portal.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Een interne load balancer maken

Eerst moet u een virtueel netwerk maken. Maak vervolgens een interne load balancer om te gebruiken met de Private Link-service.

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

In deze sectie gaat u een virtueel netwerk maken. U maakt ook het subnet om de load balancer te hosten die toegang heeft tot uw Private Link-service.

In deze sectie moet u de volgende parameters in de stappen vervangen door onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | VS - oost 2      |
| **\<IPv4-address-space>**   | 10.3.0.0/16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-standard-load-balancer"></a>Een standaardversie van een load balancer maken

Maak met behulp van de portal een interne standard load balancer. De naam en het IP-adres dat u opgeeft, worden automatisch geconfigureerd als de front-end van de load balancer.

1. Selecteer **Een resource maken** > **Netwerken** > **Load Balancer** linksboven in het scherm.

1. Typ of selecteer de volgende informatie op het tabblad **Basisbeginselen** van de pagina **Load balancer maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | **Abonnement**               | Selecteer uw abonnement.    |
    | **Resourcegroep**         | Selecteer **myResourceGroupLB** in het vak.|
    | **Naam**                   | Voer **myLoadBalancer** in.                                   |
    | **Regio**         | Selecteer **VS - oost 2**.                                        |
    | **Type**          | selecteer **Intern**.                                        |
    | **SKU**           | selecteer **Standaard**.                          |
    | **Virtueel netwerk**           | Selecteer **myVNet**.                          |
    | **IP-adrestoewijzing**              | Selecteer **Statisch**.   |
    | **Privé IP-adres**|Voer een adres in dat zich in de adresruimte van uw virtuele netwerk en subnet bevindt. Een voorbeeld is 10.3.0.7.  |

1. Accepteer de standaardwaarden voor de overige instellingen en selecteer **Beoordelen en maken**

1. Selecteer op het tabblad **Beoordelen en maken** de optie **Maken**.

### <a name="create-standard-load-balancer-resources"></a>Resources voor standard-load balancer maken

In deze sectie configureert u de instellingen voor de load balancer voor een back-endadresgroep en een statustest. U geeft ook load balancer-regels op.

#### <a name="create-a-back-end-pool"></a>Een back-endgroep maken

De back-endadresgroep bevat de IP-adressen van de virtuele netwerkinterfacekaarten die zijn verbonden met de load balancer. Met deze groep kunt u verkeer over uw resources verdelen. Maak de back-end-adresgroep met de naam **myBackendPool** om resources op te nemen die verkeer gelijkmatig verdelen.

1. Selecteer **Alle services** in het meest linkse menu.
1. Selecteer **Alle resources** en selecteer vervolgens **myLoadBalancer** in de lijst met resources.
1. Selecteer onder **Instellingen** de optie **Back-endpools** en selecteer vervolgens **Toevoegen**.
1. Voer op de pagina **Back-endgroep toevoegen** **myBackendPool** in als naam voor de back-endgroep. Selecteer vervolgens **Toevoegen**.

#### <a name="create-a-health-probe"></a>Een statustest maken

Gebruik een statustest om de load balancer de resourcestatus te laten controleren. Afhankelijk van de reactie van de resource op statuscontroles, voegt de statustest dynamisch VM's toe aan de load balancer-rotatie of verwijdert ze.

Voor het maken van een statustest om de status van de VM's te bewaken:

1. Selecteer **Alle resources** in het meest linkse menu en selecteer vervolgens **myLoadBalancer** in de lijst met resources.

1. Selecteer onder **Instellingen** de optie **Statustests** en selecteer vervolgens **Toevoegen**.

1. Op de pagina **Een statustest toevoegen** typt of selecteert u de volgende waarden:

   - **Naam**: Voer **myHealthProbe** in.
   - **Protocol**: selecteer **TCP**.
   - **Poort**: Voer **80** in.
   - **Interval**: Voer **15** in. Deze waarde is het aantal seconden tussen tests.
   - **Drempelwaarde voor onjuiste status**: Voer **2** in. Deze waarde duidt het aantal opeenvolgende mislukte tests aan dat moet optreden voordat wordt besloten dat een virtuele machine een onjuiste status heeft.

1. Selecteer **OK**.

#### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer-regel bepaalt hoe het verkeer over de resources wordt verdeeld. De regel definieert:

- De front-end-IP-configuratie voor binnenkomend verkeer.
- De back-end-IP-adresgroep voor het ontvangen van verkeer.
- De vereiste bron- en doelpoorten.

De load balancer-regel met de naam **myLoadBalancerRule** luistert op poort 80 van de front-end van **LoadBalancerFrontEnd**. Met de regel wordt netwerkverkeer ook naar de back-endadresgroep **myBackendPool** op poort 80 verzonden.

Een load balancer-regel maken:

1. Selecteer **Alle resources** in het meest linkse menu en selecteer vervolgens **myLoadBalancer** in de lijst met resources.

1. Selecteer onder **Instellingen** **Taakverdelingsregels** en selecteer vervolgens **Toevoegen**.

1. Op de pagina **Load balancer-regel toevoegen** typt of selecteert u de volgende waarden, als die er nog niet op voorkomen:

   - **Naam**: Voer **MyLoadBalancerRule** in.
   - **IP-adres voor front-end:** Voer **LoadBalancerFrontend** in.
   - **Protocol**: selecteer **TCP**.
   - **Poort**: Voer **80** in.
   - **Back-endpoort**: Voer **80** in.
   - **Back-end-pool**: Selecteer **myBackendPool**.
   - **Statustest**: Selecteer **myHealthProbe**. 

1. Selecteer **OK**.

## <a name="create-a-private-link-service"></a>Een Private Link-service maken

In deze sectie maakt u een Private Link-service achter een standaard load balancer.

1. Selecteer in de linkerbovenhoek van de pagina in de Azure Portal de optie **Een resource maken** > **Netwerken** > **Private Link-centrum (preview)** . U kunt ook het zoekvak van de portal gebruiken om te zoeken naar Private Link.

1. In **Private Link-centrum: overzicht** > **Uw eigen service beschikbaar stellen zodat anderen kunnen verbinden**, selecteert u **Start**.

1. Typ of selecteer onder **Een private link-service maken - Basisbeginselen** de volgende gegevens:

    | Instelling           | Waarde                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | Projectgegevens:  |                                                                              |
    | **Abonnement**      | Selecteer uw abonnement.                                                     |
    | **Resourcegroep**    | Selecteer **myResourceGroupLB**.                                                    |
    | Exemplaardetails: |                                                                              |
    | **Naam**              | Voer **myPrivateLinkService** in. |
    | **Regio**            | Selecteer **VS - oost 2**.                                                        |

1. Selecteer **Volgende: Instellingen voor uitgaand verkeer**.

1. Typ of selecteer onder **Een private link-service maken - instellingen voor uitgaand verkeer** de volgende gegevens:

    | Instelling                           | Waarde                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **Load Balancer**                     | Selecteer **myLoadBalancer**.                                                           |
    | **Front-end-IP-adres van load balancer** | Selecteer het front-end-IP-adres van **myLoadBalancer**.                                |
    | **Virtueel netwerk van NAT-bron**        | Selecteer **myVNet**.                                                                   |
    | **NAT-bronsubnet**                 | Selecteer **myBackendSubnet**.                                                          |
    | **TCP-proxy V2 inschakelen**               | Selecteer **JA** of **NEE** , afhankelijk van of de toepassing een TCP proxy v2-header verwacht. |
    | **Instellingen voor persoonlijk IP-adres**       | Configureer de toewijzingsmethode en het IP-adres voor elk NAT-IP-adres.                  |

1. Selecteer **Volgende: Toegangsbeveiliging**.

1. Selecteer **Zichtbaarheid** onder **Een Private Link-service maken - Toegangsbeveiliging** en kies vervolgens **Alleen op rollen gebaseerd toegangsbeheer**.
  
1. Selecteer **Volgende: Tags** > **Beoordelen en maken** of kies het tabblad **Beoordelen en maken** bovenaan de pagina.

1. Controleer uw gegevens en selecteer **Maken**.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het gebruik van de Private Link-service, verwijdert u de resourcegroep om de resources op te schonen die in deze quickstart worden gebruikt.

1. Typ **myResourceGroupLB** in het zoekvak bovenaan de portal en selecteer **myResourceGroupLB** in de zoekresultaten.
1. Selecteer **Resourcegroep verwijderen**.
1. In **TYPE THE RESOURCE GROUP** voert u **myResourceGroup** in.
1. Selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een interne Azure load balancer en een Private Link-service gemaakt. In deze quickstart leert u [een privé-eindpunt maken met behulp van Azure Portal](./create-private-endpoint-portal.md).