---
title: Apps in de portal configureren
description: Meer informatie over het configureren van algemene instellingen voor een App Service-app in de Azure Portal. App-instellingen, verbindings reeksen, platform, taal stack, container, enzovoort.
keywords: Azure app service, Web-app, app-instellingen, omgevings variabelen
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 08/13/2019
ms.custom: devx-track-csharp, seodec18, devx-track-azurecli
ms.openlocfilehash: 7c1b9ad033ade3b577dc4a06e84801dab3b7eb70
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763958"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Een App Service-app configureren in het Azure Portal

In dit onderwerp wordt uitgelegd hoe u algemene instellingen configureert voor web-apps, mobiele back-end of API-apps met behulp van de [Azure Portal].

## <a name="configure-app-settings"></a>App-instellingen configureren

In App Service zijn app-instellingen variabelen die als omgevings variabelen worden door gegeven aan de toepassings code. Voor Linux-apps en aangepaste containers App Service de app-instellingen door gegeven aan de container met behulp van de `--env` vlag voor het instellen van de omgevings variabele in de container.

In de [Azure Portal]zoekt en selecteert u **app Services** en selecteert u vervolgens uw app. 

![Zoeken naar App Services](./media/configure-common/search-for-app-services.png)

Selecteer in het menu links van de app **configuratie**  >  **Toepassings instellingen**.

![Toepassingsinstellingen](./media/configure-common/open-ui.png)

Voor ASP.NET-en ASP.NET Core-ontwikkel aars is het instellen van de app-instellingen in App Service vergelijkbaar met de instelling in `<appSettings>` in *Web.config* of *appsettings.jsop*, maar de waarden in app service overschrijven ze in *Web.config* of *appsettings.jsop*. U kunt de ontwikkelings instellingen (bijvoorbeeld het lokale MySQL-wacht woord) in *Web.config* of *appsettings.jsop* -en productie geheimen beveiligen (bijvoorbeeld Azure MySQL-database wachtwoord) veilig in app service. Dezelfde code maakt gebruik van uw ontwikkelings instellingen wanneer u lokaal fouten opspoort en uw productie geheimen gebruikt wanneer deze worden geïmplementeerd in Azure.

U kunt ook de app-instellingen ophalen als omgevings variabelen tijdens runtime. Zie voor taalspecifieke stappen voor de taal stack:

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#configure-data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [Aangepaste containers](configure-custom-container.md#configure-environment-variables)

App-instellingen worden altijd versleuteld wanneer ze worden opgeslagen (versleuteld-at-rest).

> [!NOTE]
> App-instellingen kunnen ook worden omgezet van [Key Vault](../key-vault/index.yml) met behulp van [Key Vault-verwijzingen](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Verborgen waarden weer geven

Standaard worden waarden voor app-instellingen in de portal verborgen voor beveiliging. Als u een verborgen waarde van een app-instelling wilt weer geven, klikt u op het veld **waarde** van die instelling. Als u de waarden van alle app-instellingen wilt zien, klikt u op de knop **waarde weer geven** .

### <a name="add-or-edit"></a>Toevoegen of bewerken

Klik op **nieuwe toepassings instelling** om een nieuwe app-instelling toe te voegen. In het dialoog venster kunt u [de instelling op de huidige sleuf plakken](deploy-staging-slots.md#which-settings-are-swapped).

Als u een instelling wilt bewerken, klikt u op de knop **bewerken** aan de rechter kant.

Wanneer u klaar bent, klikt u op **bijwerken**. Vergeet niet om terug te klikken op **Opslaan** op de pagina **configuratie** .

> [!NOTE]
> In een standaard-Linux-container of een aangepaste Linux-container moet elke geneste JSON-sleutel structuur in de naam van de app-instelling, zoals `ApplicationInsights:InstrumentationKey` `ApplicationInsights__InstrumentationKey` voor de sleutel naam, worden geconfigureerd in app service. Met andere woorden, `:` die moeten worden vervangen door `__` (dubbele onderstrepings tekens).
>

### <a name="edit-in-bulk"></a>Bulksgewijs bewerken

Klik op de knop **Geavanceerd bewerken** om de app-instellingen in bulk toe te voegen of te bewerken. Wanneer u klaar bent, klikt u op **bijwerken**. Vergeet niet om terug te klikken op **Opslaan** op de pagina **configuratie** .

App-instellingen hebben de volgende JSON-indeling:

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

### <a name="automate-app-settings-with-the-azure-cli"></a>App-instellingen automatiseren met Azure CLI

U kunt de Azure CLI gebruiken om instellingen te maken en te beheren vanaf de opdracht regel.

- Wijs een waarde toe aan een instelling met [AZ webapp config app Settings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set):

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings <setting-name>="<value>"
    ```
        
    Vervang door `<setting-name>` de naam van de instelling en `<value>` met de waarde die u wilt toewijzen. Met deze opdracht wordt de instelling gemaakt als deze nog niet bestaat.
    
- Alle instellingen en hun waarden weer geven met [AZ webapp config appSettings lijst](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_list):
    
    ```azurecli-interactive
    az webapp config appsettings list --name <app-name> --resource-group <resource-group-name>
    ```
    
- Verwijder een of meer instellingen met [AZ webapp config app Settings delete](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_delete):

    ```azurecli-interactive
    az webapp config appsettings delete --name <app-name> --resource-group <resource-group-name> --setting-names {<names>}
    ```
    
    Vervang door `<names>` een door spaties gescheiden lijst met instellings namen.

## <a name="configure-connection-strings"></a>Verbindingsreeksen configureren

In de [Azure Portal]zoekt en selecteert u **app Services** en selecteert u vervolgens uw app. Selecteer in het menu links van de app **configuratie**  >  **Toepassings instellingen**.

![Toepassingsinstellingen](./media/configure-common/open-ui.png)

Voor ASP.NET-en ASP.NET Core-ontwikkel aars is het instellen van verbindings reeksen in App Service vergelijkbaar met de instelling in `<connectionStrings>` in *Web.config*, maar de waarden die u in app service instelt, worden in *Web.config* genegeerd. U kunt de ontwikkelings instellingen (bijvoorbeeld een database bestand) in *Web.config* -en productie geheimen (bijvoorbeeld SQL database referenties) veilig in app service blijven. Dezelfde code maakt gebruik van uw ontwikkelings instellingen wanneer u lokaal fouten opspoort en uw productie geheimen gebruikt wanneer deze worden geïmplementeerd in Azure.

Voor andere taal stacks is het beter om de [app-instellingen](#configure-app-settings) te gebruiken, omdat verbindings reeksen speciale opmaak in de variabele sleutels nodig hebben om toegang te krijgen tot de waarden. Hier volgt één uitzonde ring: voor bepaalde typen van Azure-data bases wordt samen met de app een back-up gemaakt als u de verbindings reeksen in uw app configureert. Zie [waarvan een back-up wordt gemaakt](manage-backup.md#what-gets-backed-up)voor meer informatie. Als u deze geautomatiseerde back-up niet nodig hebt, gebruikt u de app-instellingen.

In runtime zijn verbindings reeksen beschikbaar als omgevings variabelen, met als voor voegsel de volgende verbindings typen:

* Server `SQLCONNSTR_`  
* MySQL `MYSQLCONNSTR_` 
* SQLAzure: `SQLAZURECONNSTR_` 
* Instel `CUSTOMCONNSTR_`
* PostgreSQL `POSTGRESQLCONNSTR_`  

Bijvoorbeeld, een MySql-connection string met de naam *connectionstring1* kan worden gebruikt als de omgevings variabele `MYSQLCONNSTR_connectionString1` . Zie voor taalspecifieke stappen voor de taal stack:

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#configure-data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [Aangepaste containers](configure-custom-container.md#configure-environment-variables)

Verbindings reeksen worden altijd versleuteld wanneer ze worden opgeslagen (versleuteld-at-rest).

> [!NOTE]
> Verbindings reeksen kunnen ook worden omgezet van [Key Vault](../key-vault/index.yml) met behulp van [Key Vault-verwijzingen](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Verborgen waarden weer geven

Standaard worden waarden voor verbindingsteken reeksen in de portal verborgen voor beveiliging. Als u een verborgen waarde van een connection string wilt weer geven, klikt u op het veld **waarde** van die teken reeks. Als u de waarden van alle verbindings reeksen wilt zien, klikt u op de knop **waarde weer geven** .

### <a name="add-or-edit"></a>Toevoegen of bewerken

Als u een nieuwe connection string wilt toevoegen, klikt u op **nieuw Connection String**. In het dialoog venster kunt u [de Connection String aan de huidige sleuf aansteken](deploy-staging-slots.md#which-settings-are-swapped).

Als u een instelling wilt bewerken, klikt u op de knop **bewerken** aan de rechter kant.

Wanneer u klaar bent, klikt u op **bijwerken**. Vergeet niet om terug te klikken op **Opslaan** op de pagina **configuratie** .

### <a name="edit-in-bulk"></a>Bulksgewijs bewerken

Klik op de knop **Geavanceerd bewerken** om verbindings reeksen in bulk toe te voegen of te bewerken. Wanneer u klaar bent, klikt u op **bijwerken**. Vergeet niet om terug te klikken op **Opslaan** op de pagina **configuratie** .

Verbindings reeksen hebben de volgende JSON-indeling:

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>Algemene instellingen configureren

In de [Azure Portal]zoekt en selecteert u **app Services** en selecteert u vervolgens uw app. Selecteer **Configuration**  >  **algemene instellingen** configuratie in het menu van de app.

![Algemene instellingen](./media/configure-common/open-general.png)

Hier kunt u enkele algemene instellingen voor de app configureren. Voor sommige instellingen moet u [Omhoog schalen naar een hogere prijs categorie](manage-scale-up.md).

- **Stack-instellingen**: de software stack voor het uitvoeren van de app, met inbegrip van de taal-en SDK-versies.

    Voor Linux-apps en aangepaste container-apps kunt u de taal runtime versie selecteren en een optionele **opstart opdracht** of een opstart opdracht bestand instellen.

    ![Algemene instellingen voor Linux-containers](./media/configure-common/open-general-linux.png)

- **Platform instellingen**: Hiermee kunt u instellingen configureren voor het hosting platform, waaronder:
    - **Bitness**: 32-bits of 64-bits.
    - **WebSocket-protocol**: voor [ASP.net-signa lering] of [socket.io](https://socket.io/), bijvoorbeeld.
    - **Altijd aan**: houdt de app geladen, zelfs wanneer er geen verkeer is. Het is vereist voor doorlopende webjobs of voor webjobs die worden geactiveerd met behulp van een CRON-expressie.
      > [!NOTE]
      > Met de functie Always on verzendt de front-end load balancer een aanvraag naar de hoofdmap van de toepassing. Dit toepassings eindpunt van de App Service kan niet worden geconfigureerd.
    - **Beheerde pijplijn versie**: de IIS- [pipeline-modus]. Stel deze in op **klassiek** als u een verouderde app hebt waarvoor een oudere versie van IIS vereist is.
    - **Http-versie**: ingesteld op **2,0** om ondersteuning voor [https/2-](https://wikipedia.org/wiki/HTTP/2) protocol in te scha kelen.
    > [!NOTE]
    > De meeste moderne browsers bieden alleen ondersteuning voor HTTP/2-protocollen via TLS, terwijl niet-versleuteld verkeer HTTP/1.1 blijft gebruiken. Beveilig uw aangepaste DNS-naam om ervoor te zorgen dat client browsers verbinding maken met uw app met HTTP/2. Zie [een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in azure app service](configure-ssl-bindings.md)voor meer informatie.
    - **ARR-affiniteit**: in een implementatie met meerdere exemplaren moet u ervoor zorgen dat de client voor de levens duur van de sessie wordt doorgestuurd naar hetzelfde exemplaar. U kunt deze optie instellen op **uitschakelen** voor stateless toepassingen.
- **Fout opsporing**: externe fout opsporing inschakelen voor [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug)-, [ASP.net core](/visualstudio/debugger/remote-debugging-azure)-of [Node.js](configure-language-nodejs.md#debug-remotely) -apps. Deze optie wordt na 48 uur automatisch uitgeschakeld.
- **Binnenkomende client certificaten**: client certificaten in [wederzijdse verificatie](app-service-web-configure-tls-mutual-auth.md)vereisen.

## <a name="configure-default-documents"></a>Standaard documenten configureren

Deze instelling geldt alleen voor Windows-apps.

In de [Azure Portal]zoekt en selecteert u **app Services** en selecteert u vervolgens uw app. Selecteer in het menu links de optie **configuratie**  >  **standaard documenten**.

![Standaard documenten](./media/configure-common/open-documents.png)

Het standaard document is de webpagina die wordt weer gegeven op de basis-URL voor een website. Het eerste overeenkomende bestand in de lijst wordt gebruikt. Als u een nieuw standaard document wilt toevoegen, klikt u op **Nieuw document**. Vergeet niet op **Opslaan** te klikken.

Als de app gebruikmaakt van modules die worden gerouteerd op basis van URL in plaats van statische inhoud, is er geen behoefte aan standaard documenten.

## <a name="configure-path-mappings"></a>Paden configureren

In de [Azure Portal]zoekt en selecteert u **app Services** en selecteert u vervolgens uw app. Selecteer in het menu links van de app **configuratie**  >  **paden toewijzen**.

![Paden toewijzen](./media/configure-common/open-path.png)

Op de pagina **paden toewijzen** worden verschillende dingen weer gegeven op basis van het type besturings systeem.

### <a name="windows-apps-uncontainerized"></a>Windows-apps (niet in container)

Voor Windows-apps kunt u de IIS-handlertoewijzing aanpassen en virtuele toepassingen en mappen.

Met handlertoewijzing kunt u aangepaste script processors toevoegen voor het afhandelen van aanvragen voor specifieke bestands extensies. Als u een aangepaste handler wilt toevoegen, klikt u op **nieuwe handler**. Configureer de handler als volgt:

- **Extensie**. De bestands extensie die u wilt verwerken, zoals *\* . php* of *handler. fcgi*.
- **Script processor**. Het absolute pad van de script processor naar u. Aanvragen voor bestanden die overeenkomen met de bestands extensie worden verwerkt door de script processor. Gebruik het pad `D:\home\site\wwwroot` om te verwijzen naar de hoofdmap van uw app.
- **Argumenten**. Optionele opdracht regel argumenten voor de script processor.

Aan elke app wordt het standaard hoofdpad ( `/` ) toegewezen `D:\home\site\wwwroot` , waar uw code standaard wordt geïmplementeerd. Als de hoofdmap van de app zich in een andere map bevindt, of als uw opslag plaats meer dan één toepassing heeft, kunt u hier virtuele toepassingen en directory's bewerken of toevoegen. Klik op **nieuwe virtuele toepassing of directory**.

Als u virtuele toepassingen en directory's wilt configureren, geeft u elke virtuele map en het bijbehorende fysieke pad op ten opzichte van de hoofdmap van de website ( `D:\home` ). U kunt eventueel het selectie vakje van de **toepassing** selecteren om een virtuele map als een toepassing te markeren.

### <a name="containerized-apps"></a>Apps in de container

U kunt [aangepaste opslag voor uw container-app toevoegen](configure-connect-to-azure-storage.md). Apps in containers bevatten alle Linux-apps en ook de aangepaste Windows-en Linux-containers die worden uitgevoerd op App Service. Klik op **nieuw Azure Storage** uw aangepaste opslag als volgt te koppelen en te configureren:

- **Naam**: de weergave naam.
- **Configuratie opties**: **Basic** of **Advanced**.
- **Opslag accounts**: het opslag account met de gewenste container.
- **Opslag type**: **Azure-blobs** of- **Azure files**.
  > [!NOTE]
  > Windows-container-apps ondersteunen alleen Azure Files.
- **Opslag container**: voor de basis configuratie is de gewenste container.
- **Share naam**: voor geavanceerde configuratie, de naam van de bestands share.
- **Toegangs sleutel**: voor geavanceerde configuratie, de toegangs sleutel.
- **Koppelingspad**: het absolute pad in uw container om de aangepaste opslag te koppelen.

Zie [toegang Azure Storage als een netwerk share vanuit een container in app service](configure-connect-to-azure-storage.md)voor meer informatie.

## <a name="configure-language-stack-settings"></a>Taal stack-instellingen configureren

Voor Linux-apps raadpleegt u:

- [ASP.NET Core](configure-language-dotnetcore.md)
- [Node.js](configure-language-nodejs.md)
- [PHP](configure-language-php.md)
- [Python](configure-language-python.md)
- [Java](configure-language-java.md)
- [Ruby](configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Aangepaste containers configureren

Zie [een aangepaste Linux-container configureren voor Azure app service](configure-custom-container.md)

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste domein naam configureren in Azure App Service]
- [Faseringsomgevingen in Azure App Service instellen]
- [Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service](configure-ssl-bindings.md)
- [Diagnostische logboeken inschakelen](troubleshoot-diagnostic-logs.md)
- [Een app schalen in Azure App Service]
- [Basis principes controleren in Azure App Service]
- [applicationHost.config-instellingen wijzigen met applicationHost. xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET-Signa lering]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Een aangepaste domein naam configureren in Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Faseringsomgevingen in Azure App Service instellen]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: ./web-sites-monitor.md
[Basis principes controleren in Azure App Service]: ./web-sites-monitor.md
[pijplijn modus]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Een app schalen in Azure App Service]: ./manage-scale-up.md
