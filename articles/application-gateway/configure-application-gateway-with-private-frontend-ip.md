---
title: Een intern load balancer-eind punt (ILB) configureren
titleSuffix: Azure Application Gateway
description: Dit artikel bevat informatie over het configureren van Application Gateway met een privé-frontend-IP-adres
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 02/23/2021
ms.author: victorh
ms.openlocfilehash: 224cbe1e34e5915a7fa5fc1cf415c35f86c3abe4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711651"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Een toepassings gateway met een ILB-eind punt (interne load balancer) configureren

Azure-toepassing gateway kan worden geconfigureerd met een Internet gerichte VIP of met een intern eind punt dat niet beschikbaar is op internet. Een intern eind punt maakt gebruik van een privé-IP-adres voor de frontend. dit wordt ook wel een *intern Load Balancer-eind punt (ILB)* genoemd.

Het configureren van de gateway met behulp van een privé-frontend-IP-adres is handig voor interne line-of-business-toepassingen die niet worden blootgesteld aan Internet. Het is ook nuttig voor services en lagen in een toepassing met meerdere lagen die zich in een beveiligings grens bevinden die niet beschikbaar is op internet, maar:

- Er is nog steeds een taak verdeling met Round Robin vereist
- sessie-persistentie
- of Transport Layer Security (TLS) beëindiging (voorheen bekend als Secure Sockets Layer (SSL)).

Dit artikel begeleidt u bij de stappen voor het configureren van een toepassings gateway met een privé-IP-adres met een frontend met behulp van de Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt. Maak een nieuw virtueel netwerk of gebruik een bestaande. 

In dit voorbeeld maakt u een nieuw virtueel netwerk. U kunt een virtueel netwerk maken op hetzelfde moment dat u de toepassingsgateway maakt. Instanties van toepassingsgateways worden in afzonderlijke subnetten gemaakt. Dit voor beeld bevat twee subnetten: één voor de toepassings gateway en een voor de back-endservers.

1. Vouw het menu Portal uit en selecteer **een resource maken**.
2. Selecteer **Netwerken** en vervolgens **Application Gateway** in de lijst Aanbevolen.
3. Voer *myAppGateway* in als de naam van de toepassings gateway en *myResourceGroupAG* voor de nieuwe resource groep.
4. Selecteer voor **regio** **Central US (Engelstalig**).
5. Selecteer bij **laag** de optie **standaard**.
6. Onder **virtuele netwerk configureren** selecteert u **nieuwe maken** en voert u vervolgens deze waarden in voor het virtuele netwerk:
   - *myVnet* als de naam van het virtuele netwerk.
   - *10.0.0.0/16* als de adresruimte van het virtuele netwerk.
   - *myAGSubnet* als de naam van het subnet.
   - *10.0.0.0/24* als de adresruimte van het subnet.
   - *myBackendSubnet* : de naam van het back-end-subnet.
   - *10.0.1.0/24* : voor de adres ruimte van het back-end-subnet.

    ![Virtueel netwerk maken](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. Selecteer **OK** om het virtuele netwerk en subnetten te maken.
7. Selecteer **volgende:** front-end.
8. Selecteer **privé** bij **IP-adres type voor frontend**.

   Het is standaard een dynamische IP-adres toewijzing. Het eerste beschik bare adres van het geconfigureerde subnet wordt toegewezen als het frontend-IP-adres.
   > [!NOTE]
   > Zodra het IP-adres type is toegewezen, kan het niet later worden gewijzigd.
9. Selecteer **volgende: back-end**.
10. Selecteer **een back-end-pool toevoegen**.
11. Typ *appGatewayBackendPool* voor **naam**.
12. Selecteer **Ja** als u de **back-end-pool wilt toevoegen zonder doelen**. U voegt de doelen later toe.
13. Selecteer **Toevoegen**.
14. Selecteer **volgende: Configuratie**.
15. Selecteer **een regel** voor door sturen toevoegen onder **routerings regels**.
16. Typ *Rrule-01* bij **regel naam**.
17. Voor de naam van de **listener** typt u *listener-01*.
18. Selecteer **privé** voor **frontend-IP**.
19. Accepteer de resterende standaard waarden en selecteer het tabblad **backend-doelen** .
20. Selecteer in **doel type** **back-end-pool** en selecteer vervolgens **appGatewayBackendPool**.
21. Voor **http-instelling** selecteert u **nieuwe toevoegen**.
22. Typ *http-setting-01* voor de naam van de **http-instelling**.
23. Selecteer voor back-end **-** **protocol** http.
24. Typ *80* voor de **back-end-poort**.
25. Accepteer de resterende standaard waarden en selecteer **toevoegen**.
26. Selecteer op de pagina **een routerings regel toevoegen** de optie **toevoegen**.
27. Selecteer **Volgende: Tags**.
28. Selecteer **Volgende: Beoordelen en maken**.
29. Controleer de instellingen op de pagina samen vatting en selecteer vervolgens **maken** om de netwerk resources en de toepassings gateway te maken. Het kan enkele minuten duren om de toepassingsgateway te maken. Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.

## <a name="add-backend-pool"></a>Back-endgroep toevoegen

De back-endpool word gebruikt om aanvragen te routeren naar de back-endservers die de aanvraag verwerken. De back-end kan bestaan uit Nic's, virtuele-machine schaal sets, open bare IP-adressen, interne IP-adressen, FQDN-namen (Fully Qualified Domain names) en back-ends met meerdere tenants, zoals Azure App Service. In dit voor beeld gebruikt u virtuele machines als doel-back-end. U kunt bestaande virtuele machines gebruiken of nieuwe maken. In dit voorbeeld maakt u twee virtuele machines die in Azure worden gebruikt als back-endservers voor de toepassingsgateway.

Hiervoor gaat u als volgt te werk:

1. Maak twee nieuwe virtuele machines, *myVM* en *myVM2*, die worden gebruikt als back-endservers.
2. Installeer IIS op de virtuele machines om te controleren of de toepassingsgateway is gemaakt.
3. Voeg de back-endservers toe aan de back-endpool.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken


1. Selecteer **Een resource maken**.
2. Selecteer **Compute** en vervolgens **Virtuele machine**.
4. Voer deze waarden in voor de virtuele machine:
   - Selecteer uw abonnement.
   - Selecteer *myResourceGroupAG* voor **resource groep**.
   - Typ *myVM* voor de naam van de **virtuele machine**.
   - Selecteer **Windows Server 2019 Data Center** voor **installatie kopie**.
   - Geef een geldige **gebruikers naam** op.
   - Geef een geldig **wacht woord** op.
1. Accepteer de resterende standaard waarden en selecteer **volgende: schijven**.
1. Accepteer de standaard instellingen en selecteer **volgende: netwerken**.
1. Zorg ervoor dat **myVNet** is geselecteerd voor het virtuele netwerk en dat het subnet **myBackendSubnet** is.
1. Accepteer de resterende standaard waarden en selecteer **volgende: beheer**.
1. Selecteer **uitschakelen** om diagnostische gegevens over opstarten uit te scha kelen.
1. Selecteer **Controleren + maken**.
1. Controleer de instellingen op de overzichtspagina en selecteer **Maken**. Het maken van de virtuele machine kan enkele minuten duren. Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.

### <a name="install-iis"></a>IIS installeren

1. Open de Cloud Shell en zorg ervoor dat deze is ingesteld op **Power shell**.
    ![Scherm afbeelding toont een open Azure Cloud Shell console venster waarin Power shell wordt gebruikt.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Voer de volgende opdracht uit om IIS op de virtuele machine te installeren:

   ```azurepowershell
   Set-AzVMExtension `
        -ResourceGroupName myResourceGroupAG `
        -ExtensionName IIS `
        -VMName myVM `
        -Publisher Microsoft.Compute `
        -ExtensionType CustomScriptExtension `
        -TypeHandlerVersion 1.4 `
        -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
         -Location CentralUS 

   ```

3. Maak een tweede virtuele machine en installeer IIS met behulp van de stappen die u zojuist hebt voltooid. Gebruik myVM2 voor de naam van de virtuele machine en voor `VMName` in `Set-AzVMExtension` .

### <a name="add-backend-servers-to-backend-pool"></a>Back-endservers toevoegen aan de back-endpool

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.
2. Selecteer **back-endservers** en selecteer vervolgens **appGatewayBackendPool**.
3. Onder **doel type** **virtuele machine**  selecteren en onder **doel** selecteert u de vNIC die is gekoppeld aan myVM.
4. Herhaal deze stap om MyVM2 toe te voegen.
   ![Het deel venster met de back-end-pool bewerken met doel typen en doelen gemarkeerd.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. Selecteer **Save.**

## <a name="create-a-client-virtual-machine"></a>Een virtuele client machine maken

De virtuele client machine wordt gebruikt om verbinding te maken met de back-end-groep van de toepassings gateway.

- Maak een derde virtuele machine met behulp van de vorige stappen. Gebruik myVM3 voor de naam van de virtuele machine.

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

1. Selecteer op de pagina myAppGateway **frontend-IP-configuraties** voor een notitie van het privé-frontend-IP-adres.
    ![Deel venster IP-configuratie van frontend waarvoor het persoonlijke type is gemarkeerd.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Kopieer het privé-IP-adres en plak het in de adres balk van de browser op myVM3 om toegang te krijgen tot de back-end-groep van de toepassings gateway.

## <a name="next-steps"></a>Volgende stappen

Als u de status van uw back-end-pool wilt bewaken, raadpleegt u [back-end status-en Diagnostische logboeken voor Application Gateway](application-gateway-diagnostics.md).
