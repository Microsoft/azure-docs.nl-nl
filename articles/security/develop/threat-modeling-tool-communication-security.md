---
title: Communicatie beveiliging voor de Microsoft Threat Modeling Tool
titleSuffix: Azure
description: Meer informatie over de risico beperking voor beveiligings Risico's voor communicatie die worden weer gegeven in de Threat Modeling Tool. Zie informatie over risico beperking en voor beelden van code weer geven.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-csharp
ms.openlocfilehash: d9a4eabf37101622ac69ae05f3bec232fb8d2fe6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94517526"
---
# <a name="security-frame-communication-security--mitigations"></a>Beveiligings frame: communicatie beveiliging | Oplossingen 
| Product/service | Artikel |
| --------------- | ------- |
| **Azure Event Hub** | <ul><li>[Communicatie met Event hub beveiligen met behulp van SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Controleer de bevoegdheden van het service account en controleer of de aangepaste services of ASP.NET-pagina's de beveiliging van CRM respecteren](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Data Management gateway gebruiken tijdens het verbinden van on-premises SQL Server naar Azure Data Factory](#sqlserver-factory)</li></ul> |
| **Identiteits server** | <ul><li>[Zorg ervoor dat al het verkeer naar de identiteits server de HTTPS-verbinding heeft](#identity-https)</li></ul> |
| **Webtoepassing** | <ul><li>[X. 509-certificaten verifiëren die worden gebruikt voor het verifiëren van SSL-, TLS-en DTLS-verbindingen](#x509-ssltls)</li><li>[TLS/SSL-certificaat configureren voor een aangepast domein in Azure App Service](#ssl-appservice)</li><li>[Alle verkeer naar Azure App Service via HTTPS-verbinding afdwingen](#appservice-https)</li><li>[HTTP-strikte transport beveiliging (HSTS) inschakelen](#http-hsts)</li></ul> |
| **Database** | <ul><li>[Zorg ervoor dat SQL Server-verbindings versleuteling en certificaat validatie worden](#sqlserver-validation)</li><li>[Versleutelde communicatie met SQL server afdwingen](#encrypted-sqlserver)</li></ul> |
| **Azure Storage** | <ul><li>[Zorg ervoor dat de communicatie met Azure Storage over HTTPS is](#comm-storage)</li><li>[MD5-hash valideren na het downloaden van de BLOB als HTTPS niet kan worden ingeschakeld](#md5-https)</li><li>[SMB 3,0-compatibele client gebruiken om te zorgen voor in-transit gegevens versleuteling naar Azure-bestands shares](#smb-shares)</li></ul> |
| **Mobiele client** | <ul><li>[Certificaat vastmaken implementeren](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[HTTPS-beveiligd transport kanaal inschakelen](#https-transport)</li><li>[WCF: beveiligings niveau voor bericht beveiliging instellen op EncryptAndSign](#message-protection)</li><li>[WCF: gebruik een account met minimale bevoegdheden om uw WCF-service uit te voeren](#least-account-wcf)</li></ul> |
| **Web-API** | <ul><li>[Alle verkeer naar web-Api's via HTTPS-verbinding afdwingen](#webapi-https)</li></ul> |
| **Azure Cache voor Redis** | <ul><li>[Zorg ervoor dat de communicatie met Azure cache voor redis meer dan TLS is](#redis-ssl)</li></ul> |
| **IoT-veld Gateway** | <ul><li>[Communicatie tussen het apparaat en de veld Gateway beveiligen](#device-field)</li></ul> |
| **IoT-Cloud gateway** | <ul><li>[Communicatie tussen het apparaat en de Cloud gateway met behulp van SSL/TLS](#device-cloud)</li></ul> |

## <a name="secure-communication-to-event-hub-using-ssltls"></a><a id="comm-ssltls"></a>Communicatie met Event hub beveiligen met behulp van SSL/TLS

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Azure Event Hub | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Overzicht van Event Hubs verificatie en beveiligings model](../../event-hubs/authenticate-shared-access-signature.md) |
| **Stappen** | AMQP of HTTP-verbindingen met Event hub beveiligen met behulp van SSL/TLS |

## <a name="check-service-account-privileges-and-check-that-the-custom-services-or-aspnet-pages-respect-crms-security"></a><a id="priv-aspnet"></a>Controleer de bevoegdheden van het service account en controleer of de aangepaste services of ASP.NET-pagina's de beveiliging van CRM respecteren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Dynamics CRM | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | Controleer de bevoegdheden van het service account en controleer of de aangepaste services of ASP.NET-pagina's de beveiliging van CRM respecteren |

## <a name="use-data-management-gateway-while-connecting-on-premises-sql-server-to-azure-data-factory"></a><a id="sqlserver-factory"></a>Data Management gateway gebruiken tijdens het verbinden van on-premises SQL Server naar Azure Data Factory

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Azure Data Factory | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Gekoppelde service typen: Azure en on-premises |
| **Referenties**              |[Gegevens verplaatsen tussen on-premises en Azure Data Factory](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-gateway), [Data Management Gateway](../../data-factory/v1/data-factory-data-management-gateway.md) |
| **Stappen** | <p>Het hulp programma Data Management Gateway (DMG) is vereist om verbinding te maken met gegevens bronnen die zijn beveiligd achter Corpnet of een firewall.</p><ol><li>Door de computer te vergren delen, wordt het DMG-hulp programma geïsoleerd en wordt voor komen dat Program ma's beschadigd of op de gegevens bron machine worden genoteerd. Zoals. de meest recente updates moeten worden geïnstalleerd, beschikken over Mini maal vereiste poorten, het inrichten van beheerde accounts, het inschakelen van controle, het inschakelen van de schijf versleuteling, enzovoort.)</li><li>De gegevens gateway sleutel moet met regel matige tussen pozen worden geroteerd of wanneer het wacht woord van de DMG-service account wordt vernieuwd</li><li>Gegevens doorvoer via de koppelings service moet worden versleuteld</li></ol> |

## <a name="ensure-that-all-traffic-to-identity-server-is-over-https-connection"></a><a id="identity-https"></a>Zorg ervoor dat al het verkeer naar de identiteits server de HTTPS-verbinding heeft

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Identiteits server | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [IdentityServer3-sleutels, hand tekeningen en crypto grafie](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3-implementatie](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Stappen** | IdentityServer vereist standaard dat alle binnenkomende verbindingen worden ontvangen via HTTPS. Het is absoluut verplicht dat communicatie met IdentityServer alleen worden uitgevoerd via beveiligde trans porten. Er zijn bepaalde implementatie scenario's zoals TLS-offloading waarbij deze vereiste kan worden versoepeld. Zie de pagina voor de implementatie van de identiteits server in de referenties voor meer informatie. |

## <a name="verify-x509-certificates-used-to-authenticate-ssl-tls-and-dtls-connections"></a><a id="x509-ssltls"></a>X. 509-certificaten verifiëren die worden gebruikt voor het verifiëren van SSL-, TLS-en DTLS-verbindingen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | <p>Toepassingen die gebruikmaken van SSL, TLS of DTLS, moeten de X. 509-certificaten van de entiteiten waarmee ze verbinding maken, volledig controleren. Dit omvat de verificatie van de certificaten voor:</p><ul><li>Domeinnaam</li><li>De geldigheids datums (zowel de begin-als de verval datum)</li><li>Intrekkings status</li><li>Gebruik (bijvoorbeeld Server verificatie voor servers, client verificatie voor clients)</li><li>Vertrouwens keten. Certificaten moeten worden gekoppeld aan een basis certificerings instantie (CA) die wordt vertrouwd door het platform of expliciet is geconfigureerd door de beheerder</li><li>De sleutel lengte van de open bare sleutel van het certificaat moet >2048 bits zijn</li><li>Hash-algoritme moet SHA256 en hoger zijn |

## <a name="configure-tlsssl-certificate-for-custom-domain-in-azure-app-service"></a><a id="ssl-appservice"></a>TLS/SSL-certificaat configureren voor een aangepast domein in Azure App Service

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | EnvironmentType-Azure |
| **Referenties**              | [HTTPS inschakelen voor een app in Azure App Service](../../app-service/configure-ssl-bindings.md) |
| **Stappen** | Standaard schakelt Azure HTTPS al in voor elke app met een Joker certificaat voor het domein *. azurewebsites.net. Net als alle Joker domeinen is het echter niet zo veilig als het gebruik van een aangepast domein met een eigen certificaat [verwijzen](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). Het is raadzaam om TLS in te scha kelen voor het aangepaste domein waartoe de geïmplementeerde app toegang moet hebben|

## <a name="force-all-traffic-to-azure-app-service-over-https-connection"></a><a id="appservice-https"></a>Alle verkeer naar Azure App Service via HTTPS-verbinding afdwingen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | EnvironmentType-Azure |
| **Referenties**              | [HTTPS afdwingen op Azure App Service](../../app-service/configure-ssl-bindings.md#enforce-https) |
| **Stappen** | <p>Hoewel Azure al HTTPS maakt voor Azure app Services met een Joker teken voor het domein *. azurewebsites.net, wordt HTTPS niet afgedwongen. Bezoekers hebben mogelijk nog steeds toegang tot de app via HTTP, waardoor de beveiliging van de app kan worden aangetast en daarom expliciet HTTPS moet worden afgedwongen. ASP.NET MVC-toepassingen moeten het [RequireHttps-filter](/dotnet/api/system.web.mvc.requirehttpsattribute) gebruiken dat ervoor zorgt dat een niet-beveiligde HTTP-aanvraag opnieuw wordt verzonden via https.</p><p>U kunt ook de module voor het herschrijven van URL'S, die is opgenomen in Azure App Service, gebruiken om HTTPS af te dwingen. Met de module voor het herschrijven van URL'S kunnen ontwikkel aars regels definiëren die worden toegepast op binnenkomende aanvragen voordat de aanvragen worden overgedragen aan uw toepassing. De regels voor het herschrijven van URL'S worden gedefinieerd in een web.config-bestand dat is opgeslagen in de hoofdmap van de toepassing</p>|

### <a name="example"></a>Voorbeeld
Het volgende voor beeld bevat een eenvoudige regel voor het herschrijven van URL'S waarmee alle binnenkomende verkeer wordt afgedwongen voor gebruik van HTTPS
```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```
Deze regel werkt door het retour neren van een HTTP-status code van 301 (permanente omleiding) wanneer de gebruiker een pagina aanvraagt via HTTP. De 301 leidt de aanvraag om naar dezelfde URL als de door de bezoeker aangevraagde, maar vervangt het HTTP-gedeelte van de aanvraag met HTTPS. Wordt bijvoorbeeld `HTTP://contoso.com` omgeleid naar `HTTPS://contoso.com` . 

## <a name="enable-http-strict-transport-security-hsts"></a><a id="http-hsts"></a>HTTP-strikte transport beveiliging (HSTS) inschakelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Cheat-blad OWASP HTTP strict-Trans Port beveiliging](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) |
| **Stappen** | <p>HTTP strict Trans Port Security (HSTS) is een opt-in-beveiligings verbetering die is opgegeven door een webtoepassing via het gebruik van een speciale antwoord header. Zodra een ondersteunde browser deze header ontvangt, kan de browser voor komen dat er communicatie via HTTP naar het opgegeven domein wordt verzonden en wordt in plaats daarvan alle communicatie verzonden via HTTPS. Er wordt ook voor komen dat met HTTPS wordt geklikt op vragen over browsers.</p><p>Voor het implementeren van HSTS moet de volgende reactie header worden geconfigureerd voor een website globaal, hetzij in code of in configuratie. Strict-Trans Port-Security: Max-Age = 300; includeSubDomains HSTS behandelt de volgende bedreigingen:</p><ul><li>Gebruikers bladwijzer of hand matig typen `https://example.com` en is onderworpen aan een man-in-the-Middle-aanvaller: HSTS stuurt automatisch HTTP-aanvragen om naar https voor het doel domein</li><li>Webtoepassingen die louter HTTPS bedoeld zijn, bevatten per ongeluk HTTP-koppelingen of verzendt inhoud via HTTP: HSTS stuurt automatisch HTTP-aanvragen om naar HTTPS voor het doel domein</li><li>Een man-in-the-Middle-aanvaller probeert verkeer te onderscheppen van een slacht offer-gebruiker met een ongeldig certificaat en wil de gebruiker het onjuiste certificaat accepteert: HSTS staat niet toe dat een gebruiker het ongeldige certificaat bericht overschrijft</li></ul>|

## <a name="ensure-sql-server-connection-encryption-and-certificate-validation"></a><a id="sqlserver-validation"></a>Zorg ervoor dat SQL Server-verbindings versleuteling en certificaat validatie worden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Database | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | SQL Azure  |
| **Kenmerken**              | SQL-versie-V12 |
| **Referenties**              | [Aanbevolen procedures voor het schrijven van beveiligde verbindings reeksen voor SQL Database](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Stappen** | <p>Alle communicatie tussen SQL Database en een client toepassing wordt altijd versleuteld met behulp van Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL). SQL Database biedt geen ondersteuning voor niet-versleutelde verbindingen. Om certificaten te valideren met toepassings code of hulpprogram ma's, vraagt u expliciet een versleutelde verbinding aan en vertrouwt u de server certificaten niet. Als uw toepassings code of hulpprogram ma's geen versleutelde verbinding aanvragen, worden er nog steeds versleutelde verbindingen ontvangen</p><p>Ze kunnen de server certificaten echter niet valideren en zijn dus vatbaar voor man in het midden. Certificaten valideren met ADO.NET toepassings code, set `Encrypt=True` en `TrustServerCertificate=False` in de data base Connection String. Als u certificaten wilt valideren via SQL Server Management Studio, opent u het dialoog venster verbinding maken met server. Klik op verbinding versleutelen op het tabblad verbindings eigenschappen</p>|

## <a name="force-encrypted-communication-to-sql-server"></a><a id="encrypted-sqlserver"></a>Versleutelde communicatie met SQL server afdwingen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Database | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | OnPrem |
| **Kenmerken**              | SQL-versie-MsSQL2016, SQL-versie-MsSQL2012, SQL-versie-MsSQL2014 |
| **Referenties**              | [Versleutelde verbindingen met database-engine inschakelen](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)  |
| **Stappen** | Het inschakelen van TLS-versleuteling verhoogt de beveiliging van gegevens die worden verzonden tussen netwerken tussen exemplaren van SQL Server en toepassingen. |

## <a name="ensure-that-communication-to-azure-storage-is-over-https"></a><a id="comm-storage"></a>Zorg ervoor dat de communicatie met Azure Storage over HTTPS is

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Azure Storage | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Versleuteling van Azure Storage Transport-Level: HTTPS gebruiken](../../storage/blobs/security-recommendations.md#networking) |
| **Stappen** | Gebruik altijd het HTTPS-protocol bij het aanroepen van de REST-Api's of het openen van objecten in opslag om de beveiliging van Azure Storage gegevens in transit te garanderen. Ook gedeelde toegangs handtekeningen, die kunnen worden gebruikt voor het delegeren van toegang tot Azure Storage-objecten, bevatten een optie om op te geven dat alleen het HTTPS-protocol kan worden gebruikt bij het gebruik van hand tekeningen voor gedeelde toegang, om ervoor te zorgen dat iedereen die koppelingen met SAS-tokens verzendt, het juiste protocol gebruikt.|

## <a name="validate-md5-hash-after-downloading-blob-if-https-cannot-be-enabled"></a><a id="md5-https"></a>MD5-hash valideren na het downloaden van de BLOB als HTTPS niet kan worden ingeschakeld

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Azure Storage | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Para-BLOB |
| **Referenties**              | [Overzicht van Windows Azure Blob MD5](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Stappen** | <p>Windows Azure Blob service biedt mechanismen voor het garanderen van gegevens integriteit op de toepassings-en transport lagen. Als u om een of andere reden HTTP in plaats van HTTPS moet gebruiken en u met blok-blobs werkt, kunt u de MD5-controle gebruiken om de integriteit te controleren van de blobs die worden overgedragen</p><p>Dit helpt bij de beveiliging van netwerk/Trans Port-laag fouten, maar niet noodzakelijkerwijs met tussenliggende aanvallen. Als u HTTPS kunt gebruiken, dat beveiliging op transport niveau biedt, is het gebruik van MD5-controle overbodig en onnodig.</p>|

## <a name="use-smb-30-compatible-client-to-ensure-in-transit-data-encryption-to-azure-file-shares"></a><a id="smb-shares"></a>SMB 3,0-compatibele client gebruiken om te zorgen voor in-transit gegevens versleuteling naar Azure-bestands shares

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Mobiele client | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Para-bestand |
| **Referenties**              | [Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [Azure File Storage SMB-ondersteuning voor Windows-clients](../../storage/files/storage-dotnet-how-to-use-files.md#understanding-the-net-apis) |
| **Stappen** | Azure File Storage ondersteunt HTTPS bij het gebruik van de REST API, maar wordt vaak gebruikt als een SMB-bestands share die is gekoppeld aan een virtuele machine. SMB 2,1 biedt geen ondersteuning voor versleuteling. verbindingen zijn dus alleen toegestaan binnen dezelfde regio in Azure. SMB 3,0 ondersteunt echter versleuteling en kan worden gebruikt met Windows Server 2012 R2, Windows 8, Windows 8,1 en Windows 10, zodat u toegang hebt tot meerdere regio's en zelfs toegang op het bureau blad. |

## <a name="implement-certificate-pinning"></a><a id="cert-pinning"></a>Certificaat vastmaken implementeren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Azure Storage | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen, Windows Phone |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Certificaat en open bare sleutel vastmaken](https://owasp.org/www-community/controls/Certificate_and_Public_Key_Pinning) |
| **Stappen** | <p>Certificaat vastmaken beschermt tegen man-in-the-middle-aanvallen (MITM). Vastmaken is het proces van het koppelen van een host met het verwachte x509-certificaat of de open bare sleutel. Zodra een certificaat of open bare sleutel bekend of zichtbaar is voor een host, wordt het certificaat of de open bare sleutel gekoppeld of vastgemaakt aan de host. </p><p>Wanneer een kwaadwillende persoon een TLS MITM-aanval probeert uit te voeren, wijkt tijdens de TLS-Handshake de sleutel van de server van de aanvaller af van de vastgemaakte sleutel van het certificaat en wordt de aanvraag verwijderd, waardoor MITM certificaat vastmaken niet kan worden bereikt door de gemachtigde van ServicePointManager te implementeren `ServerCertificateValidationCallback` .</p>|

### <a name="example"></a>Voorbeeld
```csharp
using System;
using System.Net;
using System.Net.Security;
using System.Security.Cryptography;

namespace CertificatePinningExample
{
    class CertificatePinningExample
    {
        /* Note: In this example, we're hardcoding a the certificate's public key and algorithm for 
           demonstration purposes. In a real-world application, this should be stored in a secure
           configuration area that can be updated as needed. */

        private static readonly string PINNED_ALGORITHM = "RSA";

        private static readonly string PINNED_PUBLIC_KEY = "3082010A0282010100B0E75B7CBE56D31658EF79B3A1" +
            "294D506A88DFCDD603F6EF15E7F5BCBDF32291EC50B2B82BA158E905FE6A83EE044A48258B07FAC3D6356AF09B2" +
            "3EDAB15D00507B70DB08DB9A20C7D1201417B3071A346D663A241061C151B6EC5B5B4ECCCDCDBEA24F051962809" +
            "FEC499BF2D093C06E3BDA7D0BB83CDC1C2C6660B8ECB2EA30A685ADE2DC83C88314010FFC7F4F0F895EDDBE5C02" +
            "ABF78E50B708E0A0EB984A9AA536BCE61A0C31DB95425C6FEE5A564B158EE7C4F0693C439AE010EF83CA8155750" +
            "09B17537C29F86071E5DD8CA50EBD8A409494F479B07574D83EDCE6F68A8F7D40447471D05BC3F5EAD7862FA748" +
            "EA3C92A60A128344B1CEF7A0B0D94E50203010001";


        public static void Main(string[] args)
        {
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("https://azure.microsoft.com");
            request.ServerCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) =>
            {
                if (certificate == null || sslPolicyErrors != SslPolicyErrors.None)
                {
                    // Error getting certificate or the certificate failed basic validation
                    return false;
                }

                var targetKeyAlgorithm = new Oid(certificate.GetKeyAlgorithm()).FriendlyName;
                var targetPublicKey = certificate.GetPublicKeyString();
                
                if (targetKeyAlgorithm == PINNED_ALGORITHM &&
                    targetPublicKey == PINNED_PUBLIC_KEY)
                {
                    // Success, the certificate matches the pinned value.
                    return true;
                }
                // Reject, either the key or the algorithm does not match the expected value.
                return false;
            };

            try
            {
                var response = (HttpWebResponse)request.GetResponse();
                Console.WriteLine($"Success, HTTP status code: {response.StatusCode}");
            }
            catch(Exception ex)
            {
                Console.WriteLine($"Failure, {ex.Message}");
            }
            Console.WriteLine("Press any key to end.");
            Console.ReadKey();
        }
    }
}
```

## <a name="enable-https---secure-transport-channel"></a><a id="https-transport"></a>HTTPS-beveiligd transport kanaal inschakelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | WCF | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | NET Framework 3 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Fortify Konink rijk](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Stappen** | De configuratie van de toepassing moet ervoor zorgen dat HTTPS wordt gebruikt voor alle toegang tot gevoelige informatie.<ul><li>**Uitleg:** Als een toepassing gevoelige informatie verwerkt en geen versleuteling op bericht niveau gebruikt, mag deze alleen communiceren via een versleuteld transport kanaal.</li><li>**Aanbevelingen:** Zorg ervoor dat HTTP-Trans Port is uitgeschakeld en schakel in plaats daarvan HTTPS-Trans Port in. Vervang bijvoorbeeld de with- `<httpTransport/>` `<httpsTransport/>` tag. Vertrouw niet op een netwerk configuratie (firewall) om te garanderen dat de toepassing alleen toegankelijk is via een beveiligd kanaal. Vanuit een Philosophical-weergave punt moet de toepassing niet afhankelijk zijn van het netwerk voor de beveiliging.</li></ul><p>Vanuit een praktisch oogpunt van de gebruikers die verantwoordelijk zijn voor het beveiligen van het netwerk, worden de beveiligings vereisten van de toepassing niet altijd gevolgd tijdens het ontwikkelen.</p>|

## <a name="wcf-set-message-security-protection-level-to-encryptandsign"></a><a id="message-protection"></a>WCF: beveiligings niveau voor bericht beveiliging instellen op EncryptAndSign

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | WCF | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | .NET Framework 3 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [MSDN](/previous-versions/msp-n-p/ff650862(v=pandp.10)) |
| **Stappen** | <ul><li>**Uitleg:** Wanneer het beveiligings niveau is ingesteld op ' geen ', wordt de beveiliging van berichten uitgeschakeld. Vertrouwelijkheid en integriteit wordt bereikt met het juiste niveau van de instelling.</li><li>**VEREISTEN**<ul><li>Wanneer `Mode=None` -schakelt bericht beveiliging uit</li><li>Als `Mode=Sign` -ondertekenen, maar het bericht niet versleutelen; moet worden gebruikt als de gegevens integriteit belang rijk is</li><li>`Mode=EncryptAndSign`het bericht wanneer-ondertekenen en versleutelen</li></ul></li></ul><p>Overweeg versleuteling uit te scha kelen en uw bericht alleen te ondertekenen wanneer u alleen de integriteit van de gegevens moet valideren zonder dat u zich zorgen hoeft te maken. Dit kan handig zijn voor bewerkingen of service contracten waarin u de oorspronkelijke afzender moet valideren, maar er geen gevoelige gegevens worden verzonden. Zorg er bij het verminderen van het beveiligings niveau voor dat het bericht geen persoonlijke gegevens bevat.</p>|

### <a name="example"></a>Voorbeeld
Het configureren van de service en de bewerking om het bericht alleen te ondertekenen wordt weer gegeven in de volgende voor beelden. Voor beeld van service contract van `ProtectionLevel.Sign` : Hier volgt een voor beeld van het gebruik van ProtectionLevel. Sign op het service contract niveau: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Voorbeeld
Voor beeld van een bewerkings contract van `ProtectionLevel.Sign` (voor gedetailleerde controle): Hier volgt een voor beeld van het gebruik `ProtectionLevel.Sign` op het niveau van de OperationContract:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a name="wcf-use-a-least-privileged-account-to-run-your-wcf-service"></a><a id="least-account-wcf"></a>WCF: gebruik een account met minimale bevoegdheden om uw WCF-service uit te voeren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | WCF | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | .NET Framework 3 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [MSDN](/previous-versions/msp-n-p/ff648826(v=pandp.10)) |
| **Stappen** | <ul><li>**Uitleg:** Voer geen WCF-services uit onder Administrator of account met hoge bevoegdheden. Als er sprake is van een inbreuk op de services, resulteert dit in grote impact.</li><li>**Aanbevelingen:** Gebruik een account met minimale bevoegdheden om uw WCF-service te hosten, omdat deze de kwets baarheid van uw toepassing vermindert en de mogelijke schade kan verminderen als u wordt aangevallen. Als het service account aanvullende toegangs rechten vereist voor infrastructuur resources zoals MSMQ, het gebeurtenis logboek, prestatie meter items en het bestands systeem, moeten de juiste machtigingen worden verleend aan deze resources zodat de WCF-service kan worden uitgevoerd.</li></ul><p>Als uw service toegang moet hebben tot specifieke resources namens de oorspronkelijke oproepende functie, gebruikt u imitatie en overdracht om de identiteit van de beller te stroom te geven voor een stroomafwaartse autorisatie controle. Gebruik in een ontwikkelings scenario het lokale netwerk service account, een speciaal ingebouwd account met beperkte bevoegdheden. Maak in een productie scenario een aangepast domein service account met minimale privileges.</p>|

## <a name="force-all-traffic-to-web-apis-over-https-connection"></a><a id="webapi-https"></a>Alle verkeer naar web-Api's via HTTPS-verbinding afdwingen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Web-API | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | MVC5, MVC6 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [SSL afdwingen in een web-API-controller](https://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Stappen** | Als een toepassing zowel een HTTPS-als een HTTP-binding heeft, kunnen clients nog steeds HTTP gebruiken voor toegang tot de site. U kunt dit voor komen door een actie filter te gebruiken om ervoor te zorgen dat aanvragen voor beveiligde Api's altijd over HTTPS zijn.|

### <a name="example"></a>Voorbeeld 
De volgende code toont een web API-verificatie filter dat controleert op TLS: 
```csharp
public class RequireHttpsAttribute : AuthorizationFilterAttribute
{
    public override void OnAuthorization(HttpActionContext actionContext)
    {
        if (actionContext.Request.RequestUri.Scheme != Uri.UriSchemeHttps)
        {
            actionContext.Response = new HttpResponseMessage(System.Net.HttpStatusCode.Forbidden)
            {
                ReasonPhrase = "HTTPS Required"
            };
        }
        else
        {
            base.OnAuthorization(actionContext);
        }
    }
}
```
Voeg dit filter toe aan Web API-acties waarvoor TLS is vereist: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a name="ensure-that-communication-to-azure-cache-for-redis-is-over-tls"></a><a id="redis-ssl"></a>Zorg ervoor dat de communicatie met Azure cache voor redis meer dan TLS is

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Azure Cache voor Redis | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Ondersteuning voor Azure redis TLS](../../azure-cache-for-redis/cache-faq.md) |
| **Stappen** | Redis-server biedt geen ondersteuning voor TLS vanuit het vak, maar Azure cache voor redis wel. Als u verbinding maakt met Azure cache voor redis en uw client TLS ondersteunt, zoals stack Exchange. redis, moet u TLS gebruiken. Standaard is niet-TLS-poort uitgeschakeld voor nieuwe Azure-cache voor redis-exemplaren. Zorg ervoor dat de beveiligde standaard waarden niet worden gewijzigd, tenzij er sprake is van een afhankelijkheid van TLS-ondersteuning voor redis-clients. |

Houd er rekening mee dat redis is ontworpen voor toegang door vertrouwde clients binnen vertrouwde omgevingen. Dit betekent dat het niet goed is om het redis-exemplaar rechtstreeks beschikbaar te maken op internet of in het algemeen naar een omgeving waarin niet-vertrouwde clients rechtstreeks toegang hebben tot de TCP-poort of UNIX-socket van redis. 

## <a name="secure-device-to-field-gateway-communication"></a><a id="device-field"></a>Communicatie tussen het apparaat en de veld Gateway beveiligen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | IoT-veld Gateway | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | Voor op IP gebaseerde apparaten kan het communicatie protocol doorgaans worden ingekapseld in een SSL/TLS-kanaal om gegevens onderweg te beschermen. Andere protocollen die SSL/TLS-onderzoek niet ondersteunen als er sprake is van een beveiligde versie van het protocol dat een beveiliging biedt op Trans Port of de laag van berichten. |

## <a name="secure-device-to-cloud-gateway-communication-using-ssltls"></a><a id="device-cloud"></a>Communicatie tussen het apparaat en de Cloud gateway met behulp van SSL/TLS

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | IoT-Cloud gateway | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Kies uw communicatie protocol](../../iot-hub/iot-hub-devguide.md) |
| **Stappen** | HTTP-AMQP of MQTT-protocollen beveiligen met SSL/TLS. |