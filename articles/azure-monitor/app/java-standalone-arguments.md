---
title: De JVM ARG-Azure Monitor Application Insights Java toevoegen
description: Het JVM ARG voor Azure Monitor Application Insights Java toevoegen
ms.topic: conceptual
ms.date: 04/16/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: e665bf734d2141081796f7810eed02eff3b5c5ad
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601098"
---
# <a name="adding-the-jvm-arg-for-azure-monitor-application-insights-java"></a>Het JVM ARG voor Azure Monitor Application Insights Java toevoegen



## <a name="azure-environments"></a>Azure-omgevingen

[App Services](../../app-service/configure-language-java.md#set-java-runtime-options)configureren.

## <a name="spring-boot"></a>Spring Boot

Voeg de JVM ARG `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` ergens vóór toe `-jar` , bijvoorbeeld:

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

## <a name="spring-boot-via-docker-entry-point"></a>Spring boot via docker-invoer punt

Als u het formulier *exec* gebruikt, voegt u de para meter `"-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"` toe aan de parameter lijst ergens vóór de `"-jar"` para meter, bijvoorbeeld:

```
ENTRYPOINT ["java", "-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar", "-jar", "<myapp.jar>"]
```

Als u het *Shell* formulier gebruikt, voegt u het JVM ARG `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` ergens vóór toe `-jar` , bijvoorbeeld:

```
ENTRYPOINT java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat geïnstalleerd via `apt-get` of `yum`

Als u Tomcat hebt geïnstalleerd via `apt-get` of `yum` , moet u een bestand hebben `/etc/tomcat8/tomcat8.conf` .  Voeg deze regel toe aan het einde van het bestand:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat geïnstalleerd via downloaden en uitpakken

Als u Tomcat hebt geïnstalleerd via downloaden en uitpakken vanuit [https://tomcat.apache.org](https://tomcat.apache.org) , hebt u een bestand nodig `<tomcat>/bin/catalina.sh` .  Maak een nieuw bestand in dezelfde map `<tomcat>/bin/setenv.sh` met de volgende inhoud:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Als het bestand `<tomcat>/bin/setenv.sh` al bestaat, wijzigt u dat bestand en voegt `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` u toe aan `CATALINA_OPTS` .


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Tomcat uitvoeren vanaf de opdracht regel

Zoek het bestand `<tomcat>/bin/catalina.bat` .  Maak een nieuw bestand in dezelfde map `<tomcat>/bin/setenv.bat` met de volgende inhoud:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

Er zijn geen aanhalings tekens nodig, maar als u deze wilt gebruiken, is de juiste plaats:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Als het bestand `<tomcat>/bin/setenv.bat` al bestaat, wijzigt u alleen dat bestand en voegt u het toe `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` aan `CATALINA_OPTS` .

### <a name="running-tomcat-as-a-windows-service"></a>Tomcat uitvoeren als een Windows-service

Zoek het bestand `<tomcat>/bin/tomcat8w.exe` .  Voer dat uitvoer bare bestand uit en voeg `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` het toe aan de `Java Options` onder het `Java` tabblad.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Zelfstandige server

Toevoegen `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` aan de bestaande `JAVA_OPTS` omgevings variabele in het bestand `JBOSS_HOME/bin/standalone.conf` (Linux) of `JBOSS_HOME/bin/standalone.conf.bat` (Windows):

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Domein server

Toevoegen `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` aan de bestaande `jvm-options` in `JBOSS_HOME/domain/configuration/host.xml` :

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

Als u meerdere beheerde servers op één host uitvoert, moet u `applicationinsights.agent.id` aan de `system-properties` voor elk een van de `server` volgende opties toevoegen:

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

De opgegeven `applicationinsights.agent.id` waarde moet uniek zijn. Het wordt gebruikt voor het maken van een submap in de applicationinsights-map, aangezien elk JVM-proces een eigen lokale applicationinsights config en lokaal applicationinsights-logboek bestand nodig heeft. Als er een rapportage aan de centrale collector wordt uitgevoerd, `applicationinsights.properties` wordt het bestand gedeeld door de meerdere beheerde servers en is het opgegeven, zodat de `applicationinsights.agent.id` `agent.id` instelling in dat gedeelde bestand moet worden overschreven. `applicationinsights.agent.rollup.id` kan op dezelfde manier worden opgegeven in de server `system-properties` Als u de `agent.rollup.id` instelling per beheerde server wilt overschrijven.


## <a name="jetty-9"></a>Jetty 9

Deze regels toevoegen aan `start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>Payara 5

Toevoegen `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` aan de bestaande `jvm-options` in `glassfish/domains/domain1/config/domain.xml` :

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSphere 8

Open de beheer console en ga naar **servers > WebSphere toepassings servers > toepassings** servers, kies de juiste toepassings servers en klik op: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
In ' algemene JVM-argumenten ' voegt u het volgende toe:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
Daarna slaat u de toepassings server op en start u deze opnieuw op.


## <a name="openliberty-18"></a>Openvrijheid 18

Maak een nieuw bestand `jvm.options` in de server directory (bijvoorbeeld `<openliberty>/usr/servers/defaultServer` ) en voeg deze regel toe:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
