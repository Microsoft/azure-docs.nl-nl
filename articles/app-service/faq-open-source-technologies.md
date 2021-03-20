---
title: Veelgestelde vragen over open-source technologieën
description: Krijg antwoorden op veelgestelde vragen over open-source technologieën in Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 36dfbf0fda060a8f273fee64098d6234b575088c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97831836"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Open-source technologieën Veelgestelde vragen over Web Apps in azure

In dit artikel vindt u antwoorden op veelgestelde vragen over problemen met open-source technologieën voor de [Web apps functie van Azure app service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Hoe kan ik PHP-logboek registratie inschakelen om PHP-problemen op te lossen?

PHP-logboek registratie inschakelen:

1. Meld u aan bij uw **kudu-website** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. Selecteer in het bovenste menu de optie **debug console**  >  **cmd**.
3. Selecteer de map van de **site** .
4. Selecteer de map **wwwroot** .
5. Selecteer het **+** pictogram en selecteer vervolgens **nieuw bestand**.
6. Stel de bestands naam in op **.user.ini**.
7. Selecteer het potlood pictogram naast **.user.ini**.
8. Voeg in het bestand de volgende code toe: `log_errors=on`
9. Selecteer **Opslaan**.
10. Selecteer het potlood pictogram naast **wp-config. php**.
11. Wijzig de tekst in de volgende code:
    ```php
    //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
    //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
    ```
12. Start uw web-app in het menu van de web-app in het Azure Portal.

Zie voor meer informatie [WordPress-fouten logboeken inschakelen](/archive/blogs/azureossds/logging-php-errors-in-wordpress-2).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Hoe kan ik python-toepassings fouten vastleggen in apps die worden gehost in App Service?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Hoe kan ik de versie wijzigen van de Node.js toepassing die wordt gehost in App Service?

Als u de versie van de Node.js toepassing wilt wijzigen, kunt u een van de volgende opties gebruiken:

* Gebruik in de Azure Portal **app-instellingen**.
  1. Ga in het Azure Portal naar uw web-app.
  2. Selecteer **Toepassings instellingen** op de Blade **instellingen** .
  3. In **app-instellingen** kunt u WEBSITE_NODE_DEFAULT_VERSION als de sleutel en de versie van Node.js u als de waarde wilt gebruiken.
  4. Ga naar uw **kudu-console** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
  5. Als u de Node.js versie wilt controleren, voert u de volgende opdracht in:  
     ```
     node -v
     ```
* Wijzig het bestand iisnode. yml. Als u de Node.js versie in het bestand iisnode. yml wijzigt, wordt alleen de runtime-omgeving ingesteld die iisnode gebruikt. Uw kudu-cmd en andere gebruiken nog steeds de Node.js versie die is ingesteld in de **app-instellingen** in de Azure Portal.

  Als u de iisnode. yml hand matig wilt instellen, maakt u een iisnode. yml-bestand in de hoofdmap van de app. Voeg in het bestand de volgende regel toe:
  ```yml
  nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
  ```
   
* Stel het bestand iisnode. yml in met behulp van package.jstijdens de implementatie van broncode beheer.
  Het implementatie proces van het Azure-bron beheer omvat de volgende stappen:
  1. Hiermee verplaatst u inhoud naar de Azure-web-app.
  2. Hiermee maakt u een standaard implementatie script als er zich geen (Deploy. cmd,. Deployment-bestanden) in de hoofdmap van de web-app bevindt.
  3. Voert een implementatie script uit waarbij het een iisnode. yml-bestand maakt als u de Node.js versie in de package.jsin de engine van file > vermeldt `"engines": {"node": "5.9.1","npm": "3.7.3"}`
  4. Het bestand iisnode. yml heeft de volgende regel code:
      ```yml
      nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
      ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>Ik zie het bericht ' fout bij het maken van een database verbinding ' in de WordPress-app die wordt gehost in App Service. Hoe kan ik dit probleem oplossen?

Als deze fout wordt weer geven in de Azure WordPress-app, kunt u php_errors. log en debug. log uitvoeren door de stappen uit te voeren die worden beschreven in [WordPress-fouten logboeken inschakelen](/archive/blogs/azureossds/logging-php-errors-in-wordpress-2).

Als de logboeken zijn ingeschakeld, reproduceert u de fout en raadpleegt u de logboeken om te controleren of er verbinding is met de volgende:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Als deze fout wordt weer geven in de bestanden debug. log of php_errors. log, overschrijdt uw app het aantal verbindingen. Als u host bent op ClearDB, controleert u het aantal verbindingen dat beschikbaar is in uw [service-abonnement](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Hoe kan ik fouten opsporen in een Node.js-app die wordt gehost in App Service?

1.  Ga naar uw **kudu-console** ( `https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole` ).
2.  Ga naar de map met toepassings Logboeken (D:\home\LogFiles\Application).
3.  Controleer in het logging_errors.txt-bestand op inhoud.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Hoe kan ik systeem eigen python-modules installeren in een App Service web-app of API-app?

Sommige pakketten kunnen niet worden geïnstalleerd met behulp van PIP in Azure. Mogelijk is het pakket niet beschikbaar op de python-pakket index of is een compiler vereist (er is geen compiler beschikbaar op de computer waarop de web-app wordt uitgevoerd in App Service). Zie [python-modules installeren in app service](/archive/blogs/azureossds/install-native-python-modules-on-azure-web-apps-api-apps)voor meer informatie over het installeren van systeem eigen modules in app service Web apps en API apps.

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Hoe kan ik een Django-app implementeren op App Service met behulp van Git en de nieuwe versie van python?

Zie [Deploying a Django app to app service](/archive/blogs/azureossds/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python)voor informatie over het installeren van Django.

## <a name="where-are-the-tomcat-log-files-located"></a>Waar bevinden zich de logboek bestanden van de Tomcat?

Voor Azure Marketplace en aangepaste implementaties:

* Maplocatie: D:\home\site\wwwroot\bin\apache-Tomcat-8.0.33\logs
* Interessante bestanden:
    * catalina. *jjjj-mm-dd*. log
    * host-Manager. *jjjj-mm-dd*. log
    * lokalehost. *jjjj-mm-dd*. log
    * Manager. *jjjj-mm-dd*. log
    * site_access_log. *jjjj-mm-dd*. log


Voor implementaties van de portal- **app-instellingen** :

* Maplocatie: D:\home\LogFiles
* Interessante bestanden:
    * catalina. *jjjj-mm-dd*. log
    * host-Manager. *jjjj-mm-dd*. log
    * lokalehost. *jjjj-mm-dd*. log
    * Manager. *jjjj-mm-dd*. log
    * site_access_log. *jjjj-mm-dd*. log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Problemen met de verbindings fouten van het JDBC-stuur programma Hoe kan ik oplossen?

Mogelijk wordt het volgende bericht weer gegeven in uw Tomcat-logboeken:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Om de fout op te lossen:

1. Verwijder het bestand sqljdbc*. jar uit de map app/lib.
2. Als u de aangepaste Tomcat of de Tomcat-webserver van Azure Marketplace gebruikt, kopieert u dit jar-bestand naar de map tomcat lib.
3. Als u Java inschakelt vanuit de Azure Portal (Selecteer **Java 1,8**  >  **Tomcat-server**), kopieert u het bestand sqljdbc. * in de map die parallel is met uw app. Voeg vervolgens de volgende Classpath-instelling toe aan het web.config-bestand:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Waarom worden fouten weer geven wanneer ik live-logboek bestanden probeer te kopiëren?

Als u probeert live-logboek bestanden te kopiëren voor een Java-app (bijvoorbeeld Tomcat), ziet u mogelijk de volgende FTP-fout:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

Het fout bericht kan variëren, afhankelijk van de FTP-client.

Alle Java-apps hebben dit vergrendelings probleem. Alleen kudu ondersteunt het downloaden van dit bestand tijdens de uitvoering van de app.

Als de app wordt gestopt, is FTP-toegang tot deze bestanden toegestaan.

Een andere oplossing is het schrijven van een Webtaak die volgens een schema wordt uitgevoerd en deze bestanden kopieert naar een andere map. Zie het [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) -project voor een voorbeeld project.

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Waar vind ik de logboek bestanden voor jetty?

Voor Marketplace en aangepaste implementaties bevindt het logboek bestand zich in de map D:\home\site\wwwroot\bin\jetty-Distribution-9.1.2.v20140210\logs. Houd er rekening mee dat de maplocatie afhankelijk is van de versie van Jetty die u gebruikt. Het pad dat hier wordt vermeld, is bijvoorbeeld voor jetty 9.1.2. Zoek naar jetty_ *YYYY_MM_DD*. stderrout. log.

Voor implementaties van portal-app-instellingen is het logboek bestand in D:\home\LogFiles. Zoek naar jetty_ *YYYY_MM_DD*. stderrout. log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Kan ik een e-mail verzenden vanuit mijn Azure-web-app?

App Service heeft geen ingebouwde e-mail functie. Zie deze [stack overflow discussie](https://stackoverflow.com/questions/17666161/sending-email-from-azure)voor een aantal goede alternatieven voor het verzenden van e-mail vanuit uw app.

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Waarom leidt mijn WordPress-site naar een andere URL?

Als u onlangs naar Azure hebt gemigreerd, kan WordPress omleiden naar de oude domein-URL. Dit wordt veroorzaakt door een instelling in de MySQL-data base.

WordPress Buddy + is een Azure-site-uitbrei ding die u kunt gebruiken om de omleidings-URL rechtstreeks in de-data base bij te werken. Zie [WordPress-hulpprogram ma's en MySQL-migratie met WordPress Buddy +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows)voor meer informatie over het gebruik van WordPress buddy +.

Als u de omleidings-URL liever hand matig bijwerkt met behulp van SQL-query's of PHPMyAdmin, raadpleegt u [WordPress: omleiden naar de verkeerde URL](/archive/blogs/azureossds/wordpress-redirecting-to-wrong-url).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Hoe kan ik mijn WordPress-aanmeldings wachtwoord wijzigen?

Als u het aanmeldings wachtwoord van WordPress hebt verg eten, kunt u WordPress Buddy + gebruiken om het wacht woord bij te werken. Als u uw wacht woord opnieuw wilt instellen, installeert u de WordPress Buddy + Azure-site-extensie en voert u de stappen uit die worden beschreven in [WordPress-hulpprogram ma's en MySQL-migratie met WordPress Buddy +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Ik kan me niet aanmelden bij WordPress. Hoe los ik dit op?

Als u zichzelf hebt geblokkeerd na de laatste keer dat u een invoeg toepassing hebt geïnstalleerd, hebt u een defecte invoeg toepassing. WordPress Buddy + is een Azure-site-uitbrei ding die u kan helpen om invoeg toepassingen uit te scha kelen in WordPress. Zie voor meer informatie [WordPress-hulpprogram ma's en MySQL-migratie met WordPress Buddy +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Hoe kan ik de WordPress-data base te migreren?

U hebt meerdere opties voor het migreren van de MySQL-data base die is verbonden met uw WordPress-website:

* Ontwikkel aars: gebruik de [opdracht prompt of phpMyAdmin](/archive/blogs/azureossds/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service)
* Niet-ontwikkel aars: gebruik [WordPress Buddy +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Hoe kan ik kunt u WordPress veiliger maken?

Zie [Aanbevolen procedures voor WordPress-beveiliging in azure](/archive/blogs/azureossds/best-practices-for-wordpress-security-on-azure)voor meer informatie over aanbevolen beveiligings procedures voor WordPress.

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Ik probeer PHPMyAdmin te gebruiken en het bericht ' toegang geweigerd ' wordt weer gegeven. Hoe los ik dit op?

Dit probleem kan optreden als de functie MySQL in-app nog niet wordt uitgevoerd in dit App Service exemplaar. Probeer het probleem op te lossen door toegang te krijgen tot uw website. Hiermee worden de vereiste processen gestart, inclusief het MySQL in-app proces. Als u wilt controleren of MySQL in-app wordt uitgevoerd, controleert u in Process Explorer of mysqld.exe wordt weer gegeven in de processen.

Nadat u hebt gezorgd dat MySQL in-app actief is, probeert u PHPMyAdmin te gebruiken.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Er wordt een HTTP 403-fout weer geven wanneer ik mijn MySQL in-app-data base probeer te importeren of exporteren met behulp van PHPMyadmin. Hoe los ik dit op?

Als u een oudere versie van Chrome gebruikt, ondervindt u mogelijk een bekende fout. Om het probleem op te lossen, voert u een upgrade uit naar een nieuwere versie van Chrome. Probeer ook een andere browser te gebruiken, zoals Internet Explorer of micro soft Edge, waar het probleem zich niet voordoet.