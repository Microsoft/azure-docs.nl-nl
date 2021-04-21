---
title: Java-apps configureren
description: Meer informatie over het configureren van Java-apps om te worden uitgevoerd op Azure App Service. In dit artikel worden de meest algemene configuratietaken beschreven.
keywords: azure app service, web-app, windows, oss, java, tomcat, jboss
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
ms.openlocfilehash: 134ac04c4f6fb5f0e38a868adc735fc816fbc875
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829508"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Een Java-app voor Azure App Service configureren

Azure App Service kunnen Java-ontwikkelaars snel hun Java SE-, Tomcat- en JBoss EAP-webtoepassingen bouwen, implementeren en schalen in een volledig beheerde service. Implementeer toepassingen met Maven-invoegtoepassingen, vanaf de opdrachtregel of in editors zoals IntelliJ, Eclipse of Visual Studio Code.

Deze handleiding bevat belangrijke concepten en instructies voor Java-ontwikkelaars die App Service. Als u dit nog nooit Azure App Service, moet u eerst de [Java-quickstart](quickstart-java.md) doorlezen. Algemene vragen over het gebruik App Service die niet specifiek zijn voor Java-ontwikkeling, worden beantwoord in [de veelgestelde App Service .](faq-configuration-and-management.md)

## <a name="deploying-your-app"></a>Uw app implementeren

U kunt de [Azure Web App Plugin voor Maven gebruiken om](https://github.com/microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) uw .war- of .jar-bestanden te implementeren. Implementatie met populaire IDE's wordt ook ondersteund met [de Azure-toolkit voor IntelliJ](/azure/developer/java/toolkit-for-intellij/) of [Azure-toolkit voor Eclipse](/azure/developer/java/toolkit-for-eclipse).

Anders is uw implementatiemethode afhankelijk van uw archieftype:

### <a name="java-se"></a>Java SE

Als u .jar-bestanden wilt implementeren in Java SE, gebruikt `/api/zipdeploy/` u het eindpunt van de Kudu-site. Raadpleeg deze documentatie voor meer informatie [over deze API.](./deploy-zip.md#rest) 

> [!NOTE]
>  Uw JAR-toepassing moet een naam hebben `app.jar` App Service om uw toepassing te identificeren en uit te voeren. De naam van uw toepassing wordt tijdens de implementatie automatisch gewijzigd in de Maven-invoegtoepassing (hierboven genoemd). Als u de naam van uw JAR niet wilt wijzigen in *app.jar,* kunt u een shellscript uploaden met de opdracht om uw JAR-app uit te voeren. Plak het absolute pad naar dit script in het [tekstvak Opstartbestand](faq-app-service-linux.md#built-in-images) in de sectie Configuratie van de portal. Het opstartscript kan niet worden uitgevoerd vanuit de map waarin deze is geplaatst. Gebruik daarom altijd absolute paden om te verwijzen naar bestanden in het opstartscript (bijvoorbeeld: `java -jar /home/myapp/myapp.jar`).

### <a name="tomcat"></a>Tomcat

Als u .war-bestanden wilt implementeren in Tomcat, gebruikt u `/api/wardeploy/` het eindpunt om uw archiefbestand te plaatsen. Raadpleeg deze documentatie voor meer informatie [over deze API.](./deploy-zip.md#deploy-war-file)

::: zone pivot="platform-linux"

### <a name="jboss-eap"></a>JBoss EAP

Als u .war-bestanden wilt implementeren in JBoss, gebruikt u `/api/wardeploy/` het eindpunt om uw archiefbestand te plaatsen. Raadpleeg deze documentatie voor meer informatie [over deze API.](./deploy-zip.md#deploy-war-file)

Gebruik FTP om .ear-bestanden [te implementeren.](deploy-ftp.md)

::: zone-end

Implementeer uw .war- of .jar-naam niet met FTP. Het FTP-hulpprogramma is ontworpen om opstartscripts, afhankelijkheden of andere runtimebestanden te uploaden. Het is niet de optimale keuze voor het implementeren van web-apps.

## <a name="logging-and-debugging-apps"></a>Logboekregistratie en debuggen van apps

Prestatierapporten, verkeersvisualisaties en statuscontroles zijn beschikbaar voor elke app via de Azure Portal. Zie overzicht van diagnostische [Azure App Service voor meer informatie.](overview-diagnostics.md)

### <a name="stream-diagnostic-logs"></a>Diagnostische logboeken streamen

::: zone pivot="platform-windows"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end
::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

Zie Stream-logboeken in Cloud Shell voor [meer Cloud Shell.](troubleshoot-diagnostic-logs.md#in-cloud-shell)

::: zone pivot="platform-linux"

### <a name="ssh-console-access"></a>Toegang tot SSH-console

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="troubleshooting-tools"></a>Hulpprogramma's voor probleemoplossing

De ingebouwde Java-installatie afbeeldingen zijn gebaseerd op het Alpine Linux-besturingssysteem. [](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) Gebruik pakketbeheer `apk` om hulpprogramma's of opdrachten voor probleemoplossing te installeren.

::: zone-end

### <a name="flight-recorder"></a>Flight Recorder

Alle Java-runtimes op App Service azul JVM's worden bij de Zulu Flight Recorder gebruikt. U kunt dit gebruiken om JVM-, systeem- en toepassingsgebeurtenissen vast te registreren en problemen in uw Java-toepassingen op te lossen.

::: zone pivot="platform-windows"

#### <a name="timed-recording"></a>Getijde opname

Als u een getijde opname wilt maken, hebt u de PID (proces-id) van de Java-toepassing nodig. Als u de PID wilt vinden, opent u een browser naar de SCM-site van uw web-app op https://<your-site-name>.scm.azurewebsites.net/ProcessExplorer/. Op deze pagina worden de processen weergegeven die worden uitgevoerd in uw web-app. Zoek het proces met de naam 'java' in de tabel en kopieer de bijbehorende PID (proces-id).

Open vervolgens de Console **voor foutopsporing** in de bovenste werkbalk van de SCM-site en voer de volgende opdracht uit. Vervang `<pid>` door de proces-id die u eerder hebt gekopieerd. Met deze opdracht start u een profiler-opname van 30 seconden van uw Java-toepassing en genereert u een bestand met de naam `timed_recording_example.jfr` in de `D:\home` map .

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

::: zone-end
::: zone pivot="platform-linux"

SSH in uw App Service en voer de opdracht uit om een lijst weer te geven met `jcmd` alle Java-processen die worden uitgevoerd. Naast jcmd zelf ziet u dat uw Java-toepassing wordt uitgevoerd met een proces-id-nummer (pid).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Voer de onderstaande opdracht uit om een opname van 30 seconden van de JVM te starten. Hiermee profileren we de JVM en maakt u een JFR-bestand met de *naam jfr_example.jfr* in de basismap. (Vervang 116 door de pid van uw Java-app.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Tijdens het interval van 30 seconden kunt u controleren of de opname plaatsvindt door uit te laten. `jcmd 116 JFR.check` Hiermee worden alle opnamen voor het opgegeven Java-proces weer gegeven.

#### <a name="continuous-recording"></a>Continue opname

U kunt Zulu Flight Recorder gebruiken om uw Java-toepassing continu te profileren met minimale invloed op de runtimeprestaties[(bron).](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf) Voer de volgende Azure CLI-opdracht uit om een app-instelling met de naam JAVA_OPTS de benodigde configuratie te maken. De inhoud van de JAVA_OPTS app-instelling wordt doorgegeven aan de `java` opdracht wanneer uw app wordt gestart.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Zodra de opname is gestart, kunt u de huidige opnamegegevens op elk moment dumpen met behulp van de `JFR.dump` opdracht .

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

::: zone-end

#### <a name="analyze-jfr-files"></a>Bestanden `.jfr` analyseren

Gebruik [FTPS om](deploy-ftp.md) uw JFR-bestand naar uw lokale computer te downloaden. Als u het JFR-bestand wilt analyseren, downloadt en installeert [u Zulu Mission Control.](https://www.azul.com/products/zulu-mission-control/) Zie de [Azul-documentatie](https://docs.azul.com/zmc/) en de installatie-instructies voor instructies voor Zulu Mission [Control.](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)

### <a name="app-logging"></a>App-logboekregistratie

::: zone pivot="platform-windows"

Schakel [](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) logboekregistratie van toepassingen via de Azure Portal of [Azure CLI](/cli/azure/webapp/log#az_webapp_log_config) in om App Service te configureren voor het schrijven van de standaard console-uitvoer van uw toepassing en standaard consolefoutstromen naar het lokale bestandssysteem of Azure Blob Storage. Logboekregistratie naar de lokale App Service bestandssysteem exemplaar is uitgeschakeld 12 uur nadat deze is geconfigureerd. Als u een langere bewaarperiode nodig hebt, configureert u de toepassing om uitvoer naar een Blob Storage-container te schrijven. De logboeken van uw Java- en Tomcat-app vindt u in de *map /home/LogFiles/Application/.*

::: zone-end
::: zone pivot="platform-linux"

Schakel [](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) logboekregistratie van toepassingen via de Azure Portal of [Azure CLI](/cli/azure/webapp/log#az_webapp_log_config) in om App Service te configureren voor het schrijven van de standaard console-uitvoer van uw toepassing en standaard consolefoutstromen naar het lokale bestandssysteem of Azure Blob Storage. Als u een langere bewaarperiode nodig hebt, configureert u de toepassing om uitvoer naar een Blob Storage-container te schrijven. De logboeken van uw Java- en Tomcat-app vindt u in de *map /home/LogFiles/Application/.*

Azure Blob Storage logboekregistratie voor linux-App Services kunnen alleen worden geconfigureerd met behulp [van Azure Monitor (preview)](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview) 

::: zone-end

Als uw toepassing [gebruikmaakt van Logback](https://logback.qos.ch/) of [Log4j](https://logging.apache.org/log4j) voor tracering, kunt u deze traceringen doorsturen voor controle naar Azure-toepassing Insights met behulp van de configuratie-instructies voor het logboekregistratie-framework in Java-traceerlogboeken [verkennen in Application Insights](../azure-monitor/app/java-trace-logs.md).

## <a name="customization-and-tuning"></a>Aanpassen en afstemmen

Azure App Service linux biedt ondersteuning voor out-of-the-box afstemming en aanpassing via de Azure Portal en CLI. Lees de volgende artikelen voor niet-Java-specifieke web-app-configuratie:

- [App-instellingen configureren](configure-common.md#configure-app-settings)
- [Een aangepast domein instellen](app-service-web-tutorial-custom-domain.md)
- [SSL-bindingen configureren](configure-ssl-bindings.md)
- [Een CDN toevoegen](../cdn/cdn-add-to-web-app.md)
- [De Kudu-site configureren](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)


### <a name="set-java-runtime-options"></a>Opties voor Java-runtime instellen

Als u toegewezen geheugen of andere JVM-runtimeopties wilt instellen, maakt u een [app-instelling](configure-common.md#configure-app-settings) met de `JAVA_OPTS` naam met de opties. App Service deze instelling door als een omgevingsvariabele aan de Java-runtime wanneer deze wordt gestart.

Maak in Azure Portal **onder** Toepassingsinstellingen voor de web-app een nieuwe app-instelling met de naam die de aanvullende instellingen `JAVA_OPTS` bevat, zoals `-Xms512m -Xmx1204m` .

Als u de app-instelling wilt configureren vanuit de Maven-invoegvoegcode, voegt u instellings-/waardetags toe in de sectie Azure-invoegvoeging. In het volgende voorbeeld wordt een specifieke minimale en maximale Java-heapgrootte bepaald:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Ontwikkelaars die één toepassing uitvoeren met één implementatiesleuf in hun App Service kunnen de volgende opties gebruiken:

- B1- en S1-instanties: `-Xms1024m -Xmx1024m`
- B2- en S2-exemplaren: `-Xms3072m -Xmx3072m`
- B3- en S3-exemplaren: `-Xms6144m -Xmx6144m`

Wanneer u de heap-instellingen van de toepassing afstemt, controleert u de details van uw App Service en houdt u rekening met meerdere toepassingen en implementatiesleuf om de optimale toewijzing van geheugen te vinden.

### <a name="turn-on-web-sockets"></a>Websockockers in-

Schakel ondersteuning voor websockers in de Azure Portal in de **toepassingsinstellingen** voor de toepassing. U moet de toepassing opnieuw starten om de instelling van kracht te laten worden.

Schakel websockeondersteuning in met behulp van de Azure CLI met de volgende opdracht:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Start de toepassing vervolgens opnieuw:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Standaardtekencoderen instellen

Maak in Azure Portal onder **Toepassingsinstellingen** voor de web-app een nieuwe app-instelling met de `JAVA_OPTS` naam met de waarde `-Dfile.encoding=UTF-8` .

U kunt de app-instelling ook configureren met behulp van App Service Maven-invoegvoeging. Voeg de naam- en waardetags van de instelling toe in de configuratie van de invoegvoegcode:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>JSP-bestanden vooraf compileren

Als u de prestaties van Tomcat-toepassingen wilt verbeteren, kunt u uw JSP-bestanden compileren voordat u implementeert in App Service. U kunt de [Maven-invoeggebruik maken](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) die wordt geleverd door Apache Sling of met dit [Ant-buildbestand.](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)

## <a name="secure-applications"></a>Toepassingen beveiligen

Java-toepassingen die worden uitgevoerd in App Service hebben dezelfde set [best practices](../security/fundamentals/paas-applications-using-app-services.md) voor beveiliging als andere toepassingen.

### <a name="authenticate-users-easy-auth"></a>Gebruikers verifiëren (Eenvoudige verificatie)

Stel app-verificatie in de Azure Portal met de **optie Verificatie en autorisatie.** Hier kunt u verificatie inschakelen met behulp van Azure Active Directory of sociale aanmeldingen, zoals Facebook, Google of GitHub. Azure Portal configuratie werkt alleen wanneer u één verificatieprovider configureert. Zie Configure your [App Service app to use Azure Active Directory login](configure-authentication-provider-aad.md) (Uw app configureren Azure Active Directory en de gerelateerde artikelen voor andere id-providers) voor meer informatie. Als u meerdere aanmeldingsproviders wilt inschakelen, volgt u de instructies in het artikel [App Service verificatie](app-service-authentication-how-to.md) aanpassen.

#### <a name="java-se"></a>Java SE

Spring Boot kunnen ontwikkelaars de Azure Active Directory Spring Boot [gebruiken](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory) om toepassingen te beveiligen met behulp van bekende Spring Security-aantekeningen en API's. Zorg ervoor dat u de maximale headergrootte in het *bestand application.properties* verhoogt. We raden een waarde aan van `16384` .

#### <a name="tomcat"></a>Tomcat

Uw Tomcat-toepassing heeft rechtstreeks vanuit de servlet toegang tot de claims van de gebruiker door het principal-object naar een kaartobject te casten. Met het object Kaart wordt elk claimtype aan een verzameling van de claims voor dat type toe te voegen. In de onderstaande code `request` is een exemplaar van `HttpServletRequest` .

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

U kunt nu het `Map` -object voor een specifieke claim inspecteren. Het volgende codefragment doorsleert bijvoorbeeld alle claimtypen en drukt de inhoud van elke verzameling af.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Gebruik het pad om gebruikers af te `/.auth/ext/logout` melden. Als u andere acties wilt uitvoeren, raadpleegt u de documentatie over [App Service verificatie en autorisatiegebruik.](./app-service-authentication-how-to.md) Er is ook officiële documentatie over de Tomcat [HttpServletRequest-interface](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) en de methoden ervan. De volgende servlet-methoden worden ook geseed op basis van uw App Service configuratie:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Als u deze functie wilt uitschakelen, maakt u een toepassingsinstelling met de `WEBSITE_AUTH_SKIP_PRINCIPAL` naam met de waarde `1` . Als u alle servlet-filters wilt uitschakelen die door App Service, maakt u een instelling met de naam `WEBSITE_SKIP_FILTERS` met de waarde `1` .

### <a name="configure-tlsssl"></a>TLS/SSL configureren

Volg de instructies in Een aangepaste DNS-naam beveiligen met een [SSL-binding in Azure App Service](configure-ssl-bindings.md) om een bestaand SSL-certificaat te uploaden en dit te binden aan de domeinnaam van uw toepassing. Standaard staat uw toepassing nog steeds HTTP-verbindingen toe. Volg de specifieke stappen in de zelfstudie om SSL en TLS af te dwingen.

### <a name="use-keyvault-references"></a>KeyVault-verwijzingen gebruiken

[Azure KeyVault biedt](../key-vault/general/overview.md) gecentraliseerd geheimbeheer met toegangsbeleid en controlegeschiedenis. U kunt geheimen (zoals wachtwoorden of verbindingsreeksen) opslaan in KeyVault en deze geheimen openen in uw toepassing via omgevingsvariabelen.

Volg eerst de instructies voor [het](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) verlenen van toegang tot Key Vault en het maken van een KeyVault-verwijzing naar uw [geheim in een toepassingsinstelling](app-service-key-vault-references.md#reference-syntax). U kunt controleren of de verwijzing naar het geheim wordt opgelost door de omgevingsvariabele af te drukken terwijl u op afstand toegang hebt tot App Service terminal.

Als u deze geheimen wilt injecteren in uw Spring- of Tomcat-configuratiebestand, gebruikt u de syntaxis voor het injecteren van omgevingsvariabelen ( `${MY_ENV_VAR}` ). Raadpleeg deze documentatie over externe [configuraties voor Spring-configuratiebestanden.](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)

::: zone pivot="platform-linux"

### <a name="use-the-java-key-store"></a>Java Key Store gebruiken

Standaard worden openbare of persoonlijke certificaten die zijn geüpload [naar App Service Linux,](configure-ssl-certificate.md) geladen in de respectieve Java-sleutelopslag wanneer de container wordt gestart. Nadat u het certificaat hebt geüpload, moet u de App Service opnieuw starten om het in het Java-sleutelopslag te laden. Openbare certificaten worden geladen in het sleutelopslag op `$JAVA_HOME/jre/lib/security/cacerts` en persoonlijke certificaten worden opgeslagen in `$JAVA_HOME/lib/security/client.jks` .

Er is mogelijk aanvullende configuratie nodig voor het versleutelen van uw JDBC-verbinding met certificaten in het Java-sleutelopslag. Raadpleeg de documentatie voor het JDBC-stuurprogramma dat u hebt gekozen.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initialize-the-java-key-store"></a>Het Java Key Store initialiseren

Als u het `import java.security.KeyStore` object wilt initialiseren, laadt u het keystore-bestand met het wachtwoord. Het standaardwachtwoord voor beide sleutelopslag is 'changeit'.

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>Het sleutelopslag handmatig laden

U kunt certificaten handmatig laden in het sleutelopslag. Maak een app-instelling, , met de waarde om uit te App Service de certificaten automatisch in het `SKIP_JAVA_KEYSTORE_LOAD` `1` sleutelopslag te laden. Alle openbare certificaten die via de App Service zijn geüpload naar Azure Portal worden opgeslagen onder `/var/ssl/certs/` . Persoonlijke certificaten worden opgeslagen onder `/var/ssl/private/` .

U kunt het Java Key Tool gebruiken of er fouten in opsporen door [een SSH-verbinding](configure-linux-open-ssh-session.md) met uw App Service en de opdracht uit te `keytool` voeren. Zie de [documentatie over het sleutelhulpprogramma](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) voor een lijst met opdrachten. Raadpleeg de officiële documentatie voor meer informatie over de [KeyStore-API.](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html)

::: zone-end

## <a name="configure-apm-platforms"></a>APM-platformen configureren

In deze sectie ziet u hoe u Java-toepassingen die zijn geïmplementeerd op Azure App Service op Linux kunt verbinden met de platformen NewRelic en AppDynamics application performance monitoring (APM).

### <a name="configure-new-relic"></a>Een New Relic

::: zone pivot="platform-windows"

1. Een NewRelic-account maken [op NewRelic.com](https://newrelic.com/signup)
2. Download de Java-agent van NewRelic. Deze heeft een bestandsnaam die vergelijkbaar is *metnewrelic-java-x.x.x.zip*.
3. Kopieer uw licentiesleutel. U hebt deze nodig om de agent later te configureren.
4. [SSH in uw App Service en](configure-linux-open-ssh-session.md) maak een nieuwe map */home/site/wwwroot/apm.*
5. Upload de uitgepakte NewRelic Java-agentbestanden naar een map onder */home/site/wwwroot/apm.* De bestanden voor uw agent moeten zich in */home/site/wwwroot/apm/newrelic.*
6. Wijzig het YAML-bestand op */home/site/wwwroot/apm/newrelic/newrelic.yml* en vervang de waarde van de tijdelijke aanduiding door uw eigen licentiesleutel.
7. Blader in Azure Portal naar uw toepassing in App Service en maak een nieuwe toepassingsinstelling.

    - Maak **voor Java SE-apps** een omgevingsvariabele met de `JAVA_OPTS` naam met de waarde `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - Maak **voor Tomcat** een omgevingsvariabele met de `CATALINA_OPTS` naam met de waarde `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end
::: zone pivot="platform-linux"

1. Maak een NewRelic-account [op NewRelic.com](https://newrelic.com/signup)
2. Download de Java-agent van NewRelic. Deze heeft een bestandsnaam die vergelijkbaar is *metnewrelic-java-x.x.x.zip*.
3. Kopieer uw licentiesleutel. U hebt deze later nodig om de agent te configureren.
4. [SSH in uw App Service en](configure-linux-open-ssh-session.md) maak een nieuwe map */home/site/wwwroot/apm.*
5. Upload de uitgepakte NewRelic Java-agentbestanden naar een map onder */home/site/wwwroot/apm.* De bestanden voor uw agent moeten zich in */home/site/wwwroot/apm/newrelic.*
6. Wijzig het YAML-bestand op */home/site/wwwroot/apm/newrelic/newrelic.yml* en vervang de waarde van de tijdelijke aanduiding door uw eigen licentiesleutel.
7. Blader in Azure Portal naar uw toepassing in App Service en maak een nieuwe toepassingsinstelling.
   
    - Maak **voor Java SE-apps** een omgevingsvariabele met de `JAVA_OPTS` naam met de waarde `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - Maak **voor Tomcat** een omgevingsvariabele met de `CATALINA_OPTS` naam met de waarde `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end

>  Als u al een omgevingsvariabele voor of hebt, moet u de optie `JAVA_OPTS` toevoegen aan het einde van de huidige `CATALINA_OPTS` `-javaagent:/...` waarde.

### <a name="configure-appdynamics"></a>AppDynamics configureren

::: zone pivot="platform-windows"

1. Een AppDynamics-account maken [op AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Download de Java-agent van de AppDynamics-website. De bestandsnaam is vergelijkbaar met *AppServerAgent-x.x.x.xxxxx.zip*
3. Gebruik de [Kudu-console om](https://github.com/projectkudu/kudu/wiki/Kudu-console) een nieuwe map */home/site/wwwroot/apm te maken.*
4. Upload de Java-agentbestanden naar een map onder */home/site/wwwroot/apm.* De bestanden voor uw agent moeten zich in */home/site/wwwroot/apm/appdynamics .*
5. Blader in Azure Portal naar uw toepassing in App Service en maak een nieuwe toepassingsinstelling.

   - Maak **voor Java SE-apps** een omgevingsvariabele met de naam `JAVA_OPTS` met de waarde waar uw App Service `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` is.
   - Maak **voor Tomcat-apps** een omgevingsvariabele met de `CATALINA_OPTS` naam met de waarde waar uw App Service `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` is.

::: zone-end
::: zone pivot="platform-linux"

1. Een AppDynamics-account maken [op AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Download de Java-agent van de AppDynamics-website. De bestandsnaam is vergelijkbaar met *AppServerAgent-x.x.x.xxxxx.zip*
3. [SSH in uw App Service en](configure-linux-open-ssh-session.md) maak een nieuwe map */home/site/wwwroot/apm.*
4. Upload de Java-agentbestanden naar een map onder */home/site/wwwroot/apm.* De bestanden voor uw agent moeten zich in */home/site/wwwroot/apm/appdynamics .*
5. Blader in Azure Portal naar uw toepassing in App Service en maak een nieuwe toepassingsinstelling.

   - Maak **voor Java SE-apps** een omgevingsvariabele met de naam `JAVA_OPTS` met de waarde waar uw App Service `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` is.
   - Maak **voor Tomcat-apps** een omgevingsvariabele met de `CATALINA_OPTS` naam met de waarde waar uw App Service `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` is.

::: zone-end

> [!NOTE]
>  Als u al een omgevingsvariabele voor of hebt, moet u de optie toevoegen aan `JAVA_OPTS` het einde van de huidige `CATALINA_OPTS` `-javaagent:/...` waarde.

## <a name="configure-data-sources"></a>Gegevensbronnen configureren

### <a name="java-se"></a>Java SE

Als u verbinding wilt maken met gegevensbronnen in Spring Boot toepassingen, raden we u aan verbindingsreeksen te maken en deze in het *bestand application.properties te* injecteren.

1. Stel in de sectie Configuratie van de pagina App Service een naam in voor de tekenreeks, plak uw JDBC-connection string in het waardeveld en stel het type in op Aangepast. U kunt deze optie eventueel instellen connection string sleufinstelling.

    Deze connection string is toegankelijk voor onze toepassing als een omgevingsvariabele met de naam `CUSTOMCONNSTR_<your-string-name>` . De naam van de connection string die we hierboven hebben gemaakt, krijgt bijvoorbeeld de naam `CUSTOMCONNSTR_exampledb` .

2. In het *bestand application.properties* verwijst u naar connection string naam van de omgevingsvariabele. In ons voorbeeld gebruiken we het volgende.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Raadpleeg de documentatie [Spring Boot over gegevenstoegang en](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) externe [configuraties](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) voor meer informatie over dit onderwerp.

::: zone pivot="platform-windows"

### <a name="tomcat"></a>Tomcat

Deze instructies zijn van toepassing op alle databaseverbindingen. U moet tijdelijke aanduidingen invullen met de naam van de stuurprogrammaklasse en het JAR-bestand van uw gekozen database. Opgegeven is een tabel met klassenamen en stuurprogrammadownloads voor algemene databases.

| Database   | Naam stuurprogrammaklasse                             | JDBC-stuurprogramma                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Downloaden](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Downloaden](https://dev.mysql.com/downloads/connector/j/) (selecteer 'Platform onafhankelijk') |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Downloaden](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server#download)                                                           |

Als u Tomcat wilt configureren voor het gebruik van Java Database Connectivity (JDBC) of de Java Persistence API (JPA), moet u eerst de omgevingsvariabele aanpassen die bij het starten door `CATALINA_OPTS` Tomcat wordt ingelezen. Stel deze waarden in via een app-instelling in de [App Service Maven-invoegvoegomgeving](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Of stel de omgevingsvariabelen in op **de pagina**  >  **Configuratietoepassingsinstellingen** in Azure Portal.

Bepaal vervolgens of de gegevensbron beschikbaar moet zijn voor één toepassing of voor alle toepassingen die worden uitgevoerd op de Tomcat-servlet.

#### <a name="application-level-data-sources"></a>Gegevensbronnen op toepassingsniveau

1. Maak een *context.xml* in de *map META-INF/* van uw project. Maak de *map META-INF/als* deze nog niet bestaat.

2. Voeg *context.xml* een `Context` -element toe om de gegevensbron te koppelen aan een JNDI-adres. Vervang de tijdelijke aanduiding door de klassenaam van uw stuurprogramma `driverClassName` uit de bovenstaande tabel.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Werk de gegevens van uw *web.xml* om de gegevensbron in uw toepassing te gebruiken.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Gedeelde resources op serverniveau

Tomcat-installaties op App Service in Windows bestaan in gedeelde ruimte op App Service-abonnement. U kunt een Tomcat-installatie niet rechtstreeks wijzigen voor configuratie voor de hele server. Als u configuratiewijzigingen op serverniveau wilt aanbrengen in uw Tomcat-installatie, moet u Tomcat kopiëren naar een lokale map, waarin u de configuratie van Tomcat kunt wijzigen. 

##### <a name="automate-creating-custom-tomcat-on-app-start"></a>Het maken van aangepaste Tomcat automatiseren bij het starten van de app

U kunt een opstartscript gebruiken om acties uit te voeren voordat een web-app wordt gestart. Het opstartscript voor het aanpassen van Tomcat moet de volgende stappen uitvoeren:

1. Controleer of Tomcat al lokaal is gekopieerd en geconfigureerd. Als dat zo is, kan het opstartscript hier eindigen.
2. Kopieer Tomcat lokaal.
3. De vereiste configuratiewijzigingen aanbrengen.
4. Geef aan dat de configuratie is voltooid.

Hier is een PowerShell-script dat deze stappen voltooit:

```powershell
    # Check for marker file indicating that config has already been done
    if(Test-Path "$LOCAL_EXPANDED\tomcat\config_done_marker"){
        return 0
    }

    # Delete previous Tomcat directory if it exists
    # In case previous config could not be completed or a new config should be forcefully installed
    if(Test-Path "$LOCAL_EXPANDED\tomcat"){
        Remove-Item "$LOCAL_EXPANDED\tomcat" --recurse
    }

    # Copy Tomcat to local
    # Using the environment variable $AZURE_TOMCAT90_HOME uses the 'default' version of Tomcat
    Copy-Item -Path "$AZURE_TOMCAT90_HOME\*" -Destination "$LOCAL_EXPANDED\tomcat" -Recurse

    # Perform the required customization of Tomcat
    {... customization ...}

    # Mark that the operation was a success
    New-Item -Path "$LOCAL_EXPANDED\tomcat\config_done_marker" -ItemType File
```

##### <a name="transforms"></a>Transformaties

Een veelvoorkomende toepassing voor het aanpassen van een Tomcat-versie is het wijzigen van de `server.xml` `context.xml` configuratiebestanden , of `web.xml` Tomcat. App Service wijzigt deze bestanden al om platformfuncties te bieden. Als u deze functies wilt blijven gebruiken, is het belangrijk dat u de inhoud van deze bestanden behoudt wanneer u er wijzigingen in aan brengen. Hiervoor raden we u aan een [XSL-transformatie (XSLT) te gebruiken.](https://www.w3schools.com/xml/xsl_intro.asp) Gebruik een XSL-transformatie om wijzigingen aan te brengen in de XML-bestanden met behoud van de oorspronkelijke inhoud van het bestand.

###### <a name="example-xslt-file"></a>Voorbeeld van XSLT-bestand

Met deze voorbeeldtransformator wordt een nieuw connector-knooppunt toegevoegd aan `server.xml` . Noteer *identiteitstransformatie,* waarmee de oorspronkelijke inhoud van het bestand behouden blijft.

```xml
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:output method="xml" indent="yes"/>
  
    <!-- Identity transform: this ensures that the original contents of the file are included in the new file -->
    <!-- Ensure that your transform files include this block -->
    <xsl:template match="@* | node()" name="Copy">
      <xsl:copy>
        <xsl:apply-templates select="@* | node()"/>
      </xsl:copy>
    </xsl:template>
  
    <xsl:template match="@* | node()" mode="insertConnector">
      <xsl:call-template name="Copy" />
    </xsl:template>
  
    <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                   contains(., '&lt;Connector') and
                                   (contains(., 'scheme=&quot;https&quot;') or
                                    contains(., &quot;scheme='https'&quot;))]">
      <xsl:value-of select="." disable-output-escaping="yes" />
    </xsl:template>
  
    <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                     comment()[contains(., '&lt;Connector') and
                                               (contains(., 'scheme=&quot;https&quot;') or
                                                contains(., &quot;scheme='https'&quot;))]
                                    )]
                        ">
      <xsl:copy>
        <xsl:apply-templates select="@* | node()" mode="insertConnector" />
      </xsl:copy>
    </xsl:template>
  
    <!-- Add the new connector after the last existing Connnector if there is one -->
    <xsl:template match="Connector[last()]" mode="insertConnector">
      <xsl:call-template name="Copy" />
  
      <xsl:call-template name="AddConnector" />
    </xsl:template>
  
    <!-- ... or before the first Engine if there is no existing Connector -->
    <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                  mode="insertConnector">
      <xsl:call-template name="AddConnector" />
  
      <xsl:call-template name="Copy" />
    </xsl:template>
  
    <xsl:template name="AddConnector">
      <!-- Add new line -->
      <xsl:text>&#xa;</xsl:text>
      <!-- This is the new connector -->
      <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
                 maxThreads="150" scheme="https" secure="true" 
                 keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
                 clientAuth="false" sslProtocol="TLS" />
    </xsl:template>

</xsl:stylesheet>
```

###### <a name="function-for-xsl-transform"></a>Functie voor XSL-transformatie

PowerShell heeft ingebouwde hulpprogramma's voor het transformeren van XML-bestanden met behulp van XSL-transformaties. Het volgende script is een voorbeeldfunctie die u in kunt gebruiken om `startup.ps1` de transformatie uit te voeren:

```powershell
    function TransformXML{
        param ($xml, $xsl, $output)

        if (-not $xml -or -not $xsl -or -not $output)
        {
            return 0
        }

        Try
        {
            $xslt_settings = New-Object System.Xml.Xsl.XsltSettings;
            $XmlUrlResolver = New-Object System.Xml.XmlUrlResolver;
            $xslt_settings.EnableScript = 1;

            $xslt = New-Object System.Xml.Xsl.XslCompiledTransform;
            $xslt.Load($xsl,$xslt_settings,$XmlUrlResolver);
            $xslt.Transform($xml, $output);

        }

        Catch
        {
            $ErrorMessage = $_.Exception.Message
            $FailedItem = $_.Exception.ItemName
            Write-Host  'Error'$ErrorMessage':'$FailedItem':' $_.Exception;
            return 0
        }
        return 1
    }
```

##### <a name="app-settings"></a>App-instellingen

Het platform moet ook weten waar uw aangepaste versie van Tomcat is geïnstalleerd. U kunt de locatie van de installatie instellen in de `CATALINA_BASE` app-instelling.

U kunt de Azure CLI gebruiken om deze instelling te wijzigen:

```powershell
    az webapp config appsettings set -g $MyResourceGroup -n $MyUniqueApp --settings CATALINA_BASE="%LOCAL_EXPANDED%\tomcat"
```

U kunt de instelling ook handmatig wijzigen in de Azure Portal:

1. Ga naar **Instellingen**  >  **Configuratie**  >  **Toepassingsinstellingen.**
1. Selecteer **Nieuwe toepassingsinstelling.**
1. Gebruik deze waarden om de instelling te maken:
   1. **Naam:**`CATALINA_BASE`
   1. **Waarde**: `"%LOCAL_EXPANDED%\tomcat"`

##### <a name="example-startupps1"></a>Voorbeeld startup.ps1

Met het volgende voorbeeldscript wordt een aangepaste Tomcat gekopieerd naar een lokale map, wordt een XSL-transformatie uitgevoerd en wordt aangegeven dat de transformatie is geslaagd:

```powershell
    # Locations of xml and xsl files
    $target_xml="$LOCAL_EXPANDED\tomcat\conf\server.xml"
    $target_xsl="$HOME\site\server.xsl"

    # Define the transform function
    # Useful if transforming multiple files
    function TransformXML{
        param ($xml, $xsl, $output)

        if (-not $xml -or -not $xsl -or -not $output)
        {
            return 0
        }

        Try
        {
            $xslt_settings = New-Object System.Xml.Xsl.XsltSettings;
            $XmlUrlResolver = New-Object System.Xml.XmlUrlResolver;
            $xslt_settings.EnableScript = 1;

            $xslt = New-Object System.Xml.Xsl.XslCompiledTransform;
            $xslt.Load($xsl,$xslt_settings,$XmlUrlResolver);
            $xslt.Transform($xml, $output);
        }

        Catch
        {
            $ErrorMessage = $_.Exception.Message
            $FailedItem = $_.Exception.ItemName
            Write-Host  'Error'$ErrorMessage':'$FailedItem':' $_.Exception;
            return 0
        }
        return 1
    }

    # Check for marker file indicating that config has already been done
    if(Test-Path "$LOCAL_EXPANDED\tomcat\config_done_marker"){
        return 0
    }

    # Delete previous Tomcat directory if it exists
    # In case previous config could not be completed or a new config should be forcefully installed
    if(Test-Path "$LOCAL_EXPANDED\tomcat"){
        Remove-Item "$LOCAL_EXPANDED\tomcat" --recurse
    }

    # Copy Tomcat to local
    # Using the environment variable $AZURE_TOMCAT90_HOME uses the 'default' version of Tomcat
    Copy-Item -Path "$AZURE_TOMCAT90_HOME\*" -Destination "$LOCAL_EXPANDED\tomcat" -Recurse

    # Perform the required customization of Tomcat
    $success = TransformXML -xml $target_xml -xsl $target_xsl -output $target_xml

    # Mark that the operation was a success if successful
    if($success){
        New-Item -Path "$LOCAL_EXPANDED\tomcat\config_done_marker" -ItemType File
    }
```

#### <a name="finalize-configuration"></a>Configuratie finaliseren

Ten slotte plaatsen we de stuurprogramma-JAR's in het Tomcat-klassepad en starten we uw App Service. Zorg ervoor dat de JDBC-stuurprogrammabestanden beschikbaar zijn voor de Tomcat-klasseloader door ze in de map */home/tomcat/lib te* plaatsen. (Maak deze map als deze nog niet bestaat.) Voer de volgende stappen uit om App Service te uploaden naar uw App Service-exemplaar:

1. Installeer in [Cloud Shell](https://shell.azure.com)de web-app-extensie:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Voer de volgende CLI-opdracht uit om een SSH-tunnel te maken van uw lokale systeem naar App Service:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Maak verbinding met de lokale tunnelingpoort met uw SFTP-client en upload de bestanden naar de *map /home/tomcat/lib.*

U kunt ook een FTP-client gebruiken om het JDBC-stuurprogramma te uploaden. Volg deze [instructies om uw FTP-referenties op te vragen.](deploy-configure-credentials.md)

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="tomcat"></a>Tomcat

Deze instructies zijn van toepassing op alle databaseverbindingen. U moet tijdelijke aanduidingen invullen met de naam van de stuurprogrammaklasse en het JAR-bestand van uw gekozen database. Opgegeven is een tabel met klassenamen en stuurprogrammadownloads voor algemene databases.

| Database   | Naam stuurprogrammaklasse                             | JDBC-stuurprogramma                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Downloaden](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Downloaden](https://dev.mysql.com/downloads/connector/j/) (selecteer 'Platform onafhankelijk') |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Downloaden](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server#download)                                                           |

Als u Tomcat wilt configureren voor het gebruik van Java Database Connectivity (JDBC) of de Java Persistence API (JPA), moet u eerst de omgevingsvariabele aanpassen die bij het starten door Tomcat wordt `CATALINA_OPTS` ingelezen. Stel deze waarden in via een app-instelling in [de App Service Maven-invoegvoegapp](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Of stel de omgevingsvariabelen in op **de pagina**  >  **Configuratietoepassingsinstellingen** in Azure Portal.

Bepaal vervolgens of de gegevensbron beschikbaar moet zijn voor één toepassing of voor alle toepassingen die worden uitgevoerd op de Tomcat-servlet.

#### <a name="application-level-data-sources"></a>Gegevensbronnen op toepassingsniveau

1. Maak een *context.xml* in de *map META-INF/* van uw project. Maak de *map META-INF/als* deze nog niet bestaat.

2. Voeg *context.xml* een `Context` -element toe om de gegevensbron te koppelen aan een JNDI-adres. Vervang de `driverClassName` tijdelijke aanduiding door de klassenaam van uw stuurprogramma uit de bovenstaande tabel.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Werk de gegevens van uw *web.xml* om de gegevensbron in uw toepassing te gebruiken.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Gedeelde resources op serverniveau

Als u een gedeelde gegevensbron op serverniveau toevoegt, moet u de gegevensbron van Tomcat bewerken server.xml. Upload eerst een [opstartscript en](faq-app-service-linux.md#built-in-images) stel het pad naar het script in **configuratie-opstartopdracht**  >  **in.** U kunt het opstartscript uploaden met [FTP.](deploy-ftp.md)

Uw opstartscript maakt een [xsl-transformatie](https://www.w3schools.com/xml/xsl_intro.asp) naar het server.xml bestand en geeft het resulterende XML-bestand uit naar `/usr/local/tomcat/conf/server.xml` . Het opstartscript moet libxslt installeren via apk. Uw xsl-bestand en opstartscript kunnen worden geüpload via FTP. Hieronder vindt u een voorbeeld van een opstartscript.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Hieronder vindt u een voorbeeld van een xsl-bestand. In het xsl-voorbeeldbestand wordt een nieuw connector-knooppunt toegevoegd aan de Tomcat-server.xml.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>Configuratie finaliseren

Plaats ten slotte de stuurprogramma-JAR's in het Tomcat-klassepad en start uw App Service.

1. Zorg ervoor dat de JDBC-stuurprogrammabestanden beschikbaar zijn voor de Tomcat-klasseloader door ze in de map */home/tomcat/lib te* plaatsen. (Maak deze map als deze nog niet bestaat.) Voer de volgende stappen uit om App Service te uploaden naar uw App Service-exemplaar:

    1. Installeer in [Cloud Shell](https://shell.azure.com)de web-app-extensie:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Voer de volgende CLI-opdracht uit om een SSH-tunnel te maken van uw lokale systeem naar App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Maak verbinding met de lokale tunnelingpoort met uw SFTP-client en upload de bestanden naar de *map /home/tomcat/lib.*

    U kunt ook een FTP-client gebruiken om het JDBC-stuurprogramma te uploaden. Volg deze [instructies voor het verkrijgen van uw FTP-referenties.](deploy-configure-credentials.md)

2. Als u een gegevensbron op serverniveau hebt gemaakt, start u de linux-App Service opnieuw. Tomcat wordt opnieuw ingesteld `CATALINA_BASE` op en gebruikt de `/home/tomcat` bijgewerkte configuratie.

### <a name="jboss-eap"></a>JBoss EAP

Er zijn drie belangrijke stappen bij het registreren van een gegevensbron bij [JBoss EAP:](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.0/html/configuration_guide/datasource_management)het uploaden van het JDBC-stuurprogramma, het toevoegen van het JDBC-stuurprogramma als module en het registreren van de module. App Service is een stateless hostingservice, dus de configuratieopdrachten voor het toevoegen en registreren van de gegevensbronmodule moeten als script worden uitgevoerd en toegepast zodra de container wordt gestart.

1. Verkrijg het JDBC-stuurprogramma van uw database. 
2. Maak een XML-moduledefinitiebestand voor het JDBC-stuurprogramma. Het onderstaande voorbeeld is een moduledefinitie voor PostgreSQL.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="org.postgres">
        <resources>
        <!-- ***** IMPORTANT : REPLACE THIS PLACEHOLDER *******-->
        <resource-root path="/home/site/deployments/tools/postgresql-42.2.12.jar" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

1. Plaats uw JBoss CLI-opdrachten in een bestand met de naam `jboss-cli-commands.cli` . De JBoss-opdrachten moeten de module toevoegen en registreren als een gegevensbron. In het onderstaande voorbeeld ziet u de JBoss CLI-opdrachten voor PostgreSQL.

    ```bash
    #!/usr/bin/env bash
    module add --name=org.postgres --resources=/home/site/deployments/tools/postgresql-42.2.12.jar --module-xml=/home/site/deployments/tools/postgres-module.xml

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name="postgres",driver-module-name="org.postgres",driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=${POSTGRES_CONNECTION_URL,env.POSTGRES_CONNECTION_URL:jdbc:postgresql://db:5432/postgres} --user-name=${POSTGRES_SERVER_ADMIN_FULL_NAME,env.POSTGRES_SERVER_ADMIN_FULL_NAME:postgres} --password=${POSTGRES_SERVER_ADMIN_PASSWORD,env.POSTGRES_SERVER_ADMIN_PASSWORD:example} --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    ```

1. Maak een opstartscript dat `startup_script.sh` de JBoss CLI-opdrachten aanroept. In het onderstaande voorbeeld ziet u hoe u uw `jboss-cli-commands.cli` aanroept. Later gaat u App Service script uit te voeren wanneer de container wordt gestart. 

    ```bash
    $JBOSS_HOME/bin/jboss-cli.sh --connect --file=/home/site/deployments/tools/jboss-cli-commands.cli
    ```

1. Upload met behulp van een FTP-client naar keuze uw JDBC-stuurprogramma, `jboss-cli-commands.cli` `startup_script.sh` , en de moduledefinitie naar `/site/deployments/tools/` .
2. Configureer uw site om te worden `startup_script.sh` uitgevoerd wanneer de container wordt gestart. Navigeer in Azure Portal naar **Configuratie Algemene**  >  **instellingen**  >  **Opstartopdracht**. Stel het opstartopdrachtveld in op `/home/site/deployments/tools/startup_script.sh` . U moet vervolgens de wijzigingen **Opslaan**.

Als u wilt controleren of de gegevensbron is toegevoegd aan de JBoss-server, voegt u SSH toe aan uw web-app en voer u `$JBOSS_HOME/bin/jboss-cli.sh --connect` uit. Zodra u bent verbonden met JBoss, voer de uit `/subsystem=datasources:read-resource` om een lijst met gegevensbronnen af te drukken.

::: zone-end

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="choosing-a-java-runtime-version"></a>Een Java-runtimeversie kiezen

App Service kunnen gebruikers de belangrijkste versie van de JVM kiezen, zoals Java 8 of Java 11, evenals de secundaire versie, zoals 1.8.0_232 of 11.0.5. U kunt er ook voor kiezen om de secundaire versie automatisch bij te stellen wanneer er nieuwe secundaire versies beschikbaar komen. In de meeste gevallen moeten productiesites gebruikmaken van vastgemaakte secundaire JVM-versies. Hiermee voorkomt u onverwachte uitval tijdens het automatisch bijwerken van een secundaire versie.

Als u ervoor kiest om de secundaire versie vast te maken, moet u de secundaire JVM-versie periodiek bijwerken op de site. Om ervoor te zorgen dat uw toepassing wordt uitgevoerd op de nieuwere secundaire versie, maakt u een staging-site en verhoogt u de secundaire versie op de staging-site. Zodra u hebt bevestigd dat de toepassing correct wordt uitgevoerd in de nieuwe secundaire versie, kunt u de staging- en productiesleuven wisselen.

## <a name="jboss-eap-hardware-options"></a>JBoss EAP-hardwareopties

JBoss EAP is alleen beschikbaar voor de Premium- en Isolated-hardwareopties. Klanten die tijdens de openbare preview een JBoss EAP-site hebben gemaakt in de categorie Gratis, Gedeeld, Basic of Standard, moeten opschalen naar de Premium- of Isolated-hardwarelaag om onverwacht gedrag te voorkomen.

## <a name="java-runtime-statement-of-support"></a>Ondersteuningsverklaring voor Java-runtime

### <a name="jdk-versions-and-maintenance"></a>JDK-versies en -onderhoud

De ondersteunde Java Development Kit (JDK) van Azure wordt [zulu](https://www.azul.com/downloads/azure-only/zulu/) geleverd via [Azul Systems](https://www.azul.com/). Azul Zulu Enterprise-builds van OpenJDK zijn een gratis, voor meerdere platforms klaarstaande distributie van de OpenJDK voor Azure en Azure Stack wordt door Microsoft en Azul Systems. Ze bevatten alle onderdelen voor het maken en uitvoeren van Java SE-toepassingen. U kunt de JDK installeren vanuit [Java JDK-installatie.](/azure/developer/java/fundamentals/java-jdk-long-term-support)

Belangrijke versie-updates worden geleverd via nieuwe runtime-opties in Azure App Service. Klanten werken bij naar deze nieuwere versies van Java door hun App Service te configureren en zijn verantwoordelijk voor het testen en ervoor zorgen dat de grote update aan hun behoeften voldoet.

Ondersteunde JDK's worden in januari, april, juli en oktober van elk jaar automatisch gepatcht op kwartaalbasis. Raadpleeg dit ondersteuningsdocument voor meer informatie over Java [in Azure.](/azure/developer/java/fundamentals/java-jdk-long-term-support)

### <a name="security-updates"></a>Beveiligingsupdates

Patches en oplossingen voor belangrijke beveiligingsproblemen worden uitgebracht zodra ze beschikbaar komen vanuit Azul Systems. Een 'major' beveiligingsprobleem wordt gedefinieerd door een basisscore van 9.0 of hoger op het [NIST Common Vulnerability Scoring System, versie 2.](https://nvd.nist.gov/vuln-metrics/cvss)

Tomcat 8.0 heeft het einde van de levensduur (EOL) bereikt vanaf [30 september 2018.](https://tomcat.apache.org/tomcat-80-eol.html) Hoewel de runtime nog steeds beschikbaar is op Azure App Service, worden in Azure geen beveiligingsupdates toegepast op Tomcat 8.0. Migreert uw toepassingen indien mogelijk naar Tomcat 8.5 of 9.0. Tomcat 8.5 en 9.0 zijn beschikbaar op Azure App Service. Zie de [officiële Tomcat-site](https://tomcat.apache.org/whichversion.html) voor meer informatie. 

### <a name="deprecation-and-retirement"></a>Afschaffing en pensioen

Als een ondersteunde Java-runtime wordt verwijderd, krijgen Azure-ontwikkelaars die de betrokken runtime gebruiken ten minste zes maanden voordat de runtime wordt teruggetrokken een kennisgeving over afschaffing.


### <a name="local-development"></a>Lokale ontwikkeling

Ontwikkelaars kunnen de Productie-editie van Azul Zulu Enterprise JDK voor lokale ontwikkeling downloaden via de [downloadsite van Azul.](https://www.azul.com/downloads/azure-only/zulu/)

### <a name="development-support"></a>Ondersteuning voor ontwikkeling

Productondersteuning voor de door Azure ondersteunde [Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) is beschikbaar via Microsoft bij het ontwikkelen voor Azure of [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) met een ondersteuning voor Azure [abonnement](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Volgende stappen

Ga naar [het Azure voor Java-ontwikkelaarscentrum](/java/azure/) voor azure-quickstarts, zelfstudies en Java-referentiedocumentatie.

Algemene vragen over het gebruik App Service voor Linux die niet specifiek zijn voor de Java-ontwikkeling, worden beantwoord in [de veelgestelde vragen App Service Linux.](faq-app-service-linux.md)
