---
title: Overzicht van wederzijdse verificatie op Azure-toepassing gateway
description: Dit artikel bevat een overzicht van wederzijdse verificatie voor Application Gateway.
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.date: 03/30/2021
ms.topic: conceptual
ms.author: caya
ms.openlocfilehash: a63697c2c9c32dfb48e77248a5e7a601677b8b3e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231441"
---
# <a name="overview-of-mutual-authentication-with-application-gateway-preview"></a>Overzicht van wederzijdse verificatie met Application Gateway (preview-versie)

Met wederzijdse verificatie of client verificatie kan de Application Gateway de client verifiëren die aanvragen verzendt. Meestal wordt de Application Gateway door de client geverifieerd. wederzijdse verificatie staat zowel de client als de Application Gateway toe om elkaar te verifiëren. 

> [!NOTE]
> U kunt het beste TLS 1,2 gebruiken met wederzijdse verificatie als TLS 1,2 in de toekomst. 

## <a name="mutual-authentication"></a>Wederzijdse verificatie

Application Gateway ondersteunt wederzijdse verificatie op basis van certificaten waarbij u een of meer vertrouwde CA-certificaten kunt uploaden naar de Application Gateway en de gateway dit certificaat gebruikt om de client te verifiëren die een aanvraag verzendt naar de gateway. Met de toename van IoT-use cases en verhoogde beveiligings vereisten in de industrie, biedt wederzijdse verificatie een manier om te beheren en te bepalen welke clients kunnen communiceren met uw Application Gateway. 

Als u wederzijdse verificatie wilt configureren, moet een vertrouwd client CA-certificaat worden geüpload als onderdeel van het client verificatie gedeelte van een SSL-profiel. Het SSL-profiel moet vervolgens aan een listener worden gekoppeld om de configuratie van wederzijdse authenticatie te volt ooien. Er moet altijd een basis-CA-certificaat zijn in het client certificaat dat u uploadt. U kunt ook een certificaat keten uploaden, maar de keten moet naast zoveel tussenliggende CA-certificaten ook een basis-CA-certificaat bevatten als u wilt. 

Als uw client certificaat bijvoorbeeld een basis-CA-certificaat, meerdere tussenliggende CA-certificaten en een blad certificaat bevat, moet u ervoor zorgen dat het basis-CA-certificaat en alle tussenliggende CA-certificaten worden geüpload naar Application Gateway in één bestand. Zie [vertrouwde client certificerings instanties ophalen](./mutual-authentication-certificate-management.md)voor meer informatie over het extra heren van een CA-certificaat van een vertrouwde client.

Als u een certificaat keten met basis-CA en tussenliggende CA-certificaten uploadt, moet de certificaat keten worden geüpload als een PEM-of CER-bestand naar de gateway. 

> [!NOTE] 
> Wederzijdse verificatie is alleen beschikbaar op Standard_v2 en WAF_v2 Sku's. 

### <a name="certificates-supported-for-mutual-authentication"></a>Certificaten die worden ondersteund voor wederzijdse verificatie

Application Gateway ondersteunt de volgende typen certificaten:

- CA-certificaat (certificerings instantie): een CA-certificaat is een digitaal certificaat dat is uitgegeven door een certificerings instantie (CA)
- Zelfondertekende CA-certificaten: client browsers vertrouwen deze certificaten niet en waarschuwen de gebruiker dat het certificaat van de virtuele service geen deel uitmaakt van een vertrouwens keten. Zelfondertekende CA-certificaten zijn geschikt voor testen of omgevingen waarin beheerders de clients beheren en de beveiligings waarschuwingen van de browser veilig kunnen passeren. Werk belastingen voor productie moeten nooit zelfondertekende CA-certificaten gebruiken.

Zie [Configure wederzijdse Authentication with Application Gateway](./mutual-authentication-portal.md)(Engelstalig) voor meer informatie over het instellen van wederzijdse verificatie.

> [!IMPORTANT]
> Zorg ervoor dat u de volledige CA-certificaat keten van de vertrouwde client uploadt naar de Application Gateway wanneer u wederzijdse verificatie gebruikt. 

## <a name="additional-client-authentication-validation"></a>Aanvullende validatie van client verificatie

### <a name="verify-client-certificate-dn"></a>Verifiëren van client certificaat DN

U hebt de mogelijkheid om de directe verlener van het client certificaat te verifiëren en alleen de Application Gateway toe te staan die verlener te vertrouwen. Deze opties zijn standaard uitgeschakeld, maar u kunt dit inschakelen via Portal, Power shell of Azure CLI. 

Als u ervoor kiest om de Application Gateway in te scha kelen om de directe verlener van het client certificaat te verifiëren, kunt u als volgt bepalen welke client certificaat verlener DN wordt opgehaald uit de geüploade certificaten. 
* **Scenario 1:** Certificaat keten bevat: basis certificaat-tussenliggend certificaat-blad certificaat 
    * De onderwerpnaam *van het tussenliggende certificaat* is wat Application Gateway wordt opgehaald als de uitgever van het client certificaat en wordt gecontroleerd op basis van. 
* **Scenario 2:** Certificaat keten bevat: basis certificaat-intermediate1 certificaat-intermediate2 certificaat-blad certificaat
    * De onderwerpnaam *van het Intermediate2-certificaat* wordt opgehaald als de naam van de uitgever van het client certificaat en wordt gecontroleerd op basis van. 
* **Scenario 3:** Certificaat keten bevat: basis certificaat-blad certificaat 
    * *De onderwerpnaam van het basis certificaat* wordt opgehaald en gebruikt als DN-naam van de uitgever van het client certificaat.
* **Scenario 4:** Meerdere certificaat ketens met dezelfde lengte in hetzelfde bestand. Ketting 1 omvat: basis certificaat-intermediate1 certificaat blad certificaat. Ketting 2 omvat: basis certificaat-intermediate2 certificaat blad certificaat. 
    * *De onderwerpnaam van het Intermediate1-certificaat* wordt OPGEHAALD als DN van de uitgever van het client certificaat.  
* **Scenario 5:** Meerdere certificaat ketens met verschillende lengtes in hetzelfde bestand. Ketting 1 omvat: basis certificaat-intermediate1 certificaat blad certificaat. De reeks 2 omvat het basis certificaat-intermediate2 Certificate-intermediate3 Certificate-Leaf Certificate. 
    * *De onderwerpnaam van het Intermediate3-certificaat* wordt OPGEHAALD als DN van de uitgever van het client certificaat. 

> [!IMPORTANT]
> We raden u aan om slechts één certificaat keten per bestand te uploaden. Dit is vooral belang rijk als u verificatie van client certificaat DN inschakelt. Door meerdere certificaat ketens in één bestand te uploaden, wordt u in het scenario vier of vijf beëindigd en kunnen de problemen later op de regel worden uitgevoerd wanneer het client certificaat niet overeenkomt met de naam van de uitgever van het client certificaat dat Application Gateway geëxtraheerd uit de ketens. 

Zie voor meer informatie over het uitpakken van vertrouwde client CA-certificaat ketens [vertrouwde client CA-certificaat ketens ophalen](./mutual-authentication-certificate-management.md).

## <a name="server-variables"></a>Server variabelen 

Bij wederzijdse verificatie zijn er extra server variabelen die u kunt gebruiken om informatie over het client certificaat door te geven aan de back-endservers achter de Application Gateway. Voor meer informatie over welke server variabelen beschikbaar zijn en hoe u deze kunt gebruiken, raadpleegt u [Server variabelen](./rewrite-http-headers-url.md#mutual-authentication-server-variables-preview).

## <a name="next-steps"></a>Volgende stappen

Nadat u hebt gebruikgemaakt van wederzijdse verificatie, gaat u naar [Application Gateway configureren met wederzijdse verificatie in Power shell](./mutual-authentication-powershell.md) om een Application Gateway te maken met behulp van wederzijdse verificatie. 

