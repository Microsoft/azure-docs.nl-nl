---
title: Azure FarmBeats installeren
description: In dit artikel wordt beschreven hoe u Azure FarmBeats installeert in uw Azure-abonnement
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 5863dcc20fb13f1bb203c68ad168655371130601
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674562"
---
# <a name="install-azure-farmbeats"></a>Azure FarmBeats installeren

In dit artikel wordt beschreven hoe u Azure FarmBeats installeert in uw Azure-abonnement.

Azure FarmBeats is een Business-to-business-aanbieding die beschikbaar is in Azure Marketplace. Hiermee kunnen aggregatie van agrarische gegevens sets worden verzameld over providers en kan er inzicht hebben in het genereren van actie bare inzichten. Azure FarmBeats doet dit door u in staat te stellen om modellen van kunst matige intelligentie (AI) of machine learning (ML) te bouwen op basis van gegevens sets met zekering. De twee belangrijkste onderdelen van Azure FarmBeats zijn:

- **Datahub**: een API-laag die aggregatie, normalisatie en contextualization van verschillende agrarische gegevens sets in verschillende providers mogelijk maakt.

- **Accelerator**: Web-app die is gebouwd op Datahub. Het gaat hier om de ontwikkeling en visualisatie van uw model. De Accelerator maakt gebruik van Azure FarmBeats-Api's om de visualisatie van opgenomen sensor gegevens te demonstreren als grafieken en visualisatie van model uitvoer als kaarten.

## <a name="general-information"></a>Algemene informatie

### <a name="components-installed"></a>Geïnstalleerde onderdelen

Wanneer u Azure FarmBeats installeert, worden de volgende resources ingericht in uw Azure-abonnement:

| Geïnstalleerde Azure-resources  | Azure FarmBeats-onderdeel  |
|---------|---------|
| Application Insights   |      Datahub & Accelerator      |
| App Service     |     Datahub & Accelerator     |
| App Service-plan   | Datahub & Accelerator  |
| API-verbinding    |  Datahub       |
| Azure Cache voor Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Datahub & Accelerator      |
| Azure Batch-account    | Datahub   |
| Azure Key Vault |  Datahub & Accelerator        |
| Azure Maps-account       |     Accelerator    |
| Event hub-naam ruimte    |     Datahub      |
| Logische apps      |  Datahub       |
| Opslagaccount      |     Datahub & Accelerator      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Gemaakte kosten

De kosten van Azure FarmBeats zijn een samen vatting van de kosten van de onderliggende Azure-Services. Prijs informatie voor Azure-Services kan worden berekend met behulp van de [prijs calculator](https://azure.microsoft.com/pricing/calculator). De werkelijke kosten van de totale installatie variëren op basis van het gebruik. De constante status kosten voor de twee onderdelen zijn:

- Datahub-minder dan $10 per dag
- Accelerator-minder dan $2 per dag

### <a name="regions-supported"></a>Regio's worden ondersteund

Momenteel wordt Azure FarmBeats ondersteund in open bare Cloud omgevingen in de volgende regio's:

- Australië - oost
- VS - centraal
- VS - oost
- VS - oost 2
- VS - west
- VS - west 2
- Europa - noord
- Europa -west
- Azië - oost
- Zuidoost-Azië

### <a name="time-taken"></a>Gebruikte tijd

De volledige installatie van Azure FarmBeats, met inbegrip van de voor bereiding en installatie, duurt minder dan een uur.

## <a name="prerequisites"></a>Vereisten

U moet de volgende stappen uitvoeren voordat u begint met de daad werkelijke installatie van Azure FarmBeats:

### <a name="verify-permissions"></a>Machtigingen verifiëren

U hebt de volgende machtigingen nodig in de Azure-Tenant om Azure FarmBeats te installeren:

- Tenant: AAD-app-maker
- Abonnement-eigenaar
- De resource groep waarin FarmBeats wordt geïnstalleerd-eigenaar

De eerste twee machtigingen zijn nodig voor [het maken van de procedure voor de Aad-toepassing](#create-an-aad-application) . Als dat nodig is, kunt u iemand met de juiste machtigingen krijgen voor het maken van de AAD-toepassing.

De persoon die de FarmBeats-installatie uitvoert vanuit Marketplace moet een eigenaar zijn van de resource groep waarin FarmBeats wordt geïnstalleerd. Voor abonnements eigenaren gebeurt dit automatisch wanneer de resource groep wordt gemaakt. Voor anderen maakt u eerst de resource groep en vraagt u de eigenaar van het abonnement om u een eigenaar van de resource groep te maken.

U kunt uw toegangs machtigingen in de Azure Portal controleren door de instructies te volgen in [toegangs beheer op basis van rollen](../../role-based-access-control/check-access.md).

### <a name="decide-subscription-and-region"></a>Abonnement en regio kiezen

U hebt de Azure-abonnements-ID en de regio waar u Azure FarmBeats wilt installeren. Kies een van de regio's die worden vermeld in de sectie regio's die worden [ondersteund](#regions-supported) .

Noteer de ID van het **Azure-abonnement** en de **Azure-regio**.

### <a name="create-an-aad-application"></a>Een AAD-toepassing maken

Voor Azure FarmBeats moet Azure Active Directory toepassing worden gemaakt en geregistreerd. U hebt de volgende machtigingen nodig om het AAD-script te maken:

- Tenant: AAD-app-maker
- Abonnement-eigenaar

Voer de volgende stappen uit in een Cloud Shell-exemplaar met behulp van de Power shell-omgeving. De eerste keer dat gebruikers worden gevraagd om een abonnement te selecteren en een opslag account te maken. Voltooi de installatie volgens de instructies.

1. Het [Aad-app-Generator script](https://aka.ms/FarmBeatsAADScript) downloaden

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. Standaard wordt het bestand gedownload naar uw basismap. Navigeer naar de map.

    ```azurepowershell-interactive
        cd
    ```

3. Het AAD-script uitvoeren

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. Het script vraagt om de volgende drie invoer:

    - **FarmBeats-website naam**: dit is het unieke URL-voor voegsel voor uw FarmBeats-webtoepassing. Als het voor voegsel al wordt gemaakt, wordt er een fout opgetreden in het script. Na de installatie is uw FarmBeats-implementatie toegankelijk via https:// \<FarmBeats-website-name> . azurewebsites.net en zijn de Swagger-api's op https:// \<FarmBeats-website-name> -API.azurewebsites.net

    - **Azure-aanmeldings-id**: Geef de Azure-aanmeldings-id op voor de gebruiker die u wilt toevoegen als beheerder van FarmBeats. Deze gebruiker kan vervolgens toegang tot FarmBeats-webtoepassing verlenen aan andere gebruikers. De aanmeldings-ID is in het algemeen van het formulier john.doe@domain.com . Azure UPN wordt ook ondersteund.

    - **Abonnements-id**: dit is de id van het abonnement waarin u Azure FarmBeats wilt installeren

5. Het AAD-script duurt ongeveer twee minuten om waarden op het scherm uit te voeren en af te ronden op een JSON-bestand in dezelfde map. Als u iemand anders het script hebt uitgevoerd, vraagt u hen deze uitvoer met u te delen.

### <a name="create-sentinel-account"></a>Verklikker account maken

Met de Azure FarmBeats-installatie kunt u satelliet installatie kopieën van de inspectie missie van de [Sentinel-2-](https://scihub.copernicus.eu/) satelliet van het Euro pees ruimte-agentschap verkrijgen voor uw farm. Als u deze configuratie wilt configureren, hebt u een Sentinel-account nodig.

Volg de stappen voor het maken van een gratis account met Sentinel:

1. Ga naar de officiële [registratie](https://aka.ms/SentinelRegistration) pagina.
2. Geef de vereiste gegevens op (voor naam, achternaam, gebruikers naam, wacht woord en e-mail-ID) en vul het formulier in.
3. Er wordt een verificatie koppeling naar de geregistreerde e-mail-ID verzonden. Selecteer de koppeling die in het e-mail bericht wordt gegeven en voltooi de verificatie.

Het registratie proces is voltooid. Noteer uw **Sentinel-gebruikers naam** en **verklikker wachtwoord** wanneer de verificatie ook is voltooid.

## <a name="install"></a>Installeren

U bent nu klaar om FarmBeats te installeren. Volg de onderstaande stappen om de installatie te starten:

1. Meld u aan bij Azure Portal. Selecteer uw account in de rechter bovenhoek en schakel over naar de Azure AD-Tenant waar u Azure FarmBeats wilt installeren.

2. Ga in de portal naar Azure Marketplace en zoek naar **Azure FarmBeats** in de Marketplace.

3. Er wordt een nieuw venster met een overzicht van Azure FarmBeats weer gegeven. Selecteer **Maken**.

4. Een nieuw venster wordt weergegeven. Voltooi het registratie proces door het juiste abonnement, de resource groep en de locatie te kiezen waarop u Azure FarmBeats wilt installeren.

5. Geef het e-mail adres op waarvoor service waarschuwingen met betrekking tot Azure FarmBeats moeten worden ontvangen in de sectie **FarmBeats service Alerts** . Selecteer **volgende** onder aan de pagina om naar het tabblad **afhankelijkheden** te gaan.

    ![Tabblad basis beginselen](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. Kopieer de afzonderlijke vermeldingen van de uitvoer van [Aad-script](#create-an-aad-application) naar de invoer in de sectie Aad-toepassing.

7. Voer de gebruikers naam en het wacht woord van het [Sentinel-account](#create-sentinel-account) in het gedeelte verklikker account in. Selecteer **volgende** om naar het tabblad **controleren + maken** te gaan.

    ![Tabblad Afhankelijkheden](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. Zodra de ingevoerde gegevens zijn gevalideerd, selecteert u **OK**. De pagina Gebruiksvoorwaarden wordt weer gegeven. Bekijk de voor waarden en selecteer **maken** om de installatie te starten. U wordt omgeleid naar de pagina waar u de voortgang van de installatie kunt volgen.

Nadat de installatie is voltooid, kunt u de installatie controleren en de FarmBeats-portal gaan gebruiken door te navigeren naar de naam van de website die u tijdens de installatie hebt ingesteld: https:// \<FarmBeats-website-name> . azurewebsites.net. U ziet de FarmBeats-gebruikers interface met een optie voor het maken van farms.

**Datahub** vindt u op https:// \<FarmBeats-website-name> -API.azurewebsites.net/Swagger. Hier ziet u de verschillende API-objecten van FarmBeats en worden REST-bewerkingen uitgevoerd op de Api's.

## <a name="upgrade"></a>Upgrade uitvoeren

Als u de FarmBeats wilt bijwerken naar de nieuwste versie, voert u de volgende stappen uit in een Cloud Shell-exemplaar met behulp van de Power shell-omgeving. De gebruiker moet de eigenaar zijn van het abonnement waarin FarmBeats is geïnstalleerd.

De eerste keer dat gebruikers worden gevraagd om een abonnement te selecteren en een opslag account te maken. Voltooi de installatie volgens de instructies.

1. Het [upgrade script](https://aka.ms/FarmBeatsUpgradeScript) downloaden

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. Standaard wordt het bestand gedownload naar uw basismap. Navigeer naar de map.

    ```azurepowershell-interactive
        cd
    ```

3. Het upgrade script uitvoeren

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

Het pad naar input.jsin het bestand is optioneel. Als u dit niet opgeeft, wordt u door het script gevraagd om alle vereiste invoer. De upgrade moet in ongeveer 30 minuten worden voltooid.

## <a name="uninstall"></a>Verwijderen

Voer de volgende stappen uit om Azure FarmBeats Datahub of Accelerator te verwijderen:

1. Meld u aan bij de Azure Portal en **Verwijder de resource groepen** waarin deze onderdelen zijn geïnstalleerd.

2. Ga naar Azure Active Directory & **de Azure AD-toepassing te verwijderen** die aan de Azure FarmBeats-installatie is gekoppeld.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u Azure FarmBeats kunt installeren in uw Azure-abonnement. Nu leert u hoe u [gebruikers kunt toevoegen](manage-users-in-azure-farmbeats.md#manage-users) aan uw Azure FarmBeats-exemplaar.