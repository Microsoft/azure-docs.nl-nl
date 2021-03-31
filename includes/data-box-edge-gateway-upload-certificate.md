---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67448632"
---
Een correct SSL-certificaat zorgt ervoor dat u versleutelde gegevens naar de juiste server verzendt. Het certificaat kan behalve versleuteld ook worden geverifieerd. U kunt uw eigen vertrouwde SSL-certificaat uploaden via de Power shell-interface van het apparaat.

1. [Verbinding maken met de Power shell-interface](#connect-to-the-powershell-interface).
2. Gebruik de `Set-HcsCertificate` cmdlet om het certificaat te uploaden. Geef desgevraagd de volgende para meters op:

   - `CertificateFilePath` -Het pad naar de share met het certificaat bestand in *PFX* -indeling.
   - `CertificatePassword` -Een wacht woord dat wordt gebruikt om het certificaat te beveiligen.
   - `Credentials` -Gebruikers naam voor toegang tot de share met het certificaat. Geef het wachtwoord voor de netwerkshare op wanneer u hierom wordt gevraagd.

     In het volgende voor beeld ziet u het gebruik van deze cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

