---
title: 'Zelfstudie: Meerdere websites hosten met behulp van Azure Portal'
titleSuffix: Azure Application Gateway
description: In deze zelfstudie wordt beschreven hoe u een toepassingsgateway maakt waarmee meerdere websites worden gehost via Azure PortaI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: victorh
ms.openlocfilehash: 208bd0fe7f3869cbe15dd27e0b883c467e41c765
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735064"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Zelfstudie: Een toepassingsgateway maken en configureren waarmee meerdere websites worden gehost via Azure PortaI

U kunt Azure PortaI gebruiken om [het hosten van meerdere websites te configureren](multiple-site-overview.md) wanneer u een [toepassingsgateway](overview.md) maakt. In deze zelfstudie definieert u back-endadresgroepen met behulp van virtuele machines. Vervolgens configureert u listeners en regels op basis van twee domeinen om te controleren of webverkeer arriveert op de juiste servers in de groepen. In deze zelf studie wordt gebruikgemaakt van voor beelden van *www.contoso.com* en *www.fabrikam.com*.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een toepassingsgateway maken
> * Virtuele machines maken voor back-endservers
> * Back-endpools maken met de back-endservers
> * Back-endlisteners maken
> * Routeringsregels maken
> * Een CNAME-record in uw domein maken

:::image type="content" source="./media/create-multiple-sites-portal/scenario.png" alt-text="Een toepassingsgateway voor meerdere sites maken":::

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

1. Selecteer **Een resource maken** in het linkermenu van de Azure-portal. Het venster **Nieuw** wordt weergegeven.

2. Selecteer **Netwerken** en vervolgens **Application Gateway** in de lijst **Aanbevolen**.

### <a name="basics-tab"></a>Tabblad Basisbeginselen

1. Op het tabblad **Basisinformatie** voert u deze waarden in voor de volgende toepassingsgateway-instellingen:

   - **Resourcegroep**: Selecteer **myResourceGroupAG** als de resourcegroep. Als deze nog niet bestaat, selecteert u **Nieuwe maken** om deze te maken.
   - **Naam toepassingsgateway**: Typ *myAppGateway* als naam voor de toepassingsgateway.

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png" alt-text="Een toepassingsgateway maken":::

2.  Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt. U kunt een nieuw virtueel netwerk maken of een bestaand gebruiken. In dit voorbeeld maakt u een virtueel netwerk op hetzelfde moment dat u de toepassingsgateway maakt. Instanties van toepassingsgateways worden in afzonderlijke subnetten gemaakt. In dit voorbeeld maakt u twee subnetten: één voor de toepassingsgateway en één voor de back-endservers.

    Selecteer onder **Virtueel netwerk configureren** de optie **Nieuw netwerk maken** om een nieuw virtueel netwerk te maken. Voer in het venster **Virtueel netwerk maken** dat wordt geopend, de volgende waarden in om het virtuele netwerk en twee subnetten te maken:

    - **Naam**: Typ *myVnet* als naam voor het virtuele netwerk.

    - **Subnetnaam** (subnet van toepassingsgateway): Het raster **Subnetten** geeft een subnet met de naam *Standaard* weer. Wijzig de naam van dit subnet in *myAGSubnet*.<br>Het subnet van de toepassingsgateway kan alleen bestaan uit toepassingsgateways. Andere resources zijn niet toegestaan.

    - **Subnetnaam** (subnet van back-endserver): In de tweede rij van het raster **Subnetten** voert u in de kolom **Subnetnaam**, *myBackendSubnet* in.

    - **Adresbereik** (subnet van back-endserver): In de tweede rij van het raster **Subnetten** voert u een adresbereik in dat niet overlapt met het adresbereik van *myAGSubnet*. Als het adresbereik van *myAGSubnet* bijvoorbeeld 10.0.0.0/24 is, voert u *10.0.1.0/24* in voor het adresbereik van *myBackendSubnet*.

    Selecteer **OK** om het venster **Virtueel netwerk maken** te sluiten en de instellingen van het virtuele netwerk op te slaan.

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png" alt-text="VNet maken":::
    
3. Accepteer in het tabblad **Basisinstellingen** de standaardwaarden voor de overige instellingen en selecteer dan **Volgende: Front-ends**.

### <a name="frontends-tab"></a>Tabblad Front-ends

1. Controleer in het tabblad **Frontends** of **Front-end-IP-adres** is ingesteld op **Openbaar**. <br>U kunt afhankelijk van uw use-case het Frontend-IP configureren als openbaar of privé. In dit voorbeeld kiest u een openbaar front-end-IP.
   > [!NOTE]
   > Voor de Application Gateway v2 SKU kunt u alleen een **openbare** front-end-IP-configuratie kiezen. De privé frontend-IP-configuratie is op dit moment niet ingeschakeld voor deze v2-SKU.

2. Selecteer **Nieuw toevoegen** voor het **open bare IP-adres** en voer *myAGPublicIPAddress* in voor de naam van het open bare IP-adres en selecteer vervolgens **OK**. 

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png" alt-text="Nog een VNet maken":::

3. Selecteer **Volgende: Back-ends**.

### <a name="backends-tab"></a>Tabblad Back-ends

De back-endpool word gebruikt om aanvragen te routeren naar de back-endservers die de aanvraag verwerken. Back-endpools kunnen bestaan uit NIC's, virtuele-machineschaalsets, openbare IP-adressen, interne IP-adressen, FQDN's (Fully Qualified Domain Name) en multitenant back-ends als Azure App Service. In dit voorbeeld maakt u een lege back-endpool met uw toepassingsgateway en voegt u vervolgens back-enddoelen toe aan de back-endpool.

1. Selecteer op het tabblad **back** -end **een back-end-groep toevoegen**.

2. Voer in het venster **Een back-endpool toevoegen** dat wordt geopend, de volgende waarden in om een lege back-endpool te maken:

    - **Naam**: Voer *contosoPool* in als naam van de back-endpool.
    - **Een back-endpool zonder doelen toevoegen**: Selecteer **Ja** om een back-endpool zonder doelen te maken. U voegt na het maken van de toepassingsgateway de back-enddoelen toe.

3. Selecteer in het venster **Een back-endpool maken** de optie **Toevoegen** om de configuratie van de back-endpool op te slaan en terug te keren naar het tabblad **Back-ends**.
4. Voeg nu nog een back-end-groep met de naam *fabrikamPool* toe, op dezelfde manier als u de vorige pool hebt toegevoegd.
1. Selecteer **Toevoegen**.

    :::image type="content" source="./media/create-multiple-sites-portal/backend-pools.png" alt-text="Back-ends maken":::

4. Selecteer in het tabblad **Back-ends** de optie **Volgende: Configuratie**.

### <a name="configuration-tab"></a>Tabblad Configuratie

Op het tabblad **Configuratie** verbindt u de front-end- en de back-endpool die u hebt gemaakt met een regel voor doorsturen.

1. Selecteer **een regel voor door sturen toevoegen** in de kolom **routerings regels** .

2. Voer in het venster **Een regel voor doorsturen toevoegen** dat wordt geopend, *contosoRule* in als de **regelnaam**.

3. Voor een regel voor doorsturen is een listener vereist. Voer in het tabblad **Listener** in het venster **Een regel voor doorsturen toevoegen** de volgende waarden in voor de listener:

    - **Regel naam**: *contosoRule*.
    - **Naam van listener**: *contosoListener*.
    - **IP van front-end**: Selecteer **Openbaar** om het openbare IP te kiezen dat u voor de front-end hebt gemaakt.

   Onder **Aanvullende instellingen**:
   - **Type listener**: Meerdere sites
   - **Hostnaam**: **www.contoso.com**

   Accepteer de standaardwaarden voor de overige instellingen in het tabblad **Listener** en selecteer vervolgens het tabblad **Back-enddoelen** om de rest van de regel voor doorsturen te configureren.

   :::image type="content" source="./media/create-multiple-sites-portal/routing-rule.png" alt-text="Een regel voor doorsturen maken":::

4. Selecteer op het tabblad **Back-enddoelen** de optie **contosoPool** als het **back-enddoel**.

5. Voor de **http-instelling** selecteert u **Nieuw toevoegen** om een nieuwe http-instelling te maken. De HTTP-instelling bepaalt het gedrag van de regel voor doorsturen. Voer in het venster **Een HTTP-instelling toevoegen** dat wordt geopend, *contosoHTTPSetting* in als de **naam van de HTTP-instelling**. Accepteer de standaardwaarden voor de overige instellingen in het venster **Een HTTP-instelling toevoegen** en selecteer vervolgens **Toevoegen** om terug te keren naar het venster **Een regel voor doorsturen toevoegen**. 

6. Selecteer in het venster **Een regel voor doorsturen toevoegen** de optie **Toevoegen** om de routeringsregel op te slaan en terug te keren naar het tabblad **Configuratie**.
7. Selecteer **een regel voor door sturen toevoegen** en voeg een soort gelijke regel, listener, back-end en http-instelling voor fabrikam toe.

     :::image type="content" source="./media/create-multiple-sites-portal/fabrikam-rule.png" alt-text="Fabrikam-regel":::

7. Selecteer **Volgende: Tags** en vervolgens **Volgende: Beoordelen en maken**.

### <a name="review--create-tab"></a>Tabblad Beoordelen en maken

Controleer de instellingen op het tabblad **Beoordelen en maken** en selecteer vervolgens **Maken** om het virtuele netwerk, het openbare IP-adres en de toepassingsgateway te maken. Het kan enkele minuten duren om de toepassingsgateway te maken in Azure.

Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.

## <a name="add-backend-targets"></a>Back-enddoelen toevoegen

In dit voorbeeld gebruikt u virtuele machines als het doelback-end. U kunt bestaande virtuele machines gebruiken of nieuwe maken. U maakt twee virtuele machines die in Azure worden gebruikt als back-endservers voor de toepassingsgateway.

Als u back-enddoelen wilt toevoegen, doet u het volgende:

1. Maak twee nieuwe virtuele machines, *contosoVM* en *fabrikamVM* die worden gebruikt als back-endservers.
2. Installeer IIS op de virtuele machines om te controleren of de toepassingsgateway is gemaakt.
3. Voeg de back-endservers toe aan de back-endpools.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer **Een resource maken** in de Azure-portal. Het venster **Nieuw** wordt weergegeven.
2. Selecteer in de lijst **Populair** de optie **Windows Server 2016-gegevenscentrum**. De pagina **Een virtuele machine maken** wordt weergegeven.<br>Toepassingsgateway kan verkeer routeren naar ieder type virtuele machine dat wordt gebruikt in de back-endpool. In dit voorbeeld gebruikt u een Windows Server 2016-gegevenscentrum.
3. Voer deze waarden in op het tabblad **Basisinformatie** voor de volgende instellingen voor de virtuele machine:

    - **Abonnement**: Selecteer uw abonnement.
    - **Resourcegroep**: Selecteer **myResourceGroupAG** als naam van de resourcegroep.
    - **Naam van virtuele machine**: Voer *contosoVM* in als de naam voor de virtuele machine.
    - **Regio**: Selecteer de regio die u eerder hebt gebruikt.
    - **Gebruikersnaam**: Voer een naam in voor de gebruikersnaam van de beheerder.
    - **Wachtwoord**: Voer een wachtwoord in voor de beheerder.
1. Accepteer de overige standaardwaarden en klik op **Volgende: Schijven**.  
2. Accepteer de standaardwaarden op het tabblad **Schijven** en selecteer **Volgende: Netwerken**.
3. Zorg ervoor dat, op het tabblad **Netwerken**, **myVNet** is geselecteerd bij **Virtueel netwerk** en dat **Subnet** is ingesteld op **myBackendSubnet**. Accepteer de overige standaardwaarden en klik op **Volgende: Beheer**.<br>Toepassingsgateway kan communiceren met instanties die zich buiten het virtuele netwerk van de gateway bevinden, maar u moet ervoor zorgen dat er een IP-verbinding is.
4. Op het tabblad **Beheer** stelt u **Diagnostische gegevens over opstarten** in op **Uitschakelen**. Accepteer de overige standaardwaarden en selecteer **Beoordelen en maken**.
5. Controleer de instellingen op het tabblad **Beoordelen en maken**, corrigeer eventuele validatiefouten en selecteer vervolgens **Maken**.
6. Wacht tot de virtuele machine is gemaakt voordat u verder gaat.

### <a name="install-iis-for-testing"></a>IIS installeren voor testen

In dit voorbeeld installeert u IIS alleen op de virtuele machines om te controleren of de toepassingsgateway in Azure is gemaakt.

1. Open [Azure PowerShell](../cloud-shell/quickstart-powershell.md). Hiertoe selecteert u **Cloud Shell** in de bovenste navigatiebalk van de Azure-portal en vervolgens **PowerShell** in de vervolgkeuzelijst. 

    ![Aangepaste extensie installeren](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Voer de volgende opdracht uit om IIS te installeren op de virtuele machine, waarbij uw resourcegroepregio word ingewisseld voor <location\>: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location <location>
    ```

3. Maak een tweede virtuele machine en installeer IIS aan de hand van de stappen die u zojuist hebt uitgevoerd. Gebruik *fabrikamVM* als naam voor de virtuele machine en voor de instelling **VMName** van de cmdlet **Set-AzVMExtension**.

### <a name="add-backend-servers-to-backend-pools"></a>Back-endservers toevoegen aan de back-endpools

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.

2. Selecteer **Back-endpools** in het linkermenu.

3. Selecteer **contosoPool**.

4. Onder **doel type**, selecteer **virtuele machine** in de vervolg keuzelijst.

5. Onder **doel** selecteert u de netwerk interface van de virtuele machine **contosoVM** in de vervolg keuzelijst.

    ![Back-endservers toevoegen](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. Selecteer **Opslaan**.
7. Herhaal dit om de *fabrikamVM* en interface toe te voegen aan de *fabrikamPool*.

Wacht tot de implementatie is voltooid voordat u doorgaat met de volgende stap.

## <a name="edit-your-hosts-file"></a>Het hosts-bestand bewerken

Nadat de toepassings gateway is gemaakt met het open bare IP-adres, kunt u het IP-adres ophalen en gebruiken om uw hosts-bestand te bewerken om het probleem op te lossen `www.contoso.com` en `www.fabrikam.com` 

1. Klik op **Alle resources** en vervolgens op **myAGPublicIPAddress**.

    ![Het DNS-adres van de toepassingsgateway registreren](./media/create-multiple-sites-portal/public-ip.png)

2. Kopieer het IP-adres en gebruik dit als de waarde voor nieuwe vermeldingen in uw `hosts` bestand.
1. Open op uw lokale computer een opdracht prompt voor beheer en ga naar `c:\Windows\System32\drivers\etc` .
1. Open het `hosts` bestand en voeg de volgende vermeldingen toe, waarbij `x.x.x.x` het open bare IP-adres van de toepassings gateway is:
   ```dos
   # Copyright (c) 1993-2009 Microsoft Corp.
   #
   # This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
   #
   # This file contains the mappings of IP addresses to host names. Each
   # entry should be kept on an individual line. The IP address should
   # be placed in the first column followed by the corresponding host name.
   # The IP address and the host name should be separated by at least one
   # space.
   #
   # Additionally, comments (such as these) may be inserted on individual
   # lines or following the machine name denoted by a '#' symbol.
   #
   # For example:
   #
   #      102.54.94.97     rhino.acme.com          # source server
   #       38.25.63.10     x.acme.com              # x client host
   
   # localhost name resolution is handled within DNS itself.
   #    127.0.0.1       localhost
   #    ::1             localhost
   x.x.x.x www.contoso.com
   x.x.x.x www.fabrikam.com

   ```
1. Sla het bestand op.
1. Voer de volgende opdrachten uit om de wijzigingen in uw hosts-bestand te laden en weer te geven:
   ```dos
    ipconfig/registerdns
    ipconfig/displaydns
   ```
   
## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

1. Typ een domein naam in de adres balk van uw browser. Bijvoorbeeld `http://www.contoso.com`.

    ![Contoso-site testen in toepassingsgateway](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Wijzig het adres voor het andere domein en u ziet iets als in het volgende voor beeld:

    ![Fabrikam-site testen in toepassingsgateway](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de bij de toepassingsgateway gemaakte resources niet meer nodig hebt, verwijdert u de resourcegroep. Wanneer u de resourcegroep verwijdert, worden ook de toepassingsgateway en alle gerelateerde resources verwijderd.

Ga als volgt te werk om de resourcegroep te verwijderen:

1. Selecteer **Resourcegroepen** in het linkermenu van de Azure-portal.
2. Zoek en selecteer **myResourceGroupAG** in de lijst op de pagina **Resourcegroepen**.
3. Selecteer **Resourcegroep verwijderen** op de **pagina van de resourcegroep**.
4. Voer *myResourceGroupAG* in bij **TYP DE RESOURCEGROEPNAAM** en selecteer vervolgens **Verwijderen**.

Het hosts-bestand herstellen:
1. Verwijder de `www.contoso.com` `www.fabrikam.com` regels en uit het bestand hosts en voer `ipconfig/registerdns` `ipconfig/flushdns` uit vanaf de opdracht prompt.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over wat u kunt doen met Azure Application Gateway](./overview.md)