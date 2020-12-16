---
title: Invoer validatie-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Meer informatie over invoer validatie in de Threat Modeling Tool. Zie informatie over risico beperking en voor beelden van code weer geven.
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
ms.openlocfilehash: c816fbad05831c83c891c70849986b38cb7fdbeb
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589539"
---
# <a name="security-frame-input-validation--mitigations"></a>Beveiligings frame: invoer validatie | Oplossingen 
| Product/service | Artikel |
| --------------- | ------- |
| **Webtoepassing** | <ul><li>[XSLT-scripting uitschakelen voor alle trans formaties met niet-vertrouwde Style Sheets](#disable-xslt)</li><li>[Zorg ervoor dat elke pagina die door de gebruiker instel bare inhoud kan bevatten, wordt gebruikt voor automatische MIME-sniffing](#out-sniffing)</li><li>[Oplossing voor beveiliging of uitschakelen van XML-entiteit](#xml-resolution)</li><li>[Toepassingen die gebruikmaken van http.sys verificatie van URL-standaardisatie uitvoeren](#app-verification)</li><li>[Zorg ervoor dat de juiste besturings elementen worden uitgevoerd wanneer bestanden van gebruikers worden geaccepteerd](#controls-users)</li><li>[Zorg ervoor dat type veilige para meters worden gebruikt in webtoepassing voor gegevens toegang](#typesafe)</li><li>[Afzonderlijke model bindings klassen of bindings filter lijsten gebruiken om beveiligings problemen met een massale toewijzing voor komen](#binding-mvc)</li><li>[Niet-vertrouwde webuitvoer coderen vóór Rendering](#rendering)</li><li>[Invoer validatie en filteren uitvoeren op alle model eigenschappen van het teken reeks type](#typemodel)</li><li>[Opschonen moet worden toegepast op formulier velden waarin alle tekens worden geaccepteerd, bijvoorbeeld Rich Text Editor](#richtext)</li><li>[Wijs geen DOM-elementen toe aan sinks die geen ingebouwde code ring hebben](#inbuilt-encode)</li><li>[Alle omleidingen valideren binnen de toepassing worden gesloten of veilig uitgevoerd](#redirect-safe)</li><li>[Invoer validatie implementeren voor alle teken reeks type parameters die door controller methoden worden geaccepteerd](#string-method)</li><li>[Time-out voor de bovengrens instellen voor de verwerking van reguliere expressies om DoS door onjuiste reguliere expressies te voor komen](#dos-expression)</li><li>[Vermijd het gebruik van HTML. RAW in haar weer gaven](#html-razor)</li></ul> | 
| **Database** | <ul><li>[Geen dynamische query's gebruiken in opgeslagen procedures](#stored-proc)</li></ul> |
| **Web-API** | <ul><li>[Controleren of model validatie is uitgevoerd op Web API-methoden](#validation-api)</li><li>[Invoer validatie implementeren voor alle teken reeks type parameters die worden geaccepteerd door Web API-methoden](#string-api)</li><li>[Zorg ervoor dat type veilige para meters worden gebruikt in Web-API voor gegevens toegang](#typesafe-api)</li></ul> | 
| **Azure document DB** | <ul><li>[SQL-query's met para meters gebruiken voor Azure Cosmos DB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[WCF-invoer validatie via schema binding](#schema-binding)</li><li>[Validatie van WCF-invoer via parameter controles](#parameters)</li></ul> |

## <a name="disable-xslt-scripting-for-all-transforms-using-untrusted-style-sheets"></a><a id="disable-xslt"></a>XSLT-scripting uitschakelen voor alle trans formaties met niet-vertrouwde Style Sheets

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [XSLT-beveiliging](/previous-versions/windows/desktop/ms763800(v=vs.85)), [eigenschap XsltSettings. EnableScript](/dotnet/api/system.xml.xsl.xsltsettings.enablescript) |
| **Stappen** | XSLT ondersteunt scripting in Style Sheets met behulp van het- `<msxml:script>` element. Hierdoor kunnen aangepaste functies worden gebruikt in een XSLT-trans formatie. Het script wordt uitgevoerd in de context van het proces dat de trans formatie uitvoert. XSLT-script moet in een niet-vertrouwde omgeving worden uitgeschakeld om de uitvoering van niet-vertrouwde code te voor komen. *Als u .net gebruikt:* XSLT-scripts zijn standaard uitgeschakeld. u moet er echter voor zorgen dat deze niet expliciet is ingeschakeld via de `XsltSettings.EnableScript` eigenschap.|

### <a name="example"></a>Voorbeeld 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Voorbeeld
Als u MSXML 6,0 gebruikt, is XSLT-scripting standaard uitgeschakeld. u moet er echter voor zorgen dat deze niet expliciet is ingeschakeld via de eigenschap AllowXsltScript van het XML DOM-object. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Voorbeeld
Als u MSXML 5 of lager gebruikt, is XSLT-scripting standaard ingeschakeld en moet u het expliciet uitschakelen. Stel de eigenschap AllowXsltScript van het XML DOM-object in op false. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a name="ensure-that-each-page-that-could-contain-user-controllable-content-opts-out-of-automatic-mime-sniffing"></a><a id="out-sniffing"></a>Zorg ervoor dat elke pagina die door de gebruiker instel bare inhoud kan bevatten, wordt gebruikt voor automatische MIME-sniffing

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Beveiligings deel van IE8 V: uitgebreide beveiliging](/archive/blogs/ie/ie8-security-part-v-comprehensive-protection)  |
| **Stappen** | <p>Voor elke pagina die door de gebruiker te raden inhoud kan bevatten, moet u de HTTP-header gebruiken `X-Content-Type-Options:nosniff` . Om aan deze vereiste te voldoen, kunt u de pagina met de vereiste header per pagina instellen voor alleen de pagina's die door de gebruiker instel bare inhoud kunnen bevatten, of u kunt deze globaal instellen voor alle pagina's in de toepassing.</p><p>Elk type bestand dat vanaf een webserver wordt geleverd, heeft een bijbehorend [MIME-type](https://en.wikipedia.org/wiki/Mime_type) (ook wel een *inhouds type* genoemd) waarmee de aard van de inhoud (dat wil zeggen, afbeelding, tekst, toepassing enzovoort) wordt beschreven.</p><p>De X-content-type-Options-header is een HTTP-header waarmee ontwikkel aars kunnen opgeven dat hun inhoud niet MIME-sniffing mag zijn. Deze header is ontworpen om MIME-Sniffing-aanvallen te verhelpen. Ondersteuning voor deze header is toegevoegd in Internet Explorer 8 (IE8)</p><p>Alleen gebruikers van Internet Explorer 8 (IE8) kunnen profiteren van X-inhouds type-opties. Eerdere versies van Internet Explorer respecteren momenteel niet de X-inhouds type-opties-header</p><p>Internet Explorer 8 (en hoger) zijn de enige grote browsers voor het implementeren van een opt-out-functie voor MIME-sniffing. Als en wanneer andere belang rijke browsers (Firefox, Safari, Chrome) vergelijk bare functies implementeren, wordt deze aanbeveling bijgewerkt met de syntaxis voor die browsers en ook</p>|

### <a name="example"></a>Voorbeeld
Als u de vereiste header globaal wilt inschakelen voor alle pagina's in de toepassing, kunt u een van de volgende handelingen uitvoeren: 

* Voeg de header toe in het web.config-bestand als de toepassing wordt gehost door Internet Information Services (IIS) 7 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* Voeg de header toe via de BeginRequest van de globale toepassing \_ 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* Aangepaste HTTP-module implementeren 

``` 
public class XContentTypeOptionsModule : IHttpModule 
  {
    #region IHttpModule Members 
    public void Dispose() 
    { 

    } 
    public void Init(HttpApplication context)
    { 
      context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders); 
    } 
    #endregion 
    void context_PreSendRequestHeaders(object sender, EventArgs e) 
      { 
        HttpApplication application = sender as HttpApplication; 
        if (application == null) 
          return; 
        if (application.Response.Headers[""X-Content-Type-Options ""] != null) 
          return; 
        application.Response.Headers.Add(""X-Content-Type-Options "", ""nosniff""); 
      } 
  } 

``` 

* U kunt de vereiste header alleen voor specifieke pagina's inschakelen door deze toe te voegen aan afzonderlijke antwoorden: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a name="harden-or-disable-xml-entity-resolution"></a><a id="xml-resolution"></a>Oplossing voor beveiliging of uitschakelen van XML-entiteit

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Uitbrei ding van XML-entiteit](https://capec.mitre.org/data/definitions/197.html), [XML-denial of service-aanvallen en-](/archive/msdn-magazine/2009/november/xml-denial-of-service-attacks-and-defenses)beveiliging, [MSXML-beveiligings overzicht](/previous-versions/windows/desktop/ms754611(v=vs.85)), [Aanbevolen procedures voor het beveiligen van MSXML-code](/previous-versions/windows/desktop/ms759188(v=vs.85)), [NSXMLParserDelegate-protocol referentie](https://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [het oplossen van externe verwijzingen](/dotnet/standard/data/xml/resolving-external-resources) |
| **Stappen**| <p>Hoewel het niet veel wordt gebruikt, is er een functie van XML waarmee de XML-parser macro-entiteiten kan uitbreiden met waarden die zijn gedefinieerd in het document zelf of vanuit externe bronnen. Het document kan bijvoorbeeld een entiteit ' CompanyName ' met de waarde ' micro soft ' definiëren, zodat elke keer dat de tekst &companyname; wordt weer gegeven in het document, deze automatisch wordt vervangen door de tekst van micro soft. Het is ook mogelijk dat het document een entiteit "MSFTStock" definieert die verwijst naar een externe webservice om de huidige waarde van micro soft-aandelen op te halen.</p><p>Vervolgens wordt het tijdstip ' &MSFTStock; ' weer gegeven in het document. dit wordt automatisch vervangen door de huidige aandelen koers. Deze functionaliteit kan echter worden misbruikt om DoS-voor waarden (Denial of service) te maken. Een aanvaller kan meerdere entiteiten nesten om een exponentiële XML bommen te maken die alle beschik bare geheugen op het systeem verbruikt. </p><p>Daarnaast kan hij een externe verwijzing maken die een oneindige hoeveelheid gegevens verstuurt of die de thread gewoon vastloopt. Als gevolg hiervan moeten alle teams de interne en/of externe XML-entiteits omzetting volledig uitschakelen als hun toepassing deze niet gebruikt, of de hoeveelheid geheugen en tijd die de toepassing kan gebruiken voor de omzetting van de entiteit hand matig beperken als deze functionaliteit absoluut nood zakelijk is. Als er geen Entiteits resolutie is vereist voor uw toepassing, schakelt u deze uit. </p>|

### <a name="example"></a>Voorbeeld
Voor .NET Framework code kunt u de volgende methoden gebruiken:

```csharp
XmlTextReader reader = new XmlTextReader(stream);
reader.ProhibitDtd = true;

XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);

// for .NET 4
XmlReaderSettings settings = new XmlReaderSettings();
settings.DtdProcessing = DtdProcessing.Prohibit;
XmlReader reader = XmlReader.Create(stream, settings);
```
Houd er rekening mee dat de standaard waarde van `ProhibitDtd` in is ingesteld op `XmlReaderSettings` True, maar in `XmlTextReader` dat geval is False. Als u XmlReaderSettings gebruikt, hoeft u ProhibitDtd niet expliciet in te stellen op True, maar wordt het echter wel aanbevolen. Houd er ook rekening mee dat de klasse XmlDocument standaard de omzetting van entiteit toestaat. 

### <a name="example"></a>Voorbeeld
Als u de entiteits resolutie voor XmlDocuments wilt uitschakelen, gebruikt u de `XmlDocument.Load(XmlReader)` overbelasting van de methode load en stelt u de juiste eigenschappen in het argument XmlReader in om de oplossing uit te scha kelen, zoals wordt geïllustreerd in de volgende code: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Voorbeeld
Als het uitschakelen van de entiteits omzetting niet mogelijk is voor uw toepassing, stelt u de eigenschap XmlReaderSettings. MaxCharactersFromEntities in op een redelijke waarde op basis van de behoeften van uw toepassing. Hierdoor wordt de impact van mogelijke exponentiële uitbreidings DoS-aanvallen beperkt. De volgende code geeft een voor beeld van deze benadering: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Voorbeeld
Als u inline-entiteiten moet omzetten, maar geen externe entiteiten hoeft op te lossen, stelt u de eigenschap XmlReaderSettings.Xmlresolver in op null. Bijvoorbeeld: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Houd er rekening mee dat in MSXML6 ProhibitDTD standaard is ingesteld op True (DTD-verwerking uitschakelen). Voor Apple OSX/iOS-code zijn er twee XML-parsers die u kunt gebruiken: NSXMLParser en libXML2. 

## <a name="applications-utilizing-httpsys-perform-url-canonicalization-verification"></a><a id="app-verification"></a>Toepassingen die gebruikmaken van http.sys verificatie van URL-standaardisatie uitvoeren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | <p>Alle toepassingen die gebruikmaken van http.sys, moeten voldoen aan de volgende richt lijnen:</p><ul><li>De lengte van de URL beperken tot Maxi maal 16.384 tekens (ASCII of Unicode). Dit is de absolute maximale URL-lengte op basis van de standaard instelling voor Internet Information Services (IIS) 6. Websites moeten zo een korte lengte hebben als dat mogelijk is</li><li>Gebruik de standaard .NET Framework bestands-I/O-klassen (zoals FileStream), omdat deze gebruikmaken van de standaardisatie regels in de .NET FX</li><li>Een lijst met bekende bestands namen expliciet samen stellen</li><li>Bekende bestands types expliciet afwijzen die u niet wilt gebruiken voor UrlScan-weigeringen: exe, bat, cmd, com, HTW, Ida, idq, HTR, IDC, shtm [l], stm, printer, ini, Pol, dat bestanden</li><li>De volgende uitzonde ringen worden onderschept:<ul><li>System. ArgumentException (voor apparaatnamen)</li><li>System. NotSupportedException (voor gegevens stromen)</li><li>System. IO. FileNotFoundException (voor ongeldige bestands namen met escape)</li><li>System. IO. DirectoryNotFoundException (voor ongeldige mappen-Escape)</li></ul></li><li>*Niet* aanroepen naar Win32 I/O-api's voor bestanden. Op een ongeldige URL retourneert een fout melding van 400 naar de gebruiker en wordt de echte fout vastgelegd.</li></ul>|

## <a name="ensure-appropriate-controls-are-in-place-when-accepting-files-from-users"></a><a id="controls-users"></a>Zorg ervoor dat de juiste besturings elementen worden uitgevoerd wanneer bestanden van gebruikers worden geaccepteerd

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | Niet- [beperkte bestands upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload), [tabel met bestands handtekeningen](https://www.garykessler.net/library/file_sigs.html) |
| **Stappen** | <p>Geüploade bestanden vertegenwoordigen een aanzienlijk risico op toepassingen.</p><p>De eerste stap in veel aanvallen is om te voor komen dat de code wordt aangevallen aan het systeem. Vervolgens hoeft de aanval alleen een manier te vinden om de uitgevoerde code op te halen. Als u een bestand uploadt, helpt de aanvaller de eerste stap uit te voeren. De gevolgen van het uploaden van onbeperkte bestanden kunnen variëren, met inbegrip van volledige systeem overname, een overbelast bestands systeem of Data Base, het door sturen van aanvallen naar back-end-systemen en eenvoudige ontdubbeling.</p><p>Dit is afhankelijk van wat de toepassing doet met het geüploade bestand en met name waar het is opgeslagen. De validatie aan de server zijde van het uploaden van bestanden ontbreekt. De volgende beveiligings controles moeten worden geïmplementeerd voor de functionaliteit voor het uploaden van bestanden:</p><ul><li>Controle van bestands extensie (alleen een geldige set toegestane bestands typen moet worden geaccepteerd)</li><li>Maximale bestands grootte</li><li>Het bestand mag niet worden geüpload naar Webroot; de locatie moet een directory op een niet-systeem station zijn</li><li>De naamgevings Conventie moet worden gevolgd, zodat de naam van het geüploade bestand een wille keurige waarde heeft, zodat het overschrijven van bestanden wordt voor komen</li><li>Bestanden moeten worden gescand op anti-virus voordat naar de schijf wordt geschreven</li><li>Zorg ervoor dat de bestands naam en eventuele andere meta gegevens (zoals bestandspad) worden gevalideerd voor schadelijke tekens</li><li>De bestands indeling hand tekening moet worden gecontroleerd om te voor komen dat een gebruiker een bestand met maskers uploadt (bijvoorbeeld het uploaden van een exe-bestand door extensie te wijzigen in txt)</li></ul>| 

### <a name="example"></a>Voorbeeld
Raadpleeg de onderstaande klasse voor meer informatie over het laatste punt met betrekking tot de validatie van de hand tekening van de bestands indeling: 

```csharp
        private static Dictionary<string, List<byte[]>> fileSignature = new Dictionary<string, List<byte[]>>
                    {
                    { ".DOC", new List<byte[]> { new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 } } },
                    { ".DOCX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".PDF", new List<byte[]> { new byte[] { 0x25, 0x50, 0x44, 0x46 } } },
                    { ".ZIP", new List<byte[]> 
                                            {
                                              new byte[] { 0x50, 0x4B, 0x03, 0x04 },
                                              new byte[] { 0x50, 0x4B, 0x4C, 0x49, 0x54, 0x55 },
                                              new byte[] { 0x50, 0x4B, 0x53, 0x70, 0x58 },
                                              new byte[] { 0x50, 0x4B, 0x05, 0x06 },
                                              new byte[] { 0x50, 0x4B, 0x07, 0x08 },
                                              new byte[] { 0x57, 0x69, 0x6E, 0x5A, 0x69, 0x70 }
                                                }
                                            },
                    { ".PNG", new List<byte[]> { new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A } } },
                    { ".JPG", new List<byte[]>
                                    {
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE1 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE8 }
                                    }
                                    },
                    { ".JPEG", new List<byte[]>
                                        { 
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 }
                                        }
                                        },
                    { ".XLS", new List<byte[]>
                                            {
                                              new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 },
                                              new byte[] { 0x09, 0x08, 0x10, 0x00, 0x00, 0x06, 0x05, 0x00 },
                                              new byte[] { 0xFD, 0xFF, 0xFF, 0xFF }
                                            }
                                            },
                    { ".XLSX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".GIF", new List<byte[]> { new byte[] { 0x47, 0x49, 0x46, 0x38 } } }
                };

        public static bool IsValidFileExtension(string fileName, byte[] fileData, byte[] allowedChars)
        {
            if (string.IsNullOrEmpty(fileName) || fileData == null || fileData.Length == 0)
            {
                return false;
            }

            bool flag = false;
            string ext = Path.GetExtension(fileName);
            if (string.IsNullOrEmpty(ext))
            {
                return false;
            }

            ext = ext.ToUpperInvariant();

            if (ext.Equals(".TXT") || ext.Equals(".CSV") || ext.Equals(".PRN"))
            {
                foreach (byte b in fileData)
                {
                    if (b > 0x7F)
                    {
                        if (allowedChars != null)
                        {
                            if (!allowedChars.Contains(b))
                            {
                                return false;
                            }
                        }
                        else
                        {
                            return false;
                        }
                    }
                }

                return true;
            }

            if (!fileSignature.ContainsKey(ext))
            {
                return true;
            }

            List<byte[]> sig = fileSignature[ext];
            foreach (byte[] b in sig)
            {
                var curFileSig = new byte[b.Length];
                Array.Copy(fileData, curFileSig, b.Length);
                if (curFileSig.SequenceEqual(b))
                {
                    flag = true;
                    break;
                }
            }

            return flag;
        }
```

## <a name="ensure-that-type-safe-parameters-are-used-in-web-application-for-data-access"></a><a id="typesafe"></a>Zorg ervoor dat type veilige para meters worden gebruikt in webtoepassing voor gegevens toegang

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | <p>Als u de para meters-verzameling gebruikt, wordt de invoer door SQL behandeld als een letterlijke waarde in plaats van als uitvoer bare code. De verzameling para meters kan worden gebruikt om type-en lengte beperkingen voor invoer gegevens af te dwingen. Waarden buiten het bereik activeren een uitzonde ring. Als type-safe SQL-para meters niet worden gebruikt, kunnen aanvallers mogelijk injectie aanvallen uitvoeren die zijn Inge sloten in de niet-gefilterde invoer.</p><p>Gebruik veilige para meters bij het samen stellen van SQL-query's om mogelijke SQL-injectie aanvallen te voor komen die zich kunnen voordoen met niet-gefilterde invoer. U kunt type safe-para meters met opgeslagen procedures en dynamische SQL-instructies gebruiken. Para meters worden beschouwd als letterlijke waarden door de data base en niet als uitvoer bare code. Para meters worden ook gecontroleerd op type en lengte.</p>|

### <a name="example"></a>Voorbeeld 
De volgende code laat zien hoe u type veilige para meters met de SqlParameterCollection gebruikt bij het aanroepen van een opgeslagen procedure. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
In het voor gaande code voorbeeld mag de invoer waarde niet langer zijn dan 11 tekens. Als de gegevens niet voldoen aan het type of de lengte die is gedefinieerd door de para meter, genereert de SqlParameter-klasse een uitzonde ring. 

## <a name="use-separate-model-binding-classes-or-binding-filter-lists-to-prevent-mvc-mass-assignment-vulnerability"></a><a id="binding-mvc"></a>Afzonderlijke model bindings klassen of bindings filter lijsten gebruiken om beveiligings problemen met een massale toewijzing voor komen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | MVC5, MVC6 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Kenmerken van meta gegevens](/dotnet/api/system.componentmodel.dataannotations.metadatatypeattribute), [beveiligings problemen met open bare sleutels](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation), [volledige hand leiding voor massale toewijzing in ASP.NET MVC](https://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [aan de slag met EF met MVC](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Stappen** | <ul><li>**Wanneer moet ik zoeken naar over-post beveiligings problemen?-** Er kunnen zich meer beveiligings problemen voordoen wanneer u model klassen bindt van gebruikers invoer. Frameworks, zoals MVC, kunnen gebruikers gegevens in aangepaste .NET-klassen vertegenwoordigen, waaronder gewone oude CLR-objecten (POCOs). MVC vult deze model klassen automatisch in met gegevens uit de aanvraag, waardoor een handige weer gave wordt geboden voor het omgaan met gebruikers invoer. Wanneer deze klassen eigenschappen bevatten die niet moeten worden ingesteld door de gebruiker, kan de toepassing kwetsbaar zijn voor over-post aanvallen, waarmee gebruikers de gegevens die de toepassing nooit heeft beoogd, kunnen worden beheerd. Net als bij het binden van MVC-modellen, biedt database toegangs technologieën, zoals object/relationele mappers, zoals Entity Framework vaak ook ondersteuning voor het gebruik van POCO-objecten om database gegevens weer te geven. Deze gegevens model klassen bieden hetzelfde gemak voor het omgaan met database gegevens als MVC voor het omgaan met gebruikers invoer. Omdat zowel MVC als de data base vergelijk bare modellen ondersteunen, zoals POCO-objecten, lijkt het eenvoudig om dezelfde klassen opnieuw te gebruiken voor beide doel einden. In deze praktijk is het niet mogelijk om de problemen te voor komen en is het een gemeen schappelijk gebied waarin onbedoelde eigenschappen worden blootgesteld aan model binding, waardoor het mogelijk is om aanvallen mogelijk te maken.</li><li>**Waarom zou ik mijn niet-gefilterde database model klassen niet als para meters voor mijn MVC-acties moeten gebruiken?-** Omdat de binding van MVC-modellen alles in die klasse verbindt. Zelfs als de gegevens niet in uw weer gave worden weer gegeven, kan een kwaadwillende gebruiker een HTTP-aanvraag met deze gegevens verzenden en MVC bindt deze, omdat uw actie aangeeft dat de database klasse de vorm van de gegevens is die voor gebruikers invoer moet worden geaccepteerd.</li><li>**Waarom zou ik moeten zorgen over de vorm die wordt gebruikt voor model binding?-** Het gebruik van de ASP.NET MVC-model binding met meer uitgebreide modellen toont een toepassing om aanvallen te voor komen. Over-boeking kunnen aanvallers de toepassings gegevens wijzigen behalve wat de ontwikkelaar heeft beoogd, zoals het overschrijven van de prijs voor een item of de beveiligings bevoegdheden voor een account. Toepassingen moeten actie-specifieke bindings modellen (of specifieke toegestane eigenschappen filter lijsten) gebruiken om een expliciet contract te bieden voor wat niet-vertrouwde invoer is toegestaan via model binding.</li><li>**Heeft afzonderlijke bindings modellen die alleen code dupliceren?-** Nee, het is een kwestie van een schei ding van de problemen. Als u database modellen opnieuw gebruikt in actie methoden, zegt u dat een eigenschap (of subeigenschap) in die klasse door de gebruiker kan worden ingesteld in een HTTP-aanvraag. Als dat niet het geval is, hebt u een filter lijst of een afzonderlijke klassen vorm nodig om MVC weer te geven welke gegevens van gebruikers invoer kunnen worden gebruikt.</li><li>**Als ik afzonderlijke bindings modellen heb voor gebruikers invoer, moet ik dan al mijn aantekeningen kenmerken dupliceren?-** Niet noodzakelijkerwijs. U kunt MetadataTypeAttribute gebruiken voor de klasse database model om een koppeling te maken met de meta gegevens van een model bindings klasse. Houd er rekening mee dat het type waarnaar wordt verwezen door de MetadataTypeAttribute, een subset van het verwijzende type moet zijn (het kan minder eigenschappen hebben, maar niet meer).</li><li>Het **is lastig om gegevens heen en weer te verplaatsen tussen gebruikers invoer modellen en database modellen. Kan ik met reflectie alleen over alle eigenschappen kopiëren? -** Ja. De enige eigenschappen die worden weer gegeven in de bindings modellen zijn degene die u hebt vastgesteld om veilig te zijn voor gebruikers invoer. Er is geen beveiligings reden waardoor reflectie niet kan worden gebruikt voor het kopiëren van alle eigenschappen die gemeen schappelijk zijn tussen deze twee modellen.</li><li>**Informatie over [BIND (exclude = "â &euro; ¦")]. Kan ik dat gebruiken in plaats van afzonderlijke bindings modellen? -** Deze methode wordt niet aanbevolen. Als u [BIND (exclude = "â &euro; ¦")] gebruikt, betekent dit dat een nieuwe eigenschap standaard kan worden gebonden. Wanneer er een nieuwe eigenschap wordt toegevoegd, is er een extra stap voor het beveiligen van dingen, in plaats van dat het ontwerp standaard wordt beveiligd. Afhankelijk van de ontwikkelaar die deze lijst controleert elke keer dat een eigenschap wordt toegevoegd, is riskant.</li><li>**Is [BIND (include = "â &euro; ¦")] handig voor bewerkingen bewerken?-** Nee. [BIND (include = "â &euro; ¦")] is alleen geschikt voor bewerkingen met toevoeging (het toevoegen van nieuwe gegevens). Voor UPDATE-Style-bewerkingen (bestaande gegevens reviseren) gebruikt u een andere methode, zoals het gebruik van afzonderlijke bindings modellen of het door geven van een expliciete lijst met toegestane eigenschappen aan UpdateModel of TryUpdateModel. Bij het toevoegen van een [BIND (include = "â &euro; ¦")]-kenmerk bij een bewerkings bewerking wordt aangegeven dat MVC een object exemplaar maakt en alleen de vermelde eigenschappen instelt, waardoor alle andere op hun standaard waarden worden uitgevoerd. Wanneer de gegevens worden bewaard, wordt de bestaande entiteit volledig vervangen en worden de waarden voor de wegge laten eigenschappen opnieuw ingesteld op de standaard instellingen. Als IsAdmin bijvoorbeeld is wegge laten uit het kenmerk [BIND (include = "â &euro; ¦")] voor een bewerking, zou elke gebruiker waarvan de naam is bewerkt via deze actie, opnieuw worden ingesteld op IsAdmin = False (elke bewerkte gebruiker heeft de status van de beheerder verloren). Als u wilt voor komen dat bepaalde eigenschappen worden bijgewerkt, gebruikt u een van de andere benaderingen hierboven. Houd er rekening mee dat sommige versies van MVC-besturings elementen controller klassen genereren met [BIND (include = "â &euro; ¦")] voor het bewerken van acties en impliceren dat het verwijderen van een eigenschap uit die lijst tot andere aanvallen voor komt. Zoals hierboven beschreven, werkt deze aanpak echter niet zoals bedoeld en worden in plaats daarvan de gegevens in de wegge laten eigenschappen opnieuw ingesteld op de standaard waarden.</li><li>**Voor Create-bewerkingen zijn er aanvullende opmerkingen met [BIND (include = "â &euro; ¦")] in plaats van afzonderlijke bindings modellen?-** Ja. De eerste deze benadering werkt niet voor bewerkings scenario's en vereist het onderhouden van twee afzonderlijke benaderingen voor het beperken van alle over-post beveiligings problemen. Ten tweede dwingen afzonderlijke bindings modellen de bezorgdheid af tussen de vorm die wordt gebruikt voor gebruikers invoer en de vorm die wordt gebruikt voor persistentie, iets [binding (include = "â &euro; ¦")] niet. Ten derde ziet u dat [BIND (include = "â &euro; ¦")] alleen eigenschappen van het hoogste niveau kan verwerken. u kunt alleen delen van subeigenschappen (zoals "Details.name") in het kenmerk toestaan. Ten slotte, en wellicht het belangrijkst, met behulp van [BIND (include = "â &euro; ¦")], wordt een extra stap toegevoegd die telkens opnieuw moet worden onthouden als de klasse voor model binding wordt gebruikt. Als een nieuwe actie methode rechtstreeks aan de gegevens klasse wordt gekoppeld en vergeet om een [BIND (include = "â &euro; ¦")]-kenmerk toe te voegen, kan dit kwetsbaar zijn voor over-post aanvallen, dus de methode [BIND (include = "â &euro; ¦")] is standaard iets minder veilig. Als u [BIND (include = "â &euro; ¦")] gebruikt, moet u er altijd voor zorgen dat u deze telkens opgeeft wanneer uw gegevens klassen worden weer gegeven als actie methode para meters.</li><li>**Voor bewerkingen voor maken moet u het kenmerk [BIND (include = "â &euro; ¦")] op de model klasse zelf plaatsen? Is dit niet zo voor komen dat u het kenmerk op elke actie methode hoeft te onthouden? :** Deze methode werkt in sommige gevallen. Met [BIND (include = "â &euro; ¦")] op het model type zelf (in plaats van op actie parameters met deze klasse), hoeft u niet te onthouden het kenmerk [BIND (include = "â &euro; ¦")] op elke actie methode toe te voegen. Als u het kenmerk rechtstreeks op de klasse gebruikt, wordt er een afzonderlijke surface area van deze klasse voor model bindings doeleinden gemaakt. Deze aanpak biedt echter alleen een shape voor model binding per model klasse. Als één actie methode een model binding van een veld moet toestaan (bijvoorbeeld een actie die door een beheerder is ingesteld waarmee gebruikers rollen worden bijgewerkt) en andere acties de model binding van dit veld moeten voor komen, werkt deze methode niet. Elke klasse kan slechts één vorm voor model binding hebben. Als verschillende acties verschillende shapes voor model bindingen nodig hebben, moeten deze afzonderlijke vormen worden aangeduid met afzonderlijke model binding klassen of afzonderlijke [BIND (include = "â &euro; ¦")] kenmerken van de actie methoden.</li><li>**Wat zijn bindings modellen? Zijn ze hetzelfde als modellen weer geven? -** Dit zijn twee gerelateerde concepten. Het bindings model term verwijst naar een model klasse die wordt gebruikt in de parameter lijst van een actie (de vorm die is door gegeven vanuit MVC model binding met de actie methode). Het model van de term weergave verwijst naar een model klasse die van een actie methode is door gegeven aan een weer gave. Het gebruik van een View-specifiek model is een algemene benadering voor het door geven van gegevens van een actie methode naar een weer gave. Deze vorm is vaak ook geschikt voor model binding en het model voor term weergave kan worden gebruikt om te verwijzen naar hetzelfde model dat wordt gebruikt op beide locaties. Om nauw keuriger te zijn, spreekt deze procedure met name over bindings modellen, waarmee wordt gefocust op de vorm die wordt door gegeven aan de actie, wat de voor delen van een massale toewijzing is.</li></ul>| 

## <a name="encode-untrusted-web-output-prior-to-rendering"></a><a id="rendering"></a>Niet-vertrouwde webuitvoer coderen vóór Rendering

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Generic, webformulieren, MVC5, MVC6 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Cross-site scripting voor komen in ASP.net](/previous-versions/msp-n-p/ff649310(v=pandp.10)), [cross-site scripting](https://cwe.mitre.org/data/definitions/79.html), [XSS (cross-site scripting) Cheat-blad voor komen](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **Stappen** | Cross-site scripting (vaak afgekort als XSS) is een aanvals vector voor onlineservices of een toepassing/onderdeel dat invoer van het web verbruikt. Met XSS-beveiligings problemen kan een aanvaller een script uitvoeren op de computer van een andere gebruiker via een kwets bare webtoepassing. Schadelijke scripts kunnen worden gebruikt om cookies te stelen en anderszins te knoeien met de machine van een slacht offer via Java script. XSS wordt voor komen door het valideren van de invoer van gebruikers, het is goed gevormd en de code ring voordat deze wordt weer gegeven op een webpagina. Invoer validatie en uitvoer codering kan worden uitgevoerd met behulp van de bibliotheek voor webbeveiligingen. Voor beheerde code (C \# , vb.net, enzovoort) gebruikt u een of meer van de juiste coderings methoden van de Web Protection-bibliotheek (anti-XSS), afhankelijk van de context waarin de gebruikers invoer wordt gemanifesteerd:| 

### <a name="example"></a>Voorbeeld

```csharp
* Encoder.HtmlEncode 
* Encoder.HtmlAttributeEncode 
* Encoder.JavaScriptEncode 
* Encoder.UrlEncode
* Encoder.VisualBasicScriptEncode 
* Encoder.XmlEncode 
* Encoder.XmlAttributeEncode 
* Encoder.CssEncode 
* Encoder.LdapEncode 
```

## <a name="perform-input-validation-and-filtering-on-all-string-type-model-properties"></a><a id="typemodel"></a>Invoer validatie en filteren uitvoeren op alle model eigenschappen van het teken reeks type

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Generic, MVC5, MVC6 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Validatie toevoegen](https://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [model gegevens valideren in een MVC-toepassing](/previous-versions/dd410404(v=vs.90)), [leidende principes voor uw ASP.NET MVC-toepassingen](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **Stappen** | <p>Alle invoer parameters moeten worden gevalideerd voordat ze in de toepassing worden gebruikt om ervoor te zorgen dat de toepassing wordt beschermd tegen schadelijke gebruikers invoer. Valideer de invoer waarden met behulp van reguliere expressies op de server met een validatie strategie waarvoor een lijst is toegestaan. Niet-verschoonde gebruikers invoer/para meters die aan de methoden worden door gegeven, kunnen beveiligings problemen voor code injectie veroorzaken.</p><p>Voor webtoepassingen kunnen ingangs punten ook formulier velden, query parameters, cookies, HTTP-headers en web service-para meters bevatten.</p><p>De volgende invoer validatie controles moeten worden uitgevoerd bij model binding:</p><ul><li>De model eigenschappen moeten worden voorzien van aantekeningen met RegularExpression-aantekening, voor het accepteren van toegestane tekens en Maxi maal toegestane lengte</li><li>De controller methoden moeten ModelState geldigheid uitvoeren</li></ul>|

## <a name="sanitization-should-be-applied-on-form-fields-that-accept-all-characters-eg-rich-text-editor"></a><a id="richtext"></a>Opschonen moet worden toegepast op formulier velden waarin alle tekens worden geaccepteerd, bijvoorbeeld Rich Text Editor

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Onveilige invoer coderen](/previous-versions/msp-n-p/ff647397(v=pandp.10)#paght000003_step3), [HTML-](https://github.com/mganss/HtmlSanitizer) opschoner |
| **Stappen** | <p>Identificeer alle statische opmaak tags die u wilt gebruiken. Een veelvoorkomende procedure is het beperken van opmaak voor veilige HTML-elementen, zoals `<b>` (vet) en `<i>` (cursief).</p><p>Voordat u de gegevens schrijft, kunt u deze door HTML coderen. Dit maakt schadelijk script veilig door ervoor te zorgen dat het wordt verwerkt als tekst, niet als uitvoer bare code.</p><ol><li>ASP.NET-aanvraag validatie uitschakelen door het kenmerk ValidateRequest = false toe te voegen aan de \@ pagina-instructie</li><li>De teken reeks invoer coderen met de methode HtmlEncode</li><li>Gebruik een String Builder en roep de vervangende methode aan om selectief de code ring te verwijderen voor de HTML-elementen die u wilt toestaan</li></ol><p>Op de pagina in de verwijzingen wordt de instelling validatie van ASP.NET-aanvragen uitgeschakeld `ValidateRequest="false"` . Het HTML-codeert de invoer en maakt selectief `<b>` `<i>` mogelijk een .net-bibliotheek voor HTML-opschoning kan ook worden gebruikt.</p><p>HtmlSanitizer is een .NET-bibliotheek voor het opruimen van HTML-fragmenten en documenten uit constructs die kunnen leiden tot XSS-aanvallen. Er wordt gebruikgemaakt van AngleSharp om HTML en CSS te parseren, te bewerken en weer te geven. HtmlSanitizer kan worden geïnstalleerd als een NuGet-pakket en de gebruikers invoer kan worden door gegeven via relevante HTML of CSS-methoden voor opschonen, zoals van toepassing, aan de server zijde. Houd er rekening mee dat opschonen als een beveiligings controle alleen als laatste optie moet worden beschouwd.</p><p>Invoer validatie en uitvoer codering worden beschouwd als betere beveiligings controles.</p> |

## <a name="do-not-assign-dom-elements-to-sinks-that-do-not-have-inbuilt-encoding"></a><a id="inbuilt-encode"></a>Wijs geen DOM-elementen toe aan sinks die geen ingebouwde code ring hebben

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | Veel Java script-functies worden niet standaard gecodeerd. Wanneer u niet-vertrouwde invoer aan DOM-elementen via dergelijke functies toewijst, kan dit leiden tot het uitvoeren van een XSS (cross-site script).| 

### <a name="example"></a>Voorbeeld
Hier volgen onveilige voor beelden: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Gebruik `innerHtml` in plaats daarvan `innerText` . Op dezelfde manier, in plaats van `$("#elm").html()` , gebruikt u `$("#elm").text()` 

## <a name="validate-all-redirects-within-the-application-are-closed-or-done-safely"></a><a id="redirect-safe"></a>Alle omleidingen valideren binnen de toepassing worden gesloten of veilig uitgevoerd

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Het OAuth 2,0-autorisatie raamwerk-open redirectors](https://tools.ietf.org/html/rfc6749#section-10.15) |
| **Stappen** | <p>Een toepassings ontwerp dat omleiding naar een door de gebruiker opgegeven locatie vereist, moet de mogelijke omleidings doelen beperken tot een vooraf gedefinieerde ' veilige ' lijst met sites of domeinen. Alle omleidingen in de toepassing moeten worden gesloten/veilig.</p><p>Om dit te doen:</p><ul><li>Alle omleidingen identificeren</li><li>Implementeer een passende beperking voor elke omleiding. De juiste beperkingen zijn onder andere het omleiden van toegestane lijst of gebruikers bevestiging. Als een website of service met een beveiligingslek met betrekking tot open omleiding gebruikmaakt van Facebook/OAuth/OpenID Connect-id-providers, kan een aanvaller het aanmeldings token van een gebruiker stelen en die gebruiker imiteren. Dit is een inherent risico voor het gebruik van OAuth, dat wordt beschreven in RFC 6749 ' The OAuth 2,0 Authorization Framework ', sectie 10,15 ' open redirections ', op vergelijk bare wijze kunnen gebruikers referenties worden aangetast door middel van een gekraakte phishing-aanval via open omleidingen</li></ul>|

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-controller-methods"></a><a id="string-method"></a>Invoer validatie implementeren voor alle teken reeks type parameters die door controller methoden worden geaccepteerd

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Generic, MVC5, MVC6 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Model gegevens valideren in een MVC-toepassing](/previous-versions/dd410404(v=vs.90)), [leidende principes voor uw ASP.NET MVC-toepassingen](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **Stappen** | Voor methoden die alleen primitieve gegevens typen accepteren en geen modellen als argument, moet invoer validatie worden uitgevoerd met reguliere expressie. Hier moet regex. IsMatch worden gebruikt met een geldig regex-patroon. Als de invoer niet overeenkomt met de opgegeven reguliere expressie, moet het besturings element niet verder worden voortgezet en moet er een passende waarschuwing over validatie fout worden weer gegeven.| 

## <a name="set-upper-limit-timeout-for-regular-expression-processing-to-prevent-dos-due-to-bad-regular-expressions"></a><a id="dos-expression"></a>Time-out voor de bovengrens instellen voor de verwerking van reguliere expressies om DoS door onjuiste reguliere expressies te voor komen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Generic, webformulieren, MVC5, MVC6  |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Eigenschap DefaultRegexMatchTimeout](/dotnet/api/system.web.configuration.httpruntimesection.defaultregexmatchtimeout) |
| **Stappen** | Om denial of service-aanvallen tegen slecht gemaakte reguliere expressies te garanderen die een groot aantal backtracking veroorzaken, stelt u de algemene standaardtime-out in. Als de verwerkings tijd langer duurt dan de gedefinieerde bovengrens, genereert het een time-outuitzondering. Als er niets is geconfigureerd, zou de time-out oneindig zijn.| 

### <a name="example"></a>Voorbeeld
Met de volgende configuratie wordt bijvoorbeeld een RegexMatchTimeoutException gegenereerd als de verwerking meer dan 5 seconden in beslag neemt: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a name="avoid-using-htmlraw-in-razor-views"></a><a id="html-razor"></a>Vermijd het gebruik van HTML. RAW in haar weer gaven

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | MVC5, MVC6 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| Stap | ASP.NET Webpagina's (haar) automatische HTML-code ring uitvoeren. Alle teken reeksen die worden afgedrukt door de Inge sloten code Nuggets (@ blokken), worden automatisch met HTML gecodeerd. Wanneer `HtmlHelper.Raw` de methode wordt aangeroepen, retourneert deze echter markeringen die geen HTML-code ring zijn. Als `Html.Raw()` er een helper-methode wordt gebruikt, wordt de automatische coderings beveiliging die haar biedt omzeild.|

### <a name="example"></a>Voorbeeld
Hier volgt een onveilig voor beeld: 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Gebruik dit `Html.Raw()` alleen als u opmaak wilt weer geven. Met deze methode wordt geen impliciete uitvoer codering uitgevoerd. Gebruik andere ASP.NET-helpers bijvoorbeeld `@Html.DisplayFor()` 

## <a name="do-not-use-dynamic-queries-in-stored-procedures"></a><a id="stored-proc"></a>Geen dynamische query's gebruiken in opgeslagen procedures

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | <p>Een aanval via SQL-injectie maakt misbruik van beveiligings problemen in invoer validatie om wille keurige opdrachten uit te voeren in de-data base. Dit kan gebeuren wanneer uw toepassing invoer gebruikt om dynamische SQL-instructies te maken voor toegang tot de data base. Dit kan ook optreden als uw code opgeslagen procedures gebruikt die zijn door gegeven en teken reeksen bevatten met onbewerkte gebruikers invoer. Met behulp van de SQL-injectie aanval kan de aanvaller wille keurige opdrachten uitvoeren in de-data base. Alle SQL-instructies (inclusief de SQL-instructies in opgeslagen procedures) moeten zijn para meters. SQL-instructies met para meters accepteren tekens die een speciale betekenis hebben voor SQL (zoals één aanhalings teken) zonder problemen, omdat ze sterk getypeerd zijn. |

### <a name="example"></a>Voorbeeld
Hieronder volgt een voor beeld van een onveilige, dynamische opgeslagen procedure: 

```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteria]
(
  @productName nvarchar(200) = NULL,
  @startPrice float = NULL,
  @endPrice float = NULL
)
AS
 BEGIN
  DECLARE @sql nvarchar(max)
  SELECT @sql = ' SELECT ProductID, ProductName, Description, UnitPrice, ImagePath' +
       ' FROM dbo.Products WHERE 1 = 1 '
       PRINT @sql
  IF @productName IS NOT NULL
     SELECT @sql = @sql + ' AND ProductName LIKE ''%' + @productName + '%'''
  IF @startPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice > ''' + CONVERT(VARCHAR(10),@startPrice) + ''''
  IF @endPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice < ''' + CONVERT(VARCHAR(10),@endPrice) + ''''

  PRINT @sql
  EXEC(@sql)
 END
```

### <a name="example"></a>Voorbeeld
Hierna volgt dezelfde opgeslagen procedure die veilig is geïmplementeerd: 
```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteriaSecure]
(
             @productName nvarchar(200) = NULL,
             @startPrice float = NULL,
             @endPrice float = NULL
)
AS
       BEGIN
             SELECT ProductID, ProductName, Description, UnitPrice, ImagePath
             FROM dbo.Products where
             (@productName IS NULL or ProductName like '%'+ @productName +'%')
             AND
             (@startPrice IS NULL or UnitPrice > @startPrice)
             AND
             (@endPrice IS NULL or UnitPrice < @endPrice)         
       END
```

## <a name="ensure-that-model-validation-is-done-on-web-api-methods"></a><a id="validation-api"></a>Controleren of model validatie is uitgevoerd op Web API-methoden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Web-API | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | MVC5, MVC6 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Model validatie in ASP.NET Web-API](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Stappen** | Wanneer een client gegevens naar een web-API verzendt, is het verplicht om de gegevens te valideren voordat ze worden verwerkt. Gebruik voor ASP.NET-Web-Api's die modellen als invoer accepteren, gegevens aantekeningen in modellen om validatie regels in te stellen voor de eigenschappen van het model.|

### <a name="example"></a>Voorbeeld
De volgende code toont hetzelfde: 

```csharp
using System.ComponentModel.DataAnnotations;

namespace MyApi.Models
{
    public class Product
    {
        public int Id { get; set; }
        [Required]
        [RegularExpression(@"^[a-zA-Z0-9]*$", ErrorMessage="Only alphanumeric characters are allowed.")]
        public string Name { get; set; }
        public decimal Price { get; set; }
        [Range(0, 999)]
        public double Weight { get; set; }
    }
}
```

### <a name="example"></a>Voorbeeld
In de actie methode van de API-controllers moet de geldigheid van het model expliciet worden gecontroleerd, zoals hieronder wordt weer gegeven: 

```csharp
namespace MyApi.Controllers
{
    public class ProductsController : ApiController
    {
        public HttpResponseMessage Post(Product product)
        {
            if (ModelState.IsValid)
            {
                // Do something with the product (not shown).

                return new HttpResponseMessage(HttpStatusCode.OK);
            }
            else
            {
                return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
            }
        }
    }
}
```

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-web-api-methods"></a><a id="string-api"></a>Invoer validatie implementeren voor alle teken reeks type parameters die worden geaccepteerd door Web API-methoden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Web-API | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen, MVC 5, MVC 6 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Model gegevens valideren in een MVC-toepassing](/previous-versions/dd410404(v=vs.90)), [leidende principes voor uw ASP.NET MVC-toepassingen](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **Stappen** | Voor methoden die alleen primitieve gegevens typen accepteren en geen modellen als argument, moet invoer validatie worden uitgevoerd met reguliere expressie. Hier moet regex. IsMatch worden gebruikt met een geldig regex-patroon. Als de invoer niet overeenkomt met de opgegeven reguliere expressie, moet het besturings element niet verder worden voortgezet en moet er een passende waarschuwing over validatie fout worden weer gegeven.|

## <a name="ensure-that-type-safe-parameters-are-used-in-web-api-for-data-access"></a><a id="typesafe-api"></a>Zorg ervoor dat type veilige para meters worden gebruikt in Web-API voor gegevens toegang

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Web-API | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | <p>Als u de para meters-verzameling gebruikt, wordt de invoer door SQL behandeld als een letterlijke waarde in plaats van als uitvoer bare code. De verzameling para meters kan worden gebruikt om type-en lengte beperkingen voor invoer gegevens af te dwingen. Waarden buiten het bereik activeren een uitzonde ring. Als type-safe SQL-para meters niet worden gebruikt, kunnen aanvallers mogelijk injectie aanvallen uitvoeren die zijn Inge sloten in de niet-gefilterde invoer.</p><p>Gebruik veilige para meters bij het samen stellen van SQL-query's om mogelijke SQL-injectie aanvallen te voor komen die zich kunnen voordoen met niet-gefilterde invoer. U kunt type safe-para meters met opgeslagen procedures en dynamische SQL-instructies gebruiken. Para meters worden beschouwd als letterlijke waarden door de data base en niet als uitvoer bare code. Para meters worden ook gecontroleerd op type en lengte.</p>|

### <a name="example"></a>Voorbeeld
De volgende code laat zien hoe u type veilige para meters met de SqlParameterCollection gebruikt bij het aanroepen van een opgeslagen procedure. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
In het voor gaande code voorbeeld mag de invoer waarde niet langer zijn dan 11 tekens. Als de gegevens niet voldoen aan het type of de lengte die is gedefinieerd door de para meter, genereert de SqlParameter-klasse een uitzonde ring. 

## <a name="use-parameterized-sql-queries-for-cosmos-db"></a><a id="sql-docdb"></a>SQL-query's met para meters gebruiken voor Cosmos DB

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Azure document DB | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Aankondigen van SQL parameterisering in Azure Cosmos DB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Stappen** | Hoewel Azure Cosmos DB alleen alleen-lezen query's ondersteunt, is SQL-injectie nog steeds mogelijk als query's worden gemaakt door samen voeging met gebruikers invoer. Het is mogelijk dat een gebruiker toegang krijgt tot gegevens die ze niet mogen openen binnen dezelfde verzameling door kwaad aardige SQL-query's te maken. SQL-query's met para meters gebruiken als query's worden samengesteld op basis van gebruikers invoer. |

## <a name="wcf-input-validation-through-schema-binding"></a><a id="schema-binding"></a>WCF-invoer validatie via schema binding

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | WCF | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen, NET Framework 3 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [MSDN](/previous-versions/msp-n-p/ff647820(v=pandp.10)) |
| **Stappen** | <p>Het ontbreken van validatie leidt tot verschillende typen ininjectie aanvallen.</p><p>Bericht validatie duidt op één verdedigings linie in de beveiliging van uw WCF-toepassing. Met deze aanpak valideert u berichten met behulp van schema's om WCF-service bewerkingen te beschermen tegen aanvallen door een kwaadwillende client. Valideer alle berichten die door de client worden ontvangen om de client te beschermen tegen aanvallen door een schadelijke service. Bericht validatie maakt het mogelijk om berichten te valideren wanneer bewerkingen gebruikmaken van bericht contracten of gegevens contracten, dat niet kan worden uitgevoerd met behulp van de validatie van de para meter. Met bericht validatie kunt u validatie logica binnen schema's maken, waardoor u meer flexibiliteit krijgt en de ontwikkelings tijd vermindert. Schema's kunnen opnieuw worden gebruikt voor verschillende toepassingen binnen de organisatie, waardoor standaarden worden gemaakt voor gegevens weergave. Daarnaast kunt u met bericht validatie bewerkingen beveiligen wanneer ze complexere gegevens typen gebruiken die betrekking hebben op contracten die bedrijfs logica vertegenwoordigen.</p><p>Als u bericht validatie wilt uitvoeren, bouwt u eerst een schema dat de bewerkingen van uw service en de gegevens typen vertegenwoordigt die door deze bewerkingen worden gebruikt. Vervolgens maakt u een .NET-klasse die een aangepaste client bericht controle en een aangepaste dispatcher-bericht controle implementeert voor het valideren van de berichten die zijn verzonden/ontvangen van/naar de service. Vervolgens implementeert u een aangepast eindpunt gedrag om bericht validatie in te scha kelen voor zowel de client als de service. Ten slotte implementeert u een aangepast configuratie-element op de klasse waarmee u het uitgebreide aangepaste eindpunt gedrag in het configuratie bestand van de service of de client kunt weer geven.</p>|

## <a name="wcf--input-validation-through-parameter-inspectors"></a><a id="parameters"></a>Validatie van WCF-invoer via parameter controles

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | WCF | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen, NET Framework 3 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [MSDN](/previous-versions/msp-n-p/ff647875(v=pandp.10)) |
| **Stappen** | <p>Invoer en gegevens validatie vertegenwoordigen een belang rijke verdedigings linie in de beveiliging van uw WCF-toepassing. U moet alle para meters valideren die worden weer gegeven in WCF-service bewerkingen om de service te beschermen tegen aanvallen door een kwaadwillende client. U moet ook alle retour waarden valideren die door de client zijn ontvangen om de client te beschermen tegen aanvallen door een schadelijke service</p><p>WCF biedt verschillende uitbreidings punten waarmee u het WCF-runtime gedrag kunt aanpassen door aangepaste extensies te maken. Bericht controles en parameter controles zijn twee uitbreidings mechanismen die worden gebruikt voor meer controle over de gegevens die tussen een client en een service worden door gegeven. Gebruik parameter controles voor invoer validatie en gebruik alleen bericht controles als u het hele bericht in en uit een service wilt inspecteren.</p><p>Als u invoer validatie wilt uitvoeren, maakt u een .NET-klasse en implementeert u een aangepaste parameter controle om para meters voor bewerkingen in uw service te valideren. Vervolgens implementeert u een aangepast eindpunt gedrag om validatie in te scha kelen voor zowel de client als de service. Ten slotte implementeert u een aangepast configuratie-element op de klasse waarmee u het uitgebreide aangepaste eindpunt gedrag kunt weer geven in het configuratie bestand van de service of de client</p>|