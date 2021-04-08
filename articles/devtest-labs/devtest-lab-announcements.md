---
title: Een aankondiging in Azure DevTest Labs plaatsen in een Lab | Microsoft Docs
description: Meer informatie over het plaatsen van een aangepaste aankondiging in een bestaand Lab om gebruikers op de hoogte te stellen van recente wijzigingen of toevoegingen aan het lab in Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 72c6ed7ab691ece094673ff5a158ffa769a9f7a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88270764"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Een aankondiging in Azure DevTest Labs plaatsen in een Lab

Als test beheerder kunt u een aangepaste aankondiging in een bestaand Lab plaatsen om gebruikers op de hoogte te stellen van recente wijzigingen of toevoegingen aan het lab. U kunt bijvoorbeeld gebruikers informeren over:

- Nieuwe VM-grootten die beschikbaar zijn
- Installatie kopieën die momenteel onbruikbaar zijn
- Updates voor Lab-beleid

Zodra de aankondiging is gepost, wordt deze weer gegeven op de overzichts pagina van het lab en kan de gebruiker deze selecteren voor meer informatie.

De aankondigings functie is bedoeld om te worden gebruikt voor tijdelijke meldingen.  U kunt een aankondiging eenvoudig uitschakelen nadat deze niet meer nodig is.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Stappen voor het plaatsen van een aankondiging in een bestaand Lab

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer indien nodig **alle services** en selecteer vervolgens **DevTest Labs** in de lijst. (Uw Lab wordt mogelijk al weer gegeven op het dash board onder **alle resources**).
1. Selecteer in de lijst met Labs het lab waarin u een aankondiging wilt plaatsen.
1. Selecteer in het gedeelte **overzicht** van het lab de optie **configuratie en beleid**.

    ![Knop configuratie en beleid](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. Selecteer aan de linkerkant onder **instellingen** de optie **Lab-aankondiging**.

    ![Knop voor Lab-aankondiging](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Stel **ingeschakeld** in op **Ja** als u een bericht wilt maken voor de gebruikers in dit lab.

1. U kunt een **verval datum** invoeren om een datum en tijd op te geven waarna de aankondiging niet meer wordt weer gegeven voor gebruikers. Als u geen verval datum invoert, blijft de aankondiging actief totdat u deze uitschakelt.

   > [!NOTE]
   > Nadat de aankondiging is verlopen, wordt deze niet meer weer gegeven voor gebruikers, maar nog steeds in het **aankondigings** deel venster voor de Lab. U kunt wijzigingen aanbrengen in het bestand en het opnieuw inschakelen om het weer actief te maken.
   >
   >

1. Voer een **titel** voor de aankondiging en de tekst van de **aankondiging** in.

   De titel mag Maxi maal 100 tekens lang zijn en wordt weer gegeven aan de gebruiker op de overzichts pagina van het lab. Als de gebruiker de titel selecteert, wordt de tekst van de aankondiging weer gegeven.

   De tekst van de aankondiging accepteert de prijs verlaging. Als u de tekst van de aankondiging opgeeft, kunt u het bericht weer geven in het voorbeeld gebied onder aan het scherm.

    ![Aankondigings scherm voor het lab om het bericht te maken.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Selecteer **Opslaan** zodra uw aankondiging gereed is om te worden geplaatst.

Als u deze aankondiging niet meer wilt weer geven voor gebruikers met een lab, keert u terug naar de **aankondigings** pagina voor Lab en stelt u **ingeschakeld** in op **Nee**. Als u een verval datum hebt opgegeven, wordt de aankondiging automatisch uitgeschakeld op die datum en tijd.

## <a name="steps-for-users-to-view-an-announcement"></a>Stappen voor het weer geven van een aankondiging door gebruikers

1. Selecteer een lab in het [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Als er een aankondiging voor het lab is geplaatst, wordt er een melding weer gegeven aan de bovenkant van de overzichts pagina van het lab. Deze informatie is de aankondigings titel die is opgegeven bij het maken van de aankondiging.

    ![Aankondiging van Lab op overzichts pagina](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. De gebruiker kan het bericht selecteren om de volledige aankondiging weer te geven.

    ![Meer informatie voor de Lab-aankondiging](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon
U kunt een aankondiging opgeven als onderdeel van een Azure Resource Manager sjabloon zoals wordt weer gegeven in het volgende voor beeld:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string",
            "defaultValue": "devtestlabfromarm"
        },
        "regionId": {
            "type": "string",
            "defaultValue": "eastus"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-26-preview",
            "name": "[parameters('name')]",
            "type": "Microsoft.DevTestLab/labs",
            "location": "[parameters('regionId')]",
            "tags": {},
            "properties": {
                "labStorageType": "Premium",
                "announcement":
                {
                    "title": "Important! Three images are currently inaccessible. Click for more information.",
                    "markdown":"# Images are inaccessible\n\nThe following 3 images are currently not available for use: \n\n- image1\n- image2\n- image3\n\nI am working to fix the problem ASAP.",
                    "enabled": "Enabled",
                    "expirationDate":"2018-12-31T06:00:00+00:00",
                    "expired": "false"
                },
                "support": {
                    "markdown": "",
                    "enabled": "Enabled"
                }
            },
            "resources": [
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "LabVmsShutdown",
                    "location": "[parameters('regionId')]",
                    "type": "schedules",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ],
                    "properties": {
                        "status": "Enabled",
                        "timeZoneId": "Eastern Standard Time",
                        "dailyRecurrence": {
                            "time": "1900"
                        },
                        "taskType": "LabVmsShutdownTask",
                        "notificationSettings": {
                            "status": "Disabled",
                            "timeInMinutes": 30
                        }
                    }
                },
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "[concat('Dtl', parameters('name'))]",
                    "type": "virtualNetworks",
                    "location": "[parameters('regionId')]",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ]
                }
            ]
        }
    ]
}
```

U kunt een Azure Resource Manager-sjabloon implementeren met behulp van een van de volgende manieren:

- [Azure-portal](../azure-resource-manager/templates/deploy-portal.md)
- [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Azure-CLI](../azure-resource-manager/templates/deploy-cli.md)
- [REST API](../azure-resource-manager/templates/deploy-rest.md)

## <a name="next-steps"></a>Volgende stappen
* Als u een Lab-beleid wijzigt of instelt, wilt u mogelijk een aankondiging plaatsen om gebruikers op de hoogte te stellen. [Beleid en planningen instellen](devtest-lab-set-lab-policy.md) biedt informatie over het Toep assen van beperkingen en conventies in uw abonnement met behulp van aangepast beleid.
* Verken de [DevTest Labs Azure Resource Manager-sjabloon galerie voor Quick](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)start.
