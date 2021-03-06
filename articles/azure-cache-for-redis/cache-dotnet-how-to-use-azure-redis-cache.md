---
title: 'Quickstart: Azure Cache voor Redis in .NET Framework gebruiken'
description: In deze snelstartgids leert u hoe u vanuit uw .NET-apps toegang hebt tot Azure Cache voor Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp, mvc
ms.date: 06/18/2020
ms.openlocfilehash: effab14316c4a959f22467b9cc50984b6571da55
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872151"
---
# <a name="quickstart-use-azure-cache-for-redis-in-net-framework"></a>Quickstart: Azure Cache voor Redis in .NET Framework gebruiken

In deze quickstart neemt u Azure Cache voor Redis op in een .NET Framework-app voor toegang tot een veilige, toegewezen cache die toegankelijk is vanuit elke toepassing binnen Azure. U gebruikt met name de client [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) met C#-code in een .NET-console-app.

## <a name="skip-to-the-code-on-github"></a>Ga naar de code op GitHub

Als u direct naar de code wilt gaan, bekijkt u de [.NET Framework quickstart](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/dotnet) op GitHub.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://www.visualstudio.com/downloads/)
- [.NET Framework 4 of hoger](https://dotnet.microsoft.com/download/dotnet-framework) is vereist voor de client StackExchange.Redis.

## <a name="create-a-cache"></a>Een cache maken
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Maak op uw computer een bestand met de naam *CacheSecrets.config*. Sla het op een locatie op waar het niet wordt ingecheckt met de broncode van uw voorbeeldtoepassing. Voor deze snelstart bevindt het bestand *CacheSecrets.config* zich hier: *C:\AppSecrets\CacheSecrets.config*.

Bewerk het bestand *CacheSecrets.config* en voeg de volgende inhoud toe:

```xml
<appSettings>
    <add key="CacheConnection" value="<host-name>,abortConnect=false,ssl=true,allowAdmin=true,password=<access-key>"/>
</appSettings>
```

Vervang `<host-name>` door de hostnaam van uw cache.

Vervang `<access-key>` door de primaire sleutel voor uw cache.


## <a name="create-a-console-app"></a>Een console-app maken

Klik in Visual Studio op **File** > **New** > **Project**.

Selecteer **Console-app (.NET Framework)** en **Volgende** om uw app te configureren. Typ een **Projectnaam,** controleer **of .NET Framework 4.6.1** of hoger  is geselecteerd en klik vervolgens op Maken om een nieuwe consoletoepassing te maken.

<a name="configure-the-cache-clients"></a>

## <a name="configure-the-cache-client"></a>De cacheclient configureren

In deze sectie configureert u de consoletoepassing voor het gebruik van de client [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) voor .NET.

Klik in Visual Studio op **Tools** > **NuGet Package Manager** > **Package Manager Console** en voer de volgende opdracht uit vanuit het venster Package Manager Console.

```powershell
Install-Package StackExchange.Redis
```

Zodra de installatie is voltooid, kan de *StackExchange.Redis*-cacheclient met uw project worden gebruikt.


## <a name="connect-to-the-cache"></a>Verbinding maken met de cache

Open in Visual Studio het bestand *App.config* en voeg een kenmerk `appSettings` `file` toe dat verwijst naar het bestand *CacheSecrets.config*.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>

    <appSettings file="C:\AppSecrets\CacheSecrets.config"></appSettings>
</configuration>
```

Klik in Solution Explorer met de rechtermuisknop op **References** en klik op **Add a reference**. Voeg een verwijzing naar de assembly **System.Configuration** toe.

Voeg de volgende `using`-instructies toe aan *Program.cs*:

```csharp
using StackExchange.Redis;
using System.Configuration;
```

De verbinding met de Azure Redis-cache wordt beheerd door de klasse `ConnectionMultiplexer`. Deze klasse moet worden gedeeld en hergebruikt in uw clienttoepassing. Maak geen nieuwe verbinding voor elke bewerking. 

Sla referenties nooit op in broncode. Om dit voorbeeld overzichtelijk te houden, gebruik ik alleen een extern configuratiebestand met geheimen. Een betere benadering is om [Azure Key Vault met certificaten](/rest/api/keyvault/certificate-scenarios) te gebruiken.

Voeg in *Program.cs* de volgende leden toe aan de klasse `Program` van uw consoletoepassing:

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = CreateConnection();

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}

private static Lazy<ConnectionMultiplexer> CreateConnection()
{
    return new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });
}
```


Deze benadering om een exemplaar van `ConnectionMultiplexer` in uw toepassing te delen, gebruikt een statische eigenschap die een verbonden exemplaar retourneert. De code biedt een thread-veilige manier om slechts één verbonden exemplaar van `ConnectionMultiplexer` te initialiseren. `abortConnect` is ingesteld op false. Dat betekent dat de aanroep slaagt, zelfs als er geen verbinding is gemaakt met de Azure Cache voor Redis. Een belangrijke functie van `ConnectionMultiplexer` is dat deze de verbinding met de cache automatisch herstelt als het netwerkprobleem of de andere oorzaken zijn opgelost.

De waarde van de appSetting *CacheConnection* wordt gebruikt om vanuit Azure Portal te verwijzen naar de verbindingsreeks voor de cache als de wachtwoordparameter.

## <a name="handle-redisconnectionexception-and-socketexception-by-reconnecting"></a>RedisConnectionException en SocketException verwerken door opnieuw verbinding te maken

Een aanbevolen best practice bij het aanroepen van methoden op is om te proberen en uitzonderingen automatisch op te lossen door de verbinding te sluiten `ConnectionMultiplexer` en opnieuw tot stand te `RedisConnectionException` `SocketException` brengen.

Voeg de volgende `using`-instructies toe aan *Program.cs*:

```csharp
using System.Net.Sockets;
using System.Threading;
```

Voeg *in Program.cs* de volgende leden toe aan de `Program` klasse :

```csharp
private static long lastReconnectTicks = DateTimeOffset.MinValue.UtcTicks;
private static DateTimeOffset firstErrorTime = DateTimeOffset.MinValue;
private static DateTimeOffset previousErrorTime = DateTimeOffset.MinValue;

private static readonly object reconnectLock = new object();

// In general, let StackExchange.Redis handle most reconnects,
// so limit the frequency of how often ForceReconnect() will
// actually reconnect.
public static TimeSpan ReconnectMinFrequency => TimeSpan.FromSeconds(60);

// If errors continue for longer than the below threshold, then the
// multiplexer seems to not be reconnecting, so ForceReconnect() will
// re-create the multiplexer.
public static TimeSpan ReconnectErrorThreshold => TimeSpan.FromSeconds(30);

public static int RetryMaxAttempts => 5;

private static void CloseConnection(Lazy<ConnectionMultiplexer> oldConnection)
{
    if (oldConnection == null)
        return;

    try
    {
        oldConnection.Value.Close();
    }
    catch (Exception)
    {
        // Example error condition: if accessing oldConnection.Value causes a connection attempt and that fails.
    }
}

/// <summary>
/// Force a new ConnectionMultiplexer to be created.
/// NOTES:
///     1. Users of the ConnectionMultiplexer MUST handle ObjectDisposedExceptions, which can now happen as a result of calling ForceReconnect().
///     2. Don't call ForceReconnect for Timeouts, just for RedisConnectionExceptions or SocketExceptions.
///     3. Call this method every time you see a connection exception. The code will:
///         a. wait to reconnect for at least the "ReconnectErrorThreshold" time of repeated errors before actually reconnecting
///         b. not reconnect more frequently than configured in "ReconnectMinFrequency"
/// </summary>
public static void ForceReconnect()
{
    var utcNow = DateTimeOffset.UtcNow;
    long previousTicks = Interlocked.Read(ref lastReconnectTicks);
    var previousReconnectTime = new DateTimeOffset(previousTicks, TimeSpan.Zero);
    TimeSpan elapsedSinceLastReconnect = utcNow - previousReconnectTime;

    // If multiple threads call ForceReconnect at the same time, we only want to honor one of them.
    if (elapsedSinceLastReconnect < ReconnectMinFrequency)
        return;

    lock (reconnectLock)
    {
        utcNow = DateTimeOffset.UtcNow;
        elapsedSinceLastReconnect = utcNow - previousReconnectTime;

        if (firstErrorTime == DateTimeOffset.MinValue)
        {
            // We haven't seen an error since last reconnect, so set initial values.
            firstErrorTime = utcNow;
            previousErrorTime = utcNow;
            return;
        }

        if (elapsedSinceLastReconnect < ReconnectMinFrequency)
            return; // Some other thread made it through the check and the lock, so nothing to do.

        TimeSpan elapsedSinceFirstError = utcNow - firstErrorTime;
        TimeSpan elapsedSinceMostRecentError = utcNow - previousErrorTime;

        bool shouldReconnect =
            elapsedSinceFirstError >= ReconnectErrorThreshold // Make sure we gave the multiplexer enough time to reconnect on its own if it could.
            && elapsedSinceMostRecentError <= ReconnectErrorThreshold; // Make sure we aren't working on stale data (e.g. if there was a gap in errors, don't reconnect yet).

        // Update the previousErrorTime timestamp to be now (e.g. this reconnect request).
        previousErrorTime = utcNow;

        if (!shouldReconnect)
            return;

        firstErrorTime = DateTimeOffset.MinValue;
        previousErrorTime = DateTimeOffset.MinValue;

        Lazy<ConnectionMultiplexer> oldConnection = lazyConnection;
        CloseConnection(oldConnection);
        lazyConnection = CreateConnection();
        Interlocked.Exchange(ref lastReconnectTicks, utcNow.UtcTicks);
    }
}

// In real applications, consider using a framework such as
// Polly to make it easier to customize the retry approach.
private static T BasicRetry<T>(Func<T> func)
{
    int reconnectRetry = 0;
    int disposedRetry = 0;

    while (true)
    {
        try
        {
            return func();
        }
        catch (Exception ex) when (ex is RedisConnectionException || ex is SocketException)
        {
            reconnectRetry++;
            if (reconnectRetry > RetryMaxAttempts)
                throw;
            ForceReconnect();
        }
        catch (ObjectDisposedException)
        {
            disposedRetry++;
            if (disposedRetry > RetryMaxAttempts)
                throw;
        }
    }
}

public static IDatabase GetDatabase()
{
    return BasicRetry(() => Connection.GetDatabase());
}

public static System.Net.EndPoint[] GetEndPoints()
{
    return BasicRetry(() => Connection.GetEndPoints());
}

public static IServer GetServer(string host, int port)
{
    return BasicRetry(() => Connection.GetServer(host, port));
}
```

## <a name="executing-cache-commands"></a>Cache-opdrachten uitvoeren

Voeg de volgende code toe voor de procedure `Main` van de klasse `Program` voor uw consoletoepassing:

```csharp
static void Main(string[] args)
{
    IDatabase cache = GetDatabase();

    // Perform cache operations using the cache object...

    // Simple PING command
    string cacheCommand = "PING";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

    // Simple get and put of integral data types into the cache
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    cacheCommand = "SET Message \"Hello! The cache is working from a .NET console app!\"";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
    Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET console app!").ToString());

    // Demonstrate "SET Message" executed as expected...
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    // Get the client list, useful to see if connection list is growing...
    // Note that this requires allowAdmin=true in the connection string
    cacheCommand = "CLIENT LIST";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    var endpoint = (System.Net.DnsEndPoint)GetEndPoints()[0];
    IServer server = GetServer(endpoint.Host, endpoint.Port);
    ClientInfo[] clients = server.ClientList();

    Console.WriteLine("Cache response :");
    foreach (ClientInfo client in clients)
    {
        Console.WriteLine(client.Raw);
    }

    CloseConnection(lazyConnection);
}
```

Azure Cache voor Redis heeft een configureerbaar aantal databases (standaard 16) die kunnen worden gebruikt om de gegevens in Azure Cache voor Redis logisch te scheiden. De code maakt verbinding met de standaarddatabase, DB 0. Zie [Wat zijn Redis-databases?](cache-development-faq.md#what-are-redis-databases) en [Standaardconfiguratie voor Redis-server](cache-configure.md#default-redis-server-configuration) voor meer informatie.

Cache-items kunnen worden opgeslagen en opgehaald met behulp van de methoden `StringSet` en `StringGet`.

Redis slaat de meeste gegevens op als Redis-tekenreeksen, maar deze tekenreeksen kunnen veel soorten gegevens bevatten, waaronder geserialiseerde binaire gegevens die kunnen worden gebruikt voor het opslaan van .NET-objecten in de cache.

Druk op **Ctrl+F5** om de consoletoepassing te bouwen en uit te voeren.

In het onderstaande voorbeeld ziet u dat de `Message`-sleutel eerder een waarde in de cache had, die was ingesteld met behulp van de Redis Console in Azure Portal. De app heeft die waarde in de cache bijgewerkt. De app heeft ook de opdrachten `PING` en `CLIENT LIST` uitgevoerd.

![Gedeeltelijke consoletoepassing](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>Werken met .NET-objecten in de cache

Azure Cache voor Redis kan zowel .NET-objecten als oudere gegevenstypen opslaan in de cache. Voordat een .NET-object in de cache kan worden opgeslagen, moet het echter worden geserialiseerd. Deze serialisatie van het .NET-object is de verantwoordelijkheid van de ontwikkelaar van de toepassing. Het geeft de ontwikkelaar flexibiliteit bij de keuze van de serializer.

Een eenvoudige manier om objecten te serialiseren is met de serialisatiemethoden `JsonConvert` in [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/). Hiermee serialiseert u van en naar JSON. In deze sectie voegt u een .NET-object toe aan de cache.

Klik in Visual Studio op **Tools** > **NuGet Package Manager** > **Package Manager Console** en voer de volgende opdracht uit vanuit het venster Package Manager Console.

```powershell
Install-Package Newtonsoft.Json
```

Voeg de volgende `using`-instructie toe aan *Program.cs*:

```csharp
using Newtonsoft.Json;
```

Voeg de volgende `Employee`-klassedefinitie toe aan *Program.cs*:

```csharp
class Employee
{
    public string Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }

    public Employee(string employeeId, string name, int age)
    {
        Id = employeeId;
        Name = name;
        Age = age;
    }
}
```

Voeg onderaan procedure `Main()` in *Program.cs* en vóór de aanroep van `CloseConnection()` de volgende regels code toe om een geserialiseerd .NET-object op te slaan in de cache en op te halen:

```csharp
    // Store .NET object to cache
    Employee e007 = new Employee("007", "Davide Columbo", 100);
    Console.WriteLine("Cache response from storing Employee .NET object : " + 
    cache.StringSet("e007", JsonConvert.SerializeObject(e007)));

    // Retrieve .NET object from cache
    Employee e007FromCache = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e007"));
    Console.WriteLine("Deserialized Employee .NET object :\n");
    Console.WriteLine("\tEmployee.Name : " + e007FromCache.Name);
    Console.WriteLine("\tEmployee.Id   : " + e007FromCache.Id);
    Console.WriteLine("\tEmployee.Age  : " + e007FromCache.Age + "\n");
```

Druk op **Ctrl + F5** om de console-app te bouwen en uit te voeren en zo de serialisatie van .NET-objecten te testen. 

![Voltooide console-app](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>Resources opschonen

Als u verder wilt gaan met de volgende zelfstudie, kunt u de resources die in deze snelstart zijn gemaakt behouden en opnieuw gebruiken.

Als u niet verder wilt met de voorbeeldtoepassing uit de snelstart, kunt u de Azure-resources verwijderen die in deze snelstart zijn gemaakt om kosten te voorkomen. 

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle bijbehorende resources worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. Als u de resources voor het hosten van dit voorbeeld in een bestaande resourcegroep hebt gemaakt en deze groep ook resources bevat die u wilt behouden, kunt u elke resource afzonderlijk verwijderen via hun respectievelijke blade.
>

Meld u aan bij [Azure Portal](https://portal.azure.com) en klik op **Resourcegroepen**.

Typ de naam van de resourcegroep in het tekstvak **Filteren op naam...** . In de instructies voor dit artikel is een resourcegroep met de naam *TestResources* gebruikt. Klik in de resourcegroep in de lijst met resultaten op **...** en vervolgens op **Resourcegroep verwijderen**.

![Verwijderen](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-delete-resource-group.png)

U wordt gevraagd om het verwijderen van de resourcegroep te bevestigen. Typ de naam van de resourcegroep om te bevestigen en klik op **Verwijderen**.

Na enkele ogenblikken worden de resourcegroep en alle resources in de groep verwijderd.



<a name="next-steps"></a>

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u geleerd hoe u Azure Cache voor Redis gebruikt vanuit een .NET-toepassing. Ga verder met de volgende snelstart om Azure Cache voor Redis te gebruiken met een ASP.NET-web-app.

> [!div class="nextstepaction"]
> [Maak een ASP.NET-web-app die gebruikmaakt van Azure Cache voor Redis.](./cache-web-app-howto.md)

Wilt u uw clouduitgaven optimaliseren en geld besparen?

> [!div class="nextstepaction"]
> [Analyseer kosten met Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
