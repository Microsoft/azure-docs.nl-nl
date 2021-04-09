---
title: Cloud Services gebruiken (uitgebreide ondersteuning) (preview)
description: Meer informatie over het maken en implementeren van een Azure-Cloud service met behulp van Azure Resource Manager met Visual Studio
author: ghogen
ms.service: cloud-services-extended-support
manager: jillfra
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: tutorial
ms.date: 10/5/2020
ms.author: ghogen
ms.openlocfilehash: 80aa160c53b278137467dba2afa41384c7c4f378
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101722667"
---
# <a name="create-and-deploy-a-azure-cloud-service-extended-support-using-visual-studio"></a>Een Azure-Cloud service (uitgebreide ondersteuning) maken en implementeren met behulp van Visual Studio

Vanaf [Visual Studio 2019 versie 16,9](https://visualstudio.microsoft.com/vs/preview/) (momenteel in Preview) kunt u werken met Cloud Services met behulp van Azure Resource Manager (arm), waardoor het onderhoud en beheer van Azure-resources aanzienlijk eenvoudiger en moderner is. Dit wordt ingeschakeld door een nieuwe Azure-service waarnaar wordt verwezen als Cloud Services (uitgebreide ondersteuning). U kunt een bestaande Cloud service publiceren naar Cloud Services (uitgebreide ondersteuning). Zie [Cloud Services (uitgebreide ondersteunings documentatie)](overview.md)voor meer informatie over deze Azure-service.

> [!IMPORTANT]
> Cloud Services (uitgebreide ondersteuning) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="register-the-feature-for-your-subscription"></a>De functie voor uw abonnement registreren
Cloud Services (uitgebreide ondersteuning) is momenteel beschikbaar als preview-versie. Registreer de functie voor uw abonnement als volgt:

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```
Zie [vereisten voor de implementatie van Cloud Services (uitgebreide ondersteuning)](deploy-prerequisite.md) voor meer informatie

## <a name="create-a-project"></a>Een project maken

Visual Studio biedt een project sjabloon waarmee u een Azure-Cloud service met uitgebreide ondersteuning kunt maken, met de naam **Azure Cloud service (uitgebreide ondersteuning)**. Een Cloud service is een eenvoudige Azure-service voor algemeen gebruik. Zodra het project is gemaakt, kunt u met Visual Studio de Cloud service configureren, fouten opsporen en implementeren in Azure.

### <a name="to-create-an-azure-cloud-service-extended-support-project-in-visual-studio"></a>Een Azure Cloud service-project (Extended support) maken in Visual Studio

In deze sectie wordt uitgelegd hoe u een Azure-Cloud service project in Visual Studio maakt met een of meer webrollen.

1. Kies in het Start venster **een nieuw project maken**.

1. Typ in het zoekvak in de *Cloud* en kies vervolgens **Azure Cloud service (uitgebreide ondersteuning)**.

   ![Nieuwe Azure-Cloud service met uitgebreide ondersteuning](./media/choose-project-template.png)

1. Geef het project een naam en kies **maken**.

   ![Het project een naam geven](./media/configure-new-project.png)

1. Selecteer in het dialoog venster **nieuwe Microsoft Azure Cloud service** de functies die u wilt toevoegen, en kies de pijl-rechts knop om deze aan uw oplossing toe te voegen.

    ![Nieuwe Azure-Cloud service rollen selecteren](./media/choose-roles.png)

1. Als u de naam wilt wijzigen van een rol die u hebt toegevoegd, houdt u de muis aanwijzer op de rol in het dialoog venster **nieuwe Microsoft Azure Cloud service** en selecteert u **naam wijzigen** in het context menu. U kunt ook de naam van een rol binnen uw oplossing (in de **Solution Explorer**) wijzigen nadat deze is toegevoegd.

    ![Naam van Azure Cloud service-rol wijzigen](./media/new-cloud-service-rename.png)

Het Visual Studio Azure-project bevat koppelingen met de rol projecten in de oplossing. Het project bevat ook het *service definitie bestand* en *Service configuratie bestand*:

- **Service definitie bestand** : definieert de runtime-instellingen voor uw toepassing, met inbegrip van de vereiste functies, eind punten en grootte van de virtuele machine.
- **Service configuratie bestand** : Hiermee configureert u hoeveel exemplaren van een rol worden uitgevoerd en de waarden van de instellingen die zijn gedefinieerd voor een rol.

Zie [de functies voor een Azure-Cloud service configureren met Visual Studio](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service)voor meer informatie over deze bestanden.

## <a name="publish-a-cloud-service"></a>Een Cloud service publiceren

1. Maak of open een Azure-Cloud service project in Visual Studio.

1. Klik in **Solution Explorer** met de rechter muisknop op het project en selecteer in het context menu **publiceren**.

   ![Aanmeldings pagina](./media/publish-step-1.png)

1. **Account** : Selecteer een account of selecteer **een account toevoegen** in de vervolg keuzelijst account.

1. **Kies uw abonnement** : Kies het abonnement dat u voor uw implementatie wilt gebruiken. Het abonnement dat u gebruikt voor de implementatie van Cloud Services (uitgebreide ondersteuning) moet eigenaar of Inzender rollen hebben die zijn toegewezen via op rollen gebaseerd toegangs beheer (RBAC). Als uw abonnement niet een van deze rollen heeft, raadpleegt [u stappen om een roltoewijzing](../role-based-access-control/role-assignments-steps.md) toe te voegen voordat u verder gaat.

1. Klik op **volgende** om naar de pagina **instellingen** te gaan.

   ![Algemene instellingen](./media/publish-settings.png)

1. **Cloud service** : Selecteer een bestaande Cloud service in de vervolg keuzelijst, of selecteer **nieuwe maken** en maak een Cloud service. Het Data Center wordt tussen haakjes weer gegeven voor elke Cloud service. Het is raadzaam dat de locatie van het Data Center voor de Cloud service hetzelfde is als de locatie van het Data Center voor het opslag account.

   Als u ervoor kiest om een nieuwe Cloud service te maken, ziet u het dialoog venster **Cloud service maken (uitgebreide ondersteuning)** . Geef de locatie en de resource groep op die u wilt gebruiken voor de Cloud service.

   ![Een Cloud service met uitgebreide ondersteuning maken](./media/extended-support-dialog.png)

1. **Configuratie bouwen** : Selecteer **debug** of **release**.

1. **Service configuratie** : Selecteer **Cloud** of **lokaal**.

1. **Opslag account** : Selecteer het opslag account dat u voor deze implementatie wilt gebruiken of **Maak een nieuw** opslag account. De regio wordt tussen haakjes weer gegeven voor elk opslag account. Het is raadzaam om de locatie van het Data Center voor het opslag account hetzelfde te hebben als de locatie van het Data Center voor de Cloud service (algemene instellingen).

   Het Azure Storage-account slaat het pakket op voor de toepassings implementatie. Nadat de toepassing is geïmplementeerd, wordt het pakket verwijderd uit het opslag account.

1. **Key Vault** : geef de Key Vault op die de geheimen voor deze Cloud service bevat. Dit is ingeschakeld als extern bureau blad is ingeschakeld of certificaten zijn toegevoegd aan de configuratie.

1. **Extern bureaublad voor alle rollen inschakelen** : Selecteer deze optie als u extern verbinding wilt kunnen maken met de service. U wordt gevraagd om referenties op te geven.

   ![Instellingen voor extern bureau blad](./media/remote-desktop-configuration.png)

1. Klik op **volgende** om naar de pagina **Diagnostische instellingen** te gaan.

   ![Diagnostische instellingen](./media/diagnostics-settings.png)

   Met diagnostische gegevens kunt u problemen met een Azure-Cloud service (of virtuele machine van Azure) oplossen. Zie voor informatie over diagnostische gegevens over het [configureren van diagnostische gegevens voor Azure Cloud Services en virtual machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines). Zie [Wat is Application Insights?](../azure-monitor/app/app-insights-overview.md)voor informatie over Application Insights.

1. Klik op **volgende** om naar de pagina **samen vatting** te gaan.

   ![Samenvatting](./media/publish-summary.png)

1. **Doel profiel** : u kunt ervoor kiezen om een publicatie profiel te maken op basis van de instellingen die u hebt gekozen. U kunt bijvoorbeeld één profiel maken voor een test omgeving en een andere voor productie. Als u dit profiel wilt opslaan, kiest u het pictogram **Opslaan** . Met de wizard wordt het profiel gemaakt en opgeslagen in het Visual Studio-project. Als u de profiel naam wilt wijzigen, opent u de lijst met **doel profielen** en kiest u vervolgens **beheren...**.

   > [!Note]
   > Het publicatie profiel wordt weer gegeven in Solution Explorer in Visual Studio en de profiel instellingen worden naar een bestand met de extensie. azurePubxml geschreven. Instellingen worden opgeslagen als kenmerken van XML-codes.

1. Wanneer u alle instellingen voor de implementatie van uw project hebt geconfigureerd, selecteert u **publiceren** onder aan het dialoog venster. U kunt de proces status bewaken in het venster uitvoer van **Azure-activiteiten logboek** in Visual Studio.

Gefeliciteerd U hebt het uitgebreide ondersteunings Cloud service project gepubliceerd naar Azure. Als u opnieuw wilt publiceren met dezelfde instellingen, kunt u het publicatie profiel opnieuw gebruiken of de stappen herhalen om een nieuwe te maken.

## <a name="clean-up-azure-resources"></a>Azure-resources opschonen

Als u de Azure-resources wilt opschonen die u hebt gemaakt door deze zelf studie te volgen, gaat u naar de [Azure Portal](https://portal.azure.com), kiest u **resource groepen**, zoekt en opent u de resource groep die u hebt gebruikt om de service te maken en kiest u **resource groep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Stel doorlopende integratie in (CI) met behulp van de knop **configureren** op het scherm **publiceren** . Zie de [documentatie van Azure pipelines](/azure/devops/pipelines)voor meer informatie.