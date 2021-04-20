---
title: Het maken van certificaten controleren en beheren
description: Scenario's die een scala aan opties demonstreren voor het maken, controleren en gebruiken van het proces voor het maken van certificaten met Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 8e4acb5195497dd31f466829b1cde301ba9696b3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751090"
---
# <a name="monitor-and-manage-certificate-creation"></a>Het maken van certificaten controleren en beheren
Van toepassing op: Azure

De scenario's/bewerkingen die in dit artikel worden beschreven, zijn:

- Een KV-certificaat aanvragen met een ondersteunde vergever
- Aanvraag in behandeling krijgen - aanvraagstatus is 'inProgress'
- Aanvraag in behandeling krijgen - aanvraagstatus is 'voltooid'
- Aanvraag in behandeling krijgen - aanvraagstatus in behandeling is 'geannuleerd' of 'mislukt'
- Aanvraag in behandeling krijgen - aanvraagstatus in behandeling is 'verwijderd' of 'overschreven'
- Maken (of importeren) wanneer aanvraag in behandeling bestaat - status is 'inProgress'
- Samenvoegen wanneer een aanvraag in behandeling wordt gemaakt met een verificer (bijvoorbeeld DigiCert)
- Een annulering aanvragen terwijl de status van de aanvraag in behandeling 'inProgress' is
- Een aanvraagobject in behandeling verwijderen
- Handmatig een KV-certificaat maken
- Samenvoegen wanneer een aanvraag in behandeling wordt gemaakt - handmatig certificaat maken

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Een KV-certificaat aanvragen met een ondersteunde vergever 

|Methode|Aanvraag-URI|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

Voor de volgende voorbeelden moet een object met de naam 'mydigicert' al beschikbaar zijn in uw sleutelkluis met de verlenerprovider als DigiCert. De certificaatverkender is een entiteit die in Azure Key Vault (KV) wordt weergegeven als een CertificateIssuer-resource. Het wordt gebruikt om informatie te geven over de bron van een KV-certificaat; naam van verlener, provider, referenties en andere beheergegevens.

### <a name="request"></a>Aanvraag

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "mydigicert",
      "cty": "OV-SSL",
    }
  }
}
```

### <a name="response"></a>Antwoord

```
StatusCode: 202, ReasonPhrase: 'Accepted'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "mydigicert"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "InProgress",
  "status_details": "Pending certificate created. Certificate request is in progress. This may take some time based on the issuer provider. Please check again later",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="get-pending-request---request-status-is-inprogress"></a>Aanvraag in behandeling krijgen - aanvraagstatus is 'inProgress'

|Methode|Aanvraag-URI|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Aanvraag
Toevoegen `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OF

Toevoegen `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

> [!NOTE]
> Als *request_id* is opgegeven in de query, fungeert deze als een filter. Als de *request_id* in de query en in het object in behandeling verschillend zijn, wordt de HTTP-statuscode 404 geretourneerd.

### <a name="response"></a>Antwoord

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "inProgress",
  "status_details": "…",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="get-pending-request---request-status-is-complete"></a>Aanvraag in behandeling krijgen - aanvraagstatus is 'voltooid'

### <a name="request"></a>Aanvraag

|Methode|Aanvraag-URI|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

Toevoegen `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OF

Toevoegen `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Antwoord

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "completed",
  "request_id": "a76827a18b63421c917da80f28e9913d",
  "target": “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"
}

```

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Aanvraag in behandeling krijgen : aanvraagstatus in behandeling is 'geannuleerd' of 'mislukt'

### <a name="request"></a>Aanvraag

|Methode|Aanvraag-URI|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

Toevoegen `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OF

Toevoegen `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Antwoord

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "failed",
  "status_details": "",
  "request_id": "a76827a18b63421c917da80f28e9913d",
  "error": {
    "code": "<errorcode>",
    "message": "<message>"
  }
}

```

> [!NOTE]
> De waarde van de *foutcode kan* respectievelijk 'Certificaatvergeverfout' of 'Aanvraag afgewezen' zijn op basis van de vereenigings- of gebruikersfout.

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Aanvraag in behandeling krijgen - aanvraagstatus in behandeling is 'verwijderd' of 'overschreven'
Een object in behandeling kan worden verwijderd of overschreven door een maak-/importbewerking wanneer de status niet 'inProgress' is.

|Methode|Aanvraag-URI|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Aanvraag
Toevoegen `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OF

Toevoegen `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Antwoord

```
StatusCode: 404, ReasonPhrase: 'Not Found'
{
  "error": {
    "code": "PendingCertificateNotFound",
    "message": "…"
  }
}

```

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Maken (of Importeren) wanneer de aanvraag in behandeling bestaat: de status is 'inProgress'
Een object in behandeling heeft vier mogelijke staten; 'inprogress', 'canceled', 'failed' of 'completed'.

Wanneer de status van een aanvraag in behandeling 'inprogress' is, mislukken maakbewerkingen (en importbewerkingen) met de HTTP-statuscode 409 (conflict).

Een conflict oplossen:

- Als het certificaat handmatig wordt gemaakt, kunt u het KV-certificaat voltooien door het object in behandeling samen te voegen of te verwijderen.

- Als het certificaat wordt gemaakt met een vergever, kunt u wachten totdat het certificaat is voltooid, mislukt of wordt geannuleerd. U kunt ook het object in behandeling verwijderen.

> [!NOTE]
> Het verwijderen van een object in behandeling kan de x509-certificaataanvraag bij de provider al dan niet annuleren.

|Methode|Aanvraag-URI|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

### <a name="request"></a>Aanvraag

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "mydigicert"
    }
  }
}
```

### <a name="response"></a>Antwoord

```
StatusCode: 409, ReasonPhrase: 'Conflict'
{
  "error": {
    "code": "Forbidden",
    "message": "A new key vault certificate can not be created or imported while a pending key vault certificate's status is inProgress."
  }
}

```

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Samenvoegen wanneer een aanvraag in behandeling wordt gemaakt met een vergever
Samenvoegen is niet toegestaan wanneer een object in behandeling wordt gemaakt met een vergever, maar is toegestaan wanneer de status 'inProgress' is. 

Als de aanvraag voor het maken van het x509-certificaat om een of andere reden mislukt of annuleert, en als een x509-certificaat kan worden opgehaald met out-of-band-middelen, kan er een samenvoegingsbewerking worden uitgevoerd om het KV-certificaat te voltooien.

|Methode|Aanvraag-URI|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Aanvraag

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

### <a name="response"></a>Antwoord

```json
StatusCode: 403, ReasonPhrase: 'Forbidden'
{
  "error": {
    "code": "Forbidden",
    "message": "Merge is forbidden on pending object created with issuer : <issuer-name> while it is in progess."
  }
}

```

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Een annulering aanvragen terwijl de status van de aanvraag in behandeling 'inProgress' is
Een annulering kan alleen worden aangevraagd. Een aanvraag kan al dan niet worden geannuleerd. Als een aanvraag niet 'inProgress' is, wordt de HTTP-status 400 (Bad Request) geretourneerd.

|Methode|Aanvraag-URI|
|------------|-----------------|
|Patch|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Aanvraag
Patch `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OF

Patch `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

```json
{
  "cancellation_requested": true
}

```

### <a name="response"></a>Antwoord

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": true,
  "status": "inProgress",
  "status_details": "…",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}
```

## <a name="delete-a-pending-request-object"></a>Een aanvraagobject in behandeling verwijderen

> [!NOTE]
> Het verwijderen van het object in behandeling kan de x509-certificaataanvraag bij de provider al dan niet annuleren.

|Methode|Aanvraag-URI|
|------------|-----------------|
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Aanvraag
Verwijderen `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OF

Verwijderen `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Antwoord

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "inProgress",
  "request_id": "a76827a18b63421c917da80f28e9913d",
}
```

## <a name="create-a-kv-certificate-manually"></a>Handmatig een KV-certificaat maken
U kunt een certificaat maken dat is uitgegeven met een ca van uw keuze via een handmatig maakproces. Stel de naam van de vergever in op 'Onbekend' of geef het veld vergever niet op.

|Methode|Aanvraag-URI|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

### <a name="request"></a>Aanvraag

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "Unknown"
    }
  }
}

```

### <a name="response"></a>Antwoord

```
StatusCode: 202, ReasonPhrase: 'Accepted'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "Unknown"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "status": "inProgress",
  "status_details": "Pending certificate created. Please Perform Merge to complete the request.",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Samenvoegen wanneer een aanvraag in behandeling wordt gemaakt - handmatig certificaat maken

|Methode|Aanvraag-URI|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Aanvraag

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

|Elementnaam|Vereist|Type|Versie|Beschrijving|
|------------------|--------------|----------|-------------|-----------------|
|x5c|Yes|matrix|\<introducing version>|X509-certificaatketen als matrix met basis 64 tekenreeksen.|

### <a name="response"></a>Antwoord

```
StatusCode: 201, ReasonPhrase: 'Created'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"
{
    "id": "https mykeyvault.vault.azure.net/certificates/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "kid": "https:// mykeyvault.vault.azure.net/keys/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "sid": " mykeyvault.vault.azure.net/secrets/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "cer": "……de34534……",
    "x5t": "n14q2wbvyXr71Pcb58NivuiwJKk",
    "attributes": {
        "enabled": true,
        "exp": 1530394215,
        "nbf": 1435699215,
        "created": 1435699919,
        "updated": 1435699919
    },
    "pending": {
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/pending"
    },
    "policy": {
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/policy",
        "key_props": {
            "exportable": false,
            "kty": "RSA",
            "key_size": 2048,
            "reuse_key": false
        },
        "secret_props": {
            "contentType": "application/x-pkcs12"
        },
        "x509_props": {
            "subject": "CN=Mycert1",
            "ekus": ["1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2"],
            "validity_months": 12
        },
        "lifetime_actions": [{
            "trigger": {
                "lifetime_percentage": 80
            },
            "action": {
                "action_type": "EmailContacts"
            }
        }],
        "issuer": {
            "name": "Unknown"
        },
        "attributes": {
            "enabled": true,
            "created": 1435699811,
            "updated": 1435699811
        }
    }
}

```
