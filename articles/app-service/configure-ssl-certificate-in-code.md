---
title: Een TLS/SSL-certificaat in code gebruiken
description: Meer informatie over het gebruik van clientcertificaten in uw code. Verifieren met externe resources met een clientcertificaat of cryptografische taken met deze resources uitvoeren.
ms.topic: article
ms.date: 09/22/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 294587fde846a3774f7d74f64029e0bca00e9c08
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829400"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Een TLS/SSL-certificaat gebruiken in uw code in Azure App Service

In uw toepassingscode hebt u toegang tot de openbare of persoonlijke certificaten [die u toevoegt aan App Service](configure-ssl-certificate.md). Uw app-code kan fungeren als een client en toegang krijgen tot een externe service die certificaatverificatie vereist, of moet mogelijk cryptografische taken uitvoeren. Deze handleiding laat zien hoe u openbare of persoonlijke certificaten gebruikt in uw toepassingscode.

Deze methode voor het gebruik van certificaten in uw code maakt gebruik van de TLS-functionaliteit in App Service, waarvoor uw app zich in de **Basic-laag** of hoger moet hebben. Als uw app zich in de **gratis of** **gedeelde** laag heeft, kunt u [het certificaatbestand opnemen in uw app-opslagplaats.](#load-certificate-from-file)

Wanneer u uw TLS/SSL App Service certificaten beheert, kunt u de certificaten en uw toepassingscode afzonderlijk onderhouden en uw gevoelige gegevens beveiligen.

## <a name="prerequisites"></a>Vereisten

Voor het volgen van deze instructiegids:

- [Een App Service-app maken](./index.yml)
- [Een certificaat toevoegen aan uw app](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>De vingerafdruk zoeken

Selecteer in het linkermenu in <a href="https://portal.azure.com" target="_blank">Azure Portal</a> de optie **App Services** >  **\<app-name>** .

Selecteer in het linkernavigatievenster van uw app **TLS/SSL-instellingen** en selecteer vervolgens Persoonlijke-sleutelcertificaten **(.pfx)** of Openbare-sleutelcertificaten **(.cer).**

Zoek het certificaat dat u wilt gebruiken en kopieer de vingerafdruk.

![De vingerafdruk van het certificaat kopiëren](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Het certificaat toegankelijk maken

Als u toegang wilt krijgen tot een certificaat in uw app-code, voegt u de vingerafdruk toe aan de app-instelling door de volgende opdracht uit te voeren `WEBSITE_LOAD_CERTIFICATES` in <a target="_blank" href="https://shell.azure.com" >de Cloud Shell:</a>

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Als u al uw certificaten toegankelijk wilt maken, stelt u de waarde in op `*` .

## <a name="load-certificate-in-windows-apps"></a>Certificaat laden in Windows-apps

De app-instelling maakt de opgegeven certificaten toegankelijk voor uw door Windows gehoste app in het `WEBSITE_LOAD_CERTIFICATES` Windows-certificaatopslag, in [Huidige gebruiker\Mijn.](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)

In C#-code hebt u toegang tot het certificaat via de vingerafdruk van het certificaat. Met de volgende code wordt een certificaat geladen met de vingerafdruk `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` .

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

In Java-code hebt u toegang tot het certificaat vanuit het windows-MIJN-opslagopslag via het veld Algemene naam onderwerp (zie [Certificaat met openbare sleutel).](https://en.wikipedia.org/wiki/Public_key_certificate) De volgende code laat zien hoe u een certificaat met een persoonlijke sleutel laadt:

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

Zie Certificaat uit bestand laden voor talen die geen ondersteuning bieden of onvoldoende ondersteuning bieden voor het [Windows-certificaatopslag.](#load-certificate-from-file)

## <a name="load-certificate-from-file"></a>Certificaat laden uit bestand

Als u een certificaatbestand moet laden dat u handmatig uploadt, is het beter om het certificaat bijvoorbeeld te uploaden met [FTPS](deploy-ftp.md) in plaats van [Git.](deploy-local-git.md) U moet gevoelige gegevens, zoals een persoonlijk certificaat, buiten het broncodebeheer houden.

> [!NOTE]
> ASP.NET en ASP.NET Core in Windows moeten toegang hebben tot het certificaatopslag, zelfs als u een certificaat uit een bestand laadt. Als u een certificaatbestand in een Windows .NET-app wilt laden, laadt u het huidige <a target="_blank" href="https://shell.azure.com" >gebruikersprofiel</a>met de volgende opdracht in de Cloud Shell :
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Deze methode voor het gebruik van certificaten in uw code maakt gebruik van de TLS-functionaliteit in App Service, waarvoor uw app zich in de **Basic-laag** of hoger moet hebben.

In het volgende C#-voorbeeld wordt een openbaar certificaat van een relatief pad in uw app geladen:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Zie de documentatie voor de respectieve taal of het webplatform voor informatie over het laden van een TLS/SSL-certificaat uit een bestand in Node.js, PHP, Python, Java of Ruby.

## <a name="load-certificate-in-linuxwindows-containers"></a>Certificaat laden in Linux-/Windows-containers

De app-instellingen maken de opgegeven certificaten toegankelijk voor uw Windows- of `WEBSITE_LOAD_CERTIFICATES` Linux-container-apps (inclusief ingebouwde Linux-containers) als bestanden. De bestanden zijn te vinden in de volgende mappen:

| Containerplatform | Openbare certificaten | Persoonlijke certificaten |
| - | - | - |
| Windows-container | `C:\appservice\certificates\public` | `C:\appservice\certificates\private` |
| Linux-container | `/var/ssl/certs` | `/var/ssl/private` |

De certificaatbestandsnamen zijn de vingerafdruk van het certificaat. 

> [!NOTE]
> App Service de certificaatpaden in Windows-containers als de volgende omgevingsvariabelen `WEBSITE_PRIVATE_CERTS_PATH` `WEBSITE_INTERMEDIATE_CERTS_PATH` , , en `WEBSITE_PUBLIC_CERTS_PATH` `WEBSITE_ROOT_CERTS_PATH` . Het is beter om naar het certificaatpad te verwijzen met de omgevingsvariabelen in plaats van het certificaatpad vast te coderen, voor het geval de certificaatpaden in de toekomst veranderen.
>

Bovendien laden [Windows Server Core-containers](configure-custom-container.md#supported-parent-images) de certificaten automatisch in het certificaatopslag, in **LocalMachine\My.** Als u de certificaten wilt laden, volgt u hetzelfde patroon als [Certificaat laden in Windows-apps.](#load-certificate-in-windows-apps) Gebruik voor Windows Nano-containers de hierboven opgegeven bestandspaden om [het certificaat rechtstreeks vanuit het bestand te laden.](#load-certificate-from-file)

De volgende C#-code laat zien hoe u een openbaar certificaat in een Linux-app laadt.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

De volgende C#-code laat zien hoe u een persoonlijk certificaat in een Linux-app laadt.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;
...
var bytes = File.ReadAllBytes("/var/ssl/private/<thumbprint>.p12");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Zie de documentatie voor de respectieve taal of het webplatform voor informatie over het laden van een TLS/SSL-certificaat uit een bestand in Node.js, PHP, Python, Java of Ruby.

## <a name="more-resources"></a>Meer bronnen

* [Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service](configure-ssl-bindings.md)
* [HTTPS afdwingen](configure-ssl-bindings.md#enforce-https)
* [TLS 1.1/1.2 afdwingen](configure-ssl-bindings.md#enforce-tls-versions)
* [Veelgestelde vragen: App Service-certificaten](./faq-configuration-and-management.md)
