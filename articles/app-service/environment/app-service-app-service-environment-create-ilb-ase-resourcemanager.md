---
title: ILB ASE v1 maken
description: Een App Service omgeving maken met een interne load balancer (ILB ASE). Dit document is alleen bedoeld voor klanten die gebruikmaken van de oudere V1-ASE.
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 2a03b791f37868010e107214ddcb7cf42174e4e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85833550"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Een ILB ASE maken met behulp van Azure Resource Manager-sjablonen

> [!NOTE] 
> Dit artikel heeft betrekking op de App Service Environment v1. Er is een nieuwere versie van de App Service Environment die eenvoudiger is te gebruiken en wordt uitgevoerd op een krachtigere infra structuur. Begin met de [Inleiding tot de app service Environment](intro.md)voor meer informatie over de nieuwe versie.
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht
App Service omgevingen kunnen worden gemaakt met een intern adres van een virtueel netwerk in plaats van een open bare VIP.  Dit interne adres wordt verzorgd door een Azure-onderdeel dat de interne load balancer (ILB) wordt genoemd.  U kunt een ILB ASE maken met behulp van de Azure Portal.  Het kan ook worden gemaakt met behulp van automatisering via Azure Resource Manager sjablonen.  In dit artikel worden de stappen en syntaxis beschreven die nodig zijn voor het maken van een ILB-ASE met Azure Resource Manager sjablonen.

Er zijn drie stappen betrokken bij het automatiseren van het maken van een ILB-ASE:

1. Eerst wordt de basis-ASE gemaakt in een virtueel netwerk met behulp van een intern load balancer adres in plaats van een open bare VIP.  Als onderdeel van deze stap wordt een root-domein naam toegewezen aan de ILB-ASE.
2. Zodra de ILB-ASE is gemaakt, wordt er een TLS/SSL-certificaat geüpload.  
3. Het geüploade TLS/SSL-certificaat is expliciet toegewezen aan de ILB-ASE als het ' standaard ' TLS/SSL-certificaat.  Dit TLS/SSL-certificaat wordt gebruikt voor TLS-verkeer naar apps op de ILB-ASE wanneer de apps worden geadresseerd met behulp van het gemeen schappelijke hoofd domein dat is toegewezen aan de ASE (bijvoorbeeld `https://someapp.mycustomrootcomain.com` )

## <a name="creating-the-base-ilb-ase"></a>De basis-ILB-ASE maken
Een voor beeld Azure Resource Manager sjabloon en het bijbehorende parameter bestand, zijn [hier][quickstartilbasecreate]beschikbaar op github.

De meeste para meters in de *azuredeploy.parameters.jsop* bestand zijn gebruikelijk voor het maken van zowel ILB as als as gebonden aan een open bare VIP.  In de onderstaande lijst worden para meters van speciale opmerkingen, of die uniek zijn, aangeroepen bij het maken van een ILB-ASE:

* *internalLoadBalancingMode*: in de meeste gevallen stelt u deze in op 3. Dit betekent dat zowel HTTP/HTTPS-verkeer op poort 80/443 en de besturings-en gegevens kanaal poorten die door de FTP-service op de ASE worden geluisterd, gebonden zijn aan een ILB toegewezen virtueel netwerk adres.  Als deze eigenschap wordt ingesteld op 2, worden alleen de aan de FTP-service gerelateerde poorten (besturings-en gegevens kanalen) gebonden aan een ILB-adres, terwijl het HTTP/HTTPS-verkeer op de open bare VIP blijft.
* *dnsSuffix*: deze para meter definieert het standaard hoofd domein dat wordt toegewezen aan de ASE.  In de open bare variatie van Azure App Service is het standaard hoofd domein voor alle web-apps *azurewebsites.net*.  Omdat een ILB-ASE echter intern is voor het virtuele netwerk van een klant, is het niet zinvol om het standaard hoofd domein van de open bare service te gebruiken.  In plaats daarvan moet een ILB-ASE een standaard hoofd domein hebben dat zinvol is voor gebruik binnen het interne virtuele netwerk van het bedrijf.  Zo kan een hypothetische contoso-onderneming bijvoorbeeld gebruikmaken van een standaard hoofd domein van *Internal-contoso.com* voor apps die alleen bedoeld zijn om te worden omgezet in het virtuele netwerk van contoso. 
* *ipSslAddressCount*: deze para meter wordt automatisch ingesteld op de waarde 0 in de *azuredeploy.jsin* het bestand omdat ILB as slechts één ILB-adres heeft.  Er zijn geen expliciete IP-SSL-adressen voor een ILB-ASE en daarom moet de IP-SSL-adres groep voor een ILB ASE worden ingesteld op nul, anders treedt er een inrichtings fout op. 

Zodra de *azuredeploy.parameters.jsin* het bestand is ingevuld voor een ILB-ASE, kan de ILB ASE worden gemaakt met behulp van het volgende Power shell-code fragment.  Wijzig de bestands paden zodat deze overeenkomen met de Azure Resource Manager sjabloon bestanden op de computer staan.  Vergeet ook niet om uw eigen waarden op te geven voor de naam van de Azure Resource Manager implementatie en de naam van de resource groep.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Nadat de Azure Resource Manager-sjabloon is ingediend, duurt het enkele uren voordat de ILB ASE is gemaakt.  Zodra het maken is voltooid, wordt de ILB ASE weer gegeven in de portal UX in de lijst met App Service omgevingen voor het abonnement dat de implementatie heeft geactiveerd.

## <a name="uploading-and-configuring-the-default-tlsssl-certificate"></a>Het ' standaard ' TLS/SSL-certificaat uploaden en configureren
Zodra de ILB-ASE is gemaakt, moet er een TLS/SSL-certificaat aan de ASE worden gekoppeld als het ' standaard ' TLS/SSL-certificaat dat wordt gebruikt voor het tot stand brengen van TLS/SSL-verbindingen met apps.  Als u doorgaat met het voor beeld van een hypothetische Contoso Corporation, als het standaard-DNS-achtervoegsel van de ASE *Internal-contoso.com* is, dan is een verbinding *`https://some-random-app.internal-contoso.com`* vereist voor een TLS/SSL-certificaat dat geldig is voor **. Internal-contoso.com*. 

Er zijn verschillende manieren om een geldig TLS/SSL-certificaat met inbegrip van interne Ca's te verkrijgen, een certificaat van een externe verlener te kopen en een zelfondertekend certificaat te gebruiken.  Ongeacht de bron van het TLS/SSL-certificaat moeten de volgende certificaat kenmerken correct worden geconfigureerd:

* *Onderwerp*: dit kenmerk moet worden ingesteld op **. Your-Root-Domain-here.com*
* *Alternatieve naam voor onderwerp*: dit kenmerk moet zowel **. Your-Root-Domain-here.com* als **. scm.Your-Root-Domain-here.com* bevatten.  De reden voor de tweede vermelding is dat er TLS-verbindingen met de SCM/kudu-site die aan elke app zijn gekoppeld, worden gemaakt met behulp van een adres van de notatie *your-app-name.scm.Your-Root-Domain-here.com*.

Als er een geldig TLS/SSL-certificaat beschikbaar is, zijn er twee extra voorbereidende stappen nodig.  Het TLS/SSL-certificaat moet worden geconverteerd/opgeslagen als een. pfx-bestand.  Houd er rekening mee dat het pfx-bestand alle tussenliggende en basis certificaten moet bevatten en moet worden beveiligd met een wacht woord.

Vervolgens moet het bestand resultd. pfx worden geconverteerd naar een base64-teken reeks omdat het TLS/SSL-certificaat wordt geüpload met behulp van een Azure Resource Manager sjabloon.  Omdat Azure Resource Manager sjablonen tekst bestanden zijn, moet het pfx-bestand worden geconverteerd naar een base64-teken reeks zodat het kan worden opgenomen als een para meter van de sjabloon.

In het onderstaande Power shell-code fragment ziet u een voor beeld van het genereren van een zelfondertekend certificaat, het exporteren van het certificaat als een. pfx-bestand, het converteren van het pfx-bestand naar een base64-gecodeerde teken reeks en het opslaan van de base64-gecodeerde teken reeks in een afzonderlijk bestand.  De Power shell-code voor Base64 code ring is aangepast op basis van de [blog Power shell-scripts][examplebase64encoding].

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

Zodra het TLS/SSL-certificaat is gegenereerd en geconverteerd naar een base64-gecodeerde teken reeks, kan het voor beeld Azure Resource Manager sjabloon op GitHub voor [het configureren van het standaard TLS/SSL-certificaat][configuringDefaultSSLCertificate] worden gebruikt.

De para meters in de *azuredeploy.parameters.jsvoor* het bestand worden hieronder weer gegeven:

* *appServiceEnvironmentName*: de naam van de ILB-ASE die wordt geconfigureerd.
* *existingAseLocation*: een tekst teken reeks met de Azure-regio waar de ILB ASE is geïmplementeerd.  Bijvoorbeeld: Zuid-Centraal vs.
* *pfxBlobString*: de met based64 gecodeerde teken reeks representatie van het pfx-bestand.  Met behulp van het code fragment dat eerder is weer gegeven, kopieert u de teken reeks in "exportedcert. pfx. b64" en plakt u deze in als de waarde van het kenmerk *pfxBlobString* .
* *wacht woord*: het wacht woord dat wordt gebruikt om het pfx-bestand te beveiligen.
* *certificateThumbprint*: de vinger afdruk van het certificaat.  Als u deze waarde ophaalt uit Power shell (bijvoorbeeld *$Certificate. Vinger afdruk* van het vorige code fragment), kunt u de waarde als-is.  Als u de waarde echter uit het dialoog venster Windows-certificaat kopieert, vergeet dan niet de overbodige spaties te verwijderen.  De *certificateThumbprint* moet er ongeveer als volgt uitzien: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificaatpad*: een beschrijvende teken reeks-id van uw eigen keuze voor het identificeren van het certificaat.  De naam wordt gebruikt als onderdeel van de unieke Azure Resource Manager-id voor de entiteit *micro soft. Web/certificates* die het TLS/SSL-certificaat vertegenwoordigt.  De naam **moet** eindigen op het volgende achtervoegsel:  \_ yourASENameHere_InternalLoadBalancingASE.  Dit achtervoegsel wordt door de portal gebruikt als een indicator waarmee het certificaat wordt gebruikt voor het beveiligen van een ILB-ASE.

Een afkorting voor beeld van *azuredeploy.parameters.jsop* wordt hieronder weer gegeven:

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

Zodra de *azuredeploy.parameters.jsin* het bestand is ingevuld, kan het standaard TLS/SSL-certificaat worden geconfigureerd met behulp van het volgende Power shell-code fragment.  Wijzig de bestands paden zodat deze overeenkomen met de Azure Resource Manager sjabloon bestanden op de computer staan.  Vergeet ook niet om uw eigen waarden op te geven voor de naam van de Azure Resource Manager implementatie en de naam van de resource groep.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Nadat de Azure Resource Manager-sjabloon is ingediend, duurt het ongeveer 40 minuten per ASE front-end om de wijziging toe te passen.  Bijvoorbeeld, met een standaard ASE met twee front-ends, duurt de sjabloon ongeveer één uur en twintig minuten om te volt ooien.  Terwijl de sjabloon wordt uitgevoerd, kan de ASE niet worden geschaald.  

Zodra de sjabloon is voltooid, zijn apps op de ILB ASE toegankelijk via HTTPS en worden de verbindingen beveiligd met het standaard-TLS/SSL-certificaat.  Het standaard TLS/SSL-certificaat wordt gebruikt wanneer apps op de ILB-ASE worden geadresseerd met behulp van een combi natie van de toepassings naam plus de standaard-hostnaam.  Gebruik bijvoorbeeld *`https://mycustomapp.internal-contoso.com`* het standaard TLS/SSL-certificaat voor **. Internal-contoso.com*.

Maar net als bij apps die worden uitgevoerd op de open bare multi tenant-service kunnen ontwikkel aars ook aangepaste hostnamen configureren voor afzonderlijke apps en vervolgens unieke SNI TLS/SSL-certificaat bindingen configureren voor afzonderlijke apps.  

## <a name="getting-started"></a>Aan de slag
Zie [Inleiding tot app service Environment](app-service-app-service-environment-intro.md) om aan de slag te gaan met app service omgevingen

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

