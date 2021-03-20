---
title: Verwijder TLS 1,0 en 1,1 van gebruik met Azure cache voor redis
description: Meer informatie over het verwijderen van TLS 1,0 en 1,1 vanuit uw toepassing tijdens het communiceren met Azure cache voor redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: fd0e6f893d152259c46ff06e9ec20af54395c5e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994380"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Verwijder TLS 1,0 en 1,1 van gebruik met Azure cache voor redis

Er is sprake van een brede push naar het exclusieve gebruik van Transport Layer Security (TLS) versie 1,2 of hoger. TLS-versies 1,0 en 1,1 zijn bekend als vatbaar voor aanvallen zoals BEAST en POODLE, en om andere veelvoorkomende zwakke plekken en bloot stellingen (CVE) te hebben. Ze bieden ook geen ondersteuning voor de moderne versleutelings methoden en coderings suites die worden aanbevolen door PCI-nalevings normen (Payment Card Industry). In deze [blog van TLS-beveiliging](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) worden enkele van deze beveiligings problemen uitvoeriger beschreven.

Als onderdeel van deze inspanning brengen we de volgende wijzigingen aan in azure cache voor redis:

* **Fase 1:** We configureren de standaard minimum versie van TLS op 1,2 voor nieuw gemaakte cache-exemplaren (voorheen was dit TLS 1,0). Bestaande cache-exemplaren worden op dit moment niet bijgewerkt. U kunt nog steeds de Azure Portal of andere beheer-Api's gebruiken om [de minimale TLS-versie te wijzigen](cache-configure.md#access-ports) in 1,0 of 1,1 voor achterwaartse compatibiliteit.
* **Fase 2:** Het ondersteunen van TLS 1,1 en TLS 1,0 wordt gestopt. Na deze wijziging moet uw toepassing TLS 1,2 of hoger gebruiken om met uw cache te communiceren. De Azure-cache voor de redis-service is naar verwachting beschikbaar tijdens de migratie zodat alleen TLS 1,2 of hoger wordt ondersteund.

  > [!NOTE]
  > Fase 2 is voorlopig gepland om te beginnen met niet eerder dan 31 december 2020. We raden u echter ten zeerste aan om deze wijziging nu te plannen en proactief bij te werken voor het ondersteunen van TLS 1,2 of hoger. 
  >

Als onderdeel van deze wijziging wordt ook ondersteuning voor oudere coderings-suites verwijderd die niet veilig zijn. Onze ondersteunde coderings-suites worden beperkt tot de volgende suites wanneer de cache is geconfigureerd met een minimum van TLS 1,2:

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

Dit artikel bevat algemene richt lijnen over het detecteren van afhankelijkheden voor deze eerdere TLS-versies en het verwijderen van deze toepassingen uit uw toepassing.

De datums waarop deze wijzigingen van kracht worden, zijn:

| Cloud                | Begin datum fase 1 | Begin datum fase 2         |
|----------------------|--------------------|----------------------------|
| Azure (globaal)       |  13 januari 2020  | Uitgesteld vanwege COVID-19  |
| Azure Government     |  13 maart 2020    | Uitgesteld vanwege COVID-19  |
| Azure Duitsland        |  13 maart 2020    | Uitgesteld vanwege COVID-19  |
| Azure China 21Vianet |  13 maart 2020    | Uitgesteld vanwege COVID-19  |

> [!NOTE]
> Fase 2 is voorlopig gepland om te beginnen met niet eerder dan 31 december 2020. Dit artikel wordt bijgewerkt wanneer specifieke datums zijn ingesteld.
>

## <a name="check-whether-your-application-is-already-compliant"></a>Controleren of uw toepassing al compatibel is

De eenvoudigste manier om erachter te komen of uw toepassing werkt met TLS 1,2 is door de waarde van de **minimale TLS-versie** in te stellen op TLS 1,2 op een test-of faserings cache en vervolgens tests uit te voeren. De instelling **minimale TLS-versie** bevindt zich in de [Geavanceerde instellingen](cache-configure.md#advanced-settings) van uw cache-exemplaar in het Azure Portal.  Als de toepassing blijft functioneren zoals verwacht na deze wijziging, is deze waarschijnlijk compatibel. Mogelijk moet u de redis-client bibliotheek die door uw toepassing wordt gebruikt, configureren om TLS 1,2 in te scha kelen om verbinding te maken met Azure cache voor redis.

## <a name="configure-your-application-to-use-tls-12"></a>Uw toepassing configureren voor het gebruik van TLS 1,2

De meeste toepassingen gebruiken redis-client bibliotheken om communicatie met de caches af te handelen. Hier vindt u instructies voor het configureren van een aantal populaire client Bibliotheken, in verschillende programmeer talen en frameworks, voor het gebruik van TLS 1,2.

### <a name="net-framework"></a>.NET Framework

Redis .NET-clients gebruiken standaard de oudste TLS-versie op .NET Framework 4.5.2 of eerder en gebruiken de meest recente versie van TLS op .NET Framework 4,6 of hoger. Als u een oudere versie van .NET Framework gebruikt, kunt u TLS 1,2 hand matig inschakelen:

* **Stack Exchange. redis:** Set `ssl=true` en `sslprotocols=tls12` in de Connection String.
* **ServiceStack. redis:** Volg de instructies voor [ServiceStack. redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) en hiervoor is ServiceStack. redis v 5.6 Mini maal vereist.

### <a name="net-core"></a>.NET Core

Redis .NET core-clients standaard ingesteld op de standaard TLS-versie van het besturings systeem, wat duidelijk afhankelijk is van het besturings systeem zelf. 

Afhankelijk van de versie van het besturings systeem en eventuele patches die zijn toegepast, kan de meest efficiënte standaard-TLS-versie verschillen. Hoewel er één bron van informatie hierover is, is [hier](/dotnet/framework/network-programming/tls#support-for-tls-12) een artikel voor Windows. 

Als u echter een oud besturings systeem gebruikt of gewoon wilt, kunt u het beste de voor Keurs-TLS-versie hand matig configureren via de client.


### <a name="java"></a>Java

Redis Java-clients gebruiken TLS 1,0 op Java versie 6 of lager. Jedis, sla en Redisson kunnen geen verbinding maken met Azure cache voor redis als TLS 1,0 is uitgeschakeld in de cache. Voer een upgrade uit voor uw Java Framework om nieuwe TLS-versies te gebruiken.

Redis-clients gebruiken voor Java 7 standaard geen TLS 1,2, maar kunnen hiervoor worden geconfigureerd. Met jedis kunt u de onderliggende TLS-instellingen opgeven met het volgende code fragment:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

De sla-en Redisson-clients bieden nog geen ondersteuning voor het opgeven van de TLS-versie, zodat ze worden onderbroken als de cache alleen TLS 1,2-verbindingen accepteert. Er worden oplossingen voor deze clients beoordeeld, dus Raadpleeg deze pakketten voor een bijgewerkte versie met deze ondersteuning.

In Java 8 wordt TLS 1,2 standaard gebruikt en hoeft in de meeste gevallen geen updates te worden vereist voor uw client configuratie. Test uw toepassing om veilig te zijn.

### <a name="nodejs"></a>Node.js

Het knoop punt redis en IORedis gebruiken standaard TLS 1,2.

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis
 
* Eerdere versies dan PHP 7: Predis ondersteunt alleen TLS 1,0. Deze versies werken niet met TLS 1,2. u moet een upgrade uitvoeren om TLS 1,2 te gebruiken.
 
* PHP 7,0 naar PHP 7.2.1: Predis gebruikt standaard alleen TLS 1,0 of 1,1. U kunt de volgende tijdelijke oplossing gebruiken om TLS 1,2 te gebruiken. Geef TLS 1,2 op wanneer u het client exemplaar maakt:

  ``` PHP
  $redis=newPredis\Client([
      'scheme'=>'tls',
      'host'=>'host',
      'port'=>6380,
      'password'=>'password',
      'ssl'=>[
          'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
      ],
  ]);
  ```

* PHP 7,3 en hoger: Predis maakt gebruik van de meest recente versie van TLS.

#### <a name="phpredis"></a>PhpRedis

PhpRedis biedt geen ondersteuning voor TLS op een PHP-versie.

### <a name="python"></a>Python

Redis-py maakt standaard gebruik van TLS 1,2.

### <a name="go"></a>GO

Redigo maakt standaard gebruik van TLS 1,2.

## <a name="additional-information"></a>Aanvullende informatie

- [Azure-cache configureren voor redis](cache-configure.md)