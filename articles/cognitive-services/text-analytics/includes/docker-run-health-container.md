---
title: Container voorbeeld van de opdracht docker run uitvoeren
titleSuffix: Azure Cognitive Services
description: Opdracht docker run voor Status container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/12/2020
ms.author: aahi
ms.openlocfilehash: af8fec56c32b52e2af584e59f08db6cc7129c9c5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947205"
---
## <a name="install-the-container"></a>De container installeren

Er zijn meerdere manieren waarop u de Text Analytics voor de status container kunt installeren en uitvoeren. 

- Gebruik de [Azure Portal](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare) om een Text Analytics resource te maken en gebruik docker om uw container op te halen.
- Gebruik de volgende Power shell-en Azure CLI-scripts voor het automatiseren van de resource-implementatie en container configuratie.

### <a name="run-the-container-locally"></a>De container lokaal uitvoeren

Als u de container in uw eigen omgeving wilt uitvoeren nadat de container installatie kopie is gedownload, zoekt u de afbeeldings-ID:
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

Voer de volgende `docker run` opdracht uit. Vervang onderstaande tijdelijke aanduidingen door uw eigen waarden:

| Tijdelijke aanduiding | Waarde | Notatie of voor beeld |
|-------------|-------|---|
| **{API_KEY}** | De sleutel voor uw Text Analytics-resource. U vindt deze op de pagina van de bron **en het eind punt** op de Azure Portal. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Het eind punt voor toegang tot de Text Analytics-API. U vindt deze op de pagina van de bron **en het eind punt** op de Azure Portal. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | De afbeeldings-ID van de container. | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | De invoer Directory voor de container. | Windows: `C:\healthcareMount` <br> Linux/MacOS: `/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

Met deze opdracht gebeurt het volgende:

- Er wordt van uitgegaan dat de invoer Directory bestaat op de hostmachine
- Hiermee wordt een Text Analytics uitgevoerd voor de status container van de container installatie kopie
- Wijst 6 CPU-kern en 12 GB aan geheugen toe
- Beschrijft TCP-poort 5000 en wijst een pseudo-TTY toe voor de container
- Verwijdert de container automatisch nadat deze is afgesloten. De container installatie kopie is nog steeds beschikbaar op de hostcomputer.

### <a name="demo-ui-to-visualize-output"></a>Demo-UI voor het visualiseren van uitvoer

> [!NOTE]
> De demo is alleen beschikbaar met de Text Analytics voor de status container.

De container bevat op REST gebaseerde eindpunt-API's voor queryvoorspelling.  Er is ook een visualisatie hulpprogramma in de container beschikbaar die toegankelijk is door toe te voegen `/demo` aan het eind punt van de container. Bijvoorbeeld:

```
http://<serverURL>:5000/demo
```

Gebruik de onderstaande voor beeld-krul aanvraag voor het verzenden van een query naar de container die u hebt geïmplementeerd om de variabele te vervangen `serverURL` door de juiste waarde.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

### <a name="install-the-container-using-azure-web-app-for-containers"></a>De container installeren met behulp van Azure Web App for Containers

Azure [Web App for containers](https://azure.microsoft.com/services/app-service/containers/) is een Azure-resource die is toegewezen aan het uitvoeren van containers in de Cloud. Het biedt out-of-the-box-mogelijkheden, zoals automatisch schalen, ondersteuning van docker-containers en docker-samen stellen, HTTPS-ondersteuning en nog veel meer.

> [!NOTE]
> Met Azure Web App krijgt u automatisch een domein in de vorm van `<appservice_name>.azurewebsites.net`

Voer dit Power shell-script uit met behulp van de Azure CLI om een Web App for Containers te maken, met behulp van uw abonnement en de container installatie kopie via HTTPS. Wacht totdat het script is voltooid (ongeveer 25-30 minuten) voordat u de eerste aanvraag indient.

```azurecli
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>De container installeren met behulp van Azure container instance

U kunt ook een Azure container instance (ACI) gebruiken om de implementatie eenvoudiger te maken. ACI is een bron waarmee u docker-containers op aanvraag kunt uitvoeren in een beheerde, serverloze Azure-omgeving. 

Zie [Azure container instances gebruiken](../../containers/azure-container-instance-recipe.md) voor stappen voor het implementeren van een ACI-bron met behulp van de Azure Portal. U kunt ook het onderstaande Power shell-script gebruiken met behulp van Azure CLI, waarmee u een ACI maakt in uw abonnement met behulp van de container installatie kopie.  Wacht totdat het script is voltooid (ongeveer 25-30 minuten) voordat u de eerste aanvraag indient.  Als gevolg van de limiet van het maximum aantal Cpu's per ACI-resource, selecteert u deze optie niet als u verwacht dat u meer dan 5 grote documenten (ongeveer 5000 tekens per aanvraag verzendt).
Raadpleeg het artikel over [regionale ondersteuning voor ACI](../../../container-instances/container-instances-region-availability.md) voor informatie over de beschik baarheid. 

> [!NOTE] 
> Azure Container Instances geen HTTPS-ondersteuning voor de ingebouwde domeinen op. Als u HTTPS nodig hebt, moet u deze hand matig configureren, met inbegrip van het maken van een certificaat en het registreren van een domein. U vindt hier instructies om dit te doen met NGINX hieronder.

```azurecli
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>Beveiligde ACI-connectiviteit

Standaard wordt er geen beveiliging gegeven bij gebruik van ACI met container-API. De reden hiervoor is dat de containers worden uitgevoerd als onderdeel van een pod dat door een netwerk brug wordt beschermd vanaf de buiten wereld. U kunt een container echter wijzigen met een front-facing onderdeel, zodat het container eindpunt privé blijft. De volgende voor beelden gebruiken [NGINX](https://www.nginx.com) als ingangs gateway voor de ondersteuning van HTTPS/SSL en verificatie van client certificaten.

> [!NOTE]
> NGINX is een open-source, snelle HTTP-server en-proxy. Een NGINX-container kan worden gebruikt om een TLS-verbinding voor één container te beëindigen. Meer complexe NGINX ingangs-gebaseerde TLS-afsluit oplossingen zijn ook mogelijk.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>NGINX instellen als een ingangs gateway

NGINX maakt gebruik van [configuratie bestanden](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) om functies tijdens runtime in te scha kelen. Als u TLS-beëindiging voor een andere service wilt inschakelen, moet u een SSL-certificaat opgeven om de TLS-verbinding te beëindigen en  `proxy_pass` een adres voor de service op te geven. Hieronder vindt u een voor beeld.


> [!NOTE]
> `ssl_certificate` verwacht een pad dat moet worden opgegeven in het lokale bestands systeem van de NGINX-container. Het opgegeven adres `proxy_pass` moet beschikbaar zijn in het NGINX-netwerk van de container.

Alle bestanden in de container NGINX `_.conf_` worden geladen die zijn gekoppeld onder `/etc/nginx/conf.d/` in het http-configuratiepad.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Voor beeld van docker-opstellen van bestand

In het onderstaande voor beeld ziet u hoe een [docker opstellen](https://docs.docker.com/compose/reference/overview) bestand kan worden gemaakt om de NGINX en Text Analytics voor status containers te implementeren:

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

Voer de volgende opdracht uit vanaf een console op het hoofd niveau van het bestand om het docker opstellen van het bestand te initiëren:

```bash
docker-compose up
```

Zie de documentatie van NGINX over [NGINX SSL-beëindiging](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/)voor meer informatie.