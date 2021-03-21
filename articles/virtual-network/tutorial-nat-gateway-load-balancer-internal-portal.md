---
title: 'Zelf studie: NAT-gateway integreren met een interne load balancer-Azure Portal'
titleSuffix: Virtual Network NAT
description: In deze zelf studie leert u hoe u een Virtual Network NAT-gateway met een interne Load Balancer integreert met behulp van de Azure Portal.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/19/2021
ms.custom: template-tutorial
ms.openlocfilehash: f25266f5a969514ca515e8cbbec959404428d6fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670845"
---
# <a name="tutorial-integrate-a-nat-gateway-with-an-internal-load-balancer-using-the-azure-portal"></a>Zelf studie: een NAT-gateway met een interne load balancer integreren met behulp van de Azure Portal

In deze zelf studie leert u hoe u een NAT-gateway kunt integreren met een interne load balancer.

Een Azure-Standard Load Balancer is standaard beveiligd. Uitgaande verbindingen worden expliciet gedefinieerd door uitgaande SNAT in te scha kelen (bron netwerk adres omzetting). 

SNAT is ingeschakeld voor een interne back-end-groep via een andere open bare load balancer, netwerk routering of een openbaar IP-adres dat op een virtuele machine is gedefinieerd.

De NAT gateway-integratie vervangt de nood zaak voor de implementatie van een openbaar load balancer, netwerk routering of een openbaar IP-adres dat is gedefinieerd op een virtuele machine in de back-endadresgroep.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Load Balancer maken
> * Maak twee virtuele machines voor de back-end-pool van de Azure Load Balancer
> * Een NAT-gateway maken
> * De uitgaande verbinding van de virtuele machines in de back-end-groep voor load balancer valideren

## <a name="prerequisites"></a>Vereisten

Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

In deze sectie gaat u een virtueel netwerk en een subnet maken.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer in de linkerbovenhoek van het scherm **Een resource maken > Netwerken > Virtueel netwerk** of zoek naar **Virtueel netwerk** in het zoekvak.

2. Selecteer **Maken**. 

3. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens op het tabblad **Basisinstellingen**:

    | **Instelling**          | **Waarde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projectgegevens**  |                                                                 |
    | Abonnement     | Selecteer uw Azure-abonnement                                  |
    | Resourcegroep   | Selecteer **Nieuw maken**. Voer **TutorIntLBNAT-RG** in. </br> Selecteer **OK**. |
    | **Exemplaardetails** |                                                                 |
    | Naam             | Voer **myVNet** in                                    |
    | Regio           | Selecteer **VS - oost** |

4. Selecteer het tabblad **IP-adressen** of klik op de knop **Volgende: IP-adressen** onderaan de pagina.

5. Voer op het tabblad **IP-adressen** deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | IPv4-adresruimte | Voer **10.1.0.0/16** in |

6. Onder **Subnetnaam** selecteert u het woord **standaard**.

7. Voer in **Subnet bewerken** deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | Subnetnaam | Voer **myBackendSubnet** in |
    | Subnetadresbereik | Voer **10.1.0.0/24** in |

8. Selecteer **Opslaan**.

9. Selecteer het tabblad **Beveiliging**.

10. Selecteer onder **BastionHost** de optie **Inschakelen**. Voer deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | Bastion-naam | Voer **myBastionHost** in |
    | AzureBastionSubnet-adresruimte | Voer **10.1.1.0/24** in |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. </br> Voer bij **Naam** de naam **myBastionIP** in. </br> Selecteer **OK**. |


11. Selecteer het tabblad **Controleren + maken** of klik op de knop **Controleren + maken**.

12. Selecteer **Maken**.

## <a name="create-load-balancer"></a>Load balancer maken

In deze sectie maakt u een standaard Azure Load Balancer. 

1. Selecteer **Een resource maken**. 

2. Voer in het zoekvak **Load Balancer** in. Selecteer **Load Balancer** in de zoek resultaten.

3. Selecteer op de pagina **Load Balancer** de optie **maken**.
4. Op de pagina **Load Balancer maken** voert u de volgende informatie in: 

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | **Projectgegevens** |   |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer **TutorIntLBNAT-RG**.|
    | **Exemplaardetails** |    |
    | Naam                   | Voer **myLoadBalancer** in                                   |
    | Regio         | Selecteer **VS Oost**.                                        |
    | Type          | selecteer **Intern**.                                        |
    | SKU           | De standaard **standaard** behouden. |
    | **Virtueel netwerk configureren** |    |
    | Virtueel netwerk | Selecteer **myVNet**. |
    | Subnet | Selecteer **myBackendSubnet**. |
    | IP-adrestoewijzing | selecteer **Dynamisch**. |
    | Beschikbaarheidszone | Selecteer **Zone-redundant** om een tolerante load balancer te maken. </br> Als u een zonegebonden load balancer wilt maken, selecteert u een specifieke zone uit 1, 2 of 3 |
    

5. Accepteer de standaardwaarden voor de overige instellingen en selecteer **Beoordelen en maken**.

6. Selecteer in het deelvenster **Beoordelen en maken** de optie **Maken**.

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

3. Voer op de pagina **een back-endserver toevoegen** de naam **myBackendPool** in als de naam voor uw back-end-groep en selecteer vervolgens **toevoegen**.

### <a name="create-a-health-probe"></a>Een statustest maken

De load balancer controleert de status van uw app met een statustest. 

De statustest voegt VM's toe aan of verwijdert ze van de load balancer op basis van hun reactie op statuscontroles. 

Maak een statustest genaamd **myHealthProbe** om de status van de VM's te bewaken.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.

2. Selecteer onder **Instellingen** de optie **Statustests** en selecteer vervolgens **Toevoegen**.
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **myHealthProbe** in. |
    | Protocol | selecteer **TCP**. |
    | Poort | Voer **80** in.|
    | Interval | Voer **15** in als waarde voor het **Interval** in seconden tussen tests. |
    | Drempelwaarde voor beschadigde status | Selecteer **2** als het aantal voor de **Drempelwaarde voor beschadigde status** of het aantal opeenvolgende mislukte tests dat moet optreden voordat wordt besloten dat een VM een beschadigde status heeft.|
   
3. Laat de overige standaard instellingen ongewijzigd en selecteer **toevoegen**.

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
    | Time-out voor inactiviteit (minuten) | Voer **15** minuten in. |
    | Opnieuw instellen van TCP | Selecteer **Ingeschakeld**. |

4. Laat de overige standaardwaarden staan en selecteer **OK**.


## <a name="create-virtual-machines"></a>Virtuele machines maken

In deze sectie maakt u twee virtuele machines (**myVM1** en **myVM2**) in twee verschillende zones (**zone 1** en **zone 2**).

Deze VM's worden toegevoegd aan de back-endpool van de load balancer die eerder is gemaakt.

1. Selecteer in de linkerbovenhoek van de portal de optie **Een resource maken** > **Compute** > **Virtuele machine**. 
   
2. In **Een virtuele machine maken** typt of selecteert u de waarden op het tabblad **Basisinformatie**:

    | Instelling | Waarde                                          |
    |-----------------------|----------------------------------|
    | **Projectgegevens** |  |
    | Abonnement | Selecteer uw Azure-abonnement |
    | Resourcegroep | Selecteer **TutorIntLBNAT-RG** |
    | **Exemplaardetails** |  |
    | Naam van de virtuele machine | Voer **myVM1** in |
    | Regio | Selecteer **VS - oost** |
    | Beschikbaarheidsopties | Selecteer **Beschikbaarheidszones** |
    | Beschikbaarheidszone | Selecteer **1** |
    | Installatiekopie | Selecteer **Windows Server 2019 Datacenter** |
    | Azure Spot-exemplaar | De standaard instelling behouden |
    | Grootte | Kies een VM-grootte of kies de standaardinstelling |
    | **Beheerdersaccount** |  |
    | Gebruikersnaam | Voer een gebruikersnaam in |
    | Wachtwoord | Voer een wachtwoord in |
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in |
    | **Regels voor binnenkomende poort** |  |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geen** |

3. Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.
  
4. Op het tabblad Netwerken selecteert u of voert u het volgende in:

    | Instelling | Waarde |
    |-|-|
    | **Netwerkinterface** |  |
    | Virtueel netwerk | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Openbare IP | Selecteer **Geen**. |
    | NIC-netwerkbeveiligingsgroep | Selecteer **Geavanceerd**|
    | Netwerkbeveiligingsgroep configureren | Selecteer **Nieuw maken**. </br> Voer in **Netwerkbeveiligingsgroep maken** bij **Naam** **myNSG** in. </br> Selecteer onder **Inkomende regels** de optie **+ Een inkomende regel toevoegen**. </br> Voer bij **Poortbereiken van doel** **80** in. </br> Voer bij **Prioriteit** **100** in. </br> Voer bij **Naam** **myHTTPRule** in. </br> Selecteer **Toevoegen** </br> Selecteer **OK** |
    | **Taakverdeling**  |
    | Deze virtuele machine achter een bestaande oplossing voor taak verdeling plaatsen? | Schakel het selectievakje in. |
    | **Instellingen voor taakverdeling** |
    | Opties voor taakverdeling | **Azure-Load Balancer** selecteren |
    | Een load balancer selecteren | Selecteer **myLoadBalancer**  |
    | Een back-endpool selecteren | Selecteer **myBackendPool** |
   
5. Selecteer **Controleren + maken**. 
  
6. Controleer de instellingen en selecteer vervolgens **Maken**.

7. Volg de stappen 1 tot en met 7 om een virtuele machine te maken met de volgende waarden en alle andere instellingen op dezelfde manier als **myVM1**:

    | Instelling | VM 2 |
    | ------- | ----- |
    | Naam |  **myVM2** |
    | Beschikbaarheidszone | **2** |
    | Netwerkbeveiligingsgroep | Het bestaande **myNSG** selecteren| 

## <a name="create-nat-gateway"></a>NAT-gateway maken

In deze sectie maakt u een NAT-gateway en wijst u deze toe aan het subnet in het virtuele netwerk dat u eerder hebt gemaakt.

1. Selecteer in de linkerbovenhoek van het scherm de optie **een resource maken > netwerk > NAT-gateway** of zoek naar **NAT-gateway** in het zoekvak.

2. Selecteer **Maken**. 

3. In **Create Network Address Translation (NAT)-gateway**, typt of selecteert u deze informatie op het tabblad **basis beginselen** :

    | **Instelling**          | **Waarde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projectgegevens**  |                                                                 |
    | Abonnement     | Selecteer uw Azure-abonnement.                                  |
    | Resourcegroep   | Selecteer **TutorIntLBNAT-RG**. |
    | **Exemplaardetails** |                                                                 |
    | Naam             | **MyNATGateway** invoeren                                    |
    | Regio           | Selecteer **(US) VS - oost**  |
    | Beschikbaarheidszone | Selecteer **Geen**. |
    | Time-out voor inactiviteit (minuten) | Voer **10** in. |

4. Selecteer het tabblad **uitgaand IP-adres** of selecteer de knop **volgende: uitgaand IP-adres** aan de onderkant van de pagina.

5. Op het tabblad **uitgaand IP-adres** voert u de volgende gegevens in of selecteert u deze:

    | **Instelling** | **Waarde** |
    | ----------- | --------- |
    | Openbare IP-adressen | Selecteer **een nieuw openbaar IP-adres maken**. </br> Voer in **naam** **myPublicIP-NAT** in. </br> Selecteer **OK**. |

6. Selecteer het tabblad **subnet** of selecteer de knop **volgende: subnet** onder aan de pagina.

7. Op het tabblad **subnet** selecteert u **MyVNet** in het **virtuele netwerk** .

8. Schakel het selectie vakje naast **myBackendSubnet** in.

9. Selecteer het tabblad **controleren + maken** of selecteer de knop blauwe **revisie + maken** onder aan de pagina.

10. Selecteer **Maken**.

## <a name="test-nat-gateway"></a>NAT-gateway testen

In deze sectie testen we de NAT-gateway. Eerst wordt het open bare IP-adres van de NAT-gateway gedetecteerd. Vervolgens maakt u verbinding met de virtuele test machine en controleert u de uitgaande verbinding via de NAT-gateway.
    
1. Zoek het open bare IP-adres voor de NAT-gateway op het scherm **overzicht** . Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myPublicIP**.

2. Noteer het open bare IP-adres:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-internal-portal/find-public-ip.png" alt-text="Scherm opname van het open bare IP-adres van de NAT-gateway." border="true":::

3. Selecteer **alle services** in het linkermenu, selecteer **alle resources** en selecteer vervolgens in de lijst met resources **myVM1** die zich in de resource groep **TutorIntLBNAT-RG** bevindt.

4. Selecteer op de pagina **Overzicht** de optie **Verbinding maken** en daarna **Bastion**.

5. Selecteer de blauwe knop **Bastion gebruiken**.

6. Voer de gebruikersnaam en het wachtwoord in die zijn ingevoerd tijdens het maken van de VM.

7. Open **Internet Explorer** op **myVM1**.

8. Voer **https://whatsmyip.com** in de adres balk in.

9. Controleer of het weer gegeven IP-adres overeenkomt met het NAT-gateway adres dat u in de vorige stap hebt genoteerd:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-internal-portal/my-ip.png" alt-text="Scherm opname van Internet Explorer met het externe uitgaande IP-adres." border="true":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet wilt blijven gebruiken, verwijdert u het virtuele netwerk, de virtuele machine en de NAT-gateway door de volgende stappen uit te voeren:

1. Selecteer **Resourcegroepen** in het linkermenu.

2. Selecteer de resource groep **TutorIntLBNAT-RG** .

3. Selecteer **Resourcegroep verwijderen**.

4. Voer **TutorIntLBNAT-RG** in en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Azure Virtual Network NAT:
> [!div class="nextstepaction"]
> [Overzicht van Virtual Network NAT](nat-overview.md)
