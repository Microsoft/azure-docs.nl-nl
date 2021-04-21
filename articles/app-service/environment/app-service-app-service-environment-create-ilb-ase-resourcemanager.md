---
title: ILB ASE v1 maken
description: Maak een App Service omgeving met een interne load balancer (ILB AS-omgeving). Dit document is alleen beschikbaar voor klanten die gebruikmaken van de verouderde v1 ASE.
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: a6cc1cae640b97ecb3d95ee1e4f8ec34750e32d2
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833000"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Een ILB ASE maken met behulp van Azure Resource Manager-sjablonen

> [!NOTE] 
> Dit artikel gaat over de App Service Environment v1. Er is een nieuwere versie van de App Service Environment die eenvoudiger te gebruiken is en wordt uitgevoerd op een krachtigere infrastructuur. Voor meer informatie over de nieuwe versie begint u met [de inleiding tot de App Service Environment](intro.md).
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht
App Service omgevingen kunnen worden gemaakt met een intern adres van een virtueel netwerk in plaats van een openbaar VIP.  Dit interne adres wordt geleverd door een Azure-onderdeel dat de interne load balancer (ILB) wordt genoemd.  Een ILB AS-account kan worden gemaakt met behulp van de Azure Portal.  Het kan ook worden gemaakt met behulp van automatisering door middel van Azure Resource Manager sjablonen.  In dit artikel worden de stappen en syntaxis beschreven die nodig zijn om een ILB AS-Azure Resource Manager maken.

Er zijn drie stappen die nodig zijn voor het automatiseren van het maken van een ILB AS-account:

1. Eerst wordt de basis-AS-omgeving gemaakt in een virtueel netwerk met behulp van een intern load balancer adres in plaats van een openbaar VIP.  Als onderdeel van deze stap wordt een hoofddomeinnaam toegewezen aan de ILB ASE.
2. Zodra de ILB ASE is gemaakt, wordt een TLS/SSL-certificaat geüpload.  
3. Het geüploade TLS/SSL-certificaat wordt expliciet toegewezen aan de ILB ASE als het 'standaard'-TLS/SSL-certificaat.  Dit TLS/SSL-certificaat wordt gebruikt voor TLS-verkeer naar apps op de ILB ASE wanneer de apps worden geadresseerd met behulp van het algemene hoofddomein dat is toegewezen aan de ASE (bijvoorbeeld `https://someapp.mycustomrootcomain.com` )

## <a name="creating-the-base-ilb-ase"></a>De Basis-ILB AS-ruimte maken
Een voorbeeld Azure Resource Manager sjabloon en het bijbehorende parametersbestand zijn hier beschikbaar op [GitHub.][quickstartilbasecreate]

De meeste parameters in de *azuredeploy.parameters.jsbestand zijn* gebruikelijk voor het maken van zowel ILB ASE's als ASE's die zijn gebonden aan een openbaar VIP.  In de onderstaande lijst worden speciale, of unieke parameters genoemd bij het maken van een ILB ASE:

* *internalLoadBalancingMode:* in de meeste gevallen stelt u dit in op 3, wat betekent dat zowel HTTP-/HTTPS-verkeer op poorten 80/443 als de poorten voor besturings-/gegevenskanaal die worden geluisterd door de FTP-service op de ASE, worden gebonden aan een intern adres van een virtueel netwerk dat aan een ILB is toegewezen.  Als deze eigenschap in plaats daarvan is ingesteld op 2, worden alleen de aan ftp-service gerelateerde poorten (zowel besturings- als gegevenskanalen) gebonden aan een ILB-adres, terwijl het HTTP/HTTPS-verkeer op het openbare VIP blijft.
* *dnsSuffix:* met deze parameter definieert u het standaarddomein dat wordt toegewezen aan de ASE.  In de openbare variatie van Azure App Service is het standaard hoofddomein voor alle web-apps *azurewebsites.net*.  Omdat een ILB AS-omgeving echter intern is voor het virtuele netwerk van een klant, is het niet logisch om het standaard hoofddomein van de openbare service te gebruiken.  In plaats daarvan moet een ILB AS-omgeving een standaard hoofddomein hebben dat zinvol is voor gebruik binnen het interne virtuele netwerk van een bedrijf.  Een hypothetische Contoso Corporation kan bijvoorbeeld een standaard hoofddomein van *internal-contoso.com* gebruiken voor apps die alleen kunnen worden opgelost en toegankelijk zijn binnen het virtuele netwerk van Contoso. 
* *ipSslAddressCount:* deze parameter wordt automatisch standaard ingesteld op een waarde van 0 in de *azuredeploy.jsop* bestand omdat ILB AS-adressen slechts één ILB-adres hebben.  Er zijn geen expliciete IP-SSL-adressen voor een ILB ASE. Daarom moet de IP-SSL-adresgroep voor een ILB ASE worden ingesteld op nul, anders treedt er een inrichtingsfout op. 

Zodra de *azuredeploy.parameters.jsin* het bestand is ingevuld voor een ILB ASE, kan de ILB ASE worden gemaakt met behulp van het volgende PowerShell-codefragment.  Wijzig de bestandspaden zo dat deze overeenkomen met Azure Resource Manager sjabloonbestanden zich op uw computer bevinden.  Vergeet ook niet om uw eigen waarden op te geven Azure Resource Manager naam van de implementatie en de naam van de resourcegroep.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Nadat de Azure Resource Manager is verzonden, duurt het enkele uren voordat de ILB ASE is gemaakt.  Zodra het maken is voltooid, wordt de ILB AS-omgeving weergegeven in de portal-UX in de lijst met App Service Environments voor het abonnement dat de implementatie heeft geactiveerd.

## <a name="uploading-and-configuring-the-default-tlsssl-certificate"></a>Het standaard-TLS/SSL-certificaat uploaden en configureren
Zodra de ILB ASE is gemaakt, moet er een TLS/SSL-certificaat aan de ASE worden gekoppeld als het 'standaard' TLS/SSL-certificaat dat wordt gebruikt voor het tot stand brengen van TLS/SSL-verbindingen met apps.  Als het standaard-DNS-achtervoegsel van de ASE *internal-contoso.com* is, is voor een verbinding naar een TLS/SSL-certificaat vereist dat geldig is voor *`https://some-random-app.internal-contoso.com`* **.internal-contoso.com*. 

Er zijn verschillende manieren om een geldig TLS/SSL-certificaat te verkrijgen, waaronder interne CERTIFICERINGen, het kopen van een certificaat van een externe verlener en het gebruik van een zelf-ondertekend certificaat.  Ongeacht de bron van het TLS/SSL-certificaat, moeten de volgende certificaatkenmerken correct worden geconfigureerd:

* *Onderwerp:* Dit kenmerk moet worden ingesteld op **.your-root-domain-here.com*
* *Alternatieve onderwerpnaam:* dit kenmerk moet zowel **.your-root-domain-here.com*, als **.scm.your-root-domain-here.com.*  De tweede vermelding is dat TLS-verbindingen met de SCM/Kudu-site die aan elke app is gekoppeld, worden gemaakt met behulp van een adres van het *formulier your-app-name.scm.your-root-domain-here.com*.

Als u een geldig TLS/SSL-certificaat hebt, zijn er twee aanvullende voorbereidende stappen nodig.  Het TLS/SSL-certificaat moet worden geconverteerd/opgeslagen als een PFX-bestand.  Vergeet niet dat het PFX-bestand alle tussenliggende en basiscertificaten moet bevatten en ook moet worden beveiligd met een wachtwoord.

Vervolgens moet het pfx-bestand worden geconverteerd naar een base64-tekenreeks, omdat het TLS/SSL-certificaat wordt geüpload met behulp van een Azure Resource Manager sjabloon.  Omdat Azure Resource Manager tekstbestanden zijn, moet het PFX-bestand worden geconverteerd naar een base64-tekenreeks, zodat het kan worden opgenomen als een parameter van de sjabloon.

Het onderstaande PowerShell-codefragment toont een voorbeeld van het genereren van een zelfonderschreven certificaat, het exporteren van het certificaat als een PFX-bestand, het converteren van het PFX-bestand naar een base64-gecodeerde tekenreeks en het opslaan van de met Base64 gecodeerde tekenreeks naar een afzonderlijk bestand.  De PowerShell-code voor base64-codering is aangepast aan de [PowerShell-scriptsblog][examplebase64encoding].

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

Zodra het TLS/SSL-certificaat is gegenereerd en geconverteerd naar een base64-gecodeerde tekenreeks, kan het voorbeeld van een Azure Resource Manager-sjabloon op GitHub voor het configureren van het [standaard-TLS/SSL-certificaat][configuringDefaultSSLCertificate] worden gebruikt.

De parameters in het *azuredeploy.parameters.jsbestand* worden hieronder weergegeven:

* *appServiceEnvironmentName:* de naam van de ILB AS-omgeving die wordt geconfigureerd.
* *existingAseLocation:* teksttekenreeks met de Azure-regio waar de ILB AS-omgeving is geïmplementeerd.  Bijvoorbeeld: VS - zuid-centraal.
* *pfxBlobString:* de met based64 gecodeerde tekenreeksweergave van het PFX-bestand.  Met behulp van het codefragment dat eerder is weergegeven, kopieert u de tekenreeks in exportedcert.pfx.b64 en plakt u deze als de waarde van het *kenmerk pfxBlobString.*
* *wachtwoord:* het wachtwoord dat wordt gebruikt om het PFX-bestand te beveiligen.
* *certificateThumbprint:* de vingerafdruk van het certificaat.  Als u deze waarde op haalt uit PowerShell (bijvoorbeeld *$certificate. Vingerafdruk van* het eerdere codefragment) kunt u de waarde as-is gebruiken.  Als u de waarde uit het dialoogvenster Windows-certificaat kopieert, moet u de overbodige spaties echter wel uit de weg halen.  De *certificateThumbprint moet* er ongeveer uitzien als: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName:* een gebruiksvriendelijke tekenreeks-id van uw eigen keuze die wordt gebruikt om het certificaat te identiteiten.  De naam wordt gebruikt als onderdeel van de unieke Azure Resource Manager voor de entiteit *Microsoft.Web/certificates* die het TLS/SSL-certificaat vertegenwoordigt.  De naam **moet** eindigen met het volgende achtervoegsel:  \_ yourASENameHere_InternalLoadBalancingASE.  Dit achtervoegsel wordt door de portal gebruikt als indicator dat het certificaat wordt gebruikt voor het beveiligen van een AS-E met ILB-functie.

Hieronder wordt een verkort voorbeeld van *azuredeploy.parameters.jsweergegeven:*

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

Zodra de *azuredeploy.parameters.jsbestand* is ingevuld, kan het standaard-TLS/SSL-certificaat worden geconfigureerd met behulp van het volgende PowerShell-codefragment.  Wijzig de bestandspaden zo dat deze overeenkomen met Azure Resource Manager sjabloonbestanden zich op uw computer bevinden.  Vergeet ook niet om uw eigen waarden op te geven Azure Resource Manager naam van de implementatie en de naam van de resourcegroep.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Nadat de Azure Resource Manager is verzonden, duurt het ongeveer 15 minuten per ASE-front-end om de wijziging toe te passen.  Als een AS-omgeving met een standaardgrootte bijvoorbeeld twee front-ends gebruikt, duurt het ongeveer één uur en twintig minuten om de sjabloon te voltooien.  Terwijl de sjabloon wordt uitgevoerd, kan de ASE niet worden geschaald.  

Zodra de sjabloon is voltooid, zijn apps op de ILB AS-omgeving toegankelijk via HTTPS en worden de verbindingen beveiligd met het standaard-TLS/SSL-certificaat.  Het standaard-TLS/SSL-certificaat wordt gebruikt wanneer apps op de ILB ASE worden geadresseerd met behulp van een combinatie van de toepassingsnaam plus de standaardhostnaam.  Gebruik bijvoorbeeld *`https://mycustomapp.internal-contoso.com`* het standaard-TLS/SSL-certificaat voor **.internal-contoso.com*.

Ontwikkelaars kunnen echter, net als apps die worden uitgevoerd op de openbare service met meerdere tenants, ook aangepaste hostnamen voor afzonderlijke apps configureren en vervolgens unieke SNI TLS/SSL-certificaatbindingen configureren voor afzonderlijke apps.  

## <a name="getting-started"></a>Aan de slag
Zie Inleiding tot App Service om aan de slag te [gaan met App Service Environment](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

