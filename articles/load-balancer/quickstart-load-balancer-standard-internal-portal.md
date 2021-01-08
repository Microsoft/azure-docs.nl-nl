---
title: 'Quickstart: Een interne load balancer maken - Azure Portal'
titleSuffix: Azure Load Balancer
description: In deze quickstart vindt u informatie over het maken van een interne load balancer via Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a internal load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 14203021846e97a53f59c3bc24a1586774613dec
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704330"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Quickstart: Een interne load balancer maken met Azure Portal om taken te verdelen over VM's

Ga aan de slag met Azure Load Balancer via Azure Portal om een interne load balancer en drie virtuele machines te maken.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

---

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/option-1-create-internal-load-balancer-standard)

>[!NOTE]
>Voor productieworkloads wordt de load balancer uit de Standard SKU aanbevolen.  Zie **[Azure Load Balancer-SKU's](skus.md)** voor meer informatie over SKU's.

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Resources voor de Standard-load balancer die worden gemaakt voor de quickstart." border="false":::

In deze sectie maakt u een load balancer die taken van virtuele machines verdeelt. 

Wanneer u een interne load balancer maakt, wordt een virtueel netwerk geconfigureerd als netwerk voor de load balancer. 

Een privé-IP-adres in het virtuele netwerk wordt geconfigureerd als de front-end (standaard **LoadBalancerFrontend** genoemd) voor de load balancer. 

Het front-end-IP-adres kan **Statisch** of **Dynamisch** zijn.

## <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

In deze sectie gaat u een virtueel netwerk en een subnet maken.

1. Selecteer in de linkerbovenhoek van het scherm **Een resource maken > Netwerken > Virtueel netwerk** of zoek naar **Virtueel netwerk** in het zoekvak.

2. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens op het tabblad **Basisinstellingen**:

    | **Instelling**          | **Waarde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projectgegevens**  |                                                                 |
    | Abonnement     | Selecteer uw Azure-abonnement                                  |
    | Resourcegroep   | Selecteer **CreateIntLBQS-rg** |
    | **Exemplaardetails** |                                                                 |
    | Naam             | Voer **myVNet** in                                    |
    | Regio           | Selecteer **Europa - west** |

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

## <a name="create-load-balancer"></a>Load balancer maken

1. Selecteer linksboven in het scherm de optie **Een resource maken** > **Netwerken** > **Load balancer**.

2. Typ of selecteer de volgende informatie op het tabblad **Basisbeginselen** van de pagina **Load balancer maken**: 

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer **CreateIntLBQS** die u in de vorige stap hebt gemaakt.|
    | Naam                   | Voer **myLoadBalancer** in                                   |
    | Regio         | Selecteer **Europa - west**.                                        |
    | Type          | selecteer **Intern**.                                        |
    | SKU           | selecteer **Standard** |
    | Virtueel netwerk | Selecteer **myVNet** die u in de vorige stap hebt gemaakt. |
    | Subnet  | Selecteer **myBackendSubnet** die u in de vorige stap hebt gemaakt. |
    | IP-adrestoewijzing | selecteer **Dynamisch**. |
    | Beschikbaarheidszone | Selecteer **Zone-redundant**. |

3. Accepteer de standaardwaarden voor de overige instellingen en selecteer **Beoordelen en maken**.

4. Selecteer in het deelvenster **Beoordelen en maken** de optie **Maken**.   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-standard-internal-load-balancer.png" alt-text="Een interne load balancer van het type Standard maken." border="true":::
 
## <a name="create-load-balancer-resources"></a>Resources voor load balancer maken

In deze sectie configureert u het volgende:

* Load balancer-instellingen voor een back-endadresgroep.
* Een statustest.
* Een load balancer-regel.

### <a name="create-a-backend-pool"></a>Een back-endpool maken

De back-endadresgroep bevat de IP-adressen van de virtuele netwerkinterfacekaarten (NIC's) die zijn verbonden met de load balancer. 

Maak de back-endadres groep **myBackendPool** om virtuele machines voor het verdelen van internetverkeer in te bewaren.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.

2. Selecteer onder **Instellingen** de optie **Back-endpools** en selecteer vervolgens **Toevoegen**.

3. Typ op de pagina **Back-endpool toevoegen** **myBackendPool** als naam voor de back-endpool. Selecteer vervolgens **Toevoegen**.

### <a name="create-a-health-probe"></a>Een statustest maken

De load balancer controleert de status van uw app met een statustest. 

De statustest voegt VM's toe aan of verwijdert ze van de load balancer op basis van hun reactie op statuscontroles. 

Maak een statustest genaamd **myHealthProbe** om de status van de VM's te bewaken.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.

2. Selecteer onder **Instellingen** de optie **Statustests** en selecteer vervolgens **Toevoegen**.
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **myHealthProbe** in. |
    | Protocol | Selecteer **HTTP**. |
    | Poort | Voer **80** in.|
    | Interval | Voer **15** in als waarde voor het **Interval** in seconden tussen tests. |
    | Drempelwaarde voor beschadigde status | Selecteer **2** als het aantal voor de **Drempelwaarde voor beschadigde status** of het aantal opeenvolgende mislukte tests dat moet optreden voordat wordt besloten dat een VM een beschadigde status heeft.|
    | | |

3. Laat de overige standaardwaarden staan en selecteer **OK**.

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer-regel wordt gebruikt om de verdeling van het verkeer over de VM's te definiëren. U definieert de front-end-IP-configuratie voor het inkomende verkeer en de back-end-IP-groep om het verkeer te ontvangen. De bron- en doelpoort worden in de regel gedefinieerd. 

In deze sectie maakt u een load balancer-regel:

* Naam: **myHTTPRule**.
* Naam in de front-end: **LoadBalancerFrontEnd**.
* Luisteren op **poort 80**.
* Hiermee wordt verkeer met gelijke taakverdeling naar de back-end met de naam **myBackendPool** op **poort 80** geleid.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.

2. Selecteer onder **Instellingen** **Taakverdelingsregels** en selecteer vervolgens **Toevoegen**.

3. Gebruik deze waarden om de taakverdelingsregel te configureren:
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **myHTTPRule** in. |
    | IP-versie | Selecteer **IPv4** |
    | IP-adres voor front-end | Selecteer **LoadBalancerFrontEnd** |
    | Protocol | selecteer **TCP**. |
    | Poort | Voer **80** in.|
    | Back-endpoort | Voer **80** in. |
    | Back-end-pool | Selecteer **myBackendPool**.|
    | Statustest | Selecteer **myHealthProbe**. |
    | Time-out voor inactiviteit (minuten) | Verplaats de schuifregelaar naar **15** minuten. |
    | Opnieuw instellen van TCP | Selecteer **Ingeschakeld**. |
    | Uitgaande SNAT (Source Network Address Translation) | Selecteer **(Aanbevolen) Uitgaande regels gebruiken om leden van de back-endgroep toegang te geven tot internet.** |

4. Laat de overige standaardwaarden staan en selecteer **OK**.

>[!NOTE]
>De virtuele machines in de back-end-pool hebben geen uitgaande internetverbinding met deze configuratie. </br> Voor meer informatie over het bieden van uitgaande connectiviteit raadpleegt u: </br> **[Uitgaande verbindingen in Azure](load-balancer-outbound-connections.md)**</br> Opties voor het bieden van connectiviteit: </br> **[Load balancer-configuratie voor alleen uitgaand verkeer](egress-only.md)** </br> **[Wat is Azure Virtual Network NAT?](../virtual-network/nat-overview.md)**

## <a name="create-backend-servers"></a>Back-endservers maken

In deze sectie doet u het volgende:

* Maak drie virtuele machines voor de back-endpool van de load balancer.
* Installeer IIS op de virtuele machines om de load balancer te testen.

### <a name="create-virtual-machines"></a>Virtuele machines maken

In deze sectie maakt u drie VM's (**myVM1**, **myVM2** en **myVM3**).

Deze VM's worden toegevoegd aan de back-endpool van de load balancer die eerder is gemaakt.

1. Selecteer in de linkerbovenhoek van de portal de optie **Een resource maken** > **Compute** > **Virtuele machine**. 
   
2. In **Een virtuele machine maken** typt of selecteert u de waarden op het tabblad **Basisinformatie**:

    | Instelling | Waarde                                          |
    |-----------------------|----------------------------------|
    | **Projectgegevens** |  |
    | Abonnement | Selecteer uw Azure-abonnement |
    | Resourcegroep | Selecteer **CreateIntLBQS-rg** |
    | **Exemplaardetails** |  |
    | Naam van de virtuele machine | Voer **myVM1** in |
    | Regio | Selecteer **Europa - west** |
    | Beschikbaarheidsopties | Selecteer **Beschikbaarheidszones** |
    | Beschikbaarheidszone | Selecteer **1** |
    | Installatiekopie | Selecteer **Windows Server 2019 Datacenter** |
    | Azure Spot-exemplaar | Selecteer **Nee** |
    | Grootte | Kies een VM-grootte of kies de standaardinstelling |
    | **Beheerdersaccount** |  |
    | Gebruikersnaam | Voer een gebruikersnaam in |
    | Wachtwoord | Voer een wachtwoord in |
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in |

3. Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.
  
4. Op het tabblad Netwerken selecteert u of voert u het volgende in:

    | Instelling | Waarde |
    |-|-|
    | **Netwerkinterface** |  |
    | Virtueel netwerk | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Openbare IP | Selecteer **Geen** |
    | NIC-netwerkbeveiligingsgroep | Selecteer **Geavanceerd**|
    | Netwerkbeveiligingsgroep configureren | Selecteer **Nieuw maken**. </br> Voer in **Netwerkbeveiligingsgroep maken** bij **Naam** **myNSG** in. </br> Selecteer **OK** |
    | **Taakverdeling**  |
    | Wilt u deze virtuele machine achter een bestaande taakverdelingsoplossing plaatsen? | Selecteer **Ja** |
    | **Instellingen voor taakverdeling** |
    | Opties voor taakverdeling | Selecteer **Azure-taakverdeling** |
    | Een load balancer selecteren | Selecteer **myLoadBalancer**  |
    | Een back-endpool selecteren | Selecteer **myBackendPool** |
   
5. Selecteer **Controleren + maken**. 
  
6. Controleer de instellingen en selecteer vervolgens **Maken**.

7. Volg de stappen 1 tot en met 8 om nog twee VM's te maken met de volgende waarden en alle andere instellingen hetzelfde als **myVM1**:

    | Instelling | VM 2 | VM 3 |
    | ------- | ----- | ---- |
    | Naam |  **myVM2** | **myVM3** |
    | Beschikbaarheidszone | **2** | **3** |
    | Netwerkbeveiligingsgroep | Het bestaande **myNSG** selecteren| Het bestaande **myNSG** selecteren |


# <a name="basic-sku"></a>[**Basis-SKU**](#tab/option-1-create-internal-load-balancer-basic)

>[!NOTE]
>Voor productieworkloads wordt de load balancer uit de Standard SKU aanbevolen.  Zie **[Azure Load Balancer-SKU's](skus.md)** voor meer informatie over SKU's.

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Resources voor de Basic-load balancer die worden gemaakt in de quickstart." border="false":::

In deze sectie maakt u een load balancer die taken van virtuele machines verdeelt. 

Wanneer u een interne load balancer maakt, wordt een virtueel netwerk geconfigureerd als netwerk voor de load balancer. 

Een privé-IP-adres in het virtuele netwerk wordt geconfigureerd als de front-end (standaard **LoadBalancerFrontend** genoemd) voor de load balancer. 

Het front-end-IP-adres kan **Statisch** of **Dynamisch** zijn.

## <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

In deze sectie gaat u een virtueel netwerk en een subnet maken.

1. Selecteer in de linkerbovenhoek van het scherm **Een resource maken > Netwerken > Virtueel netwerk** of zoek naar **Virtueel netwerk** in het zoekvak.

2. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens op het tabblad **Basisinstellingen**:

    | **Instelling**          | **Waarde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projectgegevens**  |                                                                 |
    | Abonnement     | Selecteer uw Azure-abonnement                                  |
    | Resourcegroep   | Selecteer **CreateIntLBQS-rg** |
    | **Exemplaardetails** |                                                                 |
    | Naam             | Voer **myVNet** in                                    |
    | Regio           | Selecteer **Europa - west** |

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

## <a name="create-load-balancer"></a>Load balancer maken

1. Selecteer linksboven in het scherm de optie **Een resource maken** > **Netwerken** > **Load balancer**.

2. Typ of selecteer de volgende informatie op het tabblad **Basisbeginselen** van de pagina **Load balancer maken**: 

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer **CreateIntLBQS** die u in de vorige stap hebt gemaakt.|
    | Naam                   | Voer **myLoadBalancer** in                                   |
    | Regio         | Selecteer **Europa - west**.                                        |
    | Type          | selecteer **Intern**.                                        |
    | SKU           | Selecteer **Basic** |
    | Virtueel netwerk | Selecteer **myVNet** die u in de vorige stap hebt gemaakt. |
    | Subnet  | Selecteer **myBackendSubnet** die u in de vorige stap hebt gemaakt. |
    | IP-adrestoewijzing | selecteer **Dynamisch**. |

3. Accepteer de standaardwaarden voor de overige instellingen en selecteer **Beoordelen en maken**.

4. Selecteer in het deelvenster **Beoordelen en maken** de optie **Maken**.   

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-basic-internal-load-balancer.png" alt-text="Een interne load balancer van het type Basic maken." border="true":::

## <a name="create-load-balancer-resources"></a>Resources voor load balancer maken

In deze sectie configureert u het volgende:

* Load balancer-instellingen voor een back-endadresgroep.
* Een statustest.
* Een load balancer-regel.

### <a name="create-a-backend-pool"></a>Een back-endpool maken

De back-endadresgroep bevat de IP-adressen van de virtuele netwerkinterfacekaarten (NIC's) die zijn verbonden met de load balancer. 

Maak de back-endadres groep **myBackendPool** om virtuele machines voor het verdelen van internetverkeer in te bewaren.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.

2. Selecteer onder **Instellingen** de optie **Back-endpools** en selecteer vervolgens **Toevoegen**.

3. Op de pagina **Een back-endpool toevoegen** voert u het volgende in of selecteert u het volgende:
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **myBackendPool** in. |
    | Virtueel netwerk | Selecteer **myVNet**. |
    | Gekoppeld aan | **Virtuele machines** selecteren |

4. Selecteer **Toevoegen**.

### <a name="create-a-health-probe"></a>Een statustest maken

De load balancer controleert de status van uw app met een statustest. 

De statustest voegt VM's toe aan of verwijdert ze van de load balancer op basis van hun reactie op statuscontroles. 

Maak een statustest genaamd **myHealthProbe** om de status van de VM's te bewaken.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.

2. Selecteer onder **Instellingen** de optie **Statustests** en selecteer vervolgens **Toevoegen**.
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **myHealthProbe** in. |
    | Protocol | Selecteer **HTTP**. |
    | Poort | Voer **80** in.|
    | Pad | **/** invoeren |
    | Interval | Voer **15** in als waarde voor het **Interval** in seconden tussen tests. |
    | Drempelwaarde voor beschadigde status | Selecteer **2** als het aantal voor de **Drempelwaarde voor beschadigde status** of het aantal opeenvolgende mislukte tests dat moet optreden voordat wordt besloten dat een VM een beschadigde status heeft.|

3. Selecteer **OK**.

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer-regel wordt gebruikt om de verdeling van het verkeer over de VM's te definiëren. U definieert de front-end-IP-configuratie voor het inkomende verkeer en de back-end-IP-groep om het verkeer te ontvangen. De bron- en doelpoort worden in de regel gedefinieerd. 

In deze sectie maakt u een load balancer-regel:

* Naam: **myHTTPRule**.
* Naam in de front-end: **LoadBalancerFrontEnd**.
* Luisteren op **poort 80**.
* Hiermee wordt verkeer met gelijke taakverdeling naar de back-end met de naam **myBackendPool** op **poort 80** geleid.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.

2. Selecteer onder **Instellingen** **Taakverdelingsregels** en selecteer vervolgens **Toevoegen**.

3. Gebruik deze waarden om de taakverdelingsregel te configureren:
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **myHTTPRule** in. |
    | IP-versie | Selecteer **IPv4** |
    | IP-adres voor front-end | Selecteer **LoadBalancerFrontEnd** |
    | Protocol | selecteer **TCP**. |
    | Poort | Voer **80** in.|
    | Back-endpoort | Voer **80** in. |
    | Back-end-pool | Selecteer **myBackendPool**.|
    | Statustest | Selecteer **myHealthProbe**. |
    | Time-out voor inactiviteit (minuten) | Verplaats de schuifregelaar naar **15** minuten. |
 
4. Laat de overige standaardwaarden staan en selecteer **OK**.

## <a name="create-backend-servers"></a>Back-endservers maken

In deze sectie doet u het volgende:

* Maak drie virtuele machines voor de back-endpool van de load balancer.
* Maak een beschikbaarheidsset voor de virtuele machines.
* Installeer IIS op de virtuele machines om de load balancer te testen.

### <a name="create-virtual-machines"></a>Virtuele machines maken

In deze sectie maakt u drie VM's (**myVM1**, **myVM2**, **myVM3**).

De twee VM's worden toegevoegd aan een beschikbaarheidsset met de naam **myAvailabilitySet**.

Deze VM's worden toegevoegd aan de back-endpool van de load balancer die eerder is gemaakt.

1. Selecteer in de linkerbovenhoek van de portal de optie **Een resource maken** > **Compute** > **Virtuele machine**. 
   
2. In **Een virtuele machine maken** typt of selecteert u de waarden op het tabblad **Basisinformatie**:

    | Instelling | Waarde                                          |
    |-----------------------|----------------------------------|
    | **Projectgegevens** |  |
    | Abonnement | Selecteer uw Azure-abonnement |
    | Resourcegroep | Selecteer **CreateIntLBQS-rg** |
    | **Exemplaardetails** |  |
    | Naam van de virtuele machine | Voer **myVM1** in |
    | Regio | Selecteer **Europa - west** |
    | Beschikbaarheidsopties | **Beschikbaarheidstest** selecteren |
    | Beschikbaarheidsset | Selecteer **Nieuw maken**. </br> Voer **myAvailabilitySet** in bij **Naam**. </br> Selecteer **OK** |
    | Installatiekopie | **Windows Server 2019 Datacenter** |
    | Azure Spot-exemplaar | Selecteer **Nee** |
    | Grootte | Kies een VM-grootte of kies de standaardinstelling |
    | **Beheerdersaccount** |  |
    | Gebruikersnaam | Voer een gebruikersnaam in |
    | Wachtwoord | Voer een wachtwoord in |
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in |

3. Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.
  
4. Op het tabblad Netwerken selecteert u of voert u het volgende in:

    | Instelling | Waarde |
    |-|-|
    | **Netwerkinterface** |  |
    | Virtueel netwerk | **myVNet** selecteren |
    | Subnet | **myBackendSubnet** selecteren |
    | Openbare IP | Selecteer **Geen** |
    | NIC-netwerkbeveiligingsgroep | Selecteer **Geavanceerd**|
    | Netwerkbeveiligingsgroep configureren | Selecteer **Nieuw maken**. </br> Voer in **Netwerkbeveiligingsgroep maken** bij **Naam** **myNSG** in. </br> Selecteer **OK** |
    | **Taakverdeling**  |
    | Wilt u deze virtuele machine achter een bestaande taakverdelingsoplossing plaatsen? | Selecteer **Nee** |

5. Selecteer **Controleren + maken**. 
  
6. Controleer de instellingen en selecteer vervolgens **Maken**.

7. Volg de stappen 1 tot en met 8 om nog twee VM's te maken met de volgende waarden en alle andere instellingen hetzelfde als **myVM1**:

    | Instelling | VM 2 | VM 3 |
    | ------- | ----- | ---- |
    | Naam |  **myVM2** | **myVM3** |
    | Beschikbaarheidsset | **myAvailabilitySet** selecteren | **myAvailabilitySet** selecteren |
    | Netwerkbeveiligingsgroep | Het bestaande **myNSG** selecteren | Het bestaande **myNSG** selecteren |

### <a name="add-virtual-machines-to-the-backend-pool"></a>Virtuele machines toevoegen aan de back-endpool

De virtuele machines die in de vorige stappen zijn gemaakt, moeten worden toegevoegd aan de back-endpool van **myLoadBalancer**.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.

2. Selecteer onder **Instellingen** de optie **Back-endpools** en vervolgens **myBackendPool**.

3. Selecteer in **Gekoppeld aan** de optie **Virtuele machines**.

4. Selecteer in het gedeelte **Virtuele machines** de optie **+ Toevoegen**.

5. Schakel de selectievakjes naast **myVM1**, **myVM2** en **myVM3** in.

6. Selecteer **Toevoegen**.

7. Selecteer **Opslaan**.
---

## <a name="create-test-virtual-machine"></a>Virtuele testmachine maken

In deze sectie maakt u een VM met de naam **myTestVM**.  Deze VM wordt gebruikt om de configuratie van de load balancer te testen.

1. Selecteer in de linkerbovenhoek van de portal de optie **Een resource maken** > **Compute** > **Virtuele machine**. 
   
2. In **Een virtuele machine maken** typt of selecteert u de waarden op het tabblad **Basisinformatie**:

    | Instelling | Waarde                                          |
    |-----------------------|----------------------------------|
    | **Projectgegevens** |  |
    | Abonnement | Selecteer uw Azure-abonnement |
    | Resourcegroep | Selecteer **CreateIntLBQS-rg** |
    | **Exemplaardetails** |  |
    | Naam van de virtuele machine | Voer **myTestVM** in |
    | Regio | Selecteer **Europa - west** |
    | Beschikbaarheidsopties | Selecteer **Geen infrastructuurredundantie vereist** |
    | Installatiekopie | Selecteer **Windows Server 2019 Datacenter** |
    | Azure Spot-exemplaar | Selecteer **Nee** |
    | Grootte | Kies een VM-grootte of kies de standaardinstelling |
    | **Beheerdersaccount** |  |
    | Gebruikersnaam | Voer een gebruikersnaam in |
    | Wachtwoord | Voer een wachtwoord in |
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in |

3. Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.
  
4. Op het tabblad Netwerken selecteert u of voert u het volgende in:

    | Instelling | Waarde |
    |-|-|
    | **Netwerkinterface** |  |
    | Virtueel netwerk | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Openbare IP | Selecteer **Geen**. |
    | NIC-netwerkbeveiligingsgroep | Selecteer **Geavanceerd**|
    | Netwerkbeveiligingsgroep configureren | Selecteer **MyNSG** die u in de vorige stap hebt gemaakt.|
       
5. Selecteer **Controleren + maken**. 
  
6. Controleer de instellingen en selecteer vervolgens **Maken**.

## <a name="install-iis"></a>IIS installeren

1. Selecteer in het linkermenu **Alle services**, selecteer vervolgens **Alle resources** en selecteer daarna in de lijst met resources **myVM1**, die zich in de resourcegroep **CreateIntLBQS-rg** bevindt.

2. Selecteer op de pagina **Overzicht** de optie **Verbinding maken** en daarna **Bastion**.

4. Voer de gebruikersnaam en het wachtwoord in die zijn ingevoerd tijdens het maken van de VM.

5. Selecteer **Verbinding maken**.

6. Ga op de serverdesktop naar **Windows Systeembeheer** > **Windows Powershell**.

7. In het venster PowerShell voert u de volgende opdrachten uit om het volgende te doen:

    * De IIS-server installeren
    * Het standaard iisstart.htm-bestand verwijderen
    * Een nieuw iisstart.htm-bestand toevoegen waarin de naam van de VM wordt weergegeven:

   ```powershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. Sluit de Bastion-sessie met **myVM1**.

9. Herhaal stappen 1 tot en met 6 om IIS en het bijgewerkte bestand iisstart.htm te installeren op **myVM2** en **myVM3**.


## <a name="test-the-load-balancer"></a>Load balancer testen

1. Zoek op het scherm **Overzicht** het privé-IP-adres voor de load balancer op. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer**.

2. Noteer of kopieer het adres naast **Privé IP-adres** in het **Overzicht** van **myLoadBalancer**.

3. Selecteer in het linkermenu **Alle services**, selecteer vervolgens **Alle resources** en selecteer daarna in de lijst met resources **myTestVM**, die zich in de resourcegroep **CreateIntLBQS-rg** bevindt.

4. Selecteer op de pagina **Overzicht** de optie **Verbinding maken** en daarna **Bastion**.

6. Voer de gebruikersnaam en het wachtwoord in die zijn ingevoerd tijdens het maken van de VM.

7. Open **Internet Explorer** op **myTestVM**.

8. Voer het IP-adres uit de vorige stap in in de adresbalk van de browser. De standaardpagina van IIS-webserver wordt weergegeven in de browser.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="In deze schermafbeelding ziet u een browservenster waarin de standaardpagina wordt weergegeven, zoals verwacht." border="true":::
   
U kunt de standaardpagina van de IIS-webserver van elke virtuele machine aanpassen en vervolgens uw webbrowser geforceerd vernieuwen vanaf de clientcomputer om te zien hoe de load balancer verkeer verdeelt over beide virtuele machines.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep, de load balancer en alle gerelateerde resources, wanneer u deze niet meer nodig hebt. Als u dit wilt doen, selecteert u de resourcegroep **CreateIntLBQS-rg** die de resources bevat en selecteert u **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart, gaat u het volgende doen:

* U hebt in Azure een interne load balancer van het type Standard of Basic gemaakt
* U hebt drie VM's aan de load balancer gekoppeld.
* U hebt de verkeersregel en statustest van de load balancer geconfigureerd en vervolgens de load balancer getest. 

Als u meer informatie wilt over Azure Load Balancer, gaat u naar:
> [!div class="nextstepaction"]
> [Wat is Azure Load Balancer?](load-balancer-overview.md)