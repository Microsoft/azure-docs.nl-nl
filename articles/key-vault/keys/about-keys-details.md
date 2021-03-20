---
title: Sleutel typen, algoritmen en bewerkingen-Azure Key Vault
description: Ondersteunde sleutel typen, algoritmen en bewerkingen (details).
services: key-vault
author: amitbapat
manager: msmbaldwin
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: ambapat
ms.openlocfilehash: 675c4f04ece322000ae0ebb44d6291c455db9397
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98133273"
---
# <a name="key-types-algorithms-and-operations"></a>Sleuteltypen, algoritmen en bewerkingen

Key Vault ondersteunt twee resource typen: kluizen en beheerde Hsm's. Beide typen resources ondersteunen verschillende versleutelings sleutels. Zie [over sleutels](about-keys.md)voor een overzicht van de ondersteunde sleutel typen, beveiligings typen per resource type.

De volgende tabel toont een samen vatting van sleutel typen en ondersteunde algoritmen.

|Sleuteltypen/grootten/curven| Encrypt, decrypt<br>(Wrap/Unwrap) | sign, verify | 
| --- | --- | --- |
|EC-P256, EC-P256K, EC-P384, EC-521|NA|ES256<br>ES256K<br>ES384<br>ES512|
|RSA 2K, 3K, 4K| RSA1_5<br>RSA-OAEP<br>RSA-OAEP-256|PS256<br>PS384<br>PS512<br>RS256<br>RS384<br>PS512<br>RSNULL| 
|AES 128-bit, 256-bit <br/>(Alleen beheerde HSM)| AES-KW<br>AES-GCM<br>AES-CBC| NA| 
|||

##  <a name="ec-algorithms"></a>EC-algoritmen
 De volgende algoritme-id's worden ondersteund met EC- en HSM-sleutels

### <a name="curve-types"></a>Curvetypen

-   **P-256**: de NIST-curve P-256, gedefinieerd op [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256K**: de SEC-curve SECP256K1, gedefinieerd op [SEC 2: Recommended Elliptic Curve Domain Parameters](https://www.secg.org/sec2-v2.pdf) (Engelstalig).
-   **P-384**: de NIST-curve P-384, gedefinieerd op [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521**: de NIST-curve P-521, gedefinieerd op [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

### <a name="signverify"></a>SIGN/VERIFY

-   **ES256**: ECDSA voor SHA-256-hashes en -sleutels die zijn gemaakt met curve P-256. Dit algoritme wordt beschreven op [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K**: ECDSA voor SHA-256-hashes en -sleutels die zijn gemaakt met curve P-256K. Dit algoritme is in afwachting van standaardisatie.
-   **ES384**: ECDSA voor SHA-384-hashes en -sleutels die zijn gemaakt met curve P-384. Dit algoritme wordt beschreven op [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512**: ECDSA voor SHA-512-hashes en -sleutels die zijn gemaakt met curve P-512. Dit algoritme wordt beschreven op [RFC7518](https://tools.ietf.org/html/rfc7518).

##  <a name="rsa-algorithms"></a>RSA-algoritmen  
 De volgende algoritme-id's worden ondersteund met RSA- en RSA-HSM-sleutels  

### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, ENCRYPT/DECRYPT

-   **RSA1_5**: RSAES-PKCS1-V1_5 [RFC3447] sleutelversleuteling  
-   **RSA-OAEP**: RSAES met behulp van Optimal Asymmetric Encryption Padding (OAEP) [RFC3447], met de standaardparameters die worden beschreven bij RFC 3447 in sectie A.2.1. Deze standaardparameters maken gebruik van een hash-functie van SHA-1 en een maskergenereerfunctie van MGF1 met SHA-1.  
-  **RSA-OAEP-256** – RSAES met behulp van optimale asymmetrische versleutelingsopvulling met een hashfunctie van SHA-256 en een maskergeneratiefunctie van MGF1 met SHA-256

### <a name="signverify"></a>SIGN/VERIFY

-   **PS256**: RSASSA-PSS met behulp van SHA-256 en MGF1 met SHA-256, zoals wordt beschreven in [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS384**: RSASSA-PSS met behulp van SHA-384 en MGF1 met SHA-384, zoals wordt beschreven in [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS512**: RSASSA-PSS met behulp van SHA-512 en MGF1 met SHA-512, zoals wordt beschreven in [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **RS256**: RSASSA-PKCS-v1_5 met behulp van SHA-256. De door de toepassing geleverde hash-waarde moet worden berekend met SHA-256 en moet 32 bytes lang zijn.  
-   **RS384**: RSASSA-PKCS-v1_5 met behulp van SHA-384. De door de toepassing geleverde hash-waarde moet worden berekend met SHA-384 en moet 48 bytes lang zijn.  
-   **RS512**: RSASSA-PKCS-v1_5 met behulp van SHA-512. De door de toepassing geleverde hash-waarde moet worden berekend met SHA-512 en moet 64 bytes lang zijn.  
-   **RSNULL**: zie [RFC2437](https://tools.ietf.org/html/rfc2437), een gespecialiseerde use-case om bepaalde TLS-scenario's in te schakelen.  

##  <a name="symmetric-key-algorithms-managed-hsm-only"></a>Symmetrische sleutel algoritmen (alleen beheerde HSM)
- **AES-KW** - AES Key Wrap ([RFC3394](https://tools.ietf.org/html/rfc3394)).
- **AES-GCM** -AES-versleuteling in de Galois-tellers modus ([NIST SP 800-38d](https://csrc.nist.gov/publications/sp800))
- **AES-CBC** -AES-versleuteling in de coderings modus Cipher Block ([NIST SP 800-38A](https://csrc.nist.gov/publications/sp800))

> [!NOTE] 
> Huidige AES-GCM-implementatie en de bijbehorende API's zijn experimenteel. De implementatie en de API's kunnen aanzienlijk veranderen in de toekomstige iteraties. 

##  <a name="key-operations"></a>Sleutelbewerkingen

Beheerd HSM ondersteunt de volgende bewerkingen op sleutelobjecten:  

-   **Maken**: Hiermee kan een client een sleutel maken in Key Vault. De waarde van de sleutel wordt gegenereerd en opgeslagen in Key Vault, en wordt niet vrijgegeven aan de client. Er kunnen asymmetrische sleutels worden gemaakt in Key Vault.  
-   **Importeren**: Hiermee kan een client een bestaande sleutel importeren in Key Vault. Er kunnen asymmetrische sleutels worden geïmporteerd in Key Vault met behulp van een aantal verschillende pakketmethoden in een JWK-constructie. 
-   **Bijwerken**: Hiermee kan een client met voldoende machtigingen de metagegevens (sleutelkenmerken) van een sleutel die eerder is opgeslagen in Key Vault wijzigen.  
-   **Verwijderen**: Hiermee kan een client met voldoende machtigingen een sleutel uit Key Vault verwijderen.  
-   **Weergeven**: Hiermee kan een client een lijst met alle sleutels in een bepaalde Key Vault weergeven.  
-   **Versies weergeven**: Hiermee kan een client een lijst met alle versies van een bepaalde sleutel in een bepaalde Key Vault weergeven.  
-   **Ophalen**: Hiermee kan een client de openbare onderdelen van een bepaalde sleutel in een Key Vault ophalen.  
-   **Back-ups maken**: Hiermee wordt een sleutel in een beveiligde vorm geëxporteerd.  
-   **Herstellen**: Hiermee wordt een eerder gemaakte back-up van een sleutel geïmporteerd.  

Raadpleeg de [Sleutelbewerkingen in de REST API-naslag voor Key Vault](/rest/api/keyvault) voor meer informatie.  

Zodra een sleutel is gemaakt in Key Vault, kunnen de volgende cryptografische bewerkingen worden uitgevoerd met behulp van de sleutel:  

-   **Ondertekenen en controleren**: Strikt genomen is deze bewerking 'hash ondertekenen' of 'hash controleren', omdat Key Vault geen ondersteuning biedt voor hashing van inhoud als onderdeel van het maken van een handtekening. Toepassingen moeten de te ondertekenen gegevens lokaal hashen en vervolgens een aanvraag bij Key Vault indienen om de hash te laten ondertekenen. Verificatie van ondertekende hashes wordt ondersteund als een gebruiksvriendelijke bewerking voor toepassingen die mogelijk geen toegang hebben tot [openbaar] sleutelmateriaal. Voor de beste prestaties van toepassingen moeten verificatiebewerkingen (VERIFY) lokaal worden uitgevoerd.  
-   **Sleutels versleutelen/verpakken**: Een sleutel die is opgeslagen in Key Vault, kan worden gebruikt om een andere sleutel te beveiligen. Meestal is dat een symmetrische sleutel voor het versleutelen van inhoud (CEK). Als de sleutel in Key Vault asymmetrisch is, wordt 'sleutel versleutelen' gebruikt. RSA-OAEP en de bewerkingen WRAPKEY/UNWRAPKEY (sleutel verpakken/uitpakken) zijn bijvoorbeeld gelijk aan de bewerkingen ENCRYPT/DECRYPT (versleutelen/ontsleutelen). Als de sleutel in Key Vault symmetrisch is, wordt 'sleutel verpakken' gebruikt. Bijvoorbeeld: AES-KW. WRAPKEY wordt ondersteund als een gebruiksvriendelijke bewerking voor toepassingen die mogelijk geen toegang hebben tot [openbaar] sleutelmateriaal. Voor de beste prestaties van toepassingen moeten sleutelverpakkingsbewerkingen (WRAPKEY) lokaal worden uitgevoerd.  
-   **Versleutelen en ontsleutelen**: Een sleutel die is opgeslagen in Key Vault, kan worden gebruikt voor het versleutelen of ontsleutelen van één blok gegevens. De grootte van het blok wordt bepaald door het sleuteltype en het geselecteerde versleutelingsalgoritme. ENCRYPT (versleutelen) wordt ondersteund als een gebruiksvriendelijke bewerking voor toepassingen die mogelijk geen toegang hebben tot [openbaar] sleutelmateriaal. Voor de beste prestaties van toepassingen moeten versleutelingsbewerkingen (ENCRYPT) lokaal worden uitgevoerd.  

Hoewel WRAPKEY/UNWRAPKEY (sleutels verpakken/uitpakken) met behulp van asymmetrische sleutels wellicht overbodig lijkt omdat de bewerking gelijk is aan ENCRYPT/DECRYPY (versleutelen/ontsleutelen), is het gebruik van afzonderlijke bewerkingen belangrijk. Het onderscheid biedt semantische scheiding en autorisatiescheiding van deze bewerkingen, en consistentie wanneer andere sleuteltypen door de service worden ondersteund.  

Key Vault biedt geen ondersteuning voor EXPORT-bewerkingen. Als een sleutel eenmaal is ingericht in het systeem, kan deze niet meer worden geëxtraheerd en kan het sleutelmateriaal niet meer worden gewijzigd. Gebruikers van Key Vault kunnen hun sleutel echter voor andere use-cases nodig hebben, bijvoorbeeld nadat deze is verwijderd. In dit geval kunnen ze gebruikmaken van de back-up- en herstelbewerkingen (BACKUP en RESTORE) om de sleutel in een beveiligde vorm te exporteren/importeren. Sleutels die zijn gemaakt met de BACKUP-bewerking, kunnen niet buiten Key Vault worden gebruikt. De IMPORT-bewerking kan ook worden gebruikt voor meerdere exemplaren van Key Vault.  

Gebruikers kunnen de cryptografische bewerkingen die Key Vault ondersteunt per sleutel beperken met behulp van de eigenschap key_ops van het JWK-object.  

Zie [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41) voor meer informatie over JWK-objecten.  

## <a name="key-attributes"></a>Sleutelkenmerken

Naast het sleutelmateriaal kunnen de volgende kenmerken worden opgegeven. In een JSON-aanvraag zijn een sleutelwoord en accolades ({}) om kenmerken vereist, zelfs als er geen kenmerken zijn opgegeven.  

- *enabled*: boolean, optioneel; standaardwaarde is **true**. Hiermee geeft u op of de sleutel is ingeschakeld en kan worden gebruikt voor cryptografische bewerkingen. Het kenmerk *enabled* (ingeschakeld) wordt gebruikt in combinatie met *nbf* en *exp*. Bewerkingen tussen *nbf* en *exp* worden alleen toegestaan *enabled* is ingesteld op **true**. Bewerkingen buiten het venster *nbf* / *exp* worden automatisch geweigerd, met uitzondering van bepaalde typen bewerkingen onder [bepaalde voorwaarden](#date-time-controlled-operations).
- *nbf*: IntDate, optioneel; standaardwaarde is 'now' (nu). Het kenmerk *nbf* (niet vóór) geeft de tijd aan waarvoor de sleutel NIET mag worden gebruikt voor cryptografische bewerkingen, met uitzonderingen van bepaalde typen bewerkingen onder [bepaalde voorwaarden](#date-time-controlled-operations). Voor de verwerking van het kenmerk *nbf* MOET de huidige datum/tijd later of gelijk zijn aan de niet-vóór-datum/tijd die wordt vermeld in het kenmerk *nbf*. Door Key Vault kan enig tijdsverschil (van hooguit enkele minuten) worden toegestaan, om rekening te houden met ongelijke kloktijden. De waarde MOET een getal zijn dat een IntDate-waarde bevat.  
- *exp*: IntDate, optioneel; standaardwaarde is 'forever' (permanent). Het kenmerk *exp* (vervaltijd) geeft de vervaltijd aan waarop of waarna de sleutel NIET mag worden gebruikt voor cryptografische bewerkingen, met uitzondering van bepaalde typen bewerkingen onder [bepaalde voorwaarden](#date-time-controlled-operations). Voor de verwerking van het kenmerk *exp* MOET de huidige datum/tijd eerder zijn dan de vervaldatum/-tijd die wordt vermeld in het kenmerk *exp*. Door Key Vault kan enig tijdsverschil (van hooguit enkele minuten) worden toegestaan, om rekening te houden met ongelijke kloktijden. De waarde MOET een getal zijn dat een IntDate-waarde bevat.  

Er zijn aanvullende alleen-lezen kenmerken die zijn opgenomen in elk antwoord dat sleutelkenmerken bevat:  

- *created*: IntDate, optioneel. Het kenmerk *created* geeft aan wanneer deze versie van de sleutel is gemaakt. De waarde is null voor sleutels die zijn gemaakt vóór het toevoegen van dit kenmerk. De waarde MOET een getal zijn dat een IntDate-waarde bevat.  
- *updated*: IntDate, optioneel. Het kenmerk *updated* geeft aan wanneer deze versie van de sleutel is bijgewerkt. De waarde is null voor sleutels die voor het laatst zijn bijgewerkt vóór het toevoegen van dit kenmerk. De waarde MOET een getal zijn dat een IntDate-waarde bevat.  

Zie [Over sleutels, geheimen en certificaten: [Gegevenstypen](../general/about-keys-secrets-certificates.md#data-types) voor meer informatie over IntDate.

### <a name="date-time-controlled-operations"></a>Bewerkingen met datum-/tijdcontrole

Nog-niet-geldige en vervallen sleutels, buiten het venster *nbf* / *exp*, zijn te gebruiken voor **decrypt**-, **unwrap**- en **verify**-bewerkingen (zonder 403, Verboden te retourneren). De nog-niet-geldig-status maakt het mogelijk om een sleutel te testen vóór gebruik in een productieomgeving. De vervallen-status maakt het mogelijk om herstelbewerkingen uit te voeren voor gegevens die zijn gemaakt toen de sleutel geldig was. U kunt ook de toegang tot een sleutel uitschakelen met behulp van Key Vault-beleid of door het kenmerk *enabled* te wijzigen in **false**.

Zie [Gegevenstypen](../general/about-keys-secrets-certificates.md#data-types) voor meer informatie over gegevenstypen.

Zie [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41) voor meer informatie over andere mogelijke kenmerken.

## <a name="key-tags"></a>Sleuteltags

U kunt aanvullende toepassingsspecifieke metagegevens opgeven in de vorm van tags. Key Vault ondersteunt maximaal 15 tags, die elk een naam van 256 tekens en een waarde van 256 tekens kunnen bevatten.  

> [!NOTE] 
> Labels kunnen worden gelezen door een oproepende functie als ze de machtiging *list* (weergeven) of *get* (ophalen) voor die sleutel hebben.

##  <a name="key-access-control"></a>Toegangsbeheer voor sleutels

Toegangsbeheer voor sleutels die door Key Vault worden beheerd, wordt geboden op het niveau van een Key Vault die fungeert als sleutelcontainer. Het beleid voor toegangsbeheer voor sleutels verschilt van het beleid voor toegangsbeheer voor geheimen in dezelfde Key Vault. Gebruikers kunnen een of meer kluizen maken voor het bewaren van sleutels en moeten sleutels op de juiste wijze segmenteren en beheren voor hun scenario. Toegangsbeheer voor sleutels staat los van toegangsbeheer voor geheimen.  

De volgende machtigingen kunnen worden toegekend per gebruiker of service-principal, in de ACE-vermelding voor sleutels in een kluis. Deze machtigingen zijn een nauwkeurige afspiegeling van de bewerkingen die zijn toegestaan voor een sleutelobject.  Het verlenen van toegang aan een Service-Principal in de sleutel kluis is een eenmalige-bewerking. deze blijft hetzelfde voor alle Azure-abonnementen. U kunt deze gebruiken om zoveel certificaten te implementeren als u wilt. 

- Machtigingen voor sleutelbeheerbewerkingen
  - *get*: Het openbare gedeelte van een sleutel lezen, plus de kenmerken van die sleutel
  - *list*: De sleutels of de versies van sleutels die zijn opgeslagen in een sleutelkluis weergeven
  - *update*: De kenmerken voor een sleutel bijwerken
  - *create*: Nieuwe sleutels maken
  - *import*: Een sleutel importeren in een sleutelkluis
  - *delete*: Het sleutelobject verwijderen
  - *recover*: Een verwijderde sleutel herstellen
  - *backup*: Een back-up van een sleutel in een sleutelkluis maken
  - *restore*: Een back-up van een sleutel terugzetten naar een sleutelkluis

- Machtigingen voor cryptografische bewerkingen
  - *decrypt*: De sleutel gebruiken voor het ontsleutelen van een reeks bytes
  - *encrypt*: De sleutel gebruiken voor het versleutelen van een reeks bytes
  - *unwrapKey*: De sleutel gebruiken voor het ontsleutelen van verpakte symmetrische sleutels
  - *wrapKey*: De sleutel gebruiken voor het versleutelen van een symmetrische sleutel
  - *verify*: De sleutel gebruiken om hashes te controleren  
  - *sign*: De sleutel gebruiken om hashes te ondertekenen
    
- Machtigingen voor bevoorrechte bewerkingen
  - *purge*: Een verwijderde sleutel opschonen (permanent verwijderen)

Raadpleeg de [Sleutelbewerkingen in de REST API-naslag voor Key Vault](/rest/api/keyvault) voor meer informatie over het werken met sleutels. Raadpleeg [Kluizen: maken of bijwerken](/rest/api/keyvault/vaults/createorupdate) en [Kluizen: toegangsbeleid bijwerken](/rest/api/keyvault/vaults/updateaccesspolicy) voor meer informatie over het instellen van machtigingen. 

## <a name="next-steps"></a>Volgende stappen
- [Informatie over Key Vault](../general/overview.md)
- [Informatie over beheerde HSM](../managed-hsm/overview.md)
- [Over geheimen](../secrets/about-secrets.md)
- [Over certificaten](../certificates/about-certificates.md)
- [Overzicht REST-API voor Key Vault](../general/about-keys-secrets-certificates.md)
- [Verificatie, aanvragen en antwoorden](../general/authentication-requests-and-responses.md)
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)
