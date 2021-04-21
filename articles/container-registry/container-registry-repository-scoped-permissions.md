---
title: Machtigingen voor opslagplaatsen in Azure Container Registry
description: Een token maken met machtigingen die zijn gericht op specifieke opslagplaatsen in een Premium-register om afbeeldingen op te halen of te pushen, of om andere acties uit te voeren
ms.topic: article
ms.date: 02/04/2021
ms.openlocfilehash: 0ac479b696a377509cee6459efd8bbb9de940d2a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781393"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Een token maken met machtigingen binnen het bereik van de opslagplaats

In dit artikel wordt beschreven hoe u tokens en bereikkaarten maakt om de toegang tot specifieke opslagplaatsen in uw containerregister te beheren. Door tokens te maken, kan een registereigenaar gebruikers of services beperkte toegang bieden tot opslagplaatsen voor het pullen of pushen van afbeeldingen of het uitvoeren van andere acties. Een token biedt meer fijngranenseerde [](container-registry-authentication.md)machtigingen dan andere opties voor registerverificatie, waarmee machtigingen worden beperkt tot een heel register. 

Scenario's voor het maken van een token zijn:

* Toestaan dat IoT-apparaten met afzonderlijke tokens een afbeelding uit een opslagplaats halen
* Een externe organisatie voorzien van machtigingen voor een specifieke opslagplaats 
* Beperk de toegang tot de opslagplaats tot verschillende gebruikersgroepen in uw organisatie. U kunt bijvoorbeeld schrijf- en leestoegang bieden aan ontwikkelaars die afbeeldingen bouwen die zijn gericht op specifieke opslagplaatsen, en leestoegang bieden aan teams die vanuit deze opslagplaatsen implementeren.

Deze functie is beschikbaar in de **servicelaag Premium** Container Registry. Zie Azure Container Registry servicelagen voor meer informatie [over registerservicelagen en -limieten.](container-registry-skus.md)

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-versie en er [gelden enkele beperkingen.](#preview-limitations) Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="preview-limitations"></a>Preview-beperkingen

* U kunt momenteel geen machtigingen binnen het bereik van de opslagplaats toewijzen aan Azure Active Directory identiteit, zoals een service-principal of beheerde identiteit.
* U kunt geen bereikkaart maken in een register dat is ingeschakeld voor [anonieme pull-toegang.](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)

## <a name="concepts"></a>Concepten

Als u machtigingen binnen het bereik van de opslagplaats wilt configureren, maakt u een *token* met een *gekoppelde bereikkaart*. 

* Met **een token** en een gegenereerd wachtwoord kan de gebruiker zich verifiëren bij het register. U kunt een vervaldatum voor een tokenwachtwoord instellen of een token op elk moment uitschakelen.  

  Na de authenticatie met een token kan de  gebruiker of service een of meer acties uitvoeren die zijn beperkt tot een of meer opslagplaatsen.

  |Bewerking  |Beschrijving  | Voorbeeld |
  |---------|---------|--------|
  |`content/delete`    | Gegevens uit de opslagplaats verwijderen  | Een opslagplaats of manifest verwijderen |
  |`content/read`     |  Gegevens lezen uit de opslagplaats |  Een artefact pullen |
  |`content/write`     |  Gegevens naar de opslagplaats schrijven     | Gebruiken met om `content/read` een artefact te pushen |
  |`metadata/read`    | Metagegevens lezen uit de opslagplaats   | Tags of manifesten op een lijst zetten |
  |`metadata/write`     |  Metagegevens naar de opslagplaats schrijven  | Lees-, schrijf- of verwijderbewerkingen in- of uitschakelen |

* Een **bereikkaart** groepeert de machtigingen voor de opslagplaats die u op een token toe passen en kan opnieuw worden toegepast op andere tokens. Elk token is gekoppeld aan één bereikkaart. 

   Met een bereikkaart:

    * Meerdere tokens met identieke machtigingen voor een set opslagplaatsen configureren
    * Tokenmachtigingen bijwerken wanneer u opslagplaatsacties toevoegt of verwijdert in de bereikkaart of een andere bereikkaart toewijst 

  Azure Container Registry biedt ook verschillende door het systeem gedefinieerde bereikkaarten die u kunt toepassen bij het maken van tokens. De machtigingen van door het systeem gedefinieerde bereikkaarten zijn van toepassing op alle opslagplaatsen in uw register.

In de volgende afbeelding ziet u de relatie tussen tokens en bereikkaarten. 

![Registertokens en bereikkaarten](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Vereisten

* **Azure CLI:** voor opdrachtvoorbeelden van Azure CLI-opdrachten in dit artikel is Azure CLI versie 2.17.0 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.
* **Docker:** voor verificatie met het register voor het pullen of pushen van installatie installatie van installatiegegevens hebt u een lokale Docker-installatie nodig. Docker biedt installatie-instructies voor de systemen [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) en [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Containerregister:** als u er nog geen hebt, maakt u een Premium-containerregister in uw Azure-abonnement of upgradet u een bestaand register. Gebruik bijvoorbeeld de [Azure Portal](container-registry-get-started-portal.md) of de [Azure CLI](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Token maken - CLI

### <a name="create-token-and-specify-repositories"></a>Token maken en opslagplaatsen opgeven

Maak een token met de [opdracht az acr token create.][az-acr-token-create] Wanneer u een token maakt, kunt u een of meer opslagplaatsen en bijbehorende acties voor elke opslagplaats opgeven. De opslagplaatsen hoeven nog niet in het register te staan. Zie de volgende sectie om een token te maken door een bestaande bereikkaart [op te geven.](#create-token-and-specify-scope-map)

In het volgende voorbeeld wordt een token gemaakt in het register *myregistry* met de volgende machtigingen voor `samples/hello-world` de repo: `content/write` en `content/read` . Met de opdracht wordt de standaard-tokenstatus standaard ingesteld op , maar `enabled` u kunt de status op elk moment bijwerken naar `disabled` .

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

In de uitvoer ziet u details over het token. Standaard worden er twee wachtwoorden gegenereerd die niet verlopen, maar u kunt eventueel een vervaldatum instellen. Het is raadzaam om de wachtwoorden op een veilige plaats op te slaan voor later gebruik voor verificatie. De wachtwoorden kunnen niet opnieuw worden opgehaald, maar er kunnen nieuwe worden gegenereerd.

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
> Zie [Tokenwachtwoorden](#regenerate-token-passwords) opnieuw genereren verder in dit artikel als u tokenwachtwoorden en verloopperioden opnieuw wilt genereren.

De uitvoer bevat details over de bereikkaart die de opdracht heeft gemaakt. U kunt de bereikkaart, hier genaamd , gebruiken `MyToken-scope-map` om dezelfde opslagplaatsacties toe te passen op andere tokens. Of werk de bereikkaart later bij om de machtigingen van de bijbehorende tokens te wijzigen.

### <a name="create-token-and-specify-scope-map"></a>Token maken en bereikkaart opgeven

Een andere manier om een token te maken, is door een bestaande bereikkaart op te geven. Als u nog geen bereikkaart hebt, maakt u er eerst een door opslagplaatsen en bijbehorende acties op te geven. Geef vervolgens de bereikkaart op bij het maken van een token. 

Gebruik de opdracht az [acr scope-map create][az-acr-scope-map-create] om een bereikkaart te maken. Met de volgende opdracht maakt u een bereikkaart met dezelfde machtigingen voor de `samples/hello-world` eerder gebruikte opslagplaats. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Voer [az acr token create uit om][az-acr-token-create] een token te maken en geef de *MyScopeMap-bereikkaart* op. Net als in het vorige voorbeeld stelt de opdracht de standaard-tokenstatus in op `enabled` .

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

In de uitvoer ziet u details over het token. Standaard worden er twee wachtwoorden gegenereerd. Het is raadzaam om de wachtwoorden op een veilige plaats op te slaan voor later gebruik voor verificatie. De wachtwoorden kunnen niet opnieuw worden opgehaald, maar er kunnen nieuwe worden gegenereerd.

> [!NOTE]
> Zie [Tokenwachtwoorden](#regenerate-token-passwords) opnieuw genereren verder in dit artikel als u tokenwachtwoorden en verloopperioden opnieuw wilt genereren.

## <a name="create-token---portal"></a>Token maken - portal

U kunt de Azure Portal tokens en bereikkaarten maken. Net als bij de CLI-opdracht kunt u een bestaande bereikkaart toepassen of een bereikkaart maken wanneer u een token maakt door een of meer opslagplaatsen en bijbehorende acties `az acr token create` op te geven. De opslagplaatsen hoeven nog niet in het register te staan. 

In het volgende voorbeeld wordt een token gemaakt en een bereikkaart gemaakt met de volgende machtigingen voor de `samples/hello-world` opslagplaats: `content/write` en `content/read` .

1. Navigeer in de portal naar uw containerregister.
1. Selecteer **tokens (preview)** onder Opslagplaatsmachtigingen **> +Toevoegen.**

      :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-add.png" alt-text="Token maken in de portal":::
1. Voer een tokennaam in.
1. Selecteer **onder Bereikkaart** de optie **Nieuwe maken.**
1. Configureer de bereikkaart:
    1. Voer een naam en beschrijving in voor de bereikkaart. 
    1. Voer **onder Opslagplaatsen in** en selecteer en onder `samples/hello-world`  `content/read` `content/write` Machtigingen. Selecteer vervolgens **+Toevoegen.**  

        :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-scope-map-add.png" alt-text="Een bereikkaart maken in de portal":::

    1. Nadat u opslagplaatsen en machtigingen hebt toegevoegd, selecteert u **Toevoegen om** de bereikkaart toe te voegen.
1. Accepteer de standaard-tokenstatus **Ingeschakeld en** selecteer vervolgens **Maken.** 

Nadat het token is gevalideerd en gemaakt, worden tokendetails weergegeven in het **scherm Tokens.**

### <a name="add-token-password"></a>Tokenwachtwoord toevoegen

Als u een token wilt gebruiken dat in de portal is gemaakt, moet u een wachtwoord genereren. U kunt een of twee wachtwoorden genereren en voor elk wachtwoord een vervaldatum instellen. 

1. Navigeer in de portal naar uw containerregister.
1. Selecteer **tokens** **(preview)** onder Opslagplaatsmachtigingen en selecteer een token.
1. Selecteer in de tokendetails **password1** of **password2** en selecteer het pictogram Genereren.
1. Stel in het wachtwoordscherm desgewenst een vervaldatum in voor het wachtwoord en selecteer **Genereren.** Het is raadzaam om een vervaldatum in te stellen.
1. Nadat u een wachtwoord hebt gegenereerd, kopieert u het en sla u het op een veilige locatie op. U kunt geen gegenereerd wachtwoord ophalen nadat u het scherm hebt gesloten, maar u kunt wel een nieuw wachtwoord genereren.

    :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-password.png" alt-text="Tokenwachtwoord maken in portal":::

## <a name="authenticate-with-token"></a>Verifiëren met token

Wanneer een gebruiker of service een token gebruikt om te verifiëren bij het doelregister, wordt de tokennaam als gebruikersnaam en een van de gegenereerde wachtwoorden verstrekt. 

De verificatiemethode is afhankelijk van de geconfigureerde actie of acties die aan het token zijn gekoppeld.

|Bewerking  |Verificatie  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete` in Azure CLI<br/><br/>Voorbeeld: `az acr repository delete --name myregistry --repository myrepo --username MyToken --password xxxxxxxxxx`|
  |`content/read`     |  `docker login`<br/><br/>`az acr login` in Azure CLI<br/><br/>Voorbeeld: `az acr login --name myregistry --username MyToken --password xxxxxxxxxx`  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login` in Azure CLI     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests` in Azure CLI   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update` in Azure CLI |

## <a name="examples-use-token"></a>Voorbeelden: Token gebruiken

In de volgende voorbeelden wordt het token gebruikt dat eerder in dit artikel is gemaakt om veelvoorkomende bewerkingen uit te voeren op een opslagplaats: push- en pull-afbeeldingen, afbeeldingen verwijderen en tags van opslagplaatsen opsnuiten. Het token is in eerste instantie ingesteld met pushmachtigingen ( `content/write` en `content/read` acties) voor de `samples/hello-world` opslagplaats.

### <a name="pull-and-tag-test-images"></a>Testafbeeldingen pullen en taggen

Voor de volgende voorbeelden haalt u openbare en afbeeldingen op uit Microsoft Container Registry en tagt u deze `hello-world` voor uw register en `nginx` opslagplaats.

```bash
docker pull mcr.microsoft.com/hello-world
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
docker tag mcr.microsoft.com/hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx:v1
```

### <a name="authenticate-using-token"></a>Verifiëren met behulp van token

Voer `docker login` uit of om te verifiëren met het register om afbeeldingen te `az acr login` pushen of op te halen. Geef de tokennaam op als gebruikersnaam en geef een van de wachtwoorden op. Het token moet de `Enabled` status hebben.

Het volgende voorbeeld is opgemaakt voor de bash-shell en biedt de waarden met behulp van omgevingsvariabelen.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

De uitvoer moet geslaagde verificatie tonen:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Installatiekopieën naar het register pushen

Probeer na een geslaagde aanmelding de getagde afbeeldingen naar het register te pushen. Omdat het token machtigingen heeft om afbeeldingen naar de opslagplaats te `samples/hello-world` pushen, slaagt de volgende push:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Het token heeft geen machtigingen voor de repo, dus de volgende pushpoging mislukt met een fout die vergelijkbaar is `samples/nginx` met `requested access to the resource is denied` :

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

### <a name="update-token-permissions"></a>Tokenmachtigingen bijwerken

Als u de machtigingen van een token wilt bijwerken, moet u de machtigingen in de bijbehorende bereikkaart bijwerken. De bijgewerkte bereikkaart wordt onmiddellijk toegepast op alle bijbehorende tokens. 

Werk bijvoorbeeld bij `MyToken-scope-map` met `content/write` - en `content/read` -acties voor de opslagplaats en `samples/ngnx` verwijder de actie in de `content/write` `samples/hello-world` opslagplaats.  

Als u de Azure CLI wilt gebruiken, moet [u az acr scope-map update uitvoeren om][az-acr-scope-map-update] de bereikkaart bij te werken:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/write content/read \
  --remove-repository samples/hello-world content/write 
```

In Azure Portal:

1. Navigeer naar uw containerregister.
1. Selecteer **onder Opslagplaatsmachtigingen** de optie **Bereikkaarten (preview)** en selecteer de bereikkaart die u wilt bijwerken.
1. Voer **onder Opslagplaatsen in** en selecteer en onder `samples/nginx`  `content/read` `content/write` Machtigingen. Selecteer vervolgens **+Toevoegen.**
1. Selecteer **onder Opslagplaatsen de** optie en schakel onder `samples/hello-world` **Machtigingen** de selectie `content/write` uit. Selecteer vervolgens **Opslaan**.

Na het bijwerken van de bereikkaart slaagt de volgende push:

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

Omdat de bereikkaart alleen de machtiging voor de opslagplaats heeft, mislukt nu een pushpoging naar `content/read` `samples/hello-world` de `samples/hello-world` opslagplaats:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Het binnenhalen van afbeeldingen uit beide opslagplaatsen slaagt, omdat de bereikkaart machtigingen `content/read` biedt voor beide opslagplaatsen:

```bash
docker pull myregistry.azurecr.io/samples/nginx:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Installatiekopieën verwijderen

Werk de bereikkaart bij door de actie `content/delete` toe te voegen aan de `nginx` opslagplaats. Met deze actie kunt u afbeeldingen in de opslagplaats verwijderen of de hele opslagplaats verwijderen.

Om het kort te houden, laten we alleen de [opdracht az acr scope-map update zien][az-acr-scope-map-update] om de bereikkaart bij te werken:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/delete
``` 

Zie de vorige sectie om de bereikkaart bij te werken met behulp van [de portal.](#update-token-permissions)

Gebruik de volgende [opdracht az acr repository delete om][az-acr-repository-delete] de opslagplaats te `samples/nginx` verwijderen. Als u afbeeldingen of opslagplaatsen wilt verwijderen, geeft u de naam en het wachtwoord van het token door aan de opdracht . In het volgende voorbeeld worden de omgevingsvariabelen gebruikt die eerder in het artikel zijn gemaakt:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/nginx \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Tags voor de repo tonen 

Werk de bereikkaart bij door de actie `metadata/read` toe te voegen aan de `hello-world` opslagplaats. Met deze actie kunnen manifest- en taggegevens in de opslagplaats worden gelezen.

Om het kort te houden, laten we alleen de [opdracht az acr scope-map update zien][az-acr-scope-map-update] om de bereikkaart bij te werken:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/hello-world metadata/read 
```  

Zie de vorige sectie om de bereikkaart bij te werken met behulp van [de portal.](#update-token-permissions)

Voer de opdracht `samples/hello-world` [az acr repository show-manifests][az-acr-repository-show-manifests] of [az acr repository show-tags][az-acr-repository-show-tags] uit om metagegevens in de opslagplaats te lezen. 

Als u metagegevens wilt lezen, geeft u de naam en het wachtwoord van het token door aan beide opdrachten. In het volgende voorbeeld worden de omgevingsvariabelen gebruikt die eerder in het artikel zijn gemaakt:

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

## <a name="manage-tokens-and-scope-maps"></a>Tokens en bereikkaarten beheren

### <a name="list-scope-maps"></a>Bereikkaarten op een lijst zetten

Gebruik de [opdracht az acr scope-map list][az-acr-scope-map-list] of het scherm Bereikkaarten **(preview)** in de portal om alle bereikkaarten weer te geven die in een register zijn geconfigureerd. Bijvoorbeeld:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

De uitvoer bestaat uit de drie door het systeem gedefinieerde bereikkaarten en andere bereikkaarten die door u zijn gegenereerd. Tokens kunnen worden geconfigureerd met elk van deze bereikkaarten.

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Tokendetails tonen

Als u de details van een token wilt weergeven, zoals de status en vervaldatums van het wachtwoord, moet u de opdracht [az acr token show][az-acr-token-show] uitvoeren of het token selecteren in het scherm **Tokens (preview)** in de portal. Bijvoorbeeld:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Gebruik de [opdracht az acr token list][az-acr-token-list] of het scherm Tokens **(preview)** in de portal om alle tokens weer te geven die in een register zijn geconfigureerd. Bijvoorbeeld:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="regenerate-token-passwords"></a>Tokenwachtwoorden opnieuw genereren

Als u geen tokenwachtwoord hebt gegenereerd of als u nieuwe wachtwoorden wilt genereren, voer dan de opdracht [az acr token credential generate][az-acr-token-credential-generate] uit. 

In het volgende voorbeeld wordt een nieuwe waarde voor password1 gegenereerd voor het *MyToken-token,* met een verloopperiode van 30 dagen. Het wachtwoord wordt op opslag in de omgevingsvariabele `TOKEN_PWD` . Dit voorbeeld is opgemaakt voor de bash-shell.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --expiration-in-days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Als u de Azure Portal om een tokenwachtwoord te genereren, bekijkt u de stappen in [Token](#create-token---portal) maken - portal eerder in dit artikel.

### <a name="update-token-with-new-scope-map"></a>Token bijwerken met nieuwe bereikkaart

Als u een token met een andere bereikkaart wilt bijwerken, moet u [az acr token update uitvoeren][az-acr-token-update] en de nieuwe bereikkaart opgeven. Bijvoorbeeld:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

Selecteer in de portal op **het scherm Tokens (preview)** het token en selecteer onder **Bereikkaart** een andere bereikkaart.

> [!TIP]
> Nadat u een token hebt bijgewerkt met een nieuwe bereikkaart, wilt u mogelijk nieuwe tokenwachtwoorden genereren. Gebruik de [opdracht az acr token credential generate][az-acr-token-credential-generate] of genereer een tokenwachtwoord opnieuw in de Azure Portal.

## <a name="disable-or-delete-token"></a>Token uitschakelen of verwijderen

Mogelijk moet u het gebruik van de tokenreferenties voor een gebruiker of service tijdelijk uitschakelen. 

Voer met behulp van de Azure CLI de [opdracht az acr token update uit][az-acr-token-update] om de in te stellen op `status` `disabled` :

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

Selecteer in de portal het token in het **scherm Tokens (preview)** en selecteer **Uitgeschakeld** onder **Status.**

Als u een token wilt verwijderen om de toegang permanent ongeldig te maken door iedereen die de referenties ervan gebruikt, moet u [de opdracht az acr token delete][az-acr-token-delete] uitvoeren. 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

Selecteer in de portal het token in het **scherm Tokens (preview)** en selecteer **Verwijderen.**

## <a name="next-steps"></a>Volgende stappen

* Als u bereikkaarten en -tokens wilt beheren, gebruikt u aanvullende opdrachten in de opdrachtgroepen [az acr scope-map][az-acr-scope-map] [en az acr token.][az-acr-token]
* Zie het [verificatieoverzicht voor](container-registry-authentication.md) andere opties voor verificatie met een Azure-containerregister, waaronder het gebruik van een Azure Active Directory-identiteit, een service-principal of een beheerdersaccount.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az_acr_repository_show_tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az_acr_repository_show_manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az_acr_repository_delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az_acr_scope_map_create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az_acr_scope_map_show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az_acr_scope_map_list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az_acr_scope_map_update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az_acr_scope_map_list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az_acr_token_show
[az-acr-token-list]: /cli/azure/acr/token/#az_acr_token_list
[az-acr-token-delete]: /cli/azure/acr/token/#az_acr_token_delete
[az-acr-token-create]: /cli/azure/acr/token/#az_acr_token_create
[az-acr-token-update]: /cli/azure/acr/token/#az_acr_token_update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az_acr_token_credential_generate
