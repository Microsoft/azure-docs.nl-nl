---
title: TLS inschakelen met de container zijspan
description: Een SSL-of TLS-eind punt maken voor een container groep die wordt uitgevoerd in Azure Container Instances door nginx uit te voeren in een zijspan container
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 6587a84e7cbe655c509f74e9e39e93010e7058be
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558076"
---
# <a name="enable-a-tls-endpoint-in-a-sidecar-container"></a>Een TLS-eind punt inschakelen in een zijspan wagen

In dit artikel wordt beschreven hoe u een [container groep](container-instances-container-groups.md) maakt met een toepassings container en een container voor samen voeging met een TLS/SSL-provider. Door een container groep met een afzonderlijk TLS-eind punt in te stellen, schakelt u TLS-verbindingen voor uw toepassing in zonder de toepassings code te wijzigen.

U stelt een voorbeeld container groep in die bestaat uit twee containers:
* Een toepassings container die een eenvoudige web-app uitvoert met behulp van de open bare installatie kopie van micro soft [ACI-HelloWorld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) . 
* Een zijspan container waarop de open bare [nginx](https://hub.docker.com/_/nginx) -installatie kopie wordt uitgevoerd en die is geconfigureerd voor het gebruik van TLS. 

In dit voor beeld heeft de container groep alleen poort 443 voor nginx met het open bare IP-adres. Nginx stuurt HTTPS-aanvragen naar de Companion-web-app die intern luistert op poort 80. U kunt het voor beeld aanpassen voor container-apps die Luis teren op andere poorten. 

Zie de [volgende stappen](#next-steps) voor andere benaderingen van het inschakelen van TLS in een container groep.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.55 of hoger van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

U hebt een TLS/SSL-certificaat nodig om nginx in te stellen als een TLS-provider. In dit artikel wordt beschreven hoe u een zelfondertekend TLS/SSL-certificaat maakt en instelt. Voor productie scenario's moet u een certificaat van een certificerings instantie verkrijgen.

Als u een zelfondertekend TLS/SSL-certificaat wilt maken, gebruikt u het [openssl](https://www.openssl.org/) -hulp programma dat beschikbaar is in azure Cloud shell en veel Linux-distributies, of gebruikt u een vergelijkbaar client hulpprogramma in uw besturings systeem.

Maak eerst een certificaat aanvraag (. CSR-bestand) in een lokale werkmap:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Volg de aanwijzingen om de identificatie gegevens toe te voegen. Voer bij algemene naam de hostnaam in die is gekoppeld aan het certificaat. Wanneer u wordt gevraagd om een wacht woord, drukt u op Enter zonder te typen, om het toevoegen van een wacht woord over te slaan.

Voer de volgende opdracht uit om het zelfondertekende certificaat (. crt-bestand) te maken op basis van de certificaat aanvraag. Bijvoorbeeld:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

U ziet nu drie bestanden in de map: de certificaat aanvraag ( `ssl.csr` ), de persoonlijke sleutel () `ssl.key` en het zelfondertekende certificaat ( `ssl.crt` ). U gebruikt `ssl.key` en `ssl.crt` in latere stappen.

## <a name="configure-nginx-to-use-tls"></a>Nginx configureren voor het gebruik van TLS

### <a name="create-nginx-configuration-file"></a>Nginx-configuratie bestand maken

In deze sectie maakt u een configuratie bestand voor nginx om TLS te gebruiken. Begin met het kopiëren van de volgende tekst in een nieuw bestand met de naam `nginx.conf` . In Azure Cloud Shell kunt u Visual Studio code gebruiken om het bestand in uw werkmap te maken:

```console
code nginx.conf
```

In `location` , moet u ervoor zorgen dat u `proxy_pass` de juiste poort voor uw app hebt ingesteld. In dit voor beeld stellen we poort 80 in voor de `aci-helloworld` container.

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

### <a name="base64-encode-secrets-and-configuration-file"></a>Base64-versleutelings geheimen en configuratie bestand

Base64: het nginx-configuratie bestand, het TLS/SSL-certificaat en de TLS-sleutel coderen. In de volgende sectie voert u de versleutelde inhoud in een YAML-bestand in die wordt gebruikt voor het implementeren van de container groep.

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Container groep implementeren

Implementeer nu de container groep door de container configuraties in een [yaml-bestand](container-instances-multi-container-yaml.md)op te geven.

### <a name="create-yaml-file"></a>YAML-bestand maken

Kopieer de volgende YAML naar een nieuw bestand met de naam `deploy-aci.yaml` . In Azure Cloud Shell kunt u Visual Studio code gebruiken om het bestand in uw werkmap te maken:

```console
code deploy-aci.yaml
```

Voer de inhoud van de met base64 gecodeerde bestanden in, zoals aangegeven onder `secret` . Bijvoorbeeld `cat` elk van de met base64 gecodeerde bestanden om de inhoud ervan weer te geven. Tijdens de implementatie worden deze bestanden toegevoegd aan een [geheim volume](container-instances-volume-secret.md) in de container groep. In dit voor beeld wordt het geheime volume gekoppeld aan de nginx-container.

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

### <a name="deploy-the-container-group"></a>De container groep implementeren

Maak een resource groep met de opdracht [AZ Group Create](/cli/azure/group#az-group-create) :

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Implementeer de container groep met de opdracht [AZ container Create](/cli/azure/container#az-container-create) , waarbij het yaml-bestand als argument wordt door gegeven.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Implementatie status weer geven

Als u de status van de implementatie wilt weer geven, gebruikt u de volgende opdracht [AZ container show](/cli/azure/container#az-container-show) :

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

Voor een geslaagde implementatie lijkt de uitvoer op de volgende manier:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   nginx, mcr.microsoft.com/azuredocs/aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-tls-connection"></a>TLS-verbinding controleren

Ga in uw browser naar het open bare IP-adres van de container groep. Het IP-adres dat in dit voor beeld wordt weer gegeven `52.157.22.76` , is de URL **https://52.157.22.76** . U moet HTTPS gebruiken om de actieve toepassing weer te geven vanwege de configuratie van de nginx-server. Pogingen om verbinding te maken via HTTP mislukt.

![Schermafbeelding van browser met toepassing die wordt uitgevoerd in een exemplaar van een Azure-container](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Omdat in dit voor beeld een zelfondertekend certificaat en niet een van een certificerings instantie wordt gebruikt, wordt in de browser een beveiligings waarschuwing weer gegeven wanneer verbinding wordt gemaakt met de site via HTTPS. Mogelijk moet u de waarschuwing accepteren of de instellingen voor de browser of het certificaat aanpassen om door te gaan naar de pagina. Dit gedrag is verwacht.

>

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u een nginx-container instelt voor het inschakelen van TLS-verbindingen met een web-app die wordt uitgevoerd in de container groep. U kunt dit voor beeld aanpassen voor apps die Luis teren op andere poorten dan poort 80. U kunt ook het nginx-configuratie bestand bijwerken om Server verbindingen op poort 80 (HTTP) automatisch door te sturen om HTTPS te gebruiken.

Hoewel in dit artikel nginx wordt gebruikt in de zijspan wagen, kunt u een andere TLS-provider gebruiken, zoals [Caddy](https://caddyserver.com/).

Als u uw container groep in een [virtueel Azure-netwerk](container-instances-vnet.md)implementeert, kunt u andere opties overwegen om een TLS-eind punt in te scha kelen voor een back-end-container exemplaar, waaronder:

* [Azure Functions-proxy's](../azure-functions/functions-proxies.md)
* [Azure API Management](../api-management/api-management-key-concepts.md)
* [Azure-toepassing gateway](../application-gateway/overview.md) : zie een voorbeeld [sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet)voor de implementatie.
