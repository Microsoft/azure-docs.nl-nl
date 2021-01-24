---
title: Snelstart - Een Private Link-service maken met behulp van Azure Portal
titlesuffix: Azure Private Link
description: In deze quickstart leert u hoe u een Private Link-service kunt maken met behulp van de Azure Portal
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/18/2021
ms.author: allensu
ms.openlocfilehash: d394a475c5121607f70c03437382e104a5d0cbee
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746404"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Snelstart: Een Private Link-service maken met behulp van de Azure-portal

Aan de slag met een Private Link-service die naar uw service verwijst.  Geef Private Link toegang tot uw service of resource die achter een Azure Standard Load Balancer is geïmplementeerd.  Gebruikers van uw service hebben persoonlijke toegang via hun virtuele netwerk.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Een interne load balancer maken

In dit gedeelte maakt u een virtueel netwerken en een interne Azure Load Balancer.

### <a name="virtual-network"></a>Virtueel netwerk

In dit gedeelte maakt u een virtueel netwerk en een subnet om de load balancer te hosten die toegang heeft tot uw Private Link-service.

1. Selecteer in de linkerbovenhoek van het scherm **Een resource maken > Netwerken > Virtueel netwerk** of zoek naar **Virtueel netwerk** in het zoekvak.

2. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens op het tabblad **Basisinstellingen**:

    | **Instelling**          | **Waarde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projectgegevens**  |                                                                 |
    | Abonnement     | Selecteer uw Azure-abonnement                                  |
    | Resourcegroep   | Selecteer **CreatePrivLinkService-rg** |
    | **Exemplaardetails** |                                                                 |
    | Naam             | Voer **myVNet** in                                    |
    | Regio           | Selecteer **US - oost 2** |

3. Selecteer het tabblad **IP-adressen** of klik op de knop **Volgende: IP-adressen** onderaan de pagina.

4. Voer op het tabblad **IP-adressen** deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | IPv4-adresruimte | Voer **10.1.0.0/16** in |

5. Onder **Subnetnaam** selecteert u het woord **standaard**.

6. Voer in **Subnet bewerken** deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | Subnetnaam | Open **mySubnet** |
    | Subnetadresbereik | Voer **10.1.0.0/24** in |

7. Selecteer **Opslaan**.

8. Selecteer het tabblad **Controleren + maken** of klik op de knop **Controleren + maken**.

9. Selecteer **Maken**.

### <a name="create-a-standard-load-balancer"></a>Een standaardversie van een load balancer maken

Maak met behulp van de portal een interne standard load balancer. 

1. Selecteer linksboven in het scherm de optie **Een resource maken** > **Netwerken** > **Load balancer**.

2. Typ of selecteer de volgende informatie op het tabblad **Basisbeginselen** van de pagina **Load balancer maken**: 

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer **CreatePrivLinkService-rg** die in de vorige stap is gemaakt.|
    | Naam                   | Voer **myLoadBalancer** in                                   |
    | Regio         | Selecteer **VS - oost 2**.                                        |
    | Type          | selecteer **Intern**.                                        |
    | SKU           | selecteer **Standard** |
    | Virtueel netwerk | Selecteer **myVNet** die u in de vorige stap hebt gemaakt. |
    | Subnet  | Selecteer **mySubnet** die in de vorige stap is gemaakt. |
    | IP-adrestoewijzing | selecteer **Dynamisch**. |
    | Beschikbaarheidszone | Selecteer **Zone-redundant**. |

3. Accepteer de standaardwaarden voor de overige instellingen en selecteer **Beoordelen en maken**.

4. Selecteer in het deelvenster **Beoordelen en maken** de optie **Maken**.   

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
    | Protocol | selecteer **TCP**. |
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

4. Laat de overige standaardwaarden staan en selecteer **OK**.

## <a name="create-a-private-link-service"></a>Een persoonlijke koppelings service maken

In dit gedeelte maakt u een Private Link-service achter een standard load balancer.

1. Selecteer in de linkerbovenhoek van de Azure-portal **Een resource maken**.

2. Zoek naar **Private Link** in het tekstvak **In de marketplace zoeken**.

3. Selecteer **Maken**.

4. Selecteer in **Overzicht** onder **Private Link-centrum** de blauwe knop **Prive Link-service maken**.

5. Voer in het tabblad **Basis** onder **Private Link-serivce maken** de volgende informatie in:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** |  |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **CreatePrivLinkService-rg**. |
    | **Exemplaardetails** |  |
    | Naam | Voer **myPrivateLinkService** in. |
    | Regio | Selecteer **VS - oost 2**. |

6. Selecteer het tabblad **Uitgaande instellingen** of selecteer **Volgende: Uitgaande instellingen** onderaan de pagina.

7. Voer in het tabblad **Uitgaande instellingen** de volgende informatie in:

    | Instelling | Waarde |
    | ------- | ----- |
    | Load balancer | Selecteer **myLoadBalancer**. |
    | Front-end-IP-adres van load balancer | Selecteer **LoadBalancerFrontEnd (10.1.0.4)** . |
    | NAT-bronsubnet | Selecteer **mySubnet (10.1.0.0/24)** . |
    | TCP-proxy V2 inschakelen | Laat de standaardwaarde **Nee** staan. </br> Selecteer **Ja** als uw toepassing een TCP-proxy v2-header verwacht. |
    | **Instellingen voor persoonlijk IP-adres** |  |
    | Laat de standaardinstellingen staan |  |

8. Selecteer het tabblad **Toegangsbeveiliging** of selecteer **Volgende: Toegangsbeveiliging** onderaan de pagina.

9. Laat de standaardwaarde van **Alleen op rollen gebaseerd toegangsbeheer** in het tabblad **Toegangsbeveiliging** staan.

10. Selecteer het tabblad **Tags** of selecteer **Volgende: Tags** onderaan de pagina.

11. Selecteer het tabblad **Beoordelen en maken** of selecteer **Volgende: Beoordelen en maken** onderaan de pagina.

12. Selecteer **Maken** in het tabblad **Beoordelen en maken**.

Uw persoonlijke koppelings service is gemaakt en kan verkeer ontvangen. Als u verkeers stromen wilt zien, configureert u uw toepassing achter uw standaard load balancer.


## <a name="create-private-endpoint"></a>Privé-eindpunt maken

In deze sectie wijst u de persoonlijke koppelings service toe aan een persoonlijk eind punt. Een virtueel netwerk bevat het persoonlijke eind punt voor de persoonlijke koppelings service. Dit virtuele netwerk bevat de resources die toegang hebben tot uw persoonlijke koppelings service.

### <a name="create-private-endpoint-virtual-network"></a>Virtueel netwerk voor een privé-eind punt maken

1. Selecteer in de linkerbovenhoek van het scherm **Een resource maken > Netwerken > Virtueel netwerk** of zoek naar **Virtueel netwerk** in het zoekvak.

2. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens op het tabblad **Basisinstellingen**:

    | **Instelling**          | **Waarde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projectgegevens**  |                                                                 |
    | Abonnement     | Selecteer uw Azure-abonnement                                  |
    | Resourcegroep   | Selecteer **CreatePrivLinkService-rg** |
    | **Exemplaardetails** |                                                                 |
    | Naam             | **MyVNetPE** invoeren                                    |
    | Regio           | Selecteer **US - oost 2** |

3. Selecteer het tabblad **IP-adressen** of klik op de knop **Volgende: IP-adressen** onderaan de pagina.

4. Voer op het tabblad **IP-adressen** deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | IPv4-adresruimte | Voer **11.1.0.0/16** in |

5. Onder **Subnetnaam** selecteert u het woord **standaard**.

6. Voer in **Subnet bewerken** deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | Subnetnaam | **MySubnetPE** invoeren |
    | Subnetadresbereik | Voer **11.1.0.0/24** in |

7. Selecteer **Opslaan**.

8. Selecteer het tabblad **Controleren + maken** of klik op de knop **Controleren + maken**.

9. Selecteer **Maken**.

### <a name="create-private-endpoint"></a>Privé-eindpunt maken

1. Selecteer in de linkerbovenhoek van het scherm in de portal **Een resource maken** > **Netwerken** > **Private Link**, of typ **Private Link** in het zoekvak.

2. Selecteer **Maken**.

3. Selecteer in **Private link Center** **Privé-eindpunt** in het menu aan de linkerkant.

4. Selecteer **+ Toevoegen** onder **Privé-eindpunten**.

5. Typ of selecteer op het tabblad **Basisgegevens** van **Een privé-eindpunt maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **CreatePrivLinkService-rg**. U hebt deze resourcegroep in de vorige sectie gemaakt.|
    | **Exemplaardetails** |  |
    | Naam  | Voer **myPrivateEndpoint** in. |
    | Regio | Selecteer **VS - oost 2**. |

6. Selecteer het tabblad **Resource** of de knop **Volgende: resource** onderaan de pagina.
    
7. Typ of selecteer in **Resource** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Verbindingsmethode | Selecteer **Verbinding maken met een Azure-resource in mijn directory**. |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcetype | Selecteer **micro soft. Network/privateLinkServices**. |
    | Resource | Selecteer **myPrivateLinkService**. |

8. Selecteer het tabblad **Configuratie** of de knop **Volgende: configuratie** onderaan het scherm.

9. Typ of selecteer in **Configuratie** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Netwerken** |  |
    | Virtual Network | Selecteer **myVNetPE**. |
    | Subnet | Selecteer **mySubnetPE**. |

10. Selecteer het tabblad **controleren + maken** of de knop **controleren + maken** onder aan het scherm.

11. Selecteer **Maken**.

### <a name="ip-address-of-private-endpoint"></a>IP-adres van persoonlijk eind punt

In deze sectie vindt u het IP-adres van het privé-eind punt dat overeenkomt met de load balancer en de persoonlijke koppelings service.

1. Selecteer **resource groepen** in de linkerkolom van het Azure Portal.

2. Selecteer de resource groep **CreatePrivLinkService-RG** .

3. Selecteer in de resource groep **CreatePrivLinkService-RG** **myPrivateEndpoint**.

4. Selecteer op de pagina **overzicht** van **myPrivateEndpoint** de naam van de netwerk interface die aan het persoonlijke eind punt is gekoppeld.  De naam van de netwerk interface begint met **myPrivateEndpoint. nic**.

5. Op de pagina **overzicht** van de NIC van het persoonlijke eind punt wordt het IP-adres van het eind punt weer gegeven in het **privé-IP-adres**.
    

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het gebruik van de persoonlijke koppelings service, verwijdert u de resource groep om de resources op te schonen die in deze Quick Start worden gebruikt.

1. Voer **CreatePrivLinkService-rg** in het zoekvenster boven in de portal in en selecteer **CreatePrivLinkService-rg** in de zoekresultaten.
1. Selecteer **Resourcegroep verwijderen**.
1. Voer in **TYP DE NAAM VAN DE RESOURCEGROEP** **CreatePrivLinkService-rg** in.
1. Selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart, gaat u het volgende doen:

* Een virtueel netwerk gemaakt en een interne Azure Load Balancer.
* Er is een persoonlijke koppelings service gemaakt.
* Er is een virtueel netwerk en een persoonlijk eind punt voor de persoonlijke koppelings service gemaakt.

Voor meer informatie over Azure Privé-eindpunt gaat u naar:
> [!div class="nextstepaction"]
> [Quickstart: Een privé-eindpunt maken met behulp van Azure Portal](create-private-endpoint-portal.md)