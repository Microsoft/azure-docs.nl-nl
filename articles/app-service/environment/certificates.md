---
title: Certificaten bindingen
description: Geef een beschrijving van talloze onderwerpen met betrekking tot certificaten op een App Service Environment. Meer informatie over hoe certificaat bindingen werken met de apps met één Tenant in een ASE.
author: ccompy
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 306445e26e5b236b49273b9ab8888ecc610bc075
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88962040"
---
# <a name="certificates-and-the-app-service-environment"></a>Certificaten en de App Service Environment 

De App Service Environment (ASE) is een implementatie van de Azure App Service die wordt uitgevoerd binnen uw Azure-Virtual Network (VNet). Het kan worden geïmplementeerd met een eind punt voor Internet toegang of een toepassings eindpunt dat zich in uw VNet bevindt. Als u de ASE implementeert met een Internet toegankelijk eind punt, wordt deze implementatie een externe ASE genoemd. Als u de ASE implementeert met een eind punt in uw VNet, wordt die implementatie een ILB-ASE genoemd. Meer informatie over de ILB-ASE vindt u in het document [Create and use a ILB ASE](./create-ilb-ase.md) .

De ASE is één Tenant systeem. Omdat het één Tenant is, zijn er enkele functies die alleen beschikbaar zijn voor een ASE die niet beschikbaar zijn in de multi tenant-App Service. 

## <a name="ilb-ase-certificates"></a>ILB ASE-certificaten 

Als u een externe ASE gebruikt, zijn uw apps bereikbaar op [appName]. [asename]. p. azurewebsites. net. Standaard worden alle as, zelfs ILB as, gemaakt met certificaten die de volgende indeling hebben. Wanneer u een ILB-ASE hebt, worden de apps bereikt op basis van de domein naam die u opgeeft bij het maken van de ILB ASE. Om ervoor te zorgen dat de apps TLS ondersteunen, moet u certificaten uploaden. Verkrijg een geldig TLS/SSL-certificaat door interne certificerings instanties te kopen, een certificaat aan te schaffen bij een externe verlener of een zelfondertekend certificaat te gebruiken. 

Er zijn twee opties voor het configureren van certificaten met uw ILB-ASE.  U kunt een standaard certificaat voor joker tekens voor de ILB-ASE instellen of certificaten instellen voor de afzonderlijke web-apps in de ASE.  Ongeacht de keuze die u hebt gemaakt, moeten de volgende certificaat kenmerken correct worden geconfigureerd:

- **Onderwerp:** Dit kenmerk moet worden ingesteld op *. [uw-root-hier] voor een Joker teken ILB ASE-certificaat. Als u het certificaat voor uw app maakt, moet dit [appName] zijn. [uw-root-hier]
- **Alternatieve naam voor onderwerp:** Dit kenmerk moet beide * bevatten. [uw-root-hier] en *. scm. [uw-root-hier] voor het Joker teken ILB ASE-certificaat. Als u het certificaat voor uw app maakt, moet dit [appName] zijn. [uw-root-hier] en [appName]. scm. [uw-root-hier].

Als derde variant kunt u een ILB ASE-certificaat maken dat alle afzonderlijke app-namen bevat in het SAN van het certificaat in plaats van een verwijzing naar een Joker teken. Het probleem met deze methode is dat u de namen van de apps die u in de ASE plaatst, moet weten, of dat u het ILB ASE-certificaat moet blijven bijwerken.

### <a name="upload-certificate-to-ilb-ase"></a>Certificaat uploaden naar ILB ASE 

Nadat een ILB-ASE is gemaakt in de portal, moet het certificaat worden ingesteld voor de ILB ASE. Totdat het certificaat is ingesteld, wordt in de ASE een banner weer gegeven dat het certificaat niet is ingesteld.  

Het certificaat dat u uploadt, moet een. pfx-bestand zijn. Nadat het certificaat is geüpload, wordt door de ASE een schaal bewerking uitgevoerd om het certificaat in te stellen. 

U kunt de ASE niet maken en het certificaat uploaden als één actie in de portal of zelfs in een sjabloon. Als afzonderlijke actie kunt u het certificaat uploaden met behulp van een sjabloon zoals beschreven in het document [een ASE maken op basis van een sjabloon](./create-from-template.md) .  

Als u een zelf ondertekend certificaat snel wilt maken voor het testen, kunt u de volgende bit van Power shell gebruiken:

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password
```

Wanneer u een zelfondertekend certificaat maakt, moet u ervoor zorgen dat de onderwerpnaam de indeling CN = {ASE_NAME_HERE} _InternalLoadBalancingASE heeft.

## <a name="application-certificates"></a>Toepassings certificaten 

Apps die worden gehost in een ASE kunnen gebruikmaken van de app-gerichte certificaat functies die beschikbaar zijn in de multi tenant-App Service. Deze functies omvatten:  

- Certificaten SNI 
- Op IP gebaseerde SSL, die alleen wordt ondersteund met een externe ASE.  Een ILB-ASE biedt geen ondersteuning voor SSL op basis van IP.
- Gehoste certificaten voor de sleutel kluis 

De instructies voor het uploaden en beheren van deze certificaten zijn beschikbaar in [een TLS/SSL-certificaat toevoegen in azure app service](../configure-ssl-certificate.md).  Als u alleen certificaten configureert die overeenkomen met een aangepaste domein naam die u hebt toegewezen aan uw web-app, zijn deze instructies voldoende. Als u het certificaat voor een ILB ASE-web-app uploadt met de naam van het standaard domein, geeft u de SCM-site op in het SAN van het certificaat zoals eerder is aangegeven. 

## <a name="tls-settings"></a>TLS-instellingen 

U kunt de TLS-instelling op het niveau van de app configureren.  

## <a name="private-client-certificate"></a>Persoonlijk client certificaat 

Een veelvoorkomend gebruiks voorbeeld is het configureren van uw app als een client in een client-server model. Als u uw server met een persoonlijk CA-certificaat beveiligt, moet u het client certificaat uploaden naar uw app.  Met de volgende instructies worden certificaten geladen in de truststore van de werk rollen waarop uw app wordt uitgevoerd. Als u het certificaat in één app laadt, kunt u het gebruiken met uw andere apps in hetzelfde App Service plan zonder het certificaat opnieuw te uploaden.

Upload het certificaat naar uw app in uw ASE:

1. Genereer een *CER* -bestand voor uw certificaat. 
2. Ga naar de app waarvoor het certificaat is vereist in de Azure Portal
3. Ga naar SSL-instellingen in de app. Klik op Certificaat uploaden. Selecteer Openbaar. Selecteer lokale machine. Geef een naam op. Blader en selecteer uw *CER* -bestand. Selecteer uploaden. 
4. Kopieer de vinger afdruk.
5. Ga naar toepassings instellingen. Maak een app-instelling WEBSITE_LOAD_ROOT_CERTIFICATES met de vinger afdruk als waarde. Als u meerdere certificaten hebt, kunt u deze in dezelfde instelling plaatsen, gescheiden door komma's, en geen witruimte als 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

Het certificaat is beschikbaar voor alle apps in hetzelfde app service-plan als de app die deze instelling heeft geconfigureerd. Als u wilt dat de app beschikbaar is voor apps in een ander App Service-abonnement, moet u de bewerking voor het instellen van apps in een app in dat App Service plan herhalen. Als u wilt controleren of het certificaat is ingesteld, gaat u naar de kudu-console en geeft u de volgende opdracht op in de Power shell-console voor fout opsporing:

```azurepowershell-interactive
dir cert:\localmachine\root
```

Als u tests wilt uitvoeren, kunt u een zelfondertekend certificaat maken en een *. CER* -bestand genereren met de volgende Power shell: 

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.cer"
export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT
```