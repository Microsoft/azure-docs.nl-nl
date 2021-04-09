---
title: ASP.NET-sessie status provider voor cache
description: Meer informatie over het opslaan van ASP.NET-sessie status in het geheugen met behulp van Azure cache voor redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 05/01/2017
ms.openlocfilehash: ce77f5074d707da5cfb251a103653b96e4644b5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92544525"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>ASP.NET-sessiestatusprovider voor Azure Cache voor Redis

Azure cache voor redis biedt een sessie status provider die u kunt gebruiken om de sessie status in het geheugen op te slaan met Azure cache voor redis in plaats van een SQL Server-Data Base. Als u de provider voor cache sessie status wilt gebruiken, moet u eerst uw cache configureren en vervolgens uw ASP.NET-toepassing configureren voor cache met behulp van het Azure-cache geheugen voor de redis-sessie status NuGet-pakket. Lees voor ASP.NET Core toepassingen de modus voor [sessie-en status beheer in ASP.net core](/aspnet/core/fundamentals/app-state).

Het is vaak niet praktisch in een echte Cloud-app om te voor komen dat u een vorm van status voor een gebruikers sessie opslaat, maar sommige benaderingen beïnvloeden de prestaties en schaal baarheid meer dan andere. Als u de status wilt opslaan, is de beste oplossing om de hoeveelheid status laag te bewaren en op te slaan in cookies. Als dat niet haalbaar is, is de volgende beste oplossing om de ASP.NET-sessie status te gebruiken met een provider voor gedistribueerde, in-memory cache. De slechtste oplossing van een oogpunt van prestaties en schaal baarheid is het gebruik van een Data Base-back-up van de sessie status. Dit onderwerp bevat richt lijnen voor het gebruik van de ASP.NET-sessie status provider voor Azure cache voor redis. Zie [ASP.net session status Options](#aspnet-session-state-options)(Engelstalig) voor meer informatie over andere sessie status opties.

## <a name="store-aspnet-session-state-in-the-cache"></a>De ASP.NET-sessiestatus in de cache opslaan

Als u in Visual Studio een client toepassing wilt configureren met behulp van het Azure-cache geheugen voor de redis-sessie status NuGet-pakket, klikt u op **NuGet package manager**, **Package Manager console** in het menu **extra** .

Voer de volgende opdracht uit vanuit het venster `Package Manager Console`.
    

```powershell
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Als u gebruikmaakt van de cluster functie uit de Premium-laag, moet u [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 of hoger gebruiken, anders wordt er een uitzonde ring gegenereerd. Verplaatsen naar 2.0.1 of hoger is een belang rijke wijziging; Zie [v 2.0.0 break Change Details](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details)(Engelstalig) voor meer informatie. Op het moment van de update van dit artikel is 2.2.3 de huidige versie van dit pakket.
> 
> 

Het NuGet-pakket van de redis-sessie status provider heeft een afhankelijkheid van het pakket stack Exchange. redis. Strongname. Als het pakket stack Exchange. redis. Strongname niet aanwezig is in uw project, wordt het geïnstalleerd.

>[!NOTE]
>Naast het pakket met de sterke naam stack Exchange. redis. Strongname is er ook de versie stack Exchange. redis zonder sterke naam. Als uw project de niet-sterke naam stack Exchange. redis-versie gebruikt, moet u dit verwijderen, anders krijgt u een naam conflict in uw project. Zie [.NET-cache-clients configureren](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)voor meer informatie over deze pakketten.
>
>

Het NuGet-pakket downloadt en voegt de vereiste assembly-verwijzingen toe en voegt de volgende sectie toe aan uw web.config-bestand. Deze sectie bevat de vereiste configuratie voor uw ASP.NET-toepassing voor het gebruik van de Azure-cache voor de redis-sessie status provider.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
  <providers>
    <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
    <!-- 'throwOnError','retryTimeoutInMilliseconds','databaseId' and 'applicationName' can be used with both options. -->
    <!--
      <add name="MySessionStateStore" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "5000" [number]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "1000" [number]
        connectionString = "<Valid StackExchange.Redis connection string>" [String]
        settingsClassName = "<Assembly qualified class name that contains settings method specified below. Which basically return 'connectionString' value>" [String]
        settingsMethodName = "<Settings method should be defined in settingsClass. It should be public, static, does not take any parameters and should have a return type of 'String', which is basically 'connectionString' value.>" [String]
        loggingClassName = "<Assembly qualified class name that contains logging method specified below>" [String]
        loggingMethodName = "<Logging method should be defined in loggingClass. It should be public, static, does not take any parameters and should have a return type of System.IO.TextWriter.>" [String]
        redisSerializerType = "<Assembly qualified class name that implements Microsoft.Web.Redis.ISerializer>" [String]
      />
    -->
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider"
         host=""
         accessKey=""
         ssl="true" />
  </providers>
</sessionState>
```

De sectie met opmerkingen bevat een voor beeld van de kenmerken en voorbeeld instellingen voor elk kenmerk.

Configureer de kenmerken met de waarden van uw cache-Blade in de Microsoft Azure-portal en configureer de andere waarden naar wens. Zie voor instructies over het verkrijgen van toegang tot uw cache-eigenschappen [Azure cache configureren voor redis-instellingen](cache-configure.md#configure-azure-cache-for-redis-settings).

* **host** : Geef uw cache-eind punt op.
* **poort** : gebruik een niet-TLS/SSL-poort of uw TLS/SSL-poort, afhankelijk van de TLS-instellingen.
* **accessKey** : gebruik de primaire of secundaire sleutel voor uw cache.
* **SSL** – True als u de cache/client communicatie met TLS wilt beveiligen. anders false. Zorg ervoor dat u de juiste poort opgeeft.
  * De niet-TLS-poort is standaard uitgeschakeld voor nieuwe caches. Geef waar op als u wilt dat deze instelling de TLS-poort gebruikt. Zie de sectie [toegangs poorten](cache-configure.md#access-ports) in het onderwerp [een cache configureren](cache-configure.md) voor meer informatie over het inschakelen van de niet-TLS-poort.
* **throwOnError** – True als u wilt dat een uitzonde ring wordt gegenereerd als er een fout optreedt, of ONWAAR als u wilt dat de bewerking op de achtergrond mislukt. U kunt controleren op fouten door de statische eigenschap micro soft. Web. redis. RedisSessionStateProvider. LastException te controleren. De standaard waarde is True.
* **retryTimeoutInMilliseconds** : bewerkingen waarvoor een failover wordt uitgevoerd, worden tijdens dit interval opnieuw uitgevoerd, opgegeven in milliseconden. De eerste nieuwe poging vindt plaats na 20 milliseconden en probeert vervolgens elke seconde opnieuw een poging tot het retryTimeoutInMilliseconds-interval verloopt. Onmiddellijk na dit interval wordt de bewerking een eind tijd opnieuw uitgevoerd. Als de bewerking nog steeds mislukt, wordt de uitzonde ring weer geretourneerd naar de aanroeper, afhankelijk van de instelling throwOnError. De standaard waarde is 0, wat betekent dat er geen nieuwe pogingen zijn.
* **databaseId** : Hiermee geeft u de Data Base op die moet worden gebruikt voor de uitvoer gegevens van de cache. Als u niets opgeeft, wordt de standaard waarde 0 gebruikt.
* **ApplicationName** : sleutels worden opgeslagen in redis als `{<Application Name>_<Session ID>}_Data` . Met dit naamgevings schema kunnen meerdere toepassingen hetzelfde redis-exemplaar delen. Deze para meter is optioneel en als u deze niet opgeeft, wordt de standaard waarde gebruikt.
* **connectionTimeoutInMilliseconds** : met deze instelling kunt u de instelling voor connectTimeout in de stack Exchange. redis-client onderdrukken. Als deze niet is opgegeven, wordt de standaard instelling voor connectTimeout van 5000 gebruikt. Zie het [configuratie model stack Exchange. redis](https://go.microsoft.com/fwlink/?LinkId=398705)voor meer informatie.
* **operationTimeoutInMilliseconds** : met deze instelling kunt u de instelling voor syncTimeout in de stack Exchange. redis-client onderdrukken. Als deze niet is opgegeven, wordt de standaard instelling voor syncTimeout van 1000 gebruikt. Zie het [configuratie model stack Exchange. redis](https://go.microsoft.com/fwlink/?LinkId=398705)voor meer informatie.
* **redisSerializerType** : met deze instelling kunt u aangepaste serialisatie van sessie-inhoud opgeven die wordt verzonden naar redis. Het opgegeven type moet een `Microsoft.Web.Redis.ISerializer` open bare constructor zonder para meters implementeren en moet declareren. Wordt standaard  `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` gebruikt.

Zie voor meer informatie over deze eigenschappen de oorspronkelijke blog post-aankondiging bij het [aankondigen van de ASP.net-sessie status provider voor redis](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/).

Vergeet niet om de sectie standaard InProc-sessie status provider in uw web.config uit te checken.

```xml
<!-- <sessionState mode="InProc"
     customProvider="DefaultSessionProvider">
     <providers>
        <add name="DefaultSessionProvider"
              type="System.Web.Providers.DefaultSessionStateProvider,
                    System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                    PublicKeyToken=31bf3856ad364e35"
              connectionStringName="DefaultConnection" />
      </providers>
</sessionState> -->
```

Zodra deze stappen zijn uitgevoerd, wordt uw toepassing geconfigureerd voor het gebruik van de Azure-cache voor de redis-sessie status provider. Wanneer u de sessie status in uw toepassing gebruikt, wordt deze opgeslagen in een Azure-cache voor redis-exemplaar.

> [!IMPORTANT]
> Gegevens die in de cache zijn opgeslagen, moeten serialiseerbaar zijn, in tegens telling tot de gegevens die kunnen worden opgeslagen in de standaard waarde voor de ASP.NET-sessie status provider in het geheugen. Wanneer de sessie status provider voor redis wordt gebruikt, moet u ervoor zorgen dat de gegevens typen die worden opgeslagen in de sessie status serialiseerbaar zijn.
> 
> 

## <a name="aspnet-session-state-options"></a>Opties voor de sessie status van ASP.NET

* In de geheugen sessie status provider: deze provider slaat de sessie status op in het geheugen. Het voor deel van het gebruik van deze provider is eenvoudig en snel. U kunt uw Web Apps echter niet schalen als u in de geheugen provider gebruikt, omdat deze niet is gedistribueerd.
* SQL Server-sessie status provider: deze provider slaat de sessie status op in SQL Server. Gebruik deze provider als u de sessie status wilt opslaan in permanente opslag. U kunt uw web-app schalen, maar het gebruik van SQL Server voor de sessie heeft invloed op de prestaties van uw web-app. U kunt deze provider ook gebruiken met een [OLTP-configuratie in het geheugen](/archive/blogs/sqlserverstorageengine/asp-net-session-state-with-sql-server-in-memory-oltp) om de prestaties te verbeteren.
* Gedistribueerd in de geheugen sessie status provider, zoals Azure cache voor redis-sessie status provider-deze provider biedt u het beste van beide werelden. Uw web-app kan een eenvoudige, snelle en schaal bare sessie status provider hebben. Omdat deze provider de sessie status opslaat in een cache, moet uw app rekening houden met alle kenmerken die zijn gekoppeld aan een gedistribueerd in de geheugen cache, zoals tijdelijke netwerk storingen. Zie voor aanbevolen procedures voor het gebruik van cache de [richt lijnen voor caching](/azure/architecture/best-practices/caching) van micro soft patronen & procedures [Azure Cloud Application Design and implementation guidance](https://github.com/mspnp/azure-guidance)(Engelstalig).

Zie [Aanbevolen procedures voor webontwikkeling (buil ding Real-World Cloud-apps met Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices)voor meer informatie over de sessie status en andere aanbevolen procedures.

## <a name="third-party-session-state-providers"></a>Sessie status providers van derden

* [NCache](https://www.alachisoft.com/ncache/session-index.html)
* [Apache Ignite](https://apacheignite-net.readme.io/docs/aspnet-session-state-caching)

## <a name="next-steps"></a>Volgende stappen

Bekijk de [ASP.net-uitvoer cache provider voor Azure cache voor redis](cache-aspnet-output-cache-provider.md).