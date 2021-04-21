---
title: TLS met sidecar-container inschakelen
description: Maak een SSL- of TLS-eindpunt voor een containergroep die wordt uitgevoerd in Azure Container Instances door Nginx uit te uitvoeren in een sidecar-container
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 906a1f239d7050ea17fd7d1425138049ebf045c1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790955"
---
# <a name="enable-a-tls-endpoint-in-a-sidecar-container"></a>Een TLS-eindpunt inschakelen in een sidecar-container

In dit artikel wordt beschreven hoe u een [containergroep maakt](container-instances-container-groups.md) met een toepassingscontainer en een sidecar-container waarop een TLS/SSL-provider wordt uitgevoerd. Door een containergroep met een afzonderlijk TLS-eindpunt in te stellen, kunt u TLS-verbindingen voor uw toepassing inschakelen zonder uw toepassingscode te wijzigen.

U stelt een voorbeeldcontainergroep in die bestaat uit twee containers:
* Een toepassingscontainer waarmee een eenvoudige web-app wordt uitgevoerd met behulp van de openbare Microsoft [aci-helloworld-afbeelding.](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) 
* Een sidecar-container met de openbare [Nginx-afbeelding,](https://hub.docker.com/_/nginx) geconfigureerd voor het gebruik van TLS. 

In dit voorbeeld maakt de containergroep alleen poort 443 beschikbaar voor Nginx met het openbare IP-adres. Nginx routeer HTTPS-aanvragen naar de begeleidende web-app, die intern luistert op poort 80. U kunt het voorbeeld aanpassen voor container-apps die op andere poorten luisteren. 

Zie [Volgende stappen voor](#next-steps) andere methoden voor het inschakelen van TLS in een containergroep.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.55 of hoger van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

Als u Nginx als TLS-provider wilt instellen, hebt u een TLS/SSL-certificaat nodig. In dit artikel wordt beschreven hoe u een zelf-ondertekend TLS/SSL-certificaat kunt maken en instellen. Voor productiescenario's moet u een certificaat verkrijgen van een certificeringsinstantie.

Als u een zelf-ondertekend TLS/SSL-certificaat wilt maken, gebruikt u het [OpenSSL-hulpprogramma](https://www.openssl.org/) dat beschikbaar is in Azure Cloud Shell en veel Linux-distributies, of gebruikt u een vergelijkbaar clienthulpprogramma in uw besturingssysteem.

Maak eerst een certificaataanvraag (CSR-bestand) in een lokale map:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Volg de aanwijzingen om de identificatiegegevens toe te voegen. Voer bij Algemene naam de hostnaam in die aan het certificaat is gekoppeld. Wanneer u wordt gevraagd om een wachtwoord, drukt u op Enter zonder te typen om het toevoegen van een wachtwoord over te slaan.

Voer de volgende opdracht uit om het zelf-ondertekende certificaat (CRT-bestand) van de certificaataanvraag te maken. Bijvoorbeeld:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

U ziet nu drie bestanden in de map: de certificaataanvraag ( ), de persoonlijke sleutel ( ) en het `ssl.csr` `ssl.key` zelf-ondertekende certificaat ( `ssl.crt` ). U gebruikt `ssl.key` en `ssl.crt` in latere stappen.

## <a name="configure-nginx-to-use-tls"></a>Nginx configureren voor het gebruik van TLS

### <a name="create-nginx-configuration-file"></a>Nginx-configuratiebestand maken

In deze sectie maakt u een configuratiebestand voor Nginx om TLS te gebruiken. Begin met het kopiëren van de volgende tekst naar een nieuw bestand met de naam `nginx.conf` . In Azure Cloud Shell kunt u Visual Studio Code gebruiken om het bestand in uw werkmap te maken:

```console
code nginx.conf
```

In `location` moet u instellen met de juiste poort voor uw `proxy_pass` app. In dit voorbeeld stellen we poort 80 in voor de `aci-helloworld` container.

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize TLS/SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive TLS/SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Base64-codering van geheimen en configuratiebestand

Base64-codering van het Nginx-configuratiebestand, het TLS/SSL-certificaat en de TLS-sleutel. In de volgende sectie voert u de gecodeerde inhoud in een YAML-bestand in dat wordt gebruikt om de containergroep te implementeren.

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Containergroep implementeren

Implementeer nu de containergroep door de containerconfiguraties op te geven in een [YAML-bestand](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>YAML-bestand maken

Kopieer de volgende YAML naar een nieuw bestand met de naam `deploy-aci.yaml` . In Azure Cloud Shell kunt u Visual Studio Code gebruiken om het bestand in uw werkmap te maken:

```console
code deploy-aci.yaml
```

Voer de inhoud van de met Base64 gecodeerde bestanden in, zoals aangegeven onder `secret` . Bijvoorbeeld elk `cat` van de base64-gecodeerde bestanden om de inhoud ervan te bekijken. Tijdens de implementatie worden deze bestanden toegevoegd aan een [geheim volume](container-instances-volume-secret.md) in de containergroep. In dit voorbeeld wordt het geheime volume aan de Nginx-container bevestigd.

```YAML
api-version: 2019-12-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>De containergroep implementeren

Maak een resourcegroep met de [opdracht az group create:](/cli/azure/group#az_group_create)

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Implementeer de containergroep met de [opdracht az container create,](/cli/azure/container#az_container_create) en door het YAML-bestand door te geven als argument.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Implementatietoestand weergeven

Als u de status van de implementatie wilt weergeven, gebruikt u de [volgende opdracht az container show:](/cli/azure/container#az_container_show)

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

Voor een geslaagde implementatie is de uitvoer vergelijkbaar met het volgende:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   nginx, mcr.microsoft.com/azuredocs/aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-tls-connection"></a>TLS-verbinding controleren

Gebruik uw browser om naar het openbare IP-adres van de containergroep te navigeren. Het IP-adres dat in dit voorbeeld wordt weergegeven, is `52.157.22.76` , dus de URL is **https://52.157.22.76** . U moet HTTPS gebruiken om de toepassing te zien die wordt uitgevoerd vanwege de Nginx-serverconfiguratie. Pogingen om verbinding te maken via HTTP mislukken.

![Schermafbeelding van browser met toepassing die wordt uitgevoerd in een exemplaar van een Azure-container](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Omdat in dit voorbeeld een zelf-ondertekend certificaat wordt gebruikt en niet een van een certificeringsinstantie, geeft de browser een beveiligingswaarschuwing weer wanneer u verbinding maakt met de site via HTTPS. Mogelijk moet u de waarschuwing accepteren of de browser- of certificaatinstellingen aanpassen om door te gaan naar de pagina. Dit gedrag is verwacht.

>

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u kunnen lezen hoe u een Nginx-container in kunt stellen om TLS-verbindingen in te stellen met een web-app die wordt uitgevoerd in de containergroep. U kunt dit voorbeeld aanpassen voor apps die luisteren op andere poorten dan poort 80. U kunt ook het Nginx-configuratiebestand bijwerken om serververbindingen op poort 80 (HTTP) automatisch om te leiden om HTTPS te gebruiken.

Hoewel in dit artikel Nginx in de sidecar wordt gebruikt, kunt u een andere TLS-provider gebruiken, zoals [Caddy.](https://caddyserver.com/)

Als u uw containergroep in een virtueel [Azure-netwerk](container-instances-vnet.md)implementeert, kunt u andere opties overwegen om een TLS-eindpunt in te stellen voor een back-endcontainer-instantie, waaronder:

* [Azure Functions-proxy's](../azure-functions/functions-proxies.md)
* [Azure API Management](../api-management/api-management-key-concepts.md)
* [Azure Application Gateway](../application-gateway/overview.md) voorbeeldimplementatiesjabloon [.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet)
