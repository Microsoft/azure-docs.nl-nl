---
title: 'Snelstart: resources maken en beheren in Azure Communication Services'
titleSuffix: An Azure Communication Services quickstart
description: In deze Snelstart leert u hoe u uw eerste Azure Communication Services-resource kunt maken en beheren.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-azp-net
ms.openlocfilehash: a93ac3b5d988be33c0f27726a75b1006f990d1da
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886110"
---
# <a name="quickstart-create-and-manage-communication-services-resources"></a>Quickstart: Communication Services-resources maken en beheren

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Ga aan de slag met Azure Communication Services door uw eerste Communication Services-resource te provisioneren. Communication Services-resources kunnen worden ingericht via de Azure Portal of met de .NET beheer-clientbibliotheek. Met de beheer-clientbibliotheek kunt u uw resource en interfaces maken, configureren, bijwerken en verwijderen met [Azure Resource Manager](../../azure-resource-manager/management/overview.md), de implementatie- en beheerservice van Azure. Alle beschikbare functies in de clientbibliotheken zijn beschikbaar in de Azure Portal. 

> [!WARNING]
> Houd er rekening mee dat de beschikbaarheid van Communication Services wordt beperkt tot de Amerikaanse geografie tijdens openbare preview. Houd er ook rekening mee dat communicatie bronnen niet kunnen worden overgedragen naar een ander abonnement tijdens de openbare preview.

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/create-resource-azp.md)]
::: zone-end

::: zone pivot="platform-net"
[!INCLUDE [.NET](./includes/create-resource-net.md)]
::: zone-end

## <a name="access-your-connection-strings-and-service-endpoints"></a>Toegang tot uw verbindingsreeksen en service-eindpunten

Met verbindingsreeksen kunnen de Communication Services-clientbibliotheken verbinding maken met en verifiëren bij Azure. U hebt toegang tot uw verbindingsreeksen voor Communication Services en service-eindpunten van de Azure Portal of programmatisch met Azure Resource Manager-API‘s 

Nadat u naar uw Communication Services-resource hebt genavigeerd, selecteert u **Sleutels** in het navigatiemenu en kopieert u de **Verbindingsreeks**- of **Eindpunt**-waarden voor gebruik door de Communication Services-clientbibliotheken. Houd er rekening mee dat u toegang hebt tot de primaire en secundaire sleutels. Dit kan handig zijn in scenario's waarin u tijdelijke toegang tot uw Communication Services-resources wilt verlenen aan een derde partij of staging-omgeving.

:::image type="content" source="./media/key.png" alt-text="Schermafbeelding van de sleutelpagina van de Communication Services.":::

## <a name="store-your-connection-string"></a>Uw verbindingsreeks bijwerken

Clientbibliotheken voor Communication Services gebruiken verbindingsreeksen om aanvragen voor Communication Services te autoriseren. U hebt verschillende mogelijkheden om uw verbindingsreeks op te slaan:

* Een toepassing die wordt uitgevoerd op het bureaublad of op een apparaat kan de verbindingsreeks opslaan in een bestand **app.config** of **web.config**. Voeg de verbindingsreeks toe aan de sectie **AppSettings** in deze bestanden.
* Een toepassing die wordt uitgevoerd in een Azure App Service kan de verbindingsreeks opslaan in de [App Service-toepassingsinstellingen](../../app-service/configure-common.md). Voeg de verbindingsreeks toe aan de sectie **Verbindingsreeksen** van het tabblad Toepassingsinstellingen in de portal.
* U kunt uw verbindingsreeks opslaan in [Azure Key Vault](../../data-factory/store-credentials-in-key-vault.md).
* Als u uw toepassing lokaal uitvoert, wilt u mogelijk uw verbindingsreeks opslaan in een omgevingsvariabele.

### <a name="store-your-connection-string-in-an-environment-variable"></a>De verbindingsreeks naar een omgevingsvariabele schrijven

Als u een omgevingsvariabele wilt configureren, opent u een consolevenster en selecteert u het besturingssysteem op de onderstaande tabbladen. Vervang `<yourconnectionstring>` door de feitelijke verbindingsreeks.

#### <a name="windows"></a>[Windows](#tab/windows)

Open een consolevenster en voer de volgende opdracht in:

```console
setx COMMUNICATION_SERVICES_CONNECTION_STRING "<yourconnectionstring>"
```

Nadat u de omgevingsvariabele hebt toegevoegd, moet u actieve programma's die de omgevingsvariabele moeten lezen, opnieuw starten. Start ook het consolevenster opnieuw. Als u bijvoorbeeld Visual Studio als editor gebruikt, start u Visual Studio opnieuw voordat u het voorbeeld uitvoert.

#### <a name="macos"></a>[MacOS](#tab/unix)

Bewerk uw **.zshrc** en voeg de omgevingsvariabele toe:

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.zshrc` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden. Als u de omgevingsvariabele hebt gemaakt met uw IDE open, moet u de editor, IDE of shell mogelijk sluiten en opnieuw openen om toegang te krijgen tot de variabele.

#### <a name="linux"></a>[Linux](#tab/linux)

Bewerk uw **.bash_profile** en voeg de omgevingsvariabele toe:

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.bash_profile` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden. Als u de omgevingsvariabele hebt gemaakt met uw IDE open, moet u de editor, IDE of shell mogelijk sluiten en opnieuw openen om toegang te krijgen tot de variabele.

---

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Communication Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

Als er telefoonnummers zijn toegewezen aan uw resource bij het verwijderen van resources, worden de telefoonnummers automatisch van uw resource vrijgegeven. 

## <a name="next-steps"></a>Volgende stappen

In deze Snelstart hebt u de volgende zaken geleerd:

> [!div class="checklist"]
> * Een Communication Services-resource aanmaken.
> * Resourcegeografie en -tags configureren
> * Toegang tot de sleutels voor die resource
> * De resource verwijderen

> [!div class="nextstepaction"]
> [Uw eerste gebruikers-toegangstokens maken](access-tokens.md)