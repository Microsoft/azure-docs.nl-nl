---
title: Wat is er nieuw in azure cache voor redis
description: Recente updates voor Azure cache voor redis
author: yegu-ms
ms.service: cache
ms.topic: reference
ms.date: 09/28/2020
ms.author: yegu
ms.openlocfilehash: b30e83b89b25e6400b8c7e0419406631fa1edcd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91492449"
---
# <a name="whats-new-in-azure-cache-for-redis"></a>Wat is er nieuw in azure cache voor redis

## <a name="azure-tls-certificate-change"></a>Wijziging van Azure TLS-certificaat

Micro soft werkt Azure-Services bij om TLS-server certificaten te gebruiken van een andere set certificerings instanties (Ca's). Deze wijziging wordt doorgevoerd in fasen van 13 augustus 2020 tot 26 oktober 2020 (geschat). Azure brengt deze wijziging aan omdat [de huidige CA-certificaten niet voldoen aan een van de basis vereisten voor de CA/browser-forum](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951). Het probleem is gerapporteerd op 1 juli 2020 en is van toepassing op meerdere populaire PKI-providers (Public Key Infrastructure) wereld wijd. De meeste TLS-certificaten die momenteel door Azure-Services worden gebruikt, zijn afkomstig van de *Baltimore Cyber Trust Root* PKI. De Azure-cache voor de redis-service wordt nog steeds gekoppeld aan de Baltimore Cyber Trust root. De TLS-server certificaten worden echter verleend door nieuwe tussenliggende certificerings instanties (ICAs) vanaf 12 oktober 2020.

> [!NOTE]
> Deze wijziging is beperkt tot services in open bare [Azure-regio's](https://azure.microsoft.com/global-infrastructure/geographies/). Soevereine (bijvoorbeeld China) of overheids Clouds worden uitgesloten.
>
>

### <a name="does-this-change-affect-me"></a>Heeft deze wijziging gevolgen voor mij?

We verwachten dat de meeste Azure cache voor redis-klanten niet worden beïnvloed door de wijziging. Uw toepassing kan worden beïnvloed als hierin expliciet een lijst met acceptabele certificaten wordt opgegeven, een zogenaamde ' certificaat vastmaken '. Als deze is vastgemaakt aan een tussenliggend-of Leaf-certificaat in plaats van de Baltimore Cyber Trust Root, moet u **onmiddellijk actie ondernemen** om de certificaat configuratie te wijzigen.

De volgende tabel bevat informatie over de certificaten die worden geïmplementeerd. Afhankelijk van het certificaat dat door uw toepassing wordt gebruikt, moet u dit mogelijk bijwerken om te voor komen dat de verbinding met uw Azure-cache voor redis-exemplaar verloren gaat.

| CA-type | Huidig | Post-Rolling (12 oktober 2020) | Bewerking |
| ----- | ----- | ----- | ----- |
| Hoofdmap | Vinger afdruk: d4de20d05e66fc53fe1a50882c78db2852cae474<br><br> Verloop tijd: maandag 12 mei 2025, 4:59:00 uur<br><br> Onderwerpnaam:<br> CN = Baltimore Cyber Trust Root<br> OE = Cyber Trust<br> O = Baltimore<br> C = IE | Niet wijzigen | Geen |
| Intermediates | Vinger afdrukken<br> CN = micro soft IT TLS-CA 1<br> Vinger afdruk: 417e225037fbfaa4f95761d5ae729e1aea7e3a42<br><br> CN = micro soft IT TLS CA 2<br> Vinger afdruk: 54d9d20239080c32316ed9ff980a48988f4adf2d<br><br> CN = micro soft IT TLS CA 4<br> Vinger afdruk: 8a38755d0996823fe8fa3116a277ce446eac4e99<br><br> CN = micro soft IT TLS CA 5<br> Vinger afdruk: Ad898ac73df333eb60ac1f5fc6c4b2219ddb79b7<br><br> Verloop tijd: vrijdag 20 mei 2024 5:52:38 uur<br><br> Onderwerpnaam:<br> OE = micro soft IT<br> O = micro soft Corporation<br> L = Redmond<br> S = Washington<br> C = US<br> | Vinger afdrukken<br> CN = micro soft RSA TLS CA 01<br> Vinger afdruk: 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a<br><br> CN = micro soft RSA TLS CA 02<br> Vinger afdruk: b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75<br><br> Verloop datum: dinsdag 8 oktober 2024 12:00:00 uur;<br><br> Onderwerpnaam:<br> O = micro soft Corporation<br> C = US<br> | Vereist |

### <a name="what-actions-should-i-take"></a>Welke acties moet ik ondernemen?

Als uw toepassing gebruikmaakt van het certificaat archief van het besturings systeem of de hoofdmap van de Baltimore onder anderen vastmaakt, hoeft u geen actie te ondernemen. Als uw toepassing daarentegen een tussenliggend of Leaf TLS-certificaat vastmaakt, wordt u aangeraden de volgende hoofd mappen vast te maken:

| Certificaat | Vingerafdruk |
| ----- | ----- |
| [Baltimore basis-CA](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 |
| [Digicert Global root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |

> [!TIP]
> Zowel het tussenliggende als het Leaf-certificaat wordt naar verwachting regel matig gewijzigd. We raden u aan geen afhankelijkheid te nemen. In plaats daarvan kunt u uw toepassing vastmaken aan een basis certificaat, omdat deze minder vaak wordt uitgedraaid.
>
>

Als u wilt door gaan met het vastmaken van tussenliggende certificaten, voegt u het volgende toe aan de lijst met vastgemaakte tussenliggende certificaten, met daarin enkele extra voor het minimaliseren van toekomstige wijzigingen:

| Algemene naam van de CA | Vingerafdruk |
| ----- | ----- |
| [Micro soft RSA TLS CA 01](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt) | 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a |
| [Micro soft RSA TLS CA 02](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt) | b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75 |
| [Microsoft Azure TLS-certificerings instantie 01](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.cer) | 2f2877c5d778c31e0f29c7e371df5471bd673173 |
| [Microsoft Azure TLS-certificerings instantie 02](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.cer) | e7eea674ca718e3befd90858e09f8372ad0ae2aa |
| [Microsoft Azure TLS uitgegeven CA 05](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.cer) | 6c3af02e7f269aa73afd0eff2a88a4a1f04ed1e5 |
| [Microsoft Azure TLS-certificerings instantie 06](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer) | 30e01761ab97e59a06b41ef20af6f2de7ef4f7b0 |

Als uw toepassing het certificaat in code valideert, moet u het wijzigen zodat de eigenschappen (zoals verleners, vinger afdruk) van de nieuwe vastgemaakte certificaten worden herkend. Deze extra verificatie moet alle vastgemaakte certificaten beslaan om meer toekomst te kunnen bewijzen.

## <a name="next-steps"></a>Volgende stappen

Als u aanvullende vragen hebt, kunt u contact met ons opnemen via [ondersteuning](https://azure.microsoft.com/support/options/).  
