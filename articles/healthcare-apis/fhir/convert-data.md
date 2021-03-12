---
title: Gegevens conversie voor Azure API voor FHIR
description: Gebruik de $convert data endpoint en Customize-Converter sjablonen voor het converteren van gegevens in azure API voor FHIR.
services: healthcare-apis
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 01/19/2021
ms.author: ranku
ms.openlocfilehash: 7518f5e2984029c087eec1e6697f3237410bda4b
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018320"
---
# <a name="how-to-convert-data-to-fhir-preview"></a>Gegevens converteren naar FHIR (preview-versie)

> [!IMPORTANT]
> Deze functie is in openbare preview en wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Het aangepaste eind punt voor $convert gegevens in de Azure-API voor FHIR is bedoeld voor gegevens conversie van verschillende indelingen naar FHIR. Hierbij wordt gebruikgemaakt van de vloeistof sjabloon engine en de sjablonen van het [FHIR Converter](https://github.com/microsoft/FHIR-Converter) -project als de standaard sjablonen. U kunt deze conversie sjablonen naar wens aanpassen. Momenteel wordt de conversie van HL7v2 naar FHIR ondersteund.

## <a name="use-the-convert-data-endpoint"></a>Het $convert data-eind punt gebruiken

`https://<<FHIR service base URL>>/$convert-data`

$convert-gegevens in de hoofd tekst van de aanvraag, zoals hieronder wordt beschreven, worden een [parameter](http://hl7.org/fhir/parameters.html) resource gebruikt:

**Parameter resource:**

| Parameternaam      | Beschrijving | Geaccepteerde waarden |
| ----------- | ----------- | ----------- |
| Input data      | Gegevens die moeten worden geconverteerd. | Een geldige waarde voor het gegevens type van de JSON-teken reeks|
| inputDataType   | Gegevens type van invoer. | ```HL7v2``` |
| templateCollectionReference | Verwijzing naar een sjabloon verzameling. Dit kan een verwijzing zijn naar de **standaard sjablonen** of een aangepaste sjabloon installatie kopie die is geregistreerd bij Azure API voor FHIR. Hieronder vindt u meer informatie over het aanpassen van de sjablonen, het hosten van deze op ACR en het registreren van de Azure API voor FHIR.  | ```microsofthealth/fhirconverter:default```, \<RegistryServer\>/\<imageName\>@\<imageDigest\> |
| rootTemplate | De hoofd sjabloon die moet worden gebruikt tijdens het transformeren van de gegevens. | ```ADT_A01```, ```OML_O21```, ```ORU_R01```, ```VXU_V04``` |  

> [!WARNING]
> Met de standaard sjablonen kunt u snel aan de slag. Deze kunnen echter worden bijgewerkt tijdens de upgrade van de Azure API voor FHIR. Als u een consistent gegevens conversie gedrag wilt voor de verschillende versies van de Azure API voor FHIR, moet u uw eigen kopie van sjablonen op een Azure Container Registry hosten, deze registreren bij de Azure API voor FHIR en gebruiken in uw API-aanroepen zoals verderop beschreven.

**Voorbeeld aanvraag:**

```json
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "inputData",
            "valueString": "MSH|^~\\&|SIMHOSP|SFAC|RAPP|RFAC|20200508131015||ADT^A01|517|T|2.3|||AL||44|ASCII\nEVN|A01|20200508131015|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|\nPID|1|3735064194^^^SIMULATOR MRN^MRN|3735064194^^^SIMULATOR MRN^MRN~2021051528^^^NHSNBR^NHSNMBR||Kinmonth^Joanna^Chelsea^^Ms^^CURRENT||19870624000000|F|||89 Transaction House^Handmaiden Street^Wembley^^FV75 4GJ^GBR^HOME||020 3614 5541^HOME|||||||||C^White - Other^^^||||||||\nPD1|||FAMILY PRACTICE^^12345|\nPV1|1|I|OtherWard^MainRoom^Bed 183^Simulated Hospital^^BED^Main Building^4|28b|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|||CAR|||||||||16094728916771313876^^^^visitid||||||||||||||||||||||ARRIVED|||20200508131015||"
        },
        {
            "name": "inputDataType",
            "valueString": "Hl7v2"
        },
        {
            "name": "templateCollectionReference",
            "valueString": "microsofthealth/fhirconverter:default"
        },
        {
            "name": "rootTemplate",
            "valueString": "ADT_A01"
        }
    ]
}
```

**Voorbeeld antwoord:**

```json
{
  "resourceType": "Bundle",
  "type": "transaction",
  "entry": [
    {
      "fullUrl": "urn:uuid:9d697ec3-48c3-3e17-db6a-29a1765e22c6",
      "resource": {
        "resourceType": "Patient",
        "id": "9d697ec3-48c3-3e17-db6a-29a1765e22c6",
        ...
        ...
      "request": {
        "method": "PUT",
        "url": "Location/50becdb5-ff56-56c6-40a1-6d554dca80f0"
      }
    }
  ]
}
```

## <a name="customize-templates"></a>Sjablonen aanpassen

U kunt de [FHIR Converter-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) voor Visual Studio code gebruiken om de sjablonen aan te passen volgens uw behoeften. De uitbrei ding biedt een interactieve bewerkings ervaring en maakt het eenvoudig om door micro soft gepubliceerde sjablonen en voorbeeld gegevens te downloaden. Raadpleeg de documentatie in de uitbrei ding voor meer informatie.

## <a name="host-and-use-templates"></a>Sjablonen hosten en gebruiken

Het wordt ten zeerste aanbevolen om uw eigen kopie van sjablonen te hosten op ACR. Er zijn vier stappen voor het hosten van uw eigen kopie van sjablonen en het gebruik ervan in de $convert gegevens bewerking:

1. Push de sjablonen naar uw Azure Container Registry.
1. Schakel beheerde identiteit in voor uw Azure-API voor FHIR-instantie.
1. Geef toegang tot de ACR aan de Azure-API voor FHIR beheerde identiteit.
1. Registreer de ACR-servers in de Azure API voor FHIR.

### <a name="push-templates-to-azure-container-registry"></a>Sjablonen naar Azure Container Registry pushen

Nadat u een ACR-exemplaar hebt gemaakt, kunt u de opdracht _FHIR converter: push-sjablonen_ in de [FHIR Converter Extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) gebruiken om de aangepaste sjablonen naar de ACR te pushen. U kunt ook het [hulp programma voor sjabloon beheer cli](https://github.com/microsoft/FHIR-Converter/blob/main/docs/TemplateManagementCLI.md) gebruiken voor dit doel.

### <a name="enable-managed-identity-on-azure-api-for-fhir"></a>Beheerde identiteit inschakelen op de Azure-API voor FHIR

Blader naar uw exemplaar van de Azure API voor de FHIR-service in de Azure Portal en selecteer de Blade **identiteit** .
Wijzig de status in **on** om beheerde identiteit in te scha kelen in azure API voor FHIR.

![Beheerde identiteit inschakelen](media/convert-data/fhir-mi-enabled.png)

### <a name="provide-access-of-the-acr-to-azure-api-for-fhir"></a>Toegang bieden tot de ACR voor Azure-API voor FHIR

Navigeer naar Access Control Blade (IAM) in uw ACR-exemplaar en selecteer _roltoewijzingen toevoegen_.

![Roltoewijzing voor ACR](media/convert-data/fhir-acr-role-assignment.png)

Ken AcrPull-rol toe aan uw Azure-API voor FHIR service-exemplaar.

![Rol toevoegen](media/convert-data/fhir-acr-role-add.png)

### <a name="register-the-acr-servers-in-azure-api-for-fhir"></a>De ACR-servers registreren in azure API voor FHIR

U kunt Maxi maal twintig ACR-servers registreren in de Azure API voor FHIR.

Installeer de healthcareapis CLI van Azure PowerShell indien nodig:

```powershell
az extension add -n healthcareapis
```

Registreer de ACR-servers voor de Azure-API voor FHIR volgens de onderstaande voor beelden:

#### <a name="register-a-single-acr-server"></a>Eén ACR-server registreren

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

#### <a name="register-multiple-acr-servers"></a>Meerdere ACR-servers registreren

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io fhiracr2020.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

### <a name="verify"></a>Verifiëren

Ga naar de $convert-data-API die uw sjabloon verwijzing opgeeft in de para meter templateCollectionReference.

`<RegistryServer>/<imageName>@<imageDigest>`

## <a name="known-issues-and-workarounds"></a>Bekende problemen en tijdelijke oplossingen

- Sommige standaard sjabloon bestanden bevatten UTF-8-stuk lijst. Als gevolg hiervan bevatten de gegenereerde ID-waarden een stuk lijst teken. Dit kan een probleem met de FHIR-server veroorzaken. De tijdelijke oplossing is om micro soft-sjablonen te halen met behulp van de VS code-extensie en deze te pushen naar uw eigen ACR nadat u de stuk lijst tekens hebt verwijderd van _ID/_procedure. liquide_, _ID/_bewezen._ liquide, en _ID/_Immunization. liquide_.

