---
title: 'Snelstart: Aanmelden met Microsoft toevoegen aan een Java-webapp | Azure'
titleSuffix: Microsoft identity platform
description: In deze quickstart leert u hoe u Microsoft-aanmelding implementeert in een Java-webtoepassing met behulp van OpenID Connect.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: e188c00840a4d043e94f94f9db565e2d4e06aaba
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031059"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Quickstart: Aanmelden met Microsoft toevoegen aan een Java-webapp

In deze quickstart downloadt u een codevoorbeeld en voert u dit uit. Het codevoorbeeld laat zien hoe gebruikers kunnen worden aangemeld en de Microsoft Graph kunnen aanroepen met een Java-webtoepassing. Gebruikers uit elke willekeurige Azure AD-organisatie (Azure Active Directory) kunnen zich aanmelden bij de toepassing.

 Zie [Hoe het voorbeeld werkt](#how-the-sample-works) voor een illustratie.

## <a name="prerequisites"></a>Vereisten

Als u dit voorbeeld wilt uitvoeren, hebt u het volgende nodig:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 of hoger en [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>De quickstart-app registreren en downloaden
> U hebt twee opties voor het starten van de quickstart-toepassing: express (Optie 1) of handmatig (Optie 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld
>
> 1. Ga naar de quickstart-ervaring [Azure-portal - App-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs).
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies in de quickstart van het portal om de automatisch geconfigureerde toepassingscode te downloaden.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
>
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw toepassing:
>
> 1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
> 1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
> 1. Zoek en selecteer de optie **Azure Active Directory**.
> 1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
> 1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
> 1. Selecteer **Registreren**.
> 1. Noteer op de pagina **Overzicht** de **Toepassings-id (client)** en de **Map-id (tenant)** voor later gebruik.
> 1. Selecteer **Verificatie** onder **Beheren**.
> 1. Selecteer **Een platform toevoegen** > **Web**.
> 1. Voeg `https://localhost:8443/msal4jsample/secure/aad` toe vanuit de sectie **Omleidings-URI's**.
> 1. Selecteer **Configureren**.
> 1. Voeg `https://localhost:8443/msal4jsample/graph/me` vanuit de sectie **Web** toe als tweede **Omleidings-URI**.
> 1. Selecteer onder **Beheren** de optie **Certificaten en geheimen**. Selecteer in de sectie **Clientgeheimen** de optie **Nieuw clientgeheim**.
> 1. Typ een beschrijving voor de sleutel (bijvoorbeeld 'app-geheim'), laat de standaardvervaldatum staan, en selecteer **Toevoegen**.
> 1. Noteer de **Waarde** van het **Clientgeheim** voor later gebruik.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in Azure Portal
>
> Voor een juiste werking van het codevoorbeeld uit deze quickstart, moet u:
>
> 1. Antwoord-URL's toevoegen als `https://localhost:8443/msal4jsample/secure/aad` en `https://localhost:8443/msal4jsample/graph/me`
> 1. Een clientgeheim maken.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-aspnet-webapp/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-code-sample"></a>Stap 2: Het codevoorbeeld downloaden
> [!div renderon="docs"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Download het project en pak het zip-bestand uit in een lokale map dichter bij de hoofdmap (bijvoorbeeld **C:\Azure-Samples**)
>
> Als u https met localhost wilt gebruiken, vult u de eigenschappen server.ssl.key in. Gebruik het keytool-hulpprogramma om een zelfondertekend certificaat te maken (opgenomen in JRE).
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Plaats het gegenereerde KeyStore-bestand in de map 'resources'.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>Stap 3: Het codevoorbeeld configureren
> 1. Pak het zip-bestand uit naar lokale map.
> 1. Als u een Integrated Development Environment gebruikt, opent u het voorbeeld in uw favoriete IDE (optioneel).
> 1. Open het bestand application.properties, dat u kunt vinden in src/main/resources/folder, en vervang de waarden in de velden *aad.clientId*, *aad. authority* en *aad.secretKey* door de respectievelijke waarden van **Toepassings-id**, **Tenant-id** en **Clientgeheim** als volgt:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
> Waar:
>
> - `Enter_the_Application_Id_here`: de toepassings-id voor de toepassing die u hebt geregistreerd.
> - `Enter_the_Client_Secret_Here`: het **clientgeheim** dat u in **Certificaten en geheimen** hebt gemaakt voor de toepassing die u hebt geregistreerd.
> - `Enter_the_Tenant_Info_Here`: de waarde voor **Map-id (tenant)** van de toepassing die u hebt geregistreerd.
> 1. Als u https met localhost wilt gebruiken, vult u de eigenschappen server.ssl.key in. Gebruik het keytool-hulpprogramma om een zelfondertekend certificaat te maken (opgenomen in JRE).
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Plaats het gegenereerde KeyStore-bestand in de map 'resources'.


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Stap 3: Het codevoorbeeld uitvoeren
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Stap 4: Het codevoorbeeld uitvoeren

Om het project uit te voeren, kunt u:

Het rechtstreeks uitvoeren vanuit uw IDE met behulp van de ingesloten Spring Boot-server of het verpakken als een WAR-bestand met behulp van [maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) en het implementeren naar een J2EE-containeroplossing zoals [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-from-ide"></a>Uitvoeren vanaf IDE

Als u de webtoepassing vanuit een IDE uitvoert, selecteert u Uitvoeren. Ga vervolgens naar de startpagina van het project. In dit voorbeeld is de standaard-URL voor de startpagina: https://localhost:8443.

1. Selecteer op de voorpagina de knop **Aanmelden** om naar Azure Active Directory om te leiden en de gebruiker om referenties te vragen.

1. Wanneer de gebruiker is geverifieerd, wordt deze omgeleid naar *https://localhost:8443/msal4jsample/secure/aad* . Ze zijn nu aangemeld en op de pagina wordt informatie over het aangemelde account weergegeven. De voorbeeldgebruikersinterface heeft de volgende knoppen:
    - *Afmelden*: Meldt de huidige gebruiker af bij de toepassing en leidt ze om naar de startpagina.
    - *Gebruikersgegevens weergeven*: Haalt een token voor Microsoft Graph op en roept Microsoft Graph aan met een verzoek dat de token bevat, dat informatie retourneert over de aangemelde gebruiker.

##### <a name="running-from-tomcat"></a>Uitvoeren vanaf Tomcat

Als u het webvoorbeeld wilt implementeren op Tomcat, moet u een aantal wijzigingen aanbrengen in de broncode.

1. Open ms-identity-java-webapp/pom.xml
    - Onder `<name>msal-web-sample</name>` voegt u `<packaging>war</packaging>` toe

2. Open ms-identity-java-webapp/src/main/java/com.microsoft.azure.msalwebsample/MsalWebSampleApplication

    - Verwijder alle broncode en vervang deze door het volgende:

   ```Java
    package com.microsoft.azure.msalwebsample;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.boot.builder.SpringApplicationBuilder;
    import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

    @SpringBootApplication
    public class MsalWebSampleApplication extends SpringBootServletInitializer {

     public static void main(String[] args) {
      SpringApplication.run(MsalWebSampleApplication.class, args);
     }

     @Override
     protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
      return builder.sources(MsalWebSampleApplication.class);
     }
    }
   ```

3.   De standaard HTTP-poort van Tomcat is 8080, maar er is een HTTPS-verbinding via poort 8443 nodig. U kunt dit als volgt configureren:
        - Ga naar tomcat/conf/server.xml
        - Zoek het label `<connector>` en vervang de bestaande connector door:

        ```xml
        <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
        ```

4. Open een opdrachtprompt, ga naar de hoofdmap van dit voorbeeld (waar het bestand pom.xml zich bevindt) en voer `mvn package` uit om het project te bouwen
    - Hiermee wordt een `msal-web-sample-0.1.0.war`-bestand in uw map /targets gegenereerd.
    - Geef dit bestand de naam `msal4jsample.war`.
    - Implementeer dit war-bestand met behulp van Tomcat of een andere J2EE-containeroplossing.
        - Voor de implementatie kopieert u het bestand msal4jsample.war naar de map `/webapps/` in uw Tomcat-installatie en start u vervolgens de Tomcat-server.

5. Zodra de implementatie voltooid is, gaat u naar https://localhost:8443/msal4jsample in uw browser.


> [!IMPORTANT]
> Deze quickstarttoepassing gebruikt een clientgeheim om zichzelf te identificeren als vertrouwelijke client. Omdat het clientgeheim als platte tekst aan uw projectbestanden wordt toegevoegd, wordt u om veiligheidsredenen aangeraden een certificaat te gebruiken in plaats van een clientgeheim voordat u de toepassing als productietoepassing beschouwt. Zie [Certificaatreferenties voor toepassingsverificatie](./active-directory-certificate-credentials.md) voor meer informatie over het gebruik van een certificaat.

## <a name="more-information"></a>Meer informatie

### <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt
![Toont hoe de voorbeeld-app werkt die is gegenereerd door deze quickstart](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="getting-msal"></a>MSAL ophalen

MSAL voor Java (MSAL4J) is de Java-bibliotheek die wordt gebruikt voor het aanmelden van gebruikers en de aanvraagtokens die worden gebruikt voor toegang tot een API die is beveiligd via het Microsoft-identiteitsplatform.

Voeg MSAL4J toe aan uw toepassing met behulp van Maven of Gradle om uw afhankelijkheden te beheren door de volgende wijzigingen aan te brengen in het bestand pom.xml (Maven) of build.gradle (Gradle) van de toepassing.

In pom.xml:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

In build.gradle:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>MSAL initialiseren

Voeg een verwijzing toe aan MSAL voor Java door de volgende code toe te voegen bovenaan het bestand waarin u MSAL4J gaat gebruiken:

```Java
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Voor een uitgebreidere bespreking van het bouwen van web-apps waarmee gebruikers zich aanmelden op het Microsoft Identity-platform, gaat u verder naar onze reeks scenario's:

> [!div class="nextstepaction"]
> [Scenario: Web-app waarmee gebruikers worden aangemeld](scenario-web-app-sign-user-overview.md?tabs=java)
