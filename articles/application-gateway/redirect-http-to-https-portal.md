---
title: HTTP-naar-HTTPS-omleiding in portal-Azure-toepassing gateway
description: Meer informatie over het maken van een toepassings gateway met omgeleid verkeer van HTTP naar HTTPS met behulp van de Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 67153fa750fee765dcaa1072eec87a2f6169b918
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397277"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Een toepassings gateway met HTTP-naar-HTTPS-omleiding maken met behulp van de Azure Portal

U kunt de Azure Portal gebruiken om een [toepassings gateway](overview.md) met een certificaat voor het beëindigen van TLS te maken. Er wordt een routerings regel gebruikt om HTTP-verkeer om te leiden naar de HTTPS-poort in uw toepassings gateway. In dit voor beeld maakt u ook een [schaalset voor virtuele machines](../virtual-machine-scale-sets/overview.md) voor de back-end-pool van de toepassings gateway die twee exemplaren van virtuele machines bevat.

In dit artikel leert u het volgende:

* Een zelfondertekend certificaat maken
* Een netwerk instellen
* Een toepassingsgateway maken met behulp van het certificaat
* Een listener-en omleidings regel toevoegen
* Een virtuele-machineschaalset maken met de standaard back-endgroep

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voor deze zelf studie is de Azure PowerShell module versie 1.0.0 of hoger vereist voor het maken van een certificaat en het installeren van IIS. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u de opdrachten in deze zelf studie wilt uitvoeren, moet u ook uitvoeren `Login-AzAccount` om een verbinding te maken met Azure.

## <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

Voor productie gebruik moet u een geldig certificaat importeren dat is ondertekend door een vertrouwde provider. Voor deze zelfstudie maakt u een zelfondertekend certificaat met behulp van [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). U kunt [Export-PfxCertificate ](/powershell/module/pkiclient/export-pfxcertificate) gebruiken met de Thumbprint die is geretourneerd om een ​​PFX-bestand uit het certificaat te exporteren.

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
8. Zorg ervoor dat bij **frontend-IP-configuratie** het **IP-adres type** **openbaar** is en **Nieuw maken** is geselecteerd. Voer *myAGPublicIPAddress* in als naam. Accepteer de standaardwaarden voor de overige instellingen en klik op **OK**.
9. Onder **configuratie** van de listener selecteert u **https**, selecteert u vervolgens **een bestand** en navigeert u naar het *c:\appgwcert.pfx* -bestand en selecteert u **openen**.
10. Typ *appgwcert* voor de certificaat naam en *Azure123456.* als het wachtwoord.
11. Zorg ervoor dat de Web Application firewall is uitgeschakeld en selecteer **OK**.
12. Controleer de instellingen op de pagina samen vatting en selecteer vervolgens **OK** om de netwerk resources en de toepassings gateway te maken. Het kan enkele minuten duren voordat de toepassings gateway is gemaakt. wacht tot de implementatie is voltooid voordat u verdergaat met de volgende sectie.

### <a name="add-a-subnet"></a>Een subnet toevoegen

1. Selecteer **alle resources** in het menu aan de linkerkant en selecteer vervolgens **myVNet** in de lijst met resources.
2. Selecteer **subnetten** en klik vervolgens op **subnet**.

    ![Subnet maken](./media/create-url-route-portal/application-gateway-subnet.png)

3. Typ *myBackendSubnet* voor de naam van het subnet.
4. Typ *10.0.2.0/24* voor het adres bereik en selecteer vervolgens **OK**.

## <a name="add-a-listener-and-redirection-rule"></a>Een listener-en omleidings regel toevoegen

### <a name="add-the-listener"></a>De listener toevoegen

Voeg eerst de listener met de naam *myListener* toe voor poort 80.

1. Open de resource groep **myResourceGroupAG** en selecteer **myAppGateway**.
2. Selecteer **listeners** en selecteer **+ Basic**.
3. Typ *MyListener* voor de naam.
4. Typ *http Port* voor de nieuwe frontend-poort naam en *80* voor de poort.
5. Zorg ervoor dat het protocol is ingesteld op **http** en selecteer **OK**.

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Een routerings regel met een omleidings configuratie toevoegen

1. Selecteer op **myAppGateway** de optie **regels** en selecteer vervolgens **routerings regel voor aanvraag**.
2. Typ *firewallregel2* voor de naam van de **regel**.
3. Zorg ervoor dat **MyListener** is geselecteerd voor de listener.
4. Klik op het tabblad **backend-doelen** en selecteer **doel type** als *omleiding*.
5. Selecteer voor **omleidings type** de optie **permanent**.
6. Selecteer **listener** voor **omleidings doel**.
7. Zorg ervoor dat de **doel-listener** is ingesteld op **appGatewayHttpListener**.
8. Selecteer *Ja* in het deel **reeks query toevoegen** en het **pad include** .
9. Selecteer **Toevoegen**.

## <a name="create-a-virtual-machine-scale-set"></a>Een virtuele-machineschaalset maken

In dit voorbeeld maakt u een virtuele-machineschaalset om servers op te geven voor de back-endpool in de toepassingsgateway.

1. Selecteer **+ een resource maken** in de linkerbovenhoek van de portal.
2. Selecteer **Compute**.
3. Typ *schaal set* in het zoekvak en druk op ENTER.
4. Selecteer **schaalset voor virtuele machines** en selecteer vervolgens **maken**.
5. Typ *myvmss* voor de naam van de **virtuele-machine schaalset**.
6. Voor installatie kopie van het besturings systeem, * * zorg ervoor dat **Windows Server 2016 Data Center** is geselecteerd.
7. Selecteer voor **resource groep** de optie **myResourceGroupAG**.
8. Typ *azureuser* voor **gebruikers naam**.
9. Voor **wacht woord**, typt u *Azure123456!* en bevestig het wacht woord.
10. Zorg ervoor dat de waarde **2** is voor het **aantal exemplaren**.
11. Selecteer **D2s_v3** voor de **instantie grootte**.
12. Zorg ervoor dat onder **netwerken** de optie **taak verdeling kiezen** is ingesteld op **Application Gateway**.
13. Zorg ervoor dat de **toepassings gateway** is ingesteld op **myAppGateway**.
14. Zorg ervoor dat het **subnet** is ingesteld op **myBackendSubnet**.
15. Selecteer **Maken**.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>De schaalset koppelen aan de juiste back-end-groep

De gebruikers interface van de portal voor virtuele-machine schaal sets maakt een nieuwe back-end-pool voor de schaalset, maar u wilt deze koppelen aan uw bestaande appGatewayBackendPool.

1. Open de resource groep **myResourceGroupAg** .
2. Selecteer **myAppGateway**.
3. Selecteer **back-endservers**.
4. Selecteer **myAppGatewaymyvmss**.
5. Selecteer **alle doelen verwijderen uit back-end-pool**.
6. Selecteer **Opslaan**.
7. Nadat dit proces is voltooid, selecteert u de back- **myAppGatewaymyvmss** , selecteert u **verwijderen** en vervolgens **OK** om te bevestigen.
8. Selecteer **appGatewayBackendPool**.
9. Onder **doelen** selecteert u **VMSS**.
10. Selecteer onder **VMSS** de optie **myvmss**.
11. Selecteer **myvmssNic** bij **netwerk interface configuraties**.
12. Selecteer **Opslaan**.

### <a name="upgrade-the-scale-set"></a>De schaalset upgraden

Ten slotte moet u de schaalset bijwerken met deze wijzigingen.

1. Selecteer de schaalset **myvmss** .
2. Selecteer onder **Instellingen** de optie **Instanties**.
3. Selecteer beide instanties en selecteer vervolgens **upgrade**.
4. Selecteer **Ja** om te bevestigen.
5. Nadat dit is voltooid, gaat u terug naar de **myAppGateway** en selecteert u **back-endservers**. U ziet nu dat de **appGatewayBackendPool** twee doelen heeft en  **myAppGatewaymyvmss** geen doelen heeft.
6. Selecteer **myAppGatewaymyvmss** en selecteer vervolgens **verwijderen**.
7. Selecteer **OK** om te bevestigen.

### <a name="install-iis"></a>IIS installeren

Een eenvoudige manier om IIS te installeren op de schaalset is door Power shell te gebruiken. Klik in de portal op het pictogram Cloud Shell en zorg ervoor dat **Power shell** is geselecteerd.

Plak de volgende code in het Power shell-venster en druk op ENTER.

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
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

Nadat u de instanties met IIS hebt gewijzigd, moet u de schaalset opnieuw bijwerken met deze wijziging.

1. Selecteer de schaalset **myvmss** .
2. Selecteer onder **Instellingen** de optie **Instanties**.
3. Selecteer beide instanties en selecteer vervolgens **upgrade**.
4. Selecteer **Ja** om te bevestigen.

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

U kunt het open bare IP-adres van de toepassing ophalen via de overzichts pagina van de toepassings gateway.

1. Selecteer **myAppGateway**.
2. Noteer het IP-adres onder het **open bare frontend-IP-adres** op de pagina **overzicht** .

3. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. Bijvoorbeeld: http://52.170.203.149

   ![Beveiligingswaarschuwing](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Als u de beveiligings waarschuwing wilt accepteren als u een zelfondertekend certificaat hebt gebruikt, selecteert u **Details** en **gaat u naar de webpagina**. Uw beveiligde IIS-website wordt vervolgens weergegeven zoals in het volgende voorbeeld:

   ![Basis-URL testen in de toepassingsgateway](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een toepassings gateway met interne omleiding](redirect-internal-site-powershell.md).