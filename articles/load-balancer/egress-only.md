---
title: Load balancer-configuratie voor alleen uitgaand verkeer
titleSuffix: Azure Load Balancer
description: In dit artikel vindt u informatie over het maken van een interne load balancer met uitgaande NAT
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2020
ms.author: allensu
ms.openlocfilehash: ee264a22de5ce094e8a4c1335ace77cbbba49270
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694990"
---
# <a name="outbound-only-load-balancer-configuration"></a>Load balancer-configuratie voor alleen uitgaand verkeer

Gebruik een combi natie van interne en externe standaard load balancers voor het maken van uitgaande connectiviteit voor Vm's achter een interne load balancer. 

Deze configuratie biedt een uitgaand NAT voor een intern load balancer scenario, waarbij een ' alleen-uitgevals instelling ' wordt ingesteld voor uw back-end-pool.


<p align="center">
  <img src="./media/egress-only/load-balancer-egress-only.png" alt="Figure depicts a egress only load balancer configuration." width="500" title="Virtual Network NAT">
</p>


<!-- 
:::image type="content" source="./media/egress-only/load-balancer-egress-only.png" alt-text="Figure depicts a egress only load balancer configuration" border="true":::
-->


*Afbeelding: alleen uitgaand load balancer configuratie*

De volgende stappen zijn vereist:

1. Maak een virtueel netwerk met een bastion-host.
2. Maak een virtuele machine met alleen een persoonlijk IP-adres.
3. Maak zowel interne als open bare standaard load balancers.
4. Voeg back-endservers toe aan beide load balancers en plaats de virtuele machine in elke groep.
5. Maak verbinding met uw virtuele machine via de bastion-host en:
    1. Uitgaande connectiviteit testen, 
    2. Een uitgaande regel configureren op de open bare load balancer.
    3. Uitgaande connectiviteit opnieuw testen.

## <a name="create-virtual-network-and-virtual-machine"></a>Virtueel netwerk en virtuele machine maken

Maak een virtueel netwerk met twee subnetten:

* Primair subnet
* Bastion-subnet

Maak een virtuele machine in het nieuwe virtuele netwerk.

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

1. [Meld u aan](https://portal.azure.com) bij Azure Portal.

2. Selecteer in de linkerbovenhoek van het scherm **Een resource maken > Netwerken > Virtueel netwerk** of zoek naar **Virtueel netwerk** in het zoekvak.

2. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens op het tabblad **Basisinstellingen**:

    | **Instelling**          | **Waarde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projectgegevens**  |                                                                 |
    | Abonnement     | Selecteer uw Azure-abonnement                                  |
    | Resourcegroep   | Selecteer **Nieuw maken**. </br> Voer **myResourceGroupLB** in. </br> Selecteer **OK**. |
    | **Exemplaardetails** |                                                                 |
    | Naam             | Voer **myVNet** in                                    |
    | Regio           | Selecteer **VS Oost 2** |

3. Selecteer het tabblad **IP-adressen** of klik op de knop **Volgende: IP-adressen** onderaan de pagina.

4. Voer op het tabblad **IP-adressen** deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | IPv4-adresruimte | Voer **10.1.0.0/16** in |

5. Onder **Subnetnaam** selecteert u het woord **standaard**.

6. Voer in **Subnet bewerken** deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | Subnetnaam | Voer **myBackendSubnet** in |
    | Subnetadresbereik | Voer **10.1.0.0/24** in |

7. Selecteer **Opslaan**.

8. Selecteer het tabblad **Beveiliging**.

9. Selecteer onder **BastionHost** de optie **Inschakelen**. Voer deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | Bastion-naam | Voer **myBastionHost** in |
    | AzureBastionSubnet-adresruimte | Voer **10.1.1.0/24** in |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. </br> Voer bij **Naam** de naam **myBastionIP** in. </br> Selecteer **OK**. |


8. Selecteer het tabblad **Controleren + maken** of klik op de knop **Controleren + maken**.

9. Selecteer **Maken**.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer in de linkerbovenhoek van de portal de optie **Een resource maken** > **Compute** > **Virtuele machine**. 
   
2. In **Een virtuele machine maken** typt of selecteert u de waarden op het tabblad **Basisinformatie**:

    | Instelling | Waarde                                          |
    |-----------------------|----------------------------------|
    | **Projectgegevens** |  |
    | Abonnement | Selecteer uw Azure-abonnement |
    | Resourcegroep | Selecteer **myResourceGroupLB** |
    | **Exemplaardetails** |  |
    | Naam van de virtuele machine | Open **myVM** |
    | Region | Selecteer **VS Oost 2** |
    | Beschikbaarheidsopties | Selecteer **Geen infrastructuurredundantie vereist** |
    | Installatiekopie | Selecteer **Windows Server 2019 Datacenter** |
    | Azure Spot-exemplaar | Selecteer **Nee** |
    | Grootte | Kies een VM-grootte of kies de standaardinstelling |
    | **Beheerdersaccount** |  |
    | Gebruikersnaam | Voer een gebruikersnaam in |
    | Wachtwoord | Voer een wachtwoord in |
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in |
    | **Regels voor binnenkomende poort** |  |
    | Openbare poorten voor inkomend verkeer | Selecteer **geselecteerde poorten toestaan** |
    | Binnenkomende poorten selecteren | Selecteer **RDP (3389)** |

3. Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.
  
4. Op het tabblad Netwerken selecteert u of voert u het volgende in:

    | Instelling | Waarde |
    |-----|------------|
    | **Netwerkinterface** |  |
    | Virtueel netwerk | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Openbare IP | Selecteer **Geen**. |
    | NIC-netwerkbeveiligingsgroep | Selecteer **Geen**|
    | Deze virtuele machine achter een bestaande oplossing voor taak verdeling plaatsen? | Selecteer **Nee** |
   
5. Selecteer het tabblad **Beheer** of selecteer **Volgende** > **Beheer**.

6. Op het tabblad **Beheer** voert u het volgende in of selecteert u het volgende:
    
    | Instelling | Waarde |
    |-|-|
    | **Controle** |  |
    | Diagnostische gegevens over opstarten | Selecteer **Uit** |
   
7. Selecteer **Controleren + maken**. 
  
8. Controleer de instellingen en selecteer vervolgens **Maken**.

## <a name="create-load-balancers-and-test-connectivity"></a>Load balancers maken en connectiviteit testen

Gebruik de Azure Portal om te maken:

* Interne load balancer
* Openbare load balancer
 
Voeg uw gemaakte virtuele machine toe aan de back-end-groep van elk.  Vervolgens stelt u een configuratie in om alleen uitgaande connectiviteit van uw VM toe te staan, testen vóór en na.

### <a name="create-internal-load-balancer"></a>Interne load balancer maken

1. Selecteer linksboven in het scherm de optie **Een resource maken** > **Netwerken** > **Load balancer**.

2. Typ of selecteer de volgende informatie op het tabblad **Basisbeginselen** van de pagina **Load balancer maken**: 

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer **myResourceGroupLB** die u in de vorige stap hebt gemaakt.|
    | Naam                   | **MyInternalLoadBalancer** invoeren                                   |
    | Regio         | Selecteer **VS - oost 2**.                                        |
    | Type          | selecteer **Intern**.                                        |
    | SKU           | selecteer **Standard** |
    | Virtueel netwerk | Selecteer **myVNet** die u in de vorige stap hebt gemaakt. |
    | Subnet  | Selecteer **myBackendSubnet** die u in de vorige stap hebt gemaakt. |
    | IP-adrestoewijzing | selecteer **Dynamisch**. |

3. Accepteer de standaardwaarden voor de overige instellingen en selecteer **Beoordelen en maken**.

4. Selecteer in het deelvenster **Beoordelen en maken** de optie **Maken**.   

### <a name="create-public-load-balancer"></a>Open bare load balancer maken

1. Selecteer linksboven in het scherm de optie **Een resource maken** > **Netwerken** > **Load balancer**.

2. Typ of selecteer de volgende informatie op het tabblad **Basisbeginselen** van de pagina **Load balancer maken**: 

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer **Nieuwe maken** en voer **myResourceGroupLB** in het tekstvak in.|
    | Naam                   | **MyPublicLoadBalancer** invoeren                                   |
    | Regio         | Selecteer **VS - oost 2**.                                        |
    | Type          | Selecteer **Openbaar**.                                        |
    | SKU           | selecteer **Standard** |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. |
    | Naam openbaar IP-adres | Voer **myFrontendIP** in het tekstvak in.|
    | Beschikbaarheidszone | Selecteer **Zone-redundant**. |
    | Een openbaar IPv6-adres toevoegen | Selecteer **Nee**. |

3. Accepteer de standaardwaarden voor de overige instellingen en selecteer **Beoordelen en maken**.

4. Selecteer in het deelvenster **Beoordelen en maken** de optie **Maken**.   
   
### <a name="create-internal-backend-address-pool"></a>Een interne back-end-adres groep maken

De **myInternalBackendPool** voor de back-end-adres groep maken:

1. Selecteer **alle services** in het linkermenu, selecteer **alle resources** en selecteer vervolgens **myInternalLoadBalancer** in de lijst met resources.

2. Selecteer onder **Instellingen** de optie **Back-endpools** en selecteer vervolgens **Toevoegen**.

3. Op de pagina **een back-endserver toevoegen** typt u **myInternalBackendPool**, als naam voor uw back-end-pool.
 
4. Selecteer bij **Virtueel netwerk** de optie **myVNet**.

5. Selecteer onder **virtuele machines** **toevoegen** en kies **myVM**.
 
6. Selecteer **toevoegen**.

### <a name="create-public-backend-address-pool"></a>Open bare back-end-adres groep maken

De **myPublicBackendPool** voor de back-end-adres groep maken:

1. Selecteer **alle services** in het linkermenu, selecteer **alle resources** en selecteer vervolgens **myPublicLoadBalancer** in de lijst met resources.

2. Selecteer onder **Instellingen** de optie **Back-endpools** en selecteer vervolgens **Toevoegen**.

3. Op de pagina **een back-endserver toevoegen** typt u **myPublicBackendPool**, als naam voor uw back-end-pool.

4. Selecteer bij **Virtueel netwerk** de optie **myVNet**.
 
5. Selecteer onder **virtuele machines** **toevoegen** en kies **myVM**.
 
6. Selecteer **toevoegen**.

### <a name="test-connectivity-before-outbound-rule"></a>Verbinding testen voor uitgaande regel

1. Selecteer **alle services** in het linkermenu, selecteer **alle resources** en selecteer vervolgens in de lijst met resources **myVM** die zich in de resource groep **myResourceGroupLB** bevindt.

2. Selecteer op de pagina **Overzicht** de optie **Verbinding maken** en daarna **Bastion**.

4. Voer de gebruikersnaam en het wachtwoord in die zijn ingevoerd tijdens het maken van de VM.

5. Selecteer **Verbinding maken**.

6. Open Internet Explorer.

7. Voer **https://whatsmyip.org** in de adres balk in.

8. De verbinding moet mislukken. Standaard is een openbaar load balancer [geen uitgaand verkeer zonder een gedefinieerde uitgaande regel toegestaan](load-balancer-overview.md#securebydefault).
 
### <a name="create-a-public-load-balancer-outbound-rule"></a>Een open bare regel voor load balancer uitgaande verbindingen maken

1. Selecteer **alle services** in het linkermenu, selecteer **alle resources** en selecteer vervolgens **myPublicLoadBalancer** in de lijst met resources.

2. Selecteer onder **Instellingen** **Uitgaande regels** en selecteer vervolgens **Toevoegen**.

3. Gebruik deze waarden om de uitgaande regels te configureren:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **myOutboundRule** in. |
    | IP-adres voor front-end | Selecteer **LoadBalancerFrontEnd**.|
    | Time-out voor inactiviteit (minuten) | Verplaats de schuifregelaar naar **15 minuten**.|
    | TCP opnieuw instellen | Selecteer **Ingeschakeld**.|
    | Back-end-pool | Selecteer **myPublicBackendPool**.| |
    | Poorttoewijzing -> Poorttoewijzing | Selecteer **het standaard aantal uitgaande poorten gebruiken** |

4. Selecteer **Toevoegen**.

### <a name="test-connectivity-after-outbound-rule"></a>Connectiviteit testen na uitgaande regel

1. Selecteer **alle services** in het linkermenu, selecteer **alle resources** en selecteer vervolgens in de lijst met resources **myVM** die zich in de resource groep **myResourceGroupLB** bevindt.

2. Selecteer op de pagina **Overzicht** de optie **Verbinding maken** en daarna **Bastion**.

4. Voer de gebruikersnaam en het wachtwoord in die zijn ingevoerd tijdens het maken van de VM.

5. Selecteer **Verbinding maken**.

6. Open Internet Explorer.

7. Voer **https://whatsmyip.org** in de adres balk in.

8. De verbinding moet slagen.

9. Het weer gegeven IP-adres moet het frontend-IP-adres van **myPublicLoadBalancer** zijn.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resource groep, load balancers, VM en alle gerelateerde resources. 

Als u dit wilt doen, selecteert u de resource groep **myResourceGroupLB** en selecteert u vervolgens **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een configuratie voor alleen uitgaand verkeer gemaakt met een combi natie van open bare en interne load balancers.  

Met deze configuratie kunt u het binnenkomende interne verkeer naar uw back-end-pool verdelen terwijl er nog steeds open bare binnenkomende verbindingen worden voor komen.

- Meer informatie over [Azure Load Balancer](load-balancer-overview.md).
- Meer informatie over [uitgaande verbindingen in azure](load-balancer-outbound-connections.md).
- [Veelgestelde vragen](load-balancer-faqs.md)over Load Balancer.
- Meer informatie over [Azure Bastion](../bastion/bastion-overview.md)