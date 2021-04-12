---
title: Beveiligings configuratie splitsen en samen voegen
description: Stel x409-certificaten in voor versleuteling met de Split/Merge-service voor Elastic scale.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 235efc550fd47d4244a5bf081c75d5e824a8e4b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793429"
---
# <a name="split-merge-security-configuration"></a>Beveiligings configuratie splitsen en samen voegen
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Als u de service voor splitsen en samen voegen wilt gebruiken, moet u de beveiliging op de juiste manier configureren. De service maakt deel uit van de functie voor elastisch schalen van Azure SQL Database. Zie voor meer informatie de [zelf studie voor splitsen en samen voegen met Elastic Scale](elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Certificaten configureren

Certificaten worden op twee manieren geconfigureerd. 

1. [Het TLS/SSL-certificaat configureren](#to-configure-the-tlsssl-certificate)
2. [Client certificaten configureren](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Certificaten verkrijgen

Certificaten kunnen worden verkregen van open bare certificerings instanties (Ca's) of van de [Windows-certificaat service](/windows/win32/seccrypto/certificate-services). Dit zijn de voorkeurs methoden voor het verkrijgen van certificaten.

Als deze opties niet beschikbaar zijn, kunt u **zelfondertekende certificaten** genereren.

## <a name="tools-to-generate-certificates"></a>Hulpprogram ma's voor het genereren van certificaten

* [makecert.exe](/previous-versions/dotnet/netframework-4.0/bfsktky3(v=vs.100))
* [pvk2pfx.exe](/windows-hardware/drivers/devtest/pvk2pfx)

### <a name="to-run-the-tools"></a>De hulpprogram ma's uitvoeren

* Zie [Visual Studio-opdracht prompt](/dotnet/framework/tools/developer-command-prompt-for-vs) van een opdracht prompt voor ontwikkel aars voor visuele Studios 
  
    Als deze is geïnstalleerd, gaat u naar:
  
    ```console
    %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
    ```

* Ontvang de WDK van [Windows 8,1: down load kits en hulpprogram ma's](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-tlsssl-certificate"></a>Het TLS/SSL-certificaat configureren

Er is een TLS/SSL-certificaat vereist om de communicatie te versleutelen en de server te verifiëren. Kies het meest toepas bare van de drie onderstaande scenario's en voer alle stappen uit:

### <a name="create-a-new-self-signed-certificate"></a>Een nieuw zelfondertekend certificaat maken

1. [Een Self-Signed certificaat maken](#create-a-self-signed-certificate)
2. [PFX-bestand voor Self-Signed TLS/SSL-certificaat maken](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [TLS/SSL-certificaat uploaden naar de Cloud service](#upload-tlsssl-certificate-to-cloud-service)
4. [TLS/SSL-certificaat in service configuratie bestand bijwerken](#update-tlsssl-certificate-in-service-configuration-file)
5. [TLS/SSL-certificerings instantie importeren](#import-tlsssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Een bestaand certificaat uit het certificaat archief gebruiken
1. [Het TLS/SSL-certificaat exporteren uit het certificaat archief](#export-tlsssl-certificate-from-certificate-store)
2. [TLS/SSL-certificaat uploaden naar de Cloud service](#upload-tlsssl-certificate-to-cloud-service)
3. [TLS/SSL-certificaat in service configuratie bestand bijwerken](#update-tlsssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Een bestaand certificaat in een PFX-bestand gebruiken
1. [TLS/SSL-certificaat uploaden naar de Cloud service](#upload-tlsssl-certificate-to-cloud-service)
2. [TLS/SSL-certificaat in service configuratie bestand bijwerken](#update-tlsssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Client certificaten configureren
Client certificaten zijn vereist om aanvragen voor de service te verifiëren. Kies het meest toepas bare van de drie onderstaande scenario's en voer alle stappen uit:

### <a name="turn-off-client-certificates"></a>Client certificaten uitschakelen
1. [Verificatie van client Certificate-Based uitschakelen](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Nieuwe zelfondertekende client certificaten uitgeven
1. [Een Self-Signed certificerings instantie maken](#create-a-self-signed-certification-authority)
2. [CA-certificaat uploaden naar de Cloud service](#upload-ca-certificate-to-cloud-service)
3. [CA-certificaat in service configuratie bestand bijwerken](#update-ca-certificate-in-service-configuration-file)
4. [Client certificaten uitgeven](#issue-client-certificates)
5. [PFX-bestanden maken voor client certificaten](#create-pfx-files-for-client-certificates)
6. [Client certificaat importeren](#import-client-certificate)
7. [Vinger afdrukken van client certificaten kopiëren](#copy-client-certificate-thumbprints)
8. [Toegestane clients configureren in het service configuratie bestand](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Bestaande client certificaten gebruiken
1. [Open bare sleutel van CA zoeken](#find-ca-public-key)
2. [CA-certificaat uploaden naar de Cloud service](#upload-ca-certificate-to-cloud-service)
3. [CA-certificaat in service configuratie bestand bijwerken](#update-ca-certificate-in-service-configuration-file)
4. [Vinger afdrukken van client certificaten kopiëren](#copy-client-certificate-thumbprints)
5. [Toegestane clients configureren in het service configuratie bestand](#configure-allowed-clients-in-the-service-configuration-file)
6. [Intrekkings controle van client certificaat configureren](#configure-client-certificate-revocation-check)

## <a name="allowed-ip-addresses"></a>Toegestane IP-adressen
Toegang tot de service-eind punten kan worden beperkt tot specifieke bereiken van IP-adressen.

## <a name="to-configure-encryption-for-the-store"></a>Versleuteling voor de Store configureren
Een certificaat is vereist voor het versleutelen van de referenties die zijn opgeslagen in het meta gegevens archief. Kies het meest toepas bare van de drie onderstaande scenario's en voer alle stappen uit:

### <a name="use-a-new-self-signed-certificate"></a>Een nieuw zelfondertekend certificaat gebruiken
1. [Een Self-Signed certificaat maken](#create-a-self-signed-certificate)
2. [PFX-bestand voor Self-Signed versleutelings certificaat maken](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Versleutelings certificaat uploaden naar de Cloud service](#upload-encryption-certificate-to-cloud-service)
4. [Versleutelings certificaat in service configuratie bestand bijwerken](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Een bestaand certificaat uit het certificaat archief gebruiken
1. [Versleutelings certificaat exporteren uit certificaat archief](#export-encryption-certificate-from-certificate-store)
2. [Versleutelings certificaat uploaden naar de Cloud service](#upload-encryption-certificate-to-cloud-service)
3. [Versleutelings certificaat in service configuratie bestand bijwerken](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Een bestaand certificaat gebruiken in een PFX-bestand
1. [Versleutelings certificaat uploaden naar de Cloud service](#upload-encryption-certificate-to-cloud-service)
2. [Versleutelings certificaat in service configuratie bestand bijwerken](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>De standaard configuratie
Met de standaard configuratie wordt alle toegang tot het HTTP-eind punt geweigerd. Dit is de aanbevolen instelling, omdat de aanvragen voor deze eind punten gevoelige informatie kunnen bevatten, zoals database referenties.
Met de standaard configuratie is alle toegang tot het HTTPS-eind punt toegestaan. Deze instelling kan verder worden beperkt.

### <a name="changing-the-configuration"></a>De configuratie wijzigen
De groep regels voor toegangs beheer die van toepassing zijn op en eind punt zijn geconfigureerd in de **\<EndpointAcls>** sectie in het **Service configuratie bestand**.

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

De regels in een toegangs beheer groep worden geconfigureerd in een \<AccessControl name=""> sectie van het service configuratie bestand. 

De indeling wordt uitgelegd in de documentatie over netwerk Access Control lijsten.
Als u bijvoorbeeld alleen Ip's wilt toestaan in het bereik 100.100.0.0 om toegang te krijgen tot het HTTPS-eind punt, ziet de regels er als volgt uit:

```xml
<AccessControl name="Retricted">
    <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
    <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
</AccessControl>
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />
</EndpointAcls>
```

## <a name="denial-of-service-prevention"></a>Denial of service-preventie
Er zijn twee verschillende mechanismen die worden ondersteund om denial of service-aanvallen te detecteren en voor komen:

* Aantal gelijktijdige aanvragen per externe host beperken (standaard uitgeschakeld)
* De toegangs frequentie per externe host beperken (standaard ingeschakeld)

Deze zijn gebaseerd op de functies die verder worden beschreven in dynamische IP-beveiliging in IIS. Houd bij het wijzigen van deze configuratie rekening met de volgende factoren:

* Het gedrag van proxy's en apparaten voor het omzetten van netwerk adressen via de gegevens van de externe host
* Elke aanvraag voor een resource in de webfunctie wordt overwogen (bijvoorbeeld het laden van scripts, afbeeldingen enzovoort)

## <a name="restricting-number-of-concurrent-accesses"></a>Aantal gelijktijdige toegangs rechten beperken
De instellingen die dit gedrag configureren zijn:

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

Wijzig DynamicIpRestrictionDenyByConcurrentRequests in True om deze beveiliging in te scha kelen.

## <a name="restricting-rate-of-access"></a>De toegangs frequentie beperken
De instellingen die dit gedrag configureren zijn:

```xml
<Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
<Setting name="DynamicIpRestrictionMaxRequests" value="100" />
<Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />
```

## <a name="configuring-the-response-to-a-denied-request"></a>De reactie op een geweigerde aanvraag configureren
Met de volgende instelling wordt het antwoord op een geweigerde aanvraag geconfigureerd:

```xml
<Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
```

Raadpleeg de documentatie voor dynamische IP-beveiliging in IIS voor andere ondersteunde waarden.

## <a name="operations-for-configuring-service-certificates"></a>Bewerkingen voor het configureren van service certificaten
Dit onderwerp is alleen ter informatie. Volg de configuratie stappen die worden beschreven in:

* Het TLS/SSL-certificaat configureren
* Client certificaten configureren

## <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken
Aanvaller

```console
makecert ^
  -n "CN=myservice.cloudapp.net" ^
  -e MM/DD/YYYY ^
  -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
  -a sha256 -len 2048 ^
  -sv MySSL.pvk MySSL.cer
```

Aanpassen:

* -n met de service-URL. Joker tekens ("CN = *. cloudapp. net") en alternatieve namen ("CN = myservice1. cloudapp. net, CN = myservice2. cloudapp") worden ondersteund.
* -e met de verval datum van het certificaat Maak een sterk wacht woord en geef deze op wanneer u hierom wordt gevraagd.

## <a name="create-pfx-file-for-self-signed-tlsssl-certificate"></a>PFX-bestand maken voor zelfondertekend TLS/SSL-certificaat
Aanvaller

```console
pvk2pfx -pvk MySSL.pvk -spc MySSL.cer
```

Voer het wacht woord in en exporteer het certificaat met de volgende opties:

* Ja, de persoonlijke sleutel exporteren
* Alle uitgebreide eigenschappen exporteren

## <a name="export-tlsssl-certificate-from-certificate-store"></a>Het TLS/SSL-certificaat exporteren uit het certificaat archief
* Certificaat zoeken
* Klik op acties-> alle taken-> exporteren...
* Certificaat exporteren naar een. PFX-bestand met de volgende opties:
  * Ja, de persoonlijke sleutel exporteren
  * Indien mogelijk alle certificaten in het certificeringspad toevoegen * alle uitgebreide eigenschappen exporteren

## <a name="upload-tlsssl-certificate-to-cloud-service"></a>TLS/SSL-certificaat uploaden naar de Cloud service
Upload het certificaat met de bestaande of gegenereerde. PFX-bestand met het sleutel paar TLS:

* Voer het wacht woord in voor het beveiligen van de persoonlijke sleutel gegevens

## <a name="update-tlsssl-certificate-in-service-configuration-file"></a>TLS/SSL-certificaat in service configuratie bestand bijwerken
Werk de vingerafdruk waarde van de volgende instelling in het service configuratie bestand bij met de vinger afdruk van het certificaat dat is geüpload naar de Cloud service:

```console
<Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="import-tlsssl-certification-authority"></a>TLS/SSL-certificerings instantie importeren
Voer de volgende stappen uit op alle accounts/computers die met de service zullen communiceren:

* Dubbel klik op de. CER-bestand in Windows Verkenner
* Klik in het dialoog venster certificaat op certificaat installeren...
* Certificaat importeren in het archief met vertrouwde basis certificerings instanties

## <a name="turn-off-client-certificate-based-authentication"></a>Verificatie op basis van client certificaten uitschakelen
Alleen op certificaten gebaseerde verificatie op basis van een client wordt ondersteund. Als u deze functie uitschakelt, is toegang tot de service-eind punten mogelijk, tenzij andere mechanismen aanwezig zijn (bijvoorbeeld Microsoft Azure Virtual Network).

Wijzig deze instellingen in ONWAAR in het service configuratie bestand om de functie uit te scha kelen:

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

Kopieer vervolgens dezelfde vinger afdruk als het TLS/SSL-certificaat in de instelling CA-certificaat:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>Een zelf-ondertekende certificerings instantie maken
Voer de volgende stappen uit om een zelfondertekend certificaat te maken om te fungeren als een certificerings instantie:

```console
makecert ^
-n "CN=MyCA" ^
-e MM/DD/YYYY ^
 -r -cy authority -h 1 ^
 -a sha256 -len 2048 ^
  -sr localmachine -ss my ^
  MyCA.cer
```

Om het aan te passen

* -e met de verval datum van de certificering

## <a name="find-ca-public-key"></a>Open bare sleutel van CA zoeken
Alle client certificaten moeten zijn uitgegeven door een certificerings instantie die wordt vertrouwd door de service. Zoek de open bare sleutel naar de certificerings instantie die de client certificaten heeft uitgegeven die zullen worden gebruikt voor verificatie om deze te uploaden naar de Cloud service.

Als het bestand met de open bare sleutel niet beschikbaar is, exporteert u het uit het certificaat archief:

* Certificaat zoeken
  * Zoeken naar een client certificaat dat is uitgegeven door dezelfde certificerings instantie
* Dubbel klik op het certificaat.
* Selecteer het tabblad Certificeringspad in het dialoog venster certificaat.
* Dubbel klik op de vermelding van de certificerings instantie in het pad.
* Maak notities van de certificaat eigenschappen.
* Sluit het dialoog venster **certificaat** .
* Certificaat zoeken
  * Zoek naar de hierboven vermelde certificerings instantie.
* Klik op acties-> alle taken-> exporteren...
* Certificaat exporteren naar een. CER met de volgende opties:
  * **Nee, de persoonlijke sleutel niet exporteren**
  * Voeg indien mogelijk alle certificaten in het certificeringspad toe.
  * Alle uitgebreide eigenschappen exporteren.

## <a name="upload-ca-certificate-to-cloud-service"></a>CA-certificaat uploaden naar de Cloud service
Upload het certificaat met de bestaande of gegenereerde. CER-bestand met de open bare sleutel van de CA.

## <a name="update-ca-certificate-in-service-configuration-file"></a>CA-certificaat in service configuratie bestand bijwerken
Werk de vingerafdruk waarde van de volgende instelling in het service configuratie bestand bij met de vinger afdruk van het certificaat dat is geüpload naar de Cloud service:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

Werk de waarde van de volgende instelling bij met dezelfde vinger afdruk:

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>Client certificaten uitgeven
Elke persoon die gemachtigd is voor toegang tot de service, moet een client certificaat hebben uitgegeven voor hun exclusieve gebruik en moet een eigen sterk wacht woord kiezen om de persoonlijke sleutel te beveiligen. 

De volgende stappen moeten worden uitgevoerd op dezelfde computer waarop het zelfondertekende CA-certificaat is gegenereerd en opgeslagen:

```console
makecert ^
  -n "CN=My ID" ^
  -e MM/DD/YYYY ^
  -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
  -a sha256 -len 2048 ^
  -in "MyCA" -ir localmachine -is my ^
  -sv MyID.pvk MyID.cer
```

Aanpassen

* -n met een ID voor de client die wordt geverifieerd met dit certificaat
* -e met de verval datum van het certificaat
* MyID. PVK en MyID. cer met unieke bestands namen voor dit client certificaat

Met deze opdracht wordt gevraagd om een wacht woord dat moet worden gemaakt en vervolgens één keer worden gebruikt. Gebruik een sterk wacht woord.

## <a name="create-pfx-files-for-client-certificates"></a>PFX-bestanden maken voor client certificaten
Voer voor elk gegenereerd client certificaat het volgende uit:

```console
pvk2pfx -pvk MyID.pvk -spc MyID.cer
```

Aanpassen

```console
MyID.pvk and MyID.cer with the filename for the client certificate
```

Voer het wacht woord in en exporteer het certificaat met de volgende opties:

* Ja, de persoonlijke sleutel exporteren
* Alle uitgebreide eigenschappen exporteren
* De persoon aan wie dit certificaat wordt uitgegeven, moet het wacht woord voor exporteren kiezen

## <a name="import-client-certificate"></a>Client certificaat importeren
Elke persoon voor wie een client certificaat is uitgegeven, moet het sleutel paar importeren op de computers die ze zullen gebruiken om te communiceren met de service:

* Dubbel klik op de. PFX-bestand in Windows Verkenner
* Importeer het certificaat in het persoonlijke archief met ten minste deze optie:
  * Alle uitgebreide eigenschappen zijn ingeschakeld

## <a name="copy-client-certificate-thumbprints"></a>Vinger afdrukken van client certificaten kopiëren
Elke persoon voor wie een client certificaat is uitgegeven, moet deze stappen volgen om de vinger afdruk van het certificaat te verkrijgen, dat wordt toegevoegd aan het service configuratie bestand:

* certmgr.exe uitvoeren
* Selecteer het tabblad persoonlijk
* Dubbel klik op het client certificaat dat moet worden gebruikt voor verificatie
* In het dialoog venster certificaat dat wordt geopend, selecteert u het tabblad Details
* Zorg ervoor dat weer geven alle wordt weer gegeven
* Selecteer in de lijst het veld met de naam vinger afdruk
* De waarde van de vinger afdruk kopiëren
  * Niet-zicht bare Unicode-tekens vóór het eerste cijfer verwijderen
  * Alle spaties verwijderen

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Toegestane clients configureren in het service configuratie bestand
Werk de waarde van de volgende instelling in het service configuratie bestand bij met een door komma's gescheiden lijst met de vinger afdrukken van de client certificaten die toegang hebben tot de service:

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>Intrekkings controle van client certificaat configureren
De standaard instelling controleert niet bij de certificerings instantie voor de intrekkings status van het client certificaat. Als de certificerings instantie die de client certificaten heeft uitgegeven dergelijke controles ondersteunt, wijzigt u de volgende instelling met een van de waarden die zijn gedefinieerd in de X509RevocationMode-inventarisatie:

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>PFX-bestand maken voor zelfondertekende versleutelings certificaten
Voer voor een versleutelings certificaat het volgende uit:

```console
pvk2pfx -pvk MyID.pvk -spc MyID.cer
```

Aanpassen

```console
MyID.pvk and MyID.cer with the filename for the encryption certificate
```

Voer het wacht woord in en exporteer het certificaat met de volgende opties:

* Ja, de persoonlijke sleutel exporteren
* Alle uitgebreide eigenschappen exporteren
* U hebt het wacht woord nodig wanneer u het certificaat uploadt naar de Cloud service.

## <a name="export-encryption-certificate-from-certificate-store"></a>Versleutelings certificaat exporteren uit certificaat archief
* Certificaat zoeken
* Klik op acties-> alle taken-> exporteren...
* Certificaat exporteren naar een. PFX-bestand met de volgende opties: 
  * Ja, de persoonlijke sleutel exporteren
  * Indien mogelijk alle certificaten in het certificeringspad toevoegen 
* Alle uitgebreide eigenschappen exporteren

## <a name="upload-encryption-certificate-to-cloud-service"></a>Versleutelings certificaat uploaden naar de Cloud service
Upload het certificaat met de bestaande of gegenereerde. PFX-bestand met het versleutelings sleutel paar:

* Voer het wacht woord in voor het beveiligen van de persoonlijke sleutel gegevens

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Versleutelings certificaat in service configuratie bestand bijwerken
Werk de vingerafdruk waarde van de volgende instellingen in het service configuratie bestand bij met de vinger afdruk van het certificaat dat is geüpload naar de Cloud service:

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>Algemene certificaat bewerkingen
* Het TLS/SSL-certificaat configureren
* Client certificaten configureren

## <a name="find-certificate"></a>Certificaat zoeken
Volg deze stappen:

1. Voer mmc.exe uit.
2. Bestand-> module toevoegen/verwijderen...
3. Selecteer **Certificaten**.
4. Klik op **Add**.
5. Kies de locatie van het certificaat archief.
6. Klik op **Voltooien**.
7. Klik op **OK**.
8. Vouw **certificaten** uit.
9. Vouw het knoop punt certificaat archief uit.
10. Vouw het onderliggende knoop punt voor certificaten uit.
11. Selecteer een certificaat in de lijst.

## <a name="export-certificate"></a>Certificaat exporteren
In de **wizard Certificaat exporteren**:

1. Klik op **Volgende**.
2. Selecteer **Ja** en vervolgens **de persoonlijke sleutel exporteren**.
3. Klik op **Volgende**.
4. Selecteer de gewenste indeling voor het uitvoer bestand.
5. Controleer de gewenste opties.
6. Controleer het **wacht woord**.
7. Voer een sterk wacht woord in en bevestig dit.
8. Klik op **Volgende**.
9. Typ of blader door een bestands naam waar het certificaat moet worden opgeslagen (gebruik een. PFX-extensie).
10. Klik op **Volgende**.
11. Klik op **Voltooien**.
12. Klik op **OK**.

## <a name="import-certificate"></a>Certificaat importeren
In de wizard Certificaat importeren:

1. Selecteer de opslag locatie.
   
   * **Huidige gebruiker** selecteren als alleen de processen die worden uitgevoerd onder de huidige gebruiker toegang krijgen tot de service
   * **Lokale computer** selecteren als andere processen in deze computer toegang hebben tot de service
2. Klik op **Volgende**.
3. Als u importeert uit een bestand, bevestig het bestandspad.
4. Bij het importeren van een. PFX-bestand:
   1. Voer het wacht woord in voor het beveiligen van de persoonlijke sleutel
   2. Import opties selecteren
5. Selecteer certificaten in het onderstaande archief opslaan
6. Klik op **Bladeren**.
7. Selecteer de gewenste winkel.
8. Klik op **Voltooien**.
   
   * Als het archief met vertrouwde basis certificerings instanties is gekozen, klikt u op **Ja**.
9. Klik op **OK** in alle dialoog vensters.

## <a name="upload-certificate"></a>Certificaat uploaden
In de [Azure Portal](https://portal.azure.com/)

1. Selecteer **Cloud Services**.
2. Selecteer de Cloud service.
3. Klik in het bovenste menu op **certificaten**.
4. Klik in de onderste balk op **uploaden**.
5. Selecteer het certificaat bestand.
6. Als dat het geval is. PFX-bestand, voert u het wacht woord voor de persoonlijke sleutel in.
7. Als u klaar bent, kopieert u de vinger afdruk van het certificaat uit de nieuwe vermelding in de lijst.

## <a name="other-security-considerations"></a>Andere beveiligings overwegingen
Met de TLS-instellingen die in dit document worden beschreven, wordt de communicatie tussen de service en de clients versleuteld wanneer het HTTPS-eind punt wordt gebruikt. Dit is belang rijk omdat referenties voor database toegang en mogelijk andere gevoelige informatie zijn opgenomen in de communicatie. Houd er echter rekening mee dat de service de interne status, inclusief referenties, aanhoudt in de interne tabellen in de data base in Azure SQL Database die u hebt ingesteld voor de opslag van meta gegevens in uw Microsoft Azure-abonnement. Deze data base is gedefinieerd als onderdeel van de volgende instelling in het configuratie bestand van de service (. CSCFG-bestand): 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

De referenties die zijn opgeslagen in deze data base, worden versleuteld. Als best practice, moet u er echter voor zorgen dat de web-en werk rollen van uw service-implementaties up-to-date blijven en beveiligd zijn, omdat beide toegang hebben tot de meta gegevens database en het certificaat dat wordt gebruikt voor het versleutelen en ontsleutelen van opgeslagen referenties. 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]