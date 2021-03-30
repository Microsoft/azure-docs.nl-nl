---
title: Een Git-opslag plaats toevoegen aan een lab in Azure DevTest Labs | Microsoft Docs
description: Meer informatie over het toevoegen van een GitHub-of Azure DevOps Services Git-opslag plaats voor uw aangepaste artefacten bron in Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 225aae5e0a017b711f29a47829b06f8bb7a6a8ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483139"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Een Git-opslag plaats toevoegen voor het opslaan van aangepaste artefacten en Resource Manager-sjablonen

U kunt [aangepaste artefacten maken](devtest-lab-artifact-author.md) voor de virtuele machines in uw lab of [Azure Resource Manager sjablonen gebruiken om een aangepaste test omgeving te maken](devtest-lab-create-environment-from-arm.md). U moet een persoonlijke Git-opslag plaats toevoegen voor de artefacten of Resource Manager-sjablonen die uw team maakt. De opslag plaats kan worden gehost op [github](https://github.com) of op [Azure DevOps Services](https://visualstudio.com).

We bieden een [github-opslag plaats van artefacten](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) die u kunt implementeren, of u kunt ze aanpassen voor uw Labs. Wanneer u een artefact aanpast of maakt, kunt u het artefact niet opslaan in de open bare opslag plaats. U moet uw eigen persoonlijke opslag plaats maken voor aangepaste artefacten en voor artefacten die u maakt. 

Wanneer u een virtuele machine maakt, kunt u de Resource Manager-sjabloon opslaan, deze aanpassen als u wilt en deze later gebruiken om meer Vm's te maken. U moet uw eigen privé-opslag plaats maken om uw aangepaste Resource Manager-sjablonen op te slaan.  

* Zie [github Bootcamp](https://help.github.com/categories/bootcamp/)voor meer informatie over het maken van een github-opslag plaats.
* Zie [verbinding maken met Azure DevOps Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)voor meer informatie over het maken van een Azure DevOps Services-project met een Git-opslag plaats.

De volgende afbeelding is een voor beeld van hoe een opslag plaats met artefacten eruit kan zien in GitHub:  

![Voor beeld van GitHub-artefacten opslag plaats](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>De gegevens van de opslag plaats en referenties ophalen
Als u een opslag plaats aan uw Lab wilt toevoegen, moet u eerst belang rijke informatie uit uw opslag plaats ophalen. In de volgende secties wordt beschreven hoe u de vereiste informatie krijgt voor opslag plaatsen die worden gehost op GitHub of Azure DevOps Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>De kloon-URL van de GitHub-opslag plaats en het persoonlijke toegangs Token ophalen

1. Ga naar de start pagina van de GitHub-opslag plaats die de definities van de artefact-of Resource Manager-sjabloon bevat.
2. Selecteer **Klonen of downloaden**.
3. Als u de URL naar het klem bord wilt kopiëren, selecteert u de knop **https-kloon-URL** . Sla de URL op voor later gebruik.
4. Selecteer de profiel afbeelding in de rechter bovenhoek van GitHub en selecteer vervolgens **instellingen**.
5. Selecteer in het menu **persoonlijke instellingen** aan de linkerkant **persoonlijke toegangs tokens**.
6. Selecteer **nieuw token genereren**.
7. Voer op de pagina **nieuw persoonlijk toegangs token** onder **Beschrijving van token** een beschrijving in. Accepteer de standaard items onder **scopes selecteren** en selecteer vervolgens **token genereren**.
8. Sla het gegenereerde token op. U kunt het token later gebruiken.
9. Sluit GitHub.   
10. Ga door naar de sectie [uw Lab verbinden met de opslag plaats](#connect-your-lab-to-the-repository) .

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>De Azure opslag plaatsen kloon-URL en het persoonlijke toegangs Token ophalen

1. Ga naar de start pagina van uw team verzameling (bijvoorbeeld `https://contoso-web-team.visualstudio.com` ) en selecteer vervolgens uw project.
2. Selecteer op de start pagina van het project **code**.
3. Als u de kloon-URL wilt weer geven, selecteert u in de project **code** pagina **klonen**.
4. Sla de URL op. U kunt de URL later gebruiken.
5. Als u een persoonlijk toegangs token wilt maken, selecteert u in de vervolg keuzelijst gebruikers account **Mijn profiel**.
6. Selecteer op de pagina profiel informatie **beveiliging**.
7. Selecteer **toevoegen** op het tabblad **beveiliging** .
8. Op de pagina **een persoonlijk toegangs token maken** :
   1. Voer een **Beschrijving** in voor het token.
   2. Selecteer **180 dagen** in de lijst **verloopt in** .
   3. Selecteer in de lijst **accounts** **alle toegankelijke accounts**.
   4. Selecteer de optie **alleen-lezen** .
   5. Selecteer **token maken**.
9. Het nieuwe token wordt weer gegeven in de lijst met **persoonlijke toegangs tokens** . Selecteer **token kopiëren** en sla de token waarde op voor later gebruik.
10. Ga door naar de sectie [uw Lab verbinden met de opslag plaats](#connect-your-lab-to-the-repository) .

## <a name="connect-your-lab-to-the-repository"></a>Uw Lab verbinden met de opslag plaats
1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecteer **meer services** en selecteer vervolgens **DevTest Labs** in de lijst met Services.
3. Selecteer in de lijst met Labs uw Lab. 
4. Selecteer **configuratie en beleid**  >  **opslag**  >  **plaatsen + toevoegen**.

    ![De knop opslag plaats toevoegen](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Op de tweede pagina **opslag** plaatsen geeft u de volgende informatie op:
   1. **Naam**. Voer een naam in voor de opslag plaats.
   2. **Git clone URL**. Voer de Git HTTPS kloon-URL in die u eerder hebt gekopieerd uit GitHub of Azure DevOps Services.
   3. **Vertakking**. Voer de vertakking in om uw definities op te halen.
   4. **Persoonlijk toegangs token**. Voer het persoonlijke toegangs token in dat u eerder hebt ontvangen van een GitHub-of Azure DevOps-service.
   5. **Mappaden**. Voer ten minste één mappad in ten opzichte van de kloon-URL die uw sjabloon definities voor artefacten of Resource Manager bevat. Wanneer u een submap opgeeft, moet u ervoor zorgen dat u de slash in het mappad opneemt.

      ![Opslag gebied](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Selecteer **Opslaan**.

### <a name="related-blog-posts"></a>Gerelateerde blog berichten
* [Problemen met mislukte artefacten in DevTest Labs oplossen](devtest-lab-troubleshoot-artifact-failure.md)
* [Een virtuele machine toevoegen aan een bestaand Active Directory domein met behulp van een resource manager-sjabloon in DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Volgende stappen
Nadat u uw persoonlijke Git-opslag plaats hebt gemaakt, kunt u een of beide van de volgende handelingen uitvoeren, afhankelijk van uw behoeften:
* Uw [aangepaste artefacten](devtest-lab-artifact-author.md)opslaan. U kunt deze later gebruiken om nieuwe virtuele machines te maken.
* [Maak omgevingen met meerdere vm's en Paas bronnen met behulp van Resource Manager-sjablonen](devtest-lab-create-environment-from-arm.md). Vervolgens kunt u de sjablonen opslaan in uw persoonlijke opslag plaats.

Wanneer u een virtuele machine maakt, kunt u controleren of de artefacten of sjablonen worden toegevoegd aan uw Git-opslag plaats. Ze zijn onmiddellijk beschikbaar in de lijst met artefacten of sjablonen. De naam van uw persoonlijke opslag plaats wordt weer gegeven in de kolom waarin de bron is opgegeven. 
