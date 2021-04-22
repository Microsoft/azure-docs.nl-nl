---
title: HTTP-naar-HTTPS-omleiding in portal - Azure Application Gateway
description: Meer informatie over het maken van een toepassingsgateway met omgeleid verkeer van HTTP naar HTTPS met behulp van de Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: e7fb40d95c4659bf353366770da7c903ffa1bd09
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867219"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Maak een toepassingsgateway met HTTP-naar-HTTPS-omleiding met behulp van de Azure Portal

U kunt de Azure Portal om een toepassingsgateway [te](overview.md) maken met een certificaat voor TLS-beëindiging. Er wordt een regel voor doorsturen gebruikt om HTTP-verkeer om te leiden naar de HTTPS-poort in uw toepassingsgateway. In dit voorbeeld maakt u ook een [virtuele-machineschaalset](../virtual-machine-scale-sets/overview.md) voor de back-endpool van de toepassingsgateway die twee virtuele-machine-exemplaren bevat.

In dit artikel leert u het volgende:

* Een zelfondertekend certificaat maken
* Een netwerk instellen
* Een toepassingsgateway maken met behulp van het certificaat
* Een listener en omleidingsregel toevoegen
* Een virtuele-machineschaalset maken met de standaard back-endgroep

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voor deze zelfstudie is Azure PowerShell moduleversie 1.0.0 of hoger vereist om een certificaat te maken en IIS te installeren. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u de opdrachten in deze zelfstudie wilt uitvoeren, moet u ook uitvoeren om `Login-AzAccount` een verbinding met Azure te maken.

## <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

Voor productiegebruik moet u een geldig certificaat importeren dat is ondertekend door een vertrouwde provider. Voor deze zelfstudie maakt u een zelfondertekend certificaat met behulp van [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate). U kunt [Export-PfxCertificate ](/powershell/module/pki/export-pfxcertificate) gebruiken met de Thumbprint die is geretourneerd om een ​​PFX-bestand uit het certificaat te exporteren.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

U zou iets moeten zien dat lijkt op dit resultaat:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Gebruik de vingerafdruk om het PFX-bestand te maken:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

U hebt een virtueel netwerk nodig voor communicatie tussen de resources die u maakt. In dit voorbeeld worden twee subnetten gemaakt: één voor de toepassingsgateway en de andere voor de back-endservers. U kunt een virtueel netwerk maken op hetzelfde moment dat u de toepassingsgateway maakt.

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Klik in de linkerbovenhoek van Azure Portal op **Een resource maken**.
3. Selecteer **Netwerken** en vervolgens **Application Gateway** in de lijst Aanbevolen.
4. Voer deze waarden in voor de toepassingsgateway:

   - *myAppGateway* als de naam van de toepassingsgateway.
   - *myResourceGroupAG* als de nieuwe resourcegroep.

     ![Nieuwe toepassingsgateway maken](./media/create-url-route-portal/application-gateway-create.png)

5. Accepteer de standaardwaarden voor de overige instellingen en klik op **OK**.
6. Klik op **Een virtueel netwerk kiezen**, klik op **Nieuw maken** en voer deze waarden in voor het virtuele netwerk:

   - *myVnet* als de naam van het virtuele netwerk.
   - *10.0.0.0/16* als de adresruimte van het virtuele netwerk.
   - *myAGSubnet* als de naam van het subnet.
   - *10.0.0.0/24* als de adresruimte van het subnet.

     ![Virtueel netwerk maken](./media/create-url-route-portal/application-gateway-vnet.png)

7. Klik op **OK** om het virtuele netwerk en subnet te maken.
8. Controleer **onder Front-end-IP-configuratie** of **IP-adrestype** **Openbaar** is en **Nieuwe** maken is geselecteerd. Voer *myAGPublicIPAddress in* als naam. Accepteer de standaardwaarden voor de overige instellingen en klik op **OK**.
9. Selecteer **https onder Listenerconfiguratie,** selecteer vervolgens Een bestand **selecteren,** navigeer naar het bestand *c:\appgwcert.pfx* en **selecteer Openen.** 
10. Typ *appgwcert* als de certificaatnaam en *Azure123456!* als het wachtwoord.
11. Laat Web Application Firewall uitgeschakeld en selecteer **OK.**
12. Controleer de instellingen op de overzichtspagina en selecteer ok **om** de netwerkresources en de toepassingsgateway te maken. Het kan enkele minuten duren voordat de toepassingsgateway is gemaakt. Wacht tot de implementatie is voltooid voordat u verder gaat met de volgende sectie.

### <a name="add-a-subnet"></a>Een subnet toevoegen

1. Selecteer **Alle resources** in het linkermenu en selecteer vervolgens **myVNet** in de lijst met resources.
2. Selecteer **Subnetten** en klik vervolgens op **Subnet.**

    ![Subnet maken](./media/create-url-route-portal/application-gateway-subnet.png)

3. Typ *myBackendSubnet* als naam van het subnet.
4. Typ *10.0.2.0/24* voor het adresbereik en selecteer **ok.**

## <a name="add-a-listener-and-redirection-rule&quot;></a>Een listener en omleidingsregel toevoegen

### <a name=&quot;add-the-listener&quot;></a>De listener toevoegen

Voeg eerst de listener met de *naam myListener toe* voor poort 80.

1. Open de **resourcegroep myResourceGroupAG** en selecteer **myAppGateway.**
2. Selecteer **Listeners** en selecteer **vervolgens + Basic.**
3. Typ *MyListener* als naam.
4. Typ *httpPort* voor de naam van de nieuwe front-endpoort en *80* voor de poort.
5. Zorg ervoor dat het protocol is ingesteld **op HTTP** en selecteer **ok.**

### <a name=&quot;add-a-routing-rule-with-a-redirection-configuration&quot;></a>Een routeringsregel met een omleidingsconfiguratie toevoegen

1. Selecteer **op myAppGateway** regels **en** selecteer vervolgens **+Regel voor doorsturen aanvragen.**
2. Bij **Regelnaam typt** u *Regel2.*
3. Zorg **ervoor dat MyListener** is geselecteerd voor de listener.
4. Klik op **het tabblad Back-enddoelen** en selecteer **Doeltype** als *Omleiding.*
5. Bij **Omleidingstype** selecteert u **Permanent.**
6. Selecteer **listener bij Omleidingsdoel.** 
7. Zorg ervoor **dat de doellistener** is ingesteld op **appGatewayHttpListener.**
8. Selecteer ja **voor de opties Queryreeks** opnemen **en Pad opnemen.** 
9. Selecteer **Toevoegen**.

## <a name=&quot;create-a-virtual-machine-scale-set&quot;></a>Een virtuele-machineschaalset maken

In dit voorbeeld maakt u een virtuele-machineschaalset om servers op te geven voor de back-endpool in de toepassingsgateway.

1. Selecteer in de linkerbovenhoek van de portal **+Een resource maken.**
2. Selecteer **Compute**.
3. Typ schaalset in het *zoekvak en* druk op Enter.
4. Selecteer **Virtuele-machineschaalset** en selecteer vervolgens **Maken.**
5. Bij **Naam van virtuele-machineschaalset** typt u *myvmss.*
6. Zorg ervoor dat windows **Server 2016 Datacenter** is geselecteerd bij Installatieinstallatie installatie van de besturingssysteemschijf**.
7. Bij **Resourcegroep** selecteert **u myResourceGroupAG.**
8. Bij **Gebruikersnaam typt** u *azureuser*.
9. Bij **Wachtwoord** typt u *Azure123456!* en bevestig het wachtwoord.
10. Zorg **er bij Aantal** instanties voor dat de waarde **2** is.
11. Selecteer **bij Instantiegrootte** de **D2s_v3.**
12. Controleer **onder Netwerken** of Opties voor **taakverdeling kiezen** is ingesteld op **Application Gateway**.
13. Zorg **ervoor dat Application Gateway** is ingesteld op **myAppGateway.**
14. Zorg **ervoor dat Subnet** is ingesteld **op myBackendSubnet.**
15. Selecteer **Maken**.

### <a name=&quot;associate-the-scale-set-with-the-proper-backend-pool&quot;></a>De schaalset koppelen aan de juiste back-endpool

De gebruikersinterface van de portal voor virtuele-machineschaalsets maakt een nieuwe back-endpool voor de schaalset, maar u wilt deze koppelen aan uw bestaande appGatewayBackendPool.

1. Open de **resourcegroep myResourceGroupAg.**
2. Selecteer **myAppGateway.**
3. Selecteer **Back-endpools.**
4. Selecteer **myAppGatewaymyvmss.**
5. Selecteer **Alle doelen uit de back-endpool verwijderen.**
6. Selecteer **Opslaan**.
7. Nadat dit proces is voltooid, selecteert u de back-endpool **myAppGatewaymyvmss,** selecteert u **Verwijderen** en vervolgens **OK** om te bevestigen.
8. Selecteer **appGatewayBackendPool**.
9. Selecteer **onder Doelen** de optie **VMSS.**
10. Selecteer **onder VMSS** **de optie myvmss.**
11. Selecteer **onder Netwerkinterfaceconfiguraties** **de optie myvmssNic.**
12. Selecteer **Opslaan**.

### <a name=&quot;upgrade-the-scale-set&quot;></a>De schaalset upgraden

Ten slotte moet u de schaalset bijwerken met deze wijzigingen.

1. Selecteer de **schaalset myvmss.**
2. Selecteer onder **Instellingen** de optie **Instanties**.
3. Selecteer beide exemplaren en selecteer vervolgens **Upgrade uitvoeren.**
4. Selecteer **Ja** om te bevestigen.
5. Nadat dit is voltooid, gaat u terug naar **myAppGateway** en selecteert u **Back-mailgroepen.** U ziet nu dat **de appGatewayBackendPool** twee doelen heeft en  **myAppGatewaymyvmss** nul doelen heeft.
6. Selecteer **myAppGatewaymyvmss** en selecteer **vervolgens Verwijderen.**
7. Selecteer **OK** om te bevestigen.

### <a name=&quot;install-iis&quot;></a>IIS installeren

Een eenvoudige manier om IIS op de schaalset te installeren, is met PowerShell. Klik in de portal op het Cloud Shell en zorg ervoor dat **PowerShell** is geselecteerd.

Plak de volgende code in het PowerShell-venster en druk op Enter.

```azurepowershell
$publicSettings = @{ &quot;fileUris&quot; = (,&quot;https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>De schaalset upgraden

Nadat u de exemplaren met IIS hebt gewijzigd, moet u de schaalset opnieuw bijwerken met deze wijziging.

1. Selecteer de **schaalset myvmss.**
2. Selecteer onder **Instellingen** de optie **Instanties**.
3. Selecteer beide exemplaren en selecteer vervolgens **Upgrade uitvoeren.**
4. Selecteer **Ja** om te bevestigen.

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

U kunt het openbare IP-adres van de toepassing op de pagina Overzicht van de toepassingsgateway vinden.

1. Selecteer **myAppGateway**.
2. Noteer **op de** pagina Overzicht het IP-adres onder Openbaar IP-adres van **front-end.**

3. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. Bijvoorbeeld: http://52.170.203.149

   ![Beveiligingswaarschuwing](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Als u de beveiligingswaarschuwing wilt accepteren als u een zelf-ondertekend certificaat hebt gebruikt, selecteert u **Details** en vervolgens **Ga verder naar de webpagina.** Uw beveiligde IIS-website wordt vervolgens weergegeven zoals in het volgende voorbeeld:

   ![Basis-URL testen in de toepassingsgateway](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een toepassingsgateway met interne omleiding](redirect-internal-site-powershell.md).
