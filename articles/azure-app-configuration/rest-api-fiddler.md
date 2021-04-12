---
title: Azure Active Directory REST API-testen met Fiddler
description: Gebruik Fiddler om de Azure-app configuratie te testen REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 1142aa25212d87c5484963cda4e172df3d1fbafc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932605"
---
# <a name="test-the-azure-app-configuration-rest-api-using-fiddler"></a>De configuratie van de Azure-app-REST API testen met Fiddler

Als u de REST API wilt testen met behulp van [Fiddler](https://www.telerik.com/fiddler), moet u de HTTP-headers opgeven die vereist zijn voor [verificatie](./rest-api-authentication-hmac.md) in uw aanvragen. U kunt als volgt Fiddler configureren voor het testen van de REST API, waarbij de verificatie headers automatisch worden gegenereerd:

1. Zorg ervoor dat TLS 1,2 een toegestaan protocol is:
    1. Ga naar **extra**  >  **Opties**  >  **https**).
    1. Zorg ervoor dat het **versleutelen van HTTPS-verkeer** wordt gecontroleerd.
    1. Voeg **TLS 1.2** toe aan de lijst met protocollen als deze niet aanwezig is.
1. Open **Fiddler-script editor** of druk op **CTRL-R** in Fiddler
1. Voeg de volgende code in de `Handlers` klasse toe vóór de `OnBeforeRequest` functie

    ```js
    static function SignRequest(oSession: Session, credential: String, secret: String) {
        var utcNow = DateTimeOffset.UtcNow.ToString("r", System.Globalization.DateTimeFormatInfo.InvariantInfo);
        var contentHash = ComputeSHA256Hash(oSession.RequestBody);
        var stringToSign = oSession.RequestMethod.ToUpperInvariant() + "\n" + oSession.PathAndQuery + "\n" + utcNow +";" + oSession.hostname + ";" + contentHash;
        var signature = ComputeHMACHash(secret, stringToSign);

        oSession.oRequest.headers["x-ms-date"] = utcNow;
        oSession.oRequest.headers["x-ms-content-sha256"] = contentHash;
        oSession.oRequest.headers["Authorization"] = "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature=" + signature;
    }

    static function ComputeSHA256Hash(content: Byte[]) {
        var sha256 = System.Security.Cryptography.SHA256.Create();
        try {
            return Convert.ToBase64String(sha256.ComputeHash(content));
        }
        finally {
            sha256.Dispose();
        }
    }

    static function ComputeHMACHash(secret: String, content: String) {
        var hmac = new System.Security.Cryptography.HMACSHA256(Convert.FromBase64String(secret));
        try {
            return Convert.ToBase64String(hmac.ComputeHash(System.Text.Encoding.ASCII.GetBytes(content)));
        }
        finally {
            hmac.Dispose();
        }
    }
    ```

1. Voeg de volgende code toe aan het einde van de `OnBeforeRequest` functie. Werk de toegangs sleutel bij zoals aangegeven in de opmerking TODO.

    ```js
    if (oSession.isFlagSet(SessionFlags.RequestGeneratedByFiddler) &&
        oSession.hostname.EndsWith(".azconfig.io", StringComparison.OrdinalIgnoreCase)) {

        // TODO: Replace the following placeholders with your access key
        var credential = "<Credential>"; // Id
        var secret = "<Secret>"; // Value

        SignRequest(oSession, credential, secret);
    }
    ```
1. [De componist van Fiddler](https://docs.telerik.com/fiddler/Generate-Traffic/Tasks/CreateNewRequest) gebruiken voor het genereren en verzenden van een aanvraag
