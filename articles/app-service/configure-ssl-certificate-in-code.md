---
title: Een TLS/SSL-certificaat gebruiken in code
description: Meer informatie over het gebruik van client certificaten in uw code. Verifieer met externe resources met een client certificaat of voer cryptografische taken met deze uit.
ms.topic: article
ms.date: 09/22/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: b4e184f827875ebebd40ab976ef63e77ee702d49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93126036"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Een TLS/SSL-certificaat gebruiken in uw code in Azure App Service

In de toepassings code kunt u toegang krijgen tot de [open bare of persoonlijke certificaten die u toevoegt aan app service](configure-ssl-certificate.md). Uw app-code kan fungeren als een client en toegang krijgen tot een externe service waarvoor verificatie via een certificaat vereist is, of moet mogelijk cryptografische taken uitvoeren. In deze hand leiding vindt u informatie over het gebruik van open bare of persoonlijke certificaten in de code van uw toepassing.

Deze methode voor het gebruik van certificaten in uw code maakt gebruik van de TLS-functionaliteit in App Service. hiervoor moet uw app in de **Basic** -laag of hoger zijn. Als uw app zich in de **vrije** of **gedeelde** laag bevindt, kunt u [het certificaat bestand toevoegen aan de opslag plaats van uw app](#load-certificate-from-file).

Wanneer u uw TLS/SSL-certificaten App Service beheren, kunt u de certificaten en de code van uw toepassing afzonderlijk onderhouden en uw gevoelige gegevens beveiligen.

## <a name="prerequisites"></a>Vereisten

Voor het volgen van deze instructiegids:

- [Een App Service-app maken](./index.yml)
- [Een certificaat toevoegen aan uw app](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>De vinger afdruk zoeken

Selecteer in het linkermenu in <a href="https://portal.azure.com" target="_blank">Azure Portal</a> de optie **App Services** >  **\<app-name>** .

Selecteer in de linkernavigatiebalk van uw app instellingen voor **TLS/SSL** en selecteer vervolgens **certificaten voor persoonlijke sleutels (PFX)** of certificaten met een **open bare sleutel (. CER)**.

Zoek het certificaat dat u wilt gebruiken en kopieer de vinger afdruk.

![De vinger afdruk van het certificaat kopiëren](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Het certificaat toegankelijk maken

Als u toegang wilt krijgen tot een certificaat in uw app-code, voegt u de vinger afdruk toe aan de `WEBSITE_LOAD_CERTIFICATES` app-instelling door de volgende opdracht uit te voeren in de <a target="_blank" href="https://shell.azure.com" >Cloud shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Als u al uw certificaten toegankelijk wilt maken, stelt u de waarde in op `*` .

## <a name="load-certificate-in-windows-apps"></a>Certificaat in Windows-apps laden

Met de `WEBSITE_LOAD_CERTIFICATES` app-instelling worden de opgegeven certificaten toegankelijk voor uw Windows-app die is gehost in het Windows-certificaat archief in de [huidige User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

In C#-code opent u het certificaat via de vinger afdruk van het certificaat. Met de volgende code wordt een certificaat met de vinger afdruk geladen `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` .

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

In Java-code opent u het certificaat in de Store ' Windows-mijn ' met behulp van het veld algemene naam voor het onderwerp (Zie [certificaat voor open bare sleutels](https://en.wikipedia.org/wiki/Public_key_certificate)). De volgende code laat zien hoe u een certificaat van een persoonlijke sleutel kunt laden:

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

Zie [certificaat uit bestand laden](#load-certificate-from-file)voor talen die niet de ondersteuning bieden voor of het Windows-certificaat archief onvoldoende ondersteunen.

## <a name="load-certificate-from-file"></a>Certificaat uit bestand laden

Als u een certificaat bestand moet laden dat u hand matig uploadt, is het beter om het certificaat te uploaden met behulp van [FTPS](deploy-ftp.md) in plaats van [Git](deploy-local-git.md). Houd er rekening mee dat gevoelige gegevens, zoals een persoonlijk certificaat, niet in broncode beheer worden bewaard.

> [!NOTE]
> ASP.NET en ASP.NET Core in Windows moeten toegang hebben tot het certificaat archief, zelfs als u een certificaat uit een bestand laadt. Als u een certificaat bestand in een Windows .NET-app wilt laden, laadt u het huidige gebruikers profiel met de volgende opdracht in het <a target="_blank" href="https://shell.azure.com" >Cloud shell</a>:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Deze methode voor het gebruik van certificaten in uw code maakt gebruik van de TLS-functionaliteit in App Service. hiervoor moet uw app in de **Basic** -laag of hoger zijn.

In het volgende C#-voor beeld wordt een openbaar certificaat geladen vanuit een relatief pad in uw app:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Zie de documentatie voor de desbetreffende taal of het webplatform voor meer informatie over het laden van een TLS/SSL-certificaat van een bestand in Node.js, PHP, Python, Java of Ruby.

## <a name="load-certificate-in-linuxwindows-containers"></a>Certificaat in Linux/Windows-containers laden

Met de `WEBSITE_LOAD_CERTIFICATES` app-instellingen worden de opgegeven certificaten toegankelijk voor uw Windows-of Linux-container toepassingen (inclusief ingebouwde Linux-containers) als bestanden. De bestanden bevinden zich in de volgende directory's:

| Container platform | Openbare certificaten | Persoonlijke certificaten |
| - | - | - |
| Windows-container | `C:\appservice\certificates\public` | `C:\appservice\certificates\private` |
| Linux-container | `/var/ssl/certs` | `/var/ssl/private` |

De namen van de certificaat bestanden zijn de certificaat vingerafdrukken. 

> [!NOTE]
> App service de certificaat paden in te voegen in Windows-containers als de volgende omgevings variabelen,, `WEBSITE_PRIVATE_CERTS_PATH` `WEBSITE_INTERMEDIATE_CERTS_PATH` `WEBSITE_PUBLIC_CERTS_PATH` en `WEBSITE_ROOT_CERTS_PATH` . Het is beter om te verwijzen naar het certificaatpad met de omgevings variabelen in plaats van hardcoding het pad naar het certificaat, in het geval dat de certificaat paden in de toekomst worden gewijzigd.
>

Daarnaast laden [Windows Server Core-containers](configure-custom-container.md#supported-parent-images) de certificaten automatisch in het certificaat archief in **LocalMachine\My**. Als u de certificaten wilt laden, volgt u hetzelfde patroon als [certificaat laden in Windows-apps](#load-certificate-in-windows-apps). Voor containers op basis van Windows Nano gebruikt u de hierboven vermelde bestands paden om [het certificaat rechtstreeks vanuit het bestand te laden](#load-certificate-from-file).

De volgende C#-code laat zien hoe u een openbaar certificaat in een Linux-app kunt laden.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Zie de documentatie voor de desbetreffende taal of het webplatform voor meer informatie over het laden van een TLS/SSL-certificaat van een bestand in Node.js, PHP, Python, Java of Ruby.

## <a name="more-resources"></a>Meer bronnen

* [Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service](configure-ssl-bindings.md)
* [HTTPS afdwingen](configure-ssl-bindings.md#enforce-https)
* [TLS 1.1/1.2 afdwingen](configure-ssl-bindings.md#enforce-tls-versions)
* [Veelgestelde vragen: App Service-certificaten](./faq-configuration-and-management.md)