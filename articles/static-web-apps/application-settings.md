---
title: Toepassings instellingen voor statische Azure-Web Apps configureren
description: Meer informatie over het configureren van toepassings instellingen voor statische Azure-Web Apps
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: 2886cb6433c4f8a65eb0661eded0ddd08248e137
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102219179"
---
# <a name="configure-application-settings-for-azure-static-web-apps-preview"></a>Toepassings instellingen configureren voor de preview-versie van Azure static Web Apps

Toepassings instellingen bevatten configuratie-instellingen voor waarden die kunnen worden gewijzigd, zoals teken reeksen voor database verbindingen. Door toepassings instellingen toe te voegen, kunt u de configuratie-invoer wijzigen in uw app, zonder dat u de toepassings code hoeft te wijzigen.

Toepassings instellingen zijn:

- Versleuteld op rest
- Gekopieerd naar [staging](review-publish-pull-requests.md) -en productie omgevingen

Toepassings instellingen worden soms ook aangeduid als omgevings variabelen.

> [!IMPORTANT]
> De toepassings instellingen die in dit artikel worden beschreven, gelden alleen voor de back-end-API van een statische web-app van Azure.
>
> Voor informatie over het gebruik van omgevings variabelen met uw front-end-webtoepassing raadpleegt u de documenten voor uw [Java script-Framework](#javascript-frameworks-and-libraries) of [statische-site Generator](#static-site-generators).

## <a name="prerequisites"></a>Vereisten

- Een statische Web Apps-toepassing voor Azure
- [Azure-CLI](/cli/azure/install-azure-cli)

## <a name="types-of-application-settings"></a>Typen toepassings instellingen

Er zijn doorgaans twee aspecten van een statische Web Apps-toepassing in Azure. De eerste is de webtoepassing, of statische inhoud, die wordt vertegenwoordigd door HTML, CSS, java script en afbeeldingen. De tweede is de back-end-API, die wordt aangedreven door een Azure Functions-toepassing.

In dit artikel wordt beschreven hoe u toepassings instellingen voor de back-end-API in Azure Functions kunt beheren.

De toepassings instellingen die in dit artikel worden beschreven, kunnen niet worden gebruikt of waarnaar wordt verwezen in statische webtoepassingen. Veel front-end frameworks en statische-site Generators maken echter het gebruik van omgevings variabelen mogelijk tijdens de ontwikkeling. Tijdens het bouwen worden deze variabelen vervangen door hun waarden in de gegenereerde HTML of Java script. Omdat de gegevens in HTML en Java script gemakkelijk kunnen worden gedetecteerd door site bezoekers, wilt u voor komen dat gevoelige gegevens in de front-end-toepassing worden geplaatst. Instellingen die gevoelige gegevens bevatten, worden het beste gevonden in het API-gedeelte van uw toepassing.

Raadpleeg de volgende artikelen voor meer informatie over het gebruik van omgevings variabelen met uw Java script-Framework of-bibliotheek.

### <a name="javascript-frameworks-and-libraries"></a>Java script-frameworks en-bibliotheken

- [Angular](https://angular.io/guide/build#configuring-application-environments)
- [React](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Svelte](https://linguinecode.com/post/how-to-add-environment-variables-to-your-svelte-js-app)
- [Vue](https://cli.vuejs.org/guide/mode-and-env.html)

### <a name="static-site-generators"></a>Statische site generatoren

- [Gatsby](https://www.gatsbyjs.org/docs/environment-variables/)
- [Hugo](https://gohugo.io/getting-started/configuration/)
- [Jekyll](https://jekyllrb.com/docs/configuration/environments/)

## <a name="about-api-app-settings"></a>Over API-app-instellingen

Api's in statische Azure-Web Apps worden aangedreven door Azure Functions, waarmee u toepassings instellingen kunt definiëren in de _local.settings.jsin_ het bestand. Dit bestand definieert toepassings instellingen in de `Values` eigenschap van de configuratie.

In het volgende voor beeld _local.settings.jsover_ ziet u hoe u een waarde kunt toevoegen voor `DATABASE_CONNECTION_STRING` .

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
  }
}
```

In de instellingen die in de eigenschap zijn gedefinieerd `Values` , kan worden verwezen vanuit code als omgevings variabelen die beschikbaar zijn via het `process.env` object.

```js
const connectionString = process.env.DATABASE_CONNECTION_STRING;
```

Het `local.settings.json` bestand wordt niet bijgehouden door de GitHub-opslag plaats omdat gevoelige informatie, zoals database verbindings reeksen, vaak in het bestand wordt opgenomen. Omdat de lokale instellingen op uw computer blijven staan, moet u de instellingen hand matig uploaden naar Azure.

Over het algemeen worden uw instellingen niet regel matig geüpload en is deze niet vereist bij elke build.

## <a name="uploading-application-settings"></a>Toepassings instellingen uploaden

U kunt toepassings instellingen configureren via de Azure Portal of met de Azure CLI.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

De Azure Portal biedt een interface voor het maken, bijwerken en verwijderen van toepassings instellingen.

1. Navigeer naar [Azure Portal](https://portal.azure.com).

1. Zoek en selecteer in de zoek balk **statische web apps**.

1. Klik op de optie **configuratie** in de zijbalk.

1. Selecteer de omgeving waarin u de toepassings instellingen wilt Toep assen. Faserings omgevingen worden automatisch gemaakt wanneer een pull-aanvraag wordt gegenereerd en worden in productie gepromoveerd wanneer de pull-aanvraag wordt samengevoegd. U kunt toepassings instellingen per omgeving instellen.

1. Klik op de **knop toevoegen** om een nieuwe app-instelling toe te voegen.

    :::image type="content" source="media/application-settings/configuration.png" alt-text="Configuratie weergave Azure static Web Apps":::

1. Voer een **naam** en **waarde** in.

1. Klik op **OK**.

1. Klik op **Opslaan**.

### <a name="using-the-azure-cli"></a>Met behulp van de Azure CLI

U kunt de `az rest` opdracht gebruiken om bulksgewijs uploads van uw instellingen naar Azure uit te voeren. De opdracht accepteert toepassings instellingen als JSON-objecten in een bovenliggende eigenschap met de naam `properties` .

De eenvoudigste manier om een JSON-bestand met de juiste waarden te maken, is door een aangepaste versie van uwlocal.settings.jste maken _in_ het bestand.

1. Om ervoor te zorgen dat het nieuwe bestand met gevoelige gegevens niet openbaar wordt weer gegeven, voegt u de volgende vermelding toe aan uw _. gitignore_ -bestand.

   ```bash
   local.settings*.json
   ```

2. Maak vervolgens een kopie van uw _local.settings.jsin_ het bestand en noem deze _local.settings.properties.jsop_.

3. Verwijder in het nieuwe bestand alle andere gegevens uit het bestand, met uitzonde ring van de toepassings instellingen en wijzig de naam `Values` in `properties` .

   Het bestand moet er nu uitzien als in het volgende voor beeld:

   ```json
   {
     "properties": {
       "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
     }
   }
   ```

Voor de Azure CLI-opdracht is een aantal waarden vereist die specifiek zijn voor uw account om het uploaden uit te voeren. Vanuit het venster _overzicht_ van uw statische web apps resource hebt u toegang tot de volgende informatie:

1. Naam van statische site
2. Naam van de resourcegroep
3. Abonnements-id

:::image type="content" source="media/application-settings/overview.png" alt-text="Overzicht van statische Azure-Web Apps":::

4. Voer de volgende opdracht uit vanaf een Terminal of opdracht regel. Zorg ervoor dat u de tijdelijke aanduidingen van `<YOUR_STATIC_SITE_NAME>` , `<YOUR_RESOURCE_GROUP_NAME>` , en `<YOUR_SUBSCRIPTION_ID>` met uw waarden in het _overzichts_ venster vervangt.

   ```bash
   az rest --method put --headers "Content-Type=application/json" --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/config/functionappsettings?api-version=2019-12-01-preview" --body @local.settings.properties.json
   ```

> [!IMPORTANT]
> Het bestand ' local.settings.properties.jsop ' moet zich in dezelfde map bevindt waarin deze opdracht wordt uitgevoerd. Dit bestand kan wille keurig een naam hebben. De naam is niet significant.

### <a name="view-application-settings-with-the-azure-cli"></a>Toepassings instellingen weer geven met Azure CLI

Toepassings instellingen zijn beschikbaar voor weer gave via de Azure CLI.

- Voer de volgende opdracht uit vanaf een Terminal of opdracht regel. Zorg ervoor dat u de tijdelijke aanduidingen vervangt door `<YOUR_SUBSCRIPTION_ID>` `<YOUR_RESOURCE_GROUP_NAME>` `<YOUR_STATIC_SITE_NAME>` uw eigen waarden.

   ```bash
   az rest --method post --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/listFunctionAppSettings?api-version=2019-12-01-preview"
   ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Lokale ontwikkeling instellen](local-development.md)
