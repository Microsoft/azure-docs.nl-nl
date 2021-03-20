---
title: Azure Block Chain Workbench preview implementeren
description: Azure Block Chain Workbench preview implementeren
ms.date: 07/16/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: references_regions
ms.openlocfilehash: b46a35b45a51d0cc76942c4ca142c4c7792a28b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87077023"
---
# <a name="deploy-azure-blockchain-workbench-preview"></a>Azure Block Chain Workbench preview implementeren

De preview-versie van Azure Block Chain Workbench wordt geïmplementeerd met behulp van een oplossings sjabloon in azure Marketplace. De sjabloon vereenvoudigt de implementatie van onderdelen die nodig zijn voor het maken van Block Chain-toepassingen. Na de implementatie biedt Block Chain Workbench toegang tot client-apps voor het maken en beheren van gebruikers-en block Chain-toepassingen.

Zie [Azure Block Chain Workbench-architectuur](architecture.md)voor meer informatie over de onderdelen van Block Chain Workbench.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="prepare-for-deployment"></a>Implementatie voorbereiden

Met block Chain Workbench kunt u een Block Chain-boek implementeren, samen met een set relevante Azure-Services die het vaakst worden gebruikt voor het bouwen van een Block Chain-toepassing. De implementatie van Block Chain Workbench resulteert in de volgende Azure-Services die worden ingericht binnen een resource groep in uw Azure-abonnement.

* App Service plan (Standard)
* Application Insights
* Event Grid
* Azure Key Vault
* Service Bus
* SQL Database (standaard S0)
* Azure Storage-account (standaard LRS)
* Schaalset voor virtuele machines met een capaciteit van 1
* Virtual Network resource groep (met Load Balancer, netwerk beveiligings groep, openbaar IP-adres, Virtual Network)
* Azure Block Chain-service. Als u een eerdere implementatie van Block Chain Workbench gebruikt, overweeg dan om Azure Block Chain Workbench te implementeren voor het gebruik van de Azure Block Chain-service.

Hier volgt een voor beeld van een implementatie die is gemaakt in de resource groep **myblockchain** .

![Voorbeeld implementatie](media/deploy/example-deployment.png)

De kosten van Block Chain Workbench zijn een samen vatting van de kosten van de onderliggende Azure-Services. Prijs informatie voor Azure-Services kan worden berekend met behulp van de [prijs calculator](https://azure.microsoft.com/pricing/calculator/).

## <a name="prerequisites"></a>Vereisten

Azure Block Chain Workbench vereist Azure AD-configuratie-en-toepassings registraties. U kunt ervoor kiezen om de Azure AD- [configuraties hand matig](#azure-ad-configuration) te doen voordat u de implementatie uitvoert of u voert een script na de implementatie uit. Als u Block Chain Workbench opnieuw wilt implementeren, raadpleegt u [Azure AD-configuratie](#azure-ad-configuration) om uw Azure AD-configuratie te controleren.

> [!IMPORTANT]
> Workbench hoeft niet te worden geïmplementeerd in dezelfde Tenant als het account dat u gebruikt om een Azure AD-toepassing te registreren. Workbench moet worden geïmplementeerd in een Tenant waarvoor u voldoende machtigingen hebt voor het implementeren van resources. Zie [een Active Directory Tenant verkrijgen](../../active-directory/develop/quickstart-create-new-tenant.md) en [toepassingen integreren met Azure Active Directory](../../active-directory/develop/quickstart-register-app.md)voor meer informatie over Azure AD-tenants.

## <a name="deploy-blockchain-workbench"></a>Block Chain Workbench implementeren

Zodra de vereiste stappen zijn voltooid, bent u klaar voor de implementatie van de Block Chain-Workbench. In de volgende secties wordt uitgelegd hoe u het Framework implementeert.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer uw account in de rechter bovenhoek en schakel over naar de gewenste Azure AD-Tenant waar u Azure Block Chain Workbench wilt implementeren.
1. Selecteer in de linkerbovenhoek van Azure Portal **Een resource maken**.
1. Selecteer **Block Chain**  >  **Azure Block Chain workbench (preview-versie)**.

    ![Azure Block Chain Workbench maken](media/deploy/blockchain-workbench-settings-basic.png)

    | Instelling | Beschrijving  |
    |---------|--------------|
    | Resource voorvoegsel | Korte unieke id voor uw implementatie. Deze waarde wordt gebruikt als basis voor het benoemen van resources. |
    | VM-gebruikers naam | De gebruikers naam wordt gebruikt als beheerder voor alle virtuele machines (VM). |
    | Verificatietype | Selecteer deze optie als u een wacht woord of sleutel wilt gebruiken om verbinding te maken met Vm's. |
    | Wachtwoord | Het wacht woord wordt gebruikt om verbinding te maken met Vm's. |
    | SSH | Gebruik een open bare RSA-sleutel in de indeling met één regel die begint met **ssh-rsa** of gebruik de PEM-indeling met meerdere regels. U kunt SSH-sleutels genereren met behulp `ssh-keygen` van Linux en OS X, of met behulp van puttygen in Windows. Zie [SSH-sleutels gebruiken met Windows op Azure voor](../../virtual-machines/linux/ssh-from-windows.md)meer informatie over SSH-sleutels. |
    | Data Base-en block Chain-wacht woord | Geef het wacht woord op dat moet worden gebruikt voor toegang tot de data base die is gemaakt als onderdeel van de implementatie. Het wacht woord moet aan drie van de volgende vier vereisten voldoen: de lengte moet tussen 12 & 72 tekens, 1 kleine letter, 1 hoofd letter, 1 cijfer en 1 speciaal teken zijn dat geen hekje (#), procent (%), komma (,), ster (*), back-quote ( \` ), dubbele aanhalings tekens ("), één aanhalings teken (), streepje (-) en semicolumn (;) |
    | Implementatie regio | Geef op waar u Block Chain Workbench-resources wilt implementeren. Voor de beste Beschik baarheid moet dit overeenkomen met de instelling voor de **regio** locatie. Niet alle regio's zijn beschikbaar tijdens de preview-versie. Functies zijn mogelijk niet in alle regio's beschikbaar. Azure Blockchain Data Manager is beschikbaar in de volgende Azure-regio's: VS - oost en Europa - west.|
    | Abonnement | Geef het Azure-abonnement op dat u wilt gebruiken voor uw implementatie. |
    | Resourcegroepen | Maak een nieuwe resource groep door **Nieuw maken** te selecteren en geef een unieke naam op voor de resource groep. |
    | Locatie | Geef de regio op waarvoor u het Framework wilt implementeren. |

1. Selecteer **OK** om de configuratie sectie basis instelling te volt ooien.

1. Kies in **Geavanceerde instellingen** of u een nieuw Block chain-netwerk wilt maken of een bestaand Block chain-netwerk wilt gebruiken.

    Voor **nieuwe maken**:

    Met de optie *nieuwe maken* implementeert u een Azure Block Chain Service-quorum grootboek met de standaard-basis-SKU.

    ![Geavanceerde instellingen voor het nieuwe Block chain-netwerk](media/deploy/advanced-blockchain-settings-new.png)

    | Instelling | Beschrijving  |
    |---------|--------------|
    | Prijs categorie voor Azure Block Chain-Service | Kies **Basic** of **Standard** Azure Block Chain service tier die wordt gebruikt voor Block Chain workbench |
    | Azure Active Directory-instellingen | Kies **later toevoegen**.</br>Opmerking: als u ervoor kiest om [Azure AD vooraf te configureren of opnieuw](#azure-ad-configuration) te implementeren, kiest u *nu toevoegen*. |
    | VM selecteren | Selecteer voorkeurs opslag prestaties en VM-grootte voor uw Block chain-netwerk. Kies een kleinere VM-grootte, zoals *Standard ds1 v2* als u een abonnement hebt met lage service limieten, zoals de gratis laag van Azure. |

    Voor het **gebruik van bestaande**:

    Met de optie *bestaande gebruiken* kunt u een Block Chain PoA-netwerk (Ethereum proof-of-Authority) opgeven. Eind punten hebben de volgende vereisten.

   * Het eind punt moet een Block Chain PoA-netwerk (Ethereum proof-of-Authority) zijn.
   * Het eind punt moet openbaar toegankelijk zijn via het netwerk.
   * Het PoA Block chain-netwerk moet zo zijn geconfigureerd dat de prijs van een gas is ingesteld op nul.

     > [!NOTE]
     > Block Chain Workbench-accounts zijn niet gefinancierd. Als er fondsen zijn vereist, mislukken de trans acties.

     ![Geavanceerde instellingen voor bestaand Block chain-netwerk](media/deploy/advanced-blockchain-settings-existing.png)

     | Instelling | Beschrijving  |
     |---------|--------------|
     | RPC-eind punt Ethereum | Geef het RPC-eind punt op van een bestaand PoA Block chain-netwerk. Het eind punt begint met https://of http://en eindigt met een poort nummer. Bijvoorbeeld: `http<s>://<network-url>:<port>` |
     | Azure Active Directory-instellingen | Kies **later toevoegen**.</br>Opmerking: als u ervoor kiest om [Azure AD vooraf te configureren of opnieuw](#azure-ad-configuration) te implementeren, kiest u *nu toevoegen*. |
     | VM selecteren | Selecteer voorkeurs opslag prestaties en VM-grootte voor uw Block chain-netwerk. Kies een kleinere VM-grootte, zoals *Standard ds1 v2* als u een abonnement hebt met lage service limieten, zoals de gratis laag van Azure. |

1. Selecteer **beoordeling + maken** om geavanceerde instellingen te volt ooien.

1. Bekijk de samen vatting om te controleren of de para meters juist zijn.

    ![Samenvatting](media/deploy/blockchain-workbench-summary.png)

1. Selecteer **maken** om akkoord te gaan met de voor waarden en implementeer uw Azure Block Chain Workbench.

De implementatie kan Maxi maal 90 minuten duren. U kunt de Azure Portal gebruiken om de voortgang te bewaken. Selecteer in de zojuist gemaakte resource groep **implementaties > overzicht** om de status van de geïmplementeerde artefacten te bekijken.

> [!IMPORTANT]
> Na de implementatie moet u Active Directory instellingen volt ooien. Als u **later toevoegen** hebt gekozen, moet u het [Azure AD-configuratie script](#azure-ad-configuration-script)uitvoeren.  Als u **nu toevoegen** hebt gekozen, moet u [de antwoord-URL configureren](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>Web-URL voor Block Chain workbench

Zodra de implementatie van de Block Chain-Workbench is voltooid, bevat een nieuwe resource groep uw resources van Block Chain Workbench. Block Chain Workbench-Services zijn toegankelijk via een web-URL. De volgende stappen laten zien hoe u de web-URL van het geïmplementeerde Framework kunt ophalen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **resource groepen** in het navigatie deel venster aan de linkerkant.
1. Kies de naam van de resource groep die u hebt opgegeven bij het implementeren van Block Chain Workbench.
1. Selecteer de kolomkop **type** om de lijst alfabetisch te sorteren op type.
1. Er zijn twee resources met het type **app service**. Selecteer de resource van het type **app service** *zonder* het achtervoegsel '-API '.

    ![Lijst met apps](media/deploy/resource-group-list.png)

1. Kopieer in het App Service **overzicht** de **URL** -waarde, die de web-URL voor uw geïmplementeerde Block Chain Workbench vertegenwoordigt.

    ![Essentiële informatie over app service](media/deploy/app-service.png)

Als u een aangepaste domein naam wilt koppelen aan block Chain Workbench, raadpleegt u [een aangepaste domein naam configureren voor een web-app in azure app service met behulp van Traffic Manager](../../app-service/configure-domain-traffic-manager.md).

## <a name="azure-ad-configuration-script"></a>Azure AD-configuratie script

Azure AD moet worden geconfigureerd om uw Block Chain Workbench-implementatie te volt ooien. U gebruikt een Power shell-script om de configuratie uit te voeren.

1. Navigeer in een browser naar de [Web-URL van Block Chain Workbench](#blockchain-workbench-web-url).
1. U ziet instructies voor het instellen van Azure AD met behulp van Cloud Shell. Kopieer de opdracht en start Cloud Shell.

    ![AAD-script starten](media/deploy/launch-aad-script.png)

1. Kies de Azure AD-Tenant waar u Block Chain Workbench hebt geïmplementeerd.
1. Plak en voer de opdracht uit in Cloud Shell Power shell-omgeving.
1. Wanneer u hierom wordt gevraagd, voert u de Azure AD-Tenant in die u wilt gebruiken voor Block Chain Workbench. Dit is de Tenant die de gebruikers voor Block Chain Workbench bevat.

    > [!IMPORTANT]
    > De geverifieerde gebruiker heeft machtigingen nodig voor het maken van Azure AD-toepassings registraties en het verlenen van gedelegeerde toepassings machtigingen in de Tenant. Mogelijk moet u een beheerder van de Tenant vragen om het Azure AD-configuratie script uit te voeren of een nieuwe Tenant te maken.

    ![Azure AD-Tenant invoeren](media/deploy/choose-tenant.png)

1. U wordt gevraagd om u te verifiëren bij de Azure AD-Tenant met behulp van een browser. Open de web-URL in een browser, voer de code in en authenticatie.

    ![Verifiëren met code](media/deploy/authenticate.png)

1. Het script voert diverse status berichten uit. U krijgt een **geslaagd** status bericht als de Tenant is ingericht.
1. Ga naar de URL van de Block Chain-Workbench. U wordt gevraagd om toestemming te geven om Lees machtigingen te verlenen aan de Directory. Hiermee wordt de Block Chain Workbench-web-app toegang tot de gebruikers in de Tenant. Als u de Tenant beheerder bent, kunt u ervoor kiezen om toestemming te geven voor de hele organisatie. Met deze optie wordt de toestemming geaccepteerd voor alle gebruikers in de Tenant. Anders wordt elke gebruiker gevraagd toestemming te geven bij het eerste gebruik van de Block Chain Workbench-webtoepassing.
1. Selecteer **accepteren** om toestemming te geven.

     ![Toestemming voor het lezen van gebruikers profielen](media/deploy/graph-permission-consent.png)

1. Na toestemming kunt u de Block Chain Workbench-web-app gebruiken.

U hebt uw Azure Block Chain Workbench-implementatie voltooid. Zie de [volgende stappen](#next-steps) voor suggesties om aan de slag te gaan met uw implementatie.

## <a name="azure-ad-configuration"></a>Azure AD-configuratie

Als u ervoor kiest om de Azure AD-instellingen voor de implementatie hand matig te configureren of te verifiëren, voltooit u alle stappen in deze sectie. Als u de Azure AD-instellingen liever automatisch wilt configureren, gebruikt u het [configuratie script van Azure AD](#azure-ad-configuration-script) nadat u Block Chain Workbench hebt geïmplementeerd.

### <a name="blockchain-workbench-api-app-registration"></a>Registratie van Block Chain Workbench API-app

Voor de implementatie van Block Chain Workbench is registratie van een Azure AD-toepassing vereist. U hebt een Azure Active Directory-Tenant (Azure AD) nodig om de app te registreren. U kunt een bestaande Tenant gebruiken of een nieuwe Tenant maken. Als u een bestaande Azure AD-Tenant gebruikt, hebt u voldoende machtigingen nodig voor het registreren van toepassingen, het verlenen van Graph API machtigingen en het toestaan van gast toegang binnen een Azure AD-Tenant. Als u onvoldoende machtigingen hebt in een bestaande Azure AD-Tenant, maakt u een nieuwe Tenant.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer uw account in de rechter bovenhoek en schakel over naar de gewenste Azure AD-Tenant. De Tenant moet de Tenant van de abonnements beheerder zijn van het abonnement waar Azure Block Chain Workbench wordt geïmplementeerd en u voldoende machtigingen hebt om toepassingen te registreren.
1. Selecteer in het navigatiedeelvenster aan de linkerkant de **Azure Active Directory**-service. Selecteer **app-registraties**  >  **nieuwe registratie**.

    ![App-registratie](media/deploy/app-registration.png)

1. Geef een weergave **naam** op en kies **alleen accounts in deze organisatie Directory**.

    ![Een app-registratie maken](media/deploy/app-registration-create.png)

1. Selecteer **registreren** om de Azure AD-toepassing te registreren.

### <a name="modify-manifest"></a>Manifest wijzigen

Vervolgens moet u het manifest wijzigen om toepassings rollen binnen Azure AD te gebruiken om Block Chain Workbench-beheerders op te geven.  Zie [Azure Active Directory Application manifest](../../active-directory/develop/reference-app-manifest.md)voor meer informatie over toepassings manifesten.

1. Er is een GUID vereist voor het manifest. U kunt een GUID genereren met behulp van de Power shell-opdracht `[guid]::NewGuid()` of `New-GUID` cmdlet. Een andere mogelijkheid is om een GUID-Generator website te gebruiken.
1. Voor de toepassing die u hebt geregistreerd, selecteert u **manifest** in het gedeelte **beheren** .
1. Werk vervolgens de sectie **appRoles** van het manifest bij. Vervang door `"appRoles": []` de gegeven JSON. Zorg ervoor dat u de waarde voor het `id` veld vervangt door de GUID die u hebt gegenereerd.
    ![Manifest bewerken](media/deploy/edit-manifest.png)

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    > [!IMPORTANT]
    > De waarde **beheerder** is nodig om Block Chain Workbench-beheerders te identificeren.

1. In het manifest wijzigt u ook de waarde **Oauth2AllowImplicitFlow** in **True**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

1. Selecteer **Opslaan** om de wijzigingen in het manifest op te slaan.

### <a name="add-graph-api-required-permissions"></a>Graph API vereiste machtigingen toevoegen

De API-toepassing moet toestemming van de gebruiker aanvragen voor toegang tot de Directory. Stel de volgende vereiste machtiging in voor de API-toepassing:

1. Selecteer in de *Block Chain API* -app registratie de optie **API-machtigingen**. Standaard is de machtiging Graph API **gebruiker. Read** toegevoegd.
1. Voor de workbench-toepassing is lees toegang tot de basis profiel gegevens van gebruikers vereist. Selecteer **een machtiging toevoegen** in *geconfigureerde machtigingen*. Selecteer **Microsoft Graph** in **micro soft-api's**.
1. Aangezien de workbench-toepassing gebruikmaakt van de referenties van de geverifieerde gebruiker, selecteert u **gedelegeerde machtigingen**.
1. Klik in de categorie *gebruiker* op **User. ReadBasic. all** permission.

    ![Configuratie van de Azure AD-App-registratie met het toevoegen van de Microsoft Graph User. ReadBasic. alle gedelegeerde machtigingen](media/deploy/add-graph-user-permission.png)

    Selecteer **Machtigingen toevoegen**.

1. Selecteer in *geconfigureerde machtigingen* de optie **beheerder toestemming geven** voor het domein en selecteer vervolgens **Ja** voor de verificatie prompt.

   ![Machtigingen verlenen](media/deploy/client-app-grant-permissions.png)

   Met machtiging verlenen kan Block Chain Workbench toegang krijgen tot gebruikers in de Directory. De Lees machtiging is vereist om leden te zoeken en toe te voegen aan block Chain Workbench.

### <a name="get-application-id"></a>Toepassings-id ophalen

De toepassings-ID en Tenant gegevens zijn vereist voor de implementatie. Verzamel en bewaar de informatie voor gebruik tijdens de implementatie.

1. Selecteer **overzicht** voor de toepassing die u hebt geregistreerd.
1. Kopieer de waarde van de **toepassings-id** en sla deze op voor later gebruik tijdens de implementatie.

    ![Eigenschappen van API-app](media/deploy/app-properties.png)

    | Instelling voor opslaan  | Gebruiken in implementatie |
    |------------------|-------------------|
    | (Client-)id van de app | Azure Active Directory instellen >-toepassings-ID |

### <a name="get-tenant-domain-name"></a>Domein naam van Tenant ophalen

Verzamel en sla de Active Directory Tenant domein naam op waar de toepassingen zijn geregistreerd.

Selecteer in het navigatiedeelvenster aan de linkerkant de **Azure Active Directory**-service. Selecteer **Namen van aangepaste domeinen**. Kopieer de domein naam en sla deze op.

![Domeinnaam](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Gast gebruikers instellingen

Als u gast gebruikers in uw Azure AD-Tenant hebt, volgt u de extra stappen om ervoor te zorgen dat de gebruikers toewijzing en het beheer van Block Chain Workbench goed werken.

1. Schakel over naar uw Azure AD-Tenant en selecteer **Azure Active Directory > gebruikers instellingen > externe instellingen voor samen werking beheren**.
1. Stel **gast gebruikers machtigingen** in op **Nee**.
    ![Instellingen voor externe samen werking](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>De antwoord-URL configureren

Zodra Azure Block Chain Workbench is geïmplementeerd, moet u de **antwoord-URL** voor de Azure Active Directory (Azure AD)-client toepassing configureren van de geïmplementeerde Block Chain Workbench web-URL.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Controleer of u zich in de Tenant bevindt waarin u de Azure AD-client toepassing hebt geregistreerd.
1. Selecteer in het navigatiedeelvenster aan de linkerkant de **Azure Active Directory**-service. Selecteer **App-registraties**.
1. Selecteer de Azure AD-client toepassing die u hebt geregistreerd in de sectie vereisten.
1. Selecteer **Verificatie**.
1. Geef de web-URL op van de Azure Block Chain Workbench-implementatie die u hebt opgehaald in de sectie [Block Chain Workbench web URL](#blockchain-workbench-web-url) . De antwoord-URL wordt voorafgegaan door `https://` . Bijvoorbeeld: `https://myblockchain2-7v75.azurewebsites.net`

    ![Antwoord-Url's voor verificatie](media/deploy/configure-reply-url.png)

1. Controleer in de sectie **geavanceerde instelling** de **toegangs tokens** en **id-tokens**.

    ![Geavanceerde instellingen voor verificatie](media/deploy/authentication-advanced-settings.png)

1. Selecteer **Opslaan** om de client registratie bij te werken.

## <a name="remove-a-deployment"></a>Een implementatie verwijderen

Wanneer een implementatie niet meer nodig is, kunt u een implementatie verwijderen door de resource groep Block Chain Workbench te verwijderen.

1. Ga in de Azure Portal naar **Resourcegroep** in het linkernavigatievenster en selecteer de resourcegroep die u wilt verwijderen.
1. Selecteer **Resourcegroep verwijderen**. Controleer de verwijdering door de naam van de resourcegroep in te voeren en **Verwijderen** te selecteren.

    ![Resourcegroep verwijderen](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u Azure Block Chain Workbench hebt geïmplementeerd. Ga door naar het volgende artikel voor meer informatie over het maken van een Block Chain-toepassing.

> [!div class="nextstepaction"]
> [Een blockchain-toepassing maken in Azure Blockchain Workbench](create-app.md)
