---
title: 'Zelfstudie: Routeringsregels op basis van URL-pad maken met de portal: Azure Application Gateway'
description: In deze zelfstudie leert u hoe u routeringsregels op basis van een URL-pad maakt voor een toepassingsgateway en een virtuele-machineschaalset met behulp van de Azure-portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: victorh
ms.openlocfilehash: b0ab3cbd2891ef1677c0d4ba7a00821d67714b6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708948"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Zelfstudie: Een toepassingsgateway maken met routeringsregels op basis van URL-pad met de Azure-portal

U kunt de Azure-portal gebruiken om [op een URL-pad gebaseerde routeringsregels](./url-route-overview.md) te configureren als u een [toepassingsgateway](./overview.md) maakt. In deze zelfstudie maakt u back-endpools met behulp van virtuele machines. Vervolgens maakt u routeringsregels die ervoor zorgen dat webverkeer wordt omgeleid naar de servers in de pools.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een toepassingsgateway maken
> * Virtuele machines maken voor back-endservers
> * Back-endpools maken met de back-endservers
> * Een listener maken in de back-end
> * Een routeringsregel maken op basis van een pad

![Voorbeeld van URL-routering](./media/application-gateway-create-url-route-portal/scenario.png)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-virtual-machines"></a>Virtuele machines maken

In dit voorbeeld maakt u drie virtuele machines die worden gebruikt als back-endservers voor de toepassingsgateway. U installeert ook IIS op de virtuele machines om te controleren of de toepassingsgateway werkt zoals verwacht.

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
1. Selecteer **Een resource maken** in de Azure-portal.
2. Selecteer in de lijst Populair de optie **Windows Server 2016-gegevenscentrum**.
3. Voer deze waarden in voor de virtuele machine:

    - **Abonnement** : Selecteer uw abonnement.
    - **Resourcegroep**: selecteer **Nieuw** en typ *myResourceGroupAG* als de naam.
    - **Naam van de virtuele machine**: *myVM1*
    - **Regio**: *(US) VS - oost*
    - **Username**: Typ een gebruikers naam
    - **Wacht woord**: Typ een wacht woord


4. Selecteer **Volgende: schijven**.
5. Selecteer **Volgende: Netwerken**
6. Selecteer **Nieuwe maken** bij **Virtueel netwerk** en voer deze waarden in voor het virtuele netwerk:

   - *myVnet* als de naam van het virtuele netwerk.
   - *10.0.0.0/16* als de adresruimte van het virtuele netwerk.
   - *myBackendSubnet* als de naam van het eerste subnet.
   - *10.0.1.0/24* als de adresruimte van het subnet.
   - *myAGSubnet* als de naam van het tweede subnet.
   - *10.0.0.0/24* als de adresruimte van het subnet.
7. Selecteer **OK**.

8. Zorg ervoor dat onder subnet **myBackendSubnet** is geselecteerd voor het subnet en selecteer vervolgens **volgende: beheer**.
9. Selecteer **uitschakelen** om diagnostische gegevens over opstarten uit te scha kelen.
10. Selecteer **controleren + maken**, Controleer de instellingen op de pagina samen vatting en selecteer vervolgens **maken**.
11. Maak nog twee virtuele machines, *myVM2* en *myVM3*, en plaats deze in het virtuele netwerk *MyVNet* en het subnet *myBackendSubnet*.

### <a name="install-iis"></a>IIS installeren

1. Open de interactieve shell en controleer of deze is ingesteld op **PowerShell**.

    ![Aangepaste extensie installeren](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Voer de volgende opdracht uit om IIS op de virtuele machine te installeren: 

    ```azurepowershell
         $publicSettings = @{ "fileUris&quot; = (,&quot;https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

        Set-AzVMExtension `
         -ResourceGroupName myResourceGroupAG `
         -Location eastus `
         -ExtensionName IIS `
         -VMName myVM1 `
         -Publisher Microsoft.Compute `
         -ExtensionType CustomScriptExtension `
         -TypeHandlerVersion 1.4 `
         -Settings $publicSettings
    ```

3. Installeer IIS op de andere virtuele machines met behulp van de stappen die u zojuist hebt voltooid. Gebruik *myVM2* en *myVM3* voor VMName waarden in set-AzVMExtension.

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

1. Selecteer **Een resource maken** in het linkermenu van de Azure-portal. Het venster **Nieuw** wordt weergegeven.

2. Selecteer **Netwerken** en vervolgens **Application Gateway** in de lijst **Aanbevolen**.

### <a name="basics-tab"></a>Tabblad Basisbeginselen

1. Op het tabblad **Basisinformatie** voert u deze waarden in voor de volgende toepassingsgateway-instellingen:

   - **Abonnement**: Selecteer uw abonnement.
   - **Resourcegroep**: Selecteer **myResourceGroupAG** als de resourcegroep.
   - **Naam van de toepassings gateway**: Typ *myAppGateway* voor de naam van de toepassings gateway.
   - **Regio**: selecteer **(US) VS - oost**.

        ![Nieuwe toepassingsgateway maken: Basisbeginselen](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Selecteer **myVNet** onder **Virtueel netwerk configureren** als de naam van het virtuele netwerk.
3. Selecteer **myAGSubnet** als de naam van het subnet.
3. Accepteer de standaardwaarden voor de overige instellingen en selecteer **Volgende: Front-ends**.

### <a name="frontends-tab"></a>Tabblad Front-ends

1. Controleer in het tabblad **Frontends** of **Front-end-IP-adres** is ingesteld op **Openbaar**.

   > [!NOTE]
   > Voor de Application Gateway v2 SKU kunt u alleen een **openbare** front-end-IP-configuratie kiezen. De privé frontend-IP-configuratie is op dit moment niet ingeschakeld voor deze v2-SKU.

2. Selecteer **Nieuw toevoegen** voor het **open bare IP-adres** en voer *myAGPublicIPAddress* in voor de naam van het open bare IP-adres en selecteer vervolgens **OK**. 
3. Selecteer **Volgende: Back-ends**.

### <a name="backends-tab"></a>Tabblad Back-ends

De back-endpool word gebruikt om aanvragen te routeren naar de back-endservers die de aanvraag verwerken. Back-endpools kunnen bestaan uit NIC's, virtuele-machineschaalsets, openbare IP's, interne IP's, FQDN's (Fully Qualified Domain Name) en multitenant back-ends als Azure App Service.

1. Selecteer op het tabblad **back** -end **een back-end-groep toevoegen**.

2. Voer in het venster **Een back-endpool toevoegen** dat wordt geopend, de volgende waarden in om een lege back-endpool te maken:

    - **Naam**: Voer *myBackendPool* in als naam van de back-endpool.
3. Onder **doel type**, selecteer **virtuele machine** in de vervolg keuzelijst.

5. Selecteer onder **Doel** de netwerkinterface voor **myVM1**.
6. Selecteer **Toevoegen**.
7. Herhaal dit om een back-endpool *Images* toe te voegen met *myVM2* als het doel en een back-endpool *Video* met *myVM3* als het doel.
8. Selecteer **Toevoegen** om de configuratie van de back-endpool op te slaan en terug te keren naar het tabblad **Back-ends**.

4. Selecteer in het tabblad **Back-ends** de optie **Volgende: Configuratie**.

### <a name="configuration-tab"></a>Tabblad Configuratie

In het tabblad **Configuratie** verbindt u de front-end- en de back-endpool die u hebt gemaakt met een regel voor doorsturen.

1. Selecteer **een regel voor door sturen toevoegen** in de kolom **routerings regels** .

2. Voer in het venster **Een regel voor doorsturen toevoegen** dat wordt geopend, *myRoutingRule* in als de **Regelnaam**.

3. Voor een regel voor doorsturen is een listener vereist. Typ op het tabblad **listener** in het venster **een regel voor de route ring toevoegen** de volgende waarden voor de listener:

    - **Naam van listener**: Voer *myListener* in als naam van de listener.
    - **IP van front-end**: Selecteer **Openbaar** om het openbare IP te kiezen dat u voor de front-end hebt gemaakt.
    - **Poort**: Typ *8080*
  
        Accepteer de standaardwaarden voor de overige instellingen in het tabblad **Listener** en selecteer vervolgens het tabblad **Back-enddoelen** om de rest van de regel voor doorsturen te configureren.

4. Selecteer in het tabblad **Back-enddoelen** de optie **myBackendPool** als het **Back-enddoel**.

5. Voor de **http-instelling** selecteert u **Nieuw toevoegen** om een nieuwe http-instelling te maken. De HTTP-instelling bepaalt het gedrag van de regel voor doorsturen. 

6. Voer in het venster **Een HTTP-instelling toevoegen** dat wordt geopend, *myHTTPSetting* in als de **naam van de HTTP-instelling**. Accepteer de standaardwaarden voor de overige instellingen in het venster **Een HTTP-instelling toevoegen** en selecteer vervolgens **Toevoegen** om terug te keren naar het venster **Een regel voor doorsturen toevoegen**.
7. Selecteer **Meerdere doelen toevoegen om een regel op basis van een pad te maken** onder **Padgebaseerde routering**.
8. Geef */images/* \* op voor **Pad**.
9. Typ *installatie kopieën* bij **doel naam**.
10. Selecteer **myHTTPSetting** bij **HTTP-instelling**
11. Selecteer **Images** voor **Doel van back-end**.
12. Selecteer **Toevoegen** om de padregel op te slaan en terug te gaan naar het tabblad **Een routeringsregel toevoegen**.
13. Herhaal dit om een andere regel toe te voegen voor Video.
14. Selecteer **Toevoegen** om de routeringsregel toe te voegen en terug te gaan naar het tabblad **Configuratie**.
15. Selecteer **Volgende: Tags** en vervolgens **Volgende: Beoordelen en maken**.

> [!NOTE]
> U hoeft geen aangepaste padregel */* * toe te v
 voor het afhandelen van standaardgevallen. Dit wordt automatisch afgehandeld door de standaard-back-endpool.

### <a name="review--create-tab"></a>Tabblad Beoordelen en maken

Controleer de instellingen op het tabblad **Beoordelen en maken** en selecteer vervolgens **Maken** om het virtuele netwerk, het openbare IP-adres en de toepassingsgateway te maken. Het kan enkele minuten duren om de toepassingsgateway te maken in Azure. Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.


## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.

    ![Registreer het openbare IP-adres van de toepassingsgateway](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. Bijvoorbeeld http:\//52.188.72.175:8080.

    ![Basis-URL testen in de toepassingsgateway](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

   De listener op poort 8080 routeert deze aanvraag naar de standaard-back-endpool.

3. Wijzig de URL in *http://&lt;ip-adres&gt;:8080/images/test.htm*, waarbij u &lt;ip-adres&gt; vervangt door uw eigen IP-adres. U krijgt nu iets te zien zoals in het volgende voorbeeld:

    ![Afbeeldingen-URL in toepassingsgateway testen](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

   De listener op poort 8080 routeert deze aanvraag naar de back-endpool *Images*.

4. Wijzig de URL in *http://&lt;ip-adres&gt;:8080/images/video.htm*, waarbij u &lt;ip-adres&gt; vervangt door uw eigen IP-adres. U krijgt nu iets te zien zoals in het volgende voorbeeld:

    ![Video-URL testen in de toepassingsgateway](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

   De listener op poort 8080 routeert deze aanvraag naar de back-endpool *Video*.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep en alle gerelateerde resources als u deze niet meer nodig hebt. Selecteer hiervoor de resourcegroep en klik op **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [End-to-end TLS inschakelen voor Azure Application Gateway](./ssl-overview.md)