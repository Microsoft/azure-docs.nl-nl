---
title: Het maken van certificaten controleren en beheren
description: Scenario's met een groot aantal opties voor het maken, bewaken en door lopen van het maken van het certificaat met Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 02e13ce81ed2f11c0bb69015a4864c4a1ad55593
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "81430966"
---
# <a name="monitor-and-manage-certificate-creation"></a>Het maken van certificaten controleren en beheren
Van toepassing op: Azure

De scenario's/bewerkingen die in dit artikel worden beschreven, zijn:

- Een KV-certificaat aanvragen met een ondersteunde Uitgever
- Aanvraag voor in behandeling zijn ontvangen-aanvraag status is InProgress
- Aanvraag voor in behandeling zijn ontvangen-aanvraag status is voltooid
- Aanvraag voor aanvragen in behandeling ophalen is geannuleerd of is mislukt
- Aanvraag voor aanvragen in behandeling ontvangen is "verwijderd" of "overschreven"
- Maken (of importeren) als er een aanvraag in behandeling is: de status is InProgress
- Samen voegen wanneer aanvraag in behandeling wordt gemaakt met een verlener (bijvoorbeeld DigiCert)
- Een annulering aanvragen wanneer de status van de aanvraag in behandeling is.
- Een aanvraag object voor in behandeling verwijderen
- Een KV-certificaat hand matig maken
- Samen voegen wanneer een aanvraag in behandeling is gemaakt-hand matig certificaat maken

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Een KV-certificaat aanvragen met een ondersteunde Uitgever 

|Methode|Aanvraag-URI|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

In de volgende voor beelden is een object met de naam ' mydigicert ' al beschikbaar in uw sleutel kluis met de provider van de verlener als DigiCert. De uitgever van het certificaat is een entiteit die wordt weer gegeven in Azure Key Vault (KV) als een CertificateIssuer-resource. Het wordt gebruikt om informatie op te geven over de bron van een KV-certificaat; naam van de verlener, provider, referenties en andere administratieve gegevens.

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

## <a name="get-pending-request---request-status-is-inprogress"></a>Aanvraag voor in behandeling zijn ontvangen-aanvraag status is InProgress

|Methode|Aanvraag-URI|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Aanvraag
Toevoegen `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OF

Toevoegen `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

> [!NOTE]
> Als *request_id* in de query is opgegeven, fungeert deze als een filter. Als de *request_id* in de query en in het in behandeling zijnde object verschillend zijn, wordt de HTTP-status code 404 geretourneerd.

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

## <a name="get-pending-request---request-status-is-complete"></a>Aanvraag voor in behandeling zijn ontvangen-aanvraag status is voltooid

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

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Aanvraag voor aanvragen in behandeling ophalen is geannuleerd of is mislukt

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
> De waarde van de *error code* kan de fout ' certificaat verlener ' of ' aanvraag geweigerd ' zijn, op basis van respectievelijk de verlener of de gebruikers fout.

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Aanvraag voor aanvragen in behandeling ontvangen is "verwijderd" of "overschreven"
Een object dat in behandeling is, kan worden verwijderd of overschreven door een bewerking voor maken/importeren wanneer de status niet wordt uitgevoerd.

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

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Maken (of importeren) als er een aanvraag in behandeling is: de status is InProgress
Een object in behandeling heeft vier mogelijke statussen; ' InProgress ', ' geannuleerd ', ' failed ' of ' voltooid '.

Wanneer de status van een aanvraag in behandeling is, mislukt het maken (en importeren) met de HTTP-status code 409 (conflict).

Een conflict oplossen:

- Als het certificaat hand matig wordt gemaakt, kunt u het KV-certificaat volt ooien door een samen voeging of verwijdering uit te voeren in het object dat in behandeling is.

- Als het certificaat wordt gemaakt met een uitgever, kunt u wachten tot het certificaat is voltooid, mislukt of geannuleerd. U kunt ook het in behandeling zijnde object verwijderen.

> [!NOTE]
> Als u een in behandeling zijnd object verwijdert, kan de x509-certificaat aanvraag niet worden geannuleerd met de provider.

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

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Samen voegen wanneer aanvraag in behandeling is gemaakt met een uitgever
Samen voegen is niet toegestaan wanneer een object dat in behandeling is gemaakt met een verlener, maar wel is toegestaan wanneer de status van de functie InProgress is. 

Als de aanvraag om het x509-certificaat te maken mislukt of als enige reden wordt geannuleerd, en als een x509-certificaat door out-of-band-middelen kan worden opgehaald, kan een samenvoeg bewerking worden uitgevoerd om het KV-certificaat te volt ooien.

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

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Een annulering aanvragen wanneer de status van de aanvraag in behandeling is.
Een annulering kan alleen worden aangevraagd. Een aanvraag kan al dan niet worden geannuleerd. Als een aanvraag niet wordt uitgevoerd, wordt de HTTP-status 400 (ongeldige aanvraag) geretourneerd.

|Methode|Aanvraag-URI|
|------------|-----------------|
|VERZENDEN|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Aanvraag
VERZENDEN `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OF

VERZENDEN `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="delete-a-pending-request-object"></a>Een aanvraag object voor in behandeling verwijderen

> [!NOTE]
> Als u het in behandeling zijnde object verwijdert, kan de x509-certificaat aanvraag niet worden geannuleerd met de provider.

|Methode|Aanvraag-URI|
|------------|-----------------|
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Aanvraag
VERWIJDERD `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OF

VERWIJDERD `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="create-a-kv-certificate-manually"></a>Een KV-certificaat hand matig maken
U kunt een certificaat maken dat is uitgegeven door een certificerings instantie van uw keuze door een hand matig proces maken. Stel de naam van de certificaat verlener in op ' onbekend ' of geef het veld van de verlener niet op.

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

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Samen voegen wanneer een aanvraag in behandeling is gemaakt-hand matig certificaat maken

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
|x5c|Ja|matrix|\<introducing version>|X509-certificaat keten als basis 64 teken reeks matrix.|

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
