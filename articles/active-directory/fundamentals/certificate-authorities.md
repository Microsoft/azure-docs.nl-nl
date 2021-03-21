---
title: Azure Active Directory-certificerings instanties
description: Lijst met vertrouwde certificaten die worden gebruikt in azure
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bea6f4161bcca063cd2c58d4c463473426f159ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96859128"
---
# <a name="certificate-authorities-used-by-azure-active-directory"></a>Certificerings instanties die door Azure Active Directory worden gebruikt

> [!IMPORTANT]
> De informatie op deze pagina is alleen relevant voor entiteiten die expliciet een lijst met acceptabele certificerings instanties (Ca's) opgeven. Deze procedure, ook wel certificaat vastmaken, moet worden vermeden, tenzij er geen andere opties zijn.

Elke entiteit die toegang probeert te krijgen tot Azure Active Directory-identiteits Services (Azure AD) via de TLS/SSL-protocollen, wordt weer gegeven met certificaten uit de hieronder vermelde certificerings instanties. Als de entiteit deze Ca's vertrouwt, kan deze de certificaten gebruiken om de identiteit en de geldigheid van de identiteits services te controleren en beveiligde verbindingen tot stand te brengen.

Certificerings instanties kunnen worden geclassificeerd in basis certificerings instanties en tussenliggende certificerings instanties. Normaal gesp roken hebben basis-Ca's een of meer gekoppelde tussenliggende certificerings instanties. Dit artikel bevat een lijst met de basis-Ca's die worden gebruikt door Azure AD Identity Services en de tussenliggende Ca's die zijn gekoppeld aan elk van deze hoofd mappen. Voor elke CA bevatten we Url's (Uniform Resource Identifiers) voor het downloaden van de bijbehorende toegang tot de gekoppelde CA-gegevens (AIA) en de certificaatintrekkingslijsten (CDP)-bestanden. Indien van toepassing, bieden we ook een URI naar het Online Certificate Status Protocol-eind punt (OCSP).

## <a name="cas-used-in-azure-public-and-azure-us-government-clouds"></a>Ca's die worden gebruikt in azure open bare en Azure Amerikaanse overheids Clouds

Verschillende services kunnen gebruikmaken van verschillende basis-of tussenliggende certificerings instanties. Daarom kunnen alle vermeldingen die hieronder worden vermeld, vereist zijn.

### <a name="digicert-global-root-g2"></a>DigiCert Global Root G2


| Basis-CA| Serienummer| Verval datum van de uitgifte datum| SHA1-vinger afdruk| URLs |
| - |- |-|-|-|-|
| DigiCert Global Root G2| 033af1e6a711a 9a0bb2864b11d09fae5| 1 augustus 2013 <br>15 januari 2038| df3c24f9bfd666761b268 073fe06d1cc8d4f82a4| [AIA](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt)<br>[CDP](http://crl3.digicert.com/DigiCertGlobalRootG2.crl) |


#### <a name="associated-intermediate-cas"></a>Gekoppelde tussenliggende Ca's

| Verlenende en tussenliggende CA| Serienummer| Verval datum van de uitgifte datum| SHA1-vinger afdruk| URLs |
| - | - | - | - | - | 
| Microsoft Azure TLS-certificerings instantie 01| 0aafa6c5ca63c45141 ea3be1f7c75317| 29 juli 2020<br>27 juni 2024| 2f2877c5d778c31e0f29c 7e371df5471bd673173| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.crl)|
|Microsoft Azure TLS-certificerings instantie 02| 0c6ae97cced59983 8690a00a9ea53214| 29 juli 2020<br>27 juni 2024| e7eea674ca718e3befd 90858e09f8372ad0ae2aa| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.crl) |
| Microsoft Azure TLS uitgegeven CA 05| 0d7bede97d8209967a 52631b8bdd18bd| 29 juli 2020<br>27 juni 2024| 6c3af02e7f269aa73a fd0eff2a88a4a1f04ed1e5| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.crl) |
| Microsoft Azure TLS-certificerings instantie 06| 02e79171fb8021e93fe 2d983834c50c0| 29 juli 2020<br>27 juni 2024| 30e01761ab97e59a06b 41ef20af6f2de7ef4f7b0| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.crl) |


 ### <a name="baltimore-cybertrust-root"></a>Baltimore CyberTrust Root

| Basis-CA| Serienummer| Verval datum van de uitgifte datum| SHA1-vinger afdruk| URLs |
| - | - | - | - | - | 
| Baltimore CyberTrust Root| 020000b9| 12 mei 2000<br>12 mei 2025| d4de20d05e66fc53fe 1a50882c78db2852cae474|<br>[CDP](http://crl3.digicert.com/Omniroot2025.crl)<br>[CERTIFICAAT](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>Gekoppelde tussenliggende Ca's

| Verlenende en tussenliggende CA| Serienummer| Verval datum van de uitgifte datum| SHA1-vinger afdruk| URLs |
| - | - | - | - | - | 
| Micro soft RSA TLS CA 01| 703d7a8f0ebf55aaa 59f98eaf4a206004eb2516a| 21 juli 2020<br>8 oktober 2024| 417e225037fbfaa4f9 5761d5ae729e1aea7e3a42| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt)<br>[CDP](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2001.crl)<br>[CERTIFICAAT](http://ocsp.msocsp.com/) |
| Micro soft RSA TLS CA 02| b0c2d2d13cdd56cdaa 6ab6e2c04440be4a429c75| 21 juli 2020<br>20 mei 2024| 54d9d20239080c32316ed 9ff980a48988f4adf2d| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt)<br>[CDP](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2002.crl)<br>[CERTIFICAAT](http://ocsp.msocsp.com/) |


 ### <a name="digicert-global-root-ca"></a>DigiCert Global Root CA

| Basis-CA| Serienummer| Verval datum van de uitgifte datum| SHA1-vinger afdruk| URLs |
| - | - | - | - | - | 
| DigiCert Global Root CA| 083be056904246 b1a1756ac95991c74a| 9 november 2006<br>9 november 2031| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://crl3.digicert.com/DigiCertGlobalRootCA.crl)<br>[CERTIFICAAT](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>Gekoppelde tussenliggende Ca's

| Verlenende en tussenliggende CA| Serienummer| Verval datum van de uitgifte datum| SHA1-vinger afdruk| URLs |
| - | - | - | - | - |
| DigiCert SHA2 Secure Server CA| 01fda3eb6eca75c 888438b724bcfbc91| 8 maart 2013 8 maart 2023| 1fb86b1168ec743154062 e8c9cc5b171a4b7ccb4| [AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA.crt)<br>[CDP](http://crl3.digicert.com/ssca-sha2-g6.crl)<br>[CERTIFICAAT](http://ocsp.digicert.com/) |
| DigiCert SHA2 Secure Server CA |02742eaa17ca8e21 c717bb1ffcfd0ca0 |22 september 2020<br>22 september 2030|626d44e704d1ceabe3bf 0d53397464ac8080142c|[AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA-2.crt)<br>[CDP](http://crl3.digicert.com/DigiCertSHA2SecureServerCA.crl)<br>[CERTIFICAAT](http://ocsp.digicert.com/)|


## <a name="cas-used-in-azure-china-21vianet-cloud"></a>Ca's die worden gebruikt in azure China 21Vianet Cloud

### <a name="digicert-global-root-ca"></a>DigiCert Global Root CA


| Basis-CA| Serienummer| Verval datum van de uitgifte datum| SHA1-vinger afdruk| URLs |
| - | - | - | - | - |
| DigiCert Global Root CA| 083be056904246b 1a1756ac95991c74a| Nov. 9, 2006<br>Nov. 9, 2031| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://ocsp.digicert.com/)<br>[CERTIFICAAT](http://crl3.digicert.com/DigiCertGlobalRootCA.crl) |


#### <a name="associated-intermediate-ca"></a>Gekoppelde tussenliggende CA

| Verlenende en tussenliggende CA| Serienummer| Verval datum van de uitgifte datum| SHA1-vinger afdruk| URLs |
| - | - | - | - | - | - |
| DigiCert Basic RSA CN CA G2| 02f7e1f982bad 009aff47dc95741b2f6| 4 maart 2020<br>4 maart 2030| 4d1fa5d1fb1ac3917c08e 43f65015e6aea571179| [AIA](http://cacerts.digicert.cn/DigiCertBasicRSACNCAG2.crt)<br>[CDP](http://crl.digicert.cn/DigiCertBasicRSACNCAG2.crl)<br>[CERTIFICAAT](http://ocsp.digicert.cn/) |

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over Microsoft 365 versleutelings ketens](/microsoft-365/compliance/encryption-office-365-certificate-chains)
