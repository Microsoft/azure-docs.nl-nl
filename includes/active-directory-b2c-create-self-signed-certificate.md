---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 41d9962657aa81dbe34a52302d1b68ec655f2893
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095286"
---
Als u nog geen certificaat hebt, kunt u een zelfondertekend certificaat gebruiken. Een zelfondertekend certificaat is een beveiligings certificaat dat niet is ondertekend door een certificerings instantie (CA) en biedt geen beveiligings garanties van een certificaat dat is ondertekend door een CERTIFICERINGs instantie. 

# <a name="windows"></a>[Windows](#tab/windows)

Gebruik in Windows de cmdlet [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) van Power shell om een certificaat te genereren.

1. Voer deze Power shell-opdracht uit om een zelfondertekend certificaat te genereren. Wijzig het `-Subject` argument naar wens voor uw toepassing en Azure AD B2C Tenant naam. U kunt ook de `-NotAfter` datum aanpassen om een andere verloop tijd voor het certificaat op te geven.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Open **gebruikers certificaten beheren**  >  **huidige gebruiker**  >  **persoonlijke**  >  **certificaten**  >  *yourappname.yourtenant.onmicrosoft.com*.
1. Selecteer het certificaat en selecteer vervolgens **actie**  >  **alle taken**  >  **exporteren**.
1. Selecteer **Ja**  >  **volgende**  >  **Ja, de persoonlijke sleutel exporteren**  >  **volgende**.
1. Accepteer de standaard instellingen voor de **export bestands indeling**.
1. Geef een wacht woord op voor het certificaat.

Om Azure AD B2C het wacht woord voor het pfx-bestand te accepteren, moet het wacht woord worden versleuteld met de optie TripleDES-SHA1 in het export hulpprogramma van het Windows-certificaat archief, in tegens telling tot AES256-SHA256.

# <a name="macos"></a>[MacOS](#tab/macos)

Gebruik in macOS de [Certificate Assistant](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0) in de sleutel keten toegang om een certificaat te genereren.

1. Volg de instructies voor het [maken van zelfondertekende certificaten in sleutel hanger toegang op Mac](https://support.apple.com/guide/keychain-access/kyca8916/mac).
1. Selecteer het certificaat dat u hebt gemaakt in de app voor het verkrijgen van de sleutel hanger op uw Mac.
1. Kies **bestand**  >  **exporteren items**.
1. Selecteer een bestands naam om uw certificaat op te slaan. Bijvoorbeeld **zelfondertekend certificaat. p12**.
1. Selecteer **Personal Information Exchange (. p12)** voor de **bestands indeling**.
1. Selecteer **Opslaan**.
1. Voer een **wacht woord** in en **Controleer** het wacht woord.
1. Vervang de bestands extensie naar `.pfx` . Bijvoorbeeld **self-signed-Certificate. pfx**.

---