---
title: Problemen met wederzijdse verificatie op Azure-toepassing gateway oplossen
description: Meer informatie over het oplossen van problemen met wederzijdse verificatie op Application Gateway
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 623addd253b3eb28bdf70db02ddfbe4b320cbae7
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231430"
---
# <a name="troubleshooting-mutual-authentication-errors-in-application-gateway-preview"></a>Fout bij het oplossen van problemen met wederzijdse verificatie in Application Gateway (preview-versie)

Meer informatie over het oplossen van problemen met wederzijdse verificatie bij het gebruik van Application Gateway. 

## <a name="overview"></a>Overzicht 

Na het configureren van wederzijdse verificatie op een Application Gateway, kan er een aantal fouten optreden bij het gebruik van wederzijdse verificatie. Enkele veelvoorkomende oorzaken voor fouten zijn:

* Er is een certificaat of certificaat keten geüpload zonder basis-CA-certificaat
* Een certificaat keten met meerdere basis-CA-certificaten is geüpload
* Er is een certificaat keten geüpload die alleen een Leaf-certificaat zonder CA-certificaat bevat 
* Validatie fouten vanwege niet-overeenkomende Uitgever-DN  

We gaan door in verschillende scenario's die u kunt uitvoeren en hoe u deze scenario's kunt oplossen. Vervolgens worden fout codes en mogelijke oorzaken voor bepaalde fout codes weer gegeven die u mogelijk ziet met wederzijdse verificatie. 

## <a name="scenario-troubleshooting---configuration-problems"></a>Scenario problemen oplossen-configuratie problemen
Er zijn enkele scenario's die u mogelijk ondervindt bij het configureren van wederzijdse verificatie. U leert hoe u een aantal van de meest voorkomende valk uilen kunt oplossen. 

### <a name="self-signed-certificate"></a>Zelfondertekend certificaat

#### <a name="problem"></a>Probleem 

Het client certificaat dat u hebt geüpload, is een zelfondertekend certificaat en resulteert in de fout code ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate.

#### <a name="solution"></a>Oplossing 

Controleer of het zelfondertekende certificaat dat u gebruikt de extensie *BasicConstraintsOid* = "2.5.29.19" bevat. Dit geeft aan dat het onderwerp kan fungeren als een certificerings instantie. Dit zorgt ervoor dat het gebruikte certificaat een CA-certificaat is. Bekijk [vertrouwde client](./mutual-authentication-certificate-management.md)certificaten voor meer informatie over het genereren van zelfondertekende client certificaten.

## <a name="scenario-troubleshooting---connectivity-problems"></a>Scenario problemen oplossen-verbindings problemen

Mogelijk hebt u zonder problemen de mogelijkheid om wederzijdse verificatie te configureren, maar hebt u problemen met het verzenden van aanvragen naar uw Application Gateway. We behandelen enkele veelvoorkomende problemen en oplossingen in de volgende sectie. U vindt de eigenschap *sslClientVerify* in de logboeken van uw Application Gateway. 

### <a name="sslclientverify-is-none"></a>SslClientVerify is geen

#### <a name="problem"></a>Probleem 

De eigenschap *sslClientVerify* wordt weer gegeven als ' geen ' in de logboeken van uw toegang. 

#### <a name="solution"></a>Oplossing 

Dit wordt weer gegeven wanneer de client geen client certificaat verzendt bij het verzenden van een aanvraag naar de Application Gateway. Dit kan gebeuren als de client die de aanvraag verzendt naar de Application Gateway niet correct is geconfigureerd voor het gebruik van client certificaten. Eén manier om te controleren of de installatie van de client verificatie op Application Gateway werkt zoals verwacht, is via de volgende OpenSSL-opdracht:

```
openssl s_client -connect <hostname:port> -cert <path-to-certificate> -key <client-private-key-file> 
```

De `-cert` vlag is het Leaf-certificaat `-key` . de vlag is het bestand met de persoonlijke sleutel van de client. 

Raadpleeg de hand leiding voor meer informatie over het gebruik van de OpenSSL `s_client` - [](https://www.openssl.org/docs/man1.0.2/man1/openssl-s_client.html)opdracht.

### <a name="sslclientverify-is-failed"></a>SslClientVerify is mislukt

#### <a name="problem"></a>Probleem

De eigenschap *sslClientVerify* wordt weer gegeven als ' mislukt ' in de logboeken van uw toegang. 

#### <a name="solution"></a>Oplossing

Er zijn een aantal mogelijke oorzaken voor fouten in de toegangs logboeken. Hieronder vindt u een lijst met veelvoorkomende oorzaken voor fouten:
* **Kan het certificaat van de verlener niet ophalen:** Het certificaat van de certificerings instantie van het client certificaat is niet gevonden. Dit betekent normaal gesp roken dat de CA-certificaat keten van de vertrouwde client niet is voltooid op het Application Gateway. Controleer of de vertrouwde client CA-certificaat keten die is geüpload op de Application Gateway is voltooid.  
* **Kan het lokale certificaat van de verlener niet ophalen:** Vergelijkbaar met het ophalen van het certificaat van de verlener kan het certificaat van de certificerings instantie van het client certificaat niet vinden. Dit betekent normaal gesp roken dat de CA-certificaat keten van de vertrouwde client niet is voltooid op het Application Gateway. Controleer of de vertrouwde client CA-certificaat keten die is geüpload op de Application Gateway is voltooid.
* **Kan het eerste certificaat niet verifiëren:** Kan het client certificaat niet verifiëren. Deze fout treedt specifiek op wanneer de client alleen het Leaf-certificaat presenteert waarvan de uitgever niet vertrouwd is. Controleer of de vertrouwde client CA-certificaat keten die is geüpload op de Application Gateway is voltooid.
* **Kan de uitgever van het client certificaat niet verifiëren:** Deze fout treedt op wanneer de configuratie- *VerifyClientCertIssuerDN* is ingesteld op True. Dit gebeurt meestal wanneer de DN van de verlener van het client certificaat niet overeenkomt met het *ClientCertificateIssuerDN* dat is geëxtraheerd uit de vertrouwde client CA-certificaat keten die is geüpload door de klant. Voor meer informatie over hoe Application Gateway de *ClientCertificateIssuerDN* uitpakt, raadpleegt u [Application Gateway de uitpakkende verlener DN](./mutual-authentication-overview.md#verify-client-certificate-dn). Zorg er als best practice voor dat u één certificaat keten per bestand uploadt naar Application Gateway. 

Zie voor meer informatie over het extra heren van de volledige CA-certificaat keten voor de vertrouwde client om deze naar Application Gateway te uploaden, het [ophalen van vertrouwde client CA-certificaat ketens](./mutual-authentication-certificate-management.md).

## <a name="error-code-troubleshooting"></a>Fout code problemen oplossen
Als u een van de volgende fout codes ziet, hebben we een aantal aanbevolen oplossingen om het probleem op te lossen. 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustspecifydata"></a>Fout code: ApplicationGatewayTrustedClientCertificateMustSpecifyData

#### <a name="cause"></a>Oorzaak

Er ontbreken certificaat gegevens. Het uploaden van het certificaat is mogelijk een leeg bestand zonder certificaat gegevens. 

#### <a name="solution"></a>Oplossing

Controleer of het certificaat bestand dat is geüpload geen ontbrekende gegevens bevat. 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustnothaveprivatekey"></a>Fout code: ApplicationGatewayTrustedClientCertificateMustNotHavePrivateKey

#### <a name="cause"></a>Oorzaak

Er bevindt zich een persoonlijke sleutel in de certificaat keten. Er mag geen persoonlijke sleutel voor de certificaat keten zijn. 

#### <a name="solution"></a>Oplossing

Controleer de certificaat keten die is geüpload en verwijder de persoonlijke sleutel die deel uitmaakt van de keten. Upload de keten opnieuw zonder de persoonlijke sleutel. 

### <a name="error-code-applicationgatewaytrustedclientcertificateinvaliddata"></a>Fout code: ApplicationGatewayTrustedClientCertificateInvalidData

#### <a name="cause"></a>Oorzaak

Er zijn twee mogelijke oorzaken achter deze fout code.
1. Het parseren is mislukt omdat de keten niet in de juiste indeling wordt weer gegeven. Application Gateway verwacht dat een certificaat keten in de PEM-indeling is en verwacht ook dat afzonderlijke certificaat gegevens moeten worden gescheiden. 
2. De parser heeft niets gevonden om te parseren. Het uploaden van het bestand kan mogelijk alleen de scheidings tekens bevatten, maar geen certificaat gegevens. 

#### <a name="solution"></a>Oplossing

Afhankelijk van de oorzaak van deze fout zijn er twee mogelijke oplossingen. 
* Controleer of de geüploade certificaat keten de juiste indeling heeft (PEM) en of de certificaat gegevens correct zijn gescheiden. 
* Controleer of het certificaat bestand dat is geüpload de certificaat gegevens bevat naast de scheidings tekens. 

### <a name="error-code-applicationgatewaytrustedclientcertificatedoesnotcontainanycacertificate"></a>Fout code: ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate

#### <a name="cause"></a>Oorzaak

Het certificaat dat is geüpload bevat alleen een Leaf-certificaat zonder CA-certificaat. Het uploaden van een certificaat keten met CA-certificaten en een blad certificaat is acceptabel omdat het blad certificaat alleen zou worden genegeerd, maar een certificaat moet een CA hebben. 

#### <a name="solution"></a>Oplossing 

Controleer of de certificaat keten die is geüpload, meer dan alleen het blad certificaat bevat. De uitbrei ding *BasicConstraintsOid* = "2.5.29.19" moet aanwezig zijn en aangeven dat het onderwerp kan fungeren als een certificerings instantie.

### <a name="error-code-applicationgatewayonlyonerootcaallowedintrustedclientcertificate"></a>Fout code: ApplicationGatewayOnlyOneRootCAAllowedInTrustedClientCertificate

#### <a name="cause"></a>Oorzaak

De certificaat keten bevat meerdere basis-CA-certificaten *of* bevat basis-CA-certificaten van nul. 

#### <a name="solution"></a>Oplossing

De geüploade certificaten moeten precies één basis-CA-certificaat bevatten (en er worden echter veel tussenliggende CA-certificaten indien nodig).


