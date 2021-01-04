---
title: 'Zelfstudie: WordPress implementeren op een AKS-cluster met een flexibele MySQL-server met behulp van de Azure CLI'
description: 'Meer informatie over hoe u WordPress snel kunt bouwen en implementeren op AKS met Azure Database for MySQL: flexibele server.'
ms.service: mysql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 11/25/2020
ms.custom: mvc
ms.openlocfilehash: a02cb30b0f00f732fa0c4ac9319a652ef5cb6fc1
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657053"
---
# <a name="tutorial-deploy-wordpress-app-on-aks-with-azure-database-for-mysql---flexible-server"></a>Zelfstudie: WordPress-app implementeren op AKS met Azure Database for MySQL: flexibele server

In deze quickstart implementeert u een WordPress-toepassing op een AKS-cluster (Azure Kubernetes Service) met Azure Database for MySQL - flexibele server (preview) met behulp van de Azure CLI. 
**[AKS](../../aks/intro-kubernetes.md)** is een beheerde Kubernetes-service waarmee u snel clusters kunt implementeren en beheren. **[Azure Database for MySQL - flexibele server (preview)](overview.md)** is een volledig beheerde databaseservice die is ontworpen om nauwkeurige controle en flexibiliteit te bieden als het gaat om databasebeheerfuncties en configuratie-instellingen. Momenteel is de flexibele server in preview.

> [!NOTE]
> - Azure Database for MySQL Flexible Server is momenteel beschikbaar als openbare preview
> - In deze quickstart wordt ervan uitgegaan dat u basiskennis hebt van Kubernetes-concepten WordPress en MySQL.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is de nieuwste versie van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

> [!NOTE]
> Als u de opdrachten in deze quickstart lokaal uitvoert (in plaats van met Azure Cloud Shell), zorg er dan voor dat u de opdrachten uitvoert als beheerder.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische groep waarin Azure-resources worden geïmplementeerd en beheerd. U maakt een resourcegroep, *wordpress-project*, met behulp van de opdracht [az group create][az-group-create] op locatie *eastus*.

```azurecli-interactive
az group create --name wordpress-project --location eastus
```

> [!NOTE]
> De locatie voor de resourcegroep is de plaats waar de metagegevens van de resourcegroep worden opgeslagen. Dit is ook de locatie waar uw resources worden uitgevoerd in Azure als u tijdens het maken van de resource geen andere regio opgeeft.

In de volgende voorbeelduitvoer ziet u dat de resourcegroep is gemaakt:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/wordpress-project",
  "location": "eastus",
  "managedBy": null,
  "name": "wordpress-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>AKS-cluster maken

Gebruik de opdracht [az aks create](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-create) om een AKS-cluster te maken. In het volgende voorbeeld wordt een cluster met de naam *myAKSCluster* gemaakt met één knooppunt. Dit zal enkele minuten in beslag nemen.

```azurecli-interactive
az aks create --resource-group wordpress-project --name wordpresscluster--node-count 1 --generate-ssh-keys
```

Na enkele minuten is de opdracht voltooid en retourneert deze informatie over het cluster in JSON-indeling.

> [!NOTE]
> Wanneer een AKS-cluster wordt gemaakt, wordt automatisch een tweede resourcegroep gemaakt om de AKS-resources in op te slaan. Zie [Waarom worden er twee resourcegroepen gemaakt met AKS?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Als u een Kubernetes-cluster wilt beheren, gebruikt u [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/), de Kubernetes-opdrachtregelclient. Als u Azure Cloud Shell gebruikt, is `kubectl` al geïnstalleerd. Als u `kubectl` lokaal wilt installeren, gebruikt u de opdracht [az aks install-cli](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-install-cli):

```azurecli-interactive
az aks install-cli
```

Gebruik de opdracht [az aks get-credentials](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-get-credentials) om `kubectl` zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli-interactive
az aks get-credentials --resource-group wordpress-project --name wordpresscluster
```

> [!NOTE]
> De bovenstaande opdracht maakt gebruik van de standaardlocatie voor het [Kubernetes-configuratiebestand](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), namelijk `~/.kube/config`. U kunt een andere locatie voor uw Kubernetes-configuratiebestand opgeven door *--file* te gebruiken.

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) om een lijst met clusterknooppunten te retourneren.

```azurecli-interactive
kubectl get nodes
```

In de volgende voorbeelduitvoer ziet u het enkele knooppunt dat is gemaakt in de vorige stappen. Zorg ervoor dat de status van het knooppunt *Ready* is:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL - flexibele server maken
Maak een flexibele server met de opdracht [az mysql flexible-server create](/cli/azure/mysql/flexible-server?view=azure-cli-latest&preserve-view=true). Met de volgende opdracht maakt u een server met de standaardinstellingen van de service en waarden van de lokale context van Azure CLI:

```azurecli-interactive
az mysql flexible-server create --public-access <YOUR-IP-ADDRESS>
```

De gemaakte server heeft de volgende kenmerken:
- Er wordt een nieuwe, lege database, ```flexibleserverdb```, gemaakt wanneer de server voor het eerst wordt ingericht. In deze quickstart wordt deze database gebruikt.
- Automatisch gegenereerde servernaam, beheerdersnaam, beheerderswachtwoord, resourcegroepnaam (indien nog niet opgegeven in de lokale context) en op dezelfde locatie als uw resourcegroep
- Servicestandaardwaarden voor resterende serverconfiguraties: rekenlaag (Burstable), rekenomvang/SKU (B1MS), bewaartermijn voor back-ups (7 dagen) en MySQL-versie (5.7)
- Met een argument voor openbare toegang kunt u een server maken met openbare toegang die wordt beveiligd door firewallregels. Door uw IP-adres op te geven om de firewallregel toe te voegen om toegang vanaf uw clientcomputer toe te staan.
- De opdracht maakt gebruik van lokale context, dus de server wordt in de resourcegroep ```wordpress-project``` en in de regio ```eastus``` gemaakt.


### <a name="build-your-wordpress-docker-image"></a>Een Docker-installatiekopie van WordPress bouwen

Download de [nieuwste WordPress](https://wordpress.org/download/)-versie. Maak een nieuwe directory, ```my-wordpress-app```, voor uw project en gebruik deze eenvoudige mappenstructuur

```
└───my-wordpress-app
    └───public
        ├───wp-admin
        │   ├───css
        . . . . . . .
        ├───wp-content
        │   ├───plugins
        . . . . . . .
        └───wp-includes
        . . . . . . .
        ├───wp-config-sample.php
        ├───index.php
        . . . . . . .
    └─── Dockerfile

```


Wijzig de naam ```wp-config-sample.php```  in ```wp-config.php``` en vervang regels 21 tot en met 32 door dit codefragment. De databasehost, de gebruikersnaam en het wachtwoord uit het Kubernetes-manifestbestand worden gelezen door het onderstaande codefragment.

```php
//Using environment variables for DB connection information

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */

$connectstr_dbhost = getenv('DATABASE_HOST');
$connectstr_dbusername = getenv('DATABASE_USERNAME');
$connectstr_dbpassword = getenv('DATABASE_PASSWORD');

/** MySQL database name */
define('DB_NAME', 'flexibleserverdb');

/** MySQL database username */
define('DB_USER', $connectstr_dbusername);

/** MySQL database password */
define('DB_PASSWORD',$connectstr_dbpassword);

/** MySQL hostname */
define('DB_HOST', $connectstr_dbhost);

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/** SSL*/
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
```

### <a name="create-a-dockerfile"></a>Een Dockerfile maken
Maak een nieuw Docker-bestand en kopieer dit codefragment. De Apache-webserver wordt met behulp van het Docker-bestand ingesteld met PHP en de mysqli-extensie wordt erdoor ingeschakeld.

```docker
FROM php:7.2-apache
COPY public/ /var/www/html/
RUN docker-php-ext-install mysqli
RUN docker-php-ext-enable mysqli
```

### <a name="build-your-docker-image"></a>De Docker-installatiekopie bouwen
Zorg ervoor dat u zich in de map ```my-wordpress-app``` in een terminal bevindt met behulp van de opdracht ```cd```. Voer de volgende opdracht uit om de installatiekopie te bouwen:

``` bash

docker build --tag myblog:latest .

```

Implementeer uw installatiekopie op de [Docker-hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) of het [Azure-containerregister](../../container-registry/container-registry-get-started-azure-cli.md).

> [!IMPORTANT]
>Als u het Azure-containerregister (ACR) gebruikt, voert u de opdracht ```az aks update``` uit om ACR-account aan het AKS-cluster te koppelen.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g wordpress-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Kubernetes-manifestbestand maken

In een Kubernetes-manifestbestand wordt een gewenste status voor het cluster gedefinieerd, zoals welke containerinstallatiekopieën moeten worden uitgevoerd. Maak een manifestbestand met de naam `mywordpress.yaml` en kopieer er de volgende YAML-definitie in.

>[!IMPORTANT]
> - Vervang ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` door de feitelijke naam en tag van de WordPress Docker-installatiekopie, bijvoorbeeld ```docker-hub-user/myblog:latest```.
> - Werk de sectie ```env``` hieronder bij met ```SERVERNAME```, ```YOUR-DATABASE-USERNAME``` ```YOUR-DATABASE-PASSWORD``` van uw flexibele MySQL-server.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-blog
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress-blog
  template:
    metadata:
      labels:
        app: wordpress-blog
    spec:
      containers:
      - name: wordpress-blog
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.mysql.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "flexibleserverdb"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - wordpress-blog
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: php-svc
spec:
  type: LoadBalancer
  ports:
    - port: 80
  selector:
    app: wordpress-blog
```

## <a name="deploy-wordpress-to-aks-cluster"></a>WordPress implementeren op AKS-cluster
Implementeer de toepassing met de opdracht [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) en geef de naam op van uw YAML-manifest:

```console
kubectl apply -f mywordpress.yaml
```

In de volgende voorbeelduitvoer ziet u dat je implementaties en services zijn gemaakt:

```output
deployment "wordpress-blog" created
service "php-svc" created
```

## <a name="test-the-application"></a>De toepassing testen

Wanneer de toepassing wordt uitgevoerd, maakt een Kubernetes-service de front-end van de toepassing beschikbaar op internet. Dit proces kan enkele minuten duren.

Gebruik de opdracht [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) met het argument `--watch` om de voortgang te controleren.

```azurecli-interactive
kubectl get service wordpress-blog --watch
```

Eerst wordt het *EXTERNAL-IP*-adres voor de service *wordpress-blog* weergegeven als *in behandeling*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
wordpress-blog   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Zodra het *EXTERNAL-IP*-adres is gewijzigd van *in behandeling* in een echt openbaar IP-adres, gebruikt u `CTRL-C` om het controleproces van `kubectl` te stoppen. In de volgende voorbeelduitvoer ziet u een geldig openbaar IP-adres dat aan de service is toegewezen:

```output
wordpress-blog  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

### <a name="browse-wordpress"></a>Door WordPress bladeren

Open een webbrowser naar het externe IP-adres van uw service als u de WordPress-installatiepagina wilt zien.

   :::image type="content" source="./media/tutorial-deploy-wordpress-on-aks/wordpress-aks-installed-success.png" alt-text="WordPress is geïnstalleerd op AKS en de flexibele MySQL-server":::

>[!NOTE]
> - Momenteel gebruikt de WordPress-site geen HTTPS. Het is raadzaam om [TLS IN TE SCHAKELEN voor uw eigen certificaten](../../aks/ingress-own-tls.md).
> - U kunt [HTTP-routering](../../aks/http-application-routing.md) inschakelen voor uw cluster.

## <a name="clean-up-the-resources"></a>Resources opschonen

Om Azure-kosten te vermijden, moet u overbodige resources opschonen.  Gebruik de opdracht [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) om de resourcegroep, de containerservice en alle gerelateerde resources te verwijderen wanneer u het cluster niet meer nodig hebt.

```azurecli-interactive
az group delete --name wordpress-project --yes --no-wait
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [Overwegingen voor en verwijdering van AKS service-principal](../../aks/kubernetes-service-principal.md#additional-considerations) voor stappen voor het verwijderen van de service-principal. Als u een beheerde identiteit hebt gebruikt, wordt de identiteit beheerd door het platform en hoeft deze niet te worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

- Leer hoe u [toegang krijgt tot het Kubernetes-dashboard](../../aks/kubernetes-dashboard.md) voor uw AKS-cluster
- Leer hoe u uw [cluster kunt schalen](../../aks/tutorial-kubernetes-scale.md)
- Leer hoe u de [flexibele MySQL-server](./quickstart-create-server-cli.md) kunt beheren
- Leer hoe u [serverparameters kunt configureren](./how-to-configure-server-parameters-cli.md) voor uw databaseserver.

