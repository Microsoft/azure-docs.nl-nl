---
title: 'Zelfstudie: Een load balancer met meer dan één beschikbaarheidsset in de back-Azure Portal'
titleSuffix: Azure Load Balancer
description: In deze zelfstudie implementeert u een Azure Load Balancer met meer dan één beschikbaarheidsset in de back-endpool.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 04/16/2021
ms.custom: template-tutorial
ms.openlocfilehash: 21ff43217a7b2bd874a384f3b07a48d5223a1be2
ms.sourcegitcommit: 089c2bd1ac4861f43c4b89396d3d056a6eef4913
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107602347"
---
# <a name="tutorial-create-a-load-balancer-with-more-than-one-availability-set-in-the-backend-pool-using-the-azure-portal"></a>Zelfstudie: Een load balancer met meer dan één beschikbaarheidsset in de back-Azure Portal

Als onderdeel van een implementatie met hoge beschikbaarheid worden virtuele machines vaak gegroepeerd in meerdere beschikbaarheidssets. 

Load Balancer ondersteunt meer dan één beschikbaarheidsset met virtuele machines in de back-endpool.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel netwerk maken
> * Een NAT-gateway maken voor uitgaande connectiviteit
> * Een standaard-SKU-Azure Load Balancer
> * Vier virtuele machines en twee beschikbaarheidssets maken
> * Virtuele machines in beschikbaarheidssets toevoegen aan de back-load balancer
> * Load balancer testen

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

In deze sectie maakt u een virtueel netwerk voor de load balancer en de andere resources die in de zelfstudie worden gebruikt.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Voer in het zoekvak boven aan de portal Virtueel **netwerk in.**

3. Selecteer Virtuele netwerken in de **zoekresultaten.**

4. Selecteer **+ Maken.**

5. Voer op **het** tabblad Basisinformatie **van virtueel netwerk maken** de volgende gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ------|
    | **Projectgegevens** |   |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **Nieuw maken**. </br> Voer **Bij Naam in: RgLmultiAVS-rg.**  |
    | **Exemplaardetails** |   |
    | Naam | Voer **myVNet in.** |
    | Region | Selecteer **(US) VS - west 2**. |

6. Selecteer het **tabblad IP-adressen** of de **knop Volgende: IP-adressen** onderaan de pagina.

7. Selecteer op **het tabblad IP-adressen** onder **Subnetnaam** de **standaardinstelling**.

8. Voer in **het deelvenster Subnet** bewerken onder **Subnetnaam** **myBackendSubnet in.**

9. Selecteer **Opslaan**.

10. Selecteer het **tabblad** Beveiliging of de **knop Volgende:** Beveiliging onderaan de pagina.

11. Selecteer op **het tabblad** Beveiliging in **BastionHost de** optie **Inschakelen.**

12. Voer de volgende gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | Bastion-naam | Voer **MyBastionHost in.** |
    | AzureBastionSubnet-adresruimte | Voer **10.1.1.0/27 in.** |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. </br> Voer **myBastionIP** in bij **Naam.** |

13. Selecteer het **tabblad Beoordelen en** maken of de blauwe knop Beoordelen en **maken** onderaan de pagina.

14. Selecteer **Maken**.

## <a name="create-nat-gateway"></a>NAT-gateway maken 

In deze sectie maakt u een NAT-gateway voor uitgaande connectiviteit van de virtuele machines.

1. Voer in het zoekvak boven aan de portal **NAT-gateway in.**

2. Selecteer **NAT-gateways** in de zoekresultaten.

3. Selecteer **+ Maken.**

4. Voer op **het tabblad** Basisbeginselen van **De NAT-gateway (Network Address Translation)** maken de volgende gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** |   |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **VoorlbmultiAVS-rg.** |
    | **Exemplaardetails** |   |
    | NAT-gatewaynaam | Voer **myNATgateway in.** |
    | Region | Selecteer **(US) VS - west 2**. |
    | Beschikbaarheidszone | Selecteer **Geen**. |
    | Time-out voor inactiviteit (minuten) | Voer **15 in.** |

5. Selecteer het **tabblad Uitgaand IP-adres** of selecteer de knop **Volgende: Uitgaand IP-adres** onderaan de pagina.

6. Selecteer **Een nieuw openbaar IP-adres maken** naast Openbare **IP-adressen** op het **tabblad Uitgaand IP-adres.**

7. Voer **myPublicIP-nat** in bij **Naam.**

8. Selecteer **OK**.

9. Selecteer het **tabblad Subnet** of selecteer **de knop Volgende: Subnet** onderaan de pagina.

10. Selecteer **myVNet** in de pull-down onder **Virtueel netwerk.**

11. Schakel het selectievakje in naast **myBackendSubnet.**

12. Selecteer het **tabblad Beoordelen en** maken of selecteer de blauwe knop Beoordelen en **maken** onderaan de pagina.

13. Selecteer **Maken**.

## <a name="create-load-balancer"></a>Load balancer maken

In deze sectie maakt u een load balancer voor de virtuele machines.

1. Voer in het zoekvak boven aan de portal **Load balancer in.**

2. Selecteer **Load balancers** in de zoekresultaten.

3. Selecteer **+ Maken.**

4. Voer op **het** tabblad **Basisinformatie van Load balancer** de volgende gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** |   |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **OmmelbmultiAVS-rg.** |
    | **Exemplaardetails** |   |
    | Naam | Voer **myLoadBalancer** in. |
    | Region | Selecteer **(US) VS - west 2**. |
    | Type | Laat de standaardwaarde **Openbaar staan.** |
    | SKU | Laat de standaardwaarde **Standard staan.** |
    | Laag | Laat de standaardwaarde **Regionale staan.** |
    | **Openbaar IP-adres** |   |
    | Openbaar IP-adres | Laat de standaardinstelling van **Nieuwe maken** staan. |
    | Naam openbaar IP-adres | Voer **myPublicIP-lb in.** |
    | Beschikbaarheidszone | selecteer **Zone-redundant**. |
    | Een openbaar IPv6-adres toevoegen | Laat de standaardwaarde **Nee** staan. |
    | Routeringsvoorkeur | Laat de standaardwaarde **Van Microsoft-netwerk staan.** |

5. Selecteer het **tabblad Beoordelen en** maken of selecteer de blauwe knop Beoordelen en **maken** onderaan de pagina.

6. Selecteer **Maken**.

### <a name="configure-load-balancer-settings"></a>Instellingen load balancer configureren

In deze sectie maakt u een back-endpool voor **myLoadBalancer**.

U maakt een statustest om HTTP en **poort** **80 te bewaken.** De statustest bewaakt de status van de virtuele machines in de back-endpool. 

U maakt een taakverdelingsregel voor poort **80** met uitgaande SNAT uitgeschakeld. De NAT-gateway die u eerder hebt gemaakt, verwerkt de uitgaande connectiviteit van de virtuele machines.

1. Voer in het zoekvak bovenaan de portal **Load balancer in.**

2. Selecteer **Load balancers** in de zoekresultaten.

3. Selecteer **myLoadBalancer**.

4. Selecteer **in myLoadBalancer** **Back-endpools** in **Instellingen.**

5. Selecteer **+ Toevoegen** in **back-endpools.**

6. Typ **of selecteer in Back-end-pool** toevoegen de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **myBackendPool** in. |
    | Virtueel netwerk | Selecteer **myVNet**. |
    | Configuratie van back-endpool | Laat de standaardwaarde van **NIC staan.** |
    | IP-versie | Laat de standaardwaarde **IPv4 staan.** |

7. Selecteer **Toevoegen**.

8. Selecteer **Statustests.**

9. Selecteer **+ Toevoegen**.

10. Voer **in Statustest toevoegen** de volgende gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **myHTTPProbe in.** |
    | Protocol | Selecteer **HTTP**. |
    | Poort | Laat de standaardwaarde **80 staan.** |
    | Pad | Laat de standaardwaarde **/** staan. |
    | Interval | Laat de standaardwaarde **van 5 seconden** staan. |
    | Drempelwaarde voor beschadigde status | Laat de standaardwaarde **staan op 2** opeenvolgende fouten. |

11. Selecteer **Toevoegen**.

12. Selecteer **Taakverdelingsregels.** 

13. Selecteer **+ Toevoegen**.

14. Voer de volgende informatie in of selecteer deze in **Taakverdelingsregel toevoegen:**

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **myHTTPRule** in. |
    | IP-versie | Laat de standaardwaarde **IPv4 staan.** |
    | IP-adres voor front-end | Selecteer **LoadBalancerFrontEnd.** |
    | Protocol | Selecteer de standaardwaarde **van TCP**. |
    | Poort | Voer **80** in. |
    | Back-endpoort | Voer **80** in. |
    | Back-end-pool | Selecteer **myBackendPool**. |
    | Statustest | Selecteer **myHTTPProbe.** |
    | Sessiepersistentie | Laat de standaardwaarde **Geen staan.** |
    | Time-out voor inactiviteit (minuten) | Wijzig de schuifregelaar in **15**. |
    | Opnieuw instellen van TCP | Selecteer **Ingeschakeld**. |
    | Zwevend IP-adres | Laat de standaardwaarde **Uitgeschakeld staan.** |
    | Uitgaande SNAT (Source Network Address Translation) | Laat de standaardwaarde (Aanbevolen) Uitgaande regels gebruiken staan om leden van **de back-endpool** toegang tot internet te geven. |

5. Selecteer **Toevoegen**.

## <a name="create-virtual-machines"></a>Virtuele machines maken

In deze sectie maakt u twee beschikbaarheidsgroepen met twee virtuele machines per groep. Deze machines worden tijdens het maken toegevoegd aan de back-load balancer de back-load balancer. 

### <a name="create-first-set-of-vms"></a>Eerste set VM's maken

1. Selecteer **+ Een resource maken** in de linkerbovenhoek van de portal.

2. In **Nieuw** selecteert u **Virtuele machine**  >  **berekenen.**

3. Voer op **het** tabblad **Basisinformatie van Een virtuele machine maken** de volgende gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** |   |
    | Abonnement | Selecteer uw abonnement |
    | Resourcegroep | Selecteer **OmmelbmultiAVS-rg.** |
    | **Exemplaardetails** |   |
    | Naam van de virtuele machine | Voer **myVM1 in.** |
    | Region | Selecteer **(US) VS - west 2**. |
    | Beschikbaarheidsopties | Selecteer **Beschikbaarheidsset.** |
    | Beschikbaarheidsset | Selecteer **Nieuw maken**. </br> Voer **myAvailabilitySet1** in bij **Naam**. </br> Selecteer **OK**. |
    | Installatiekopie | Selecteer **Windows Server 2019 Datacenter - Gen1.** |
    | Azure Spot-exemplaar | Laat de standaardwaarde van uitgeschakeld. |
    | Grootte | Selecteer een grootte voor de virtuele machine. |
    | **Beheerdersaccount** |   |
    | Gebruikersnaam | Voer een gebruikersnaam in. |
    | Wachtwoord | Voer een wachtwoord in. |
    | **Regels voor binnenkomende poort** |   |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geen**. |

4. Selecteer het **tabblad** Netwerken of selecteer de knop **Volgende:** Schijven en vervolgens **Volgende: Netwerken** onderaan de pagina.

5. Op het tabblad **Netwerken** de volgende informatie invoeren of selecteren:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Netwerkinterface** |   |
    | Virtueel netwerk | Selecteer **myVNet**. |
    | Subnet | Selecteer **myBackendSubnet**. |
    | Openbare IP | Selecteer **Geen**. |
    | NIC-netwerkbeveiligingsgroep | Selecteer **Geavanceerd**. |
    | Netwerkbeveiligingsgroep configureren | Selecteer **Nieuw maken**. </br> Voer **bij Naam** **myNSG in.** </br> Selecteer **+Een binnenkomende regel toevoegen** in Regels voor **binnenkomende verkeer.** </br> Selecteer **HTTP** bij **Service**. </br> Voer **myHTTPrule in** bij **Naam.** </br> Selecteer **Toevoegen**. </br> Selecteer **OK**. | 
    | **Taakverdeling** |   |
    | Deze virtuele machine achter een bestaande taakverdelingsoplossing plaatsen? | Schakel het selectievakje in. |
    | **Instellingen voor taakverdeling** |   |
    | Opties voor taakverdeling | Selecteer **Azure load balancer**. |
    | Een load balancer selecteren | Selecteer **myLoadBalancer**. |
    | Een back-endpool selecteren | Selecteer **myBackendPool**. |

6. Selecteer het **tabblad Beoordelen en** maken of selecteer de blauwe knop Beoordelen en **maken** onderaan de pagina.

7. Selecteer **Maken**.

8. Herhaal stap 1 tot en met zeven om de tweede virtuele machine van de set te maken. Vervang de instellingen voor de VM door de volgende informatie:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **myVM2 in.** |
    | Beschikbaarheidsset | Selecteer **myAvailabilitySet1.** |
    | Virtual Network | Selecteer **myVNet**. |
    | Subnet | Selecteer **myBackendSubnet**. |
    | Openbare IP | Selecteer **Geen**. |
    | Netwerkbeveiligingsgroep | Selecteer **myNSG.** |
    | Opties voor taakverdeling | Selecteer **Azure load balancer**. |
    | Een load balancer selecteren | Selecteer **myLoadBalancer**. |
    | Een back-endpool selecteren | Selecteer **myBackendPool**. |

### <a name="create-second-set-of-vms"></a>Tweede set VM's maken

1. Selecteer **+ Een resource maken** in de linkerbovenhoek van de portal.

2. Selecteer **in Nieuw** de optie Virtuele **machine**  >  **berekenen.**

3. Voer op **het** tabblad **Basisinformatie van Een virtuele machine maken** de volgende gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** |   |
    | Abonnement | Selecteer uw abonnement |
    | Resourcegroep | Selecteer **OmmelbmultiAVS-rg.** |
    | **Exemplaardetails** |   |
    | Naam van de virtuele machine | Voer **myVM3 in.** |
    | Region | Selecteer **(US) VS - west 2**. |
    | Beschikbaarheidsopties | Selecteer **Beschikbaarheidsset.** |
    | Beschikbaarheidsset | Selecteer **Nieuw maken**. </br> Voer **myAvailabilitySet2** in bij **Naam.** </br> Selecteer **OK**. |
    | Installatiekopie | Selecteer **Windows Server 2019 Datacenter - Gen1.** |
    | Azure Spot-exemplaar | Laat de standaardwaarde van uitgeschakeld. |
    | Grootte | Selecteer een grootte voor de virtuele machine. |
    | **Beheerdersaccount** |   |
    | Gebruikersnaam | Voer een gebruikersnaam in. |
    | Wachtwoord | Voer een wachtwoord in. |
    | **Regels voor binnenkomende poort** |   |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geen**. |

4. Selecteer het **tabblad** Netwerken of selecteer de knop **Volgende:** Schijven en vervolgens **Volgende: Netwerken** onderaan de pagina.

5. Op het tabblad **Netwerken** de volgende informatie invoeren of selecteren:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Netwerkinterface** |   |
    | Virtueel netwerk | Selecteer **myVNet**. |
    | Subnet | Selecteer **myBackendSubnet**. |
    | Openbare IP | Selecteer **Geen**. |
    | NIC-netwerkbeveiligingsgroep | Selecteer **Geavanceerd**. |
    | Netwerkbeveiligingsgroep configureren | Selecteer **myNSG.** | 
    | **Taakverdeling** |   |
    | Deze virtuele machine achter een bestaande taakverdelingsoplossing plaatsen? | Schakel het selectievakje in. |
    | **Instellingen voor taakverdeling** |   |
    | Opties voor taakverdeling | Selecteer **Azure load balancer.** |
    | Een load balancer selecteren | Selecteer **myLoadBalancer**. |
    | Een back-endpool selecteren | Selecteer **myBackendPool**. |

6. Selecteer het **tabblad Beoordelen en** maken of selecteer de blauwe knop Beoordelen en **maken** onderaan de pagina.

7. Selecteer **Maken**.

8. Herhaal stap 1 t/m zeven om de tweede virtuele machine van de set te maken. Vervang de instellingen voor de VM door de volgende informatie:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **myVM4 in.** |
    | Beschikbaarheidsset | Selecteer **myAvailabilitySet2.** |
    | Virtual Network | Selecteer **myVNet**. |
    | Netwerkbeveiligingsgroep | Selecteer **myNSG.** |
    | Opties voor taakverdeling | Selecteer **Azure load balancer**. |
    | Een load balancer selecteren | Selecteer **myLoadBalancer**. |
    | Een back-endpool selecteren | Selecteer **myBackendPool**. |

## <a name="install-iis"></a>IIS installeren

In deze sectie gebruikt u de host Azure Bastion u eerder hebt gemaakt om verbinding te maken met de virtuele machines en IIS te installeren.

1. Voer in het zoekvak boven aan de portal de tekst **Virtuele machine in.**

2. Selecteer **Virtuele machines** in de zoekresultaten.

3. Selecteer **myVM1.**

4. Selecteer op **de** pagina Overzicht van myVM1 de optie **Verbinding maken** met  >  **Bastion**.

5. Selecteer **Azure Bastion gebruiken**.

6. Voer de **gebruikersnaam en** het wachtwoord **in die** u hebt gemaakt tijdens het maken van de virtuele machine.

7. Selecteer **Verbinding maken**.

7. Ga op de serverdesktop naar **Windows Systeembeheer** > **Windows Powershell**.

8. In het venster PowerShell voert u de volgende opdrachten uit om het volgende te doen:

    * De IIS-server installeren
    * Het standaard iisstart.htm-bestand verwijderen
    * Een nieuw iisstart.htm-bestand toevoegen waarin de naam van de VM wordt weergegeven:

   ```powershell
    # Install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    Remove-Item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. Sluit de Bastion-sessie met **myVM1**.

9. Herhaal stap 1 tot en met acht **voor myVM2,** **myVM3** en **myVM4.**

## <a name="test-the-load-balancer"></a>Load balancer testen

In deze sectie detecteert u het openbare IP-adres van de load balancer. U gebruikt het IP-adres om de werking van de load balancer.

1. Voer in het zoekvak boven aan de portal openbaar **IP-adres in.**

2. Selecteer **Openbare IP-adressen** in de zoekresultaten.

3. Selecteer **myPublicIP-lb.**

4. Noteer het openbare IP-adres dat wordt vermeld in **het IP-adres** op **de pagina Overzicht** van **myPublicIP-lb:**

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/find-public-ip.png" alt-text="Zoek het openbare IP-adres van de load balancer." border="true":::

5. Open een webbrowser en voer het openbare IP-adres in de adresbalk in:

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/verify-load-balancer.png" alt-text="Test load balancer webbrowser." border="true":::

6. Selecteer Vernieuwen in de browser om te zien hoe het verkeer wordt verdeeld over de andere virtuele machines in de back-endpool.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijdert u de load balancer en de ondersteunende resources met de volgende stappen:

1. Voer in het zoekvak boven aan de portal **Resourcegroep in.**

2. Selecteer **Resourcegroepen** in de zoekresultaten.

3. Selecteer **OmmelbmultiAVS-rg.**

4. Selecteer op de **overzichtspagina van OverviewLBmultiAVS-rg** de optie **Resourcegroep verwijderen.**

5. Voer Bij TYPT U DE NAAM VAN DE **RESOURCEGROEP** in: **RgLmultiAVS-rg.**

6. Selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

* U hebt een virtueel netwerk en een Azure Bastion gemaakt.
* Er is een Azure-Standard Load Balancer.
* U hebt twee beschikbaarheidssets gemaakt met twee virtuele machines per set.
* IIS geïnstalleerd en de load balancer.

Naar het volgende artikel gaan voor meer informatie over het maken van een regio-overschrijdende Azure Load Balancer:
> [!div class="nextstepaction"]
> [Een load balancer voor meerdere regio's maken](tutorial-cross-region-portal.md)

