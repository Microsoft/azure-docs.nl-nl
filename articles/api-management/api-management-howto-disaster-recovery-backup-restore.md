---
title: Herstel na noodherstel implementeren met behulp van back-up en herstel in API Management
titleSuffix: Azure API Management
description: Meer informatie over het gebruik van back-up en herstel om herstel na noodherstel uit te voeren in Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/05/2020
ms.author: apimpm
ms.openlocfilehash: ad0936fddacf8f5b2e4917441f5feaa41aad9de4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739796"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Noodherstel implementeren met back-up en herstellen van services in Azure API Management

Door uw API's te publiceren en beheren via Azure API Management, profiteert u van fouttolerantie en infrastructuurmogelijkheden die u anders handmatig zou ontwerpen, implementeren en beheren. Het Azure-platform vermindert een groot deel van de potentiële fouten tegen een fractie van de kosten.

Als u wilt herstellen van beschikbaarheidsproblemen die van invloed zijn op de regio die als host voor uw API Management-service, kunt u uw service op elk moment opnieuw in een andere regio gebruiken. Afhankelijk van uw hersteltijddoel wilt u mogelijk een stand-byservice in een of meer regio's houden. U kunt ook proberen hun configuratie en inhoud synchroon te houden met de actieve service volgens uw herstelpuntdoelstelling. De back-up- en herstelfuncties van de service bieden de benodigde bouwstenen voor het implementeren van een strategie voor herstel na noodherstel.

Back-up- en herstelbewerkingen kunnen ook worden gebruikt voor het repliceren API Management serviceconfiguratie tussen operationele omgevingen, bijvoorbeeld ontwikkeling en fasering. Let erop dat runtimegegevens, zoals gebruikers en abonnementen, ook worden gekopieerd, wat mogelijk niet altijd wenselijk is.

Deze handleiding laat zien hoe u back-up- en herstelbewerkingen automatiseert en hoe u ervoor kunt zorgen dat back-up- en herstelaanvragen met succes worden Azure Resource Manager.

> [!IMPORTANT]
> Herstelbewerking wijzigt de aangepaste hostnaamconfiguratie van de doelservice niet. We raden u aan dezelfde aangepaste hostnaam en hetzelfde TLS-certificaat te gebruiken voor zowel actieve als stand-byservices, zodat het verkeer, nadat de herstelbewerking is voltooid, opnieuw kan worden omgeleid naar het stand-by-exemplaar door een eenvoudige DNS CNAME-wijziging.
>
> Back-upbewerking legt geen vooraf geaggregeerde logboekgegevens vast die worden gebruikt in rapporten die worden weergegeven op de blade Analyse in de Azure Portal.

> [!WARNING]
> Elke back-up verloopt na 30 dagen. Als u probeert een back-up te herstellen nadat de verloopperiode van 30 dagen is verlopen, mislukt het herstellen met een `Cannot restore: backup expired` bericht.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Azure Resource Manager-aanvragen

> [!IMPORTANT]
> De REST API voor back-up en herstel maakt gebruik van Azure Resource Manager en heeft een ander verificatiemechanisme dan de REST API's voor het beheren van uw API Management entiteiten. In de stappen in deze sectie wordt beschreven hoe u Azure Resource Manager verifiëren. Zie [Authenticating Azure Resource Manager requests (Aanvragen voor Azure Resource Manager authenticeren) voor meer informatie.](/rest/api/index)

Alle taken die u op resources uitvoert met behulp van de Azure Resource Manager moeten worden geverifieerd Azure Active Directory de volgende stappen:

-   Voeg een toepassing toe aan de Azure Active Directory tenant.
-   Stel machtigingen in voor de toepassing die u hebt toegevoegd.
-   Haal het token op voor het authenticeren van aanvragen Azure Resource Manager.

### <a name="create-an-azure-active-directory-application"></a>Een Azure Active Directory-toepassing maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Gebruik het abonnement dat uw API Management service-exemplaar bevat en navigeer naar het tabblad **App-registraties** in **Azure Active Directory** (Azure Active Directory > Beheren/App-registraties).

    > [!NOTE]
    > Als de Azure Active Directory directory niet zichtbaar is voor uw account, neem dan contact op met de beheerder van het Azure-abonnement om de vereiste machtigingen voor uw account te verlenen.

3. Klik op **Nieuwe toepassing registreren**.

    Het **venster** Maken wordt aan de rechterkant weergegeven. Hier voert u de relevante informatie voor de AAD-app in.

4. Voer een naam in voor de toepassing.
5. Selecteer Voor het toepassingstype de optie **Native**.
6. Voer een tijdelijke aanduiding in, zoals voor de `http://resources` **omleidings-URI,** omdat dit een vereist veld is, maar de waarde wordt later niet gebruikt. Klik op het selectievakje om de toepassing op te slaan.
7. Klik op **Create**.

### <a name="add-an-application"></a>Een toepassing toevoegen

1. Zodra de toepassing is gemaakt, klikt u op **API-machtigingen.**
2. Klik op **+ Een machtiging toevoegen**.
4. Druk **op Microsoft API's selecteren.**
5. Kies **Azure Service Management.**
6. Druk **op Selecteren.**

    :::image type="content" source="./media/api-management-howto-disaster-recovery-backup-restore/add-app-permission.png" alt-text="Schermopname die laat zien hoe u app-machtigingen toevoegt."::: 

7. Klik **op Gedelegeerde machtigingen** naast de zojuist toegevoegde toepassing en vink het selectievakje **Toegang tot Azure Service Management (preview) aan.**

    :::image type="content" source="./media/api-management-howto-disaster-recovery-backup-restore/delegated-app-permission.png" alt-text="Schermopname van het toevoegen van gedelegeerde app-machtigingen.":::

8. Druk **op Selecteren.**
9. Klik **op Machtigingen toevoegen.**

### <a name="configuring-your-app"></a>Uw app configureren

Voordat u de API's aanroept die de back-up genereren en deze herstellen, moet u een token krijgen. In het volgende voorbeeld wordt het [NuGet-pakket Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) gebruikt om het token op te halen.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Vervang `{tenant id}` , en met behulp van de volgende `{application id}` `{redirect uri}` instructies:

1. Vervang `{tenant id}` door de tenant-id van de Azure Active Directory die u hebt gemaakt. U kunt de id openen door te klikken **App-registraties**  ->  **Eindpunten.**

    ![Eindpunten][api-management-endpoint]

2. Vervang `{application id}` door de waarde die u krijgt door naar de pagina Instellingen **te** navigeren.
3. Vervang door `{redirect uri}` de waarde van het tabblad **Omleidings-URI's** van Azure Active Directory toepassing.

    Zodra de waarden zijn opgegeven, moet het codevoorbeeld een token retourneren dat lijkt op het volgende voorbeeld:

    ![Token][api-management-arm-token]

    > [!NOTE]
    > Het token kan na een bepaalde periode verlopen. Voer het codevoorbeeld opnieuw uit om een nieuw token te genereren.

## <a name="calling-the-backup-and-restore-operations"></a>De back-up- en herstelbewerkingen aanroepen

De REST API's zijn [API Management Service - Backup en](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup) API Management Service - [Restore](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore).

Voordat u de bewerkingen 'back-up en herstel' aanroept die in de volgende secties worden beschreven, stelt u de header voor de autorisatieaanvraag voor uw REST-aanroep in.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>Een back-up maken API Management service

Als u een back-up wilt API Management service de volgende HTTP-aanvraag:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

Hierbij

-   `subscriptionId` - Id van het abonnement dat de API Management service waar u een back-up van wilt maken
-   `resourceGroupName` - naam van de resourcegroep van uw Azure API Management service
-   `serviceName` : de naam van de API Management van de service die u maakt op het moment dat deze wordt gemaakt
-   `api-version` - vervangen door `2019-12-01`

Geef in de hoofdcode van de aanvraag de naam van het Azure-opslagaccount, de toegangssleutel, de naam van de blobcontainer en de back-upnaam op:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Stel de waarde van de `Content-Type` aanvraagheader in op `application/json` .

Back-up is een langdurige bewerking die meer dan een minuut kan duren. Als de aanvraag is geslaagd en het back-upproces is gestart, ontvangt u een `202 Accepted` antwoordstatuscode met een `Location` header. Maak GET-aanvragen naar de URL in de `Location` header om de status van de bewerking te vinden. Terwijl de back-up wordt uitgevoerd, blijft u de statuscode '202 Geaccepteerd' ontvangen. Een antwoordcode van `200 OK` geeft aan dat de back-upbewerking is voltooid.

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>Een API Management herstellen

Als u een API Management wilt herstellen vanuit een eerder gemaakte back-up, maakt u de volgende HTTP-aanvraag:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

Hierbij

-   `subscriptionId` - Id van het abonnement dat de API Management service waar u een back-up naar herstelt
-   `resourceGroupName` - naam van de resourcegroep met de Azure API Management-service waar u een back-up naar herstelt
-   `serviceName` - de naam van de API Management service die wordt hersteld in opgegeven tijdens het maken
-   `api-version` - vervangen door `api-version=2019-12-01`

Geef in de body van de aanvraag de locatie van het back-upbestand op. Dat wil zeggen dat u de naam van het Azure-opslagaccount, de toegangssleutel, de naam van de blobcontainer en de back-upnaam toevoegt:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Stel de waarde van de `Content-Type` aanvraagheader in op `application/json` .

Herstellen is een langdurige bewerking die maximaal 30 minuten kan duren. Als de aanvraag is geslaagd en het herstelproces is gestart, ontvangt u een `202 Accepted` antwoordstatuscode met een `Location` header. Maak GET-aanvragen naar de URL in de `Location` header om de status van de bewerking te vinden. Terwijl het herstel wordt uitgevoerd, blijft u de statuscode '202 Geaccepteerd' ontvangen. Een antwoordcode van `200 OK` geeft aan dat de herstelbewerking is voltooid.

> [!IMPORTANT]
> **De SKU van** de service die wordt hersteld in **moet overeenkomen met** de SKU van de service met back-up die wordt hersteld.
>
> **Wijzigingen** die zijn aangebracht in de serviceconfiguratie (bijvoorbeeld API's, beleidsregels en het uiterlijk van de ontwikkelaarsportal) terwijl de herstelbewerking wordt uitgevoerd, kunnen **worden overschreven.**

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> Back-up- en herstelbewerkingen kunnen ook worden uitgevoerd met respectievelijk de opdrachten PowerShell [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) en [_Restore-AzApiManagement._](/powershell/module/az.apimanagement/restore-azapimanagement)

## <a name="constraints-when-making-backup-or-restore-request"></a>Beperkingen bij het maken van een back-up- of herstelaanvraag

-   **De container** die is opgegeven in de aanvraag body **moet bestaan.**
-   Terwijl de back-up wordt uitgevoerd, vermijdt u beheerwijzigingen in de **service,** zoals het upgraden of downgraden van de SKU, het wijzigen van de domeinnaam en meer.
-   Het herstellen van **een back-up wordt slechts 30 dagen na** het maken ervan gegarandeerd.
-   **Wijzigingen** die zijn aangebracht in de serviceconfiguratie (bijvoorbeeld API's, beleidsregels en het uiterlijk van de ontwikkelaarsportal) terwijl de back-upbewerking wordt uitgevoerd, kunnen worden uitgesloten van de back-up en gaan **verloren.**
-   Als de firewall van het Azure Storage-account [is][azure-storage-ip-firewall]  ingeschakeld, moet de klant de set IP-adressen van de Azure [API Management-besturingsvlak][control-plane-ip-address] in het opslagaccount toestaan voor back-up naar of herstellen van naar het werk. Het Azure Storage-account kan zich in elke Azure-regio bevinden, met uitzondering van de regio API Management service zich bevindt. Als de API Management-service zich bijvoorbeeld in VS - west bevinden, kan het Azure Storage-account zich in VS - west 2 bevinden en moet de klant het IP-adres van het besturingsvlak 13.64.39.16 (API Management Control Plane IP van VS - west) openen in de firewall. Dit komt doordat de aanvragen voor Azure Storage niet worden geSNAT naar een openbaar IP-adres van Compute (Azure Api Management Control Plane) in dezelfde Azure-regio. Opslagaanvraag tussen regio's wordt via SNAT naar het openbare IP-adres geSNAT.
-   [Cross-Origin Resource Sharing (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) mag **niet** zijn ingeschakeld op de Blob-service in het Azure Storage account.
-   **De SKU van** de service die wordt hersteld in **moet overeenkomen met** de SKU van de back-upservice die wordt hersteld.

## <a name="what-is-not-backed-up"></a>Er wordt geen back-up van de back-up van de back-up
-   **Gebruiksgegevens die** worden gebruikt voor het maken van **analyserapporten, zijn niet opgenomen** in de back-up. Gebruik [Azure API Management REST API][azure api management rest api] om periodiek analyserapporten op te halen voor het veilig bewaren.
-   [Aangepaste domein-TLS/SSL-certificaten.](configure-custom-domain.md)
-   [Aangepast CA-certificaat,](api-management-howto-ca-certificates.md)dat tussen- of basiscertificaten bevat die door de klant zijn geüpload.
-   [Instellingen voor integratie van](api-management-using-with-vnet.md) virtuele netwerken.
-   [Configuratie van beheerde](api-management-howto-use-managed-service-identity.md) identiteit.
-   [Azure Monitor Diagnostic](api-management-howto-use-azure-monitor.md) Configuratie.
-   [Protocollen en coderingsinstellingen.](api-management-howto-manage-protocols-ciphers.md)
-   [Inhoud van de](developer-portal-faq.md#is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management) ontwikkelaarsportal.

De frequentie waarmee u serviceback-ups moet uitvoeren, is van invloed op uw herstelpuntdoelstelling. Om dit te minimaliseren, raden we u aan om regelmatige back-ups te implementeren en back-ups op aanvraag uit te voeren nadat u wijzigingen hebt aangebracht in uw API Management service.

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende resources voor verschillende overzichten van het back-up-/herstelproces.

-   [Azure API Management-accounts repliceren](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Back-up maken en terugzetten in API Management automatiseren met Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Azure API Management: back-up maken en configuratie herstellen](/archive/blogs/stuartleeks/azure-api-management-backing-up-and-restoring-configuration) 
     _De aanpak die doorMatch is beschreven, komt niet overeen met de officiële richtlijnen, maar is wel interessant._

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: /rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
[control-plane-ip-address]: api-management-using-with-vnet.md#control-plane-ips
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range
