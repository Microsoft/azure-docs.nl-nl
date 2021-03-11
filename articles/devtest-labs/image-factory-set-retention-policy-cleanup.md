---
title: Bewaar beleid instellen in Azure DevTest Labs | Microsoft Docs
description: Meer informatie over het configureren van een Bewaar beleid, het opschonen van de fabriek en het buiten gebruik stellen van oude installatie kopieën van DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 85384e88f8d456c7bf67302a57618d7a9703a5ee
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550022"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Bewaar beleid instellen in Azure DevTest Labs
In dit artikel vindt u informatie over het instellen van een Bewaar beleid, het opschonen van de fabriek en het buiten gebruik stellen van oude installatie kopieën van alle andere DevTest Labs in de organisatie. 

## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat u deze artikelen hebt gevolgd voordat u verder gaat:

- [Een fabrieksinstallatiekopie maken](image-factory-create.md)
- [Een fabrieksinstallatiekopie uitvoeren vanuit Azure DevOps](image-factory-set-up-devops-lab.md)
- [Aangepaste installatiekopieën opslaan en distribueren naar meerdere labs](image-factory-save-distribute-custom-images.md)

De volgende items moeten al aanwezig zijn:

- Een Lab voor de installatie kopie-Factory in Azure DevTest Labs
- Een of meer doel Azure DevTest Labs waar de fabriek Golden-installatie kopieën zal distribueren
- Een Azure DevOps-project waarmee de installatie kopie-Factory wordt geautomatiseerd.
- Locatie van de bron code met de scripts en configuratie (in dit voor beeld in hetzelfde DevOps-project dat hierboven wordt gebruikt)
- Een build-definitie voor het organiseren van de Azure Power shell-taken
 
## <a name="setting-the-retention-policy"></a>Het Bewaar beleid instellen
Voordat u de stappen voor opschonen configureert, definieert u hoeveel historische installatie kopieën u in de DevTest Labs wilt behouden. Wanneer u het artikel [een installatie kopie uitvoeren uit de Azure DevOps uitvoert](image-factory-set-up-devops-lab.md) , hebt u verschillende build-variabelen geconfigureerd. Een van deze is **ImageRetention**. U stelt deze variabele in op `1` , wat betekent dat de DevTest Labs geen geschiedenis van aangepaste installatie kopieën bijhoudt. Alleen de meest recente gedistribueerde installatie kopieën zijn beschikbaar. Als u deze variabele wijzigt in `2` , wordt de meest recente gedistribueerde installatie kopie plus de vorige opgenomen. U kunt deze waarde instellen om het aantal historische installatie kopieën te definiëren dat u wilt behouden in uw DevTest Labs.

## <a name="cleaning-up-the-factory"></a>De fabriek opschonen
De eerste stap bij het opschonen van de fabriek bestaat uit het verwijderen van de industriële installatie kopie-Vm's uit de installatie kopie-Factory. Er is een script om deze taak uit te voeren op dezelfde manier als onze vorige scripts. De eerste stap bestaat uit het toevoegen van een andere **Azure Power shell** -taak aan de build-definitie, zoals wordt weer gegeven in de volgende afbeelding:

![Power shell-stap](./media/set-retention-policy-cleanup/powershell-step.png)

Wanneer u de nieuwe taak in de lijst hebt, selecteert u het item en vult u alle details in, zoals wordt weer gegeven in de volgende afbeelding:

![De Power shell-taak oude installatie kopieën opruimen](./media/set-retention-policy-cleanup/configure-powershell-task.png)

De script parameters zijn: `-DevTestLabName $(devTestLabName)` .

## <a name="retire-old-images"></a>Oude installatie kopieën buiten gebruik stellen 
Met deze taak worden oude installatie kopieën verwijderd, waarbij alleen een geschiedenis wordt bijgehouden die overeenkomt met de **ImageRetention** -build-variabele. Een extra **Azure Power shell** -build-taak toevoegen aan de build-definitie. Zodra deze is toegevoegd, selecteert u de taak en vult u de details in, zoals wordt weer gegeven in de volgende afbeelding: 

![De Power shell-taak oude installatie kopieën buiten gebruik stellen](./media/set-retention-policy-cleanup/retire-old-image-task.png)

De script parameters zijn: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>De build in de wachtrij plaatsen
Nu u de build-definitie hebt voltooid, moet u een nieuwe build in de wachtrij plaatsen om er zeker van te zijn dat alles werkt. Nadat de build is voltooid, worden de nieuwe aangepaste installatie kopieën weer gegeven in het bestemmings Lab en als u het lab van de installatie kopie op de afbeelding controleert, ziet u geen ingerichte Vm's. Als u nog meer builds in de wachtrij hebt geplaatst, ziet u dat de verouderde taken voor het buiten gebruik stellen van oude aangepaste installatie kopieën uit de DevTest Labs in overeenstemming zijn met de retentie waarde die is ingesteld in de build-variabelen.

> [!NOTE]
> Als u de build-pijp lijn aan het einde van het laatste artikel in de reeks hebt uitgevoerd, verwijdert u hand matig de virtuele machines die zijn gemaakt in het lab voor installatie kopieën voordat u een nieuwe build maakt in de wachtrij.  De stap hand matig opschonen is alleen nodig om alles uit te stellen en te controleren of deze werkt.



## <a name="summary"></a>Samenvatting
U hebt nu een image Factory die aangepaste installatie kopieën kan genereren en distribueren naar uw Labs op aanvraag. Op dit moment is het slechts een kwestie van het correct instellen van uw installatie kopieën en het identificeren van de doel-Labs. Zoals vermeld in het vorige artikel, specificeert de **Labs.jsvoor** het bestand in de map **Configuration** welke installatie kopieën beschikbaar moeten worden gemaakt in elk van de doel-Labs. Wanneer u andere DevTest Labs toevoegt aan uw organisatie, hoeft u alleen maar een vermelding toe te voegen aan de Labs.jsop voor het nieuwe lab.

Het toevoegen van een nieuwe installatie kopie aan uw fabriek is ook eenvoudig. Wanneer u een nieuwe installatie kopie in uw fabriek wilt toevoegen, gaat u naar [Azure Portal](https://portal.azure.com)de DevTest Labs en selecteert u de knop voor het toevoegen van een virtuele machine en kiest u de gewenste installatie kopie en artefacten van de Marketplace. In plaats van de knop **maken** te selecteren om de nieuwe VM te maken, selecteert u **Azure Resource Manager sjabloon weer geven** en slaat u de sjabloon op als een JSON-bestand ergens in de map **GoldenImages** in uw opslag plaats. De volgende keer dat u de installatie kopie-Factory uitvoert, wordt uw aangepaste installatie kopie gemaakt.


## <a name="next-steps"></a>Volgende stappen
1. [Plan uw build/release](/azure/devops/pipelines/build/triggers?tabs=designer) om de installatie kopie in de fabriek periodiek uit te voeren. De door de fabriek gegenereerde installatie kopieën worden regel matig vernieuwd.
2. Maak meer gouden installatie kopieën voor uw fabriek. U kunt ook overwegen om [artefacten te maken](devtest-lab-artifact-author.md) om extra onderdelen van uw VM-installatie taken te script en de artefacten in uw fabrieks installatie kopieën op te nemen.
4. Maak een [afzonderlijke build/release](/azure/devops/pipelines/overview?view=azure-devops-2019) om het **DistributeImages** -script afzonderlijk uit te voeren. U kunt dit script uitvoeren wanneer u wijzigingen aanbrengt in Labs.jsen afbeeldingen kopieert naar doel-Labs zonder dat u alle installatie kopieën opnieuw hoeft te maken.

