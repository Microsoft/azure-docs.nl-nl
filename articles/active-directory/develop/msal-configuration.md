---
title: Android MSAL-configuratie bestand | Azure
titleSuffix: Microsoft identity platform
description: Een overzicht van het configuratie bestand van de Android micro soft Authentication Library (MSAL), dat de configuratie van een toepassing vertegenwoordigt in Azure Active Directory.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: aa0ce6a5f909e67f0551c8667bb7e5c5e6d7eb04
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92275611"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>Configuratie bestand voor micro soft-verificatie bibliotheek voor Android

De Android micro soft Authentication Library (MSAL) wordt geleverd met een [JSON-standaard configuratie bestand](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) dat u kunt aanpassen om het gedrag van uw open bare client-app te definiëren voor zaken als de standaard instantie, de instanties die u gaat gebruiken, enzovoort.

In dit artikel vindt u meer informatie over de verschillende instellingen in het configuratie bestand en hoe u het configuratie bestand opgeeft dat moet worden gebruikt in uw MSAL-app.

## <a name="configuration-settings"></a>Configuratie-instellingen

### <a name="general-settings"></a>Algemene instellingen

| Eigenschap | Gegevenstype | Vereist | Notities |
|-----------|------------|-------------|-------|
| `client_id` | Tekenreeks | Ja | De client-ID van uw app op de [pagina Toepassings registratie](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `redirect_uri`   | Tekenreeks | Ja | De omleidings-URI van uw app op de [pagina Toepassings registratie](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `broker_redirect_uri_registered` | Booleaans | Nee | Mogelijke waarden: `true` , `false` |
| `authorities` | Orderverzamellijst\<Authority> | Nee | De lijst met instanties die uw app nodig heeft |
| `authorization_user_agent` | AuthorizationAgent (enum) | Nee | Mogelijke waarden: `DEFAULT` , `BROWSER` , `WEBVIEW` |
| `http` | HttpConfiguration | Nee | Configureren `HttpUrlConnection` `connect_timeout` en `read_timeout` |
| `logging` | LoggingConfiguration | Nee | Hiermee geeft u het detail niveau voor logboek registratie op. Optionele configuraties omvatten: `pii_enabled` , die een Booleaanse waarde en,,, `log_level` `ERROR` , of, accepteert,, `WARNING` `INFO` of `VERBOSE` . |

### <a name="client_id"></a>client_id

De client-ID of App-ID die is gemaakt tijdens het registreren van uw toepassing.

### <a name="redirect_uri"></a>redirect_uri

De omleidings-URI die u hebt geregistreerd tijdens het registreren van uw toepassing. Als de omleidings-URI naar een Broker-app verwijst, raadpleegt u de [omleidings-URI voor open bare client-apps](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) om te controleren of u de juiste omleidings-URI-indeling gebruikt voor uw broker-app

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Als u brokered-verificatie wilt gebruiken, `broker_redirect_uri_registered` moet de eigenschap worden ingesteld op `true` . Als de toepassing in een scenario met brokered verificatie niet de juiste indeling heeft om te praten met de Broker zoals beschreven in [omleidings-URI voor open bare client-apps](msal-client-application-configuration.md#redirect-uri-for-public-client-apps), valideert de toepassing uw omleidings-URI en genereert een uitzonde ring wanneer deze wordt gestart.

### <a name="authorities"></a>ca's

De lijst met instanties die bekend zijn en worden vertrouwd door u. Naast de instanties die hier worden vermeld, vraagt MSAL ook micro soft om een lijst met Clouds en instanties te verkrijgen die bekend zijn bij micro soft. Geef in deze lijst met instanties het type van de instantie en eventuele aanvullende optionele para meters op `"audience"` , zoals, die moeten worden uitgelijnd met de doel groep van uw app op basis van de registratie van uw app. Hier volgt een voor beeld van een lijst met instanties:

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenant_id": "contoso.com" // Provide your specific tenant ID here
    }
},
// Example AzureAD Multiple Organizations
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMultipleOrgs"
    }
},
//Example PersonalMicrosoftAccount
{
    "type": "AAD",
    "audience": {
        "type": "PersonalMicrosoftAccount"
    }
}
```

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>AAD Authority & doel groep toewijzen aan micro soft Identity platform-eind punten

| Type | Doelgroep | Tenant-id | Authority_Url | Resulterende eind punt | Notities |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADandPersonalMicrosoftAccount | | | `https://login.microsoftonline.com/common` | `common` is een Tenant alias voor waar het account zich bevindt. Zoals een specifieke Azure Active Directory Tenant of het Microsoft-account systeem. |
| AAD | AzureADMyOrg | contoso.com | | `https://login.microsoftonline.com/contoso.com` | Alleen accounts die aanwezig zijn in contoso.com kunnen een token verkrijgen. Elk geverifieerd domein of de Tenant-GUID kan worden gebruikt als Tenant-ID. |
| AAD | AzureADMultipleOrgs | | | `https://login.microsoftonline.com/organizations` | Alleen Azure Active Directory accounts kunnen met dit eind punt worden gebruikt. Micro soft-accounts kunnen lid zijn van organisaties. Voor het verkrijgen van een token met behulp van een Microsoft-account voor een resource in een organisatie, geeft u de Tenant van de organisatie op waarvan u het token wilt maken. |
| AAD | PersonalMicrosoftAccount | | | `https://login.microsoftonline.com/consumers` | Alleen micro soft-accounts kunnen dit eind punt gebruiken. |
| B2C | | | Bekijk het resulterende eind punt | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | Alleen accounts die aanwezig zijn in de contoso.onmicrosoft.com-Tenant kunnen een token verkrijgen. In dit voor beeld is het B2C-beleid onderdeel van het URL-pad van de instantie. |

> [!NOTE]
> Verificatie van de certificerings instantie kan niet worden ingeschakeld en uitgeschakeld in MSAL.
> Instanties zijn bekend als de ontwikkelaar die is opgegeven via configuratie of bekend bij micro soft via meta gegevens.
> Als MSAL een aanvraag voor een token naar een onbekende instantie ontvangt, een `MsalClientException` van de type `UnknownAuthority` resultaten.
> Brokered-verificatie werkt niet voor Azure AD B2C.

#### <a name="authority-properties"></a>Instantie-eigenschappen

| Eigenschap | Gegevenstype  | Vereist | Notities |
|-----------|-------------|-----------|--------|
| `type` | Tekenreeks | Ja | Komt overeen met de doel groep of het account type van uw app-doelen. Mogelijke waarden: `AAD` , `B2C` |
| `audience` | Object | Nee | Alleen van toepassing als type = `AAD` . Hiermee geeft u de identiteit op van uw app-doelen. De waarde van de app-registratie gebruiken |
| `authority_url` | Tekenreeks | Ja | Alleen vereist als type = `B2C` . Hiermee geeft u de CA-URL of het beleid dat door uw app moet worden gebruikt  |
| `default` | booleaans | Ja | Eén `"default":true` is vereist wanneer een of meer instanties zijn opgegeven. |

#### <a name="audience-properties"></a>Eigenschappen van doel groep

| Eigenschap | Gegevenstype  | Vereist | Notities |
|-----------|-------------|------------|-------|
| `type` | Tekenreeks | Ja | Hiermee geeft u de doel groep op die uw app wil richten. Mogelijke waarden: `AzureADandPersonalMicrosoftAccount` , `PersonalMicrosoftAccount` , `AzureADMultipleOrgs` , `AzureADMyOrg` |
| `tenant_id` | Tekenreeks | Ja | Alleen vereist wanneer `"type":"AzureADMyOrg"` . Optioneel voor andere `type` waarden. Dit kan een Tenant domein `contoso.com` , zoals of een Tenant-id, zoals `72f988bf-86f1-41af-91ab-2d7cd011db46` ) |

### <a name="authorization_user_agent"></a>authorization_user_agent

Hiermee wordt aangegeven of een Inge sloten webweergave of de standaard browser op het apparaat moet worden gebruikt bij het aanmelden bij een account of het machtigen van toegang tot een bron.

Mogelijke waarden:
- `DEFAULT`: Geeft de voor keur aan de systeem browser. Maakt gebruik van de Inge sloten webweergave als er geen browser beschikbaar is op het apparaat.
- `WEBVIEW`: Gebruik de Inge sloten webweergave.
- `BROWSER`: Gebruikt de standaard browser op het apparaat.

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

Voor clients die ondersteuning bieden voor meerdere nationale Clouds, geeft u op `true` . Het micro soft Identity-platform wordt vervolgens automatisch omgeleid naar de juiste nationale Cloud tijdens autorisatie en het aflossen van het token. U kunt de nationale cloud van het aangemelde account bepalen door de instantie te controleren die aan de is gekoppeld `AuthenticationResult` . Houd er rekening mee dat het `AuthenticationResult` National-specifieke eindpunt adres van de resource waarvoor u een token aanvraagt, niet wordt verstrekt.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Een Booleaanse waarde die aangeeft of u gebruikmaakt van een door micro soft Identity Broker compatibele in-Broker omleidings-URI. Stel `false` deze in als u de Broker niet wilt gebruiken in uw app.

Als u de AAD-instantie met de doel groep hebt ingesteld op `"MicrosoftPersonalAccount"` , wordt de Broker niet gebruikt.

### <a name="http"></a>http

Algemene instellingen voor HTTP-time-outs configureren, zoals:

| Eigenschap | Gegevenstype | Vereist | Notities |
| ---------|-----------|------------|--------|
| `connect_timeout` | int | Nee | Tijd in milliseconden |
| `read_timeout` | int | Nee | Tijd in milliseconden |

### <a name="logging"></a>logboekregistratie

De volgende algemene instellingen zijn voor logboek registratie:

| Eigenschap | Gegevenstype  | Vereist | Notities |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | booleaans | Nee | Of persoons gegevens moeten worden geverzendd |
| `log_level`   | tekenreeks | No | De logboek berichten die moeten worden uitgevoerd. De ondersteunde logboek niveaus zijn onder andere `ERROR` , `WARNING` , en `INFO` `VERBOSE` . |
| `logcat_enabled` | booleaans | Nee | Of er moet worden uitgevoerd naar log kat naast de interface voor logboek registratie |

### <a name="account_mode"></a>account_mode

Hiermee geeft u op hoeveel accounts in uw app tegelijk kunnen worden gebruikt. De mogelijke waarden zijn:

- `MULTIPLE` Prijs
- `SINGLE`

Als u een `PublicClientApplication` account modus maakt die niet overeenkomt met deze instelling, resulteert dit in een uitzonde ring.

Zie voor meer informatie over de verschillen tussen enkele en meerdere accounts [apps met één en meerdere accounts](single-multi-account.md).

### <a name="browser_safelist"></a>browser_safelist

Een lijst met browsers die compatibel zijn met MSAL. Deze browsers worden omgeleid naar aangepaste intenties op de juiste manier verwerkt. U kunt aan deze lijst toevoegen. De standaard instelling is de standaard configuratie die hieronder wordt weer gegeven.
``
## <a name="the-default-msal-configuration-file"></a>Het standaard MSAL-configuratie bestand

De standaard MSAL-configuratie die wordt geleverd met MSAL, wordt hieronder weer gegeven. U kunt de meest recente versie bekijken op [github](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json).

Deze configuratie wordt aangevuld door de waarden die u opgeeft. De waarden die u opgeeft, overschrijven de standaard instellingen.

```javascript
{
  "authorities": [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      },
      "default": true
    }
  ],
  "authorization_user_agent": "DEFAULT",
  "multiple_clouds_supported": false,
  "broker_redirect_uri_registered": false,
  "http": {
    "connect_timeout": 10000,
    "read_timeout": 30000
  },
  "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": false
  },
  "shared_device_mode_supported": false,
  "account_mode": "MULTIPLE",
  "browser_safelist": [
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "45"
    },
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "57"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "4.0"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.cloudmosa.puffinFree",
      "browser_signature_hashes": [
        "1WqG8SoK2WvE4NTYgr2550TRhjhxT-7DWxu6C_o6GrOLK6xzG67Hq7GCGDjkAFRCOChlo2XUUglLRAYu3Mn8Ag=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.duckduckgo.mobile.android",
      "browser_signature_hashes": [
        "S5Av4cfEycCvIvKPpKGjyCuAE5gZ8y60-knFfGkAEIZWPr9lU5kA7iOAlSZxaJei08s0ruDvuEzFYlmH-jAi4Q=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.explore.web.browser",
      "browser_signature_hashes": [
        "BzDzBVSAwah8f_A0MYJCPOkt0eb7WcIEw6Udn7VLcizjoU3wxAzVisCm6bW7uTs4WpMfBEJYf0nDgzTYvYHCag=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.ksmobile.cb",
      "browser_signature_hashes": [
        "lFDYx1Rwc7_XUn4KlfQk2klXLufRyuGHLa3a7rNjqQMkMaxZueQfxukVTvA7yKKp3Md3XUeeDSWGIZcRy7nouw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.microsoft.emmx",
      "browser_signature_hashes": [
        "Ivy-Rk6ztai_IudfbyUrSHugzRqAtHWslFvHT0PTvLMsEKLUIgv7ZZbVxygWy_M5mOPpfjZrd3vOx3t-cA6fVQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.browser",
      "browser_signature_hashes": [
        "FIJ3IIeqB7V0qHpRNEpYNkhEGA_eJaf7ntca-Oa_6Feev3UkgnpguTNV31JdAmpEFPGNPo0RHqdlU0k-3jWJWw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.mini.native",
      "browser_signature_hashes": [
        "TOTyHs086iGIEdxrX_24aAewTZxV7Wbi6niS2ZrpPhLkjuZPAh1c3NQ_U4Lx1KdgyhQE4BiS36MIfP6LbmmUYQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "mobi.mgeek.TunnyBrowser",
      "browser_signature_hashes": [
        "RMVoXuK1sfJZuGZ8onG1yhMc-sKiAV2NiB_GZfdNlN8XJ78XEE2wPM6LnQiyltF25GkHiPN2iKQiGwaO2bkyyQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "org.mozilla.focus",
      "browser_signature_hashes": [
        "L72dT-stFqomSY7sYySrgBJ3VYKbipMZapmUXfTZNqOzN_dekT5wdBACJkpz0C6P0yx5EmZ5IciI93Q0hq0oYA=="
      ],
      "browser_use_customTab" : false
    }
  ]
}
```
## <a name="example-basic-configuration"></a>Voor beeld van basis configuratie

In het volgende voor beeld ziet u een basis configuratie met de client-ID, omleidings-URI, of een Broker omleiding is geregistreerd en een lijst met instanties.

```javascript
{
  "client_id" : "4b0db8c2-9f26-4417-8bde-3f0e3656f8e0",
  "redirect_uri" : "msauth://com.microsoft.identity.client.sample.local/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      }
      "default": true
    }
  ]
}
```

## <a name="how-to-use-a-configuration-file"></a>Een configuratie bestand gebruiken

1. Een configuratie bestand maken. U wordt aangeraden om uw aangepaste configuratie bestand te maken in `res/raw/auth_config.json` . Maar u kunt het overal waar u maar wilt.
2. Vertel MSAL waar de configuratie moet worden gezocht wanneer u de maakt `PublicClientApplication` . Bijvoorbeeld:

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
