---
title: Methoden voor het maken van certificaten
description: Meer informatie over de verschillende opties voor het maken of importeren van een Key Vault certificaat in Azure Key Vault. Er zijn verschillende manieren om een Key Vault certificaat te maken.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: a9545c040809331a5556b11f6cc7536931e2d421
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93289571"
---
# <a name="certificate-creation-methods"></a>Methoden voor het maken van certificaten

 Een Key Vault-certificaat (KV) kan worden gemaakt of geïmporteerd in een sleutel kluis. Wanneer een KV-certificaat wordt gemaakt, wordt de persoonlijke sleutel gemaakt in de sleutel kluis en nooit weer gegeven aan de eigenaar van het certificaat. U kunt op de volgende manieren een certificaat maken in Key Vault:  

-   **Een zelfondertekend certificaat maken:** Hiermee maakt u een openbaar-persoonlijk sleutel paar en koppelt u het aan een certificaat. Het certificaat wordt door een eigen sleutel ondertekend.  

-    **Hand matig een nieuw certificaat maken:** Hiermee maakt u een openbaar-persoonlijk sleutel paar en genereert u een X. 509-aanvraag voor certificaat ondertekening. De handtekening aanvraag kan worden ondertekend door uw registratie-instantie of certificerings instantie. Het ondertekende x509-certificaat kan worden samengevoegd met het sleutel paar in behandeling om het KV-certificaat in Key Vault te volt ooien. Hoewel deze methode meer stappen vereist, biedt dit u een betere beveiliging omdat de persoonlijke sleutel wordt gemaakt in en beperkt tot Key Vault. Dit wordt uitgelegd in het onderstaande diagram.  

![Een certificaat maken met uw eigen certificerings instantie](../media/certificate-authority-1.png)  

De volgende beschrijvingen komen overeen met de groene letterlijke stappen in het voor gaande diagram.

1. In het bovenstaande diagram maakt uw toepassing een certificaat dat intern begint met het maken van een sleutel in uw sleutelkluis.
2. Key Vault keert terug naar uw toepassing een aanvraag voor certificaat ondertekening (CSR)
3. Uw toepassing stuurt de CSR door naar de gekozen certificeringsinstantie.
4. De gekozen certificerings instantie reageert met een x509-certificaat.
5. Uw toepassing heeft het maken van het nieuwe certificaat voltooid met een fusie van het x509-certificaat van uw certificerings instantie.

-   **Een certificaat maken met een bekende verlener:** Voor deze methode moet u een eenmalige taak uitvoeren om een object van de verlener te maken. Zodra een object met de naam van de certificaat verlener in de sleutel kluis is gemaakt, kan in het beleid van het KV-certificaat worden verwezen naar de namen. Een aanvraag om een dergelijk KV-certificaat te maken, maakt een sleutel paar in de kluis en communiceert met de provider service van de verlener met behulp van de informatie in het verleners object waarnaar wordt verwezen om een x509-certificaat op te halen. Het x509-certificaat wordt opgehaald van de verlener-service en wordt samengevoegd met het sleutel paar om het KV-certificaat te kunnen maken.  

![Een certificaat maken met een certificerings instantie voor een Key Vault partner](../media/certificate-authority-2.png)  

De volgende beschrijvingen komen overeen met de groene letterlijke stappen in het voor gaande diagram.

1. In het bovenstaande diagram maakt uw toepassing een certificaat dat intern begint met het maken van een sleutel in uw sleutelkluis.
2. Key Vault verzendt een TLS/SSL-certificaat aanvraag naar de certificerings instantie.
3. Uw toepassing bevraagt, in een proces van lussen en wachten, uw Key Vault voor het voltooien van het certificaat. Het maken van het certificaat is voltooid wanneer Key Vault de reactie van de certificeringsinstantie met x509-certificaat ontvangt.
4. De CA reageert op de TLS/SSL-certificaat aanvraag van Key Vault met een TLS/SSL X. 509-certificaat.
5. Het nieuwe certificaat is gemaakt met de fusie van het TLS/SSL X. 509-certificaat voor de CA.

## <a name="asynchronous-process"></a>Asynchroon proces
Het maken van een KV-certificaat is een asynchroon proces. Met deze bewerking wordt een KV-certificaat aanvraag gemaakt en wordt een HTTP-status code van 202 (geaccepteerd) geretourneerd. De status van de aanvraag kan worden gevolgd door te pollen van het object dat in behandeling is gemaakt door deze bewerking. De volledige URI van het in behandeling zijnde object wordt geretourneerd in de locatie-header.  

Wanneer een aanvraag voor het maken van een KV-certificaat is voltooid, wordt de status van het object in behandeling gewijzigd in ' voltooid ' van ' InProgress ' en wordt er een nieuwe versie van het KV-certificaat gemaakt. Dit wordt de huidige versie.  

## <a name="first-creation"></a>Eerst maken
 Wanneer er voor de eerste keer een KV-certificaat wordt gemaakt, wordt er ook een adresseer bare sleutel en een geheim gemaakt met dezelfde naam als die van het certificaat. Als de naam al in gebruik is, mislukt de bewerking met de HTTP-status code 409 (conflict).
De adresseer bare sleutel en het geheim ontvangen hun kenmerken van de kenmerken van het KV-certificaat. De adresseer bare sleutel en het geheim die op deze manier zijn gemaakt, zijn gemarkeerd als beheerde sleutels en geheimen, waarvan de levens duur wordt beheerd door Key Vault. Beheerde sleutels en geheimen hebben het kenmerk alleen-lezen. Opmerking: als een KV-certificaat verloopt of is uitgeschakeld, worden de bijbehorende sleutel en het betreffende geheim niet meer bruikbaar.  

 Als dit de eerste bewerking is voor het maken van een KV-certificaat, is een beleid vereist.  U kunt ook een beleid opgeven bij opeenvolgende Create-bewerkingen om de beleids bron te vervangen. Als er geen beleid is opgegeven, wordt de beleids bron voor de service gebruikt om een volgende versie van het KV-certificaat te maken. Houd er rekening mee dat wanneer een aanvraag voor het maken van een volgende versie wordt uitgevoerd, het huidige KV-certificaat en de bijbehorende adresseer bare sleutel en geheim, ongewijzigd blijven.  

## <a name="self-issued-certificate"></a>Zelf-verleend certificaat
 Als u een zelf-verleend certificaat wilt maken, stelt u de naam van de verlener in het certificaat beleid in, zoals wordt weer gegeven in het volgende code fragment van het certificaat beleid.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Als de naam van de certificaat verlener niet is opgegeven, wordt de naam van de verlener ingesteld op ' onbekend '. Wanneer de verlener ' onbekend ' is, moet de eigenaar van het certificaat hand matig een x509-certificaat van de verlener van zijn/haar keuze ophalen en vervolgens het open bare x509-certificaat samen voegen met het sleutel kluis certificaat dat in behandeling is om het maken van het certificaat te volt ooien.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Certificerings instanties van de partner
Het maken van een certificaat kan hand matig worden voltooid of met een ' zelf-' verlener. Key Vault ook partners met bepaalde aanbieders van uitgevers om het maken van certificaten te vereenvoudigen. De volgende typen certificaten kunnen worden besteld voor sleutel kluis met deze partner Issuer-providers.  

|Provider|Certificaattype|Configuratie-installatie  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault biedt OV- of EV SSL-certificaten met DigiCert| [Integratiehandleiding](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault biedt OV- of EV SSL-certificaten met GlobalSign| [Integratiehandleiding](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

 Een certificaat Uitgever is een entiteit die wordt weer gegeven in Azure Key Vault (KV) als een CertificateIssuer-resource. Het wordt gebruikt om informatie op te geven over de bron van een KV-certificaat; naam van de verlener, provider, referenties en andere administratieve gegevens.

Houd er rekening mee dat wanneer een bestelling wordt geplaatst met de provider van de verlener, het mogelijk is om de x509-certificaat uitbreidingen en de geldigheids periode van het certificaat te vervangen op basis van het type certificaat.  

 Autorisatie: vereist de machtiging voor certificaten/maken.

## <a name="see-also"></a>Zie ook

 - [Het maken van certificaten controleren en beheren](create-certificate-scenarios.md)