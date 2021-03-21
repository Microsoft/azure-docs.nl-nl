---
title: 'Zelfstudie: Django implementeren op een AKS-cluster met PostgreSQL - Flexibele server met behulp van Azure CLI'
description: Meer informatie over hoe u Django snel kunt bouwen en implementeren op AKS met Azure Database PostgreSQL - Flexibele server.
ms.service: postgresql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 12/10/2020
ms.custom: mvc
ms.openlocfilehash: 6e8effee91eed73193319238c2ad2f6eaf6d0473
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211274"
---
# <a name="tutorial-deploy-django-app-on-aks-with-azure-database-for-postgresql---flexible-server"></a>Zelfstudie: Django-app implementeren op AKS met Azure Database for PostgreSQL - Flexibele server

In deze quickstart implementeert u een Django-toepassing op een AKS-cluster (Azure Kubernetes Service) met Azure Database for PostgreSQL - Flexibele server (preview) met behulp van Azure CLI.

**[AKS](../../aks/intro-kubernetes.md)** is een beheerde Kubernetes-service waarmee u snel clusters kunt implementeren en beheren. **[Azure Database for PostgreSQL - Flexible Server (preview)](overview.md)** is een volledig beheerde databaseservice die is ontworpen om nauwkeurige controle en flexibiliteit te bieden als het gaat om databasebeheerfuncties en configuratie-instellingen.

> [!NOTE]
> - Azure Database for PostgreSQL - Flexibele server is momenteel in de openbare preview
> - In deze quickstart wordt ervan uitgegaan dat u basiskennis hebt van Kubernetes-concepten, Django en PostgreSQL.

## <a name="pre-requisites"></a>Vereisten
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

- Gebruik [Azure Cloud Shell](../../cloud-shell/quickstart.md) met behulp van de bash-omgeving.

   [![Starten insluiten](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell starten")](https://shell.azure.com)  
- [Installeer](/cli/azure/install-azure-cli) Azure CLI, indien gewenst, om CLI-referentieopdrachten uit te voeren.
  - Als u een lokale installatie gebruikt, meldt u zich aan bij Azure CLI met behulp van de opdracht [AZ login](/cli/azure/reference-index#az-login).  Volg de stappen die worden weergegeven in de terminal, om het verificatieproces te voltooien.  Zie [Aanmelden met Azure CLI](/cli/azure/authenticate-azure-cli) voor aanvullende aanmeldingsopties.
  - Installeer de Azure CLI-extensie bij het eerste gebruik, wanneer u hierom wordt gevraagd.  Zie [Extensies gebruiken met Azure CLI](/cli/azure/azure-cli-extensions-overview) voor meer informatie over extensies.
  - Voer [az version](/cli/azure/reference-index?#az_version) uit om de geïnstalleerde versie en afhankelijke bibliotheken te vinden. Voer [az upgrade](/cli/azure/reference-index?#az_upgrade) uit om te upgraden naar de nieuwste versie. Voor dit artikel is de nieuwste versie van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

> [!NOTE]
> Als u de opdrachten in deze quickstart lokaal uitvoert (in plaats van met Azure Cloud Shell), zorg er dan voor dat u de opdrachten uitvoert als beheerder.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische groep waarin Azure-resources worden geïmplementeerd en beheerd. U maakt een resourcegroep, *django-project*, met behulp van de opdracht [az group create][az-group-create] op locatie *eastus*.

```azurecli-interactive
az group create --name django-project --location eastus
```

> [!NOTE]
> De locatie voor de resourcegroep is de plaats waar de metagegevens van de resourcegroep worden opgeslagen. Dit is ook de locatie waar uw resources worden uitgevoerd in Azure als u tijdens het maken van de resource geen andere regio opgeeft.

In de volgende voorbeelduitvoer ziet u dat de resourcegroep is gemaakt:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/django-project",
  "location": "eastus",
  "managedBy": null,
  
  "name": "django-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>AKS-cluster maken

Gebruik de opdracht [az aks create](/cli/azure/aks#az-aks-create) om een AKS-cluster te maken. In het volgende voorbeeld wordt een cluster met de naam *myAKSCluster* gemaakt met één knooppunt. Dit zal enkele minuten in beslag nemen.

```azurecli-interactive
az aks create --resource-group django-project --name djangoappcluster --node-count 1 --generate-ssh-keys
```

Na enkele minuten is de opdracht voltooid en retourneert deze informatie over het cluster in JSON-indeling.

> [!NOTE]
> Wanneer een AKS-cluster wordt gemaakt, wordt automatisch een tweede resourcegroep gemaakt om de AKS-resources in op te slaan. Zie [Waarom worden er twee resourcegroepen gemaakt met AKS?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Als u een Kubernetes-cluster wilt beheren, gebruikt u [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/), de Kubernetes-opdrachtregelclient. Als u Azure Cloud Shell gebruikt, is `kubectl` al geïnstalleerd. Als u `kubectl` lokaal wilt installeren, gebruikt u de opdracht [az aks install-cli](/cli/azure/aks#az-aks-install-cli):

```azurecli-interactive
az aks install-cli
```

Gebruik de opdracht [az aks get-credentials](/cli/azure/aks#az-aks-get-credentials) om `kubectl` zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli-interactive
az aks get-credentials --resource-group django-project --name djangoappcluster
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

## <a name="create-an-azure-database-for-postgresql---flexible-server"></a>Een Azure Database for PostgreSQL - Flexibele server maken
Maak een flexibele server met de opdracht [az postgreSQL flexible-server create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create). Met de volgende opdracht maakt u een server met de standaardinstellingen van de service en waarden van de lokale context van Azure CLI:

```azurecli-interactive
az postgres flexible-server create --public-access <YOUR-IP-ADDRESS>
```

De gemaakte server heeft de volgende kenmerken:
- Er wordt een nieuwe, lege database, ```postgres```, gemaakt wanneer de server voor het eerst wordt ingericht. In deze quickstart wordt deze database gebruikt.
- Automatisch gegenereerde servernaam, beheerdersnaam, beheerderswachtwoord, resourcegroepnaam (indien nog niet opgegeven in de lokale context) en op dezelfde locatie als uw resourcegroep
- Servicestandaardwaarden voor resterende serverconfiguraties: rekenlaag (Algemeen), rekenkracht/SKU (Standard_D2s_v3 dat gebruikmaakt van 2vCores), bewaartermijn voor back-ups (7 dagen) en PostgreSQL-versie (12)
- Met een argument voor openbare toegang kunt u een server maken met openbare toegang die wordt beveiligd door firewallregels. Door uw IP-adres op te geven om de firewallregel toe te voegen om toegang vanaf uw clientcomputer toe te staan.
- De opdracht maakt gebruik van lokale context, dus de server wordt in de resourcegroep ```django-project``` en in de regio ```eastus``` gemaakt.


## <a name="build-your-django-docker-image"></a>Een Django Docker-installatiekopie bouwen

Maak een nieuwe [Django-toepassing](https://docs.djangoproject.com/en/3.1/intro/) of gebruik uw bestaande Django-project. Zorg ervoor dat de code deze mapstructuur heeft.

```
└───my-djangoapp
    └───views.py
    └───models.py
    └───forms.py
    ├───templates
        . . . . . . .
    ├───static
        . . . . . . .
└───my-django-project
    └───settings.py
    └───urls.py
    └───wsgi.py
        . . . . . . .
    └─── Dockerfile
    └─── requirements.txt
    └─── manage.py
    
```
Werk ```ALLOWED_HOSTS``` in ```settings.py``` bij om ervoor te zorgen dat de Django-toepassing gebruikmaakt van het externe IP-adres dat wordt toegewezen aan de Kubernetes-app.

```python
ALLOWED_HOSTS = ['*']
```

Werk sectie ```DATABASES={ }``` in het ```settings.py```-bestand bij. De databasehost, de gebruikersnaam en het wachtwoord uit het Kubernetes-manifestbestand worden gelezen door het onderstaande codefragment.

```python
DATABASES={
   'default':{
      'ENGINE':'django.db.backends.postgresql_psycopg2',
      'NAME':os.getenv('DATABASE_NAME'),
      'USER':os.getenv('DATABASE_USER'),
      'PASSWORD':os.getenv('DATABASE_PASSWORD'),
      'HOST':os.getenv('DATABASE_HOST'),
      'PORT':'5432',
      'OPTIONS': {'sslmode': 'require'}
   }
}
```

### <a name="generate-a-requirementstxt-file"></a>Een requirements.txt-bestand genereren
Maak een ```requirements.txt```-bestand om de afhankelijkheden voor de Django-toepassing in te vermelden. Hier is een voorbeeld van een ```requirements.txt```-bestand. U kunt [``` pip freeze > requirements.txt```](https://pip.pypa.io/en/stable/reference/pip_freeze/) gebruiken om een requirements.txt-bestand te genereren voor uw bestaande toepassing.

``` text
Django==2.2.17
postgres==3.0.0
psycopg2-binary==2.8.6
psycopg2-pool==1.1
pytz==2020.4
```

### <a name="create-a-dockerfile"></a>Een Dockerfile maken
Maak een nieuw bestand met de naam ```Dockerfile```, en kopieer het onderstaande codefragment. Met dit Dockerfile wordt Python 3.8 ingesteld en worden alle vereisten geïnstalleerd die zijn vermeld in het requirements.txt-bestand.

```docker
# Use the official Python image from the Docker Hub
FROM python:3.8.2

# Make a new directory to put our code in.
RUN mkdir /code

# Change the working directory.
WORKDIR /code

# Copy to code folder
COPY . /code/

# Install the requirements.
RUN pip install -r requirements.txt

# Run the application:
CMD python manage.py runserver 0.0.0.0:8000
```

### <a name="build-your-image"></a>Uw installatiekopie bouwen
Zorg ervoor dat u zich in de map ```my-django-app``` in een terminal bevindt met behulp van de opdracht ```cd```. Voer de volgende opdracht uit om de installatiekopie van het bulletinboard te bouwen:

``` bash

docker build --tag myblog:latest .

```

Implementeer uw installatiekopie op de [Docker-hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) of het [Azure-containerregister](../../container-registry/container-registry-get-started-azure-cli.md).

> [!IMPORTANT]
>Als u het Azure-containerregister (ACR) gebruikt, voert u de opdracht ```az aks update``` uit om ACR-account aan het AKS-cluster te koppelen.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g django-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Kubernetes-manifestbestand maken

In een Kubernetes-manifestbestand wordt een gewenste status voor het cluster gedefinieerd, zoals welke containerinstallatiekopieën moeten worden uitgevoerd. Maak een manifestbestand met de naam ```djangoapp.yaml``` en kopieer er de volgende YAML-definitie in.

>[!IMPORTANT]
> - Vervang ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` door de feitelijke naam en tag van de Django Docker-installatiekopie, bijvoorbeeld ```docker-hub-user/myblog:latest```.
> - Werk de sectie ```env``` hieronder bij met ```SERVERNAME```, ```YOUR-DATABASE-USERNAME``` ```YOUR-DATABASE-PASSWORD``` van uw flexibele Postgres-server.


```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: django-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: django-app
  template:
    metadata:
      labels:
        app: django-app
    spec:
      containers:
      - name: django-app
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.postgres.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "postgres"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - django-app
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: python-svc
spec:
  type: LoadBalancer
  ports:
    - port: 8000
  selector:
    app: django-app
```

## <a name="deploy-django-to-aks-cluster"></a>Django implementeren op het AKS-cluster
Implementeer de toepassing met de opdracht [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) en geef de naam op van uw YAML-manifest:

```console
kubectl apply -f djangoapp.yaml
```

In de volgende voorbeelduitvoer ziet u dat je implementaties en services zijn gemaakt:

```output
deployment "django-app" created
service "python-svc" created
```

Met een implementatie ```django-app``` kunt u de details van uw implementatie beschrijven, zoals welke installatiekopieën moeten worden gebruikt voor de app, het aantal pods, en de pod-configuratie. Er wordt een service ```python-svc``` gemaakt om de toepassing beschikbaar te maken via een extern IP-adres.

## <a name="test-the-application"></a>De toepassing testen

Wanneer de toepassing wordt uitgevoerd, maakt een Kubernetes-service de front-end van de toepassing beschikbaar op internet. Dit proces kan enkele minuten duren.

Gebruik de opdracht [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) met het argument `--watch` om de voortgang te controleren.

```azurecli-interactive
kubectl get service django-app --watch
```

Eerst wordt *EXTERNAL-IP* voor de service *django-app* weergegeven als *In behandeling*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
django-app   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Zodra het *EXTERNAL-IP*-adres is gewijzigd van *in behandeling* in een echt openbaar IP-adres, gebruikt u `CTRL-C` om het controleproces van `kubectl` te stoppen. In de volgende voorbeelduitvoer ziet u een geldig openbaar IP-adres dat aan de service is toegewezen:

```output
django-app  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Open nu een webbrowser naar het externe IP-adres van de service om de Django-toepassing weer te geven.  

>[!NOTE]
> - Voor de Django-site wordt momenteel geen HTTPS gebruikt. Het is raadzaam om [TLS IN TE SCHAKELEN voor uw eigen certificaten](../../aks/ingress-own-tls.md).
> - U kunt [HTTP-routering](../../aks/http-application-routing.md) inschakelen voor uw cluster. Wanneer HTTP-routering is ingeschakeld, wordt een toegangscontroller geconfigureerd in het AKS-cluster. Terwijl toepassingen worden geïmplementeerd, worden met de oplossing ook openbaar toegankelijke DNS-namen gemaakt voor toepassingseindpunten.

## <a name="run-database-migrations"></a>Databasemigraties uitvoeren

Voor elke Django-toepassing moet u databasemigratie uitvoeren of statische bestanden verzamelen. U kunt deze Django Shell-opdrachten uitvoeren met behulp van ```$ kubectl exec <pod-name> -- [COMMAND]```.  Voordat u de opdracht uitvoert, moet u de podnaam vinden met behulp van ```kubectl get pods```. 

```bash
$ kubectl get pods
```

U ziet nu uitvoer die lijkt op dit
```output
NAME                             READY   STATUS          RESTARTS   AGE
django-app-5d9cd6cd8-l6x4b     1/1     Running              0       2m
```

Zodra de podnaam is gevonden, kunt u Django-databasemigraties uitvoeren met behulp van de opdracht ```$ kubectl exec <pod-name> -- [COMMAND]```. Opmerking: ```/code/``` is de werkmap voor het project dat hierboven in ```Dockerfile``` is gedefinieerd.

```bash
$ kubectl exec django-app-5d9cd6cd8-l6x4b -- python /code/manage.py migrate
```

De uitvoer ziet er ongeveer als volgt uit 
```output 
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  . . . . . . 
```

Als u problemen ondervindt, voert u ```kubectl logs <pod-name>``` uit om te zien welke uitzondering wordt geretourneerd met de toepassing. Als de toepassing goed werkt, ziet u uitvoer die er ongeveer zo uitziet als u ```kubectl logs``` uitvoert.

```output
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
December 08, 2020 - 23:24:14
Django version 2.2.17, using settings 'django_postgres_app.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
```

## <a name="clean-up-the-resources"></a>Resources opschonen

Om Azure-kosten te vermijden, moet u overbodige resources opschonen.  Gebruik de opdracht [az group delete](/cli/azure/group&preserve-view=true#az_group_delete) om de resourcegroep, de containerservice en alle gerelateerde resources te verwijderen wanneer u het cluster niet meer nodig hebt.

```azurecli-interactive
az group delete --name django-project --yes --no-wait
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [Overwegingen voor en verwijdering van AKS service-principal](../../aks/kubernetes-service-principal.md#additional-considerations) voor stappen voor het verwijderen van de service-principal. Als u een beheerde identiteit hebt gebruikt, wordt de identiteit beheerd door het platform en hoeft deze niet te worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

- Leer hoe u [toegang krijgt tot het Kubernetes-dashboard](../../aks/kubernetes-dashboard.md) voor uw AKS-cluster
- Leer hoe u [continue implementatie kunt inschakelen](../../aks/deployment-center-launcher.md)
- Leer hoe u uw [cluster kunt schalen](../../aks/tutorial-kubernetes-scale.md)
- Leer hoe u de [flexibele Postgres-server](./quickstart-create-server-cli.md) kunt beheren
- Leer hoe u [serverparameters kunt configureren](./howto-configure-server-parameters-using-cli.md) voor uw databaseserver.