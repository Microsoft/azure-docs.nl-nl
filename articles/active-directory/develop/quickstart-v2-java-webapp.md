---
title: 'Snelstart: Aanmelden met Microsoft toevoegen aan een Java-webapp | Azure'
titleSuffix: Microsoft identity platform
description: In deze quickstart leert u hoe u aanmelden bij Microsoft toevoegt in een Java-webtoepassing met behulp van OpenID Connect.
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
ms.openlocfilehash: a7337175241834cef862b4af07c7bcf7c8b845d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103767"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Quickstart: Aanmelden met Microsoft toevoegen aan een Java-webapp

In deze quickstart downloadt u een codevoorbeeld en voert u dit uit. Het codevoorbeeld laat zien hoe gebruikers kunnen worden aangemeld en de Microsoft Graph kunnen aanroepen met een Java-webtoepassing. Gebruikers uit elke willekeurige Azure AD-organisatie (Azure Active Directory) kunnen zich aanmelden bij de toepassing.

 Zie het [diagram met de werking van het voorbeeld](#how-the-sample-works) voor een overzicht.

## <a name="prerequisites"></a>Vereisten

Als u dit voorbeeld wilt uitvoeren, hebt u het volgende nodig:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 of nieuwer. 
- [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>De quickstart-app registreren en downloaden
> Er zijn twee manieren waarop u de quickstart-toepassing kunt starten: express (optie 1) en handmatig (optie 2).
>
> ### <a name="option-1-register-and-automatically-configure-your-app-and-then-download-the-code-sample"></a>Optie 1: Registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld
>
> 1. Ga naar de quickstart-ervaring <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs" target="_blank">Azure-portal - App-registraties</a>.
> 1. Voer een naam in voor de toepassing en selecteer dan **Registreren**.
> 1. Volg de instructies in de quickstart van het portal om de automatisch geconfigureerde toepassingscode te downloaden.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
>
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen:
>
> 1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
> 1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement**:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u de toepassing wilt registreren.
> 1. Zoek en selecteer de optie **Azure Active Directory**.
> 1. Selecteer **App-registraties** onder **Beheren**.
> 1. Selecteer **Nieuwe registratie**.
> 1. Voer een **Naam** in voor de toepassing, bijvoorbeeld **java-webapp**. Gebruikers van uw app kunnen deze naam zien. U kunt deze later wijzigen.
> 1. Selecteer **Registreren**.
> 1. Noteer op de pagina **Overzicht** de **toepassings-id (client)** en de **map-id (tenant)** . U hebt deze waarden later nodig.
> 1. Selecteer **Verificatie** onder **Beheren**.
> 1. Selecteer **Een platform toevoegen** > **Web**.
> 1. Voer in de sectie **Omleidings-URI's** `https://localhost:8443/msal4jsample/secure/aad` in.
> 1. Selecteer **Configureren**.
> 1. Voer in de sectie **Web**, onder **Omleidings-URI's**, `https://localhost:8443/msal4jsample/graph/me` in als een tweede omleidings-URI.
> 1. Selecteer onder **Beheren** de optie **Certificaten en geheimen**. Selecteer in de sectie **Clientgeheimen** de optie **Nieuw clientgeheim**.
> 1. Voer een beschrijving voor de sleutel in (bijvoorbeeld *app-geheim*), laat de standaardvervaldatum staan, en selecteer **Toevoegen**.
> 1. Noteer de **waarde** van het clientgeheim. U hebt deze later nodig.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in Azure Portal
>
> Gebruik het code voorbeeld in deze Snelstartgids:
>
> 1. Voeg de antwoord-URL's `https://localhost:8443/msal4jsample/secure/aad` en `https://localhost:8443/msal4jsample/graph/me` toe.
> 1. Maak een clientgeheim.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-aspnet-webapp/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-code-sample"></a>Stap 2: Het codevoorbeeld downloaden
> [!div renderon="docs"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Download het project en pak het ZIP-bestand uit in een map in de buurt van de hoofdmap van uw station. Bijvoorbeeld *C:\Azure-Samples*.
>
> Als u HTTPS voor localhost wilt gebruiken, geeft u de `server.ssl.key`-eigenschappen op. Gebruik het keytool-hulpprogramma om een zelfondertekend certificaat te maken (opgenomen in JRE).
>
> Hier volgt een voorbeeld:
>  ```
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Plaats het gegenereerde KeyStore-bestand in de map *resources*.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>Stap 3: Het codevoorbeeld configureren
> 1. Pak het zip-bestand uit naar lokale map.
> 1. *Optioneel.* Als u een Integrated Development Environment gebruikt, opent u het voorbeeld in die omgeving.
> 1. Open het bestand *application.properties*. U vindt dit in de map *src/main/resources/* . Vervang de waarden in de velden `aad.clientId`, `aad.authority` en `aad.secretKey` door respectievelijk de toepassings-id, de tenant-id en de waarden van het clientgeheim. Zo moet het er nu uitzien:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
>    In de vorige code:
>
>    - `Enter_the_Application_Id_here` is de toepassings-id voor de toepassing die u hebt geregistreerd.
>    - `Enter_the_Client_Secret_Here` is het **clientgeheim** dat u in **Certificaten en geheimen** hebt gemaakt voor de toepassing die u hebt geregistreerd.
>    - `Enter_the_Tenant_Info_Here` is de waarde voor **Map-id (tenant)** van de toepassing die u hebt geregistreerd.
> 1. Als u HTTPS voor localhost wilt gebruiken, geeft u de `server.ssl.key`-eigenschappen op. Gebruik het keytool-hulpprogramma om een zelfondertekend certificaat te maken (opgenomen in JRE).
>
>    Hier volgt een voorbeeld:
>
>     ```
>      keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>      server.ssl.key-store-type=PKCS12
>      server.ssl.key-store=classpath:keystore.p12
>      server.ssl.key-store-password=password
>      server.ssl.key-alias=testCert
>      ```
>   1. Plaats het gegenereerde KeyStore-bestand in de map *resources*.


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Stap 3: Het codevoorbeeld uitvoeren
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Stap 4: Het codevoorbeeld uitvoeren

Voer een van de volgende stappen uit om het project uit te voeren:

- Voer het rechtstreeks vanuit uw IDE uit met behulp van de ingesloten Spring Boot-server. 
- Verpak het in een WAR-bestand met behulp van [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) en implementeer het in een J2EE-containeroplossing, zoals [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-the-project-from-an-ide"></a>Het project uitvoeren vanuit een IDE

Als u de webtoepassing vanuit een IDE wilt uitvoeren, selecteert u Uitvoeren en gaat u naar de startpagina van het project. In dit voorbeeld is de standaard-URL voor de startpagina: https://localhost:8443.

1. Selecteer op de voorpagina de knop **Aanmelden** om gebruikers om te leiden naar Azure Active Directory en ze om referenties te vragen.

1. Nadat de gebruikers zijn geverifieerd, worden ze omgeleid naar `https://localhost:8443/msal4jsample/secure/aad`. Ze zijn nu aangemeld en op de pagina wordt informatie over het gebruikersaccount weergegeven. De voorbeeld-UI heeft deze knoppen:
    - **Afmelden**: Hiermee wordt de huidige gebruiker afgemeld bij de toepassing en omgeleid naar de startpagina.
    - **Gebruikersgegevens weergeven**: Hiermee wordt een token voor Microsoft Graph opgehaald en wordt Microsoft Graph aangeroepen met een verzoek dat de token bevat, dat informatie retourneert over de aangemelde gebruiker.

##### <a name="running-the-project-from-tomcat"></a>Het project uitvoeren vanuit Tomcat

Als u het webvoorbeeld wilt implementeren op Tomcat, brengt u een paar wijzigingen aan in de bron code.

1. Open *ms-identity-java-webapp/pom.xml*.
    - Onder `<name>msal-web-sample</name>` voegt u `<packaging>war</packaging>` toe.

2. Open *ms-identity-java-webapp/src/main/java/com.microsoft.azure.msalwebsample/MsalWebSampleApplication*.

    - Verwijder alle broncode en vervang deze door deze code:

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

3.   De standaard HTTP-poort van Tomcat is 8080, maar er is een HTTPS-verbinding via poort 8443 nodig. U kunt deze instelling als volgt configureren:
        - Ga naar *tomcat/conf/server.xml*.
        - Zoek de tag `<connector>` en vervang de bestaande connector door deze connector:

          ```xml
          <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
          ```

4. Open een opdrachtpromptvenster. Ga naar de hoofdmap van dit voorbeeld (waar het bestand pom.xml zich bevindt) en voer `mvn package` uit om het project te bouwen.
    - Met deze opdracht wordt een *msal-web-sample-0.1.0.war*-bestand in de map */targets* gegenereerd.
    - Wijzig de naam van dit bestand in *msal4jsample.war*.
    - Implementeer dit WAR-bestand met behulp van Tomcat of een andere J2EE-containeroplossing.
        - Voor de implementatie van bestand msal4jsample.war kopieert u het bestand naar de map */webapps/* in uw Tomcat-installatie en start u de Tomcat-server.

5. Nadat het bestand is geïmplementeerd, gaat u in een browser naar https://localhost:8443/msal4jsample.


> [!IMPORTANT]
> Deze quickstarttoepassing gebruikt een clientgeheim om zichzelf te identificeren als een vertrouwelijke client. Omdat het clientgeheim als platte tekst aan uw projectbestanden wordt toegevoegd, wordt u om veiligheidsredenen aangeraden een certificaat te gebruiken in plaats van een clientgeheim voordat u de toepassing in een productieomgeving gebruikt. Zie [Certificaatreferenties voor toepassingsverificatie](./active-directory-certificate-credentials.md) voor meer informatie over het gebruik van een certificaat.

## <a name="more-information"></a>Meer informatie

### <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt
![Diagram waarin wordt getoond hoe de voorbeeld-app werkt die is gegenereerd in deze snelstart.](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="get-msal"></a>MSAL ophalen

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

### <a name="initialize-msal"></a>MSAL initialiseren

Voeg een verwijzing toe aan MSAL voor Java door de volgende code toe te voegen aan het begin van het bestand waarin u MSAL4J gaat gebruiken:

```Java
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Voor een uitgebreidere bespreking van het bouwen van web-apps waarmee gebruikers zich aanmelden op het Microsoft Identity-platform, raadpleegt u onze reeks scenario's:

> [!div class="nextstepaction"]
> [Scenario: Web-app waarmee gebruikers worden aangemeld](scenario-web-app-sign-user-overview.md?tabs=java)
