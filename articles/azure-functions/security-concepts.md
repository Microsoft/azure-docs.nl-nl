---
title: Azure Functions beveiligen
description: Meer informatie over hoe u de functie code die wordt uitgevoerd in azure beter kunt beveiligen tegen veelvoorkomende aanvallen.
ms.date: 4/13/2020
ms.topic: conceptual
ms.openlocfilehash: 351bdca7ff94b6c058b5ab62fd9c16d707e7dc78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368486"
---
# <a name="securing-azure-functions"></a>Azure Functions beveiligen

In veel gevallen is het plannen voor veilige ontwikkeling, implementatie en werking van serverloze functies veel hetzelfde als voor elke webtoepassing of gehoste toepassingen in de Cloud. [Azure app service](../app-service/index.yml) biedt de hosting-infra structuur voor uw functie-apps. Dit artikel bevat beveiligings strategieën voor het uitvoeren van uw functie code en hoe App Service u kunt helpen bij het beveiligen van uw functies. 

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

Zie [Azure-beveiligings basislijn voor Azure functions](security-baseline.md)voor een aantal beveiligings aanbevelingen die volgen op de [beveiligings benchmark van Azure](../security/benchmarks/overview.md).

## <a name="secure-operation"></a>Beveiligde bewerking 

In deze sectie vindt u meer over het configureren en uitvoeren van uw functie-app zo veilig mogelijk. 

### <a name="security-center"></a>Beveiligingscentrum

Security Center integreert met uw functie-app in de portal. Het biedt gratis een snelle evaluatie van mogelijke beveiligings problemen met betrekking tot configuraties. Functie-apps die in een specifiek abonnement worden uitgevoerd, kunnen ook gebruikmaken van de real-time beveiligings functies van Security Center, voor extra kosten. Zie [uw Azure app service Web-apps en Api's beveiligen](../security-center/defender-for-app-service-introduction.md)voor meer informatie. 

### <a name="log-and-monitor"></a>Logboeken en bewaking

Een manier om aanvallen te detecteren is via activiteiten voor activiteit bewaking en logboek registratie. Functies worden geïntegreerd met Application Insights om logboek-, prestatie-en fout gegevens voor uw functie-app te verzamelen. Application Insights detecteert automatisch prestatie afwijkingen en bevat krachtige analyse hulpprogramma's waarmee u problemen kunt vaststellen en inzicht krijgt in de manier waarop uw functies worden gebruikt. Zie [Azure functions bewaken](functions-monitoring.md)voor meer informatie.

Functies kunnen ook worden geïntegreerd met Azure Monitor logboeken, zodat u de logboeken van de functie-app kunt samen voegen met systeem gebeurtenissen voor een eenvoudige analyse. U kunt Diagnostische instellingen gebruiken voor het configureren van het exporteren van streaming van platform logboeken en metrische gegevens voor uw functies naar het doel van uw keuze, zoals een werk ruimte voor logboek analyse. Zie [Azure functions bewaken met Azure monitor-logboeken](functions-monitor-log-analytics.md)voor meer informatie. 

Voor detectie van bedreigingen en reacties op bedrijfs niveau kunt u uw logboeken en gebeurtenissen streamen naar een werk ruimte voor logboek analyse. U kunt vervolgens Azure Sentinel verbinden met deze werk ruimte. Zie [Wat is Azure Sentinel](../sentinel/overview.md)? voor meer informatie.  

Zie de [Azure-beveiligings basislijn voor Azure functions](security-baseline.md#logging-and-monitoring)voor meer beveiligings aanbevelingen voor de bekrachtiging. 

### <a name="require-https"></a>HTTPS vereisen

Standaard kunnen clients verbinding maken met functie-eind punten met behulp van HTTP of HTTPS. U moet HTTP omleiden naar HTTPs omdat HTTPS het SSL/TLS-protocol gebruikt om een beveiligde verbinding te bieden, die zowel is versleuteld als geverifieerd. Zie [https afdwingen](../app-service/configure-ssl-bindings.md#enforce-https)voor meer informatie.

Wanneer u HTTPS nodig hebt, moet u ook de meest recente versie van TLS vereisen. Zie [TLS-versies afdwingen](../app-service/configure-ssl-bindings.md#enforce-tls-versions)voor meer informatie.

Zie [Secure Connections (TSL) (Engelstalig)](../app-service/overview-security.md#https-and-certificates)voor meer informatie.

### <a name="function-access-keys"></a>Functie toegangs toetsen

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

#### <a name="system-key"></a>Systeem sleutel 

Voor specifieke uitbrei dingen is mogelijk een door een systeem beheerde sleutel nodig om toegang te krijgen tot webhook-eind punten. Systeem sleutels zijn ontworpen voor uitbrei ding-specifieke functie-eind punten die worden aangeroepen door interne onderdelen. De [Event grid trigger](functions-bindings-event-grid-trigger.md) vereist bijvoorbeeld dat het abonnement een systeem sleutel gebruikt bij het aanroepen van het trigger eindpunt. Durable Functions maakt ook gebruik van systeem sleutels voor het aanroepen van [duurzame taak extensie-api's](durable/durable-functions-http-api.md). 

Het bereik van systeem sleutels wordt bepaald door de uitbrei ding, maar is in het algemeen van toepassing op de volledige functie-app. Systeem sleutels kunnen alleen worden gemaakt met behulp van specifieke extensies en u kunt de waarden niet expliciet instellen. Net als andere sleutels kunt u een nieuwe waarde voor de sleutel genereren vanuit de portal of met behulp van de sleutel-Api's.

#### <a name="keys-comparison"></a>Vergelijking van sleutels

De volgende tabel vergelijkt het gebruik voor verschillende soorten toegangs sleutels:

| Bewerking                                        | Bereik                    | Geldige sleutels         |
|-----------------------------------------------|--------------------------|--------------------|
| Een functie uitvoeren                            | Specifieke functie        | Functie           |
| Een functie uitvoeren                            | Een functie             | Functie of host   |
| Een Administrator-eind punt aanroepen                        | Functie-app             | Host (alleen Master) |
| Api's voor duurzame taak uitbreidingen aanroepen              | Functie-app<sup>1</sup> | Systeem<sup>2</sup> |
| Een extensie-specifieke webhook aanroepen (intern) | Functie-app<sup>1</sup> | systeem<sup>2</sup> |

<sup>1</sup> Bereik dat door de extensie wordt bepaald.  
<sup>2</sup> Specifieke namen die zijn ingesteld op uitbrei ding.

Zie het artikel over het [binden van http-triggers](functions-bindings-http-webhook-trigger.md#obtaining-keys)voor meer informatie over toegangs sleutels.


#### <a name="secret-repositories"></a>Geheime opslag plaatsen

Standaard worden sleutels opgeslagen in een BLOB storage-container in het account dat is ingesteld door de `AzureWebJobsStorage` instelling. U kunt specifieke toepassings instellingen gebruiken om dit gedrag te negeren en sleutels op een andere locatie op te slaan.

|Locatie  |Instelling | Waarde | Beschrijving  |
|---------|---------|---------|---------|
|Ander opslag account     |  `AzureWebJobsSecretStorageSas`       | `<BLOB_SAS_URL` | Hiermee worden sleutels opgeslagen in Blob-opslag van een tweede opslag account, op basis van de meegeleverde SAS-URL. Sleutels worden versleuteld voordat ze worden opgeslagen met behulp van een geheim dat uniek is voor uw functie-app. |
|Bestandssysteem   | `AzureWebJobsSecretStorageType`   |  `files`       | Sleutels worden bewaard op het bestands systeem, versleuteld vóór opslag met behulp van een geheim dat uniek is voor uw functie-app. |
|Azure Key Vault  | `AzureWebJobsSecretStorageType`<br/>`AzureWebJobsSecretStorageKeyVaultName` | `keyvault`<br/>`<VAULT_NAME>` | De kluis moet een toegangs beleid hebben dat overeenkomt met de door het systeem toegewezen beheerde identiteit van de hosting bron. Het toegangs beleid moet de identiteit de volgende geheime machtigingen verlenen: `Get` , `Set` , `List` , en `Delete` . <br/>Wanneer lokaal wordt uitgevoerd, wordt de identiteit van de ontwikkelaar gebruikt. de instellingen moeten zich in delocal.settings.jsin het [ bestand](functions-run-local.md#local-settings-file)bevinden. | 
|Kubernetes Secrets  |`AzureWebJobsSecretStorageType`<br/>`AzureWebJobsKubernetesSecretName` (optioneel) | `kubernetes`<br/>`<SECRETS_RESOURCE>` | Alleen ondersteund bij het uitvoeren van de functions-runtime in Kubernetes. Als `AzureWebJobsKubernetesSecretName` niet is ingesteld, wordt de opslag plaats als alleen-lezen beschouwd. In dit geval moeten de waarden worden gegenereerd vóór de implementatie. De Azure Functions Core Tools genereert automatisch de waarden bij het implementeren naar Kubernetes.|

### <a name="authenticationauthorization"></a>Verificatie/autorisatie

Hoewel functie sleutels een zekere beperking kunnen bieden voor ongewenste toegang, is de enige manier om uw functie-eind punten te beveiligen, door positieve verificatie te implementeren van clients die toegang hebben tot uw functies. U kunt vervolgens autorisatie beslissingen nemen op basis van identiteit.  

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

### <a name="permissions"></a>Machtigingen

Net als bij elke toepassing of service voert het doel uw functie-app uit met de laagst mogelijke machtigingen. 

#### <a name="user-management-permissions"></a>Machtigingen voor gebruikers beheer

Functies bieden ondersteuning voor ingebouwde op [rollen gebaseerd toegangs beheer van Azure (Azure RBAC)](../role-based-access-control/overview.md). Azure-functies die worden ondersteund door-functies zijn [Inzender](../role-based-access-control/built-in-roles.md#contributor), [eigenaar](../role-based-access-control/built-in-roles.md#owner)en [lezer](../role-based-access-control/built-in-roles.md#owner). 

Machtigingen zijn van kracht op het niveau van de functie-app. De rol Inzender is vereist voor het uitvoeren van de meeste taken op app-niveau. Alleen de rol eigenaar kan een functie-app verwijderen. 

#### <a name="organize-functions-by-privilege"></a>Functies ordenen op bevoegdheid 

Verbindings reeksen en andere referenties die zijn opgeslagen in toepassings instellingen, bieden alle functies in de functie-app dezelfde set machtigingen in de bijbehorende resource. Overweeg het aantal functies met toegang tot specifieke referenties te minimaliseren door functies te verplaatsen die deze referenties niet naar een afzonderlijke functie-app gebruiken. U kunt altijd gebruikmaken van technieken als [functie](/learn/modules/chain-azure-functions-data-using-bindings/) koppeling voor het door geven van gegevens tussen functies in verschillende functie-apps.  

#### <a name="managed-identities"></a>Beheerde identiteiten

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

Beheerde identiteiten kunnen worden gebruikt in plaats van geheimen voor verbindingen van sommige triggers en bindingen. Zie [identiteits verbindingen](#identity-based-connections).

Zie [Managed Identities (beheerde identiteiten gebruiken voor app service en Azure functions](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json)) voor meer informatie.

#### <a name="restrict-cors-access"></a>CORS-toegang beperken

[Cross-Origin Resource Sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) is een manier om toe te staan dat web-apps die in een ander domein worden uitgevoerd, aanvragen indienen bij uw http-trigger-eind punten. App Service biedt ingebouwde ondersteuning voor het leveren van de vereiste CORS-headers in HTTP-aanvragen. CORS-regels worden gedefinieerd op het niveau van een functie-app.  

Hoewel het voor u een Joker teken is, waarmee alle sites toegang hebben tot uw eind punt. Maar deze terwijl is het doel van CORS, die u helpen cross-site scripting aanvallen te voor komen. Voeg in plaats daarvan een afzonderlijke CORS-vermelding toe voor het domein van elke web-app die toegang moet hebben tot uw eind punt. 

### <a name="managing-secrets"></a>Geheimen beheren 

Om verbinding te kunnen maken met de verschillende services en resources moet uw code worden uitgevoerd, functie-apps moeten toegang kunnen hebben tot geheimen, zoals verbindings reeksen en service sleutels. In deze sectie wordt beschreven hoe u geheimen opslaat die vereist zijn voor uw functies.

Sla nooit geheimen op in uw functie code. 

#### <a name="application-settings"></a>Toepassingsinstellingen

Standaard worden verbindings reeksen en geheimen die worden gebruikt door uw functie-app en bindingen opgeslagen als toepassings instellingen. Dit maakt deze referenties beschikbaar voor zowel de functie code als de verschillende bindingen die door de functie worden gebruikt. De naam van de toepassings instelling (sleutel) wordt gebruikt om de werkelijke waarde op te halen. Dit is het geheim. 

Voor elke functie-app is bijvoorbeeld een gekoppeld opslag account vereist dat wordt gebruikt door de runtime. Standaard wordt de verbinding met dit opslag account opgeslagen in een toepassings instelling met de naam `AzureWebJobsStorage` .

App-instellingen en verbindings reeksen worden versleuteld opgeslagen in Azure. Ze worden alleen ontsleuteld voordat ze worden ingevoegd in het proces geheugen van uw app wanneer de app wordt gestart. De versleutelings sleutels worden regel matig gedraaid. Als u liever de beveiligde opslag van uw geheimen wilt beheren, moet de app-instelling in plaats daarvan verwijzen naar Azure Key Vault. 

U kunt de instellingen standaard ook versleutelen in de local.settings.jsbij het bestand bij het ontwikkelen van functies op uw lokale computer. Zie de `IsEncrypted` eigenschap in het [lokale instellingen bestand](functions-run-local.md#local-settings-file)voor meer informatie.  

#### <a name="key-vault-references"></a>Key Vault verwijzingen

Hoewel toepassings instellingen voldoende zijn voor de meeste functies, wilt u mogelijk dezelfde geheimen delen in meerdere services. In dit geval resulteert de redundante opslag van geheimen in meer potentiële beveiligings problemen. Een veiligere benadering is een centrale service voor het opslaan van geheimen en het gebruik van verwijzingen naar deze service in plaats van de geheimen zelf.      

[Azure Key Vault](../key-vault/general/overview.md) is een service die gecentraliseerd geheimen beheer biedt, met volledige controle over het toegangs beleid en de controle geschiedenis. U kunt een Key Vault verwijzing gebruiken in de plaats van een connection string of sleutel in de instellingen van uw toepassing. Zie [Key Vault verwijzingen gebruiken voor app service en Azure functions](../app-service/app-service-key-vault-references.md?toc=%2fazure%2fazure-functions%2ftoc.json)voor meer informatie.

### <a name="identity-based-connections"></a>Verbindingen op basis van identiteiten

Identiteiten kunnen worden gebruikt in plaats van geheimen om verbinding te maken met een aantal resources. Dit heeft het voor deel dat het beheer van een geheim niet vereist is en biedt meer verfijnde toegangs beheer en controle. 

Wanneer u code schrijft waarmee de verbinding wordt gemaakt met [Azure-Services die ondersteuning bieden voor Azure AD-verificatie](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), kunt u kiezen voor het gebruik van een identiteit in plaats van een geheim of Connection String. Details voor beide verbindings methoden worden behandeld in de documentatie voor elke service.

Sommige Azure Functions triggers en bindings uitbreidingen kunnen worden geconfigureerd met behulp van een verbinding op basis van een identiteit. Dit omvat nu de [Azure Blob](./functions-bindings-storage-blob.md) -en [Azure Queue](./functions-bindings-storage-queue.md) -extensies. Zie voor meer informatie over het configureren van deze uitbrei dingen voor het gebruik van een identiteit [identiteits verbindingen gebruiken in azure functions](./functions-reference.md#configure-an-identity-based-connection).

### <a name="set-usage-quotas"></a>Gebruiks quota's instellen

Stel een gebruiks quotum in voor functies die worden uitgevoerd in een verbruiks abonnement. Wanneer u een limiet van dagelijks GB per seconde instelt voor het totaal van de uitvoering van functies in uw functie-app, wordt de uitvoering gestopt wanneer de limiet is bereikt. Dit kan helpen bij het oplossen van schadelijke code die uw functies uitvoert. Zie voor meer informatie over het schatten van verbruik voor uw functies het schatten van de [kosten van verbruiks plan](functions-consumption-costs.md). 

### <a name="data-validation"></a>Gegevensvalidatie

De triggers en bindingen die door uw functies worden gebruikt, bieden geen aanvullende gegevens validatie. De code moet alle ontvangen gegevens van een trigger of invoer binding valideren. Als er is geknoeid met een upstream-service, wilt u niet dat niet-gevalideerde invoer stromen door uw functies worden overgelopen. Als uw functie bijvoorbeeld gegevens van een Azure Storage wachtrij in een relationele data base opslaat, moet u de gegevens valideren en uw opdrachten para meters om SQL-injectie aanvallen te voor komen. 

Ga er niet van uit dat de gegevens die in uw functie worden geleverd, al zijn gevalideerd of opgeschoond. Het is ook een goed idee om te controleren of de gegevens die naar uitvoer bindingen worden geschreven, geldig zijn. 

### <a name="handle-errors"></a>Fouten verwerken

Hoewel het een eenvoudige oplossing lijkt, is het belang rijk dat u een goede fout afhandeling in uw functies schrijft. Niet-verwerkte fouten bellen op de host en worden verwerkt door de runtime. Verschillende bindingen verwerken de verwerking van fouten op een andere manier. Zie [Azure functions error handling](functions-bindings-error-pages.md)(Engelstalig) voor meer informatie.

### <a name="disable-remote-debugging"></a>Fout opsporing op afstand uitschakelen

Zorg ervoor dat externe fout opsporing is uitgeschakeld, behalve wanneer u uw functies actief opspoort. U kunt externe fout opsporing uitschakelen op het tabblad **algemene instellingen** van de **configuratie** van de functie-app in de portal. 

### <a name="restrict-cors-access"></a>CORS-toegang beperken

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

Gebruik geen joker tekens in de lijst met toegestane oorsprongen. In plaats daarvan vermeldt u de specifieke domeinen waarvan u verwacht dat ze aanvragen ontvangen.

### <a name="store-data-encrypted"></a>Archief gegevens versleuteld

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="secure-deployment"></a>Veilige implementatie

Met Azure Functions hulp middelen voor het maken van een integratie kunt u eenvoudig lokale functie project code naar Azure publiceren. Het is belang rijk om te begrijpen hoe de implementatie werkt bij het overwegen van beveiliging voor een Azure Functions topologie.   

### <a name="deployment-credentials"></a>Referenties voor implementatie

Voor App Service implementaties is een set implementatie referenties vereist. Deze implementatie referenties worden gebruikt voor het beveiligen van uw functie-app-implementaties. Implementatie referenties worden beheerd door het App Service-platform en worden op rest versleuteld. 

Er zijn twee soorten implementatie referenties:

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

Op dit moment wordt Key Vault niet ondersteund voor implementatie referenties. Zie [implementatie referenties voor Azure app service configureren voor](../app-service/deploy-configure-credentials.md)meer informatie over het beheren van implementatie referenties.

### <a name="disable-ftp"></a>FTP uitschakelen

Voor elke functie-app is standaard een FTP-eind punt ingeschakeld. Het FTP-eind punt wordt geopend met implementatie referenties. 

FTP wordt niet aanbevolen voor het implementeren van uw functie code. FTP-implementaties zijn hand matig en hiervoor moet u triggers synchroniseren. Zie [FTP-implementatie](functions-deployment-technologies.md#ftp)voor meer informatie. 

Wanneer u geen gebruik maakt van de FTP-fase, moet u deze uitschakelen in de portal. Als u ervoor kiest om FTP te gebruiken, moet u [FTPS afdwingen](../app-service/deploy-ftp.md#enforce-ftps).

### <a name="secure-the-scm-endpoint"></a>Het SCM-eind punt beveiligen

Elke functie-app heeft een bijbehorend `scm` service-eind punt dat wordt gebruikt door de kudu-service (Advanced tools) voor implementaties en andere app service [site-uitbrei dingen](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions). Het SCM-eind punt voor een functie-app is altijd een URL in het formulier `https://<FUNCTION_APP_NAME.scm.azurewebsites.net>` . Wanneer u netwerk isolatie gebruikt om uw functies te beveiligen, moet u ook rekening met dit eind punt. 

Door een afzonderlijk SCM-eind punt te hebben, kunt u implementaties en andere functies voor geavanceerde hulpprogram ma's beheren voor functie-apps die zijn geïsoleerd of worden uitgevoerd in een virtueel netwerk. Het SCM-eind punt ondersteunt zowel basis verificatie (met implementatie referenties) als eenmalige aanmelding met uw Azure Portal referenties. Zie [toegang tot de kudu-service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)voor meer informatie. 

### <a name="continuous-security-validation"></a>Continue beveiligings validatie

Omdat de beveiliging moet worden beschouwd als een stap in het ontwikkelings proces, is het zinnig dat u ook beveiligings validaties in een continue implementatie omgeving implementeert. Dit wordt ook wel DevSecOps genoemd. Met Azure DevOps voor uw implementatie pijplijn kunt u validatie in het implementatie proces integreren. Zie voor meer informatie over [het toevoegen van continue beveiligings validatie aan uw CI/cd-pijp lijn](/azure/devops/migrate/security-validation-cicd-pipeline).  

## <a name="network-security"></a>Netwerkbeveiliging

Door netwerk toegang tot uw functie-app te beperken, kunt u bepalen wie toegang heeft tot uw function-eind punten. -Functies maken gebruik van App Service-infra structuur om uw functies toegang te geven tot bronnen zonder Internet Routeer bare adressen of om Internet toegang tot een eind punt van een functie te beperken. Zie [Azure functions-netwerk opties](functions-networking-options.md)voor meer informatie over deze netwerk opties.

### <a name="set-access-restrictions"></a>Toegangs beperkingen instellen

Met toegangs beperkingen kunt u een lijst met regels voor toestaan/weigeren definiëren om het verkeer naar uw app te beheren. Regels worden in volg orde van prioriteit geëvalueerd. Als er geen regels zijn gedefinieerd, wordt door uw app verkeer van elk adres geaccepteerd. Zie [Azure app service toegangs beperkingen #](../app-service/app-service-ip-restrictions.md?toc=%2fazure%2fazure-functions%2ftoc.json)voor meer informatie.

### <a name="private-site-access"></a>Toegang tot privésite

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

### <a name="deploy-your-function-app-in-isolation"></a>Uw functie-app in isolatie implementeren

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

### <a name="use-a-gateway-service"></a>Een Gateway Service gebruiken

Met Gateway Services, zoals [Azure-toepassing gateway](../application-gateway/overview.md) en [Azure front-deur](../frontdoor/front-door-overview.md) , kunt u een Web Application firewall (WAF) instellen. WAF-regels worden gebruikt voor het bewaken of blok keren van gedetecteerde aanvallen, die een extra beveiligingslaag bieden voor uw functies. Als u een WAF wilt instellen, moet uw functie-app worden uitgevoerd in een ASE of gebruikmaken van privé-eind punten (preview-versie). Zie [persoonlijke eind punten gebruiken](../app-service/networking/private-endpoint.md)voor meer informatie.    

## <a name="next-steps"></a>Volgende stappen

+ [Azure-beveiligings basislijn voor Azure Functions](security-baseline.md)
+ [Diagnostische gegevens Azure Functions](functions-diagnostics.md)
