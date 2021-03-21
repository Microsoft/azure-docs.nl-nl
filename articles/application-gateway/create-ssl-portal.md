---
title: 'Zelfstudie: TLS-beëindiging configureren in de portal - Azure Application Gateway'
description: In deze zelfstudie leert u hoe u een toepassingsgateway maakt en een certificaat voor TLS-beëindiging toevoegt met behulp van Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: victorh
ms.openlocfilehash: c976ea236ae1d37cc0a543b10a9de55609035632
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98986749"
---
# <a name="tutorial-configure-an-application-gateway-with-tls-termination-using-the-azure-portal"></a>Zelfstudie: Een toepassingsgateway configureren met TLS-beëindiging met Azure Portal

U kunt Azure Portal gebruiken om een [​​toepassingsgateway](overview.md) te configureren met een certificaat voor TLS-beëindiging waarvoor virtuele machines voor back-endservers worden gebruikt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een zelfondertekend certificaat maken
> * Een toepassingsgateway maken met behulp van het certificaat
> * De virtuele machines maken die als back-endservers worden gebruikt
> * De toepassingsgateway testen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Meld u aan bij Azure Portal op [https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

In deze sectie gebruikt u [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) om een zelfondertekend certificaat te maken. U uploadt het certificaat naar Azure Portal wanneer u de listener voor de toepassingsgateway maakt.

Open als beheerder op uw lokale computer een Windows PowerShell-venster. Voer de volgende opdracht uit om het certificaat te maken:

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

U zou iets moeten zien dat lijkt op dit antwoord:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Gebruik [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate) met de vingerafdruk die is geretourneerd om een ​​PFX-bestand uit het certificaat te exporteren. Zorg ervoor dat uw wachtwoord tussen de 4 en 12 tekens lang is:


```powershell
$pwd = ConvertTo-SecureString -String <your password> -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

1. Selecteer **Een resource maken** in het linkermenu van de Azure-portal. Het venster **Nieuw** wordt weergegeven.

2. Selecteer **Netwerken** en vervolgens **Application Gateway** in de lijst **Aanbevolen**.

### <a name="basics-tab"></a>Tabblad Basisbeginselen

1. Op het tabblad **Basisinformatie** voert u deze waarden in voor de volgende toepassingsgateway-instellingen:

   - **Resourcegroep**: Selecteer **myResourceGroupAG** als de resourcegroep. Als deze nog niet bestaat, selecteert u **Nieuwe maken** om deze te maken.
   - **Naam toepassingsgateway**: Typ *myAppGateway* als naam voor de toepassingsgateway.

        ![Nieuwe toepassingsgateway maken: Basisbeginselen](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt. U kunt een nieuw virtueel netwerk maken of een bestaand gebruiken. In dit voorbeeld maakt u een virtueel netwerk op hetzelfde moment dat u de toepassingsgateway maakt. Instanties van toepassingsgateways worden in afzonderlijke subnetten gemaakt. In dit voorbeeld maakt u twee subnetten: één voor de toepassingsgateway en één voor de back-endservers.

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
   > Voor de Application Gateway v2 SKU kunt u alleen een **openbare** front-end-IP-configuratie kiezen. De privé frontend-IP-configuratie is op dit moment niet ingeschakeld voor deze v2-SKU.

2. Kies **Nieuw toevoegen** voor het **open bare IP-adres** en voer *myAGPublicIPAddress* in voor de naam van het open bare IP-adres en selecteer vervolgens **OK**. 

   ![Nieuwe toepassingsgateway maken: front-ends](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Selecteer **Volgende: Back-ends**.

### <a name="backends-tab"></a>Tabblad Back-ends

De back-endpool word gebruikt om aanvragen te routeren naar de back-endservers die de aanvraag verwerken. Back-endpools kunnen bestaan uit NIC's, virtuele-machineschaalsets, openbare IP's, interne IP's, FQDN's (Fully Qualified Domain Name) en multitenant back-ends als Azure App Service. In dit voorbeeld maakt u een lege back-endpool met uw toepassingsgateway en voegt u vervolgens back-enddoelen toe aan de back-endpool.

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
    - **Protocol**: Selecteer **HTTPS**.
    - **Poort**: Controleer of 443 is opgegeven voor de poort.

   Onder **https-instellingen**:

   - **Een certificaat kiezen** : Selecteer **een certificaat uploaden**.
   - **PFX-certificaatbestand**: blader naar en het bestand c:\appgwcert.pfx dat u eerder hebt gemaakt en selecteer het bestand.
   - **Certificaatnaam**: voer *mycert1* in als de naam van het certificaat.
   - **Wacht woord** : Typ het wacht woord dat u hebt gebruikt om het certificaat te maken.
  
        Accepteer de standaardwaarden voor de overige instellingen in het tabblad **Listener** en selecteer vervolgens het tabblad **Back-enddoelen** om de rest van de regel voor doorsturen te configureren.

   ![Nieuwe toepassingsgateway maken: listener](./media/create-ssl-portal/application-gateway-create-rule-listener.png)

4. Selecteer in het tabblad **Back-enddoelen** de optie **myBackendPool** als het **Back-enddoel**.

5. Voor de **http-instelling** selecteert u **Nieuw toevoegen** om een nieuwe http-instelling te maken. De HTTP-instelling bepaalt het gedrag van de regel voor doorsturen. Voer in het venster **een HTTP-instelling toevoegen** dat wordt geopend, *myHTTPSetting* in als de naam van de **http-instelling**. Accepteer de standaard waarden voor de overige instellingen in het venster **een HTTP-instelling toevoegen** en selecteer vervolgens **toevoegen** om terug te gaan naar het venster een regel voor het **routeren van een route ring toevoegen** . 

   :::image type="content" source="./media/create-ssl-portal/application-gateway-create-httpsetting.png" alt-text="Nieuwe toepassingsgateway maken: HTTP-instelling":::

6. Selecteer in het venster **Een regel voor doorsturen toevoegen** de optie **Toevoegen** om de routeringsregel op te slaan en terug te keren naar het tabblad **Configuratie**.

   ![Een nieuwe toepassingsgateway maken: regel voor doorsturen](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Selecteer **Volgende: Tags** en vervolgens **Volgende: Beoordelen en maken**.

### <a name="review--create-tab"></a>Tabblad Beoordelen en maken

Controleer de instellingen op het tabblad **Beoordelen en maken** en selecteer vervolgens **Maken** om het virtuele netwerk, het openbare IP-adres en de toepassingsgateway te maken. Het kan enkele minuten duren om de toepassingsgateway te maken in Azure. Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.

## <a name="add-backend-targets"></a>Back-enddoelen toevoegen

In dit voorbeeld gebruikt u virtuele machines als het doelback-end. U kunt bestaande virtuele machines gebruiken of nieuwe maken. U maakt twee virtuele machines die in Azure worden gebruikt als back-endservers voor de toepassingsgateway.

Hiervoor moet u het volgende doen:

1. Maak twee nieuwe virtuele machines, *myVM* en *myVM2* om te gebruiken als back-endservers.
2. Installeer IIS op de virtuele machines om te controleren of de toepassingsgateway is gemaakt.
3. Voeg de back-endservers toe aan de back-endpool.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer **Een resource maken** in de Azure-portal. Het venster **Nieuw** wordt weergegeven.
2. Selecteer in de lijst **Populair** de optie **Windows Server 2016-gegevenscentrum**. De pagina **Een virtuele machine maken** wordt weergegeven.

   Toepassingsgateway kan verkeer routeren naar ieder type virtuele machine dat wordt gebruikt in de back-endpool. In dit voorbeeld gebruikt u een Windows Server 2016-gegevenscentrum.

1. Voer deze waarden in op het tabblad **Basisinformatie** voor de volgende instellingen voor de virtuele machine:

    - **Resourcegroep**: Selecteer **myResourceGroupAG** als naam van de resourcegroep.
    - **Naam van virtuele machine**: Typ *myVM* als naam voor de virtuele machine.
    - **Gebruikersnaam**: Voer een naam in voor de gebruikersnaam van de beheerder.
    - **Wachtwoord**: voer een wachtwoord in voor het Administrator-account.
1. Accepteer de overige standaardwaarden en klik op **Volgende: Schijven**.  
2. Accepteer de standaardwaarden op het tabblad **Schijven** en selecteer **Volgende: Netwerken**.
3. Zorg ervoor dat, op het tabblad **Netwerken**, **myVNet** is geselecteerd bij **Virtueel netwerk** en dat **Subnet** is ingesteld op **myBackendSubnet**. Accepteer de overige standaardwaarden en klik op **Volgende: Beheer**.

   Toepassingsgateway kan communiceren met instanties die zich buiten het virtuele netwerk van de gateway bevinden, maar u moet ervoor zorgen dat er een IP-verbinding is.
1. Op het tabblad **Beheer** stelt u **Diagnostische gegevens over opstarten** in op **Uitschakelen**. Accepteer de overige standaardwaarden en selecteer **Beoordelen en maken**.
2. Controleer de instellingen op het tabblad **Beoordelen en maken**, corrigeer eventuele validatiefouten en selecteer vervolgens **Maken**.
3. Wacht totdat de implementatie is voltooid voordat u doorgaat.

### <a name="install-iis-for-testing"></a>IIS installeren voor testen

In dit voorbeeld installeert u IIS alleen op de virtuele machines om te controleren of de toepassingsgateway in Azure is gemaakt.

1. Open [Azure PowerShell](../cloud-shell/quickstart-powershell.md). Hiertoe selecteert u **Cloud Shell** in de bovenste navigatiebalk van de Azure-portal en vervolgens **PowerShell** in de vervolgkeuzelijst. 

    ![Aangepaste extensie installeren](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Wijzig de locatie-instelling voor uw omgeving en voer vervolgens de volgende opdracht uit om IIS op de virtuele machine te installeren: 

   ```azurepowershell-interactive
          Set-AzVMExtension `
            -ResourceGroupName myResourceGroupAG `
            -ExtensionName IIS `
            -VMName myVM `
            -Publisher Microsoft.Compute `
            -ExtensionType CustomScriptExtension `
            -TypeHandlerVersion 1.4 `
            -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
            -Location <location>
   ```

3. Maak een tweede virtuele machine en installeer IIS met behulp van de stappen die u zojuist hebt voltooid. Gebruik *myVM2* als naam voor de virtuele machine en voor de instelling **VMName** van de cmdlet **Set-AzVMExtension**.

### <a name="add-backend-servers-to-backend-pool"></a>Back-endservers toevoegen aan de back-endpool

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.

2. Selecteer **Back-endpools** in het linkermenu.

3. Selecteer **myBackendPool**.

4. Onder **doel type**, selecteer **virtuele machine** in de vervolg keuzelijst.

5. Onder **doel** selecteert u de netwerk interface onder **myVM** in de vervolg keuzelijst.

6. Herhaal dit om de netwerk interface voor **myVM2** toe te voegen.

    ![Back-endservers toevoegen](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Selecteer **Opslaan**.

7. Wacht tot de implementatie is voltooid voordat u doorgaat met de volgende stap.

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

1. Selecteer **Alle resources** en vervolgens **myAGPublicIPAddress**.

    ![Registreer het openbare IP-adres van de toepassingsgateway](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Typ *https//\<your application gateway ip address\>* in de adresbalk van de browser.

   Voor het accepteren van de beveiligingswaarschuwing als u een zelfondertekend certificaat hebt gebruikt, selecteert u **Details** (of **Geavanceerd** in Chrome) en gaat u vervolgens naar de webpagina:

    ![Beveiligingswaarschuwing](./media/create-ssl-portal/application-gateway-secure.png)

    Uw beveiligde IIS-website wordt vervolgens weergegeven zoals in het volgende voorbeeld:

    ![Basis-URL testen in de toepassingsgateway](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep en alle gerelateerde resources als u deze niet meer nodig hebt. Selecteer hiervoor de resourcegroep en klik op **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over TLS-ondersteuning voor Application Gateway](ssl-overview.md)