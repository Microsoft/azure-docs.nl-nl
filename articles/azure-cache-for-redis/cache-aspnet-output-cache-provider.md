---
title: ASP.NET-uitvoer cache provider voor Azure cache voor redis
description: Meer informatie over het opslaan van ASP.NET pagina-uitvoer met behulp van Azure cache voor redis. De redis-uitvoer cache provider is een out-of-process-opslag mechanisme voor uitvoer cache gegevens.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: 6d711b07a10e04dcdf31259f3e53c9687af28e28
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993390"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>ASP.NET-uitvoer cache provider voor Azure cache voor redis

De redis-uitvoer cache provider is een out-of-process-opslag mechanisme voor uitvoer cache gegevens. Deze gegevens zijn specifiek voor volledige HTTP-antwoorden (caching voor pagina-uitvoer). De provider wordt toegevoegd aan het nieuwe uitbreidings punt van de uitvoer cache provider die is geïntroduceerd in ASP.NET 4. Lees voor ASP.NET Core toepassingen [caching in ASP.net core](/aspnet/core/performance/caching/response). 

Als u de redis-uitvoer cache provider wilt gebruiken, moet u eerst uw cache configureren en vervolgens uw ASP.NET-toepassing configureren met het redis output cache provider NuGet-pakket. Dit onderwerp bevat richt lijnen voor het configureren van uw toepassing voor het gebruik van de redis-uitvoer cache provider. Zie [een cache maken](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)voor meer informatie over het maken en configureren van een Azure-cache voor redis-instantie.

## <a name="store-aspnet-page-output-in-the-cache"></a>ASP.NET-pagina-uitvoer opslaan in de cache

Als u in Visual Studio een client toepassing wilt configureren met behulp van het Azure-cache geheugen voor de redis-sessie status NuGet-pakket, klikt u op **NuGet package manager**, **Package Manager console** in het menu **extra** .

Voer de volgende opdracht uit vanuit het venster `Package Manager Console`.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Het NuGet-pakket van de redis-uitvoer cache provider heeft een afhankelijkheid van het pakket stack Exchange. redis. Strongname. Als het pakket stack Exchange. redis. Strongname niet aanwezig is in uw project, wordt het geïnstalleerd. Zie de pagina [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet voor meer informatie over het redis-pakket voor de NuGet.

>[!NOTE]
>Naast het pakket met de sterke naam stack Exchange. redis. Strongname is er ook de versie stack Exchange. redis zonder sterke naam. Als uw project de niet-sterke naam stack Exchange. redis-versie gebruikt, moet u het verwijderen. anders treden er naam conflicten op in uw project. Zie [.NET-cache-clients configureren](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)voor meer informatie over deze pakketten.

Het NuGet-pakket downloadt en voegt de vereiste assembly-verwijzingen toe en voegt de volgende sectie toe aan uw web.config-bestand. Deze sectie bevat de vereiste configuratie voor uw ASP.NET-toepassing voor het gebruik van de redis-uitvoer cache provider.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Configureer de kenmerken met de waarden van uw cache-Blade in de Microsoft Azure-portal en configureer de andere waarden naar wens. Zie voor instructies over het verkrijgen van toegang tot uw cache-eigenschappen [Azure cache configureren voor redis-instellingen](cache-configure.md#configure-azure-cache-for-redis-settings).

| Kenmerk | Type | Standaard | Beschrijving |
| --------- | ---- | ------- | ----------- |
| *hostsite* | tekenreeks | lokalehost | Het IP-adres of de hostnaam van de redis-server |
| *Importeer* | positief geheel getal | 6379 (niet-TLS/SSL)<br/>6380 (TLS/SSL) | Redis-server poort |
| *accessKey* | tekenreeks | "" | Redis-server wachtwoord wanneer redis-autorisatie is ingeschakeld. De waarde is een lege teken reeks, wat betekent dat de sessie status provider geen wacht woord gebruikt bij het verbinden met de redis-server. **Als uw redis-server zich in een openbaar toegankelijk netwerk zoals Azure Redis Cache bevindt, zorg er dan voor dat u redis-autorisatie inschakelt om de beveiliging te verbeteren en een veilig wacht woord op te geven.** |
| *-* | booleaans | **terecht** | Hiermee wordt aangegeven of verbinding moet worden gemaakt met een redis-server via TLS. Deze waarde is standaard **False** omdat redis geen ondersteuning biedt voor TLS van het vak. **Als u Azure Redis Cache gebruikt dat SSL van het vak ondersteunt, moet u dit instellen op waar om de beveiliging te verbeteren.**<br/><br/>De niet-TLS-poort is standaard uitgeschakeld voor nieuwe caches. Geef **waar** op als u wilt dat deze instelling de niet-TLS-poort gebruikt. Zie de sectie [toegangs poorten](cache-configure.md#access-ports) in het onderwerp [een cache configureren](cache-configure.md) voor meer informatie over het inschakelen van de niet-TLS-poort. |
| *databaseIdNumber* | positief geheel getal | 0 | *Dit kenmerk kan alleen worden opgegeven via web.config of AppSettings.*<br/><br/>Geef op welke redis-data base moet worden gebruikt. |
| *connectionTimeoutInMilliseconds* | positief geheel getal | Verschaft door stack Exchange. redis | Wordt gebruikt om *ConnectTimeout* in te stellen bij het maken van stack Exchange. redis. ConnectionMultiplexer. |
| *operationTimeoutInMilliseconds* | positief geheel getal | Verschaft door stack Exchange. redis | Wordt gebruikt om *SyncTimeout* in te stellen bij het maken van stack Exchange. redis. ConnectionMultiplexer. |
| *Connections Tring* (geldige stack Exchange. redis Connection String) | tekenreeks | *n.v.t.* | Ofwel een parameter verwijzing naar AppSettings of web.config, of een geldige stack Exchange. redis connection string. Dit kenmerk kan waarden bieden voor de kenmerken *host*, *poort*, *accessKey*, *SSL* en andere stack Exchange. redis. Zie voor meer informatie over *Connections* Tring [instellen](#setting-connectionstring) in het gedeelte [kenmerk opmerkingen](#attribute-notes) . |
| *settingsClassName*<br/>*settingsMethodName* | tekenreeks<br/>tekenreeks | *n.v.t.* | *Deze kenmerken kunnen alleen worden opgegeven via web.config of AppSettings.*<br/><br/>Gebruik deze kenmerken om een connection string op te geven. *settingsClassName* moet een assembly-gekwalificeerde klassen naam zijn die de methode bevat die is opgegeven door *settingsMethodName*.<br/><br/>De methode die wordt opgegeven door *settingsMethodName* moet openbaar, statisch zijn en void (geen para meters hebben) met het retour type **teken reeks**. Deze methode retourneert de werkelijke connection string. |
| *loggingClassName*<br/>*loggingMethodName* | tekenreeks<br/>tekenreeks | *n.v.t.* | *Deze kenmerken kunnen alleen worden opgegeven via web.config of AppSettings.*<br/><br/>Gebruik deze kenmerken voor het opsporen van fouten in uw toepassing door Logboeken te verstrekken uit de sessie status/uitvoer cache en logboeken van stack Exchange. redis. *loggingClassName* moet een assembly-gekwalificeerde klassen naam zijn die de methode bevat die is opgegeven door *loggingMethodName*.<br/><br/>De methode die wordt opgegeven door *loggingMethodName* moet openbaar, statisch zijn en void (geen para meters hebben) met het retour type **System. io. TextWriter**. |
| *applicationName* | tekenreeks | De module naam van het huidige proces of/ | *Alleen SessionStateProvider*<br/>*Dit kenmerk kan alleen worden opgegeven via web.config of AppSettings.*<br/><br/>Het voor voegsel van de app-naam dat moet worden gebruikt in de redis-cache. De klant kan dezelfde redis-cache gebruiken voor verschillende doel einden. Om ervoor te zorgen dat de sessie sleutels niet conflicteren, kan deze worden voorafgegaan door de naam van de toepassing. |
| *throwOnError* | booleaans | waar | *Alleen SessionStateProvider*<br/>*Dit kenmerk kan alleen worden opgegeven via web.config of AppSettings.*<br/><br/>Hiermee wordt aangegeven of er een uitzonde ring moet worden gegenereerd wanneer er een fout optreedt.<br/><br/>Voor meer informatie over *throwOnError* raadpleegt u [opmerkingen bij *ThrowOnError*](#notes-on-throwonerror) in het gedeelte [kenmerk opmerkingen](#attribute-notes) . |>*Microsoft. Web. redis. RedisSessionStateProvider. LastException*. |
| *retryTimeoutInMilliseconds* | positief geheel getal | 5000 | *Alleen SessionStateProvider*<br/>*Dit kenmerk kan alleen worden opgegeven via web.config of AppSettings.*<br/><br/>Hoe lang het opnieuw moet duren wanneer een bewerking mislukt. Als deze waarde lager is dan *operationTimeoutInMilliseconds*, wordt de provider niet opnieuw geprobeerd.<br/><br/>Voor meer informatie over *retryTimeoutInMilliseconds* raadpleegt u [opmerkingen bij *RetryTimeoutInMilliseconds*](#notes-on-retrytimeoutinmilliseconds) in het gedeelte [kenmerk opmerkingen](#attribute-notes) . |
| *redisSerializerType* | tekenreeks | *n.v.t.* | Hiermee geeft u de assembly-gekwalificeerde type naam op van een klasse die micro soft. Web. redis implementeert. ISerializer en die de aangepaste logica bevat voor het serialiseren en deserialiseren van de waarden. Zie [about *redisSerializerType*](#about-redisserializertype) in het gedeelte [kenmerk opmerkingen](#attribute-notes) voor meer informatie. |

## <a name="attribute-notes"></a>Opmerkingen bij kenmerk

### <a name="setting-connectionstring"></a>*Connections Tring* instellen

De waarde van *Connections Tring* wordt gebruikt als sleutel voor het ophalen van de werkelijke Connection String van appSettings, als een dergelijke teken reeks bestaat in appSettings. Als dit niet wordt gevonden in AppSettings, wordt de waarde van *Connections Tring* gebruikt als sleutel om de werkelijke Connection String op te halen uit de sectie web.config **Connections Tring** als deze sectie bestaat. Als de connection string niet bestaat in AppSettings of de sectie web.config **Connections Tring** , wordt de letterlijke waarde van *Connections Tring* als Connection String gebruikt bij het maken van stack Exchange. redis. ConnectionMultiplexer.

De volgende voor beelden laten zien hoe *Connections Tring* wordt gebruikt.

#### <a name="example-1"></a>Voorbeeld 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

In `web.config` gebruikt u de sleutel als parameter waarde in plaats van de werkelijke waarde.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>Voorbeeld 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

In `web.config` gebruikt u de sleutel als parameter waarde in plaats van de werkelijke waarde.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>Voorbeeld 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>Opmerkingen over *throwOnError*

Als er op dit moment een fout optreedt tijdens een sessie bewerking, genereert de sessie status provider een uitzonde ring. Hiermee wordt de toepassing afgesloten.

Dit gedrag is gewijzigd op een manier die de verwachtingen van bestaande gebruikers van de ASP.NET-sessie status provider ondersteunt en ook de mogelijkheid biedt om op uitzonde ringen te reageren, indien gewenst. Het standaard gedrag genereert nog steeds een uitzonde ring wanneer er een fout optreedt, consistent met andere ASP.NET-sessie status providers. bestaande code moet op dezelfde wijze werken als voorheen.

Als u *throwOnError* instelt op **False**, wordt er in plaats van een uitzonde ring gegenereerd wanneer er een fout optreedt, mislukt deze op de achtergrond. Controleer de statische eigenschap *Microsoft. Web. redis. RedisSessionStateProvider. LastException* om na te gaan of er een fout is opgetreden en of, als dit het geval is, wordt gedetecteerd wat de uitzonde ring was.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Opmerkingen over *retryTimeoutInMilliseconds*

Dit biedt enige pogings logica voor het vereenvoudigen van de situatie waarbij een bepaalde sessie bewerking opnieuw moet worden uitgevoerd wegens een fout vanwege dingen zoals netwerk storingen, terwijl u ook de time-out voor opnieuw proberen kunt bepalen of de nieuwe poging volledig opnieuw wilt proberen.

Als u *retryTimeoutInMilliseconds* instelt op een aantal, bijvoorbeeld 2000, wanneer een sessie bewerking mislukt, zal er een nieuwe poging worden gedaan voor 2000 milliseconden voordat deze wordt behandeld als een fout. Als u de sessie status provider wilt gebruiken om deze pogings logica toe te passen, moet u de time-out gewoon configureren. De eerste nieuwe poging vindt plaats na 20 milliseconden, wat in de meeste gevallen voldoende is wanneer er een netwerk fout optreedt. Daarna wordt elke seconde opnieuw geprobeerd, totdat er een time-out optreedt. Direct nadat de time-out is opgetreden, wordt er nog een keer opnieuw geprobeerd om ervoor te zorgen dat de time-out door (Maxi maal) één seconde niet wordt afgekapt.

Als u niet denkt dat u opnieuw moet proberen (bijvoorbeeld wanneer u de redis-server op dezelfde computer uitvoert als uw toepassing) of als u zelf de logica voor opnieuw proberen wilt afhandelen, stelt u *retryTimeoutInMilliseconds* in op 0.

### <a name="about-redisserializertype"></a>Over *redisSerializerType*

De serialisatie voor het opslaan van de waarden op redis wordt standaard uitgevoerd in een binaire indeling die wordt verschaft door de **BinaryFormatter** -klasse. Gebruik *redisSerializerType* om de assembly-gekwalificeerde type naam op te geven van een klasse die **micro soft. Web. redis. ISerializer** implementeert en de aangepaste logica heeft voor het serialiseren en deserialiseren van de waarden. Dit is bijvoorbeeld een JSON serializer-klasse met behulp van JSON.NET:

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Ervan uitgaande dat deze klasse is gedefinieerd in een assembly met de naam **MyCompanyDll**, kunt u de para meter *redisSerializerType* instellen om deze te gebruiken:

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>Instructie voor de uitvoer cache

Voeg een OutputCache-instructie toe aan elke pagina waarvoor u de uitvoer wilt opslaan in de cache.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

In het vorige voor beeld blijven de pagina gegevens in de cache gedurende 60 seconden in de cache en wordt er een andere versie van de pagina in de cache opgeslagen voor elke parameter combinatie. Zie voor meer informatie over de OutputCache-instructie [@OutputCache](/previous-versions/dotnet/netframework-4.0/hdxfb6cy(v=vs.100)) .

Zodra deze stappen zijn uitgevoerd, wordt uw toepassing geconfigureerd om de redis-uitvoer cache provider te gebruiken.

## <a name="third-party-output-cache-providers"></a>Uitvoer cache providers van derden

* [NCache](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [Apache Ignite](https://apacheignite-net.readme.io/docs/aspnet-output-caching)


## <a name="next-steps"></a>Volgende stappen

Bekijk de [ASP.net-sessie status provider voor Azure cache voor redis](cache-aspnet-session-state-provider.md).