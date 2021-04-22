---
title: Zelfstudie voor Azure Data Box bestellen | Microsoft Docs
description: In deze zelfstudie leert u over Azure Data Box, een hybride oplossing waarmee u on-premises gegevens kunt importeren in Azure en hoe u Azure Data Box kunt bestellen.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: ed539df9fec3c66b3881257ff1ce68d03cf4c382
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878592"
---
# <a name="tutorial-order-azure-data-box"></a>Zelfstudie: Azure Data Box bestellen

Azure Data Box is een hybride oplossing waarmee u uw on-premises gegevens snel, gemakkelijk en betrouwbaar in Azure kunt importeren. U kunt uw gegevens overdragen naar een door Microsoft geleverd opslagapparaat van 80 TB (gebruikscapaciteit) en het apparaat vervolgens terug verzenden. Deze gegevens worden vervolgens geüpload in Azure.

In deze zelfstudie wordt beschreven hoe u een Azure Data Box bestelt. In deze zelfstudie komen deze onderwerpen aan bod:  

> [!div class="checklist"]
>
> * Vereisten om Data Box te implementeren
> * Een Data Box bestellen
> * De bestelling volgen
> * De bestelling annuleren

## <a name="prerequisites"></a>Vereisten

# <a name="portal"></a>[Portal](#tab/portal)

Zorg dat u aan de volgende configuratievereisten voor de Data Box-service en het apparaat voldoet voordat u het apparaat implementeert:

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

U kunt u aanmelden bij Azure en Azure CLI-opdrachten uitvoeren. Dit kan op twee manieren:

* U kunt de CLI installeren en CLI-opdrachten lokaal uitvoeren.
* U kunt CLI-opdrachten uitvoeren vanuit Azure Portal, in Azure Cloud Shell.

Voor de zelfstudie gebruiken we Azure CLI via Windows PowerShell, maar het staat u vrij om een van beide opties te kiezen.

### <a name="for-azure-cli"></a>Voor Azure CLI

Zorg voordat u begint voor het volgende:

#### <a name="install-the-cli-locally"></a>De CLI lokaal installeren

* [Azure CLI](/cli/azure/install-azure-cli) (versie 2.0.67 of hoger) installeren. U kunt ook [installeren met behulp van MSI](https://aka.ms/installazurecliwindows).

**Aanmelden bij Azure**

Open een Windows PowerShell-opdrachtvenster en meld u aan bij Azure met de opdracht [az login](/cli/azure/reference-index#az_login):

```azurecli
PS C:\Windows> az login
```

Dit is de uitvoer bij een geslaagde aanmelding:

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

**De Azure Data Box CLI-extensie installeren**

Voordat u de Azure Data Box CLI-opdrachten kunt gebruiken, moet u de extensie installeren. Azure CLI-extensies geven u toegang tot experimentele opdrachten en opdrachten in een evaluatieversie die nog niet zijn verzonden als onderdeel van de kern-CLI. Zie [Extensies gebruiken met Azure CLI](/cli/azure/azure-cli-extensions-overview) voor meer informatie over extensies.

Voer de volgende opdracht uit om de extensie voor Azure Data Box te installeren: `az extension add --name databox`:

```azurecli

    PS C:\Windows> az extension add --name databox
```

Als de extensie is geïnstalleerd, ziet u de volgende uitvoer:

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

#### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell gebruiken

U kunt [Azure Cloud Shell](https://shell.azure.com/), een door Azure gehoste interactieve shell-omgeving, gebruiken via uw browser om CLI-opdrachten uit te voeren. Azure Cloud Shell biedt ondersteuning voor Bash of Windows PowerShell met Azure-services. De Azure CLI is vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Selecteer de knop Cloud Shell in het menu in het gedeelte rechtsboven in Azure Portal:

![Menuselectie Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

Met de knop start u een interactieve shell waarmee u alle stappen in dit instructieartikel kunt uitvoeren.

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

### <a name="for-azure-powershell"></a>Voor Azure PowerShell

Zorg voordat u begint dat u het volgende hebt gedaan:

* Windows PowerShell 6.2.4 of hoger installeren.
* De AZ-module van Azure PowerShell installeren.
* De Azure Data Box-module (Az.DataBox) installeren.
* Meld u aan bij Azure.

#### <a name="install-azure-powershell-and-modules-locally"></a>Azure PowerShell en modules lokaal installeren

**Windows PowerShell installeren of bijwerken**

U moet Windows PowerShell-versie 6.2.4 of hoger hebben geïnstalleerd. Als u wilt weten welke versie van PowerShell u hebt geïnstalleerd, voert u de volgende opdracht uit: `$PSVersionTable`.

U ziet de volgende uitvoer:

```azurepowershell
    PS C:\users\gusp> $PSVersionTable
    
    Name                           Value
    ----                           -----
    PSVersion                      6.2.3
    PSEdition                      Core
    GitCommitId                    6.2.3
    OS                             Microsoft Windows 10.0.18363
    Platform                       Win32NT
    PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0…}
    PSRemotingProtocolVersion      2.3
    SerializationVersion           1.1.0.1
    WSManStackVersion              3.0
```

Als uw versie lager is dan 6.2.4, moet u een upgrade uitvoeren van uw versie van Windows PowerShell. Zie [Azure PowerShell installeren](/powershell/scripting/install/installing-powershell) voor informatie over het installeren van de meest recente versie van Windows PowerShell.

**Azure PowerShell- en Data Box-modules installeren**

U moet de Azure PowerShell-modules installeren om Azure PowerShell te gebruiken om een Azure Data Box te bestellen. Ga als volgt te werk om de Azure PowerShell-modules te installeren:

1. Installeer de [Azure PowerShell Az-module](/powershell/azure/new-azureps-module-az).
2. Installeer vervolgens Az.DataBox met behulp van de opdracht `Install-Module -Name Az.DataBox`.

```azurepowershell
PS C:\PowerShell\Modules> Install-Module -Name Az.DataBox
PS C:\PowerShell\Modules> Get-InstalledModule -Name "Az.DataBox"

Version              Name                                Repository           Description
-------              ----                                ----------           -----------
0.1.1                Az.DataBox                          PSGallery            Microsoft Azure PowerShell - DataBox ser…
```

#### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Open een Windows PowerShell-opdrachtvenster en meld u aan bij Azure met de opdracht [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount):

```azurepowershell
PS C:\Windows> Connect-AzAccount
```

Dit is de uitvoer bij een geslaagde aanmelding:

```output
WARNING: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FSBFZMBKC to authenticate.

Account              SubscriptionName                          TenantId                             Environment
-------              ----------------                          --------                             -----------
gusp@contoso.com     MySubscription                            aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa AzureCloud

PS C:\Windows\System32>
```

Zie [Aanmelden met Azure PowerShell](/powershell/azure/authenticate-azureps) voor meer informatie over het aanmelden bij Azure met behulp van Windows PowerShell.

---

## <a name="order-data-box"></a>Data Box bestellen

# <a name="portal"></a>[Portal](#tab/portal)

Voer de volgende stappen uit in Azure Portal om een apparaat te bestellen.

1. Gebruik uw Microsoft Azure-referenties om u aan te melden op deze URL: [https://portal.azure.com](https://portal.azure.com).
2. Selecteer **+ Een resource maken** en zoek naar *Azure Data Box*. Selecteer **Azure Data Box**.

   ![Schermopname van de nieuwe sectie met Azure Data Box in het zoekveld](media/data-box-deploy-ordered/select-data-box-import-02.png)

3. Selecteer **Maken**.

   ![Schermopname van de sectie Azure Data Box met de optie 'Maken' uitgelicht](media/data-box-deploy-ordered/select-data-box-import-03.png)

4. Controleer of de Data Box-service beschikbaar is in uw regio. Voer de volgende gegevens in of selecteer deze en selecteer **Toepassen**.

    |Instelling  |Waarde  |
    |---------|---------|
    |Type overdracht     | Selecteer **Importeren in Azure**.        |
    |Abonnement     | Selecteer een EA-, CSP- of Azure Sponsorship-abonnement voor de Data Box-service. <br> Het abonnement is gekoppeld aan uw factureringsrekening.       |
    |Resourcegroep | Selecteer een bestaande resourcegroep. Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd. |
    |Bronland/-regio    |    Selecteer het land/de regio waar uw gegevens zich momenteel bevinden.         |
    |Doel-Azure-regio     |     Selecteer de Azure-regio waarnaar u uw gegevens wilt overdragen. <br> Ga naar [Beschikbaarheid in de regio](data-box-overview.md#region-availability) voor meer informatie.            |

    [![Een importorder Azure Data Box starten ](media/data-box-deploy-ordered/select-data-box-import-04-b.png)](media/data-box-deploy-ordered/select-data-box-import-04-b.png#lightbox)

5. Selecteer **Data Box**. De maximale bruikbare capaciteit voor één bestelling is 80 TB. U kunt meerdere bestellingen doen voor grotere gegevensgrootten.

    ![Beschikbare gegevensgrootten: Data Box Disk 40 TB, Data Box 100 TB, Data Box Heavy, 1000 TB. Uw eigen schijven verzenden 1 TB](media/data-box-deploy-ordered/select-data-box-import-05.png)

6. Ga in **Bestelling** naar het tabblad **Basisinformatie**. Voer de volgende gegevens in of selecteer deze en selecteer **Volgende: Gegevensbestemming>** .

    |Instelling  |Waarde  |
    |---------|---------|
    |Abonnement      | Het abonnement wordt automatisch ingevuld op basis van uw eerdere selectie.|
    |Resourcegroep    | De resourcegroep die u eerder hebt geselecteerd. |
    |Naam van importorder | Geef een beschrijvende naam op om de bestelling te volgen. <br> De naam kan tussen 3 en 24 tekens bevatten (letters, cijfers en afbreekstreepjes). <br> De naam moet beginnen en eindigen met een letter of cijfer.    |

    ![Wizard voor Data Box-importorder, het scherm Basisgegevens, ingevuld met de juiste gegevens](media/data-box-deploy-ordered/select-data-box-import-06.png)

7. Selecteer op het scherm **Gegevensdoel** het **Gegevensdoel** - opslagaccounts of beheerde schijven.

    Als u een **opslagaccount** selecteert als de opslaglocatie, ziet u het volgende scherm:

    ![Wizard voor Data Box-importorder, het scherm Gegevensbestemming, met de opslagaccounts geselecteerd](media/data-box-deploy-ordered/select-data-box-import-07.png)

    Selecteer op basis van de opgegeven Azure-regio een of meer opslagaccounts in de gefilterde lijst met bestaande opslagaccounts. Data Box kan worden gekoppeld aan maximaal 10 opslagaccounts. U kunt ook een nieuw account van het type **Algemeen gebruik v1**, **Algemeen gebruik v2** of **Blob-opslag** maken.

   > [!NOTE]
   > - Als u Azure Premium FileStorage-accounts selecteert, wordt het inrichtende quotum voor de opslagaccount-share vergroot tot de grootte van de gegevens die naar de bestands shares worden gekopieerd. Nadat het quotum is verhoogd, wordt het niet opnieuw aangepast, bijvoorbeeld als de Data Box uw gegevens niet kan kopiëren.
   > - Dit quotum wordt gebruikt voor facturering. Nadat uw gegevens naar het datacenter zijn geüpload, moet u het quotum aanpassen aan uw behoeften. Zie Inzicht in facturering [voor meer informatie.](../../articles/storage/files/understanding-billing.md)

    Opslagaccounts met virtuele netwerken worden ondersteund. Als u wilt dat de Data Box-service kan werken met beveiligde opslagaccounts, schakelt u in de firewallinstellingen van het opslagaccount de vertrouwde services in. Zie [Azure Data Box toevoegen als een vertrouwde service](../storage/common/storage-network-security.md#exceptions) voor meer informatie.

    Als u Data Box gebruikt om **beheerde schijven** te maken op basis van de on-premises VHD's (virtuele harde schijven), moet u ook de volgende informatie opgeven:

    |Instelling  |Waarde  |
    |---------|---------|
    |Resourcegroepen     | Maak nieuwe resourcegroepen als u beheerde schijven wilt maken van on-premises virtuele harde schijven. U kunt alleen een bestaande resourcegroep gebruiken als de resourcegroep eerder is gemaakt en beschikbaar was op het moment van het plaatsen van een Data Box-bestelling voor beheerde schijven door de Data Box-service. <br> U kunt meerdere resourcegroepen opgeven door de namen te scheiden met een puntkomma. Er worden maximaal tien resourcegroepen ondersteund.|

    ![Wizard voor Data Box-importorder, het scherm Gegevensbestemming, met Managed Disks geselecteerd](media/data-box-deploy-ordered/select-data-box-import-07-b.png)

    Het opslagaccount dat is opgegeven voor beheerde schijven wordt gebruikt als een opslagaccount waarin de gegevens worden klaargezet. De Data Box-service uploadt de virtuele harde schijven als pagina-blobs naar dit opslagaccount waarna de schijven worden omgezet in beheerde schijven en naar de resourcegroepen worden verplaatst. Zie [Uploaden van gegevens naar Azure controleren](data-box-deploy-picked-up.md#verify-data-upload-to-azure) voor meer informatie.

   > [!NOTE]
   > Als een pagina-blob niet naar een beheerde schijf is geconverteerd, blijft deze in het opslagaccount en worden er kosten in rekening gebracht voor opslag.

8. Selecteer **Volgende: Beveiliging** om door te gaan.

    In het scherm **Beveiliging** kunt u uw eigen versleutelingssleutel en uw eigen wachtwoord voor apparaten en shares gebruiken, en ervoor kiezen om dubbele versleuteling te gebruiken.

    Alle instellingen op het scherm **Beveiliging** zijn optioneel. Als u geen instellingen wijzigt, worden de standaardinstellingen toegepast.

    ![Het scherm Beveiliging van de wizard Data Box-importorder](media/data-box-deploy-ordered/select-data-box-import-security-01.png)

9. Als u uw eigen door de klant beheerde sleutel wilt gebruiken om de ontgrendelingswachtwoordsleutel voor uw nieuwe resource te beveiligen, vouwt u **Versleutelingstype** uit.

    Het configureren van een door de klant beheerde sleutel voor uw Azure Data Box is optioneel. Data Box maakt standaard gebruik van een door Microsoft beheerde sleutel om de ontgrendelingswachtwoordsleutel te beveiligen.

    Een door de klant beheerde sleutel is niet van invloed op hoe gegevens op het apparaat worden versleuteld. De sleutel wordt alleen gebruikt voor het versleutelen van de ontgrendelingswachtwoordsleutel voor het apparaat.

    Als u geen door de klant beheerde sleutel wilt gebruiken, gaat u verder met stap 15.

   ![Beveiligingsscherm met instellingen voor het Versleutelingstype](./media/data-box-deploy-ordered/customer-managed-key-01.png)

10. Selecteer **Door de klant beheerde sleutel** als het sleuteltype. Selecteer vervolgens **Een sleutelkluis en sleutel selecteren**.
   
    ![Beveiligingsscherm, instellingen voor een door de klant beheerde sleutel](./media/data-box-deploy-ordered/customer-managed-key-02.png)

11. Het abonnement wordt automatisch ingevuld in de blade **Sleutel selecteren in Azure Key Vault**.

    - Voor **Sleutelkluis** kunt u een bestaande sleutelkluis selecteren in de vervolgkeuzelijst.

      ![Sleutel selecteren in het scherm van Azure Key Vault](./media/data-box-deploy-ordered/customer-managed-key-03.png)

    - U kunt ook **Nieuwe maken** selecteren om een nieuwe sleutelkluis te maken. Voer de resourcegroep en de naam van een sleutelkluis in op het scherm **Sleutelkluis maken**. Zorg ervoor dat **Voorlopig verwijderen** en **Beveiliging tegen leegmaken** zijn ingeschakeld. Accepteer de overige standaardwaarden en selecteer **Beoordelen + Maken**.

      ![Nieuwe instellingen voor Azure Key Vault maken](./media/data-box-deploy-ordered/customer-managed-key-04.png)

      Controleer de informatie voor uw sleutelkluis en selecteer **Maken**. Wacht enkele minuten tot het maken van de sleutelkluis is voltooid.

      ![Nieuw controlescherm voor Azure Key Vault](./media/data-box-deploy-ordered/customer-managed-key-05.png)

12. In **Sleutel selecteren in Azure Key Vault**, kunt u een bestaande sleutel selecteren in de sleutelkluis.

    ![Bestaande sleutel selecteren in Azure Key Vault](./media/data-box-deploy-ordered/customer-managed-key-06.png)

    Als u een nieuwe sleutel wilt maken, selecteert u **Nieuwe maken**. U moet een RSA-sleutel gebruiken. De grootte kan 2048 of meer zijn. Voer een naam in voor de nieuwe sleutel, accepteer de andere standaardwaarden en selecteer **Maken**.

      ![Een nieuwe sleuteloptie maken](./media/data-box-deploy-ordered/customer-managed-key-07.png)

      U krijgt een melding wanneer de sleutel in uw sleutelkluis is gemaakt.

13. Selecteer de **Versie** van de te gebruiken sleutel en kies vervolgens **Selecteren**.

      ![Nieuwe sleutel gemaakt in sleutelkluis](./media/data-box-deploy-ordered/customer-managed-key-08.png)

    Als u een nieuwe sleutelversie wilt maken, selecteert u **Nieuwe maken**.

    ![Open een dialoogvenster voor het maken van een nieuwe sleutelversie](./media/data-box-deploy-ordered/customer-managed-key-08-a.png)

    Kies instellingen voor de nieuwe sleutelversie en selecteer **Maken**.

    ![Een nieuwe sleutel versie maken](./media/data-box-deploy-ordered/customer-managed-key-08-b.png)

    De instellingen van het **Versleutelingstype** in het scherm **Beveiliging** geven uw sleutelkluis en sleutel weer.

    ![Sleutel en sleutelkluis voor een door de klant beheerde sleutel](./media/data-box-deploy-ordered/customer-managed-key-09.png)

14. Selecteer een gebruikersidentiteit die u gaat gebruiken voor het beheren van de toegang tot deze resource. Kies **Een gebruikersidentiteit selecteren**. Selecteer in het deelvenster aan de rechterkant het abonnement en de beheerde identiteit die u wilt gebruiken. Kies dan de optie **Selecteren**.

    Een door de gebruiker toegewezen beheerde identiteit is een zelfstandige Azure-resource die kan worden gebruikt voor het beheren van meerdere resources. Zie [Beheerde identiteitstypen](../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.  

    Als u een nieuwe beheerde identiteit wilt maken, volgt u de richtlijnen in [Een rol maken, weergeven, verwijderen of toewijzen aan een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
    
    ![Een gebruikersidentiteit selecteren](./media/data-box-deploy-ordered/customer-managed-key-10.png)

    De gebruikersidentiteit wordt weergegeven in de instellingen van **Versleutelingstype**.

    ![Een geselecteerde gebruikersidentiteit die wordt weergegeven in de instellingen van Versleutelingstype](./media/data-box-deploy-ordered/customer-managed-key-11.png)

15. Als u geen gebruik wilt maken van de door het systeem gegenereerde wachtwoorden die Azure Data Box standaard gebruikt, vouwt u **Uw eigen wachtwoord gebruiken** uit op het scherm **Beveiliging**.

    De door het systeem gegenereerde wachtwoorden zijn veilig en worden aanbevolen, tenzij uw organisatie anders vereist.

    ![Uitgevouwen opties voor het gebruiken van een eigen wachtwoord voor een Data Box-importorder](media/data-box-deploy-ordered/select-data-box-import-security-02.png) 

   - Als u uw eigen wachtwoord wilt gebruiken voor uw nieuwe apparaat, selecteert u **Voorkeur voor het apparaatwachtwoord instellen**. Selecteer **Uw eigen wachtwoord gebruiken** en typ een wachtwoord dat aan de beveiligingsvereisten voldoet.
     
     Het wachtwoord moet alfanumeriek zijn en moet tussen 12 en 15 tekens lang zijn, met ten minste één hoofdletter, één kleine letter, één speciaal teken en één cijfer. 

     - Toegestane speciale tekens: @ # - $ % ^ ! + = ; : _ ( )
     - Tekens niet toegestaan: I i L o O 0
   
     ![Opties voor het gebruiken van uw eigen apparaatwachtwoord in het scherm Beveiliging voor een Data Box-importorder](media/data-box-deploy-ordered/select-data-box-import-security-03.png)

 - Uw eigen wachtwoorden gebruiken voor shares:

   1. Selecteer bij **Voorkeur voor share-wachtwoorden instellen** de optie **Uw eigen wachtwoorden gebruiken** en vervolgens **Wachtwoorden voor de shares selecteren**.
     
       ![Opties voor het gebruiken van uw eigen sharewachtwoord in het scherm Beveiliging voor een Data Box-importorder](media/data-box-deploy-ordered/select-data-box-import-security-04.png)

    1. Typ een wachtwoord voor elk opslagaccount in de order. Het wachtwoord wordt gebruikt voor alle shares voor het opslagaccount.
    
       Het wachtwoord moet alfanumeriek zijn en moet tussen 12 en 64 tekens lang zijn, met ten minste één hoofdletter, één kleine letter, één speciaal teken en één cijfer.

       - Toegestane speciale tekens: @ # - $ % ^ ! + = ; : _ ( )
       - Tekens niet toegestaan: I i L o O 0
     
    1. Selecteer **Kopiëren naar alle** om hetzelfde wachtwoord te gebruiken voor alle opslagaccounts. 

    1. Selecteer **Opslaan** wanneer u klaar bent.
     
       ![Scherm voor het invoeren van share-wachtwoorden voor een Data Box-importorder](media/data-box-deploy-ordered/select-data-box-import-security-05.png)

    In het scherm **Beveiliging** kunt u met **Wachtwoorden weergeven of wijzigen** de wachtwoorden wijzigen.

16. Als u dubbele versleuteling op basis van software wilt inschakelen, selecteert u in **Beveiliging** de optie **Dubbele versleuteling (voor zeer veilige omgevingen)** en selecteer **Dubbele versleuteling inschakelen voor de order**.

    ![Beveiligingsscherm voor Data Box-import waarin softwareversleuteling wordt ingeschakeld voor een Data Box-order](media/data-box-deploy-ordered/select-data-box-import-security-07.png)

    De op software gebaseerde versleuteling wordt uitgevoerd naast de AES-256-bits versleuteling van de gegevens op de Data Box.

    > [!NOTE]
    > Als u deze optie inschakelt, kan verwerking van de opdracht en het kopiëren van gegevens langer duren. U kunt deze optie niet wijzigen nadat u uw opdracht hebt gemaakt.

    Selecteer **Volgende: Contactgegevens** om verder te gaan.

17. Selecteer in **Contactgegevens** de optie **+ Verzendadres toevoegen**.

    ![Voeg vanuit het scherm Contactgegevens verzendadressen toe aan uw Azure Data Box-importorder](media/data-box-deploy-ordered/select-data-box-import-08-a.png)

18. Bij **Verzendadres** geeft u uw voor- en achternaam, de naam en het postadres van het bedrijf en een geldig telefoonnummer op. Selecteer **Adres valideren**. De service controleert of de service beschikbaar is voor de regio van het verzendadres. Als de service beschikbaar is voor het opgegeven verzendadres, ontvangt u daarover een melding.

    ![Schermopname van het dialoogvenster Verzendadres toevoegen met de opties Verzenden met en Verzendadres toevoegen uitgelicht.](media/data-box-deploy-ordered/select-data-box-import-10.png)

    Als u zelfbeheerde verzending hebt geselecteerd, ontvangt u een e-mailmelding nadat de bestelling is geplaatst. Zie [Zelfbeheerde verzending gebruiken](data-box-portal-customer-managed-shipping.md) voor meer informatie over zelfbeheerde verzendingen.

19. Selecteer **Verzendadres toevoegen** zodra de verzendgegevens zijn gevalideerd. U gaat terug naar het tabblad **Contactgegevens**.

20. Voeg wanneer u weer in **Contactgegevens** bent, een of meer e-mailadressen toe. De service stuurt e-mailmeldingen naar het opgegeven e-mailadres over updates van de bestelstatus.

    We raden u aan een e-mailadres van een groep te gebruiken, zodat u meldingen blijft ontvangen als een beheerder de groep verlaat.

    ![De sectie E-mail van Contactgegevens in de wizard Bestelling](media/data-box-deploy-ordered/select-data-box-import-08-c.png)

21. Bekijk de gegevens met betrekking tot de bestelling, het contact, de meldingen en de privacyvoorwaarden in **Controleren en bestellen**. Vink het selectievakje aan waarmee u akkoord gaat met de privacyvoorwaarden.

22. Selecteer **Bestellen**. Het duurt een paar minuten voordat de bestelling is gemaakt.

    ![Het scherm Controleren en bestellen van de wizard Bestellen](media/data-box-deploy-ordered/select-data-box-import-11.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Voer de volgende stappen uit met behulp van Azure CLI om een apparaat te bestellen:

1. Noteer uw instellingen voor uw Data Box-bestelling. Deze instellingen omvatten uw persoonlijke/zakelijke gegevens, abonnementsnaam, apparaatgegevens en verzendgegevens. U moet deze instellingen als parameters gebruiken bij het uitvoeren van de CLI-opdracht om de Data Box-bestelling te maken. In de volgende tabel ziet u de parameterinstellingen die worden gebruikt voor `az databox job create`:

   | Instelling (parameter) | Beschrijving |  Voorbeeldwaarde |
   |---|---|---|
   |resource-group| Gebruik een bestaande of maak een nieuwe. Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd. | "myresourcegroup"|
   |naam| De naam van de bestelling die u maakt. | "mydataboxorder"|
   |contact-name| De naam die is gekoppeld aan het verzendadres. | "Gus Poland"|
   |telefoon| Het telefoonnummer van de persoon die of het bedrijf dat de bestelling zal ontvangen.| "14255551234"
   |location| De dichtstbijzijnde Azure-regio voor u waaruit uw apparaat wordt verzonden.| "US - west"|
   |sku| Het specifieke Data Box-apparaat dat u bestelt. Geldige waarden zijn: "DataBox", "DataBoxDisk" en "DataBoxHeavy"| "DataBox" |
   |email-list| De e-mailadressen die zijn gekoppeld aan de bestelling.| "gusp@contoso.com" |
   |street-address1| Het adres waarnaar de bestelling wordt verzonden. | "15700 NE 39th St" |
   |street-address2| De secundaire adresgegevens, zoals het appartementnummer of het gebouwnummer. | Gebouw 123 |
   |city| De plaats waarnaar het apparaat wordt verzonden. | "Redmond" |
   |state-or-province| De staat/provincie waarnaar het apparaat wordt verzonden.| "WA" |
   |country| Het land waarnaar het apparaat wordt verzonden. | "United States" |
   |postal-code| De postcode die is gekoppeld aan het verzendadres.| "98052"|
   |company-name| De naam van het bedrijf waarvoor u werkt.| "Contoso, LTD" |
   |opslagaccount| Het Azure Storage account waaruit u gegevens wilt importeren.| "mystorageaccount"|
   |debug| Informatie over foutopsporing opnemen in uitgebreide logboekregistratie  | --debug |
   |help| Help-informatie weergeven voor deze opdracht. | --help -h |
   |only-show-errors| Alleen fouten weergeven, waarschuwingen onderdrukken. | --only-show-errors |
   |output -o| Hiermee stelt u de uitvoerindeling in.  Toegestane waarden: json, jsonc, none, table, tsv, yaml, yamlc. De standaardwaarde is json. | --output "json" |
   |query| De JMESPath-querytekenreeks. Zie [JMESPath](http://jmespath.org/) voor meer informatie. | --query <string>|
   |verbose| Uitgebreide logboekregistratie opnemen. | --verbose |

2. Voer in de opdrachtprompt van uw keuze of terminal de opdracht [az data box job create](/cli/azure/databox/job#az_databox_job_create) uit om uw Azure Data Box-order te maken.

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   Hier volgt een voorbeeld van het gebruik van de opdracht:

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   Dit is de uitvoer van het uitvoeren van de opdracht:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. Alle Azure CLI-opdrachten gebruiken standaard json als de uitvoerindeling, tenzij u deze wijzigt. U kunt de uitvoerindeling wijzigen met behulp van de algemene parameter `--output <output-format>`. Als u de indeling wijzigt in 'tabel', is de uitvoer beter leesbaar.

   Dit is dezelfde opdracht die we zojuist hebben uitgevoerd met een kleine aanpassing om de opmaak te wijzigen:

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   Dit is de uitvoer van het uitvoeren van de opdracht:

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

Voer de volgende stappen uit met behulp van Azure PowerShell om een apparaat te bestellen:

1. Voordat u de importbestelling maakt, moet u uw opslagaccount ophalen en het opslagaccountobject in een variabele opslaan.

   ```azurepowershell
    $storAcct = Get-AzStorageAccount -Name "mystorageaccount" -ResourceGroup "myresourcegroup"
   ```

2. Noteer uw instellingen voor uw Data Box-bestelling. Deze instellingen omvatten uw persoonlijke/zakelijke gegevens, abonnementsnaam, apparaatgegevens en verzendgegevens. U moet deze instellingen als parameters gebruiken bij het uitvoeren van de PowerShell-opdracht om de Data Box-bestelling te maken. In de volgende tabel ziet u de parameterinstellingen die worden gebruikt voor [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob).

    | Instelling (parameter) | Beschrijving |  Voorbeeldwaarde |
    |---|---|---|
    |ResourceGroupName [vereist]| Gebruik een bestaande resourcegroep. Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd. | "myresourcegroup"|
    |Name [vereist]| De naam van de bestelling die u maakt. | "mydataboxorder"|
    |ContactName [vereist]| De naam die is gekoppeld aan het verzendadres. | "Gus Poland"|
    |PhoneNumber [vereist]| Het telefoonnummer van de persoon die of het bedrijf dat de bestelling zal ontvangen.| "14255551234"
    |Location [vereist]| De dichtstbijzijnde Azure-regio voor u waaruit uw apparaat wordt verzonden.| "WestUS"|
    |DataBoxType [vereist]| Het specifieke Data Box-apparaat dat u bestelt. Geldige waarden zijn: "DataBox", "DataBoxDisk" en "DataBoxHeavy"| "DataBox" |
    |EmailId [vereist]| De e-mailadressen die zijn gekoppeld aan de bestelling.| "gusp@contoso.com" |
    |StreetAddress1 [vereist]| Het adres waarnaar de bestelling wordt verzonden. | "15700 NE 39th St" |
    |StreetAddress2| De secundaire adresgegevens, zoals het appartementnummer of het gebouwnummer. | Gebouw 123 |
    |StreetAddress3| De derde adresgegevens. | |
    |City [vereist]| De plaats waarnaar het apparaat wordt verzonden. | "Redmond" |
    |StateOrProvinceCode [vereist]| De staat/provincie waarnaar het apparaat wordt verzonden.| "WA" |
    |CountryCode [vereist]| Het land waarnaar het apparaat wordt verzonden. | "United States" |
    |PostalCode [vereist]| De postcode die is gekoppeld aan het verzendadres.| "98052"|
    |CompanyName| De naam van het bedrijf waarvoor u werkt.| "Contoso, LTD" |
    |StorageAccountResourceId [vereist]| De id van het Azure Storage-account waaruit u gegevens wilt importeren.| <AzStorageAccount>.id |

3. Gebruik in de opdrachtprompt van uw keuze of terminal de opdracht [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob) om uw Azure Data Box-bestelling te maken.

   ```azurepowershell
    PS> $storAcct = Get-AzureStorageAccount -StorageAccountName "mystorageaccount"
    PS> New-AzDataBoxJob -Location "WestUS" \
                         -StreetAddress1 "15700 NE 39th St" \
                         -PostalCode "98052" \
                         -City "Redmond" \
                         -StateOrProvinceCode "WA" \
                         -CountryCode "US" \
                         -EmailId "gusp@contoso.com" \
                         -PhoneNumber 4255551234 \
                         -ContactName "Gus Poland" \
                         -StorageAccount $storAcct.id \
                         -DataBoxType DataBox \
                         -ResourceGroupName "myresourcegroup" \
                         -Name "myDataBoxOrderPSTest"
   ```

   Dit is de uitvoer van het uitvoeren van de opdracht:

   ```output
    jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
    ----------------     -------------------- ------------------ --------------------- -------------------- -------------
    myDataBoxOrderPSTest DataBox              DeviceOrdered      07-06-2020 05:25:30   westus               myresourcegroup
   ```

---

## <a name="track-the-order"></a>De bestelling volgen

# <a name="portal"></a>[Portal](#tab/portal)

Nadat u uw bestelling hebt geplaatst, kunt u de status van de bestelling volgen via de Azure-portal. Ga naar uw Data Box-bestelling en vervolgens naar **Overzicht** om de status te bekijken. In de portal ziet u dat de bestelling de status **Besteld** heeft.

Als het apparaat niet beschikbaar is, ontvangt u een melding. Als het apparaat wel beschikbaar is, identificeert Microsoft het apparaat dat moet worden verzonden en bereidt Microsoft de verzending voor. Tijdens de voorbereiding van het apparaat vinden de volgende acties plaats:

* Voor elk opslagaccount dat aan het apparaat is gekoppeld, worden SMB-shares gemaakt.
* Voor elke share worden toegangsreferenties zoals een gebruikersnaam en wachtwoord gegenereerd.
* Er wordt ook een apparaatwachtwoord gegenereerd om het apparaat mee te ontgrendelen.
* De Data Box wordt vergrendeld om onbevoegde toegang tot het apparaat te voorkomen.

Wanneer de apparaatvoorbereiding is voltooid, wordt de bestelling in de portal weergegeven met de status **Verwerkt**.

![Een Data Box-bestelling die is verwerkt](media/data-box-overview/data-box-order-status-processed.png)

Microsoft bereidt de verzending vervolgens voor en verzendt het apparaat met een regionale vervoerder. U ontvangt uw volgnummer zodra het apparaat is verzonden. In de portal wordt bestelling weergegeven met de status **Verzonden**.

![Een Data Box-bestelling die is verzonden](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

### <a name="track-a-single-order"></a>Eén bestelling volgen

Voer uit om traceringsinformatie over één bestaande Azure Data Box op te [`az databox job show`](/cli/azure/databox/job#az_databox_job_show) halen. De opdracht geeft informatie weer over de bestelling, zoals, maar niet beperkt tot: naam, resourcegroep, traceringsinformatie, abonnements-ID, contactgegevens, type verzending en apparaat-SKU.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   In de volgende tabel ziet u de parameterinformatie voor `az databox job show`:

   | Parameter | Beschrijving |  Voorbeeldwaarde |
   |---|---|---|
   |resource-group [vereist]| De naam van de resourcegroep die is gekoppeld aan de bestelling. Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd. | "myresourcegroup"|
   |name [vereist]| De naam van de bestelling die moet worden weergegeven. | "mydataboxorder"|
   |debug| Informatie over foutopsporing opnemen in uitgebreide logboekregistratie | --debug |
   |help| Help-informatie weergeven voor deze opdracht. | --help -h |
   |only-show-errors| Alleen fouten weergeven, waarschuwingen onderdrukken. | --only-show-errors |
   |output -o| Hiermee stelt u de uitvoerindeling in.  Toegestane waarden: json, jsonc, none, table, tsv, yaml, yamlc. De standaardwaarde is json. | --output "json" |
   |query| De JMESPath-querytekenreeks. Zie [JMESPath](http://jmespath.org/) voor meer informatie. | --query <string>|
   |verbose| Uitgebreide logboekregistratie opnemen. | --verbose |

   Hier volgt een voorbeeld van de opdracht met de uitvoerindeling ingesteld op 'tabel':

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   Dit is de uitvoer van het uitvoeren van de opdracht:

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> Weergeven van de bestelling kan worden ondersteund op abonnementsniveau en dat maakt de resourcegroep tot een optionele parameter (in plaats van een vereiste parameter).

### <a name="list-all-orders"></a>Alle bestellingen weergeven

Als u meerdere apparaten hebt besteld, kunt u uitvoeren om [`az databox job list`](/cli/azure/databox/job#az_databox_job_list) al uw Azure Data Box bekijken. Met de opdracht wordt een lijst weergegeven met alle bestellingen die deel uitmaken van een specifieke resourcegroep. Het volgende wordt ook weergegeven in de uitvoer: naam van bestelling, status van verzending, Azure-regio, leveringstype, status van bestelling. Geannuleerde orders worden ook opgenomen in de lijst.
De opdracht geeft ook tijdstempels van elke bestelling weer.

```azurecli
az databox job list --resource-group <resource-group>
```

In de volgende tabel ziet u de parameterinformatie voor `az databox job list`:

   | Parameter | Beschrijving |  Voorbeeldwaarde |
   |---|---|---|
   |resource-group [vereist]| De naam van de resourcegroep die de bestellingen bevat. Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd. | "myresourcegroup"|
   |debug| Informatie over foutopsporing opnemen in uitgebreide logboekregistratie | --debug |
   |help| Help-informatie weergeven voor deze opdracht. | --help -h |
   |only-show-errors| Alleen fouten weergeven, waarschuwingen onderdrukken. | --only-show-errors |
   |output -o| Hiermee stelt u de uitvoerindeling in.  Toegestane waarden: json, jsonc, none, table, tsv, yaml, yamlc. De standaardwaarde is json. | --output "json" |
   |query| De JMESPath-querytekenreeks. Zie [JMESPath](http://jmespath.org/) voor meer informatie. | --query <string>|
   |verbose| Uitgebreide logboekregistratie opnemen. | --verbose |

   Hier volgt een voorbeeld van de opdracht met de uitvoerindeling ingesteld op 'tabel':

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   Dit is de uitvoer van het uitvoeren van de opdracht:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        MyResGrp         2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  MyResGrp         2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       MyResGrp         2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       MyResGrp         2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       MyResGrp         2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="track-a-single-order"></a>Eén bestelling volgen

Als u informatie wilt ophalen over een enkele, bestaande Azure Data Box-bestelling, voert u [Get-AzDataBoxJob](/powershell/module/az.databox/Get-AzDataBoxJob) uit. De opdracht geeft informatie weer over de bestelling, zoals, maar niet beperkt tot: naam, resourcegroep, traceringsinformatie, abonnements-ID, contactgegevens, type verzending en apparaat-SKU.

> [!NOTE]
> `Get-AzDataBoxJob` wordt gebruikt voor het weergeven van zowel één als meerdere bestellingen. Het verschil is dat u de naam van de bestelling opgeeft als u één bestelling weergeeft.

   ```azurepowershell
    Get-AzDataBoxJob -ResourceGroupName <String> -Name <String>
   ```

   In de volgende tabel ziet u de parameterinformatie voor `Get-AzDataBoxJob`:

   | Parameter | Beschrijving |  Voorbeeldwaarde |
   |---|---|---|
   |ResourceGroup [vereist]| De naam van de resourcegroep die is gekoppeld aan de bestelling. Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd. | "myresourcegroup"|
   |Name [vereist]| De naam van bestelling waarvoor informatie moet worden opgehaald. | "mydataboxorder"|
   |ResourceId| De id van de resource die is gekoppeld aan de bestelling. |  |

   Hier volgt een voorbeeld van de opdracht met uitvoer:

   ```azurepowershell
    PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup" -Name "myDataBoxOrderPSTest"
   ```

   Dit is de uitvoer van het uitvoeren van de opdracht:

   ```output
   jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
   ----------------     -------------------- ------------------ --------------------- -------------------- -------------
   myDataBoxOrderPSTest DataBox              DeviceOrdered      7/7/2020 12:37:16 AM  WestUS               myResourceGroup
   ```

### <a name="list-all-orders"></a>Alle bestellingen weergeven

Als u meerdere apparaten hebt besteld, kunt u uitvoeren om [`Get-AzDataBoxJob`](/powershell/module/az.databox/Get-AzDataBoxJob) al uw Azure Data Box bekijken. Met de opdracht wordt een lijst weergegeven met alle bestellingen die deel uitmaken van een specifieke resourcegroep. Het volgende wordt ook weergegeven in de uitvoer: naam van bestelling, status van verzending, Azure-regio, leveringstype, status van bestelling. Geannuleerde orders worden ook opgenomen in de lijst.
De opdracht geeft ook tijdstempels van elke bestelling weer.

```azurepowershell
Get-AzDataBoxJob -ResourceGroupName <String>
```

Hier volgt een voorbeeld van de opdracht:

```azurepowershell
PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup"
```

Dit is de uitvoer van het uitvoeren van de opdracht:

```output
jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
----------------     -------------------- ------------------ --------------------- -------------------- -------------
guspImportTest       DataBox              Cancelled          5/26/2020 11:20:57 PM WestUS               myResourceGroup
mydataboxExportTest  DataBox              Cancelled          5/27/2020 12:04:16 AM WestUS               myResourceGroup
mydataboximport1     DataBox              Cancelled          6/26/2020 11:00:34 PM WestUS               myResourceGroup
myDataBoxOrderPSTest DataBox              Cancelled          7/07/2020 12:37:16 AM WestUS               myResourceGroup
mydataboxtest2       DataBox              Cancelled          6/10/2020 4:54:23  PM WestUS               myResourceGroup
mydataboxtest4       DataBox              DeviceOrdered      6/18/2020 3:48:00  AM WestUS               myResourceGroup
PS C:\WINDOWS\system32>
```

---

## <a name="cancel-the-order"></a>De bestelling annuleren

# <a name="portal"></a>[Portal](#tab/portal)

Als u deze bestelling wilt annuleren, gaat u in de Azure-portal naar **Overzicht** en selecteert u **Annuleren** in de opdrachtbalk.

Nadat u een bestelling hebt geplaatst, kunt u deze op elk moment annuleren voordat de status op Verwerkt is ingesteld.

Als u een geannuleerde bestelling wilt verwijderen, gaat u naar **Overzicht** en selecteert u **Verwijderen** in de opdrachtbalk.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

### <a name="cancel-an-order"></a>Een order annuleren

Voer uit om Azure Data Box order te [`az databox job cancel`](/cli/azure/databox/job#az_databox_job_cancel) annuleren. U moet een reden opgeven voor het annuleren van de bestelling.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   In de volgende tabel ziet u de parameterinformatie voor `az databox job cancel`:

   | Parameter | Beschrijving |  Voorbeeldwaarde |
   |---|---|---|
   |resource-group [vereist]| De naam van de resourcegroep die is gekoppeld aan de bestelling die moet worden verwijderd. Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd. | "myresourcegroup"|
   |name [vereist]| De naam van de bestelling die moet worden verwijderd. | "mydataboxorder"|
   |reason [vereist]| De reden voor het annuleren van de bestelling. | "Ik heb onjuiste informatie ingevoerd en wil de bestelling annuleren." |
   |ja| Niet vragen om bevestiging. | --yes (-y)| 
   |debug| Informatie over foutopsporing opnemen in uitgebreide logboekregistratie | --debug |
   |help| Help-informatie weergeven voor deze opdracht. | --help -h |
   |only-show-errors| Alleen fouten weergeven, waarschuwingen onderdrukken. | --only-show-errors |
   |output -o| Hiermee stelt u de uitvoerindeling in.  Toegestane waarden: json, jsonc, none, table, tsv, yaml, yamlc. De standaardwaarde is json. | --output "json" |
   |query| De JMESPath-querytekenreeks. Zie [JMESPath](http://jmespath.org/) voor meer informatie. | --query <string>|
   |verbose| Uitgebreide logboekregistratie opnemen. | --verbose |

   Hier volgt een voorbeeld van de opdracht met uitvoer:

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   Dit is de uitvoer van het uitvoeren van de opdracht:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>Een bestelling verwijderen

Als u een order hebt Azure Data Box, kunt u uitvoeren om [`az databox job delete`](/cli/azure/databox/job#az_databox_job_delete) de order te verwijderen.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   In de volgende tabel ziet u de parameterinformatie voor `az databox job delete`:

   | Parameter | Beschrijving |  Voorbeeldwaarde |
   |---|---|---|
   |resource-group [vereist]| De naam van de resourcegroep die is gekoppeld aan de bestelling die moet worden verwijderd. Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd. | "myresourcegroup"|
   |name [vereist]| De naam van de bestelling die moet worden verwijderd. | "mydataboxorder"|
   |abonnement| De naam of ID (GUID) van uw Azure-abonnement. | "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" |
   |ja| Niet vragen om bevestiging. | --yes (-y)|
   |debug| Informatie over foutopsporing opnemen in uitgebreide logboekregistratie | --debug |
   |help| Help-informatie weergeven voor deze opdracht. | --help -h |
   |only-show-errors| Alleen fouten weergeven, waarschuwingen onderdrukken. | --only-show-errors |
   |output -o| Hiermee stelt u de uitvoerindeling in.  Toegestane waarden: json, jsonc, none, table, tsv, yaml, yamlc. De standaardwaarde is json. | --output "json" |
   |query| De JMESPath-querytekenreeks. Zie [JMESPath](http://jmespath.org/) voor meer informatie. | --query <string>|
   |verbose| Uitgebreide logboekregistratie opnemen. | --verbose |

Hier volgt een voorbeeld van de opdracht met uitvoer:

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   Dit is de uitvoer van het uitvoeren van de opdracht:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="cancel-an-order"></a>Een order annuleren

Als u een Azure Data Box-bestelling wilt annuleren, voert u [Stop-AzDataBoxJob](/powershell/module/az.databox/stop-azdataboxjob) uit. U moet een reden opgeven voor het annuleren van de bestelling.

```azurepowershell
Stop-AzDataBoxJob -ResourceGroup <String> -Name <String> -Reason <String>
```

In de volgende tabel ziet u de parameterinformatie voor `Stop-AzDataBoxJob`:

| Parameter | Beschrijving |  Voorbeeldwaarde |
|---|---|---|
|ResourceGroup [vereist]| De naam van de resourcegroep die is gekoppeld aan de bestelling die moet worden geannuleerd. Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd. | "myresourcegroup"|
|Name [vereist]| De naam van de bestelling die moet worden verwijderd. | "mydataboxorder"|
|Reden [vereist]| De reden voor het annuleren van de bestelling. | "Ik heb onjuiste informatie ingevoerd en wil de bestelling annuleren." |
|Force | Hiermee wordt de cmdlet geforceerd uitgevoerd zonder bevestiging van de gebruiker. | -Force |

Hier volgt een voorbeeld van de opdracht met uitvoer:

```azurepowershell
PS C:\PowerShell\Modules> Stop-AzDataBoxJob -ResourceGroupName myResourceGroup \
                                            -Name "myDataBoxOrderPSTest" \
                                            -Reason "I entered erroneous information and had to cancel."
```

Dit is de uitvoer van het uitvoeren van de opdracht:

```output
Confirm
"Cancelling Databox Job "myDataBoxOrderPSTest
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\WINDOWS\system32>
```

### <a name="delete-an-order"></a>Een bestelling verwijderen

Als u een order hebt Azure Data Box, kunt u uitvoeren om [`Remove-AzDataBoxJob`](/powershell/module/az.databox/remove-azdataboxjob) de order te verwijderen.

```azurepowershell
Remove-AzDataBoxJob -Name <String> -ResourceGroup <String>
```

In de volgende tabel ziet u de parameterinformatie voor `Remove-AzDataBoxJob`:

| Parameter | Beschrijving |  Voorbeeldwaarde |
|---|---|---|
|ResourceGroup [vereist]| De naam van de resourcegroep die is gekoppeld aan de bestelling die moet worden verwijderd. Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd. | "myresourcegroup"|
|Name [vereist]| De naam van de bestelling die moet worden verwijderd. | "mydataboxorder"|
|Force | Hiermee wordt de cmdlet geforceerd uitgevoerd zonder bevestiging van de gebruiker. | -Force |

Hier volgt een voorbeeld van de opdracht met uitvoer:

```azurepowershell
PS C:\Windows> Remove-AzDataBoxJob -ResourceGroup "myresourcegroup" \
                                   -Name "mydataboxtest3"
```

Dit is de uitvoer van het uitvoeren van de opdracht:

```output
Confirm
"Removing Databox Job "mydataboxtest3
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\Windows>
```

---

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende artikelen met betrekking tot Azure Data Box, zoals:

> [!div class="checklist"]
>
> * Vereisten om Data Box te implementeren
> * Data Box bestellen
> * De bestelling volgen
> * De bestelling annuleren

Ga naar de volgende zelfstudie om te lezen hoe u uw Data Box instelt.

> [!div class="nextstepaction"]
> [Uw Azure Data Box instellen](./data-box-deploy-set-up.md)
