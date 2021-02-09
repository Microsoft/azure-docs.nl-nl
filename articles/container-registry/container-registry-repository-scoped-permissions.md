---
title: Machtigingen voor opslag plaatsen in Azure Container Registry
description: Een token maken met machtigingen die zijn gericht op specifieke opslag plaatsen in een Premium-REGI ster om installatie kopieën op te halen of te pushen of om andere acties uit te voeren
ms.topic: article
ms.date: 02/04/2021
ms.openlocfilehash: ceec69d746f77ea7a23bc70d029c8b3736e7f292
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988264"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Een token met machtigingen voor een opslag plaats maken

In dit artikel wordt beschreven hoe u tokens en Scope Maps maakt om de toegang tot specifieke opslag plaatsen in het container register te beheren. Door tokens te maken, kan een register eigenaar gebruikers of Services voorzien van scoped, beperkte toegang tot opslag plaatsen om installatie kopieën te halen of te pushen of andere acties uit te voeren. Een token biedt meer verfijnde machtigingen dan andere opties voor het [instellen](container-registry-authentication.md)van het REGI ster, die de bereik machtigingen voor een volledig REGI ster hebben. 

Scenario's voor het maken van een token zijn onder andere:

* IoT-apparaten met afzonderlijke tokens toestaan een installatie kopie van een opslag plaats te halen
* Een externe organisatie voorzien van machtigingen voor een specifieke opslag plaats 
* Beperk de toegang tot opslag plaatsen tot verschillende gebruikers groepen in uw organisatie. U kunt bijvoorbeeld schrijf-en lees toegang bieden aan ontwikkel aars die installatie kopieën bouwen die gericht zijn op specifieke opslag plaatsen en lees toegang tot teams die vanuit deze opslag plaatsen worden geïmplementeerd.

Deze functie is beschikbaar in de service tier van het **Premium** -container register. Zie [Azure container Registry service lagen](container-registry-skus.md)voor meer informatie over de service lagen en limieten van het REGI ster.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-versie en er [zijn enkele beperkingen van toepassing](#preview-limitations). Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="preview-limitations"></a>Preview-beperkingen

* U kunt momenteel geen machtigingen voor opslag plaatsen toewijzen aan een Azure Active Directory identiteit, zoals een service-principal of beheerde identiteit.
* U kunt geen scope toewijzing maken in een REGI ster dat is ingeschakeld voor [anonieme pull-toegang](container-registry-faq.md#how-do-i-enable-anonymous-pull-access).

## <a name="concepts"></a>Concepten

Als u machtigingen met een opslag plaats met een bereik wilt configureren, maakt u een *token* met een bijbehorende *bereik toewijzing*. 

* Met een **token** in combi natie met een gegenereerd wacht woord kan de gebruiker worden geverifieerd met het REGI ster. U kunt een verval datum instellen voor een token wachtwoord of een token op elk gewenst moment uitschakelen.  

  Na verificatie met een token kan de gebruiker of service een of meer *acties* uitvoeren die zijn gericht op een of meer opslag plaatsen.

  |Bewerking  |Beschrijving  | Voorbeeld |
  |---------|---------|--------|
  |`content/delete`    | Gegevens uit de opslag plaats verwijderen  | Een opslag plaats of een manifest verwijderen |
  |`content/read`     |  Gegevens uit de opslag plaats lezen |  Een artefact ophalen |
  |`content/write`     |  Gegevens naar de opslag plaats schrijven     | Gebruiken `content/read` om een artefact te pushen |
  |`metadata/read`    | Meta gegevens uit de opslag plaats lezen   | Tags of manifesten weer geven |
  |`metadata/write`     |  Meta gegevens naar de opslag plaats schrijven  | Lees-, schrijf-of verwijder bewerkingen in-of uitschakelen |

* Een **Scope toewijzing** groepeert de machtigingen voor de opslag plaats die u toepast op een token en kan opnieuw worden toegepast op andere tokens. Elk token is gekoppeld aan één scope-toewijzing. 

   Met een bereik toewijzing:

    * Meerdere tokens met identieke machtigingen configureren voor een set opslag plaatsen
    * Token machtigingen bijwerken bij het toevoegen of verwijderen van opslagplaats acties in de scope toewijzing of het Toep assen van een andere scope toewijzing 

  Azure Container Registry biedt ook verschillende door het systeem gedefinieerde bereik toewijzingen die u kunt Toep assen bij het maken van tokens. De machtigingen van door het systeem gedefinieerde bereik toewijzingen zijn van toepassing op alle opslag plaatsen in het REGI ster.

In de volgende afbeelding ziet u de relatie tussen tokens en Scope Maps. 

![Register tokens en Scope Maps](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Vereisten

* **Azure cli** -opdracht voor beelden van Azure cli-opdrachten in dit artikel is Azure CLI-versie 2.17.0 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.
* **Docker** : u hebt een lokale docker-installatie nodig om te verifiëren met het REGI ster om installatie kopieën op te halen of te pushen. Docker biedt installatie-instructies voor de systemen [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) en [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Container register** : als u er nog geen hebt, maakt u een Premium container registry in uw Azure-abonnement of werkt u een bestaand REGI ster bij. Gebruik bijvoorbeeld de [Azure Portal](container-registry-get-started-portal.md) of de [Azure cli](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Token maken-CLI

### <a name="create-token-and-specify-repositories"></a>Token maken en opslag plaatsen opgeven

Maak een token met behulp van de opdracht [AZ ACR token Create][az-acr-token-create] . Wanneer u een token maakt, kunt u een of meer opslag plaatsen en gekoppelde acties op elke opslag plaats opgeven. De opslag plaatsen hoeven nog niet in het REGI ster te staan. Zie de [volgende sectie](#create-token-and-specify-scope-map)voor het maken van een token door een bestaande scope toewijzing op te geven.

In het volgende voor beeld wordt een token gemaakt in het REGI ster *myregistry* met de volgende machtigingen voor de `samples/hello-world` opslag plaats: `content/write` en `content/read` . Standaard wordt met de opdracht de standaard token status ingesteld op `enabled` , maar u kunt de status op `disabled` elk gewenst moment bijwerken.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

De uitvoer bevat details over het token. Standaard worden er twee wacht woorden gegenereerd die niet verlopen, maar u kunt desgewenst een verval datum instellen. Het is raadzaam om de wacht woorden op een veilige plek op te slaan, zodat u deze later kunt gebruiken voor verificatie. Het wacht woord kan niet opnieuw worden opgehaald, maar er kunnen nieuwe worden gegenereerd.

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

> [!NOTE]
> Zie [token wacht woorden opnieuw genereren](#regenerate-token-passwords) verderop in dit artikel voor meer informatie over het opnieuw genereren van token wachtwoorden en verloop perioden.

De uitvoer bevat details over het bereik dat is gemaakt met de opdracht. U kunt de scope-toewijzing hier genoemd gebruiken `MyToken-scope-map` om dezelfde opslagplaats acties toe te passen op andere tokens. Of werk de scope toewijzing later bij om de machtigingen van de gekoppelde tokens te wijzigen.

### <a name="create-token-and-specify-scope-map"></a>Token maken en bereik toewijzing opgeven

Een andere manier om een token te maken, is door een bestaande scope toewijzing op te geven. Als u nog geen scope toewijzing hebt, moet u er een maken door opslag plaatsen en gekoppelde acties op te geven. Geef vervolgens de scope toewijzing op bij het maken van een token. 

Als u een scope toewijzing wilt maken, gebruikt u de opdracht [AZ ACR Scope-map Create][az-acr-scope-map-create] . Met de volgende opdracht maakt u een scope toewijzing met dezelfde machtigingen voor de `samples/hello-world` opslag plaats die u eerder hebt gebruikt. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Voer [AZ ACR token Create][az-acr-token-create] uit om een token te maken, waarbij u de *MyScopeMap* -bereik toewijzing opgeeft. Net als in het vorige voor beeld wordt met de opdracht de standaard token status ingesteld op `enabled` .

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

De uitvoer bevat details over het token. Standaard worden er twee wacht woorden gegenereerd. Het is raadzaam om de wacht woorden op een veilige plek op te slaan, zodat u deze later kunt gebruiken voor verificatie. Het wacht woord kan niet opnieuw worden opgehaald, maar er kunnen nieuwe worden gegenereerd.

> [!NOTE]
> Zie [token wacht woorden opnieuw genereren](#regenerate-token-passwords) verderop in dit artikel voor meer informatie over het opnieuw genereren van token wachtwoorden en verloop perioden.

## <a name="create-token---portal"></a>Token maken-Portal

U kunt de Azure Portal gebruiken om tokens en Scope Maps te maken. Net als met de `az acr token create` cli-opdracht kunt u een bestaande bereik toewijzing Toep assen of een scope toewijzing maken wanneer u een token maakt door een of meer opslag plaatsen en gekoppelde acties op te geven. De opslag plaatsen hoeven nog niet in het REGI ster te staan. 

In het volgende voor beeld wordt een token gemaakt en wordt een scope toewijzing gemaakt met de volgende machtigingen voor de `samples/hello-world` opslag plaats: `content/write` en `content/read` .

1. Navigeer in de portal naar het container register.
1. Selecteer onder **opslagplaats machtigingen** de optie **tokens (preview) > + toevoegen**.

      :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-add.png" alt-text="Token maken in de portal":::
1. Voer een token naam in.
1. Selecteer onder **Scope toewijzing** de optie **nieuwe maken**.
1. De scope toewijzing configureren:
    1. Voer een naam en beschrijving in voor de bereik toewijzing. 
    1. Onder **opslag** plaatsen, Enter en `samples/hello-world` onder **machtigingen** selecteert u  `content/read` en `content/write` . Selecteer vervolgens **+ toevoegen**.  

        :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-scope-map-add.png" alt-text="Scope toewijzing maken in de portal":::

    1. Nadat u opslag plaatsen en machtigingen hebt toegevoegd, selecteert u **toevoegen** om de scope toewijzing toe te voegen.
1. Accepteer de standaard token **status** **ingeschakeld** en selecteer vervolgens **maken**.

Nadat het token is gevalideerd en gemaakt, worden de token details weer gegeven in het scherm **tokens** .

### <a name="add-token-password"></a>Token wachtwoord toevoegen

Als u een token wilt gebruiken dat is gemaakt in de portal, moet u een wacht woord genereren. U kunt één of twee wacht woorden genereren en een verval datum instellen voor elke groep. 

1. Navigeer in de portal naar het container register.
1. Selecteer onder **opslagplaats machtigingen** de optie **tokens (voor beeld)** en selecteer een token.
1. Selecteer in de details van het token **Wachtwoord1** of **password2** en selecteer het pictogram genereren.
1. In het scherm wacht woord stelt u desgewenst een verval datum voor het wacht woord in en selecteert u **genereren**. Het is raadzaam om een verval datum in te stellen.
1. Nadat u een wacht woord hebt gegenereerd, kopieert u het naar een veilige locatie en slaat u deze op. U kunt een gegenereerd wacht woord niet ophalen nadat het scherm is gesloten, maar er kan wel een nieuwe worden gegenereerd.

    :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-password.png" alt-text="Token wachtwoord maken in de portal":::

## <a name="authenticate-with-token"></a>Verifiëren met token

Wanneer een gebruiker of service een token gebruikt voor verificatie met het doel register, geeft het de token naam als een gebruikers naam en een van de gegenereerde wacht woorden. 

De verificatie methode is afhankelijk van de geconfigureerde actie of acties die zijn gekoppeld aan het token.

|Bewerking  |Verificatie uitvoeren  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete` in azure CLI<br/><br/>Voorbeeld: `az acr repository delete --name myregistry --repository myrepo --username MyToken --password xxxxxxxxxx`|
  |`content/read`     |  `docker login`<br/><br/>`az acr login` in azure CLI<br/><br/>Voorbeeld: `az acr login --name myregistry --username MyToken --password xxxxxxxxxx`  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login` in azure CLI     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests` in azure CLI   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update` in azure CLI |

## <a name="examples-use-token"></a>Voor beelden: token gebruiken

In de volgende voor beelden wordt het token gebruikt dat eerder in dit artikel is gemaakt om algemene bewerkingen uit te voeren op een opslag plaats: push-en pull-installatie kopieën, verwijderen van afbeeldingen en List opslagplaats Tags. Het token is in eerste instantie ingesteld met push-machtigingen ( `content/write` en `content/read` acties) op de `samples/hello-world` opslag plaats.

### <a name="pull-and-tag-test-images"></a>Test installatie kopieën voor pull en Tags

Voor de volgende voor beelden haalt u open bare `hello-world` en `nginx` installatie kopieën van micro soft container Registry en labelt u deze voor uw REGI ster en opslag plaats.

```bash
docker pull mcr.microsoft.com/hello-world
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
docker tag mcr.microsoft.com/hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx:v1
```

### <a name="authenticate-using-token"></a>Verifiëren met token

Voer uit `docker login` of `az acr login` voor verificatie met het REGI ster om installatie kopieën te pushen of te halen. Geef de token naam op als gebruikers naam en geef een wacht woord op. Het token moet de `Enabled` status hebben.

Het volgende voor beeld is opgemaakt voor de bash-shell en biedt de waarden met behulp van omgevings variabelen.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

Bij uitvoer moet de verificatie slagen worden weer gegeven:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Installatiekopieën naar het register pushen

Na een geslaagde aanmelding, probeert u de gelabelde installatie kopieën naar het REGI ster te pushen. Omdat het token machtigingen heeft om installatie kopieën naar de `samples/hello-world` opslag plaats te pushen, lukt de volgende push:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Het token heeft geen machtigingen voor de `samples/nginx` opslag plaats, dus de volgende push poging mislukt met een fout die vergelijkbaar is met `requested access to the resource is denied` :

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

### <a name="update-token-permissions"></a>Token machtigingen bijwerken

Als u de machtigingen van een token wilt bijwerken, werkt u de machtigingen bij in de bijbehorende toewijzing van het bereik. De bijgewerkte Scope toewijzing wordt direct toegepast op alle bijbehorende tokens. 

U kunt bijvoorbeeld bijwerken `MyToken-scope-map` met `content/write` en `content/read` acties in de `samples/ngnx` opslag plaats en de `content/write` actie verwijderen uit de `samples/hello-world` opslag plaats.  

Als u de Azure CLI wilt gebruiken, voert u [AZ ACR Scope-map Update][az-acr-scope-map-update] uit om de scope toewijzing bij te werken:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/write content/read \
  --remove-repository samples/hello-world content/write 
```

In Azure Portal:

1. Navigeer naar het container register.
1. Onder **opslagplaats machtigingen** selecteert u **Scope toewijzingen (preview)** en selecteert u de scope toewijzing die u wilt bijwerken.
1. Onder **opslag** plaatsen, Enter en `samples/nginx` onder **machtigingen** selecteert u `content/read` en `content/write` . Selecteer vervolgens **+ toevoegen**.
1. Onder **opslag** plaatsen selecteert u `samples/hello-world` en selecteert u onder **machtigingen** de optie selectief `content/write` . Selecteer vervolgens **Opslaan**.

Nadat de scope toewijzing is bijgewerkt, is de volgende push geslaagd:

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

Omdat de scope toewijzing alleen de `content/read` machtiging voor de `samples/hello-world` opslag plaats heeft, mislukt een push poging naar de `samples/hello-world` opslag plaats nu:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Het ophalen van installatie kopieën van beide opslag plaatsen slaagt, omdat de scope toewijzing `content/read` machtigingen voor beide opslag plaatsen biedt:

```bash
docker pull myregistry.azurecr.io/samples/nginx:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Installatiekopieën verwijderen

Werk de scope toewijzing bij door de `content/delete` actie toe te voegen aan de `nginx` opslag plaats. Met deze actie kunnen installatie kopieën in de opslag plaats worden verwijderd of kan de hele opslag plaats worden verwijderd.

Voor een beknopt overzicht worden alleen de opdracht [AZ ACR Scope-map Update][az-acr-scope-map-update] weer gegeven om de scope toewijzing bij te werken:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/delete
``` 

Zie de [vorige sectie](#update-token-permissions)als u de scope toewijzing wilt bijwerken met behulp van de portal.

Gebruik de volgende opdracht [AZ ACR repository delete][az-acr-repository-delete] voor het verwijderen van de `samples/nginx` opslag plaats. Als u installatie kopieën of opslag plaatsen wilt verwijderen, geeft u de naam en het wacht woord van het token door aan de opdracht. In het volgende voor beeld worden de omgevings variabelen gebruikt die eerder in het artikel zijn gemaakt:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/nginx \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Opslag plaats-Tags weer geven 

Werk de scope toewijzing bij door de `metadata/read` actie toe te voegen aan de `hello-world` opslag plaats. Met deze actie kunnen manifest-en label gegevens in de opslag plaats worden gelezen.

Voor een beknopt overzicht worden alleen de opdracht [AZ ACR Scope-map Update][az-acr-scope-map-update] weer gegeven om de scope toewijzing bij te werken:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/hello-world metadata/read 
```  

Zie de [vorige sectie](#update-token-permissions)als u de scope toewijzing wilt bijwerken met behulp van de portal.

Voor het lezen van meta gegevens in de `samples/hello-world` opslag plaats voert u de opdracht [AZ ACR repository show-manifests][az-acr-repository-show-manifests] of [AZ ACR repository show-Tags][az-acr-repository-show-tags] uit. 

Geef de naam en het wacht woord van het token door aan een van de opdrachten om meta gegevens te lezen. In het volgende voor beeld worden de omgevings variabelen gebruikt die eerder in het artikel zijn gemaakt:

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

Voorbeelduitvoer:

```console
[
  "v1"
]
```

## <a name="manage-tokens-and-scope-maps"></a>Tokens en Scope toewijzingen beheren

### <a name="list-scope-maps"></a>Lijst met bereik toewijzingen

Gebruik de opdracht [AZ ACR Scope-map list][az-acr-scope-map-list] of het scherm **Scope Maps (preview)** in de portal om een lijst weer te geven met alle scope toewijzingen die in een REGI ster zijn geconfigureerd. Bijvoorbeeld:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

De uitvoer bestaat uit drie door het systeem gedefinieerde bereik toewijzingen en andere bereik toewijzingen die door u zijn gegenereerd. Tokens kunnen worden geconfigureerd met een van deze scope toewijzingen.

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Details van token weer geven

Als u de details van een token, zoals de status en de verval datum van het wacht woord, wilt weer geven, voert u de opdracht [AZ ACR token show][az-acr-token-show] uit of selecteert u het token in het scherm **tokens (preview)** in de portal. Bijvoorbeeld:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Gebruik de opdracht [AZ ACR token List][az-acr-token-list] of het scherm **tokens (preview)** in de portal om een lijst weer te geven met alle tokens die in een REGI ster zijn geconfigureerd. Bijvoorbeeld:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="regenerate-token-passwords"></a>Token wachtwoorden opnieuw genereren

Als u geen token wachtwoord hebt gegenereerd of als u nieuwe wacht woorden wilt genereren, voert u de opdracht [AZ ACR token Credential generate][az-acr-token-credential-generate] uit. 

In het volgende voor beeld wordt een nieuwe waarde voor Wachtwoord1 voor het *MyToken* -token gegenereerd, met een verval periode van 30 dagen. Het wacht woord wordt opgeslagen in de omgevings variabele `TOKEN_PWD` . Dit voor beeld is opgemaakt voor de bash-shell.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --expiration-in-days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Als u de Azure Portal wilt gebruiken om een token wachtwoord te genereren, raadpleegt u de stappen in [Create token-Portal](#create-token---portal) eerder in dit artikel.

### <a name="update-token-with-new-scope-map"></a>Token bijwerken met een nieuwe scope toewijzing

Als u een token wilt bijwerken met een andere scope toewijzing, voert u [AZ ACR token update][az-acr-token-update] uit en geeft u de nieuwe scope toewijzing op. Bijvoorbeeld:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

Selecteer in de portal op het scherm **tokens (voor beeld)** het token en selecteer een andere scope toewijzing onder **Scope toewijzing**.

> [!TIP]
> Nadat u een token met een nieuwe scope toewijzing hebt bijgewerkt, wilt u mogelijk nieuwe token wachtwoorden genereren. Gebruik de opdracht [AZ ACR token Credential generate][az-acr-token-credential-generate] of Genereer een token wachtwoord in de Azure Portal.

## <a name="disable-or-delete-token"></a>Token uitschakelen of verwijderen

Mogelijk moet u het gebruik van de token referenties voor een gebruiker of service tijdelijk uitschakelen. 

Voer de opdracht [AZ ACR token update][az-acr-token-update] uit met behulp van de Azure CLI om het `status` volgende in te stellen `disabled` :

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

Selecteer in de portal het token in het scherm **tokens (voor beeld)** en selecteer **uitgeschakeld** onder **status**.

Als u een token wilt verwijderen om de toegang door iedereen die de referenties gebruikt permanent ongeldig te maken, voert u de opdracht [AZ ACR token delete][az-acr-token-delete] uit. 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

Selecteer in de portal het token in het scherm **tokens (voor beeld)** en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

* Als u Scope toewijzingen en tokens wilt beheren, gebruikt u aanvullende opdrachten in de opdracht [AZ ACR Scope-map][az-acr-scope-map] en [AZ ACR token][az-acr-token] Command groups.
* Zie het [verificatie overzicht](container-registry-authentication.md) voor andere opties voor verificatie met een Azure container Registry, inclusief het gebruik van een Azure Active Directory identiteit, een service-principal of een beheerders account.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az-acr-repository-show-manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az-acr-repository-delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-list]: /cli/azure/acr/token/#az-acr-token-list
[az-acr-token-delete]: /cli/azure/acr/token/#az-acr-token-delete
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
