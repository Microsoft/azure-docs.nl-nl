---
title: Java-apps configureren
description: Meer informatie over het configureren van Java-apps om te worden uitgevoerd op Azure App Service. In dit artikel worden de meest algemene configuratietaken beschreven.
keywords: Azure app service, Web-app, Windows, OSS, Java, Tomcat, JBoss
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
ms.openlocfilehash: 0334e259f75440cae25f1e165c0621c85f7c7705
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97804000"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Een Java-app voor Azure App Service configureren

Met Azure App Service kunnen Java-Ontwikkel aars snel hun Java SE-, Tomcat-en JBoss EAP-webtoepassingen bouwen, implementeren en schalen op een volledig beheerde service. Implementeer toepassingen met maven plugins, vanaf de opdracht regel of in editors zoals IntelliJ, eclips of Visual Studio code.

Deze hand leiding bevat belang rijke concepten en instructies voor Java-Ontwikkel aars die App Service gebruiken. Als u Azure App Service nog nooit hebt gebruikt, lees dan eerst de [Java-Snelstartgids](quickstart-java.md) . Algemene vragen over het gebruik van App Service die niet specifiek zijn voor Java-ontwikkeling, worden beantwoord in de [app Service Veelgestelde vragen](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Uw app implementeren

U kunt de [Azure web app-invoeg toepassing voor maven](https://github.com/microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) gebruiken om uw. War-of jar-bestanden te implementeren. Implementatie met populaire Ide's wordt ook ondersteund met de [Azure-Toolkit voor IntelliJ](/azure/developer/java/toolkit-for-intellij/) of [Azure-Toolkit voor eclipse](/azure/developer/java/toolkit-for-eclipse).

Anders is uw implementatie methode afhankelijk van het type archief:

### <a name="java-se"></a>Java SE

Gebruik het `/api/zipdeploy/` eind punt van de kudu-site om jar-bestanden te implementeren in Java SE. Raadpleeg [deze documentatie](./deploy-zip.md#rest)voor meer informatie over deze API.

### <a name="tomcat"></a>Tomcat

Als u een WAR-bestand wilt implementeren in Tomcat, gebruikt u het `/api/wardeploy/` eind punt om het archief bestand te plaatsen. Raadpleeg [deze documentatie](./deploy-zip.md#deploy-war-file)voor meer informatie over deze API.

::: zone pivot="platform-linux"

### <a name="jboss-eap"></a>JBoss EAP

Als u een WAR-bestand wilt implementeren in JBoss, gebruikt u het `/api/wardeploy/` eind punt om het archief bestand te plaatsen. Raadpleeg [deze documentatie](./deploy-zip.md#deploy-war-file)voor meer informatie over deze API.

Als u. oormerk bestanden wilt implementeren, [gebruikt u FTP](deploy-ftp.md).

::: zone-end

Implementeer uw War-of JAR-protocol niet met FTP. Het FTP-hulp programma is ontworpen voor het uploaden van opstart scripts, afhankelijkheden of andere runtime bestanden. Het is niet de beste keuze voor het implementeren van web-apps.

## <a name="logging-and-debugging-apps"></a>Apps registreren en fouten opsporen

Prestatie rapporten, visualisaties van verkeer en de status checkups zijn beschikbaar voor elke app via de Azure Portal. Zie [Azure app service Diagnostics-overzicht](overview-diagnostics.md)voor meer informatie.

### <a name="stream-diagnostic-logs"></a>Diagnostische logboeken streamen

::: zone pivot="platform-windows"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end
::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

Zie [Stream-Logboeken in Cloud shell](troubleshoot-diagnostic-logs.md#in-cloud-shell)voor meer informatie.

::: zone pivot="platform-linux"

### <a name="ssh-console-access"></a>SSH-console toegang

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="troubleshooting-tools"></a>Hulpprogram ma's voor probleem oplossing

De ingebouwde Java-installatie kopieën zijn gebaseerd op het besturings systeem [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) . Gebruik `apk` Package Manager om hulpprogram ma's voor probleem oplossing of opdrachten te installeren.

::: zone-end

### <a name="flight-recorder"></a>Vlucht recorder

Alle Java-Runtimes op App Service met behulp van de Azul-JVMs worden geleverd met de Zulu Flight-recorder. U kunt dit gebruiken om JVM-, systeem-en toepassings gebeurtenissen op te nemen en problemen in uw Java-toepassingen op te lossen.

::: zone pivot="platform-windows"

#### <a name="timed-recording"></a>Getimede opname

Als u een getimede opname wilt maken, hebt u de PID (proces-ID) van de Java-toepassing nodig. Als u de PID wilt vinden, opent u een browser voor de SCM-site van uw web-app op https://<uw-site naam>. scm.azurewebsites.net/ProcessExplorer/. Op deze pagina worden de actieve processen in uw web-app weer gegeven. Zoek het proces met de naam ' Java ' in de tabel en kopieer de bijbehorende PID (proces-ID).

Open vervolgens de **console fout opsporing** in de bovenste werk balk van de SCM-site en voer de volgende opdracht uit. Vervang door `<pid>` de proces-id die u eerder hebt gekopieerd. Met deze opdracht wordt een registratie van 30 seconden voor het maken van een profilering van uw Java-toepassing gestart en wordt een bestand `timed_recording_example.jfr` met de naam in de `D:\home` map gegenereerd.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

::: zone-end
::: zone pivot="platform-linux"

SSH in uw App Service en voer de `jcmd` opdracht uit om een lijst weer te geven met alle Java-processen die worden uitgevoerd. Naast jcmd wordt uw Java-toepassing met een proces-ID (PID) weer geven.

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Voer de onderstaande opdracht uit om een 30-seconden-opname van de JVM te starten. Hiermee wordt het JVM profiel gemaakt en wordt er een JFR-bestand met de naam *jfr_example. JFR* in de hoofdmap. (Vervang 116 door de PID van uw Java-app.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Tijdens het interval van 30 seconden kunt u controleren of de registratie plaatsvindt door uit te voeren `jcmd 116 JFR.check` . Hiermee worden alle opnamen voor het gegeven Java-proces weer gegeven.

#### <a name="continuous-recording"></a>Doorlopende opname

U kunt Zulu Flight recorder gebruiken om uw Java-toepassing continu te profileren met minimale gevolgen voor de prestaties van runtime ([bron](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Als u dit wilt doen, voert u de volgende Azure CLI-opdracht uit om een app-instelling met de naam JAVA_OPTS te maken met de vereiste configuratie. De inhoud van de instelling van de JAVA_OPTS app wordt door gegeven aan de `java` opdracht wanneer uw app wordt gestart.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Zodra de opname is gestart, kunt u de huidige opname gegevens op elk gewenst moment dumpen met behulp van de `JFR.dump` opdracht.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

::: zone-end

#### <a name="analyze-jfr-files"></a>`.jfr`Bestanden analyseren

Gebruik [FTPS](deploy-ftp.md) om uw JFR-bestand te downloaden naar uw lokale computer. Down load en Installeer [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/)om het JFR-bestand te analyseren. Zie de [Azul-documentatie](https://docs.azul.com/zmc/) en de [installatie-instructies](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)voor instructies voor Zulu-missie beheer.

### <a name="app-logging"></a>App-logboek registratie

::: zone pivot="platform-windows"

Schakel [toepassings logboeken](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) in via de Azure portal of [Azure cli](/cli/azure/webapp/log#az-webapp-log-config) om app service te configureren voor het schrijven van de standaard console-uitvoer van de toepassing en de standaard console fout stromen naar het lokale bestands systeem of Azure Blob Storage. Logboek registratie naar het lokale App Service bestandssysteem exemplaar is uitgeschakeld 12 uur nadat het is geconfigureerd. Als u meer retentie nodig hebt, configureert u de toepassing voor het schrijven van uitvoer naar een BLOB storage-container. Uw Java-en tomcat-app-Logboeken kunt u vinden in de */Home/logfiles/Application/* -map.

::: zone-end
::: zone pivot="platform-linux"

Schakel [toepassings logboeken](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) in via de Azure portal of [Azure cli](/cli/azure/webapp/log#az-webapp-log-config) om app service te configureren voor het schrijven van de standaard console-uitvoer van de toepassing en de standaard console fout stromen naar het lokale bestands systeem of Azure Blob Storage. Als u meer retentie nodig hebt, configureert u de toepassing voor het schrijven van uitvoer naar een BLOB storage-container. Uw Java-en tomcat-app-Logboeken kunt u vinden in de */Home/logfiles/Application/* -map.

Azure Blob Storage-logboek registratie voor op Linux gebaseerde App Services kan alleen worden geconfigureerd met behulp van [Azure monitor (preview-versie)](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview) 

::: zone-end

Als uw toepassing gebruikmaakt van [logback](https://logback.qos.ch/) of [Log4j](https://logging.apache.org/log4j) voor tracering, kunt u deze traceringen door sturen naar Azure-toepassing Insights met behulp van de configuratie-instructies voor logboek registratie in [Java-traceer Logboeken in Application Insights verkennen](../azure-monitor/app/java-trace-logs.md).

## <a name="customization-and-tuning"></a>Aanpassing en afstemming

Azure App Service voor Linux ondersteunt het afstemmen en aanpassen van het kader via de Azure Portal en CLI. Raadpleeg de volgende artikelen voor een niet-Java-specifieke Web-app-configuratie:

- [App-instellingen configureren](configure-common.md#configure-app-settings)
- [Een aangepast domein instellen](app-service-web-tutorial-custom-domain.md)
- [SSL-bindingen configureren](configure-ssl-bindings.md)
- [Een CDN toevoegen](../cdn/cdn-add-to-web-app.md)
- [De kudu-site configureren](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)


### <a name="set-java-runtime-options"></a>Java runtime-opties instellen

Als u toegewezen geheugen of andere JVM runtime opties wilt instellen, maakt u een [app-instelling](configure-common.md#configure-app-settings) `JAVA_OPTS` met de naam met de opties. App Service geeft deze instelling als een omgevings variabele door aan de Java-runtime wanneer deze wordt gestart.

Maak in de Azure Portal onder **Toepassings instellingen** voor de web-app een nieuwe app-instelling `JAVA_OPTS` met de naam die de aanvullende instellingen bevat, zoals `-Xms512m -Xmx1204m` .

Als u de app-instelling wilt configureren vanuit de Maven-invoeg toepassing, voegt u instellingen/waarde-tags toe in de sectie Azure-invoeg toepassing. In het volgende voor beeld wordt een specifieke minimale en maximale grootte voor Java-heap ingesteld:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Ontwikkel aars die één toepassing met één implementatie sleuf in hun App Service-abonnement uitvoeren, kunnen de volgende opties gebruiken:

- B1 en S1-instanties: `-Xms1024m -Xmx1024m`
- B2-en S2-instanties: `-Xms3072m -Xmx3072m`
- B3-en S3-instanties: `-Xms6144m -Xmx6144m`

Bij het afstemmen van de instellingen voor de heap van toepassingen, raadpleegt u de details van het App Service plan en houdt u rekening met meerdere toepassingen en implementatie sleuven om de optimale toewijzing van het geheugen te vinden.

### <a name="turn-on-web-sockets"></a>Websockets inschakelen

Schakel ondersteuning voor websockets in de Azure Portal in de **Toepassings instellingen** voor de toepassing. U moet de toepassing opnieuw opstarten om de instelling van kracht te laten worden.

Schakel de ondersteuning voor websockets in met behulp van de Azure CLI met de volgende opdracht:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Start de toepassing vervolgens opnieuw:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Standaard teken codering instellen

Maak in de Azure Portal onder **Toepassings instellingen** voor de web-app een nieuwe app-instelling met de naam `JAVA_OPTS` met waarde `-Dfile.encoding=UTF-8` .

U kunt de app-instelling ook configureren met behulp van de App Service maven-invoeg toepassing. Voeg de instellingen naam en waarde tags toe aan de configuratie van de invoeg toepassing:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>JSP-bestanden vooraf compileren

Als u de prestaties van Tomcat-toepassingen wilt verbeteren, kunt u uw JSP-bestanden compileren voordat u implementeert in App Service. U kunt de [maven-invoeg toepassing](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) van Apache strop gebruiken of dit [Ant build-bestand](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)gebruiken.

## <a name="secure-applications"></a>Veilige toepassingen

Java-toepassingen die worden uitgevoerd in App Service hebben dezelfde [aanbevolen beveiligings procedures](../security/fundamentals/paas-applications-using-app-services.md) als andere toepassingen.

### <a name="authenticate-users-easy-auth"></a>Gebruikers verifiëren (eenvoudige verificatie)

Stel app-verificatie in het Azure Portal in met de optie **verificatie en autorisatie** . Van daaruit kunt u verificatie inschakelen met behulp van Azure Active Directory of sociale aanmeldingen, zoals Facebook, Google of GitHub. Azure Portal configuratie werkt alleen bij het configureren van één verificatie provider. Zie [uw app service-app configureren voor het gebruik van Azure Active Directory login](configure-authentication-provider-aad.md) en de verwante artikelen voor andere id-providers voor meer informatie. Als u meerdere aanmeld providers wilt inschakelen, volgt u de instructies in het artikel [customize app service-verificatie](app-service-authentication-how-to.md) .

#### <a name="java-se"></a>Java SE

Spring boot-ontwikkel aars kunnen de [Azure Active Directory Spring boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory) gebruiken om toepassingen te beveiligen met behulp van vertrouwde beveiligings aantekeningen en-api's. Zorg ervoor dat u de maximale header-grootte in het bestand *Application. Properties* verhoogt. We suggereren een waarde van `16384` .

#### <a name="tomcat"></a>Tomcat

Uw Tomcat-toepassing kan rechtstreeks vanuit de servlet toegang krijgen tot de claims van de gebruiker door het Principal-object naar een kaart object te casten. Het kaart object wijst elk claim type toe aan een verzameling van de claims voor dat type. In de onderstaande code `request` is een exemplaar van `HttpServletRequest` .

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Nu kunt u het `Map` object voor een specifieke claim controleren. Het volgende code fragment loopt bijvoorbeeld door alle claim typen en drukt de inhoud van elke verzameling af.

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

Als u gebruikers wilt afmelden, gebruikt u het `/.auth/ext/logout` pad. Raadpleeg de documentatie over [app service verificatie en autorisatie gebruik](./app-service-authentication-how-to.md)om andere acties uit te voeren. Er is ook officiële documentatie over de Tomcat [HttpServletRequest-interface](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) en de bijbehorende methoden. De volgende servlet-methoden worden ook gehydrateerd op basis van uw App Service configuratie:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Als u deze functie wilt uitschakelen, maakt u een toepassings instelling `WEBSITE_AUTH_SKIP_PRINCIPAL` met de naam met een waarde van `1` . Als u alle Servlet-filters die zijn toegevoegd door App Service wilt uitschakelen, maakt u een instelling `WEBSITE_SKIP_FILTERS` met de naam met een waarde van `1` .

### <a name="configure-tlsssl"></a>TLS/SSL configureren

Volg de instructies in het gedeelte [een aangepaste DNS-naam beveiligen met een SSL-binding in azure app service](configure-ssl-bindings.md) om een bestaand SSL-certificaat te uploaden en te binden aan de domein naam van uw toepassing. Uw toepassing staat standaard nog HTTP-verbindingen toe. Volg de specifieke stappen in de zelf studie om SSL en TLS af te dwingen.

### <a name="use-keyvault-references"></a>Verwijzingen naar de sleutel kluis gebruiken

[Azure](../key-vault/general/overview.md) -sleutel kluis biedt gecentraliseerd geheim beheer met toegangs beleid en controle geschiedenis. U kunt geheimen (zoals wacht woorden of verbindings reeksen) opslaan in de sleutel kluis en toegang krijgen tot deze geheimen in uw toepassing via omgevings variabelen.

Volg eerst de instructies voor het [verlenen van toegang tot Key Vault van uw app](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) en het [maken van een verwijzing naar een sleutel kluis in een toepassings instelling](app-service-key-vault-references.md#reference-syntax). U kunt controleren of de verwijzing naar het geheim wordt omgezet door de omgevings variabele af te drukken terwijl u extern toegang hebt tot de App Service Terminal.

Als u deze geheimen wilt injecteren in het configuratie bestand voor de lente of het Tomcat, gebruikt u de syntaxis voor het injecteren van omgevings variabelen ( `${MY_ENV_VAR}` ). Raadpleeg deze documentatie over [externe configuraties](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)voor lente configuratie bestanden.

::: zone pivot="platform-linux"

### <a name="use-the-java-key-store"></a>Het Java-sleutel archief gebruiken

Standaard worden alle open bare of persoonlijke certificaten [die zijn geüpload naar app service Linux](configure-ssl-certificate.md) geladen in de respectieve Java-sleutel archieven wanneer de container wordt gestart. Nadat u het certificaat hebt geüpload, moet u de App Service opnieuw opstarten zodat het in het Java-sleutel archief wordt geladen. Open bare certificaten worden geladen in de sleutel opslagplaats op `$JAVA_HOME/jre/lib/security/cacerts` en persoonlijke certificaten worden opgeslagen in `$JAVA_HOME/lib/security/client.jks` .

Er is mogelijk extra configuratie nodig voor het versleutelen van uw JDBC-verbinding met certificaten in het Java-sleutel archief. Raadpleeg de documentatie voor het gekozen JDBC-stuur programma.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initialize-the-java-key-store"></a>Het Java-sleutel archief initialiseren

Als u het object wilt initialiseren `import java.security.KeyStore` , laadt u het bestand met de opslag plaats met het wacht woord. Het standaard wachtwoord voor beide sleutel archieven is "changeit".

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

#### <a name="manually-load-the-key-store"></a>Het sleutel archief hand matig laden

U kunt certificaten hand matig laden naar de sleutel opslag. Maak een app-instelling, `SKIP_JAVA_KEYSTORE_LOAD` met de waarde `1` app service uit te scha kelen, zodat de certificaten niet automatisch in het sleutel archief worden geladen. Alle open bare certificaten die zijn geüpload naar App Service via de Azure Portal worden opgeslagen onder `/var/ssl/certs/` . Persoonlijke certificaten worden opgeslagen onder `/var/ssl/private/` .

U kunt het Java-sleutel programma interactief gebruiken of fouten opsporen door [een SSH-verbinding](configure-linux-open-ssh-session.md) met uw app service te openen en de opdracht uit te voeren `keytool` . Raadpleeg de [documentatie van het belang rijk hulp programma](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) voor een lijst met opdrachten. Raadpleeg [de officiële documentatie](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html)voor meer informatie over de API voor de-opslag.

::: zone-end

## <a name="configure-apm-platforms"></a>APM-platforms configureren

In deze sectie wordt beschreven hoe u Java-toepassingen die zijn geïmplementeerd op Azure App Service op Linux verbindt met de NewRelic-en AppDynamics-platformen voor het controleren van de prestaties van toepassingen (APM).

### <a name="configure-new-relic"></a>Nieuwe Relic configureren

::: zone pivot="platform-windows"

1. Een NewRelic-account maken op [NewRelic.com](https://newrelic.com/signup)
2. Down load de Java-Agent van NewRelic. deze heeft een bestands naam die vergelijkbaar is met *newrelic-java-x.x.x.zip*.
3. Kopieer uw licentie sleutel, u hebt deze later nodig om de agent te configureren.
4. [Ssh in uw app service-exemplaar](configure-linux-open-ssh-session.md) en maak een nieuwe directory */Home/site/wwwroot/apm*.
5. Upload de uitgepakte NewRelic Java-Agent bestanden naar een map onder */Home/site/wwwroot/apm*. De bestanden voor uw agent moeten in */Home/site/wwwroot/apm/newrelic* zijn.
6. Wijzig het YAML-bestand op */Home/site/wwwroot/apm/newrelic/newrelic.yml* en vervang de tijdelijke aanduiding voor de licentie waarde door uw eigen licentie code.
7. In de Azure Portal, bladert u naar uw toepassing in App Service en maakt u een nieuwe toepassings instelling.

    - Voor **Java SE** -apps maakt u een omgevings variabele `JAVA_OPTS` met de naam met de waarde `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - Voor **Tomcat** maakt u een omgevings variabele `CATALINA_OPTS` met de naam met de waarde `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end
::: zone pivot="platform-linux"

1. Een NewRelic-account maken op [NewRelic.com](https://newrelic.com/signup)
2. Down load de Java-Agent van NewRelic. deze heeft een bestands naam die vergelijkbaar is met *newrelic-java-x.x.x.zip*.
3. Kopieer uw licentie sleutel, u hebt deze later nodig om de agent te configureren.
4. [Ssh in uw app service-exemplaar](configure-linux-open-ssh-session.md) en maak een nieuwe directory */Home/site/wwwroot/apm*.
5. Upload de uitgepakte NewRelic Java-Agent bestanden naar een map onder */Home/site/wwwroot/apm*. De bestanden voor uw agent moeten in */Home/site/wwwroot/apm/newrelic* zijn.
6. Wijzig het YAML-bestand op */Home/site/wwwroot/apm/newrelic/newrelic.yml* en vervang de tijdelijke aanduiding voor de licentie waarde door uw eigen licentie code.
7. In de Azure Portal, bladert u naar uw toepassing in App Service en maakt u een nieuwe toepassings instelling.
   
    - Voor **Java SE** -apps maakt u een omgevings variabele `JAVA_OPTS` met de naam met de waarde `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - Voor **Tomcat** maakt u een omgevings variabele `CATALINA_OPTS` met de naam met de waarde `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end

>  Als u al een omgevings variabele voor `JAVA_OPTS` of hebt `CATALINA_OPTS` , voegt u de `-javaagent:/...` optie toe aan het einde van de huidige waarde.

### <a name="configure-appdynamics"></a>AppDynamics configureren

::: zone pivot="platform-windows"

1. Een AppDynamics-account maken op [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. De Java-agent downloaden van de AppDynamics-website, de bestands naam is vergelijkbaar met *AppServerAgent-x.x.x.xxxxx.zip*
3. Gebruik de [kudu-console](https://github.com/projectkudu/kudu/wiki/Kudu-console) om een nieuwe directory */Home/site/wwwroot/apm* te maken.
4. Upload de Java-Agent bestanden naar een map onder */Home/site/wwwroot/apm*. De bestanden voor uw agent moeten in */Home/site/wwwroot/apm/appdynamics* zijn.
5. In de Azure Portal, bladert u naar uw toepassing in App Service en maakt u een nieuwe toepassings instelling.

   - Voor **Java SE** -apps maakt u een omgevings variabele `JAVA_OPTS` met de naam met de waarde `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` waar de naam van `<app-name>` uw app service is.
   - Voor **Tomcat** -apps maakt u een omgevings variabele `CATALINA_OPTS` met de naam met de waarde `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` waar de naam van `<app-name>` uw app service is.

::: zone-end
::: zone pivot="platform-linux"

1. Een AppDynamics-account maken op [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. De Java-agent downloaden van de AppDynamics-website, de bestands naam is vergelijkbaar met *AppServerAgent-x.x.x.xxxxx.zip*
3. [Ssh in uw app service-exemplaar](configure-linux-open-ssh-session.md) en maak een nieuwe directory */Home/site/wwwroot/apm*.
4. Upload de Java-Agent bestanden naar een map onder */Home/site/wwwroot/apm*. De bestanden voor uw agent moeten in */Home/site/wwwroot/apm/appdynamics* zijn.
5. In de Azure Portal, bladert u naar uw toepassing in App Service en maakt u een nieuwe toepassings instelling.

   - Voor **Java SE** -apps maakt u een omgevings variabele `JAVA_OPTS` met de naam met de waarde `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` waar de naam van `<app-name>` uw app service is.
   - Voor **Tomcat** -apps maakt u een omgevings variabele `CATALINA_OPTS` met de naam met de waarde `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` waar de naam van `<app-name>` uw app service is.

::: zone-end

> [!NOTE]
>  Als u al een omgevings variabele voor `JAVA_OPTS` of hebt `CATALINA_OPTS` , voegt u de `-javaagent:/...` optie toe aan het einde van de huidige waarde.

## <a name="configure-data-sources"></a>Gegevensbronnen configureren

### <a name="java-se"></a>Java SE

Om verbinding te maken met gegevens bronnen in veer boot-toepassingen, wordt u geadviseerd om verbindings reeksen te maken en deze in uw toepassing te injecteren *. eigenschappen* bestand.

1. Stel in het gedeelte ' configuratie ' van de pagina App Service een naam in voor de teken reeks, plak uw JDBC-connection string in het waardeveld en stel het type in op aangepast. U kunt deze connection string eventueel instellen als sleuf instelling.

    Deze connection string is toegankelijk voor onze toepassing als een omgevings variabele met de naam `CUSTOMCONNSTR_<your-string-name>` . De connection string die u hierboven hebt gemaakt, krijgen bijvoorbeeld de naam `CUSTOMCONNSTR_exampledb` .

2. In uw *toepassing. Properties* -bestand verwijst u naar deze Connection String met de naam van de omgevings variabele. In ons voor beeld gebruiken we het volgende.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Raadpleeg de [Spring boot-documentatie over gegevens toegang](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) en [externe configuraties](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) voor meer informatie over dit onderwerp.

::: zone pivot="platform-windows"

### <a name="tomcat"></a>Tomcat

Deze instructies zijn van toepassing op alle database verbindingen. U moet tijdelijke aanduidingen vullen met de naam van de stuurprogrammanaam en het JAR-bestand van uw gekozen data base. Gegeven is een tabel met klasse-namen en down loads van Stuur Programma's voor algemene data bases.

| Database   | Naam stuur programma klasse                             | JDBC-stuur programma                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Downloaden](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Downloaden](https://dev.mysql.com/downloads/connector/j/) (Selecteer ' platform onafhankelijk ') |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Downloaden](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Als u Tomcat wilt configureren voor het gebruik van de Java Data Base Connectivity (JDBC) of de Java Persistence API (JPA), moet u eerst de `CATALINA_OPTS` omgevings variabele aanpassen die in Tomcat wordt gelezen tijdens het opstarten. Stel deze waarden in via een app-instelling in de [maven-invoeg toepassing van app service](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Of stel de omgevings variabelen in op de pagina **configuratie**  >  **Toepassings instellingen** in het Azure Portal.

Bepaal vervolgens of de gegevens bron beschikbaar moet zijn voor één toepassing of voor alle toepassingen die worden uitgevoerd op de Tomcat-servlet.

#### <a name="application-level-data-sources"></a>Gegevens bronnen op toepassings niveau

1. Maak een *context.xml* -bestand in de *META-INF/-* map van uw project. Maak de *META-INF/-* map als deze niet bestaat.

2. Voeg in *context.xml* een `Context` element toe om de gegevens bron te koppelen aan een JNDI-adres. Vervang de `driverClassName` tijdelijke aanduiding door de naam van de klasse van uw stuur programma uit de bovenstaande tabel.

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

3. Werk de *web.xml* van uw toepassing bij om de gegevens bron in uw toepassing te gebruiken.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Configuratie volt ooien

Ten slotte gaan we de potten van het stuur programma in het Tomcat CLASSPATH plaatsen en de App Service opnieuw opstarten. Zorg ervoor dat de JDBC-stuurprogrammabestanden beschikbaar zijn voor de Tomcat-ClassLoader door ze te plaatsen in de map */Home/tomcat/lib* . (Maak deze map als deze nog niet bestaat.) Als u deze bestanden wilt uploaden naar uw App Service-exemplaar, voert u de volgende stappen uit:

1. Installeer de webapp-extensie in de [Cloud shell](https://shell.azure.com):

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Voer de volgende CLI-opdracht uit om een SSH-tunnel van uw lokale systeem te maken om te App Service:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Maak verbinding met de lokale tunnel poort met uw SFTP-client en upload de bestanden naar de map */Home/tomcat/lib* .

U kunt ook een FTP-client gebruiken om het JDBC-stuur programma te uploaden. Volg deze [instructies voor het ophalen van uw FTP-referenties](deploy-configure-credentials.md).

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="tomcat"></a>Tomcat

Deze instructies zijn van toepassing op alle database verbindingen. U moet tijdelijke aanduidingen vullen met de naam van de stuurprogrammanaam en het JAR-bestand van uw gekozen data base. Gegeven is een tabel met klasse-namen en down loads van Stuur Programma's voor algemene data bases.

| Database   | Naam stuur programma klasse                             | JDBC-stuur programma                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Downloaden](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Downloaden](https://dev.mysql.com/downloads/connector/j/) (Selecteer ' platform onafhankelijk ') |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Downloaden](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Als u Tomcat wilt configureren voor het gebruik van de Java Data Base Connectivity (JDBC) of de Java Persistence API (JPA), moet u eerst de `CATALINA_OPTS` omgevings variabele aanpassen die in Tomcat wordt gelezen tijdens het opstarten. Stel deze waarden in via een app-instelling in de [maven-invoeg toepassing van app service](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Of stel de omgevings variabelen in op de pagina **configuratie**  >  **Toepassings instellingen** in het Azure Portal.

Bepaal vervolgens of de gegevens bron beschikbaar moet zijn voor één toepassing of voor alle toepassingen die worden uitgevoerd op de Tomcat-servlet.

#### <a name="application-level-data-sources"></a>Gegevens bronnen op toepassings niveau

1. Maak een *context.xml* -bestand in de *META-INF/-* map van uw project. Maak de *META-INF/-* map als deze niet bestaat.

2. Voeg in *context.xml* een `Context` element toe om de gegevens bron te koppelen aan een JNDI-adres. Vervang de `driverClassName` tijdelijke aanduiding door de naam van de klasse van uw stuur programma uit de bovenstaande tabel.

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

3. Werk de *web.xml* van uw toepassing bij om de gegevens bron in uw toepassing te gebruiken.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Resources op gedeelde server niveau

Als u een gedeelde gegevens bron op server niveau wilt toevoegen, moet u de server.xml van Tomcat bewerken. Upload eerst een [opstart script](faq-app-service-linux.md#built-in-images) en stel het pad naar het script in de   >  **opstart opdracht** van de configuratie in. U kunt het opstart script uploaden met [FTP](deploy-ftp.md).

Met het opstart script wordt een [XSL-trans formatie](https://www.w3schools.com/xml/xsl_intro.asp) naar het server.xml-bestand gemaakt en wordt het resulterende XML-bestand uitgevoerd naar `/usr/local/tomcat/conf/server.xml` . Het opstart script moet libxslt installeren via APK. Het XSL-bestand en het opstart script kunnen worden geüpload via FTP. Hieronder ziet u een voor beeld van een opstart script.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Hieronder vindt u een voor beeld van een XSL-bestand. Het XSL-voorbeeld bestand voegt een nieuw connector knooppunt toe aan de Tomcat-server.xml.

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

#### <a name="finalize-configuration"></a>Configuratie volt ooien

Plaats tot slot de potten van het stuur programma in het Tomcat CLASSPATH en start uw App Service opnieuw op.

1. Zorg ervoor dat de JDBC-stuurprogrammabestanden beschikbaar zijn voor de Tomcat-ClassLoader door ze te plaatsen in de map */Home/tomcat/lib* . (Maak deze map als deze nog niet bestaat.) Als u deze bestanden wilt uploaden naar uw App Service-exemplaar, voert u de volgende stappen uit:

    1. Installeer de webapp-extensie in de [Cloud shell](https://shell.azure.com):

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Voer de volgende CLI-opdracht uit om een SSH-tunnel van uw lokale systeem te maken om te App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Maak verbinding met de lokale tunnel poort met uw SFTP-client en upload de bestanden naar de map */Home/tomcat/lib* .

    U kunt ook een FTP-client gebruiken om het JDBC-stuur programma te uploaden. Volg deze [instructies voor het ophalen van uw FTP-referenties](deploy-configure-credentials.md).

2. Als u een gegevens bron op server niveau hebt gemaakt, start u de App Service Linux-toepassing opnieuw. Tomcat wordt opnieuw ingesteld `CATALINA_BASE` op `/home/tomcat` en gebruikt de bijgewerkte configuratie.

### <a name="jboss-eap"></a>JBoss EAP

Er zijn drie kern stappen bij het [registreren van een gegevens bron met JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.0/html/configuration_guide/datasource_management): het JDBC-stuur programma uploaden, het JDBC-stuur programma toevoegen als een module en de module registreren. App Service is een stateless hosting service. Daarom moeten de configuratie opdrachten voor het toevoegen en registreren van de gegevens bron module worden vastgelegd en worden toegepast wanneer de container wordt gestart.

1. Verkrijg het JDBC-stuur programma van uw data base. 
2. Maak een XML-module definitie bestand voor het JDBC-stuur programma. Het onderstaande voor beeld is een module definitie voor PostgreSQL.

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

1. Plaats uw JBoss CLI-opdrachten in een bestand met de naam `jboss-cli-commands.cli` . De JBoss-opdrachten moeten de module toevoegen en als gegevens bron registreren. In het onderstaande voor beeld ziet u de JBoss CLI-opdrachten voor PostgreSQL.

    ```bash
    #!/usr/bin/env bash
    module add --name=org.postgres --resources=/home/site/deployments/tools/postgresql-42.2.12.jar --module-xml=/home/site/deployments/tools/postgres-module.xml

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name="postgres",driver-module-name="org.postgres",driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=${POSTGRES_CONNECTION_URL,env.POSTGRES_CONNECTION_URL:jdbc:postgresql://db:5432/postgres} --user-name=${POSTGRES_SERVER_ADMIN_FULL_NAME,env.POSTGRES_SERVER_ADMIN_FULL_NAME:postgres} --password=${POSTGRES_SERVER_ADMIN_PASSWORD,env.POSTGRES_SERVER_ADMIN_PASSWORD:example} --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    ```

1. Maak een opstart script `startup_script.sh` dat de JBoss cli-opdrachten aanroept. In het volgende voor beeld ziet u hoe u kunt bellen `jboss-cli-commands.cli` . Later configre u App Service om dit script uit te voeren wanneer de container wordt gestart. 

    ```bash
    $JBOSS_HOME/bin/jboss-cli.sh --connect --file=/home/site/deployments/tools/jboss-cli-commands.cli
    ```

1. Gebruik een FTP-client van uw keuze, upload uw JDBC-stuur programma,, `jboss-cli-commands.cli` `startup_script.sh` en de module definitie naar `/site/deployments/tools/` .
2. Configureer uw site om uit te voeren `startup_script.sh` wanneer de container wordt gestart. In azure portal gaat u naar **configuratie**  >  **algemene instellingen**  >  **opstart opdracht**. Stel het opstart opdracht veld in op `/home/site/deployments/tools/startup_script.sh` . U moet vervolgens de wijzigingen **Opslaan**.

Om te bevestigen dat de gegevens bron is toegevoegd aan de JBoss-server, SSH in uw webapp en run `$JBOSS_HOME/bin/jboss-cli.sh --connect` . Zodra u verbinding hebt gemaakt met JBoss, voert u de uit `/subsystem=datasources:read-resource` om een lijst met gegevens bronnen af te drukken.

::: zone-end

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="choosing-a-java-runtime-version"></a>Een Java runtime-versie kiezen

Met App Service kunnen gebruikers de primaire versie van de JVM kiezen, zoals Java 8 of Java 11, evenals de secundaire versie, zoals 1.8.0 _232 of 11.0.5. U kunt er ook voor kiezen om de secundaire versie automatisch te laten bijwerken wanneer nieuwe secundaire versies beschikbaar worden. In de meeste gevallen moeten productie sites vastgemaakte secundaire JVM-versies gebruiken. Hiermee voor komt u dat er unnanticipated storingen optreden tijdens het automatisch bijwerken van de secundaire versie.

Als u ervoor kiest om de secundaire versie vast te maken, moet u de secundaire versie van JVM regel matig bijwerken op de site. Om ervoor te zorgen dat uw toepassing wordt uitgevoerd op de nieuwere secundaire versie, maakt u een faserings sleuf en verhoogt u de secundaire versie op de staging-site. Nadat u hebt gecontroleerd of de toepassing correct wordt uitgevoerd op de nieuwe secundaire versie, kunt u de fase ring en productie-sleuven wisselen.

## <a name="jboss-eap-hardware-options"></a>Opties voor JBoss EAP-hardware

JBoss EAP is alleen beschikbaar op de opties Premium en geïsoleerd. Klanten die een JBoss EAP-site hebben gemaakt in een gratis, gedeeld, basis of Standard-laag tijdens de open bare preview, moeten omhoog schalen naar de laag voor Premium of geïsoleerd om onverwacht gedrag te voor komen.

## <a name="java-runtime-statement-of-support"></a>Java runtime-instructie van ondersteuning

### <a name="jdk-versions-and-maintenance"></a>JDK-versies en onderhoud

De ondersteunde Java Development Kit (JDK) van Azure wordt [Zulu](https://www.azul.com/downloads/azure-only/zulu/) geboden via [Azul-systemen](https://www.azul.com/). Azul Zulu Enter prise builds van OpenJDK zijn een gratis, op productie gebaseerde distributie van de OpenJDK voor Azure en Azure Stack ondersteund door micro soft en Azul Systems. Ze bevatten alle onderdelen voor het maken en uitvoeren van Java SE-toepassingen. U kunt de JDK installeren vanuit de [Java JDK-installatie](/azure/developer/java/fundamentals/java-jdk-long-term-support).

Primaire versie-updates worden via nieuwe runtime-opties in Azure App Service verschaft. Klanten werken bij naar deze nieuwere versies van Java door hun App Service-implementatie te configureren en verantwoordelijk te zijn voor het testen en ervoor te zorgen dat de belang rijke update aan hun behoeften voldoet.

Ondersteunde JDKs worden automatisch op een driemaandelijkse patch uitgevoerd in januari, april, juli en oktober van elk jaar. Raadpleeg [Dit ondersteunings document](/azure/developer/java/fundamentals/java-jdk-long-term-support)voor meer informatie over java in Azure.

### <a name="security-updates"></a>Beveiligingsupdates

Patches en oplossingen voor belang rijke beveiligings problemen worden vrijgegeven zodra deze beschikbaar worden gesteld via Azul-systemen. Een ' belang rijk ' beveiligingslek is gedefinieerd met een basis Score van 9,0 of hoger in het [gemeen schappelijke beveiligings risico van het NIST-systeem versie 2](https://nvd.nist.gov/vuln-metrics/cvss).

Tomcat 8,0 is [sinds 30 September 2018 geëindigd (EOL)](https://tomcat.apache.org/tomcat-80-eol.html). Terwijl de runtime nog steeds beschikbaar is op Azure App Service, zullen Azure geen beveiligings updates Toep assen op Tomcat 8,0. Migreer, indien mogelijk, uw toepassingen naar Tomcat 8,5 of 9,0. Zowel Tomcat 8,5 als 9,0 zijn beschikbaar op Azure App Service. Raadpleeg de [officiële Tomcat-site](https://tomcat.apache.org/whichversion.html) voor meer informatie. 

### <a name="deprecation-and-retirement"></a>Afschaffing en buiten gebruik stellen

Als een ondersteunde Java-runtime wordt ingetrokken, krijgen Azure-ontwikkel aars die de betrokken runtime gebruiken, een afschaffing die ten minste zes maanden duurt voordat de runtime wordt afgetrokken.


### <a name="local-development"></a>Lokale ontwikkeling

Ontwikkel aars kunnen de productie-editie van Azul Zulu Enter prise JDK downloaden voor lokale ontwikkeling vanaf [de download site van Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Ontwikkelings ondersteuning

Product ondersteuning voor de door [Azure ondersteunde Azul ZULU jdk](https://www.azul.com/downloads/azure-only/zulu/) is beschikbaar via micro soft bij het ontwikkelen voor azure of [Azure stack](https://azure.microsoft.com/overview/azure-stack/) met een [gekwalificeerd ondersteunings abonnement voor Azure](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Volgende stappen

Ga naar [Azure voor Java-ontwikkelaars](/java/azure/) centrum voor meer informatie over Azure Quick starts, zelf studies en naslag informatie voor Java.

Algemene vragen over het gebruik van App Service voor Linux die niet specifiek zijn voor de Java-ontwikkeling, worden beantwoord in de [Veelgestelde vragen over app service Linux](faq-app-service-linux.md).
