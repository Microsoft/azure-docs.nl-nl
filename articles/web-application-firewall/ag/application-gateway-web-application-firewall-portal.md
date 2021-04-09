---
title: 'Zelfstudie: Maken met behulp van portal - Web Application Firewall'
description: In deze zelfstudie leert u hoe u een toepassingsgateway met een Web Application Firewall kunt maken met behulp van de Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: tutorial
ms.date: 03/25/2021
ms.author: victorh
ms.openlocfilehash: 35bede052f06c0fcffe46460a376d10690fd4417
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559605"
---
# <a name="tutorial-create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Zelfstudie: Een toepassingsgateway met een Web Application Firewall maken met behulp van de Azure-portal

In deze zelfstudie wordt uitgelegd hoe u de Azure Portal gebruikt om een toepassingsgateway met een Web Application Firewall (WAF) te maken. De WAF gebruikt [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)-regels om uw toepassing te beveiligen. Deze regels omvatten bescherming tegen aanvallen als SQL-injectie, XSS-aanvallen (cross-site scripting) en sessiekapingen. Nadat u de toepassingsgateway hebt gemaakt, test u deze om te controleren of deze correct werkt. Met Azure Application Gateway kunt u webverkeer van uw toepassing omleiden naar specifieke resources door listeners toe te wijzen aan poorten, regels te maken en resources toe te voegen aan een back-endpool. Deze zelfstudie gebruikt een eenvoudige configuratie met een openbaar front-end-IP, een eenvoudige listener om een enkele site op deze toepassingsgateway te hosten, twee virtuele machines die worden gebruikt voor de back-endpool en een eenvoudige regel voor doorsturen van aanvragen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een toepassingsgateway maken met WAF ingeschakeld
> * De virtuele machines maken die als back-endservers worden gebruikt
> * Een opslagaccount maken en diagnostische gegevens configureren
> * De toepassingsgateway testen

![Voorbeeld van een WAF (Web Application Firewall)](../media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!---If you prefer, you can complete this tutorial using [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md) or [Azure CLI](tutorial-restrict-web-traffic-cli.md).--->

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

1. Selecteer **Een resource maken** in het linkermenu van de Azure-portal. Het venster **Nieuw** wordt weergegeven.

2. Selecteer **Netwerken** en vervolgens **Application Gateway** in de lijst **Aanbevolen**.

### <a name="basics-tab"></a>Tabblad Basisbeginselen

1. Op het tabblad **Basisinformatie** voert u deze waarden in voor de volgende toepassingsgateway-instellingen:

   - **Resourcegroep**: Selecteer **myResourceGroupAG** als de resourcegroep. Als deze nog niet bestaat, selecteert u **Nieuwe maken** om deze te maken.
   - **Naam toepassingsgateway**: Typ *myAppGateway* als naam voor de toepassingsgateway.
   - **Laag**: selecteer **WAF V2**.

     ![Nieuwe toepassingsgateway maken: Basisbeginselen](../media/application-gateway-web-application-firewall-portal/application-gateway-create-basics.png)

2.  Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt. U kunt een nieuw virtueel netwerk maken of een bestaand gebruiken. In dit voorbeeld maakt u een virtueel netwerk op hetzelfde moment dat u de toepassingsgateway maakt. Instanties van toepassingsgateways worden in afzonderlijke subnetten gemaakt. In dit voorbeeld maakt u twee subnetten: één voor de toepassingsgateway en één voor de back-endservers.

    Onder **virtueel netwerk configureren** selecteert u **nieuwe maken** om een nieuw virtueel netwerk te maken. Voer in het venster **Virtueel netwerk maken** dat wordt geopend, de volgende waarden in om het virtuele netwerk en twee subnetten te maken:

    - **Naam**: Typ *myVnet* als naam voor het virtuele netwerk.

    - **Subnetnaam** (subnet van toepassingsgateway): Het raster **Subnetten** geeft een subnet met de naam *Standaard* weer. Wijzig de naam van dit subnet in *myAGSubnet*.<br>Het subnet van de toepassingsgateway kan alleen bestaan uit toepassingsgateways. Andere resources zijn niet toegestaan.

    - **Subnetnaam** (subnet van back-endserver): In de tweede rij van het raster **Subnetten** voert u in de kolom **Subnetnaam**, *myBackendSubnet* in.

    - **Adresbereik** (subnet van back-endserver): In de tweede rij van het raster **Subnetten** voert u een adresbereik in dat niet overlapt met het adresbereik van *myAGSubnet*. Als het adresbereik van *myAGSubnet* bijvoorbeeld 10.0.0.0/24 is, voert u *10.0.1.0/24* in voor het adresbereik van *myBackendSubnet*.

    Selecteer **OK** om het venster **Virtueel netwerk maken** te sluiten en de instellingen van het virtuele netwerk op te slaan.

     ![Nieuwe toepassingsgateway maken: virtueel netwerk](../media/application-gateway-web-application-firewall-portal/application-gateway-create-vnet.png)
    
3. Accepteer in het tabblad **Basisinstellingen** de standaardwaarden voor de overige instellingen en selecteer dan **Volgende: Front-ends**.

### <a name="frontends-tab"></a>Tabblad Front-ends

1. Controleer in het tabblad **Frontends** of **Front-end-IP-adres** is ingesteld op **Openbaar**. <br>U kunt afhankelijk van uw use-case het Frontend-IP configureren als openbaar of privé. In dit voorbeeld kiest u een openbaar front-end-IP.
   > [!NOTE]
   > Voor de Application Gateway v2 SKU kunt u alleen een **openbare** front-end-IP-configuratie kiezen. De privé frontend-IP-configuratie is op dit moment niet ingeschakeld voor deze v2-SKU.

2. Kies **Nieuw toevoegen** voor het **open bare IP-adres** en voer *myAGPublicIPAddress* in voor de naam van het open bare IP-adres en selecteer vervolgens **OK**. 

     ![Nieuwe toepassingsgateway maken: front-ends](../media/application-gateway-web-application-firewall-portal/application-gateway-create-frontends.png)

3. Selecteer **Volgende: Back-ends**.

### <a name="backends-tab"></a>Tabblad Back-ends

De back-endpool word gebruikt om aanvragen te routeren naar de back-endservers die de aanvraag verwerken. Back-endpools kunnen bestaan uit NIC's, virtuele-machineschaalsets, openbare IP's, interne IP's, FQDN's (Fully Qualified Domain Name) en multitenant back-ends als Azure App Service. In dit voor beeld maakt u een lege back-end-pool met uw toepassings gateway en voegt u later back-endservers toe aan de back-end-groep.

1. Selecteer op het tabblad **back** -end **een back-end-groep toevoegen**.

2. Voer in het venster **Een back-endpool toevoegen** dat wordt geopend, de volgende waarden in om een lege back-endpool te maken:

    - **Naam**: Voer *myBackendPool* in als naam van de back-endpool.
    - **Een back-endpool zonder doelen toevoegen**: Selecteer **Ja** om een back-endpool zonder doelen te maken. U voegt na het maken van de toepassingsgateway de back-enddoelen toe.

3. Selecteer in het venster **Een back-endpool maken** de optie **Toevoegen** om de configuratie van de back-endpool op te slaan en terug te keren naar het tabblad **Back-ends**.

     ![Nieuwe toepassingsgateway maken: back-ends](../media/application-gateway-web-application-firewall-portal/application-gateway-create-backends.png)

4. Selecteer in het tabblad **Back-ends** de optie **Volgende: Configuratie**.

### <a name="configuration-tab"></a>Tabblad Configuratie

In het tabblad **Configuratie** verbindt u de front-end- en de back-endpool die u hebt gemaakt met een regel voor doorsturen.

1. Selecteer **een regel voor door sturen toevoegen** in de kolom **routerings regels** .

2. Voer in het venster **Een regel voor doorsturen toevoegen** dat wordt geopend, *myRoutingRule* in als de **Regelnaam**.

3. Voor een regel voor doorsturen is een listener vereist. Voer in het tabblad **Listener** in het venster **Een regel voor doorsturen toevoegen** de volgende waarden in voor de listener:

    - **Naam van listener**: Voer *myListener* in als naam van de listener.
    - **IP van front-end**: Selecteer **Openbaar** om het openbare IP te kiezen dat u voor de front-end hebt gemaakt.
  
      Accepteer de standaardwaarden voor de overige instellingen in het tabblad **Listener** en selecteer vervolgens het tabblad **Back-enddoelen** om de rest van de regel voor doorsturen te configureren.

   ![Nieuwe toepassingsgateway maken: listener](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-listener.png)

4. Selecteer in het tabblad **Back-enddoelen** de optie **myBackendPool** als het **Back-enddoel**.

5. Voor de **http-instelling** selecteert u **Nieuw toevoegen** om een nieuwe http-instelling te maken. De HTTP-instelling bepaalt het gedrag van de regel voor doorsturen. Voer in het venster **Een HTTP-instelling toevoegen** dat wordt geopend, *myHTTPSetting* in als de **naam van de HTTP-instelling**. Accepteer de standaardwaarden voor de overige instellingen in het venster **Een HTTP-instelling toevoegen** en selecteer vervolgens **Toevoegen** om terug te keren naar het venster **Een regel voor doorsturen toevoegen**. 

     ![Nieuwe toepassingsgateway maken: HTTP-instelling](../media/application-gateway-web-application-firewall-portal/application-gateway-create-httpsetting.png)

6. Selecteer in het venster **Een regel voor doorsturen toevoegen** de optie **Toevoegen** om de routeringsregel op te slaan en terug te keren naar het tabblad **Configuratie**.

     ![Een nieuwe toepassingsgateway maken: regel voor doorsturen](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-backends.png)

7. Selecteer **Volgende: Tags** en vervolgens **Volgende: Beoordelen en maken**.

### <a name="review--create-tab"></a>Tabblad Beoordelen en maken

Controleer de instellingen op het tabblad **Beoordelen en maken** en selecteer vervolgens **Maken** om het virtuele netwerk, het openbare IP-adres en de toepassingsgateway te maken. Het kan enkele minuten duren om de toepassingsgateway te maken in Azure. 

Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.

## <a name="add-backend-targets"></a>Back-enddoelen toevoegen

In dit voorbeeld gebruikt u virtuele machines als het doelback-end. U kunt bestaande virtuele machines gebruiken of nieuwe maken. U maakt twee virtuele machines die in Azure worden gebruikt als back-endservers voor de toepassingsgateway.

Hiervoor moet u het volgende doen:

1. Maak twee nieuwe virtuele machines, *myVM* en *myVM2* om te gebruiken als back-endservers.
2. Installeer IIS op de virtuele machines om te controleren of de toepassingsgateway is gemaakt.
3. Voeg de back-endservers toe aan de back-endpool.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer **Een resource maken** in de Azure-portal. Het venster **Nieuw** wordt weergegeven.
2. Selecteer in de lijst **Populair** de optie **Windows Server 2016-gegevenscentrum**. De pagina **Een virtuele machine maken** wordt weergegeven.<br>Toepassingsgateway kan verkeer routeren naar ieder type virtuele machine dat wordt gebruikt in de back-endpool. In dit voorbeeld gebruikt u een Windows Server 2016-gegevenscentrum.
3. Voer deze waarden in op het tabblad **Basisinformatie** voor de volgende instellingen voor de virtuele machine:

    - **Resourcegroep**: Selecteer **myResourceGroupAG** als naam van de resourcegroep.
    - **Naam van virtuele machine**: Typ *myVM* als naam voor de virtuele machine.
    - **Gebruikersnaam**: Voer een naam in voor de gebruikersnaam van de beheerder.
    - **Wacht woord**: Voer een wacht woord in voor het beheerders wachtwoord.
4. Accepteer de overige standaardwaarden en klik op **Volgende: Schijven**.  
5. Accepteer de standaardwaarden op het tabblad **Schijven** en selecteer **Volgende: Netwerken**.
6. Zorg ervoor dat, op het tabblad **Netwerken**, **myVNet** is geselecteerd bij **Virtueel netwerk** en dat **Subnet** is ingesteld op **myBackendSubnet**. Accepteer de overige standaardwaarden en klik op **Volgende: Beheer**.<br>Toepassingsgateway kan communiceren met instanties die zich buiten het virtuele netwerk van de gateway bevinden, maar u moet ervoor zorgen dat er een IP-verbinding is.
7. Op het tabblad **Beheer** stelt u **Diagnostische gegevens over opstarten** in op **Uitschakelen**. Accepteer de overige standaardwaarden en selecteer **Beoordelen en maken**.
8. Controleer de instellingen op het tabblad **Beoordelen en maken**, corrigeer eventuele validatiefouten en selecteer vervolgens **Maken**.
9. Wacht tot de virtuele machine is gemaakt voordat u verder gaat.

### <a name="install-iis-for-testing"></a>IIS installeren voor testen

In dit voorbeeld installeert u IIS alleen op de virtuele machines om te controleren of de toepassingsgateway in Azure is gemaakt.

1. Open [Azure PowerShell](../../cloud-shell/quickstart-powershell.md). Hiertoe selecteert u **Cloud Shell** in de bovenste navigatiebalk van de Azure-portal en vervolgens **PowerShell** in de vervolgkeuzelijst. 

    ![Aangepaste extensie installeren](../media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

2. Stel de locatie parameter voor uw omgeving in en voer de volgende opdracht uit om IIS op de virtuele machine te installeren: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Maak een tweede virtuele machine en installeer IIS met behulp van de stappen die u zojuist hebt voltooid. Gebruik *myVM2* als naam voor de virtuele machine en voor de instelling **VMName** van de cmdlet **Set-AzVMExtension**.

### <a name="add-backend-servers-to-backend-pool"></a>Back-endservers toevoegen aan de back-endpool

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.

2. Selecteer **Back-endpools** in het linkermenu.

3. Selecteer **myBackendPool**.

4. Onder **doel type**, selecteer **virtuele machine** in de vervolg keuzelijst.

5. Onder **doel** selecteert u de bijbehorende netwerk interface voor **myVM** in de vervolg keuzelijst.
1. Herhaal dit voor **myVM2**.

   :::image type="content" source="../media/application-gateway-web-application-firewall-portal/application-gateway-backend.png" alt-text="Back-endservers toevoegen":::


6. Selecteer **Opslaan**.

7. Wacht tot de implementatie is voltooid voordat u doorgaat met de volgende stap.

   
## <a name="create-and-link-a-web-application-firewall-policy"></a>Een Web Application Firewall-beleid maken en ernaar koppelen

Alle WAF-aanpassingen en -instellingen bevinden zich in een afzonderlijk object, een WAF-beleid genoemd. Het beleid moet worden gekoppeld aan uw toepassingsgateway. 

Maak een Basic-WAF-beleid met een beheerde standaardregelset (DRS).

1. Selecteer in de linkerbovenhoek van de Portal de optie **een resource maken**. Zoek naar **WAF**, selecteer **Web Application firewall** en selecteer vervolgens **maken**.
2. Op de pagina **een WAF-beleid maken** , tabblad **basis** gegevens, voer de volgende informatie in of Selecteer deze, accepteer de standaard waarden voor de overige instellingen en selecteer vervolgens **controleren + maken**:

   |Instelling  |Waarde  |
   |---------|---------|
   |Beleid voor     |Regionale WAF (Application Gateway)|
   |Abonnement     |De naam van uw abonnement selecteren|
   |Resourcegroep     |**MyResourceGroupAG** selecteren|
   |Beleidsnaam     |Typ een unieke naam voor uw WAF-beleid.|
1. Selecteer **volgende: beleids instellingen**.
1. Accepteer de standaard instellingen en selecteer vervolgens **volgende: beheerde regels**.
1. Accepteer de standaard instelling en selecteer **volgende: aangepaste regels**.
1. Selecteer **volgende: koppeling**.
1. Selecteer **koppeling toevoegen** en selecteer vervolgens **Application Gateway**.
1. Schakel het selectie vakje in voor **het Toep assen van de Web Application firewall-beleids configuratie, zelfs als deze niet overeenkomt met de huidige configuratie**.
1. Selecteer **Toevoegen**.
1. Selecteer op het tabblad **koppeling** de optie **koppeling toevoegen** en selecteer **Application Gateway**.

   > [!NOTE]
   > Als u een beleid toewijst aan uw Application Gateway (of listener) waaraan al een beleid is toegewezen, wordt het oorspronkelijke beleid overschreven en vervangen door het nieuwe beleid.
4. Selecteer **Controleren en maken** en selecteer vervolgens **Maken**.
1. Selecteer **Volgende: Labels**.
1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

Het is niet nodig IIS te installeren om de toepassingsgateway te maken, maar u hebt het geïnstalleerd om te controleren of het maken van de toepassingsgateway in Azure is geslaagd. Gebruik IIS om de toepassingsgateway te testen:

1. Zoek op de pagina **Overzicht** het openbare IP-adres voor de toepassingsgateway.![Het openbare IP-adres van de toepassingsgateway registreren](../media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png) 

   U kunt ook **Alle resources** selecteren, *myAGPublicIPAddress* invoeren in het zoekvak en het openbare IP-adres vervolgens selecteren in de lijst met zoekresultaten. Het openbare IP-adres wordt weergegeven op de pagina **Overzicht**.
1. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser.
1. Controleer het antwoord. Een geldig antwoord verifieert dat de toepassingsgateway is gemaakt en verbinding kan maken met de back-end.

   ![Toepassingsgateway testen](../media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de bij de toepassingsgateway gemaakte resources niet meer nodig hebt, verwijdert u de resourcegroep. Als u de resourcegroep verwijdert, worden ook de toepassingsgateway en alle gerelateerde resources verwijderd. 

Ga als volgt te werk om de resourcegroep te verwijderen:

1. Selecteer **Resourcegroepen** in het linkermenu van de Azure-portal.
2. Zoek en selecteer **myResourceGroupAG** in de lijst op de pagina **Resourcegroepen**.
3. Selecteer **Resourcegroep verwijderen** op de **pagina van de resourcegroep**.
4. Voer *myResourceGroupAG* in bij **TYP DE RESOURCEGROEPNAAM** en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Web Application Firewall](../overview.md)