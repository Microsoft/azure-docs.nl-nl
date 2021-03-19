---
title: "Zelfstudie: Een load balancer voor meerdere regio's maken met behulp van Azure Portal"
titleSuffix: Azure Load Balancer
description: Ga aan de slag met deze zelfstudie en implementeer een Azure Load Balancer voor meerdere regio's met Azure Portal.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/24/2021
ms.openlocfilehash: c16123fae63b89eff57b5c91864d9a947e01b386
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104576914"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-the-azure-portal"></a>Zelfstudie: Een Azure Load Balancer voor meerdere regio's maken met behulp van Azure Portal

Een load balancer voor meerdere regio's zorgt ervoor dat een service wereldwijd beschikbaar is in meerdere Azure-regio's. Als de ene regio uitvalt, wordt het verkeer doorgestuurd naar de dichtstbijzijnde regionale load balancer.  

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een load balancer voor meerdere regio's maken.
> * Een back-end-pool met twee regionale load balancers maken.
> * Maak een load balancer-regel.
> * De load balancer testen.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!IMPORTANT]
> Azure Load Balancer voor meerdere regio's is momenteel in de openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement.
- Twee Azure Load Balancers met een **standaard**-SKU met back-end-pools die in twee verschillende Azure-regio's zijn geïmplementeerd.
    - Zie voor meer informatie over het maken van een regionale standaard load balancer en virtuele machines voor back-end-pools [Quickstart: Een openbare load balancer maken om taken van VM's te verdelen via Azure Portal](quickstart-load-balancer-standard-public-portal.md).
        - Voeg aan de naam van de load balancers, virtuele machines en andere resources in elke regio **-R1** en **-R2** toe. 

## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal

[Meld u aan](https://portal.azure.com) bij Azure Portal.

## <a name="create-cross-region-load-balancer"></a>Een load balancer voor meerdere regio's maken

In deze sectie maakt u een load balancer voor meerdere regio's en een openbaar IP-adres.

1. Selecteer **Een resource maken**. 
2. Voer in het zoekvak **Load Balancer** in. Selecteer **Load Balancer** in de zoek resultaten.
3. Selecteer op de pagina **Load Balancer** de optie **maken**.
4. Typ of selecteer de volgende informatie op het tabblad **Basisbeginselen** van de pagina **Load balancer maken**: 

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer **Nieuwe maken** en voer **CreateCRLBTutorial-rg** in het tekstvak in.|
    | Naam                   | Voer **myLoadBalancer-CR** in                                   |
    | Regio         | Selecteer **VS West**.                                        |
    | Type          | Selecteer **Openbaar**.                                        |
    | SKU           | Behoud de standaard instelling **standaard**. |
    | Laag           | Selecteer **Globaal** |
    | Openbaar IP-adres | Selecteer **Nieuw maken**.|
    | Naam openbaar IP-adres | Typ **myPublicIP-CR** in het tekstvak.|
    | Routeringsvoorkeur| Selecteer **micro soft-netwerk**. </br> Zie [Wat is routerings voorkeur (preview)?](../virtual-network/routing-preference-overview.md)voor meer informatie over routerings voorkeur. |

    > [!NOTE]
    > Een load balancer voor meerdere regio's kan alleen worden geïmplementeerd in de volgende basisregio's: **VS - oost 2, VS - west, Europa - west, Azië - zuidoost, VS - centraal, Europa - noord, Azië - oost**. Zie **https://aka.ms/homeregionforglb** voor meer informatie.


3. Accepteer de standaardwaarden voor de overige instellingen en selecteer **Beoordelen en maken**.

4. Selecteer in het deelvenster **Beoordelen en maken** de optie **Maken**.   

    :::image type="content" source="./media/tutorial-cross-region-portal/create-cross-region.png" alt-text="Een load balancer voor meerdere regio's maken" border="true":::

## <a name="create-backend-pool"></a>Back-endpool maken

In deze sectie voegt u twee regionale standaard load balancers toe aan de back-end-pool van de load balancer voor meerdere regio's.

> [!IMPORTANT]
> Als u deze stappen wilt uitvoeren, moet u ervoor zorgen dat er twee regionale load balancers met back-end-pools zijn geïmplementeerd in uw abonnement.  Zie voor meer informatie **[Quickstart: Een openbare load balancer maken om taken van VM's te verdelen via Azure Portal](quickstart-load-balancer-standard-public-portal.md)** .

Maak de back-endadrespool **myBackendPool-CR** om de regionale standaard load balancers te bevatten.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer-CR** in de lijst met resources.

2. Selecteer onder **Instellingen** de optie **Back-endpools** en selecteer vervolgens **Toevoegen**.

3. Typ op de pagina **Een back-end-pool toevoegen** de naam **myBackendPool-CR**.

4. Selecteer **Toevoegen**.

4. Selecteer **myBackendPool-CR**.

5. Selecteer onder **Load balancers** de vervolgkeuzelijst onder **Load balancer**.

5. Selecteer **myLoadBalancer-R1** of de naam van uw load balancer in regio 1.

6. Selecteer de vervolgkeuzelijst onder **Front-end-IP-configuratie**. Kies **LoadBalancerFrontEnd**.

7. Herhaal stap 4-6 om **myLoadBalancer-R2** toe te voegen.

8. Selecteer **Toevoegen**.

    :::image type="content" source="./media/tutorial-cross-region-portal/add-to-backendpool.png" alt-text="Regionale load balancers toevoegen aan back-end-pool" border="true":::

## <a name="create-a-health-probe"></a>Een statustest maken

In deze sectie maakt u een statustest om de taakverdelingsregel te maken:

* Met de naam **myHealthProbe**.
* Protocol: **TCP**.
* Interval van **5 seconden**.
* Drempelwaarde voor beschadigde status van **twee** storingen.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer-CR** in de lijst met resources.

2. Selecteer onder **Instellingen** de optie **Statustesten**.

3. Gebruik deze waarden om de statustest te configureren:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **myHealthProbe** in. |
    | Protocol | selecteer **TCP**. |
    | Poort | Voer **80** in. |
    | Interval | Voer **5** in. |
    | Drempelwaarde voor beschadigde status | Voer **2** in. |

4. Selecteer **OK**.

    > [!NOTE]
    > De load balancer voor meerdere regio's heeft een ingebouwde statustest. Deze test is een tijdelijke vervanging totdat de taakverdelingsregel werkt.  Zie **[Beperkingen van de load balancer voor meerdere regio's](cross-region-overview.md#limitations)** voor meer informatie.

## <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

In deze sectie maakt u een load balancer-regel:

* Naam: **myHTTPRule**.
* Naam in de front-end: **LoadBalancerFrontEnd**.
* Luisteren op **poort 80**.
* Hiermee wordt verkeer met gelijke taakverdeling naar de back-end met de naam **myBackendPool-CR** op **poort 80** geleid.

    > [!NOTE]
    > De front-endpoort moet overeenkomen met de back-endpoort en de front-endpoort van de regionale load balancers in de back-end-pool.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer-CR** in de lijst met resources.

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
    | Time-out voor inactiviteit (minuten) | Verplaats de schuifregelaar naar **15**. |
    | Opnieuw instellen van TCP | Selecteer **Ingeschakeld**. |

4. Laat de overige standaardwaarden staan en selecteer **OK**.

    :::image type="content" source="./media/tutorial-cross-region-portal/create-lb-rule.png" alt-text="Load balancer-regel maken" border="true":::

## <a name="test-the-load-balancer"></a>Load balancer testen

In deze sectie gaat u de load balancer voor meerdere regio's testen. U maakt verbinding met het openbare IP-adres in een webbrowser.  U stopt de virtuele machines in een van de back-end-pools van de regionale load balancer en bekijkt de failover.

1. Zoek op het scherm **Overzicht** het openbare IP-adres voor de load balancer op. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myPublicIP-CR**.

2. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. De standaardpagina van IIS-webserver wordt weergegeven in de browser.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-1.png" alt-text="Load balancer testen" border="true":::

3. Stop de virtuele machines in de back-end-pool van een van de regionale load balancers.

4. Vernieuw de webbrowser en bekijk de failover van de verbinding naar de andere regionale load balancer.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-2.png" alt-text="Load balancer na een failover testen" border="true":::

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep, de load balancer en alle gerelateerde resources, wanneer u deze niet meer nodig hebt. 

Als u dit wilt doen, selecteert u de resourcegroep **CreateCRLBTutorial-rg** die de resources bevat en selecteert u **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

* Een load balancer voor meerdere regio's gemaakt.
* Regionale standaard load balancers toegevoegd aan de back-end-pool van de load balancer voor meerdere regio's.
* Een load balancer-regel toegevoegd.
* De load balancer getest.

Zie voor meer informatie over de cross-regio load balancer:
> [!div class="nextstepaction"]
> [Load balancer voor meerdere regio's (preview-versie)](cross-region-overview.md)
