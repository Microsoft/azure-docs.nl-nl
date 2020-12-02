---
title: Informatie over Azure Key Vault-certificaten - Azure Key Vault
description: Overzicht van REST-interface en -certificaten van Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 66f077028b9f9f7a7644a318d4447eeaaab19e98
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94919927"
---
# <a name="about-azure-key-vault-certificates"></a>Informatie over Azure Key Vault-certificaten

Ondersteuning voor Key Vault-certificaten voorziet in het beheer van uw x509-certificaten en het volgende gedrag:  

-   Hiermee kan een certificaateigenaar een certificaat maken met behulp van een Key Vault-aanmaakproces of door middel van het importeren van een bestaand certificaat. Hierin zijn zowel zelf-ondertekende als door de certificeringsinstantie gegenereerde certificaten inbegrepen.
-   Hiermee kan de eigenaar van een Key Vault-certificaat de beveiligde opslag en beheer van x509-certificaten implementeren zonder interactie met privésleutelmateriaal.  
-   Hiermee kan een certificaateigenaar een beleid maken dat Key Vault aanstuurt om de levenscyclus van een certificaat te beheren.  
-   Hiermee kunnen eigenaren van certificaten contactgegevens opgeven voor meldingen over levenscyclusgebeurtenissen zoals de vervaldatum en verlenging van het certificaat.  
-   Hiermee wordt ondersteuning geboden voor automatische verlenging met geselecteerde certificaatverleners: x509-certificaatproviders /certificeringsinstanties die Key Vault-partner zijn.

>[!Note]
>Providers/instanties die geen partner zijn, zijn ook toegestaan, maar bieden geen ondersteuning voor de functie voor automatisch verlengen.

## <a name="composition-of-a-certificate"></a>Samenstelling van een certificaat

Wanneer er een Key Vault-certificaat wordt gemaakt, worden er ook een adresseerbare sleutel en een geheim gemaakt met dezelfde naam. Met de Key Vault sleutel kunnen sleutelbewerkingen worden uitgevoerd en met het Key Vault-geheim kan de certificaatwaarde als geheim worden opgehaald. Een Key Vault-certificaat bevat ook openbare metagegevens voor x509-certificaten.  

De id en de versie van certificaten zijn vergelijkbaar met die van sleutels en geheimen. Er is een specifieke versie beschikbaar van een adresseerbare sleutel en een geheim die met de Key Vault-certificaatversie zijn gemaakt in het Key Vault-certificaatantwoord.
 
![Certificaten zijn complexe objecten](../media/azure-key-vault.png)

## <a name="exportable-or-non-exportable-key"></a>Exporteerbare of niet-exporteerbare sleutel

Wanneer een Key Vault-certificaat wordt gemaakt, kan het worden opgehaald uit het adresseerbare geheim met de privésleutel in een PFX- of PEM-indeling. Het beleid dat wordt gebruikt voor het maken van het certificaat moet aangeven dat de sleutel exporteerbaar is. Als het beleid aangeeft dat het niet kan worden geëxporteerd, is de privésleutel geen onderdeel van de waarde wanneer deze wordt opgehaald als geheim.  

De adresseerbare sleutel wordt relevanter bij niet-exporteerbare KV-certificaten. De adresseerbare bewerkingen voor de KV-sleutel worden toegewezen uit het veld *keyusage* van het KV-certificaatbeleid dat wordt gebruikt om het KV-certificaat te maken.  

Het type sleutelpaar dat wordt ondersteund voor certificaten

 - Ondersteunde sleuteltypen: RSA, RSA-HSM, EC, EC-HSM, oct ([hier](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype) genoemd) Exporteren is alleen toegestaan met RSA, EC. HSM-sleutels zouden niet exporteerbaar zijn.

|Type sleutel|Over|Beveiliging|
|--|--|--|
|**RSA**| Met software beveiligde RSA-sleutel|FIPS 140-2 Level 1|
|**RSA-HSM**| "Met HSM beveiligde" RSA-sleutel (alleen Premium SKU)|FIPS 140-2 Level 2 HSM|
|**EC**| Met software beveiligde Elliptic Curve-sleutel|FIPS 140-2 Level 1|
|**EC-HSM**| "Met HSM beveiligde" Elliptic Curve-sleutel (alleen Premium SKU)|FIPS 140-2 Level 2 HSM|
|||

## <a name="certificate-attributes-and-tags"></a>Certificaatkenmerken en -tags

Naast metagegevens van het certificaat, een adresseerbare sleutel en een adresseerbaar geheim bevat een Key Vault-certificaat ook kenmerken en tags.  

### <a name="attributes"></a>Kenmerken

De certificaatkenmerken worden gespiegeld met kenmerken van de adresseerbare sleutel en het geheim dat werd gemaakt toen het KV-certificaat werd gemaakt.  

Een Key Vault-certificaat heeft de volgende kenmerken:  

-   *enabled*: boolean, optioneel; standaardwaarde is **true**. Kan worden opgegeven om aan te geven of de certificaatgegevens kunnen worden opgehaald als geheim of bruikbaar als sleutel. Wordt tevens gebruikt in combinatie met *nbf* en *exp* wanneer er een bewerking plaatsvindt tussen *nbf* en *exp*, en wordt alleen toegestaan als enabled is ingesteld op true. Bewerkingen buiten het venster van *nbf* en *exp* worden automatisch niet toegestaan.  

Er zijn aanvullende alleen-lezen kenmerken die zijn opgenomen in de volgende antwoorden:

-   *created*: IntDate - geeft aan wanneer deze versie van het certificaat is gemaakt.  
-   *updated*: IntDate - geeft aan wanneer deze versie van het certificaat is bijgewerkt.  
-   *exp*: IntDate - bevat de waarde van de vervaldatum van het x509-certificaat.  
-   *nbf*: IntDate - bevat de waarde van de datum van het x509-certificaat.  

> [!Note] 
> Als een certificaat van Key Vault verloopt, werken de adresseerbare sleutel en het geheim van het certificaat niet meer.  

### <a name="tags"></a>Tags

 De client heeft een woordenboek opgegeven van sleutel-waardeparen, vergelijkbaar met de tags in sleutels en geheimen.  

 > [!Note]
> Labels kunnen worden gelezen door een oproepende functie als ze de machtiging *list* (weergeven) of *get* (ophalen) voor dat objecttype (sleutels, geheimen of certificaten) hebben.

## <a name="certificate-policy"></a>Certificaatbeleid

Een certificaatbeleid bevat informatie over het maken en beheren van de levenscyclus van een Key Vault-certificaat. Wanneer een certificaat met een privésleutel in de sleutelkluis wordt geïmporteerd, wordt standaardbeleid gemaakt door het x509-certificaat te lezen.  

Wanneer een volledig nieuw Key Vault-certificaat wordt gemaakt, moet er een beleid worden aangeleverd. Het beleid bepaalt hoe u deze Key Vault-certificaatversie maakt, of de volgende Key Vault-certificaatversie. Als er beleid is ingesteld, is dit niet nodig bij daaropvolgende nieuwe bewerkingen voor toekomstige versies. Er is slechts één beleidsexemplaar voor alle versies van een Key Vault-certificaat.  

Op hoog niveau bevat certificaatbeleid de volgende informatie (de definities ervan zijn [hier](/powershell/module/az.keyvault/set-azkeyvaultcertificatepolicy?view=azps-4.4.0) te vinden):  

-   Eigenschappen X509-certificaat: Bevat de onderwerpnaam, alternatieve onderwerpnamen en andere eigenschappen waarmee de aanvraag van een x509-certificaat wordt gemaakt.  
-   Sleuteleigenschappen: bevat sleuteltype, sleutellengte, exporteerbare en ReuseKeyOnRenewal-velden. Met deze velden wordt de sleutelkluis geïnstrueerd hoe een sleutel moet worden gegenereerd. 
     - Ondersteunde sleuteltypen: RSA, RSA-HSM, EC, EC-HSM, oct ([hier](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype) vermeld) 
-   Geheimeigenschappen: bevat geheimeigenschappen, zoals het inhoudstype van adresseerbare geheim om de geheime waarde te genereren, voor het ophalen van een certificaat als geheim.  
-   Levensduuracties: bevat levensduuracties voor het KV-certificaat. Elke levensduuractie bevat:  

     - Een trigger, opgegeven in dagen vóór de vervaldatum of het percentage van de levensduur  

     - Actie, waarmee het type actie wordt opgegeven: *emailContacts* of *autoRenew*  

-   Verlener: Parameters over de certificaatverlener die wordt gebruikt om x509-certificaten uit te geven.  
-   Beleidskenmerken: bevat kenmerken die zijn gekoppeld aan het beleid  

### <a name="x509-to-key-vault-usage-mapping"></a>Toewijzing van X509 aan Key Vault-gebruik

De volgende tabel geeft de toewijzing van het gebruiksbeleid voor x509-sleutels weer voor geldige sleutelbewerkingen van een sleutel die is gemaakt bij het maken van een Key Vault-certificaat.

|**Vlaggen voor X509-sleutelgebruik**|**Key Vault-sleutelbewerkingen**|**Standaardgedrag**|
|----------|--------|--------|
|DataEncipherment|encrypt, decrypt| N.v.t. |
|DecipherOnly|decrypt| N.v.t.  |
|DigitalSignature|sign, verify| Key Vault-standaardinstelling zonder een gebruiksspecificatie op de aanmaaktijd van het certificaat | 
|EncipherOnly|encrypt| N.v.t. |
|KeyCertSign|sign, verify|N.v.t.|
|KeyEncipherment|wrapKey, unwrapKey| Key Vault-standaardinstelling zonder een gebruiksspecificatie op de aanmaaktijd van het certificaat | 
|NonRepudiation|sign, verify| N.v.t. |
|crlsign|sign, verify| N.v.t. |

## <a name="certificate-issuer"></a>Certificaatverlener

Een Key Vault-certificaatobject bevat een configuratie die wordt gebruikt om te communiceren met een geselecteerde certificaatverlener/-provider om x509-certificaten te bestellen.  

-   Key Vault-partners met de volgende certificaatverleners/-providers van TLS/SSL-certificaten

|**Naam van provider**|**Locaties**|
|----------|--------|
|DigiCert|Ondersteund in alle Key Vault-servicelocaties in de openbare cloud en Azure Government|
|GlobalSign|Ondersteund in alle Key Vault-servicelocaties in de openbare cloud en Azure Government|

Voordat een certificaatverlener kan worden gemaakt in een Key Vault, moeten de volgende stappen 1 en 2 worden uitgevoerd.  

1. Onboarding bij CA-providers (CA: certificeringsinstantie)  

    -   Een beheerder van de organisatie moet het bedrijf (bijvoorbeeld Contoso) onboarden met ten minste één CA-provider.  

2. De beheerder maakt referenties voor de aanvrager van Key Vault om TLS/SSL-certificaten in te schrijven (en te verlengen)  

    -   Deze biedt de configuratie die moet worden gebruikt voor het maken van een verlenerobject van de provider in de sleutelkluis  

Raadpleeg de blog [Key Vault-certificaten](/archive/blogs/kv/manage-certificates-via-azure-key-vault) Voor meer informatie over het maken van verlenerobjecten vanuit de portal Certificaten  

Met Key Vault kunnen meerdere verlenerobjecten worden gemaakt met een andere verlener-/providerconfiguratie. Zodra een verlenerobject is gemaakt, kan in een of meer certificaatbeleidsregels naar de naam ervan worden verwezen. In referenties naar het verlenerobject wordt Key Vault geïnstrueerd om van de configuratie gebruik te maken zoals die is opgegeven in het verlenerobject bij het aanvragen van het x509-certificaat van de CA-provider tijdens het maken en verlengen van het certificaat.  

Verlenerobjecten worden in de kluis gemaakt en kunnen alleen worden gebruikt bij KV-certificaten in dezelfde kluis.  

## <a name="certificate-contacts"></a>Certificaatcontactpersonen

Certificaatcontactpersonen bevatten contactgegevens om meldingen te verzenden die worden geactiveerd door de levensduurgebeurtenissen van het certificaat. De contactpersoongegevens worden gedeeld door alle certificaten in de sleutelkluis. Alle opgegeven contactpersonen krijgen een melding bij een gebeurtenis met betrekking tot een certificaat in de sleutelkluis. Kijk [hier](overview-renew-certificate.md#steps-to-set-certificate-notifications) voor meer informatie over het instellen van een certificaatcontactpersoon  

## <a name="certificate-access-control"></a>Toegangsbeheer voor certificaten

 Toegangsbeheer voor certificaten wordt beheerd door Key Vault en wordt gegeven door de Key Vault die deze certificaten bevat. Het beleid voor toegangsbeheer voor certificaten verschilt van het beleid voor sleutels en geheimen in dezelfde sleutelkluis. Gebruikers kunnen een of meer kluizen maken waarin zij certificaten kunnen bewaren en de segmentatie en het beheer van hun scenario naar behoren kunnen bijhouden.  Kijk [hier](certificate-access-control.md) voor meer informatie over toegangsbeheer voor certificaten

## <a name="next-steps"></a>Volgende stappen

- [Informatie over Key Vault](../general/overview.md)
- [Informatie over sleutels, geheimen en certificaten](../general/about-keys-secrets-certificates.md)
- [Over sleutels](../keys/about-keys.md)
- [Over geheimen](../secrets/about-secrets.md)
- [Verificatie, aanvragen en antwoorden](../general/authentication-requests-and-responses.md)
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)
