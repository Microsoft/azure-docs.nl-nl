---
title: Een ASE maken met ARM
description: Meer informatie over het maken van een externe of ILB App Service omgeving met behulp van een Azure Resource Manager sjabloon.
author: ccompy
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 15cd0979fdc2468ab50451042cd99a8442470139
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148172"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Een ASE maken met behulp van een Azure Resource Manager sjabloon

## <a name="overview"></a>Overzicht

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure App Service omgevingen (as) kunnen worden gemaakt met een eind punt dat toegankelijk is via internet of een eind punt op een intern adres in een Azure Virtual Network (VNet). Wanneer u een intern eind punt maakt, wordt dat eind punt verschaft door een Azure-onderdeel, een intern load balancer (ILB). De ASE op een intern IP-adres wordt een ILB-ASE genoemd. De ASE met een openbaar eind punt wordt een externe ASE genoemd. 

Een ASE kan worden gemaakt met behulp van de Azure Portal of een Azure Resource Manager sjabloon. In dit artikel worden de stappen en syntaxis beschreven die u nodig hebt om een externe ASE of ILB-ASE te maken met Resource Manager-sjablonen. Zie voor meer informatie over het maken van een ASE in de Azure Portal [een externe ASE][MakeExternalASE] maken of [een ILB ASE][MakeILBASE]maken.

Wanneer u een ASE maakt in de Azure Portal, kunt u uw VNet op hetzelfde moment maken of een bestaand VNet selecteren dat u wilt implementeren in. Wanneer u een ASE maakt op basis van een sjabloon, moet u beginnen met: 

* Een resource manager-VNet.
* Een subnet in dat VNet. We raden u aan om een ASE-subnet grootte van `/24` met 256-adressen aan te passen aan toekomstige groei-en schaal behoeften. Nadat de ASE is gemaakt, kunt u de grootte niet wijzigen.
* De resource-ID van uw VNet. U kunt deze informatie ophalen van de Azure Portal onder de eigenschappen van uw virtuele netwerk.
* Het abonnement dat u wilt implementeren.
* De locatie waarnaar u wilt implementeren.

Het maken van uw ASE automatiseren:

1. De ASE maken op basis van een sjabloon. Als u een externe ASE maakt, bent u klaar na deze stap. Als u een ILB-ASE maakt, zijn er nog enkele dingen die u moet doen.

2. Nadat uw ILB-ASE is gemaakt, wordt een TLS/SSL-certificaat dat overeenkomt met uw ILB ASE-domein geüpload.

3. Het geüploade TLS/SSL-certificaat is toegewezen aan de ILB-ASE als het ' standaard ' TLS/SSL-certificaat.  Dit certificaat wordt gebruikt voor TLS/SSL-verkeer naar apps op de ILB-ASE wanneer ze gebruikmaken van het gemeen schappelijke hoofd domein dat is toegewezen aan de ASE (bijvoorbeeld `https://someapp.mycustomrootdomain.com` ).


## <a name="create-the-ase"></a>De ASE maken
Een resource manager-sjabloon voor het maken van een ASE en het bijbehorende parameter bestand is beschikbaar [in een voor beeld][quickstartasev2create] op github.

Als u een ILB-ASE wilt maken, gebruikt u deze [voor beelden][quickstartilbasecreate]van Resource Manager-sjablonen. Ze zijn geschikt voor die use-case. De meeste para meters in de *azuredeploy.parameters.jsop* bestand zijn gebruikelijk voor het maken van ILB as en externe as. In de volgende lijst worden de para meters van speciale opmerkingen, of die uniek zijn, aangeroepen wanneer u een ILB-ASE maakt:

* *internalLoadBalancingMode*: Stel dit in de meeste gevallen in op 3. Dit betekent dat zowel HTTP/HTTPS-verkeer op poort 80/443 als de besturings-en gegevens kanaal poorten die worden geluisterd door de FTP-service op de ASE, gebonden zijn aan een ILB-toegewezen virtueel netwerk adres. Als deze eigenschap is ingesteld op 2, worden alleen de FTP-service poorten (zowel besturings-als gegevens kanalen) gebonden aan een ILB-adres. Het HTTP/HTTPS-verkeer blijft op het open bare VIP.
* *dnsSuffix*: met deze para meter wordt het standaard hoofd domein gedefinieerd dat wordt toegewezen aan de ASE. In de open bare variatie van Azure App Service is het standaard hoofd domein voor alle web-apps *azurewebsites.net*. Omdat een ILB-ASE intern is voor het virtuele netwerk van een klant, is het niet zinvol het standaard hoofd domein van de open bare service te gebruiken. In plaats daarvan moet een ILB-ASE een standaard hoofd domein hebben dat zinvol is voor gebruik binnen het interne virtuele netwerk van het bedrijf. Contoso Corporation kan bijvoorbeeld gebruikmaken van een standaard hoofd domein van *Internal-contoso.com* voor apps die bedoeld zijn om te worden omgezet en alleen toegankelijk zijn binnen het virtuele netwerk van contoso. 
* *ipSslAddressCount*: deze para meter wordt automatisch ingesteld op de waarde 0 in de *azuredeploy.jsin* het bestand omdat ILB as slechts één ILB-adres heeft. Er zijn geen expliciete IP-SSL-adressen voor een ILB-ASE. De IP-SSL-adres groep voor een ILB-ASE moet daarom worden ingesteld op nul. Anders treedt er een inrichtings fout op. 

Nadat de *azuredeploy.parameters.jsvoor* het bestand is ingevuld, maakt u het ASE met behulp van het Power shell-code fragment. Wijzig de bestands paden zodat deze overeenkomen met de locatie van het Resource Manager-bestand op uw computer. Zorg ervoor dat u uw eigen waarden opgeeft voor de naam van de Resource Manager-implementatie en de naam van de resource groep:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Het duurt ongeveer een uur voordat de ASE is gemaakt. Vervolgens wordt de ASE weer gegeven in de portal in de lijst met as voor het abonnement dat de implementatie heeft geactiveerd.

## <a name="upload-and-configure-the-default-tlsssl-certificate"></a>Het ' standaard TLS/SSL-certificaat uploaden en configureren
Een TLS/SSL-certificaat moet worden gekoppeld aan de ASE als het ' standaard ' TLS/SSL-certificaat dat wordt gebruikt om TLS-verbindingen met apps tot stand te brengen. Als het standaard-DNS-achtervoegsel van de ASE *Internal-contoso.com* is, is een verbinding `https://some-random-app.internal-contoso.com` vereist voor een TLS/SSL-certificaat dat geldig is voor **. Internal-contoso.com*. 

Verkrijg een geldig TLS/SSL-certificaat door interne certificerings instanties te kopen, een certificaat aan te schaffen bij een externe verlener of een zelfondertekend certificaat te gebruiken. Ongeacht de bron van het TLS/SSL-certificaat moeten de volgende certificaat kenmerken correct worden geconfigureerd:

* **Onderwerp**: dit kenmerk moet worden ingesteld op **. Your-Root-Domain-here.com*.
* **Alternatieve naam voor onderwerp**: dit kenmerk moet zowel **. Your-Root-Domain-here.com* als **. scm.Your-Root-Domain-here.com* bevatten. TLS-verbindingen met de SCM/kudu-site die is gekoppeld aan elke app, gebruiken een adres van de notatie *your-app-name.scm.Your-Root-Domain-here.com*.

Als er een geldig TLS/SSL-certificaat beschikbaar is, zijn er twee extra voorbereidende stappen nodig. Het TLS/SSL-certificaat converteren/opslaan als een. pfx-bestand. Houd er rekening mee dat het pfx-bestand alle tussenliggende en basis certificaten moet bevatten. Beveilig het bestand met een wachtwoord.

Het pfx-bestand moet worden geconverteerd naar een base64-teken reeks omdat het TLS/SSL-certificaat is geüpload met behulp van een resource manager-sjabloon. Omdat Resource Manager-sjablonen tekst bestanden zijn, moet het pfx-bestand worden geconverteerd naar een base64-teken reeks. Op deze manier kan het worden opgenomen als een para meter van de sjabloon.

Gebruik het volgende Power shell-code fragment om:

* Genereer een zelfondertekend certificaat.
* Exporteer het certificaat als een. pfx-bestand.
* Converteer het pfx-bestand naar een met base64 gecodeerde teken reeks.
* Sla de base64-gecodeerde teken reeks op in een afzonderlijk bestand. 

Deze Power shell-code voor Base64 code ring is aangepast op de [blog Power shell-scripts][examplebase64encoding]:

```powershell
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com&quot;,&quot;*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

Nadat het TLS/SSL-certificaat is gegenereerd en is geconverteerd naar een base64-gecodeerde teken reeks, moet u het [standaard SSL-certificaat][quickstartconfiguressl] op github configureren in de voorbeeld sjabloon voor de Resource Manager. 

De para meters in de *azuredeploy.parameters.jsvoor* het bestand worden hier vermeld:

* *appServiceEnvironmentName*: de naam van de ILB-ASE die wordt geconfigureerd.
* *existingAseLocation*: een tekst teken reeks met de Azure-regio waar de ILB ASE is geïmplementeerd.  Bijvoorbeeld: Zuid-Centraal vs.
* *pfxBlobString*: de based64-gecodeerde teken reeks representatie van het pfx-bestand. Gebruik het code fragment dat eerder is weer gegeven en kopieer de teken reeks die is opgenomen in ' exportedcert. pfx. b64 '. Plak deze in als de waarde van het kenmerk *pfxBlobString* .
* *wacht woord*: het wacht woord dat wordt gebruikt om het pfx-bestand te beveiligen.
* *certificateThumbprint*: de vinger afdruk van het certificaat. Als u deze waarde ophaalt uit Power shell (bijvoorbeeld *$Certificate. Vinger afdruk* van het vorige code fragment), kunt u de waarde gebruiken als. Als u de waarde in het dialoog venster Windows-certificaat kopieert, vergeet dan niet de overbodige spaties te verwijderen. De *certificateThumbprint* moet er ongeveer uitzien als AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificaatpad*: een beschrijvende teken reeks-id van uw eigen keuze voor het identificeren van het certificaat. De naam wordt gebruikt als onderdeel van de unieke Resource Manager-id voor de entiteit *micro soft. Web/getuigies* die het TLS/SSL-certificaat vertegenwoordigt. De naam *moet* eindigen op het volgende achtervoegsel: \_ yourASENameHere_InternalLoadBalancingASE. Het Azure Portal gebruikt dit achtervoegsel als een indicator waarmee het certificaat wordt gebruikt voor het beveiligen van een ILB-ASE.

Een afkorting voor beeld van *azuredeploy.parameters.jsop* wordt hier weer gegeven:

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

Nadat de *azuredeploy.parameters.jsin* het bestand is ingevuld, configureert u het standaard TLS/SSL-certificaat met behulp van het Power shell-code fragment. Wijzig de bestands paden zodat deze overeenkomen met de locatie van de Resource Manager-sjabloon bestanden op de computer. Zorg ervoor dat u uw eigen waarden opgeeft voor de naam van de Resource Manager-implementatie en de naam van de resource groep:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Het duurt ongeveer 40 minuten per ASE front-end om de wijziging toe te passen. Bijvoorbeeld: voor een ASE met standaard grootte die twee front-ends gebruikt, duurt de sjabloon ongeveer één uur en 20 minuten om te volt ooien. Terwijl de sjabloon wordt uitgevoerd, kan de ASE niet worden geschaald.  

Nadat de sjabloon is voltooid, kunnen apps op de ILB ASE via HTTPS worden geopend. De verbindingen worden beveiligd met behulp van het standaard TLS/SSL-certificaat. Het standaard TLS/SSL-certificaat wordt gebruikt wanneer apps op de ILB ASE worden geadresseerd met behulp van een combi natie van de naam van de toepassing plus de naam van de standaard-host. `https://mycustomapp.internal-contoso.com`Maakt bijvoorbeeld gebruik van het standaard TLS/SSL-certificaat voor **. Internal-contoso.com*.

Net als bij apps die worden uitgevoerd op de open bare multi tenant-service kunnen ontwikkel aars echter aangepaste hostnamen configureren voor afzonderlijke apps. Ze kunnen ook unieke SNI TLS/SSL-certificaat bindingen configureren voor afzonderlijke apps.

## <a name="app-service-environment-v1"></a>App Service-omgeving v1 ##
App Service Environment heeft twee versies: ASEv1 en ASEv2. De voorgaande informatie is gebaseerd op ASEv2. In deze sectie leest u wat de verschillen zijn tussen ASEv1 en ASEv2.

In ASEv1 beheert u alle resources hand matig. Dit geldt ook voor de front-ends, werkrollen en IP-adressen die worden gebruikt voor op IP gebaseerd SSL. Voordat u uw App Service plan kunt uitschalen, moet u de werk groep die u wilt hosten, uitschalen.

ASEv1 maakt gebruik van een ander prijsmodel dan ASEv2. In ASEv1 betaalt u voor elke toegewezen vCPU. Dit omvat Vcpu's die worden gebruikt voor front-ends of werk rollen die geen werk belastingen hosten. In ASEv1 is de maximale schaalgrootte voor een AS-omgeving standaard 55 hosts (in totaal). Dit is inclusief werkrollen en front-ends. Een voordeel van ASEv1 is dat deze versie kan worden geïmplementeerd in een klassiek virtueel netwerk en een virtueel netwerk van Resource Manager. Zie [Inleiding tot App Service Environment v1][ASEv1Intro] voor meer informatie over ASEv1.

Zie een [ILB ASE v1 maken met een resource manager-sjabloon][ILBASEv1Template]voor het maken van een ASEv1 met behulp van een resource manager-sjabloon.


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