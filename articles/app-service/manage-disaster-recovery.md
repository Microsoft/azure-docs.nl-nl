---
title: Herstellen uit de regio-brede fout
description: Meer informatie over hoe Azure App Service u helpt de mogelijkheden voor bedrijfs continuïteit en herstel na nood gevallen (BCDR) te onderhouden. Herstel uw app op basis van een probleem met de hele regio in Azure.
ms.topic: how-to
ms.date: 06/09/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 1388dc11254324f74efcbaa55c97cac2ccd0c026
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87073736"
---
# <a name="move-an-app-service-app-to-another-region"></a>Een App Service-app naar een andere regio verplaatsen

In dit artikel wordt beschreven hoe u App Service bronnen weer online brengt in een andere Azure-regio tijdens een nood geval die van invloed is op een hele Azure-regio. Wanneer een nood geval een hele Azure-regio offline brengt, worden alle App Service-apps die in die regio worden gehost, in de modus voor herstel na nood gevallen geplaatst. Er zijn functies beschikbaar om u te helpen bij het herstellen van de app naar een andere regio of het herstellen van bestanden van de betreffende app.

App Service resources zijn regio-specifiek en kunnen niet worden verplaatst naar andere regio's. U moet de app naar een nieuwe app in een andere regio herstellen en vervolgens spiegel configuraties of bronnen voor de nieuwe app maken.

## <a name="prerequisites"></a>Vereisten

- Geen. Voor het [herstellen van de moment opname](app-service-web-restore-snapshots.md) is doorgaans de **Premium** -laag vereist, maar in de modus voor herstel na nood gevallen wordt deze automatisch ingeschakeld voor uw getroffen app, ongeacht in welke laag de betrokken app zich bevindt.

## <a name="prepare"></a>Voorbereiden

Identificeer alle App Service resources die worden beïnvloed door de app die momenteel wordt gebruikt. Bijvoorbeeld:

- App Service-apps
- [App Service-abonnementen](overview-hosting-plans.md)
- [Implementatiesites](deploy-staging-slots.md)
- [Aangepaste domeinen die zijn gekocht in azure](manage-custom-dns-buy-domain.md)
- [SSL-certificaten](configure-ssl-certificate.md)
- [Integratie van Azure Virtual Network](web-sites-integrate-with-vnet.md)
- [Hybride verbindingen](app-service-hybrid-connections.md).
- [Beheerde identiteiten](overview-managed-identity.md)
- [Back-upinstellingen](manage-backup.md)

Bepaalde resources, zoals geïmporteerde certificaten of hybride verbindingen, bevatten integratie met andere Azure-Services. Zie de documentatie voor de desbetreffende services voor informatie over het verplaatsen van deze resources over regio's.

## <a name="restore-app-to-a-different-region"></a>App herstellen naar een andere regio

1. Maak een nieuwe App Service-app in een *andere* Azure-regio dan de betreffende app. Dit is de doel-app in het scenario voor herstel na nood gevallen.

1. Ga in het [Azure Portal](https://portal.azure.com)naar de beheer pagina van de impact van de app. In een mislukte Azure-regio toont de betrokken app een waarschuwings tekst. Klik op de tekst van de waarschuwing.

    ![Scherm afbeelding van de pagina met impact van de app. Er wordt een waarschuwings bericht weer gegeven waarin de situatie wordt beschreven en een koppeling wordt geboden voor het herstellen van de app.](media/manage-disaster-recovery/restore-start.png)

1. Configureer op de pagina **back-up terugzetten** de herstel bewerking volgens de volgende tabel. Klik op **OK** wanneer u klaar bent.

   | Instelling | Waarde | Beschrijving |
   |-|-|-|
   | **Moment opname (preview-versie)** | Selecteer een moment opname. | De twee meest recente moment opnamen zijn beschikbaar. |
   | **Doel herstellen** | **Bestaande app** | Klik hieronder op de opmerking om de doel-app **voor herstellen te wijzigen** en de doel-app te selecteren. In een nood geval kunt u de moment opname alleen herstellen naar een app in een andere Azure-regio. |
   | **Site configuratie herstellen** | **Ja** | |

    ![Scherm afbeelding van de back-uppagina voor terugzetten. Een specifieke moment opname, de opties die in de voor gaande tabel worden weer gegeven en de knop OK zijn gemarkeerd.](media/manage-disaster-recovery/restore-configure.png)

3. Configureer [alle andere](#prepare) in de doel-app om de betrokken app te spie gelen en controleer uw configuratie.

4. Wanneer u klaar bent voor het aangepaste domein om te verwijzen naar de doel-app, moet u [de domein naam opnieuw toewijzen](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

## <a name="recover-app-content-only"></a>Alleen app-inhoud herstellen

Als u alleen de bestanden van de betrokken app wilt herstellen zonder deze te herstellen, gebruikt u de volgende stappen:

1. Navigeer in het [Azure Portal](https://portal.azure.com)naar de beheer pagina van de impact van de app en klik op **publicatie profiel ophalen**.

    ![Scherm afbeelding van de pagina met impact van de app. Er wordt een waarschuwings melding weer gegeven, maar deze is niet gemarkeerd. In plaats daarvan is het item publicatie profiel ophalen gemarkeerd.](media/manage-disaster-recovery/get-publish-profile.png)

1. Open het gedownloade bestand en zoek het publicatie profiel met `ReadOnly - FTP` de naam. Dit is het nood herstel profiel. Bijvoorbeeld:

    ```xml
    <publishProfile profileName="%app-name% - ReadOnly - FTP" publishMethod="FTP" publishUrl="ftp://%ftp-site%/site/wwwroot" ftpPassiveMode="True" userName="%app-name%\$%app-name%" userPWD="" destinationAppUrl="http://%app-name%.azurewebsites.net" SQLServerDBConnectionString="" mySQLDBConnectionString="" hostingProviderForumLink="" controlPanelLink="http://windows.azure.com" webSystem="WebSites">
        <databases />
    </publishProfile>
    ```
    
    Kopieer drie kenmerk waarden: 
        
    - `publishUrl`: de FTP-hostnaam
    - `userName` en `userPWD` : de FTP-referenties

1. Gebruik de FTP-client van uw keuze, maak verbinding met de FTP-host van de betreffende app met behulp van de hostnaam en referenties.

1. Nadat de verbinding is gemaakt, downloadt u de volledige map */site/wwwroot* . De volgende scherm afbeelding laat zien hoe u kunt downloaden in [filezilla](https://filezilla-project.org/).

    ![Scherm opname van een FileZilla-bestands hiërarchie. De map wwwroot wordt gemarkeerd en het bijbehorende snelmenu wordt weer gegeven. Down load is in dat menu gemarkeerd.](media/manage-disaster-recovery/download-content.png)

## <a name="next-steps"></a>Volgende stappen
[Een app in azure herstellen vanuit een moment opname](app-service-web-restore-snapshots.md)
