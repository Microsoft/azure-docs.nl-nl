---
title: 'Zelfstudie: routeren van subnetverkeer configureren met Azure Traffic Manager'
description: In deze zelfstudie wordt uitgelegd hoe u Traffic Manager kunt configureren om verkeer van subnetten van gebruikers naar specifieke eindpunten te routeren.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2021
ms.author: duau
ms.openlocfilehash: 9b916f9942b0459b41d98b952fad072ae48318b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505427"
---
# <a name="tutorial-direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Zelfstudie: Verkeer naar specifieke eindpunten routeren met Traffic Manager op basis van subnets van gebruiker

In dit artikel wordt beschreven hoe u de verkeersrouteringsmethode op basis van subnetten kunt configureren. Met de routerings methode voor het **subnet** Traffic kunt u een set IP-adresbereiken toewijzen aan specifieke eind punten. Wanneer een aanvraag wordt ontvangen door Traffic Manager, wordt het bron-IP-adres van de aanvraag gecontroleerd en wordt het bijbehorende eind punt geretourneerd.

In deze zelf studie wordt gebruikgemaakt van subnet routering, afhankelijk van het IP-adres van de query van de gebruiker, wordt verkeer doorgestuurd naar een interne website of een productie website.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Twee virtuele machines maken met daarop een eenvoudige website in IIS
> * Twee virtuele machines voor tests maken om Traffic Manager in actie te zien
> * DNS-naam configureren voor de VM’s met behulp van IIS
> * Een Traffic Manager-profiel maken om verkeer te routeren op basis van het subnet van een gebruiker
> * VM-eindpunten toevoegen aan het Traffic Manager-profiel
> * Traffic Manager in werking zien

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u de Traffic Manager in actie wilt zien, moet u voor deze zelf studie het volgende implementeren:

- twee basiswebsites in verschillende Azure-regio’s - **VS - oost** (fungeert als interne website) en **Europa - west** (fungeert als productiewebsite).
- twee virtuele machines voor het testen van de Traffic Manager - Een VM in **VS - oost** en de tweede VM in **Europa - west**.

De virtuele machines voor de tests worden gebruikt om te laten zien hoe Traffic Manager verkeer van gebruikers naar de interne website of de productiewebsite routeert op basis van het subnet waar de query van de gebruiker vandaan komt.

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
   - **Binnenkomende poort regels**  >  **Selecteer binnenkomende poorten**: Selecteer **RDP** en **http** in de vervolg keuzelijst.

3. Selecteer het tabblad **Beheer** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken** en vervolgens **Volgende: Beheer**. Stel bij **Bewaking** **Diagnostische gegevens over opstarten** in op **Uit**.
4. Selecteer **Controleren + maken**.
5. Controleer de instellingen en selecteer vervolgens **Maken**.  
6. Volg de stappen om een tweede VM te maken met de naam *myIISVMWestEurope*, een **resourcegroep** met de naam *myResourceGroupTM2*, met **locatie** *Europa - west* en met alle overige instellingen gelijk aan die voor *myIISVMEastUS*.
7. Het maken van de VM's duurt enkele minuten. Ga niet verder met de resterende stappen totdat beide Vm's zijn gemaakt.

#### <a name="install-iis-and-customize-the-default-web-page"></a>IIS installeren en de standaardwebpagina aanpassen

In dit gedeelte installeert u de IIS-server op de twee virtuele machines - *myIISVMEastUS* & *myIISVMWestEurope* en werkt u daarna de standaardpagina van de website bij. Op de pagina aangepaste website ziet u de naam van de virtuele machine waarmee u verbinding maakt wanneer u de website vanuit een webbrowser bezoekt.

1. Selecteer **alle resources** in het menu aan de linkerkant en selecteer vervolgens in de lijst met resources *myIISVMEastUS* die zich in de resource groep *myResourceGroupTM1* bevindt.
2. Selecteer op de pagina **overzicht** de optie **verbinding maken** en selecteer vervolgens in **verbinding maken met virtuele machine** **RDP-bestand downloaden**.
3. Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine. Mogelijk moet u **Meer opties** en vervolgens **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM.
4. Selecteer **OK**.
5. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als u de waarschuwing ontvangt, selecteert u **Ja** of **door gaan** om door te gaan met de verbinding.
6. Ga op de serverdesktop naar **Windows Systeembeheer**>**Serverbeheer**.
7. Start Windows PowerShell op VM *myIISVMEastUS* en gebruik de volgende opdrachten om de IIS-server te installeren en het standaard HTM-bestand bij te werken.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. Sluit de RDP-verbinding met de VM *myIISVMEastUS*.
9. Herhaal stap 1 tot en met 6 door een RDP-verbinding te maken met de VM *myIISVMWestEurope* in de resourcegroep *myResourceGroupTM2*, zodat u IIS kunt installeren en de standaardwebpagina kunt aanpassen.
10. Start Windows PowerShell op de virtuele machine *myIISVMWestEurope* en gebruik de volgende opdrachten om de IIS-server te installeren en het standaard HTM-bestand bij te werken.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>DNS-namen voor virtuele machines configureren met behulp van IIS

Traffic Manager routeert gebruikersverkeer op basis van de DNS-naam van de service-eindpunten. In dit gedeelte configureert u de DNS-namen voor de IIS-servers *myIISVMEastUS* en *myIISVMWestEurope*.

1. Selecteer **alle resources** in het menu aan de linkerkant en selecteer vervolgens in de lijst met resources *myIISVMEastUS* die zich in de resource groep *myResourceGroupTM1* bevindt.
2. Selecteer op de pagina **Overzicht** onder **DNS-naam** de optie **Configureren**.
3. Voeg op de pagina **Configuratie** onder het label DNS-naam een unieke naam toe en selecteer vervolgens **Opslaan**.
4. Herhaal stap 1 tot en met 3 voor de VM met de naam *myIISVMWestEurope* die zich in resourcegroep *myResourceGroupTM2* bevindt.

### <a name="create-test-vms"></a>Test-VM’s maken

In deze sectie maakt u een VM (*myVMEastUS* en *myVMWestEurope*) in elke Azure-regio (**US - oost** en **EU - west**). U gebruikt deze VM's om te testen hoe Traffic Manager gebruikersverkeer routeert op basis van het subnet van de query van de gebruiker.

1. Selecteer **Een resource maken** > **Compute** > **Datacenter met Windows Server 2019** in de linkerbovenhoek van Azure Portal.
2. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:

   - **Abonnement** > **Resourcegroep**: Selecteer **myResourceGroupTM1**.
   - **Instantiedetails** > **Naam van virtuele machine**: Typ *myVMEastUS*.
   - **Instantiedetails** > **Regio**:  Selecteer **VS - oost**.
   - **Administrator-account** > **Gebruikersnaam**:  Voer een gebruikersnaam naar keuze in.
   - **Administrator-account** > **Wachtwoord**:  Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Regels voor binnenkomende poort** > **Openbare poorten voor inkomend verkeer**: Selecteer **Geselecteerde poorten toestaan**.
   - **Binnenkomende poort regels**  >  **Selecteer binnenkomende poorten**: Selecteer **RDP** in de vervolg keuzelijst.

3. Selecteer het tabblad **Beheer** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken** en vervolgens **Volgende: Beheer**. Stel bij **Bewaking** **Diagnostische gegevens over opstarten** in op **Uit**.
4. Selecteer **Controleren + maken**.
5. Controleer de instellingen en selecteer vervolgens **Maken**.  
6. Volg de stappen om een tweede VM te maken met de naam *myVMWestEurope*, een **resourcegroep** met de naam *myResourceGroupTM2*, met **locatie** *EU - west* en met alle overige instellingen gelijk aan die voor *myVMEastUS*.
7. Het maken van de VM's duurt enkele minuten. Ga niet verder met de overige stappen voordat beide VM's zijn gemaakt.

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

Maak een Traffic Manager-profiel waarmee u specifieke eindpunten kunt retourneren op basis van het bron-IP-adres van de aanvraag.

1. Selecteer in de linkerbovenhoek van het scherm de optie **een resource maken**. Zoek naar *Traffic Manager-profiel* en selecteer **Maken**.
2. Voer in het **profiel Traffic Manager maken** de volgende informatie in of Selecteer deze. Accepteer de standaard waarden voor de overige instellingen en selecteer vervolgens **maken**.

    ![Een Traffic Manager-profiel maken](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Naam                   | Deze naam moet uniek zijn binnen de zone trafficmanager.net en resulteert in de DNS-naam, trafficmanager.net, die wordt gebruikt voor het openen van uw Traffic Manager-profiel.                                   |
    | Routeringsmethode          | Selecteer de routeringsmethode **Subnet**.                                       |
    | Abonnement            | Selecteer uw abonnement.                          |
    | Resourcegroep          | Selecteer **Bestaande** en voer *myResourceGroupTM1* in. |

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-eindpunten toevoegen

Voeg de twee virtuele machines toe waarop de IIS-servers worden uitgevoerd (*myIISVMEastUS* & *myIISVMWestEurope*) om verkeer van gebruikers te routeren op basis van het subnet van de query van de gebruiker.

1. Zoek in de zoekbalk van de portal de naam van het Traffic Manager-profiel dat u in de vorige sectie hebt gemaakt en selecteer het profiel in de weergegeven resultaten.
2. Selecteer in **Traffic Manager-profiel**, in de sectie **Instellingen**, de optie **Eindpunten** en selecteer **Toevoegen**.
3. Voer de volgende informatie in of Selecteer deze. Accepteer de standaard waarden voor de overige instellingen en selecteer **OK**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Type                    | Azure-eindpunt                                   |
    | Name           | myInternalWebSiteEndpoint                                        |
    | Doelbrontype           | Openbaar IP-adres                          |
    | Doelbron          | **Kies een openbaar IP-adres** om het overzicht van resources met openbare IP-adressen onder hetzelfde abonnement weer te geven. Selecteer in **Resource** het openbare IP-adres met de naam *myIISVMEastUS-ip*. Dit is het openbare IP-adres van de IIS-server VM in VS - oost.|
    |  Instellingen voor subnetroutering    |   Voeg het IP-adres toe van de test-VM, *myVMEastUS*. Elke gebruikersquery die van deze VM afkomstig is, wordt naar *InternalWebSiteEndpoint* omgeleid.    |

4. Herhaal stap 2 en 3 om nog een eindpunt met de naam *myProdWebsiteEndpoint* toe te voegen voor het openbare IP-adres *myIISVMWestEurope-ip* dat is gekoppeld aan de IIS-server-VM met de naam *myIISVMWestEurope*. Voeg voor **Instellingen voor subnetroutering** het IP-adres van de test-VM toe, *myVMWestEurope*. Elke gebruikersquery van deze test-VM zal naar het eindpunt *myProdWebsiteEndpoint* worden gerouteerd.
5. Wanneer beide eind punten zijn toegevoegd, worden ze weer gegeven in **Traffic Manager profiel** samen met hun controle status als **online**.

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profiel testen

In dit gedeeltetest u hoe de Traffic Manager gebruikersverkeer vanaf een bepaald subnet naar een specifiek eindpunt routeert. Voer de volgende stappen uit om de Traffic Manager in actie te zien:

1. Bepaal de DNS-naam van uw Traffic Manager-profiel.
2. Zie Traffic Manager als volgt in werking:
    - Ga van de test-VM (*myVMEastUS*) in de regio **VS - oost** in een webbrowser naar de DNS-naam van het Traffic Manager-profiel.
    - Ga van de test-VM (*myVMWestEurope*) in de regio **Europa - west** in een webbrowser naar de DNS-naam van het Traffic Manager-profiel.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>De DNS-naam van het Traffic Manager-profiel vaststellen

In deze zelfstudie maakt u voor het gemak gebruik van de DNS-naam van het Traffic Manager-profiel om de websites te bezoeken.

U kunt de DNS-naam van het Traffic Manager-profiel als volgt vaststellen:

1. Zoek in de zoekbalk van de portal de naam van het **Traffic Manager-profiel** dat u in de vorige sectie hebt gemaakt. Selecteer in de resultaten die worden weer gegeven het Traffic Manager-profiel.
2. Selecteer **Overzicht**.
3. Het **Traffic Manager-profiel** geeft de DNS-naam weer van het Traffic Manager-profiel dat u zojuist hebt gemaakt. In productie-implementaties configureert u een aangepaste domeinnaam om met behulp van een DNS CNAME-record naar de Traffic Manager-domeinnaam te verwijzen.

### <a name="view-traffic-manager-in-action"></a>Traffic Manager in werking zien

In dit gedeelte kunt u Traffic Manager in werking zien.

1. Selecteer **alle resources** in het menu aan de linkerkant en selecteer vervolgens in de lijst met resources *myVMEastUS* die zich in de resource groep *myResourceGroupTM1* bevindt.
2. Selecteer op de pagina **overzicht** de optie **verbinding maken** en selecteer vervolgens in **verbinding maken met virtuele machine** **RDP-bestand downloaden**.
3. Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine. Mogelijk moet u **Meer opties** en vervolgens **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM.
4. Selecteer **OK**.
5. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als u de waarschuwing ontvangt, selecteert u **Ja** of **door gaan** om door te gaan met de verbinding.
6. Typ in een webbrowser op de VM *myVMEastUS* de DNS-naam van uw Traffic Manager-profiel om uw website weer te geven. Het IP-adres van VM *UserVMUS* is aan het eindpunt *myInternalWebsiteEndpoint* gekoppeld, dus server *myIISVMEastUS* van de testwebsite wordt in de browser geopend.

7. Maak vervolgens met behulp van de stappen 1-5 verbinding met de virtuele machine *myVMWestEurope* in **EU - west** en ga vanaf deze VM naar de domeinnaam van het Traffic Manager-profiel. Het IP-adres van VM *myVMWestEurope* is aan het eindpunt *myProductionWebsiteEndpoint* gekoppeld, dus server *myIISVMWestEurope* van de testwebsite wordt in de browser geopend.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroepen (**ResourceGroupTM1** en **ResourceGroupTM2**) als deze niet meer nodig zijn. Selecteer daarvoor de resourcegroep (**ResourceGroupTM1** of **ResourceGroupTM2**) en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de subnetrouteringsmethode:

> [!div class="nextstepaction"]
> [Subnetverkeersrouteringsmethode](traffic-manager-routing-methods.md#subnet)
