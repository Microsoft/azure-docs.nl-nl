---
title: Wijzigingen in Azure TLS-certificaat
description: Wijzigingen in Azure TLS-certificaat
services: security
author: msmbaldwin
tags: azure-resource-manager
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 11/10/2020
ms.author: mbaldwin
ms.openlocfilehash: 04b68899b796088bd4b89509a413c7a1fe5369bb
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475349"
---
# <a name="azure-tls-certificate-changes"></a>Wijzigingen in Azure TLS-certificaat  

Microsoft werkt Azure-services bij om TLS-certificaten te gebruiken van een andere set basis-CA's (certificeringsinstanties). Deze wijziging wordt doorgevoerd omdat de huidige CA-certificaten niet voldoen aan een van de vereisten voor de basis lijn van de CA/browser-forum en op 15 februari 2021 worden ingetrokken.

## <a name="when-will-this-change-happen"></a>Wanneer vindt deze wijziging plaats?

De bestaande Azure-eind punten zijn sinds 13 augustus 2020 overgegaan op een gefaseerde manier. Alle nieuw gemaakte Azure TLS/SSL-eindpunten bevatten bijgewerkte certificaten die zijn gekoppeld aan de nieuwe basis-CA's.

Servicespecifieke Details:

- Deze transitie is op 7 juli 2020 begonnen in [Azure AD](../../active-directory/index.yml)-services (Active Directory).
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub) en [DPS](../../iot-dps/index.yml) blijven in de Baltimore CyberTrust Root CA, maar de tussenliggende CA's veranderen. [Klik hier voor meer informatie](https://techcommunity.microsoft.com/t5/internet-of-things/azure-iot-tls-changes-are-coming-and-why-you-should-care/ba-p/1658456).
- [Azure Storage](../../storage/index.yml) blijft aanwezig in de basis-CA van Baltimore CyberTrust, maar de tussenliggende CA's veranderen. [Klik hier voor meer informatie](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518).
- [Azure cache voor redis](../../azure-cache-for-redis/index.yml) blijft aanwezig op Baltimore Cyber Trust-basis certificerings instantie, maar de tussenliggende ca's worden gewijzigd. [Klik hier voor meer informatie](../../azure-cache-for-redis/cache-whats-new.md).
- Azure Instance Metadata Service blijft aanwezig op de basis-CA Baltimore Cyber Trust, maar de tussenliggende certificerings instantie verandert. [Klik hier voor meer informatie](/answers/questions/172717/action-required-for-attested-data-tls-with-azure-i.html).

> [!IMPORTANT]
> Klanten moeten hun toepassing(en) mogelijk bijwerken na deze wijziging om verbindingsfouten te voorkomen wanneer ze proberen verbinding te maken met Azure-services.

## <a name="what-is-changing"></a>Wat wordt er gewijzigd?

Op dit moment zijn de meeste TLS-certificaten die worden gebruikt voor Azure-services, gekoppeld aan de volgende basis-CA:

| Algemene naam van de CA | Vingerafdruk (SHA1) |
|--|--|
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

TLS-certificaten die worden gebruikt voor Azure-services, worden gekoppeld aan de volgende basis-CA's:

| Algemene naam van de CA | Vingerafdruk (SHA1) |
|--|--|
| [DigiCert Global Root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [DigiCert Global Root CA](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D-TRUST Root Class 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft ECC Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20ECC%20Root%20Certificate%20Authority%202017.crt) | 999a64c37ff47d9fab95f14769891460eec4c3c5 |

## <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>Wanneer kan ik de oude tussenliggende vingerafdruk buiten gebruik stellen?

De huidige CA-certificaten worden *niet* pas ingetrokken op 15 februari 2021. Na deze datum kunt u de oude vingerafdrukken uit uw code verwijderen.

Als deze datum wordt gewijzigd, ontvangt u een melding over de nieuwe intrekkingsdatum.

## <a name="will-this-change-affect-me"></a>Heeft deze wijziging gevolgen voor mij? 

We verwachten dat **de meeste Azure-klanten niet** worden beïnvloed.  Uw toepassing kan echter worden beïnvloed als expliciet een lijst met geaccepteerde CA's is opgegeven voor de toepassing. Dit wordt certificaatkoppeling genoemd.

Hier zijn enkele manieren om vast te stellen of uw toepassing wordt beïnvloed:

- Bekijk uw broncode voor de vingerafdruk, de algemene naam, en andere certificaateigenschappen van de Microsoft IT TLS-CA's die u [hier](https://www.microsoft.com/pki/mscorp/cps/default.htm) kunt vinden. Als er een overeenkomst is, heeft dit gevolgen voor de toepassing. Als u dit probleem wilt oplossen, werkt u de broncode bij met de nieuwe CA's. Als best practice kunt u ervoor zorgen dat CA's op korte termijnen worden toegevoegd of bewerkt. Regelgeving vanuit de branche vereist dat CA-certificaten binnen zeven dagen worden vervangen, en daarom moeten klanten die afhankelijk zijn van koppeling, snel reageren.

- Als u een toepassing hebt die integreert met Azure-API's of andere Azure-services, en u niet zeker weet of de toepassing gebruikmaakt van certificaatkoppeling, neemt u contact op met de leverancier.

- Voor verschillende besturingssystemen en taalruntimes die communiceren met Azure-services, zijn mogelijk extra stappen vereist om de certificaatketen juist te bouwen met deze nieuwe basis-CA's.
    - **Linux**: Voor veel distributies moet u de CA's toevoegen aan /etc/ssl/certs. Raadpleeg de documentatie van de distributie voor specifieke instructies.
    - **Java**: Controleer of het Java-sleutelarchief de CA's bevat die hierboven worden vermeld.
    - **Windows uitgevoerd in omgevingen zonder verbinding**: Voor systemen die worden uitgevoerd in omgevingen zonder verbinding, moeten de nieuwe basis-CA's worden toegevoegd aan het archief Vertrouwde basiscertificeringsinstanties, en de tussenliggende CA's aan het archief Tussenliggende certificeringsinstanties.
    - **Android**: Raadpleeg de documentatie voor uw apparaat en versie van Android.
    - **Andere hardwareapparaten, met name IoT**: Neem contact op met de fabrikant van het apparaat.

- Als u een omgeving hebt waarin firewallregels zijn ingesteld om alleen uitgaande oproepen toe te staan naar specifieke CRL-downloadlocaties (certificaatintrekkingslijst) en/of OCSP-verificatielocaties (Online Certificate Status Protocol). U hebt de volgende CRL- en OCSP-URL's nodig:

    - http://crl3&#46;digicert&#46;com
    - http://crl4&#46;digicert&#46;com
    - http://ocsp&#46;digicert&#46;com
    - http://www&#46;d-trust&#46;net
    - http://root-c3-ca2-2009&#46;ocsp&#46;d-trust&#46;net
    - http://crl&#46;microsoft&#46;com
    - http://oneocsp&#46;microsoft&#46;com
    - http://ocsp&#46;msocsp&#46;com
    - http://www&#46; micro soft&#46;com-pkiops

## <a name="next-steps"></a>Volgende stappen

Als u aanvullende vragen hebt, kunt u contact met ons opnemen via [ondersteuning](https://azure.microsoft.com/support/options/).
