---
title: Geneste sjabloon omgevingen implementeren in Azure DevTest Labs
description: Meer informatie over het implementeren van geneste Azure Resource Manager sjablonen om omgevingen te voorzien van Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 39002e286fafd4f813333a14ed86256a517897e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85481337"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Geneste Azure Resource Manager sjablonen implementeren voor test omgevingen
Met een geneste implementatie kunt u andere Azure Resource Manager sjablonen uitvoeren vanuit een belang rijke Resource Manager-sjabloon. Hiermee kunt u uw implementatie afbreken in een set doel gerichte en specifieke sjablonen. Dit biedt voor delen op het gebied van testen, hergebruik en lees baarheid. Het artikel [met gekoppelde sjablonen bij het implementeren van Azure-resources](../azure-resource-manager/templates/linked-templates.md) biedt een goed overzicht van deze oplossing met verschillende code voorbeelden. In dit artikel vindt u een voor beeld dat specifiek is voor Azure DevTest Labs. 

## <a name="key-parameters"></a>Sleutel parameters
Hoewel u uw eigen Resource Manager-sjabloon helemaal zelf kunt maken, is het raadzaam om het [Azure-resource groep-project](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) in Visual Studio te gebruiken, waarmee u eenvoudig sjablonen voor het ontwikkelen en opsporen van fouten opstelt. Wanneer u een geneste implementatie resource toevoegt aan azuredeploy.js, voegt Visual Studio verschillende items toe om de sjabloon flexibeler te maken. Deze items omvatten de submap met de secundaire sjabloon en het parameter bestand, namen van variabelen binnen het hoofd sjabloon bestand en twee para meters voor de opslag locatie voor de nieuwe bestanden. De **_artifactsLocation** en **_artifactsLocationSasToken** zijn de belangrijkste para meters die door de DevTest Labs worden gebruikt. 

Zie [multi-VM-omgevingen en Paas-resources maken met Azure Resource Manager sjablonen](devtest-lab-create-environment-from-arm.md)als u niet bekend bent met de manier waarop de DevTest Labs met omgevingen werkt. Uw sjablonen worden opgeslagen in de opslag plaats die is gekoppeld aan het lab in DevTest Labs. Wanneer u een nieuwe omgeving met deze sjablonen maakt, worden de bestanden verplaatst naar een Azure Storage container in het lab. Om de geneste bestanden te kunnen identificeren en kopiëren, worden in DevTest Labs de _artifactsLocation-en _artifactsLocationSasToken-para meters geïdentificeerd en worden de submappen gekopieerd naar de opslag container. Vervolgens wordt de locatie en het Shared Access Signature SaS-token automatisch ingevoegd in para meters. 

## <a name="nested-deployment-example"></a>Voor beeld van geneste implementatie
Hier volgt een eenvoudig voor beeld van een geneste implementatie:

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

De map in de opslag plaats met deze sjabloon bevat een submap `nestedtemplates` met de bestanden **NestOne.jsop** en **NestOne.parameters.jsop**. In de **azuredeploy.jsop** wordt URI voor de sjabloon gebouwd op basis van de locatie van de artefacten, de geneste sjabloon, de geneste sjabloon bestands naam. Op dezelfde manier wordt URI voor de para meters gebouwd met behulp van de artefacten locatie, geneste sjabloon en het parameter bestand voor de geneste sjabloon. 

Dit is de afbeelding van dezelfde project structuur in Visual Studio: 

![Project structuur in Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

U kunt extra mappen toevoegen aan de primaire map, maar niet op één niveau dieper. 

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over omgevingen: 

- [Multi-VM-omgevingen en PaaS-resources maken met Azure Resource Manager-sjablonen](devtest-lab-create-environment-from-arm.md)
- [Open bare omgevingen configureren en gebruiken in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Een omgeving verbinden met het virtuele netwerk van uw Lab in Azure DevTest Labs](connect-environment-lab-virtual-network.md)