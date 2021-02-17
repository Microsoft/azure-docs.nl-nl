---
title: Gids voor probleem oplossing voor Azure lente-Cloud | Microsoft Docs
description: Gids voor probleem oplossing voor Azure lente-Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 9350b5dbcedd2773e94ef9d35cfe26f97fbd97bc
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571607"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>Veelvoorkomende problemen met Azure lente-Cloud oplossen

In dit artikel vindt u instructies voor het oplossen van problemen met Azure lente voor Cloud ontwikkeling. Zie [Veelgestelde vragen over Azure veer Cloud](spring-cloud-faq.md)voor meer informatie.

## <a name="availability-performance-and-application-issues"></a>Problemen met Beschik baarheid, prestaties en toepassingen

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>Mijn toepassing kan niet worden gestart (bijvoorbeeld omdat het eind punt niet kan worden verbonden, of het retourneert een 502 na een paar nieuwe pogingen)

De logboeken exporteren naar Azure Log Analytics. De tabel voor veer toepassings logboeken heeft de naam *AppPlatformLogsforSpring*. Zie [Logboeken en metrische gegevens analyseren met Diagnostische instellingen](diagnostic-services.md)voor meer informatie.

Het volgende fout bericht kan worden weer gegeven in de logboeken:

> "org. springframework. context. ApplicationContextException: kan de webserver niet starten"

Het bericht geeft een van de twee mogelijke problemen aan: 
* Een van de bonen of een van de afhankelijkheden ontbreekt.
* Een van de bean-eigenschappen ontbreekt of is ongeldig In dit geval wordt ' Java. lang. IllegalArgumentException ' waarschijnlijk weer gegeven.

Service bindingen kunnen ook start fouten van toepassingen veroorzaken. Als u een query wilt uitvoeren voor de logboeken, gebruikt u tref woorden die zijn gerelateerd aan de gebonden Services. Stel bijvoorbeeld dat uw toepassing een binding heeft met een MySQL-exemplaar dat is ingesteld op lokale systeem tijd. Als de toepassing niet kan worden gestart, wordt het volgende fout bericht weer gegeven in het logboek:

> ' Java. SQL. SQLException: de tijdzone waarde ' Coordinated Universal Time ' van de server wordt niet herkend of bevat meer dan een tijd zone. '

Als u deze fout wilt oplossen, gaat u naar de `server parameters` van uw MySQL-exemplaar en wijzigt u de `time_zone` waarde van *systeem* in *+ 0:00*.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>Mijn toepassing is vastgelopen of heeft een onverwachte fout gegenereerd

Wanneer u fouten opspoort in de toepassing, moet u eerst de status en detectie status van de toepassing controleren. Als u dit wilt doen, gaat u naar _app-beheer_ in de Azure Portal om ervoor te zorgen dat de statussen van alle toepassingen worden _uitgevoerd_ _._

* Als de status _actief_ is, maar de detectie status niet _actief is_, gaat u naar de sectie [mijn toepassing kan niet worden geregistreerd](#my-application-cant-be-registered) .

* Als de detectie status _actief_ is, gaat u naar metrische gegevens om de status van de toepassing te controleren. Inspecteer de volgende metrische gegevens:


  - `TomcatErrorCount` (_Tomcat. Global. error_): alle uitzonde ringen voor de lente toepassing worden hier geteld. Als dit aantal groot is, gaat u naar Azure Log Analytics om uw toepassings logboeken te controleren.

  - `AppMemoryMax` (_JVM. Memory. Max_): de maximale hoeveelheid geheugen die beschikbaar is voor de toepassing. Het bedrag kan niet worden gedefinieerd, of het kan in de loop van de tijd worden gewijzigd als het is gedefinieerd. Als deze is gedefinieerd, is de hoeveelheid gebruikt en toegewezen geheugen altijd kleiner dan of gelijk aan de maximum waarde. Een geheugen toewijzing kan echter mislukken met een `OutOfMemoryError` bericht als de toewijzing probeert het gebruikte geheugen te verg Roten, zoals het gebruik van *> vastgelegd*, zelfs als het *wordt gebruikt <= Max* is ingesteld op True. Probeer in een dergelijke situatie de maximale Heap-grootte te verhogen met behulp van de `-Xmx` para meter.

  - `AppMemoryUsed` (_JVM. Memory. used_): de hoeveelheid geheugen in bytes die momenteel door de toepassing wordt gebruikt. Voor een normale toepassing voor het laden van Java vormt deze metrische reeks een *zaagtand* patroon, waarbij het geheugen gebruik gestaag toeneemt en afneemt in kleine stappen en plotseling een hoop zou afnemen en het patroon wordt herhaald. Deze metrische serie wordt veroorzaakt door garbagecollection in Java Virtual Machine, waarbij verzamelings acties voor komen op het zaagtand patroon.
    
    Deze metrische gegevens zijn belang rijk voor het identificeren van geheugen problemen, zoals:
    * Een geheugen explosie aan het begin.
    * De toewijzing van piek geheugen voor een specifiek logisch pad.
    * Geleidelijke geheugen lekkage.
  Zie [metrische](spring-cloud-concept-metrics.md)gegevens voor meer informatie.
  
* Als de toepassing niet kan worden gestart, controleert u of de toepassing geldige JVM-para meters heeft. Als het JVM-geheugen te hoog is ingesteld, wordt het volgende fout bericht weer gegeven in de logboeken:

  >de vereiste hoeveelheid geheugen 2728741K is groter dan 2000M beschikbaar voor toewijzing.



Zie aan de [slag met log Analytics in azure monitor](../azure-monitor/logs/log-analytics-tutorial.md)voor meer informatie over Azure log Analytics.

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Mijn toepassing heeft een hoog CPU-gebruik of een hoog geheugengebruik

Als uw toepassing een hoog CPU-of geheugen gebruik ondervindt, is een van de twee dingen waar:
* Alle app-exemplaren hebben een hoog CPU-of geheugen gebruik.
* Enkele van de app-exemplaren hebben een hoog CPU-of geheugen gebruik.

Ga als volgt te werk om te controleren welke situatie van toepassing is:

1. Ga naar **metrische gegevens** en selecteer vervolgens het **CPU-gebruiks percentage** van de service of het **gebruikte service geheugen**.
2. Voeg een **app =** filter toe om op te geven welke toepassing u wilt bewaken.
3. Splits de metrische gegevens op **exemplaar**.

Als *alle instanties* een hoog CPU-of geheugen gebruik ondervinden, moet u de toepassing uitschalen of het CPU-of geheugen gebruik omhoog schalen. Zie [zelf studie: een toepassing schalen in azure veer Cloud](spring-cloud-tutorial-scale-manual.md)voor meer informatie.

Als *sommige instanties een* hoog CPU-of geheugen gebruik ondervinden, controleert u de status van het exemplaar en de detectie status ervan.

Zie [metrische gegevens voor Azure lente-Cloud](spring-cloud-concept-metrics.md)voor meer informatie.

Als alle exemplaren actief zijn, gaat u naar Azure Log Analytics om een query uit te voeren op uw toepassings logboeken en de code logica te controleren. Dit helpt u te zien of een van deze van invloed kan zijn op de schaal partitionering. Zie [Logboeken en metrische gegevens analyseren met Diagnostische instellingen](diagnostic-services.md)voor meer informatie.

Zie aan de [slag met log Analytics in azure monitor](../azure-monitor/logs/log-analytics-tutorial.md)voor meer informatie over Azure log Analytics. Query's uitvoeren op de logboeken met behulp van de [Kusto-query taal](/azure/kusto/query/).

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Controle lijst voor het implementeren van uw lente toepassing in azure veer Cloud

Voordat u de toepassing uitschakelt, moet u controleren of deze voldoet aan de volgende criteria:

* De toepassing kan lokaal worden uitgevoerd met de opgegeven Java runtime-versie.
* De omgevings configuratie (CPU/RAM/exemplaren) voldoet aan de minimum vereisten die zijn ingesteld door de provider van de toepassing.
* De configuratie-items hebben hun verwachte waarden. Zie [config server](spring-cloud-tutorial-config-server.md)(Engelstalig) voor meer informatie.
* De omgevings variabelen hebben hun verwachte waarden.
* De JVM-para meters hebben hun verwachte waarden.
* U wordt aangeraden de Inge sloten _Configuratie server_ -en _veer service register_ services uit het toepassings pakket uit te scha kelen of te verwijderen.
* Als Azure-resources moeten worden gebonden via _servicebinding_, moet u ervoor zorgen dat de doelresources actief zijn.

## <a name="configuration-and-management"></a>Configuratie en beheer

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Er is een probleem opgetreden bij het maken van een Azure lente-Cloud service-exemplaar

Wanneer u een Azure lente-Cloud service-exemplaar instelt met behulp van de Azure Portal, voert Azure lente Cloud de validatie voor u uit.

Maar als u probeert het Azure lente-Cloud service-exemplaar in te stellen met behulp van de [Azure cli](/cli/azure/get-started-with-azure-cli) of de [Azure Resource Manager-sjabloon](../azure-resource-manager/index.yml), controleert u het volgende:

* Het abonnement is actief.
* De locatie wordt [ondersteund](spring-cloud-faq.md) door de Azure lente-Cloud.
* De resource groep voor het exemplaar is al gemaakt.
* De resource naam voldoet aan de naamgevings regel. De naam mag alleen kleine letters, cijfers en afbreek streepjes bevatten. Het eerste teken moet een letter zijn. Het eerste teken moet een letter of cijfer zijn. De waarde moet tussen 2 en 32 tekens bevatten.

Als u het Azure lente-Cloud service-exemplaar wilt instellen met behulp van de Resource Manager-sjabloon, raadpleegt u eerst [de structuur en de syntaxis van Azure Resource Manager sjablonen](../azure-resource-manager/templates/template-syntax.md).

De naam van het Azure lente-Cloud service-exemplaar wordt gebruikt voor het aanvragen van een subdomeinnaam onder `azureapps.io` , zodat de installatie mislukt als de naam in conflict is met een bestaande. Mogelijk vindt u meer informatie in de activiteiten Logboeken.

### <a name="i-cant-deploy-a-net-core-app"></a>Ik kan geen .NET core-app implementeren

U kunt een *zip* -bestand voor een .net core Steeltoe-app niet uploaden met behulp van de Azure portal of de Resource Manager-sjabloon.

Wanneer u uw toepassings pakket implementeert met behulp van de [Azure cli](/cli/azure/get-started-with-azure-cli), pollt de Azure cli regel matig de voortgang van de implementatie en wordt het resultaat weer gegeven in het einde.

Zorg ervoor dat uw toepassing is ingepakt in de juiste *. zip* -bestands indeling. Als de app niet goed is verpakt, reageert het proces niet meer of ontvangt u een fout melding.

### <a name="i-cant-deploy-a-jar-package"></a>Ik kan geen JAR-pakket implementeren

U kunt het/source-pakket van Java Archive File (JAR) niet uploaden met behulp van de Azure Portal of de Resource Manager-sjabloon.

Wanneer u uw toepassings pakket implementeert met behulp van de [Azure cli](/cli/azure/get-started-with-azure-cli), pollt de Azure cli regel matig de voortgang van de implementatie en wordt het resultaat weer gegeven in het einde.

Als de controles worden onderbroken, kunt u nog steeds de volgende opdrachten gebruiken om de implementatielogboeken op te halen:

`az spring-cloud app show-deploy-log -n <app-name>`

Zorg ervoor dat uw toepassing is verpakt in de juiste [uitvoer bare jar-indeling](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html). Als deze niet correct is verpakt, wordt een fout bericht van de volgende strekking weer gegeven:

> "Fout: ongeldig of beschadigd jarfile/jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>Ik kan een bron pakket niet implementeren

U kunt het JAR/Bron pakket niet uploaden met behulp van de Azure Portal of de Resource Manager-sjabloon.

Wanneer u uw toepassings pakket implementeert met behulp van de [Azure cli](/cli/azure/get-started-with-azure-cli), pollt de Azure cli regel matig de voortgang van de implementatie en wordt het resultaat weer gegeven in het einde.

Als de controles worden onderbroken, kunt u nog steeds de volgende opdrachten gebruiken om de compilatie- en implementatielogboeken op te halen:

`az spring-cloud app show-deploy-log -n <app-name>`

Houd er echter rekening mee dat één exemplaar van de Azure veer-Cloud service per keer slechts één bouw taak voor één bron pakket kan activeren. Zie [een toepassing implementeren](spring-cloud-quickstart.md) en [een faserings omgeving instellen in azure lente-Cloud](spring-cloud-howto-staging-environment.md)voor meer informatie.

### <a name="my-application-cant-be-registered"></a>Mijn toepassing kan niet worden geregistreerd

In de meeste gevallen treedt deze situatie op wanneer *vereiste afhankelijkheden* en *service detectie* niet correct zijn geconfigureerd in uw pom-bestand (project object model). Zodra de configuratie is geconfigureerd, wordt het ingebouwde eind punt van het service register server geïnjecteerd als een omgevings variabele met uw toepassing. Toepassingen registreren zich vervolgens bij de service register server en ontdekken andere afhankelijke micro Services.

Wacht ten minste twee minuten voordat een nieuw geregistreerd exemplaar het verkeer ontvangt.

Als u een bestaande tot een cloud gebaseerde oplossing naar Azure migreert, moet u ervoor zorgen dat uw _REGI ster_ -en _Configuratie server_ -exemplaren van ad-hoc service worden verwijderd (of uitgeschakeld) om te voor komen dat er conflicten ontstaan met de beheerde exemplaren van Azure lente Cloud.

U kunt ook de logboeken van de _service register_ -client in azure log Analytics controleren. Zie [Logboeken en metrische gegevens analyseren met Diagnostische instellingen](diagnostic-services.md) voor meer informatie.

Zie aan de [slag met log Analytics in azure monitor](../azure-monitor/logs/log-analytics-tutorial.md)voor meer informatie over Azure log Analytics. Query's uitvoeren op de logboeken met behulp van de [Kusto-query taal](/azure/kusto/query/).

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Ik wil de omgevings variabelen van mijn toepassing controleren

Met omgevings variabelen wordt het Azure lente Cloud-Framework geïnformeerd, waardoor Azure begrijpt waar en hoe de services kunnen worden geconfigureerd waaruit uw toepassing is opgebouwd. Zorg ervoor dat uw omgevings variabelen correct zijn om mogelijke problemen op te lossen.  U kunt het eind punt voor de Spring boot-klep gebruiken om uw omgevings variabelen te controleren.  

> [!WARNING]
> Met deze procedure worden de omgevings variabelen weer gegeven met behulp van het eind punt van de test.  Ga niet verder als uw test eindpunt openbaar toegankelijk is of als u een domein naam aan uw toepassing hebt toegewezen.

1. Ga naar `https://<your application test endpoint>/actuator/health`.  
    - Een antwoord dat lijkt op `{"status":"UP"}` dat het eind punt is ingeschakeld.
    - Als het antwoord negatief is, neemt u de volgende afhankelijkheden op in uw *POM.xml* -bestand:

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Als het eind punt voor de installatie van de veer boot is ingeschakeld, gaat u naar de Azure Portal en zoekt u naar de pagina configuratie van uw toepassing.  Voeg een omgevings variabele met de naam `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` en de waarde toe `*` . 

1. Start de toepassing opnieuw.

1. Ga naar `https://<your application test endpoint>/actuator/env` en controleer het antwoord.  Dit ziet er als volgt uit:

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

Zoek naar het onderliggende knoop punt met de naam `systemEnvironment` .  Dit knoop punt bevat de omgevings variabelen van uw toepassing.

> [!IMPORTANT]
> Vergeet niet om de bloot stelling van uw omgevings variabelen terug te draaien voordat u uw toepassing toegankelijk maakt voor het publiek.  Ga naar de Azure Portal, zoek de configuratie pagina van uw toepassing en verwijder deze omgevings variabele:  `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` .

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>Ik kan geen metrische gegevens of Logboeken vinden voor mijn toepassing

Ga naar **app-beheer** om te controleren of de statussen _van_ de toepassing _actief_ zijn.

Controleer of weer _JMX_ is ingeschakeld in uw toepassings pakket. Deze functie kan worden ingeschakeld met de configuratie-eigenschap `spring.jmx.enabled=true` .  

Controleer of de `spring-boot-actuator` afhankelijkheid is ingeschakeld in uw toepassings pakket en of deze correct wordt opgestart.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Als uw toepassings logboeken kunnen worden gearchiveerd in een opslag account, maar niet naar Azure Log Analytics worden verzonden, controleert u of u [uw werk ruimte op de juiste wijze hebt ingesteld](../azure-monitor/logs/quick-create-workspace.md). Als u een gratis laag van Azure Log Analytics gebruikt, moet u er rekening mee houden dat [de gratis laag geen Sla (Service Level Agreement) biedt](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).

## <a name="next-steps"></a>Volgende stappen

* [Zelf diagnose en probleem oplossing in azure lente Cloud](spring-cloud-howto-self-diagnose-solve.md)