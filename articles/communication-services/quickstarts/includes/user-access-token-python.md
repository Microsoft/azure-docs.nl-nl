---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 3f49977ce5bf32f03dbb301e2d7a37449d7de4cc
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105630154"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Python](https://www.python.org/downloads/) 2,7 of 3.6 +.
- Een actieve Communication Services-resource en verbindingsreeks. [Een Communication Services-resource maken](../create-communication-resource.md).

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

1. Open uw terminal of opdrachtvenster, maak een nieuwe map voor uw app en navigeer daar naartoe.

   ```console
   mkdir access-tokens-quickstart && cd access-tokens-quickstart
   ```

1. Gebruik een teksteditor om een bestand te maken met de naam **issue-access-tokens.py** in de hoofdmap van het project en voeg de structuur voor het programma toe, waaronder de basale verwerking van uitzonderingen. In de volgende secties voegt u alle broncode voor deze quickstart toe aan dit bestand.

   ```python
   import os
   from azure.communication.identity import CommunicationIdentityClient, CommunicationUserIdentifier

   try:
      print("Azure Communication Services - Access Tokens Quickstart")
      # Quickstart code goes here
   except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### <a name="install-the-package"></a>Het pakket installeren

Terwijl u nog steeds in de toepassingsmap, installeert u de Azure Communication Services Identity SDK voor python-pakket met behulp van de `pip install` opdracht.

```console
pip install azure-communication-identity
```

## <a name="authenticate-the-client"></a>De client verifiëren

Breng een `CommunicationIdentityClient` tot stand met uw verbindingsreeks. Met de onderstaande code wordt de verbindingsreeks voor de resource opgehaald uit een omgevingsvariabele met de naam `COMMUNICATION_SERVICES_CONNECTION_STRING`. Meer informatie over het [beheren van de verbindingsreeks van uw resource](../create-communication-resource.md#store-your-connection-string).

Voeg deze code toe in het `try`-blok:

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ["COMMUNICATION_SERVICES_CONNECTION_STRING"]

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

Als u beheerde identiteit hebt ingesteld, raadpleegt u beheerde identiteiten [gebruiken](../managed-identity.md), maar u kunt ook verifiëren met beheerde identiteit.
```python
endpoint = os.environ["COMMUNICATION_SERVICES_ENDPOINT"]
client = CommunicationIdentityClient(endpoint, DefaultAzureCredential())
```

## <a name="create-an-identity"></a>Een identiteit maken

Azure Communication Services onderhoudt een lichte identiteitsmap. Gebruik de methode `create_user` om een nieuwe vermelding in de map te maken met een unieke `Id`. Sla de ontvangen identiteit op met een toewijzing aan gebruikers van uw toepassing. U kunt dit bijvoorbeeld doen door ze op te slaan in de database van uw toepassingsserver. De identiteit is later vereist voor het uitgeven van toegangstokens.

```python
identity = client.create_user()
print("\nCreated an identity with ID: " + identity.identifier)
```

## <a name="issue-access-tokens"></a>Toegangstokens uitgeven

Gebruik de methode `get_token` om een toegangstoken voor de al bestaande Communication Services-identiteit uit te geven. Met de parameter `scopes` wordt een set primitieven gedefinieerd, waarmee dit toegangstoken wordt geautoriseerd. Raadpleeg de [lijst met ondersteunde acties](../../concepts/authentication.md). Er kan een nieuw exemplaar van de parameter `CommunicationUserIdentifier` worden samengesteld op basis van de tekenreeksweergave van de Azure Communication Service-identiteit.

```python
# Issue an access token with the "voip" scope for an identity
token_result = client.get_token(identity, ["voip"])
expires_on = token_result.expires_on.strftime("%d/%m/%y %I:%M %S %p")
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

Toegangstokens zijn kortdurende referenties die opnieuw moeten worden uitgegeven. Als u dit niet doet, kan dit leiden tot onderbrekingen van de gebruikerservaring van uw toepassing. De antwoordeigenschap `expires_on` geeft de levensduur van het toegangstoken aan.

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>Een identiteit maken en binnen dezelfde aanvraag een toegangs token uitgeven

Gebruik de `create_user_and_token` methode voor het maken van een communicatie Services-identiteit en het uitgeven van een toegangs token. Met de parameter `scopes` wordt een set primitieven gedefinieerd, waarmee dit toegangstoken wordt geautoriseerd. Raadpleeg de [lijst met ondersteunde acties](../../concepts/authentication.md).

```python
# Issue an identity and an access token with the "voip" scope for the new identity
identity_token_result = client.create_user_and_token(["voip"])
identity = identity_token_result[0].identifier
token = identity_token_result[1].token
expires_on = identity_token_result[1].expires_on.strftime("%d/%m/%y %I:%M %S %p")
print("\nCreated an identity with ID: " + identity)
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token)
```

## <a name="refresh-access-tokens"></a>Toegangstokens vernieuwen

Als u een toegangstoken wilt vernieuwen, gebruikt u het `CommunicationUserIdentifier`-object om het opnieuw uit te geven:

```python
# Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identity = CommunicationUserIdentifier(existingIdentity)
token_result = client.get_token( identity, ["voip"])
```

## <a name="revoke-access-tokens"></a>Toegangstokens intrekken

In sommige gevallen kunt u toegangstokens expliciet intrekken. Wanneer de gebruiker van een toepassing bijvoorbeeld het wachtwoord wijzigt dat wordt gebruikt voor verificatie bij uw service. Met de methode `revoke_tokens` worden alle actieve toegangstokens die zijn verleend aan de identiteit ongeldig gemaakt.

```python
client.revoke_tokens(identity)
print("\nSuccessfully revoked all access tokens for identity with ID: " + identity.identifier)
```

## <a name="delete-an-identity"></a>Een identiteit verwijderen

Als u een identiteit verwijdert, worden alle actieve toegangstokens ingetrokken en wordt voorkomen dat er toegangstokens voor de identiteit worden uitgegeven. Ook wordt alle persistente inhoud verwijderd die aan de identiteit is gekoppeld.

```python
client.delete_user(identity)
print("\nDeleted the identity with ID: " + identity.identifier)
```

## <a name="run-the-code"></a>De code uitvoeren

Navigeer vanuit een console prompt naar de map met het *Issue-Access-tokens.py* -bestand en voer de volgende `python` opdracht uit om de app uit te voeren.

```console
python ./issue-access-tokens.py
```
