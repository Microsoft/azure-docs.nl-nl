---
title: 'Azure Red Hat open SHIFT met openshift 4: Configureer Azure Active Directory-verificatie met behulp van de Azure Portal en de open Shift-webconsole'
description: Meer informatie over het configureren van Azure Active Directory-verificatie voor een open ploeg cluster van Azure Red Hat met openshift 4 met behulp van de Azure Portal en de webconsole open Shift
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: Aro, open Shift, AZ Aro, Red Hat, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7f01404f63a32e3a23413b8eaca64f679f7036d8
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635159"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-portal"></a>Azure Active Directory authenticatie configureren voor een Azure Red Hat open Shift 4-cluster (Portal)

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.6.0 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli?view=azure-cli-latest) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="before-you-begin"></a>Voordat u begint

Maak de **OAuth call back-URL** van het cluster en noteer deze. Zorg ervoor dat u **Aro-RG** vervangt door de naam van de resource groep en het **Aro-cluster** met de naam van uw cluster.

> [!NOTE]
> De `AAD` sectie in de OAuth call back-URL moet overeenkomen met de naam van de OAuth ID-provider die u later gaat instellen.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
echo "OAuth callback URL: https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD"
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Een Azure Active Directory-toepassing maken voor verificatie

Meld u aan bij de Azure Portal en navigeer naar [app-registraties Blade](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)en klik vervolgens op **nieuwe registratie** om een nieuwe toepassing te maken.

Geef een naam op voor de toepassing, bijvoorbeeld **Aro-azuread-auth**, en vul de **omleidings-URI** in met de waarde van de OAUTH call back-URL die u eerder hebt opgehaald.

![Nieuwe toepassing registreren](media/aro4-ad-registerapp.png)

Navigeer naar **certificaten & geheimen** en klik op **Nieuw client geheim** en vul de details in. Noteer de sleutel waarde, omdat u deze in een later stadium gebruikt. Het is niet meer mogelijk om het opnieuw op te halen.

![Een geheim maken](media/aro4-ad-clientsecret.png)

Navigeer naar het **overzicht** en noteer de id van de **toepassing (client) ID** en de **Directory (Tenant)**. U hebt deze in een later stadium nodig.

![Toepassings-Id's (client) en Directory (Tenant) ophalen](media/aro4-ad-ids.png)

## <a name="configure-optional-claims"></a>Optionele claims configureren

Ontwikkel aars van toepassingen kunnen [optionele claims](../active-directory/develop/active-directory-optional-claims.md) in hun Azure AD-toepassingen gebruiken om op te geven welke claims ze willen in tokens die naar hun toepassing worden verzonden.

U kunt optionele claims gebruiken voor het volgende:

* Selecteer extra claims die moeten worden toegevoegd aan de tokens voor uw toepassing.
* Wijzig het gedrag van bepaalde claims die Azure AD retourneert in tokens.
* Aangepaste claims toevoegen en openen voor uw toepassing.

We configureren open Shift om de claim te gebruiken `email` en terug te vallen op `upn` om de voorkeurs gebruikersnaam in te stellen door het toevoegen `upn` van het id-token dat wordt geretourneerd door Azure Active Directory.

Navigeer naar **token configuratie (preview)** en klik op **optionele claim toevoegen**. Selecteer **id** en controleer vervolgens de **e-mail-** en **UPN** -claims.

![Scherm opname van het e-mail adres en UPN-claims die zijn toegevoegd.](media/aro4-ad-tokens.png)

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Gebruikers en groepen toewijzen aan het cluster (optioneel)

Toepassingen die zijn geregistreerd in een Azure Active Directory-Tenant (Azure AD), zijn standaard beschikbaar voor alle gebruikers van de Tenant die zijn geverifieerd. Met Azure AD kunnen Tenant beheerders en ontwikkel aars een app beperken tot een specifieke set gebruikers of beveiligings groepen in de Tenant.

Volg de instructies in de Azure Active Directory-documentatie om [gebruikers en groepen toe te wijzen aan de app](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md#app-registration).

## <a name="configure-openshift-openid-authentication"></a>Open Shift OpenID Connect-verificatie configureren

De `kubeadmin` referenties ophalen. Voer de volgende opdracht uit om het wachtwoord voor de `kubeadmin`-gebruiker te vinden.

```azurecli-interactive
az aro list-credentials \
  --name aro-cluster \
  --resource-group aro-rg
```

In de volgende voorbeeld ziet u dat het wachtwoord in `kubeadminPassword` is.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

U kunt de URL van de clusterconsole vinden door de volgende opdracht uit te voeren, die eruit ziet als `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name aro-cluster \
    --resource-group aro-rg \
    --query "consoleProfile.url" -o tsv
```

Start de console-URL in een browser en meld u aan met de referenties van `kubeadmin`.

Ga naar **beheer**, klik op **cluster instellingen** en selecteer vervolgens het tabblad **globale configuratie** . Schuif naar Select **OAuth**.

Schuif omlaag om **toevoegen** onder **id-providers** te selecteren en selecteer **OpenID Connect Connect**.
![Selecteer OpenID Connect Connect in de vervolg keuzelijst ID-providers](media/aro4-oauth-idpdrop.png)

Vul de naam in als **Aad**, de **client-id** als de **toepassings-id** en het **client geheim**. De **URL** van de uitgever heeft de volgende indeling: `https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` . Vervang de tijdelijke aanduiding door de Tenant-ID die u eerder hebt opgehaald.

![OAuth-details invullen](media/aro4-oauth-idp-1.png)

Schuif omlaag naar de sectie **claims** en werk de **Voorkeurs gebruikersnaam** bij om de waarde van de **UPN** -claim te gebruiken.

![Claim details invullen](media/aro4-oauth-idp-2.png)

## <a name="verify-login-through-azure-active-directory"></a>Aanmelden via Azure Active Directory verifiëren

Als u nu de open Shift-webconsole afmeldt en zich opnieuw probeert aan te melden, wordt een nieuwe optie weer gegeven om u aan te melden bij **Aad**. Mogelijk moet u enkele minuten wachten.

![Aanmeldings scherm met Azure Active Directory optie](media/aro4-login-2.png)
