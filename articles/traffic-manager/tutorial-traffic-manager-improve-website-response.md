---
title: 'Zelfstudie: Website-responsiviteit verbeteren met Azure Traffic Manager'
description: In dit zelfstudie-artikel wordt beschreven hoe u een Traffic Manager-profiel maakt voor het bouwen van een zeer responsieve website.
services: traffic-manager
author: duongau
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: d8262a80fac42f103d571523c75c5064d5d43949
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003817"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Zelfstudie: Website-responsiviteit verbeteren met Traffic Manager

In deze zelfstudie wordt beschreven hoe u Traffic Manager kunt gebruiken om een zeer responsieve website te maken door gebruikersverkeer naar de website met de laagste latentie te sturen. Normaal gesproken is het datacentrum met de laagste latentie het datacentrum dat het dichtst bij ligt in geografische afstand.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Twee virtuele machines maken met daarop een eenvoudige website in IIS
> * Twee virtuele machines voor tests maken om Traffic Manager in actie te zien
> * DNS-naam configureren voor de VM’s met behulp van IIS
> * Een Traffic Manager-profiel maken voor verbeterde websiteprestaties
> * VM-eindpunten toevoegen aan het Traffic Manager-profiel
> * Traffic Manager in werking zien

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U moet voor deze zelfstudie de volgende zaken implementeren om de Traffic Manager in actie te zien:

- Twee instanties van basiswebsites die worden uitgevoerd in verschillende Azure-regio's - **VS - oost** en **Europa - west**.
- Twee virtuele machines voor het testen van de Traffic Manager - Een VM in **VS - oost** en de tweede VM in **Europa - west**. De test-VM's worden gebruikt om te illustreren hoe Traffic Manager gebruikersverkeer naar de website leidt die wordt uitgevoerd in dezelfde regio omdat deze de laagste latentie biedt.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

### <a name="create-websites"></a>Websites maken

In dit gedeelte maakt u twee website-instanties die de twee service-eindpunten voor het Traffic Manager-profiel vormen in twee Azure-regio’s. Om de twee websites te maken, moeten de volgende stappen worden uitgevoerd:

1. Maak twee virtuele machines om een eenvoudige website uit te voeren: een in **VS - oost** en de andere in **Europa - west**.
2. Installeer IIS-server op elke VM en werk de standaardpagina van de website bij die de naam beschrijft van de virtuele machine waarmee een gebruiker is verbonden als deze de website bezoekt.

#### <a name="create-vms-for-running-websites"></a>Virtuele machines maken voor het uitvoeren van websites

In deze sectie maakt u twee VM's (*myIISVMEastUS* en *myIISVMWestEurope*) in de Azure-regio's **US - oost** en **EU - west**.

1. Selecteer **Een resource maken** > **Compute** > **Datacenter met Windows Server 2019** in de linkerbovenhoek van Azure Portal.
2. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:

   - **Abonnement** > **Resourcegroep**: Selecteer **Nieuwe maken** en typ **myResourceGroupTM1**.
   - **Instantiedetails** > **Naam van virtuele machine**: Typ *myIISVMEastUS*.
   - **Instantiedetails** > **Regio**:  Selecteer **VS - oost**.
   - **Administrator-account** > **Gebruikersnaam**:  Voer een gebruikersnaam naar keuze in.
   - **Administrator-account** > **Wachtwoord**:  Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Regels voor binnenkomende poort** > **Openbare poorten voor inkomend verkeer**: Selecteer **Geselecteerde poorten toestaan**.
   - **Regels voor binnenkomende poort** > **Binnenkomende poorten selecteren**: Selecteer **RDP** en **HTTP** in de vervolgkeuzelijst.

3. Selecteer het tabblad **Beheer** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken** en vervolgens **Volgende: Beheer**. Stel bij **Bewaking** **Diagnostische gegevens over opstarten** in op **Uit**.
4. Selecteer **Controleren + maken**.
5. Controleer de instellingen en klik op **Maken**.  
6. Volg de stappen om een tweede VM te maken met de naam *myIISVMWestEurope*, een **resourcegroep** met de naam *myResourceGroupTM2*, met **locatie** *Europa - west* en met alle overige instellingen gelijk aan die voor *myIISVMEastUS*.
7. Het maken van de VM's duurt enkele minuten. Ga niet verder met de overige stappen voordat beide VM's zijn gemaakt.

   ![Een virtuele machine maken](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>IIS installeren en de standaardwebpagina aanpassen

In dit gedeelte installeert u de IIS-server op de twee virtuele machines *myIISVMEastUS* en *myIISVMWEurope* en werkt u de standaardwebpagina bij. De aangepaste websitepagina geeft de naam weer van de virtuele machine waarmee u verbinding maakt als u de website in een webbrowser bezoekt.

1. Selecteer in het linkermenu **Alle resources** en klik in de lijst met resources op *myIISVMEastUS*, die zich in de resourcegroep *myresourceGroupTM1* bevindt.
2. Klik op de pagina **Overzicht** op **Verbinding maken** en selecteer vervolgens in **Verbinding maken met virtuele machine** de optie **RDP-bestand downloaden**.
3. Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine. Mogelijk moet u **Meer opties** en vervolgens **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM.
4. Selecteer **OK**.
5. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als u de waarschuwing ontvangt, selecteert u **Ja** of **Doorgaan** om door te gaan met de verbinding.
6. Ga op de serverdesktop naar **Windows Systeembeheer**>**Serverbeheer**.
7. Start Windows PowerShell op VM1 en gebruik de volgende opdrachten om de IIS-server te installeren en het standaard htm-bestand bij te werken.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![IIS installeren en de webpagina aanpassen](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)
8. Sluit de RDP-verbinding met *myIISVMEastUS*.
9. Herhaal stap 1 tot en met 8 door een RDP-verbinding te maken met de VM *myIISVMWestEurope* in de resourcegroep *myResourceGroupTM2*, zodat u IIS kunt installeren en de standaardwebpagina kunt aanpassen.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>DNS-namen voor virtuele machines configureren met behulp van IIS

Traffic Manager routeert gebruikersverkeer op basis van de DNS-naam van de service-eindpunten. In dit gedeelte configureert u de DNS-namen voor de IIS-servers *myIISVMEastUS* en *myIISVMWestEurope*.

1. Klik in het linkermenu op **Alle resources** en selecteer *myIISVMEastUS* in de lijst met resources, die zich in de resourcegroep *myresourceGroupTM1* bevindt.
2. Selecteer op de pagina **Overzicht** onder **DNS-naam** de optie **Configureren**.
3. Voeg op de pagina **Configuratie** onder het label DNS-naam een unieke naam toe en selecteer vervolgens **Opslaan**.
4. Herhaal stap 1 tot en met 3 voor de VM met de naam *myIISVMWestEurope* die zich in resourcegroep *myResourceGroupTM2* bevindt.

### <a name="create-test-vms"></a>Test-VM’s maken

In dit gedeelte maakt u een VM (*myVMEastUS* en *myVMWestEurope*) in elke Azure-regio (**VS - oost** en **Europa - west**). U zult deze virtuele machines gebruiken om te testen hoe Traffic Manager verkeer naar de dichtstbijzijnde IIS-server routeert als u de website bezoekt.

1. Selecteer **Een resource maken** > **Compute** > **Datacenter met Windows Server 2019** in de linkerbovenhoek van Azure Portal.
2. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:

   - **Abonnement** > **Resourcegroep**: Selecteer **myResourceGroupTM1**.
   - **Instantiedetails** > **Naam van virtuele machine**: Typ *myVMEastUS*.
   - **Instantiedetails** > **Regio**:  Selecteer **VS - oost**.
   - **Administrator-account** > **Gebruikersnaam**:  Voer een gebruikersnaam naar keuze in.
   - **Administrator-account** > **Wachtwoord**:  Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Regels voor binnenkomende poort** > **Openbare poorten voor inkomend verkeer**: Selecteer **Geselecteerde poorten toestaan**.
   - **Regels voor binnenkomende poort** > **Binnenkomende poorten selecteren**: Selecteer **RDP** in de vervolgkeuzelijst.

3. Selecteer het tabblad **Beheer** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken** en vervolgens **Volgende: Beheer**. Stel bij **Bewaking** **Diagnostische gegevens over opstarten** in op **Uit**.
4. Selecteer **Controleren + maken**.
5. Controleer de instellingen en klik op **Maken**.  
6. Volg de stappen om een tweede VM te maken met de naam *myVMWestEurope*, een **resourcegroep** met de naam *myResourceGroupTM2*, met **locatie** *EU - west* en met alle overige instellingen gelijk aan die voor *myVMEastUS*.
7. Het maken van de VM's duurt enkele minuten. Ga niet verder met de overige stappen voordat beide VM's zijn gemaakt.

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

Maak een Traffic Manager-profiel dat gebruikersverkeer omleidt door gebruikers naar het eindpunt met de laagste latentie te sturen.

1. Selecteer linksboven in het scherm de optie **Een resource maken** > **Netwerken** > **Traffic Manager-profiel** > **Maken**.
2. Voer in **Traffic Manager-profiel maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **Maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Naam                   | Deze naam moet uniek zijn binnen de zone trafficmanager.net en resulteert in de DNS-naam, trafficmanager.net, die wordt gebruikt voor het openen van uw Traffic Manager-profiel.                                   |
    | Routeringsmethode          | Selecteer de routeringsmethode **Prestaties**.                                       |
    | Abonnement            | Selecteer uw abonnement.                          |
    | Resourcegroep          | Selecteer de Resourcegroep *myResourceGroupTM1*. |
    | Locatie                | Selecteer **VS - oost**. Deze instelling verwijst naar de locatie van de resourcegroep en heeft geen invloed op het Traffic Manager-profiel dat wereldwijd wordt geïmplementeerd.                              |
    |

    ![Een Traffic Manager-profiel maken](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-eindpunten toevoegen

Voeg de twee VM's toe waarop de IIS-servers worden uitgevoerd - *myIISVMEastUS* & *myIISVMWestEurope* om gebruikersverkeer naar het dichtstbijzijnde eindpunt voor de gebruiker te routeren.

1. Zoek in de zoekbalk van de portal de naam van het Traffic Manager-profiel dat u in de vorige sectie hebt gemaakt en selecteer het profiel in de weergegeven resultaten.
2. Klik in **Traffic Manager-profiel**, in de sectie **Instellingen**, op **Eindpunten** en vervolgens op **Toevoegen**.
3. Voer de volgende informatie in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **OK**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Type                    | Azure-eindpunt                                   |
    | Naam           | myEastUSEndpoint                                        |
    | Doelbrontype           | Openbaar IP-adres                          |
    | Doelbron          | **Kies een openbaar IP-adres** om het overzicht van resources met openbare IP-adressen onder hetzelfde abonnement weer te geven. Selecteer in **Resource** het openbare IP-adres met de naam *myIISVMEastUS-ip*. Dit is het openbare IP-adres van de IIS-server VM in VS - oost.|
    |        |           |

4. Herhaal stap 2 en 3 om nog een eindpunt met de naam *myWestEuropeEndpoint* toe te voegen voor het openbare IP-adres *myIISVMWestEurope-ip* dat is gekoppeld aan de IIS-server-VM met de naam *myIISVMWestEurope*.
5. Als beide eindpunten zijn toegevoegd, worden ze weergegeven in **Traffic Manager-profiel**, samen met de controlestatus **Online**.

    ![Traffic Manager-eindpunt toevoegen](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profiel testen

In dit gedeelte test u hoe de Traffic Manager gebruikersverkeer routeert naar de dichtstbijzijnde VM's waarop de website wordt uitgevoerd om minimale latentie te bieden. Voer de volgende stappen uit om de Traffic Manager in actie te zien:

1. Bepaal de DNS-naam van uw Traffic Manager-profiel.
2. Zie Traffic Manager als volgt in werking:
    - Ga van de test-VM (*myVMEastUS*) in de regio **VS - oost** in een webbrowser naar de DNS-naam van het Traffic Manager-profiel.
    - Ga van de test-VM (*myVMWestEurope*) in de regio **Europa - west** in een webbrowser naar de DNS-naam van het Traffic Manager-profiel.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>De DNS-naam van het Traffic Manager-profiel vaststellen

In deze zelfstudie maakt u voor het gemak gebruik van de DNS-naam van het Traffic Manager-profiel om de websites te bezoeken.

U kunt de DNS-naam van het Traffic Manager-profiel als volgt vaststellen:

1. Zoek in de zoekbalk van de portal de naam van het **Traffic Manager-profiel** dat u in de vorige sectie hebt gemaakt. Klik op het Traffic Manager-profiel in de resultaten die worden weergegeven.
2. Klik op **Overzicht**.
3. Het **Traffic Manager-profiel** geeft de DNS-naam weer van het Traffic Manager-profiel dat u zojuist hebt gemaakt. In productie-implementaties configureert u een aangepaste domeinnaam om met behulp van een DNS CNAME-record naar de Traffic Manager-domeinnaam te verwijzen.

   ![DNS-naam van Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Traffic Manager in werking zien

In dit gedeelte kunt u Traffic Manager in werking zien.

1. Selecteer in het linkermenu **Alle resources** en klik in de lijst met resources op *myVMEastUS*, die zich in de resourcegroep *myresourceGroupTM1* bevindt.
2. Klik op de pagina **Overzicht** op **Verbinding maken** en selecteer vervolgens in **Verbinding maken met virtuele machine** de optie **RDP-bestand downloaden**.
3. Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine. Mogelijk moet u **Meer opties** en vervolgens **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM.
4. Selecteer **OK**.
5. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als u de waarschuwing ontvangt, selecteert u **Ja** of **Doorgaan** om door te gaan met de verbinding.
1. Typ in een webbrowser op de VM *myVMEastUS* de DNS-naam van uw Traffic Manager-profiel om uw website weer te geven. Omdat de VM in **VS - oost** ligt, wordt u doorgestuurd naar de dichtstbijzijnde website die wordt gehost op de dichtstbijzijnde IIS-server *myIISVMEastUS* in **VS - oost**.

   ![Schermopname met het profiel 'Traffic Manager' in een webbrowser.](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Maak vervolgens met behulp van de stappen 1-5 verbinding met de virtuele machine *myVMWestEurope* in **Europa - west** en ga vanaf deze VM naar de domeinnaam van het Traffic Manager-profiel. Omdat de VM in **Europa - west** ligt, wordt u nu doorgestuurd naar de website die wordt gehost op de dichtstbijzijnde IIS-server *myIISVMWestEurope* in **Europa - west**.

   ![Traffic Manager-profiel testen](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroepen (**ResourceGroupTM1** en **ResourceGroupTM2**) als deze niet meer nodig zijn. Selecteer daarvoor de resourcegroep (**ResourceGroupTM1** of **ResourceGroupTM2**) en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verkeer naar een set eindpunten distribueren](traffic-manager-configure-weighted-routing-method.md)
