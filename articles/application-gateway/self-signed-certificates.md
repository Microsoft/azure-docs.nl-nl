---
title: Zelfondertekend certificaat genereren met een aangepaste basis-CA
titleSuffix: Azure Application Gateway
description: Meer informatie over het genereren van een zelfondertekend certificaat van Azure-toepassing gateway met een aangepaste basis certificerings instantie
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: e60aa9f072a447af97aa7cc66534e6e893fdbcf6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93396937"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Een zelfondertekend certificaat van Azure Application Gateway genereren met een aangepast basiscertificaat

Met de SKU van Application Gateway v2 wordt het gebruik van vertrouwde basis certificaten geïntroduceerd voor het toestaan van back-endservers. Hiermee worden verificatie certificaten verwijderd die vereist waren in de V1-SKU. Het *basis certificaat* is een Base-64 Encoded X. 509 (. CER) basis certificaat van de back-end-certificaat server indelen. Het identificeert de basis certificerings instantie (CA) die het server certificaat heeft uitgegeven en het server certificaat wordt vervolgens gebruikt voor de TLS/SSL-communicatie.

Application Gateway vertrouwt het certificaat van uw website standaard als dit is ondertekend door een bekende certificerings instantie (bijvoorbeeld GoDaddy of DigiCert). U hoeft het basis certificaat niet expliciet in dat geval te uploaden. Zie [overzicht van TLS-beëindiging en end-to-end-TLS met Application Gateway](ssl-overview.md)voor meer informatie. Als u echter een ontwikkel-en test omgeving hebt en geen geverifieerde certificerings instantie wilt aanschaffen, kunt u uw eigen aangepaste CA maken en een zelfondertekend certificaat maken met het certificaat. 

> [!NOTE]
> Zelfondertekende certificaten worden niet standaard vertrouwd en kunnen lastig zijn om te onderhouden. Ze kunnen ook verouderde hash-en coderings suites gebruiken die mogelijk niet sterk zijn. Voor betere beveiliging koopt u een certificaat dat is ondertekend door een bekende certificerings instantie.

In dit artikel leert u het volgende:

- Uw eigen aangepaste certificerings instantie maken
- Een zelfondertekend certificaat maken dat is ondertekend door uw aangepaste certificerings instantie
- Een zelfondertekend basis certificaat uploaden naar een Application Gateway voor het verifiëren van de back-end-server

## <a name="prerequisites"></a>Vereisten

- **[Openssl](https://www.openssl.org/) op een computer met Windows of Linux** 

   Er kunnen andere hulpprogram ma's beschikbaar zijn voor certificaat beheer, maar in deze zelf studie wordt gebruikgemaakt van OpenSSL. U kunt OpenSSL bundelen met veel Linux-distributies, zoals Ubuntu.
- **Een webserver**

   Bijvoorbeeld Apache, IIS of NGINX om de certificaten te testen.

- **Een SKU van Application Gateway v2**
   
  Als u geen bestaande toepassings gateway hebt, raadpleegt u [Quick Start: direct Web Traffic with Azure-toepassing gateway-Azure Portal](quick-create-portal.md).

## <a name="create-a-root-ca-certificate"></a>Een basis-CA-certificaat maken

Maak uw basis-CA-certificaat met behulp van OpenSSL.

### <a name="create-the-root-key"></a>De basis sleutel maken

1. Meld u aan bij de computer waarop OpenSSL is geïnstalleerd en voer de volgende opdracht uit. Hiermee maakt u een met een wacht woord beveiligde sleutel.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. Typ bij de prompt een sterk wacht woord. Bijvoorbeeld ten minste negen tekens, met behulp van hoofd letters, kleine letters, cijfers en symbolen.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Een basis certificaat maken en zelf ondertekenen

1. Gebruik de volgende opdrachten om de CSR en het certificaat te genereren.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   Met de vorige opdrachten maakt u het basis certificaat. U gebruikt deze om uw server certificaat te ondertekenen.

1. Wanneer u hierom wordt gevraagd, typt u het wacht woord voor de hoofd sleutel en de organisatie gegevens voor de aangepaste certificerings instantie, zoals land/regio, staat, organisatie, OE en de Fully Qualified Domain Name (dit is het domein van de uitgever).

   ![basis certificaat maken](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Een server certificaat maken

Vervolgens maakt u een server certificaat met behulp van OpenSSL.

### <a name="create-the-certificates-key"></a>De sleutel van het certificaat maken

Gebruik de volgende opdracht om de sleutel voor het server certificaat te genereren.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>De CSR maken (aanvraag voor certificaat ondertekening)

De CSR is een open bare sleutel die aan een CA wordt toegewezen bij het aanvragen van een certificaat. De CA geeft het certificaat voor deze specifieke aanvraag uit.

> [!NOTE]
> De CN (common name) voor het server certificaat moet verschillen van het domein van de verlener. In dit geval is de CN voor de verlener `www.contoso.com` en de CN van het server certificaat `www.fabrikam.com` .


1. Gebruik de volgende opdracht om de CSR te genereren:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. Wanneer u hierom wordt gevraagd, typt u het wacht woord voor de hoofd sleutel en de organisatie gegevens voor de aangepaste CA: land/regio, staat, organisatie, OE en de Fully Qualified Domain Name. Dit is het domein van de website en het moet afwijken van de verlener.

   ![Server certificaat](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>Genereer het certificaat met de CSR en de sleutel en onderteken het met de basis sleutel van de CA

1. Gebruik de volgende opdracht om het certificaat te maken:

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>Het zojuist gemaakte certificaat verifiëren

1. Gebruik de volgende opdracht om de uitvoer van het CRT-bestand af te drukken en de inhoud ervan te controleren:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![Certificaat verificatie](media/self-signed-certificates/verify-cert.png)

1. Controleer de bestanden in uw map en zorg ervoor dat u de volgende bestanden hebt:

   - contoso. CRT
   - contoso. key
   - fabrikam. CRT
   - fabrikam. key

## <a name="configure-the-certificate-in-your-web-servers-tls-settings"></a>Het certificaat in de TLS-instellingen van uw webserver configureren

Configureer op uw webserver TLS met de bestanden fabrikam. CRT en fabrikam. key. Als uw webserver geen twee bestanden kan gebruiken, kunt u deze combi neren in één. pem-of. pfx-bestand met behulp van OpenSSL-opdrachten.

### <a name="iis"></a>IIS

Zie [procedure: geïmporteerde certificaten installeren op een webserver in Windows server 2003](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server)voor instructies over het importeren van een certificaat en het uploaden ervan als server certificaat in IIS.

Zie [SSL instellen in IIS 7](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1)voor instructies voor TLS-binding.

### <a name="apache"></a>Apache

De volgende configuratie is een voor beeld [van een virtuele host die is geconfigureerd voor SSL](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) in Apache:

```
<VirtualHost www.fabrikam:443>
      DocumentRoot /var/www/fabrikam
      ServerName www.fabrikam.com
      SSLEngine on
      SSLCertificateFile /home/user/fabrikam.crt
      SSLCertificateKeyFile /home/user/fabrikam.key
</VirtualHost>
```

### <a name="nginx"></a>NGINX

De volgende configuratie is een voor beeld van een [NGINX-server blok](https://nginx.org/docs/http/configuring_https_servers.html) met TLS-configuratie:

![NGINX met TLS](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>Toegang tot de server om de configuratie te controleren

1. Voeg het basis certificaat toe aan het vertrouwde basis archief van de computer. Wanneer u de website opent, moet u ervoor zorgen dat de volledige certificaat keten wordt weer gegeven in de browser.

   ![Vertrouwde basiscertificaten](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Er wordt van uitgegaan dat DNS is geconfigureerd om de naam van de webserver (in dit voor beeld www.fabrikam.com) te laten wijzen aan het IP-adres van uw webserver. Als dat niet het geval is, kunt u het [bestand hosts](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) bewerken om de naam op te lossen.
1. Blader naar uw website en klik op het vergrendelings pictogram in het adresvak van uw browser om de site-en certificaat gegevens te controleren.

## <a name="verify-the-configuration-with-openssl"></a>De configuratie controleren met OpenSSL

U kunt ook OpenSSL gebruiken om het certificaat te verifiëren.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![OpenSSL-certificaat verificatie](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>Het basis certificaat uploaden naar de HTTP-instellingen van de Application Gateway

Als u het certificaat in Application Gateway wilt uploaden, moet u het. CRT-certificaat exporteren naar een. CER-indeling basis-64-code ring. Omdat CRT de open bare sleutel in de indeling base-64 bevat, kunt u de bestands extensie alleen wijzigen van. CRT naar. cer. 

### <a name="azure-portal"></a>Azure Portal

Als u het vertrouwde basis certificaat wilt uploaden vanuit de portal, selecteert u de **http-instellingen** en kiest u het **https** -protocol.

![Een certificaat toevoegen met behulp van de portal](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

U kunt ook Azure CLI of Azure PowerShell gebruiken om het basis certificaat te uploaden. De volgende code is een Azure PowerShell-voor beeld.

> [!NOTE]
> In het volgende voor beeld wordt een vertrouwd basis certificaat toegevoegd aan de toepassings gateway, wordt een nieuwe HTTP-instelling gemaakt en een nieuwe regel toegevoegd, ervan uitgaande dat de back-end-pool en de listener al bestaan.

```azurepowershell
## Add the trusted root certificate to the Application Gateway

$gw=Get-AzApplicationGateway -Name appgwv2 -ResourceGroupName rgOne

Add-AzApplicationGatewayTrustedRootCertificate `
   -ApplicationGateway $gw `
   -Name CustomCARoot `
   -CertificateFile "C:\Users\surmb\Downloads\contoso.cer"

$trustedroot = Get-AzApplicationGatewayTrustedRootCertificate `
   -Name CustomCARoot `
   -ApplicationGateway $gw

## Get the listener, backend pool and probe

$listener = Get-AzApplicationGatewayHttpListener `
   -Name basichttps `
   -ApplicationGateway $gw

$bepool = Get-AzApplicationGatewayBackendAddressPool `
  -Name testbackendpool `
  -ApplicationGateway $gw

Add-AzApplicationGatewayProbeConfig `
  -ApplicationGateway $gw `
  -Name testprobe `
  -Protocol Https `
  -HostName "www.fabrikam.com" `
  -Path "/" `
  -Interval 15 `
  -Timeout 20 `
  -UnhealthyThreshold 3

$probe = Get-AzApplicationGatewayProbeConfig `
  -Name testprobe `
  -ApplicationGateway $gw

## Add the configuration to the HTTP Setting and don't forget to set the "hostname" field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server's certificate. Note that TLS handshake will
## fail otherwise and might lead to backend servers being deemed as Unhealthy by the probes

Add-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $gw `
  -Name testbackend `
  -Port 443 `
  -Protocol Https `
  -Probe $probe `
  -TrustedRootCertificate $trustedroot `
  -CookieBasedAffinity Disabled `
  -RequestTimeout 20 `
  -HostName www.fabrikam.com

## Get the configuration and update the Application Gateway

$backendhttp = Get-AzApplicationGatewayBackendHttpSettings `
  -Name testbackend `
  -ApplicationGateway $gw

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $gw `
  -Name testrule `
  -RuleType Basic `
  -BackendHttpSettings $backendhttp `
  -HttpListener $listener `
  -BackendAddressPool $bepool

Set-AzApplicationGateway -ApplicationGateway $gw 
```

### <a name="verify-the-application-gateway-backend-health"></a>De status van de back-end van de toepassings Gateway controleren

1. Klik op de weer gave **back-end** van uw toepassings gateway om te controleren of de test in orde is.
1. U ziet dat de status **in orde** is voor de https-test.

![HTTPS-test](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van TLS-beëindiging en end-to-end-TLS met Application Gateway](ssl-overview.md)voor meer informatie over SSL\TLS in Application Gateway.