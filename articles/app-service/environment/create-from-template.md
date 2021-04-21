---
title: Een ASE maken met ARM
description: Meer informatie over het maken van een externe of ILB App Service omgeving met behulp van een Azure Resource Manager sjabloon.
author: ccompy
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 10482538c819f9713e9940cffbeb5a1f966ddcf5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832046"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Een ASE maken met behulp van een Azure Resource Manager sjabloon

## <a name="overview"></a>Overzicht

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure App Service omgevingen (ASE's) kunnen worden gemaakt met een eindpunt dat toegankelijk is via internet of een eindpunt op een intern adres in een virtueel Azure-netwerk (VNet). Wanneer dit eindpunt wordt gemaakt met een intern eindpunt, wordt dat eindpunt geleverd door een Azure-onderdeel dat een interne load balancer (ILB) wordt genoemd. De ASE op een intern IP-adres wordt een ILB AS-adres genoemd. De ASE met een openbaar eindpunt wordt een externe ASE genoemd. 

Een ASE kan worden gemaakt met behulp van de Azure Portal of een Azure Resource Manager sjabloon. In dit artikel worden de stappen en syntaxis beschreven die u nodig hebt om een externe ASE of ILB AS-Resource Manager maken. Zie voor meer informatie over het maken van een AS-Azure Portal in de Azure Portal een externe [ASE maken][MakeExternalASE] of [Een ILB AS-as-as maken.][MakeILBASE]

Wanneer u een ASE maakt in de Azure Portal, kunt u uw VNet tegelijkertijd maken of een bestaande VNet kiezen waarin u wilt implementeren. Wanneer u een ASE maakt op basis van een sjabloon, moet u beginnen met: 

* Een Resource Manager VNet.
* Een subnet in dat VNet. We raden een ASE-subnetgrootte van met 256 adressen aan om te voldoen aan toekomstige groei- en `/24` schaalbehoeften. Nadat de ASE is gemaakt, kunt u de grootte niet meer wijzigen.
* De resource-id van uw VNet. U kunt deze informatie verkrijgen uit de Azure Portal onder de eigenschappen van uw virtuele netwerk.
* Het abonnement waar u in wilt implementeren.
* De locatie waar u wilt implementeren.

Het maken van uw ASE automatiseren:

1. Maak de ASE op basis van een sjabloon. Als u een externe ASE maakt, bent u na deze stap klaar. Als u een ILB AS-account maakt, zijn er nog een paar dingen die u moet doen.

2. Nadat uw ILB ASE is gemaakt, wordt een TLS/SSL-certificaat geüpload dat overeenkomt met uw ILB ASE-domein.

3. Het geüploade TLS/SSL-certificaat wordt als standaard-TLS/SSL-certificaat toegewezen aan de ILB ASE.  Dit certificaat wordt gebruikt voor TLS/SSL-verkeer naar apps op de ILB ASE wanneer ze het algemene hoofddomein gebruiken dat is toegewezen aan de ASE (bijvoorbeeld `https://someapp.mycustomrootdomain.com` ).


## <a name="create-the-ase"></a>De ASE maken
Een Resource Manager die een ASE maakt en het bijbehorende parametersbestand is [beschikbaar in een voorbeeld][quickstartasev2create] op GitHub.

Als u een ILB AS-account wilt maken, gebruikt u deze Resource Manager [sjabloonvoorbeelden][quickstartilbasecreate]. Ze zijn geschikt voor die use-case. De meeste parameters in deazuredeploy.parameters.js *op bestand* zijn gemeenschappelijk voor het maken van ILB AS-as-en externe AS-ases. In de volgende lijst worden speciale, of unieke parameters genoemd wanneer u een ILB ASE maakt:

* *internalLoadBalancingMode:* in de meeste gevallen stelt u dit in op 3, wat betekent dat zowel HTTP/HTTPS-verkeer op poorten 80/443 als de poorten voor beheer/gegevenskanaal die worden geluisterd door de FTP-service op de ASE, worden gebonden aan een intern adres van een virtueel netwerk dat is toegewezen via ILB. Als deze eigenschap is ingesteld op 2, zijn alleen de FTP-servicegerelateerde poorten (zowel besturings- als gegevenskanalen) gebonden aan een ILB-adres. Het HTTP/HTTPS-verkeer blijft op het openbare VIP.
* *dnsSuffix:* met deze parameter definieert u het standaard hoofddomein dat is toegewezen aan de ASE. In de openbare variatie van Azure App Service is het standaard hoofddomein voor alle web-apps *azurewebsites.net*. Omdat een ILB AS-omgeving intern is voor het virtuele netwerk van een klant, is het niet zinvol om het standaard hoofddomein van de openbare service te gebruiken. In plaats daarvan moet een ILB AS-omgeving een standaard hoofddomein hebben dat zinvol is voor gebruik binnen het interne virtuele netwerk van een bedrijf. Contoso Corporation kan bijvoorbeeld een standaard hoofddomein van *internal-contoso.com* gebruiken voor apps die zijn bedoeld om te worden opgelost en alleen toegankelijk zijn binnen het virtuele netwerk van Contoso. 
* *ipSslAddressCount:* deze parameter wordt automatisch ingesteld op een waarde van 0 in de *azuredeploy.jsin* het bestand omdat ILB AS-adressen slechts één ILB-adres hebben. Er zijn geen expliciete IP-SSL-adressen voor een ILB AS-adres. Daarom moet de IP-SSL-adresgroep voor een ILB AS-adres worden ingesteld op nul. Anders treedt er een inrichtingsfout op. 

Nadat de *azuredeploy.parameters.jsbestand* is ingevuld, maakt u de ASE met behulp van het PowerShell-codefragment. Wijzig de bestandspaden zo dat ze overeenkomen Resource Manager sjabloonbestandslocaties op uw computer. Vergeet niet om uw eigen waarden op te geven Resource Manager naam van de implementatie en de naam van de resourcegroep:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Het duurt ongeveer een uur voordat de ASE is gemaakt. Vervolgens wordt de ASE weergegeven in de portal in de lijst met ASE's voor het abonnement dat de implementatie heeft geactiveerd.

## <a name="upload-and-configure-the-default-tlsssl-certificate"></a>Het 'standaard' TLS/SSL-certificaat uploaden en configureren
Een TLS/SSL-certificaat moet aan de ASE zijn gekoppeld als het 'standaard'-TLS/SSL-certificaat dat wordt gebruikt om TLS-verbindingen met apps tot stand te brengen. Als het standaard-DNS-achtervoegsel van de ASE internal-contoso.com *is,* vereist een verbinding met een TLS/SSL-certificaat dat geldig is voor `https://some-random-app.internal-contoso.com` **.internal-contoso.com.* 

Verkrijg een geldig TLS/SSL-certificaat met behulp van interne certificeringsinstanties, koop een certificaat van een externe verlener of gebruik een zelf-ondertekend certificaat. Ongeacht de bron van het TLS/SSL-certificaat, moeten de volgende certificaatkenmerken correct worden geconfigureerd:

* **Onderwerp:** Dit kenmerk moet worden ingesteld op **.your-root-domain-here.com*.
* **Alternatieve onderwerpnaam:** dit kenmerk moet zowel **.your-root-domain-here.com* als **.scm.your-root-domain-here.com.* TLS-verbindingen met de SCM/Kudu-site die aan elke app zijn gekoppeld, gebruiken een adres van het *formulier your-app-name.scm.your-root-domain-here.com*.

Met een geldig TLS/SSL-certificaat hebt u twee aanvullende voorbereidende stappen nodig. Converteer/sla het TLS/SSL-certificaat op als een PFX-bestand. Houd er rekening mee dat het PFX-bestand alle tussenliggende en basiscertificaten moet bevatten. Beveilig het bestand met een wachtwoord.

Het PFX-bestand moet worden geconverteerd naar een base64-tekenreeks omdat het TLS/SSL-certificaat wordt geüpload met behulp van een Resource Manager sjabloon. Omdat Resource Manager tekstbestanden zijn, moet het PFX-bestand worden geconverteerd naar een base64-tekenreeks. Op deze manier kan deze worden opgenomen als een parameter van de sjabloon.

Gebruik het volgende PowerShell-codefragment om:

* Genereer een zelf-ondertekend certificaat.
* Exporteer het certificaat als een PFX-bestand.
* Converteer het PFX-bestand naar een tekenreeks met base64-codering.
* Sla de met base64 gecodeerde tekenreeks op in een afzonderlijk bestand. 

Deze PowerShell-code voor base64-codering is aangepast aan de [blog met PowerShell-scripts:][examplebase64encoding]

```powershell
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

Nadat het TLS/SSL-certificaat is gegenereerd en geconverteerd naar een met Base64 gecodeerde tekenreeks, gebruikt u het voorbeeld van de Resource Manager-sjabloon Het standaard [SSL-certificaat][quickstartconfiguressl] configureren op GitHub. 

De parameters in de *azuredeploy.parameters.jsin* het bestand worden hier vermeld:

* *appServiceEnvironmentName:* de naam van de ILB AS-omgeving die wordt geconfigureerd.
* *existingAseLocation:* teksttekenreeks met de Azure-regio waar de ILB AS-omgeving is geïmplementeerd.  Bijvoorbeeld: VS - zuid-centraal.
* *pfxBlobString:* de met based64 gecodeerde tekenreeksweergave van het PFX-bestand. Gebruik het codefragment dat eerder is weergegeven en kopieer de tekenreeks in exportedcert.pfx.b64. Plak deze als de waarde van het *kenmerk pfxBlobString.*
* *wachtwoord:* het wachtwoord dat wordt gebruikt om het PFX-bestand te beveiligen.
* *certificateThumbprint:* de vingerafdruk van het certificaat. Als u deze waarde op haalt uit PowerShell (bijvoorbeeld *$certificate. Vingerafdruk van* het eerdere codefragment) kunt u de waarde gebruiken zoals deze is. Als u de waarde uit het dialoogvenster Windows-certificaat kopieert, moet u de overbodige spaties uit de doos halen. De *certificateThumbprint moet* er ongeveer uitzien als AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName:* een gebruiksvriendelijke tekenreeks-id van uw eigen keuze die wordt gebruikt om het certificaat te identiteiten. De naam wordt gebruikt als onderdeel van de unieke Resource Manager-id voor de entiteit *Microsoft.Web/certificates* die het TLS/SSL-certificaat vertegenwoordigt. De naam *moet* eindigen met het volgende achtervoegsel: \_ yourASENameHere_InternalLoadBalancingASE. De Azure Portal dit achtervoegsel gebruikt als indicator dat het certificaat wordt gebruikt voor het beveiligen van een AS-omgeving met ILB-functie.

Hier wordt een verkort voorbeeld van *azuredeploy.parameters.jsweergegeven:*

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appServiceEnvironmentName": {
      "value": "yourASENameHere"
    },
    "existingAseLocation": {
      "value": "East US 2"
    },
    "pfxBlobString": {
      "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
    },
    "password": {
      "value": "PASSWORDGOESHERE"
    },
    "certificateThumbprint": {
      "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
    },
    "certificateName": {
      "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
    }
  }
}
```

Nadat de *azuredeploy.parameters.jsbestand* is ingevuld, configureert u het standaard-TLS/SSL-certificaat met behulp van het PowerShell-codefragment. Wijzig de bestandspaden zo dat deze overeenkomen met de Resource Manager sjabloonbestanden zich op uw computer bevinden. Vergeet niet om uw eigen waarden op te geven Resource Manager naam van de implementatie en de naam van de resourcegroep:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Het duurt ongeveer 40 minuten per ASE-front-end om de wijziging toe te passen. Voor een AS-omgeving met een standaardgrootte die gebruikmaakt van twee front-ends, duurt het ongeveer één uur en 20 minuten voordat de sjabloon is voltooid. Terwijl de sjabloon wordt uitgevoerd, kan de ASE niet worden geschaald.  

Nadat de sjabloon is gemaakt, zijn apps in de ILB AS-ase toegankelijk via HTTPS. De verbindingen worden beveiligd met behulp van het standaard-TLS/SSL-certificaat. Het standaard-TLS/SSL-certificaat wordt gebruikt wanneer apps op de ILB ASE worden geadresseerd met behulp van een combinatie van de toepassingsnaam plus de standaardhostnaam. Gebruikt bijvoorbeeld `https://mycustomapp.internal-contoso.com` het standaard-TLS/SSL-certificaat voor **.internal-contoso.com.*

Ontwikkelaars kunnen echter, net als apps die worden uitgevoerd op de openbare multitenant-service, aangepaste hostnamen configureren voor afzonderlijke apps. Ze kunnen ook unieke SNI TLS/SSL-certificaatbindingen configureren voor afzonderlijke apps.

## <a name="app-service-environment-v1"></a>App Service-omgeving v1 ##
App Service Environment heeft twee versies: ASEv1 en ASEv2. De voorgaande informatie is gebaseerd op ASEv2. In deze sectie leest u wat de verschillen zijn tussen ASEv1 en ASEv2.

In ASEv1 beheert u alle resources handmatig. Dit geldt ook voor de front-ends, werkrollen en IP-adressen die worden gebruikt voor op IP gebaseerd SSL. Voordat u uw abonnement App Service uitschalen, moet u de werkgroep uitschalen die u wilt hosten.

ASEv1 maakt gebruik van een ander prijsmodel dan ASEv2. In ASEv1 betaalt u voor elke toegewezen vCPU. Dit geldt ook voor vCCPUs die worden gebruikt voor front-ends of werkbelastingen die geen workloads hosten. In ASEv1 is de maximale schaalgrootte voor een AS-omgeving standaard 55 hosts (in totaal). Dit is inclusief werkrollen en front-ends. Een voordeel van ASEv1 is dat deze versie kan worden geïmplementeerd in een klassiek virtueel netwerk en een virtueel netwerk van Resource Manager. Zie [Inleiding tot App Service Environment v1][ASEv1Intro] voor meer informatie over ASEv1.

Zie Create [an ILB ASE v1 with a Resource Manager template (Een ILB ASE v1][ILBASEv1Template]met een Resource Manager maken) als u een ASEv1-sjabloon wilt maken met behulp Resource Manager sjabloon .


<!--Links-->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../../app-service/configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
