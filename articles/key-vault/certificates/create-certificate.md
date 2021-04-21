---
title: Methoden voor het maken van certificaten
description: Meer informatie over verschillende opties voor het maken of importeren van Key Vault certificaat in Azure Key Vault. Er zijn verschillende manieren om een Key Vault maken.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 72ff2a1a7b8bcff768248833183ce03a169f9a4d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752116"
---
# <a name="certificate-creation-methods"></a>Methoden voor het maken van certificaten

 Een Key Vault (KV)-certificaat kan worden gemaakt of geïmporteerd in een sleutelkluis. Wanneer er een KV-certificaat wordt gemaakt, wordt de persoonlijke sleutel gemaakt in de sleutelkluis en nooit zichtbaar voor de eigenaar van het certificaat. Hier volgen enkele manieren om een certificaat te maken in Key Vault:  

-   **Een zelf-ondertekend certificaat maken:** Hiermee maakt u een openbaar-persoonlijk sleutelpaar en koppelt u dit aan een certificaat. Het certificaat wordt ondertekend met een eigen sleutel.  

-    **Maak handmatig een nieuw certificaat:** Hiermee maakt u een openbaar-persoonlijk sleutelpaar en genereert u een X.509-aanvraag voor certificaat-ondertekening. De ondertekeningsaanvraag kan worden ondertekend door uw registratie-instantie of certificeringsinstantie. Het ondertekende x509-certificaat kan worden samengevoegd met het sleutelpaar dat in behandeling is om het KV-certificaat in de Key Vault. Hoewel voor deze methode meer stappen nodig zijn, biedt deze wel betere beveiliging omdat de persoonlijke sleutel wordt gemaakt in en wordt beperkt tot Key Vault. Dit wordt uitgelegd in het onderstaande diagram.  

![Een certificaat maken met uw eigen certificeringsinstantie](../media/certificate-authority-1.png)  

De volgende beschrijvingen komen overeen met de stappen met groene letters in het voorgaande diagram.

1. In het bovenstaande diagram maakt uw toepassing een certificaat dat intern begint met het maken van een sleutel in uw sleutelkluis.
2. Key Vault retourneert naar uw toepassing een certificate signing request (CSR)
3. Uw toepassing stuurt de CSR door naar de gekozen certificeringsinstantie.
4. De gekozen CA reageert met een X509-certificaat.
5. Uw toepassing voltooit het maken van het nieuwe certificaat met een fusie van het X509-certificaat van uw CA.

-   **Maak een certificaat met een bekende verlenerprovider:** Voor deze methode moet u een een time-task uitvoeren voor het maken van een vereenigingsobject. Zodra een vergeverobject in uw sleutelkluis is gemaakt, kan naar de naam ervan worden verwezen in het beleid van het KV-certificaat. Een aanvraag voor het maken van een dergelijk KV-certificaat maakt een sleutelpaar in de kluis en communiceert met de providerservice van de verlener met behulp van de informatie in het object waarnaar wordt verwezen om een x509-certificaat op te halen. Het x509-certificaat wordt opgehaald uit de verlenerservice en wordt samengevoegd met het sleutelpaar om het maken van het KV-certificaat te voltooien.  

![Een certificaat maken met een Key Vault van een partnercertificaatinstantie](../media/certificate-authority-2.png)  

De volgende beschrijvingen komen overeen met de stappen met groene letters in het voorgaande diagram.

1. In het bovenstaande diagram maakt uw toepassing een certificaat dat intern begint met het maken van een sleutel in uw sleutelkluis.
2. Key Vault verzendt een TLS/SSL-certificaataanvraag naar de CA.
3. Uw toepassing bevraagt, in een proces van lussen en wachten, uw Key Vault voor het voltooien van het certificaat. Het maken van het certificaat is voltooid wanneer Key Vault de reactie van de certificeringsinstantie met x509-certificaat ontvangt.
4. De CA reageert op Key Vault TLS/SSL-certificaataanvraag met een TLS/SSL X.509-certificaat.
5. Het maken van het nieuwe certificaat wordt voltooid met de samenvoeging van het TLS/SSL X.509-certificaat voor de CA.

## <a name="asynchronous-process"></a>Asynchroon proces
Het maken van KV-certificaten is een asynchroon proces. Met deze bewerking maakt u een KV-certificaataanvraag en retourneerd u de HTTP-statuscode 202 (Geaccepteerd). De status van de aanvraag kan worden bijgespoord door het object in behandeling te peilen dat door deze bewerking is gemaakt. De volledige URI van het object in behandeling wordt geretourneerd in de LOCATION-header.  

Wanneer een aanvraag voor het maken van een KV-certificaat is voltooid, wordt de status van het object in behandeling gewijzigd in 'voltooid' van 'wordt uitgevoerd' en wordt er een nieuwe versie van het KV-certificaat gemaakt. Dit wordt de huidige versie.  

## <a name="first-creation"></a>Eerste aanmaak
 Wanneer een KV-certificaat voor het eerst wordt gemaakt, worden er ook een adresseerbare sleutel en geheim gemaakt met dezelfde naam als die van het certificaat. Als de naam al in gebruik is, mislukt de bewerking met de HTTP-statuscode 409 (conflict).
De adresseerbare sleutel en het geheim halen hun kenmerken op uit de KV-certificaatkenmerken. De adresseerbare sleutel en het geheim die op deze manier zijn gemaakt, worden gemarkeerd als beheerde sleutels en geheimen, waarvan de levensduur wordt beheerd door Key Vault. Beheerde sleutels en geheimen zijn alleen-lezen. Opmerking: Als een KV-certificaat verloopt of is uitgeschakeld, zijn de bijbehorende sleutel en het geheim niet meer bruikbaar.  

 Als dit de eerste bewerking is om een KV-certificaat te maken, is een beleid vereist.  Een beleid kan ook worden opgegeven met opeenvolgende maakbewerkingen ter vervanging van de beleidsresource. Als er geen beleid wordt opgegeven, wordt de beleidsresource in de service gebruikt om een volgende versie van het KV-certificaat te maken. Houd er rekening mee dat een aanvraag voor het maken van een volgende versie wordt uitgevoerd, maar dat het huidige KV-certificaat en de bijbehorende adresseerbare sleutel en het bijbehorende geheim ongewijzigd blijven.  

## <a name="self-issued-certificate"></a>Zelf uitgegeven certificaat
 Als u een zelf-uitgegeven certificaat wilt maken, stelt u de naam van de vergever in het certificaatbeleid in op 'Zelf', zoals wordt weergegeven in het volgende fragment van het certificaatbeleid.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Als de naam van de vergever niet is opgegeven, wordt de naam van de vergever ingesteld op Onbekend. Wanneer de vergever Onbekend is, moet de eigenaar van het certificaat handmatig een x509-certificaat van de vergever van zijn/haar keuze verkrijgen en vervolgens het openbare x509-certificaat samenvoegen met het object key vault-certificaat in behandeling om het maken van het certificaat te voltooien.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Partner-CA-providers
Het maken van certificaten kan handmatig worden uitgevoerd of met behulp van een 'Self'-vergever. Key Vault ook samenwerken met bepaalde verlenerproviders om het maken van certificaten te vereenvoudigen. De volgende typen certificaten kunnen worden besteld voor de sleutelkluis bij deze partnerverlenerproviders.  

|Provider|Certificaattype|Configuratie-installatie  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault biedt OV- of EV SSL-certificaten met DigiCert| [Integratiehandleiding](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault biedt OV- of EV SSL-certificaten met GlobalSign| [Integratiehandleiding](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

 Een certificaatverkender is een entiteit die in Azure Key Vault (KV) wordt weergegeven als een CertificateIssuer-resource. Het wordt gebruikt om informatie over de bron van een KV-certificaat op te geven; naam van verlener, provider, referenties en andere beheergegevens.

Houd er rekening mee dat wanneer een bestelling bij de verlenerprovider wordt geplaatst, de x509-certificaatextensies en de geldigheidsperiode van het certificaat kunnen worden overgenomen of overschrijven op basis van het type certificaat.  

 Autorisatie: hiervoor is de machtiging certificaten/maken vereist.

## <a name="see-also"></a>Zie ook

 - Handleiding voor het maken van certificaten in Key Vault [portal,](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-portal) [Azure CLI](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-cli) [en Azure PowerShell](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-powershell)
 - [Het maken van certificaten controleren en beheren](create-certificate-scenarios.md)
