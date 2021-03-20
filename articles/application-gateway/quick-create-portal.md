---
title: 'Quickstart: Webverkeer omleiden via de portal'
titleSuffix: Azure Application Gateway
description: In deze quickstart leest u hoe u Azure Portal gebruikt om een Azure Application Gateway te maken die webverkeer doorstuurt naar virtuele machines in een back-endpool.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 01/19/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: c1b17d8c624d4bef74278acc24ece37a736a5ca8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98572966"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Quickstart: Webverkeer omleiden met Azure Application Gateway - Azure Portal

In deze snelstartgids gebruikt u Azure Portal voor het maken van een toepassingsgateway. Vervolgens test u de toepassing om te controleren of alles correct werkt. 

De toepassingsgateway stuurt webverkeer van toepassingen naar specifieke resources in een back-endpool. U wijst listeners toe aan poorten, maakt regels en voegt resources toe aan een back-endpool. Dit artikel gebruikt een eenvoudige configuratie met een openbaar front-end-IP, een eenvoudige listener om een enkele site op de toepassingsgateway te hosten, een eenvoudige regel voor doorsturen van aanvragen en twee virtuele machines in de back-endpool.

U kunt deze snelstartgids ook voltooien met [Azure PowerShell](quick-create-powershell.md) of [Azure CLI](quick-create-cli.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u met uw Azure-account aan bij [Azure Portal](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

U maakt de toepassingsgateway met de tabbladen in de pagina **Een toepassingsgateway maken**.

1. Selecteer in het menu van Azure Portal of op de **startpagina** de optie **Een resource maken**. Het venster **Nieuw** wordt weergegeven.

2. Selecteer **Netwerken** en vervolgens **Application Gateway** in de lijst **Aanbevolen**.

### <a name="basics-tab"></a>Tabblad Basisbeginselen

1. Op het tabblad **Basisinformatie** voert u deze waarden in voor de volgende toepassingsgateway-instellingen:

   - **Resourcegroep**: Selecteer **myResourceGroupAG** als de resourcegroep. Als deze nog niet bestaat, selecteert u **Nieuwe maken** om deze te maken.
   - **Naam toepassingsgateway**: Typ *myAppGateway* als naam voor de toepassingsgateway.

     ![Nieuwe toepassingsgateway maken: Basisbeginselen](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2. Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt. U kunt een nieuw virtueel netwerk maken of een bestaand gebruiken. In dit voorbeeld maakt u een virtueel netwerk op hetzelfde moment dat u de toepassingsgateway maakt. Instanties van toepassingsgateways worden in afzonderlijke subnetten gemaakt. In dit voorbeeld maakt u twee subnetten: één voor de toepassingsgateway en één voor de back-endservers.

    > [!NOTE]
    > [Het beleid voor eindpunten van virtuele netwerken](../virtual-network/virtual-network-service-endpoint-policies-overview.md) wordt momenteel niet ondersteund in een Application Gateway-subnet.

    Maak onder **Virtueel netwerk configureren** een nieuw virtueel netwerk door **Nieuw netwerk maken** te selecteren. Voer in het venster **Virtueel netwerk maken** dat wordt geopend, de volgende waarden in om het virtuele netwerk en twee subnetten te maken:

    - **Naam**: Typ *myVnet* als naam voor het virtuele netwerk.

    - **Subnetnaam** (subnet van toepassingsgateway): Het raster **Subnetten** geeft een subnet met de naam *Standaard* weer. Wijzig de naam van dit subnet in *myAGSubnet*.<br>Het subnet van de toepassingsgateway kan alleen bestaan uit toepassingsgateways. Andere resources zijn niet toegestaan.

    - **Subnetnaam** (subnet van back-endserver): In de tweede rij van het raster **Subnetten** voert u in de kolom **Subnetnaam**, *myBackendSubnet* in.

    - **Adresbereik** (subnet van back-endserver): In de tweede rij van het raster **Subnetten** voert u een adresbereik in dat niet overlapt met het adresbereik van *myAGSubnet*. Als het adresbereik van *myAGSubnet* bijvoorbeeld 10.0.0.0/24 is, voert u *10.0.1.0/24* in voor het adresbereik van *myBackendSubnet*.

    Selecteer **OK** om het venster **Virtueel netwerk maken** te sluiten en de instellingen van het virtuele netwerk op te slaan.

     ![Nieuwe toepassingsgateway maken: virtueel netwerk](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Accepteer in het tabblad **Basisinstellingen** de standaardwaarden voor de overige instellingen en selecteer dan **Volgende: Front-ends**.

### <a name="frontends-tab"></a>Tabblad Front-ends

1. Controleer in het tabblad **Frontends** of **Front-end-IP-adres** is ingesteld op **Openbaar**. <br>U kunt afhankelijk van uw use-case het Frontend-IP configureren als openbaar of privé. In dit voorbeeld kiest u een openbaar front-end-IP.
   > [!NOTE]
   > Voor de toepassingsgateway v2 SKU moet er een **Openbaar** front-end-IP-configuratie zijn. U kunt nog steeds zowel een openbare als een privé-front-end-IP-configuratie hebben, maar momenteel is de configuratie van alleen een privé-front-end (alleen ILB-modus) niet ingeschakeld voor de v2-SKU. 

2. Selecteer **Nieuw toevoegen** voor het **open bare IP-adres** en voer *myAGPublicIPAddress* in voor de naam van het open bare IP-adres en selecteer vervolgens **OK**. 

     ![Nieuwe toepassingsgateway maken: front-ends](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Selecteer **Volgende: Back-ends**.

### <a name="backends-tab"></a>Tabblad Back-ends

De back-endpool word gebruikt om aanvragen te routeren naar de back-endservers die de aanvraag verwerken. Back-endservers kunnen bestaan uit Nic's, virtuele-machine schaal sets, open bare IP-adressen, interne IP-adressen, FQDN-namen (Fully Qualified Domain names) en back-ends met meerdere tenants, zoals Azure App Service. In dit voorbeeld maakt u een lege back-endpool met uw toepassingsgateway en voegt u vervolgens back-enddoelen toe aan de back-endpool.

1. Selecteer op het tabblad **back** -end **een back-end-groep toevoegen**.

2. Voer in het venster **Een back-endpool toevoegen** dat wordt geopend, de volgende waarden in om een lege back-endpool te maken:

    - **Naam**: Voer *myBackendPool* in als naam van de back-endpool.
    - **Een back-endpool zonder doelen toevoegen**: Selecteer **Ja** om een back-endpool zonder doelen te maken. U voegt na het maken van de toepassingsgateway de back-enddoelen toe.

3. Selecteer in het venster **Een back-endpool maken** de optie **Toevoegen** om de configuratie van de back-endpool op te slaan en terug te keren naar het tabblad **Back-ends**.

     ![Nieuwe toepassingsgateway maken: back-ends](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. Selecteer in het tabblad **Back-ends** de optie **Volgende: Configuratie**.

### <a name="configuration-tab"></a>Tabblad Configuratie

In het tabblad **Configuratie** verbindt u de front-end- en de back-endpool die u hebt gemaakt met een regel voor doorsturen.

1. Selecteer **een regel voor door sturen toevoegen** in de kolom **routerings regels** .

2. Voer in het venster **Een regel voor doorsturen toevoegen** dat wordt geopend, *myRoutingRule* in als de **Regelnaam**.

3. Voor een regel voor doorsturen is een listener vereist. Voer in het tabblad **Listener** in het venster **Een regel voor doorsturen toevoegen** de volgende waarden in voor de listener:

    - **Naam van listener**: Voer *myListener* in als naam van de listener.
    - **IP van front-end**: Selecteer **Openbaar** om het openbare IP te kiezen dat u voor de front-end hebt gemaakt.
  
      Accepteer de standaardwaarden voor de overige instellingen in het tabblad **Listener** en selecteer vervolgens het tabblad **Back-enddoelen** om de rest van de regel voor doorsturen te configureren.

   ![Nieuwe toepassingsgateway maken: listener](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-listener.png)

4. Selecteer in het tabblad **Back-enddoelen** de optie **myBackendPool** als het **Back-enddoel**.

5. Selecteer voor de **http-instelling** **nieuwe toevoegen** om een nieuwe http-instelling toe te voegen. De HTTP-instelling bepaalt het gedrag van de regel voor doorsturen. Voer in het venster **Een HTTP-instelling toevoegen** dat wordt geopend, *myHTTPSetting* in als de **naam van de HTTP-instelling** en *80* voor de **Back-endpoort**. Accepteer de standaardwaarden voor de overige instellingen in het venster **Een HTTP-instelling toevoegen** en selecteer vervolgens **Toevoegen** om terug te keren naar het venster **Een regel voor doorsturen toevoegen**. 

     ![Nieuwe toepassingsgateway maken: HTTP-instelling](./media/application-gateway-create-gateway-portal/application-gateway-create-httpsetting.png)

6. Selecteer in het venster **Een regel voor doorsturen toevoegen** de optie **Toevoegen** om de routeringsregel op te slaan en terug te keren naar het tabblad **Configuratie**.

     ![Een nieuwe toepassingsgateway maken: regel voor doorsturen](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Selecteer **Volgende: Tags** en vervolgens **Volgende: Beoordelen en maken**.

### <a name="review--create-tab"></a>Tabblad Beoordelen en maken

Controleer de instellingen op het tabblad **Beoordelen en maken** en selecteer vervolgens **Maken** om het virtuele netwerk, het openbare IP-adres en de toepassingsgateway te maken. Het kan enkele minuten duren om de toepassingsgateway te maken in Azure. Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.

## <a name="add-backend-targets"></a>Back-enddoelen toevoegen

In dit voorbeeld gebruikt u virtuele machines als het doelback-end. U kunt bestaande virtuele machines gebruiken of nieuwe maken. U maakt twee virtuele machines als back-endservers voor de toepassingsgateway.

Hiervoor moet u het volgende doen:

1. Maak twee nieuwe virtuele machines, *myVM* en *myVM2* om te gebruiken als back-endservers.
2. Installeer IIS op de virtuele machines om te controleren of de toepassingsgateway is gemaakt.
3. Voeg de back-endservers toe aan de back-endpool.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer in het menu van Azure Portal of op de **startpagina** de optie **Een resource maken**. Het venster **Nieuw** wordt weergegeven.
2. Selecteer in de lijst **Populair** de optie **Windows Server 2016-gegevenscentrum**. De pagina **Een virtuele machine maken** wordt weergegeven.<br>Toepassingsgateway kan verkeer routeren naar ieder type virtuele machine dat wordt gebruikt in de back-endpool. In dit voor beeld gebruikt u een virtuele machine met Windows Server 2016 Data Center.
3. Voer deze waarden in op het tabblad **Basisinformatie** voor de volgende instellingen voor de virtuele machine:

    - **Resourcegroep**: Selecteer **myResourceGroupAG** als naam van de resourcegroep.
    - **Naam van virtuele machine**: Typ *myVM* als naam voor de virtuele machine.
    - **Regio**: Selecteer dezelfde regio als waarin u de toepassingsgateway hebt gemaakt.
    - **Gebruikersnaam**: Typ een naam voor de gebruikersnaam van de beheerder.
    - **Wachtwoord**: Typ een wachtwoord.
    - **Openbare binnenkomende poorten**: Geen.
4. Accepteer de overige standaardwaarden en klik op **Volgende: Schijven**.  
5. Accepteer de standaardwaarden op het tabblad **Schijven** en selecteer **Volgende: Netwerken**.
6. Zorg ervoor dat, op het tabblad **Netwerken**, **myVNet** is geselecteerd bij **Virtueel netwerk** en dat **Subnet** is ingesteld op **myBackendSubnet**. Accepteer de overige standaardwaarden en klik op **Volgende: Beheer**.<br>Toepassingsgateway kan communiceren met instanties die zich buiten het virtuele netwerk van de gateway bevinden, maar u moet ervoor zorgen dat er een IP-verbinding is.
7. Op het tabblad **Beheer** stelt u **Diagnostische gegevens over opstarten** in op **Uitschakelen**. Accepteer de overige standaardwaarden en selecteer **Beoordelen en maken**.
8. Controleer de instellingen op het tabblad **Beoordelen en maken**, corrigeer eventuele validatiefouten en selecteer vervolgens **Maken**.
9. Wacht tot de virtuele machine is gemaakt voordat u verder gaat.

### <a name="install-iis-for-testing"></a>IIS installeren voor testen

In dit voor beeld installeert u IIS op de virtuele machines om te controleren of de toepassings gateway door Azure is gemaakt.

1. Open Azure PowerShell.

   Selecteer **Cloud Shell** in de bovenste navigatiebalk van Azure-portal en vervolgens **PowerShell** in de vervolgkeuzelijst. 

    ![Aangepaste extensie installeren](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Voer de volgende opdracht uit om IIS op de virtuele machine te installeren. Wijzig indien nodig de parameter *Locatie*: 

    ```azurepowershell
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

1. Selecteer in het menu Azure-portal de optie **Alle resources** of zoek naar en selecteer *Alle resources*. Selecteer vervolgens **myAppGateway**.

2. Selecteer **Back-endpools** in het linkermenu.

3. Selecteer **myBackendPool**.

4. Selecteer onder **Back-enddoelen**, **Doeltype** de optie **Virtuele machine** in de vervolgkeuzelijst.

5. Selecteer onder **Doel** de virtuele machines **myVM** en **myVM2** en de bijbehorende netwerkinterfaces in de vervolgkeuzelijsten.

   > [!div class="mx-imgBorder"]
   > ![Back-endservers toevoegen](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Selecteer **Opslaan**.

7. Wacht tot de implementatie is voltooid voordat u doorgaat met de volgende stap.

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

Het is niet nodig IIS te installeren om de toepassingsgateway te maken, maar u hebt het in deze snelstartgids geïnstalleerd om te controleren of het maken van de toepassingsgateway in Azure is geslaagd. 

Gebruik IIS om de toepassingsgateway te testen:

1. Zoek het openbare IP-adres voor de toepassingsgateway op de bijbehorende pagina **Overzicht**.![Noteer het openbare IP-adres van de toepassingsgateway](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png). Of selecteer **Alle bronnen**, voer in het zoekvak *myAGPublicIPAddress* in en selecteer het adres vervolgens in de zoekresultaten. Het openbare IP-adres wordt weergegeven op de pagina **Overzicht**.
2. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser om het IP-adres te bekijken.
3. Controleer het antwoord. Een geldig antwoord verifieert dat de toepassingsgateway is gemaakt en verbinding kan maken met de back-end.

   ![Toepassingsgateway testen](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Vernieuw de browser meerdere keren en u zou verbindingen moeten zien naar myVM en myVM2.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de bij de toepassingsgateway gemaakte resources niet meer nodig hebt, verwijdert u de resourcegroep. Wanneer u de resourcegroep verwijdert, verwijdert u ook de toepassingsgateway en alle gerelateerde resources.

De resourcegroep verwijderen:

1. Selecteer in het menu Azure-portal de optie **Resourcegroepen** of zoek ernaar en selecteer *Resourcegroepen*.
2. Zoek en selecteer **myResourceGroupAG** in de lijst op de pagina **Resourcegroepen**.
3. Selecteer **Resourcegroep verwijderen** op de **pagina van de resourcegroep**.
4. Voer *myResourceGroupAG* in onder **Typ de naam van de resource groep** en selecteer vervolgens **verwijderen** .

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Een toepassingsgateway configureren met TLS-beëindiging met de Azure-portal](create-ssl-portal.md)
