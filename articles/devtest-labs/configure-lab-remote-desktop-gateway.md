---
title: Een lab configureren om Extern bureaublad-gateway te gebruiken in Azure DevTest Labs
description: Informatie over het configureren van een lab in Azure DevTest Labs met een extern bureau blad-gateway om beveiligde toegang tot de Lab-Vm's te garanderen zonder dat de RDP-poort moet worden weer gegeven.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: dcf5191dea64c3d7bf28b9ce1c616d3d2defb73e
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695683"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Uw Lab in Azure DevTest Labs configureren voor het gebruik van een extern bureau blad-gateway
In Azure DevTest Labs kunt u een extern bureau blad-gateway voor uw lab configureren om te zorgen voor veilige toegang tot de virtuele lab-machines (Vm's) zonder dat de RDP-poort moet worden weer gegeven. Het Lab biedt een centrale locatie voor uw Lab-gebruikers om te zien en verbinding te maken met alle virtuele machines waartoe ze toegang hebben. De knop **verbinding maken** op de pagina **virtuele machine** maakt een apparaat-specifiek RDP-bestand dat u kunt openen om verbinding te maken met de computer. U kunt de RDP-verbinding verder aanpassen en beveiligen door uw Lab te koppelen aan een extern bureau blad-gateway. 

Deze methode is veiliger omdat de test gebruiker zich rechtstreeks verifieert bij de gateway computer of bedrijfs referenties kan gebruiken op een gateway computer die lid is van een domein om verbinding te maken met hun computers. Het Lab biedt ook ondersteuning voor het gebruik van token verificatie voor de gateway computer waarmee gebruikers verbinding kunnen maken met hun Lab-virtuele machines zonder dat de RDP-poort aan Internet kan worden blootgesteld. In dit artikel vindt u een voor beeld van het instellen van een lab dat gebruikmaakt van token verificatie om verbinding te maken met Lab-machines.

## <a name="architecture-of-the-solution"></a>Architectuur van de oplossing

![Architectuur van de oplossing](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. De actie [RDP-bestands inhoud ophalen](/rest/api/dtl/virtualmachines/getrdpfilecontents) wordt aangeroepen wanneer u de knop **verbinding maken** selecteert. 1. 
1. De actie RDP-bestands inhoud ophalen wordt aangeroepen `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` om een verificatie token aan te vragen.
    1. `{gateway-hostname}` is de hostnaam van de gateway die is opgegeven op de pagina **Lab-instellingen** voor uw Lab in de Azure Portal. 
    1. `{lab-machine-name}` de naam is van de computer waarmee u verbinding wilt maken.
    1. `{port-number}` is de poort waarop de verbinding moet worden gemaakt. Meestal is deze poort 3389. Als de VM van het lab de [gedeelde IP-](devtest-lab-shared-ip.md) functie in DevTest Labs gebruikt, is de poort verschillend.
1. De extern bureau blad-gateway Defert de aanroep van `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` naar een Azure-functie om het verificatie token te genereren. De DevTest Labs-service bevat automatisch de functie code in de aanvraag header. De functie sleutel moet worden opgeslagen in de sleutel kluis van het lab. De naam van het geheim dat moet worden weer gegeven als het geheim van de **Gateway token** op de pagina **Lab-instellingen** voor het lab.
1. De Azure-functie wordt verwacht een token te retour neren voor certificaat authenticatie op basis van certificaten op de gateway computer.  
1. De actie RDP-bestands inhoud ophalen retourneert vervolgens het volledige RDP-bestand, met inbegrip van de verificatie gegevens.
1. U opent het RDP-bestand met behulp van het RDP-verbindings programma van uw voor keur. Houd er rekening mee dat niet alle RDP-verbindings Programma's de verificatie van tokens ondersteunen. Het verificatie token heeft een verval datum, ingesteld door de functie-app. Maak verbinding met de VM van het lab voordat het token verloopt.
1. Zodra de extern bureau blad-gateway computer het token verifieert in het RDP-bestand, wordt de verbinding doorgestuurd naar uw test machine.

### <a name="solution-requirements"></a>Oplossingsvereisten
Als u wilt werken met de DevTest Labs-token verificatie, zijn er enkele configuratie vereisten voor de gateway-computers, Domain Name Services (DNS) en functions.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Vereisten voor extern bureau blad-gateway computers
- Er moet een TLS/SSL-certificaat op de gateway computer zijn geïnstalleerd om HTTPS-verkeer te kunnen verwerken. Het certificaat moet overeenkomen met de Fully Qualified Domain Name (FQDN) van de load balancer voor de gateway Farm of de FQDN van de computer zelf als er slechts één computer is. TLS/SSL-certificaten met Joker tekens werken niet.  
- Een handtekening certificaat dat is geïnstalleerd op de gateway computer (s). Een handtekening certificaat maken met behulp van [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) script.
- Installeer de module voor [Plug en authenticatie](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) die token verificatie ondersteunt voor de extern bureau blad-gateway. Een voor beeld van een dergelijke module is `RDGatewayFedAuth.msi` dat wordt geleverd met [installatie kopieën van System Center Virtual Machine Manager (VMM)](/system-center/vmm/install-console?view=sc-vmm-1807). Zie [System Center-documentatie](/system-center/) en [prijs informatie](https://www.microsoft.com/cloud-platform/system-center-pricing)voor meer informatie over System Center.  
- De gateway server kan aanvragen afhandelen naar `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` .

    De gateway-hostnaam is de FQDN-naam van de load balancer van de gateway Farm of de FQDN van de computer zelf als er slechts één computer is. De `{lab-machine-name}` is de naam van de test machine waarmee u verbinding wilt maken en de `{port-number}` poort waarmee de verbinding tot stand wordt gebracht.  Deze poort is standaard 3389.  Als de virtuele machine echter de [gedeelde IP-](devtest-lab-shared-ip.md) functie in DevTest Labs gebruikt, is de poort anders.
- De module [toepassings routering aanvragen](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) voor IIS (Internet Information Server) kan worden gebruikt om `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` aanvragen om te leiden naar de Azure-functie, die de aanvraag afhandelt om een token voor verificatie op te halen.


## <a name="requirements-for-azure-function"></a>Vereisten voor Azure function
De Azure-functie verwerkt de aanvraag met de indeling `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` en retourneert het verificatie token op basis van hetzelfde handtekening certificaat dat is geïnstalleerd op de gateway machines. De `{function-app-uri}` is de URI die wordt gebruikt voor toegang tot de functie. De functie sleutel wordt automatisch door gegeven in de koptekst van de aanvraag. Zie voor een voor beeld van een functie [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs) . 


## <a name="requirements-for-network"></a>Vereisten voor netwerk

- DNS voor de FQDN die is gekoppeld aan het TLS/SSL-certificaat dat is geïnstalleerd op de gateway-computers moet verkeer naar de gateway computer of de load balancer van de gateway computer Farm omleiden.
- Als de test machine gebruikmaakt van privé IP-adressen, moet er een netwerkpad van de gateway computer naar de test machine zijn, hetzij via het delen van hetzelfde virtuele netwerk, hetzij met behulp van peered virtuele netwerken.

## <a name="configure-the-lab-to-use-token-authentication"></a>Het lab configureren voor het gebruik van token verificatie 
In deze sectie wordt beschreven hoe u een Lab configureert voor het gebruik van een extern bureau blad-gateway computer die token verificatie ondersteunt. Deze sectie heeft geen betrekking op het instellen van een extern bureau blad-gateway-Farm zelf. Zie de sectie voor beeld voor het [maken van een extern bureau blad-gateway](#sample-to-create-a-remote-desktop-gateway) aan het einde van dit artikel voor meer informatie. 

Voordat u de Lab-instellingen bijwerkt, moet u de sleutel opslaan die nodig is om de functie uit te voeren om een verificatie token te retour neren in de sleutel kluis van het lab. U kunt de functie sleutel waarde ophalen op de pagina **beheren** voor de functie in de Azure Portal. Zie [een geheim toevoegen aan Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault)voor meer informatie over het opslaan van een geheim in een sleutel kluis. Sla de naam van het geheim op voor later gebruik.

Voer de volgende Azure CLI-opdracht uit om de ID van de sleutel kluis van het lab te vinden: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Configureer het lab voor het gebruik van de token verificatie met behulp van de volgende stappen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de lijst.
1. Selecteer in de lijst met Labs uw **Lab**.
1. Selecteer op de pagina Lab de optie **configuratie en beleid**.
1. Selecteer in het menu links in het gedeelte **instellingen** de optie **Lab-instellingen**.
1. Voer in de sectie **extern bureau blad** de Fully QUALIFIED domain name (FQDN) of het IP-adres van de extern bureau blad-Services Gateway computer of farm in voor het veld **hostnaam van Gateway** . Deze waarde moet overeenkomen met de FQDN van het TLS/SSL-certificaat dat op Gateway computers wordt gebruikt.

    ![Opties voor extern bureau blad in Lab-instellingen](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. In de sectie **extern bureau blad** , voor het geheim van **Gateway-tokens** , voert u de naam in van het geheim dat u eerder hebt gemaakt. Deze waarde is niet de functie toets zelf, maar de naam van het geheim in de sleutel kluis van het lab die de functie sleutel bevat.

    ![Geheim van het gateway token in de Lab-instellingen](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Opslaan** Gewijzigde.

    > [!NOTE] 
    > Als u op **Opslaan** klikt, gaat u akkoord met [de licentie voorwaarden van extern bureaublad-gateway](https://www.microsoft.com/licensing/product-licensing/products). Zie voor meer informatie over de externe gateway [Welkom bij extern bureaublad-services](/windows-server/remote/remote-desktop-services/Welcome-to-rds) en [Implementeer uw extern bureau blad-omgeving](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


Zie [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) voor een Power shell-voorbeeld script voor het instellen van de **Gateway-hostnaam** en de geheime instellingen van het **Gateway token** als u de test omgeving via Automation wilt configureren. De [github-opslag plaats van Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) biedt ook een Azure Resource Manager sjabloon waarmee een lab wordt gemaakt of bijgewerkt met de instellingen van de **Gateway-hostnaam** en het **Gateway token geheim** .

## <a name="configure-network-security-group"></a>Netwerkbeveiligingsgroep configureren
Om het lab verder te beveiligen, kan een netwerk beveiligings groep (NSG) worden toegevoegd aan het virtuele netwerk dat wordt gebruikt door de virtuele machines van het lab. Zie [een netwerk beveiligings groep maken, wijzigen of verwijderen](../virtual-network/manage-network-security-group.md)voor instructies voor het instellen van een NSG.

Hier volgt een voor beeld van een NSG die verkeer alleen toestaat die de gateway voor de eerste keer doorloopt. De bron in deze regel is het IP-adres van de afzonderlijke gateway computer of het IP-adres van de load balancer vóór de gateway-computers.

![Netwerk beveiligings groep-regels](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Voor beeld voor het maken van een extern bureau blad-gateway

> [!NOTE] 
> Met behulp van de voorbeeld sjablonen gaat u akkoord met de [licentie voorwaarden van extern bureaublad-gateway](https://www.microsoft.com/licensing/product-licensing/products). Zie voor meer informatie over de externe gateway [Welkom bij extern bureaublad-services](/windows-server/remote/remote-desktop-services/Welcome-to-rds) en [Implementeer uw extern bureau blad-omgeving](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

De [github-opslag plaats van Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) biedt enkele voor beelden voor het instellen van de bronnen die nodig zijn voor het gebruik van token verificatie en extern bureau blad-gateway met DevTest Labs. Deze voor beelden bevatten Azure Resource Manager sjablonen voor gateway machines, Lab-instellingen en functie-apps.

Volg deze stappen om een voor beeld van een oplossing voor de extern bureau blad-gateway Farm in te stellen.

1. Een handtekening certificaat maken.  Voer [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)uit. Sla de vinger afdruk, het wacht woord en de base64-code ring van het gemaakte certificaat op.
2. Een TLS/SSL-certificaat ophalen. De FQDN die is gekoppeld aan het TLS/SSL-certificaat moet voor het domein dat u wilt beheren. Sla de vinger afdruk, het wacht woord en de base64-code ring op voor dit certificaat. Gebruik de volgende opdrachten om de vinger afdruk op te halen met behulp van Power shell.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    Gebruik de volgende opdracht om de base64-code ring op te halen met behulp van Power shell.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. Bestanden downloaden van [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) .

    Voor de sjabloon is toegang tot een paar andere Resource Manager-sjablonen en gerelateerde resources met dezelfde basis-URI vereist. Kopieer alle bestanden van [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) en RDGatewayFedAuth.msi naar een BLOB-container in een opslag account.  
4. **azuredeploy.jsimplementeren op** van [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) . De sjabloon heeft de volgende para meters:
    - adminUsername: vereist.  De gebruikers naam van de beheerder voor de gateway-computers.
    - adminPassword – vereist. Wacht woord voor het beheerders account voor de gateway-computers.
    - instanceCount: het aantal gateway machines dat moet worden gemaakt.  
    - AlwaysOn: Hiermee wordt aangegeven of de gemaakte Azure Functions-app in een warme staat moet blijven of niet. Door de Azure Functions-app te blijven vertragen wanneer gebruikers eerst proberen verbinding te maken met hun Lab-VM, maar ze hebben wel kosten gevolgen.  
    - tokenLifetime: de periode waarin het gemaakte token geldig is. De notatie is uu: MM: SS.
    - sslCertificate: de base64-code ring van het TLS/SSL-certificaat voor de gateway computer.
    - sslCertificatePassword: het wacht woord van het TLS/SSL-certificaat voor de gateway computer.
    - sslCertificateThumbprint: de vinger afdruk van het certificaat voor identificatie in het lokale certificaat archief van het TLS/SSL-certificaat.
    - signCertificate: de base64-code ring voor het ondertekenen van certificaten voor de gateway computer.
    - signCertificatePassword: het wacht woord voor het handtekening certificaat voor de gateway computer.
    - signCertificateThumbprint: de vinger afdruk van het certificaat voor identificatie in het lokale certificaat archief van het handtekening certificaat.
    - _artifactsLocation: de URI-locatie waar alle ondersteunende bronnen kunnen worden gevonden. Deze waarde moet een volledig gekwalificeerd UIR zijn, geen relatief pad.
    - _artifactsLocationSasToken: het Shared Access Signature SAS-token dat wordt gebruikt voor toegang tot ondersteunende bronnen als de locatie een Azure-opslag account is.

    De sjabloon kan worden geïmplementeerd met behulp van de Azure CLI met behulp van de volgende opdracht:

    ```azurecli
    az deployment group create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    Hier volgen de beschrijvingen van de para meters:

    - Het {Storage-account-endpoint} kan worden verkregen door uit te voeren `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob` .  De {Storage-acct-name} is de naam van het opslag account dat bestanden bevat die u hebt geüpload.  
    - De {container-name} is de naam van de container in de {Storage-acct-name} die bestanden bevat die u hebt geüpload.  
    - Het {SAS-token} kan worden verkregen door uit te voeren `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}` . 
        - De {Storage-acct-name} is de naam van het opslag account dat bestanden bevat die u hebt geüpload.  
        - De {container-name} is de naam van de container in de {Storage-acct-name} die bestanden bevat die u hebt geüpload.  
        - De {UTC-verval datum} is de datum, in UTC, waarop het SAS-token verloopt en de SAS-token kan niet meer worden gebruikt voor toegang tot het opslag account.

    Noteer de waarden voor gatewayFQDN en gatewayIP van de sjabloon implementatie-uitvoer. U moet ook de waarde van de functie sleutel voor de zojuist gemaakte functie opslaan, die u kunt vinden op het tabblad [functie-app-instellingen](../azure-functions/functions-how-to-use-azure-function-app-settings.md) .
5. Configureer DNS zodanig dat FQDN van TLS/SSL-certificaat naar het IP-adres van gatewayIP uit de vorige stap leidt.

    Nadat de Extern bureaublad-gateway Farm is gemaakt en de juiste DNS-updates zijn gemaakt, is het klaar om te worden gebruikt door een lab in DevTest Labs. De instellingen voor de **Gateway-hostnaam** en het **Gateway token geheim** moeten worden geconfigureerd voor het gebruik van de door u geïmplementeerde gateway computer (s). 

    > [!NOTE]
    > Als de test machine gebruikmaakt van privé IP-adressen, moet er een netwerkpad van de gateway computer naar de test machine zijn, hetzij via het delen van hetzelfde virtuele netwerk of met behulp van een gekoppeld virtueel netwerk.

    Als zowel de gateway als het lab zijn geconfigureerd, wordt het verbindings bestand dat wordt gemaakt wanneer de gebruiker op de test omgeving klikt automatisch informatie bevatten die **nodig is om** verbinding te maken met behulp van token authenticatie.     

## <a name="next-steps"></a>Volgende stappen
Raadpleeg het volgende artikel voor meer informatie over Extern bureaublad-services: [extern bureaublad-services documentatie](/windows-server/remote/remote-desktop-services/Welcome-to-rds)