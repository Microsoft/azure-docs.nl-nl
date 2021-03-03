---
title: Verbinding maken met SAP-systemen
description: Toegang tot en beheer van SAP-bronnen door werk stromen te automatiseren met Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 02/01/2021
tags: connectors
ms.openlocfilehash: e52c4acb4b59414e89e87bf5a6ee2cfae8207cae
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712450"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Verbinding maken met SAP-systemen in Azure Logic Apps

In dit artikel wordt uitgelegd hoe u vanaf Logic Apps toegang kunt krijgen tot uw SAP-resources via de [SAP-connector](/connectors/sap/).

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, [meldt u zich aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een logische app die u wilt gebruiken om toegang te krijgen tot uw SAP-resources. Als u geen ervaring hebt met Logic Apps, raadpleegt u het [overzicht van Logic apps-service](../logic-apps/logic-apps-overview.md) en de [Snelstartgids voor het maken van uw eerste logische app in de Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

    * Als u een eerdere versie van de SAP-connector hebt gebruikt die is afgeschaft, moet u [migreren naar de huidige connector](#migrate-to-current-connector) voordat u verbinding kunt maken met uw SAP-server.

    * Als u uw logische app uitvoert in een multi tenant Azure, raadpleegt u de [vereisten voor meerdere tenants](#multi-tenant-azure-prerequisites).

    * Als u uw logische app uitvoert in een ISE (Premium-Level[ Integration service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), raadpleegt u de [ISE-vereisten](#ise-prerequisites).

* Een [SAP-toepassings server](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) of [SAP-bericht server](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm) waartoe u toegang wilt krijgen via Logic apps. Zie [SAP-compatibiliteit](#sap-compatibility)voor meer informatie over welke SAP-servers en SAP-acties u met de connector kunt gebruiken.

* Bericht inhoud die naar uw SAP-server moet worden verzonden, zoals een voor beeld van een IDoc-bestand. Deze inhoud moet de XML-indeling hebben en de naam ruimte bevatten van de SAP-actie die u wilt gebruiken. U kunt [IDocs verzenden met een plat bestands schema door ze in een XML-envelop te laten teruglopen](#send-flat-file-idocs).

* Als u de trigger **Wanneer een bericht wordt ontvangen van SAP** wilt gebruiken, moet u ook het volgende doen:

    * Stel de beveiligings machtigingen voor uw SAP-gateway in met deze instelling: `"TP=Microsoft.PowerBI.EnterpriseGateway HOST=<gateway-server-IP-address> ACCESS=*"`

    * Stel de beveiligings logboeken van de SAP-gateway in om te helpen bij het vinden van Access Control lijst (ACL). Zie het [SAP Help-onderwerp voor het instellen van Gateway logboek registratie](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm)voor meer informatie. Anders wordt deze fout weer gegeven: `"Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed"`

    > [!NOTE]
    > Deze trigger maakt gebruik van dezelfde URI-locatie voor het vernieuwen en afmelden van een webhook-abonnement. Voor de vernieuwings bewerking wordt de HTTP- `PATCH` methode gebruikt, terwijl de afmeldings bewerking de HTTP- `DELETE` methode gebruikt. Dit gedrag kan ertoe leiden dat een vernieuwings bewerking wordt weer gegeven als een afmeldings bewerking in de geschiedenis van uw trigger, maar de bewerking is nog steeds een verlenging omdat de trigger wordt gebruikt `PATCH` als de HTTP-methode, niet `DELETE` .

### <a name="sap-compatibility"></a>SAP-compatibiliteit

De SAP-connector is compatibel met de volgende typen SAP-systemen:

* Op de lokale en cloud gebaseerde op HANA gebaseerde SAP-systemen, zoals S/4 HANA.

* Klassieke on-premises SAP-systemen, zoals R/3 en ECC.

De SAP-connector ondersteunt de volgende typen voor bericht-en gegevens integratie van op SAP NetWeaver gebaseerde systemen:

* Tussenliggend document (IDoc)

* BAPI (Business Application Programming Interface)

* Externe functie aanroepen (RFC) en transactionele RFC (tRFC)

De SAP-connector maakt gebruik van de [SAP .net connector (NCo)-bibliotheek](https://support.sap.com/en/product/connectors/msnet.html). U kunt de volgende SAP-acties en triggers met de connector gebruiken:

* **Verzend een bericht naar SAP** om [IDocs te verzenden via tRFC](#send-idoc-action) actie, die u kunt gebruiken om het volgende te doen:

    * [BAPI-functies aanroepen via RFC](#call-bapi-action)

    * RFC/tRFC aanroepen in SAP-systemen

    * Stateful sessies maken of sluiten

    * BAPI-trans acties door voeren of terugdraaien

    * Een trans actie-id bevestigen

    * Verzend IDocs, haal de status van een IDoc op uit het nummer en ontvang een lijst met IDocs voor een trans actie

    * Een SAP-tabel lezen

* **Wanneer er een bericht wordt ontvangen van SAP** -trigger, dat u kunt gebruiken voor het volgende:

    * IDocs ontvangen via tRFC

    * BAPI-functies aanroepen via tRFC

    * RFC/tRFC aanroepen in SAP-systemen

* De actie **Schema's genereren** , die u kunt gebruiken om schema's voor de SAP-artefacten voor IDOC, BAPI of RFC te genereren.

Als u deze SAP-acties wilt gebruiken, moet u uw verbinding eerst verifiëren met een gebruikers naam en wacht woord. De SAP-connector biedt ook ondersteuning voor [beveiligde netwerk communicatie (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). U kunt SNC gebruiken voor SAP NetWeaver eenmalige aanmelding (SSO) of voor aanvullende beveiligings mogelijkheden van externe producten. Als u SNC gebruikt, raadpleegt u de [SNC-vereisten](#snc-prerequisites).

### <a name="migrate-to-current-connector"></a>Migreren naar de huidige connector

De vorige SAP-toepassings server en SAP Message server-connectors zijn afgeschaft op 29 februari 2020. Voer de volgende stappen uit om te migreren naar de huidige SAP-connector:

1. Werk uw [on-premises gegevens gateway](https://www.microsoft.com/download/details.aspx?id=53127) bij naar de huidige versie. Zie [een on-premises gegevens gateway installeren voor Azure Logic apps](../logic-apps/logic-apps-gateway-install.md)voor meer informatie.

1. Verwijder de actie **Send to SAP** in uw logische app die gebruikmaakt van de afgeschafte sap-connector.

1. Voeg de actie **bericht verzenden naar SAP** toe vanuit de huidige SAP-connector.

1. Maak opnieuw verbinding met uw SAP-systeem met de nieuwe actie.

1. Sla uw logische app op.

### <a name="multi-tenant-azure-prerequisites"></a>Azure-vereisten voor meerdere tenants

Deze vereisten zijn van toepassing als uw logische app wordt uitgevoerd in een multi tenant-Azure. De beheerde SAP-connector wordt niet systeem eigen uitgevoerd in een [ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

> [!TIP]
> Als u een ISE op Premium niveau gebruikt, kunt u de SAP ISE-connector gebruiken in plaats van de beheerde SAP-connector. Zie de [ISE-vereisten](#ise-prerequisites)voor meer informatie.

De beheerde SAP-connector integreert met SAP-systemen via uw [on-premises gegevens gateway](../logic-apps/logic-apps-gateway-connection.md). Als bijvoorbeeld in scenario's voor het verzenden van berichten een bericht vanuit een logische app naar een SAP-systeem wordt verzonden, fungeert de gegevens gateway als een RFC-client en worden de aanvragen die van de logische app worden ontvangen, doorgestuurd naar SAP. Evenzo fungeert de gegevens gateway in scenario's voor het ontvangen van berichten als een RFC-server die aanvragen van SAP ontvangt en deze doorstuurt naar de logische app.

* [Down load en installeer de on-premises gegevens gateway](../logic-apps/logic-apps-gateway-install.md) op een hostcomputer of virtuele machine die zich in hetzelfde virtuele netwerk bevindt als het SAP-systeem waarmee u verbinding maakt.

* [Maak een Azure gateway-resource](../logic-apps/logic-apps-gateway-connection.md#create-azure-gateway-resource) voor uw on-premises gegevens gateway in de Azure Portal. Met deze gateway kunt u veilig toegang krijgen tot on-premises gegevens en bronnen. Zorg ervoor dat u een ondersteunde versie van de gateway gebruikt.

    * Als u een probleem met uw gateway ondervindt, voert u [een upgrade uit naar de nieuwste versie](https://aka.ms/on-premises-data-gateway-installer), die mogelijk updates bevat om het probleem op te lossen.

* [Down load en installeer de meest recente SAP-client bibliotheek](#sap-client-library-prerequisites) op dezelfde lokale computer als uw on-premises gegevens gateway.

### <a name="ise-prerequisites"></a>Vereisten voor ISE

Deze vereisten zijn van toepassing als u uw logische app uitvoert in een ISE op Premium-niveau. Ze zijn echter niet van toepassing op Logic apps die worden uitgevoerd in een ISE op ontwikkelaars niveau. Een ISE biedt toegang tot bronnen die worden beveiligd met een virtueel Azure-netwerk en biedt andere ISE-systeem eigen connectors waarmee Logic apps rechtstreeks toegang hebben tot on-premises resources zonder gebruik te maken van een on-premises gegevens gateway.

> [!NOTE]
> Terwijl de SAP ISE-connector zichtbaar is binnen een ISE op ontwikkelaars niveau, mislukt de installatie van de connector.

1. Als u nog geen Azure Storage account met een BLOB-container hebt, maakt u een container met behulp van de [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) of [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

1. [Down load en installeer de meest recente SAP-client bibliotheek](#sap-client-library-prerequisites) op uw lokale computer. U moet de volgende assembly bestanden hebben:

   * libicudecnumber.dll

   * rscp4n.dll

   * sapnco.dll

   * sapnco_utils.dll

1. Maak een zip-bestand met deze assembly bestanden. Upload het pakket naar de BLOB-container in Azure Storage.

1. Blader in het Azure Portal of Azure Storage Explorer naar de container locatie waar u het zip-bestand hebt geüpload.

1. Kopieer de URL van de container locatie. Zorg ervoor dat u het SAS-token (Shared Access Signature) opneemt, zodat het SAS-token wordt geautoriseerd. Anders mislukt de implementatie van de SAP ISE-connector.

1. Installeer en implementeer de SAP-connector in uw ISE. Zie [ISE-connectors toevoegen](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment)voor meer informatie.

   1. Zoek en open uw ISE in de [Azure Portal](https://portal.azure.com).

   1. Selecteer in het menu ISE **beheerde connectors** &gt; **toevoegen**. Zoek en selecteer **SAP** in de lijst connectors.

   1. Plak in het deel venster **een nieuwe beheerde connector toevoegen** , in het vak **SAP package** , de URL voor het zip-bestand dat de SAP-assembly's bevat. Zorg ervoor dat u het SAS-token opneemt.
 
  1. Selecteer **maken** om de ISE-connector te maken.

1. Als uw SAP-exemplaar en ISE zich in verschillende virtuele netwerken bevinden, moet u [deze netwerken ook peeren](../virtual-network/tutorial-connect-virtual-networks-portal.md) zodat ze zijn verbonden.

### <a name="sap-client-library-prerequisites"></a>Vereisten voor SAP-client bibliotheek

Dit zijn de vereisten voor de SAP-client bibliotheek die u met de-connector gebruikt.

* Zorg ervoor dat u de meest recente versie [van SAP connector (NCo 3,0) installeert voor Microsoft .net 3.0.22.0 gecompileerd met .NET Framework 4,0-Windows 64-bits (x64)](https://support.sap.com/en/product/connectors/msnet.html). Eerdere versies van SAP NCo kunnen problemen ondervinden wanneer er meer dan één IDoc-bericht tegelijk wordt verzonden. Deze voor waarde blokkeert alle latere berichten die worden verzonden naar de SAP-bestemming, waardoor er een time-out optreedt voor de berichten.

* U moet de 64-bits versie van de SAP-client bibliotheek hebben geïnstalleerd, omdat de gegevens gateway alleen wordt uitgevoerd op 64-bits systemen. Het installeren van de niet-ondersteunde 32-bits versie resulteert in een fout bericht over een onjuiste installatie kopie.

* Kopieer de assembly bestanden van de standaardmap voor installatie naar een andere locatie op basis van uw scenario als volgt.

    * Voor logische apps die in een ISE worden uitgevoerd, volgt u de [ISE-vereisten](#ise-prerequisites) in plaats daarvan.

    * Voor logische apps die worden uitgevoerd in multi tenant Azure en uw on-premises gegevens gateway gebruiken, kopieert u de assembly bestanden naar de installatiemap van de gegevens gateway. 

        
        * Als uw SAP-verbinding mislukt met het fout bericht, controleert u de **account gegevens en/of de machtigingen en probeert** u het opnieuw. Controleer of u de assembly bestanden hebt gekopieerd naar de installatiemap van de gegevens gateway.
        
        * Meer problemen oplossen met behulp van de [.NET-assembly-bindings logboek Viewer](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer). Met dit hulp programma kunt u controleren of de assembly bestanden zich op de juiste locatie bevinden. 
        
        * U kunt eventueel de optie voor de **registratie van globale assembly-cache** selecteren wanneer u de SAP-client bibliotheek installeert.

Houd rekening met de volgende relaties tussen de SAP-client bibliotheek, de .NET Framework, de .NET-runtime en de gateway:

* Zowel de micro soft SAP-adapter als de gateway-hostservice gebruiken .NET Framework 4.7.2.

* De SAP-NCo voor .NET Framework 4,0 werkt met processen die .NET runtime 4,0 tot 4,8 gebruiken.

* De SAP-NCo voor .NET Framework 2,0 werkt met processen die gebruikmaken van .NET runtime 2,0 tot 3,5, maar werkt niet meer met de nieuwste gateway.

### <a name="snc-prerequisites"></a>Vereisten voor SNC

Als u een on-premises gegevens gateway gebruikt met optionele SNC, die alleen wordt ondersteund in multi tenant Azure, moet u deze aanvullende instellingen configureren.

Als u SNC gebruikt met SSO, moet u ervoor zorgen dat de data Gateway-Service wordt uitgevoerd als een gebruiker die is toegewezen aan de SAP-gebruiker. Als u het standaard account wilt wijzigen, selecteert u **account wijzigen** en voert u de gebruikers referenties in.

![Scherm opname van de instellingen van de on-premises gegevens gateway in de Azure-Portal, met de knop pagina Service-instellingen, waarmee u het geselecteerde Gateway Service-account kunt wijzigen.](./media/logic-apps-using-sap-connector/gateway-account.png)

Als u SNC inschakelt via een extern beveiligings product, kopieert u de SNC-bibliotheek of-bestanden op dezelfde computer waarop uw gegevens gateway is geïnstalleerd. Enkele voor beelden van SNC-producten zijn [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos en NTLM. Zie [veilige netwerk communicatie inschakelen](#enable-secure-network-communications)voor meer informatie over het inschakelen van SNC voor de gegevens gateway.

## <a name="send-idoc-messages-to-sap-server"></a>IDoc-berichten verzenden naar SAP-server

Volg deze voor beelden om een logische app te maken die een IDoc-bericht naar een SAP-server verzendt en een antwoord retourneert:

1. [Maak een logische app die wordt geactiveerd door een HTTP-aanvraag.](#create-http-request-trigger)

1. [Een actie in uw werk stroom maken om een bericht naar SAP te verzenden.](#create-sap-action-to-send-message)

1. [Maak een HTTP-antwoord actie in uw werk stroom.](#create-http-response-action)

1. [Maak een aanvraag-antwoord patroon voor externe functie aanroepen (RFC), als u een RFC gebruikt om antwoorden van SAP ABAP te ontvangen.](#create-rfc-request-response)

1. [Test uw logische app.](#test-logic-app)

### <a name="create-http-request-trigger"></a>HTTP-aanvraag trigger maken

> [!NOTE]
> Wanneer een logische app IDocs ontvangt van SAP, ondersteunt de [aanvraag trigger](../connectors/connectors-native-reqres.md) nu de SAP Plain XML-indeling. Als u IDocs als gewone XML wilt ontvangen, gebruikt u de trigger **wanneer er een bericht van SAP wordt ontvangen**. Stel de indeling van de para meter **IDOC** in op **SapPlainXml**.

Maak eerst een logische app met een eind punt in azure om *http post-* aanvragen naar uw logische app te verzenden. Wanneer uw logische app deze HTTP-aanvragen ontvangt, wordt de [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) geactiveerd en wordt de volgende stap in uw werk stroom uitgevoerd.

1. Maak in de [Azure Portal](https://portal.azure.com)een lege logische app, waarmee de **Logic apps Designer** wordt geopend.

1. Voer in het zoekvak `http request` als uw filter in. Selecteer in de lijst **Triggers** **Wanneer een HTTP-aanvraag wordt ontvangen**.

   ![Scherm opname van Logic Apps Designer, met een nieuwe HTTP-aanvraag trigger die wordt toegevoegd aan de logische app.](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Sla uw logische app op zodat u een eind punt-URL kunt genereren voor uw logische app. Selecteer **Opslaan** op de werkbalk van de ontwerper. De eind punt-URL wordt nu weer gegeven in de trigger. 

   ![Scherm opname van Logic Apps Designer, met de HTTP-aanvraag trigger met de gegenereerde POST-URL die wordt gekopieerd.](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="create-sap-action-to-send-message"></a>SAP-actie maken om bericht te verzenden

Maak vervolgens een actie om uw IDoc-bericht naar SAP te verzenden wanneer de activering van de [HTTP-aanvraag](#create-http-request-trigger) wordt geactiveerd.

1. Selecteer in de Logic Apps Designer, onder de trigger, de optie **nieuwe stap**.

   ![Scherm opname van Logic Apps Designer, met daarin de logische app die wordt bewerkt om een nieuwe stap toe te voegen.](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Voer in het zoekvak `sap` als uw filter in. Selecteer in de lijst **acties** de optie **bericht naar SAP verzenden**.
  
   ![Scherm opname van Logic Apps Designer, waarin de selectie van de actie bericht verzenden naar SAP wordt weer gegeven.](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   U kunt ook het tabblad **onderneming** selecteren en de SAP-actie selecteren.

   ![Scherm opname van Logic Apps Designer, waarbij de selectie van de actie bericht verzenden naar SAP wordt weer gegeven onder het tabblad onderneming.](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Als uw verbinding al bestaat, gaat u verder met de volgende stap. Als u wordt gevraagd om een nieuwe verbinding te maken, geeft u de volgende informatie op voor verbinding met uw on-premises SAP-server.

   1. Geef een naam op voor de verbinding.

   1. Als u de gegevens gateway gebruikt, voert u de volgende stappen uit:
   
      1. Selecteer in de sectie **gegevens gateway** , onder **abonnement**, eerst het Azure-abonnement voor de gegevens gateway resource die u hebt gemaakt in de Azure portal voor uw data Gateway-installatie.
   
      1. Onder **verbindings gateway** selecteert u uw gegevens gateway resource in Azure.

   1. Blijf verbindings gegevens opgeven. Volg voor de eigenschap **aanmeldings type** de stap op basis van het feit of de eigenschap is ingesteld op **toepassings server** of **groep**:
   
      * Voor **toepassings server** zijn deze eigenschappen, die meestal optioneel worden weer gegeven, vereist:

        ![Een SAP-toepassings server verbinding maken](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Voor **groep** zijn deze eigenschappen, die meestal optioneel worden weer gegeven, vereist:

        ![SAP Message server-verbinding maken](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Standaard wordt met sterk typen gecontroleerd op ongeldige waarden door XML-validatie uit te voeren op basis van het schema. Dit gedrag kan u helpen bij het detecteren van eerder gemaakte problemen. De optie voor **veilig typen** is beschikbaar voor achterwaartse compatibiliteit en controleert alleen de lengte van de teken reeks. Meer informatie over de [optie voor veilig typen](#safe-typing).

   1. Wanneer u klaar bent, selecteert u **Maken**.

      Logic Apps stelt uw verbinding in en test deze om te controleren of de verbinding goed werkt.

    > [!NOTE]

    > Als het volgende fout bericht wordt weer gegeven, is er een probleem met de installatie van de SAP NCo-client bibliotheek: 
    >
    > **De test verbinding is mislukt. Fout: kan de aanvraag niet verwerken. Fout Details: kan bestand of Assembly ' sapnco, version = 3.0.0.42, Culture = neutrale, PublicKeyToken 50436dca5c7f7d23 ' of een van de bijbehorende afhankelijkheden niet laden. Het systeem kan het opgegeven bestand niet vinden.**
    >
    > Zorg ervoor dat u [de vereiste versie van de SAP NCo-client bibliotheek installeert en aan alle andere vereisten voldoet](#sap-client-library-prerequisites).

1. Nu vindt u een actie op de SAP-server en selecteert u deze.

   1. Selecteer het mappictogram in het vak **actie van SAP** . Zoek en selecteer in de lijst bestand het SAP-bericht dat u wilt gebruiken. Gebruik de pijlen om door de lijst te navigeren.

      In dit voor beeld wordt een IDoc geselecteerd met het type **Orders** .

      ![Zoek en selecteer IDoc-actie](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Als de gewenste actie niet kan worden gevonden, kunt u hand matig een pad invoeren, bijvoorbeeld:

      ![Hand matig het pad naar de IDoc-actie opgeven](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Geef de waarde voor **SAP-actie** op via de expressie-editor. Op die manier kunt u dezelfde actie voor verschillende bericht typen gebruiken.

      Zie [bericht schema's voor IDOC-bewerkingen](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)voor meer informatie over IDOC-bewerkingen.

   1. Klik in het vak **invoer bericht** zodat de lijst met dynamische inhoud wordt weer gegeven. Selecteer in deze lijst, onder **Wanneer een HTTP-aanvraag wordt ontvangen**, het veld **hoofd tekst** .

      In deze stap wordt de inhoud van de hoofd tekst van uw HTTP-aanvraag trigger opgenomen en wordt die uitvoer naar uw SAP-server verzonden.

      ![Selecteer de eigenschap Body in de trigger](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Wanneer u klaar bent, ziet uw SAP-actie eruit als in dit voor beeld:

      ![De SAP-actie volt ooien](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

#### <a name="send-flat-file-idocs"></a>IDocs voor platte bestanden verzenden

U kunt IDocs gebruiken met een plat bestands schema als u deze inpakt in een XML-envelop. Als u een IDoc voor een plat bestand wilt verzenden, gebruikt u de algemene instructies om [een SAP-actie te maken om uw IDOC-bericht te verzenden](#create-sap-action-to-send-message) met de volgende wijzigingen.

1. Voor de actie **bericht verzenden naar SAP** gebruikt u SAP-actie-URI `http://microsoft.lobservices.sap/2007/03/Idoc/SendIdoc` .

1. Uw invoer bericht opmaken met een XML-envelop. Zie het volgende voorbeeld bericht voor een voor beeld:

```xml
<?xml version="1.0" encoding="utf-8"?>
<SendIdoc xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/">
  <idocData>EDI_DC    300                      ORDERS052SAPMSS    LIMSFTABCSWI                                                                                           ED  93AORDERSOLP     VLTRFC    KUMSFTABCSWI                                                                                           13561                       231054476                                                                           20190523085430ORDERSORDERS05          US
E2EDK01005300                1     E2EDK010050     1       USD                                                                        Z4O14506907554
E2EDK03   300                2     E2EDK03   0     2   02220190523
E2EDKA1   300                3     E2EDKA1   0     2   RE                  MSFTASWI
E2EDKA1   300                4     E2EDKA1   0     2   US                  MSFTASWI
E2EDKA1   300                5     E2EDKA1   0     2   WE                  MSFTASWILIC
E2EDKA1   300                6     E2EDKA1   0     2   Z1 KKKKKKK                           ABC YYYYYYYYYYY ZZ                                                                                                                          BBBBBBBBBBBBBBBB 11                                                                                      ttttttttttt                                 6666              US                                                                                                999 999 99 99                                                                                                                SSSSSSS SSS SSSSSS                                                                                                                                SSSSSSS SSS SSSSSS
E2EDKA1   300                7     E2EDKA1   0     2   Z2 KKKKKKK                           BBBBBBBBBBBBBBBB DDDDDDDD ZZ                                                                                                                EEEEEEEEEEE 86                                                                                           rrrrrrrr                                    8888              US                                                                                                999 999 99 99                                                                                                                NNNNNN NNNNNN                                                                                                                                     NNNNNN NNNNNN
E2EDK02   300                8     E2EDK02   0     2   901Z
E2EDK02   300                9     E2EDK02   0     2   90399680096ZZS2002
E2EDK02   300                10    E2EDK02   0     2   902S
E2EDKT1   300                11    E2EDKT1   0     2   Z1EME
E2EDKT2   300                12    E2EDKT2   0     3   xxx@xxx-xx.xx
E2EDKT1   300                13    E2EDKT1   0     2   Z2EME
E2EDKT2   300                14    E2EDKT2   0     3   x.xxxxxx@xxxxxxxx-xxxxxxxxxx.xx
E2EDP01001300                15    E2EDP010010     2   10         1              EA                          999.9
E2EDP19   300                16    E2EDP19   0     3   00AAAA-11111</idocData>
</SendIdoc>
```



### <a name="create-http-response-action"></a>HTTP-antwoord actie maken

Voeg nu een reactie actie toe aan de werk stroom van uw logische app en neem de uitvoer op uit de SAP-actie. Op die manier retourneert uw logische app de resultaten van uw SAP-server naar de oorspronkelijke aanvrager.

1. Selecteer in de Logic Apps Designer, onder de actie SAP, de optie **nieuwe stap**.

1. Voer in het zoekvak `response` als uw filter in. Selecteer in de lijst **acties** de optie **antwoord**.

1. Klik in het vak **hoofd tekst** zodat de lijst met dynamische inhoud wordt weer gegeven. Selecteer in de lijst onder **bericht naar SAP verzenden** het veld **hoofd tekst** .

   ![SAP-actie volt ooien](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Sla uw logische app op.

#### <a name="create-rfc-request-response"></a>RFC-aanvraag maken-antwoord

> [!NOTE]
> De SAP-trigger ontvangt IDocs via tRFC, waarvoor geen antwoord parameter is ontworpen. 

U moet een aanvraag-en reactie patroon maken als u antwoorden moet ontvangen met behulp van een externe functie aanroep (RFC) voor het Logic Apps van SAP-ABAP. Als u IDocs in uw logische app wilt ontvangen, moet u de eerste actie een [HTTP-aanvraag](../connectors/connectors-native-reqres.md#add-a-response-action) met de status code `200 OK` en geen inhoud maken. Deze aanbevolen stap voltooit de SAP LUW asynchrone overdracht via tRFC onmiddellijk, waardoor de SAP CPIC-conversatie weer beschikbaar blijft. U kunt vervolgens verdere acties in uw logische app toevoegen om de ontvangen IDoc te verwerken zonder verdere overdrachten te blok keren.

Als u een aanvraag-en antwoord patroon wilt implementeren, moet u eerst het RFC-schema detecteren met behulp van de [ `generate schema` opdracht](#generate-schemas-for-artifacts-in-sap). Het gegenereerde schema heeft twee mogelijke hoofd knooppunten: 

1. Het aanvraag knooppunt, dat de oproep is die u van SAP ontvangt.

1. Het antwoord knooppunt, dat uw antwoord terugstuurt naar SAP.

In het volgende voor beeld wordt een aanvraag-en antwoord patroon gegenereerd vanuit de `STFC_CONNECTION` RFC-module. De XML van de aanvraag wordt geparseerd om een knooppunt waarde met SAP-aanvragen uit te pakken `<ECHOTEXT>` . Met het antwoord wordt de huidige tijds tempel ingevoegd als een dynamische waarde. U ontvangt een soortgelijk antwoord wanneer u een `STFC_CONNECTION` RFC van een logische app naar SAP verzendt.

```http

<STFC_CONNECTIONResponse xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <ECHOTEXT>@{first(xpath(xml(triggerBody()?['Content']), '/*[local-name()="STFC_CONNECTION"]/*[local-name()="REQUTEXT"]/text()'))}</ECHOTEXT>
  <RESPTEXT>Azure Logic Apps @{utcNow()}</RESPTEXT>


```

### <a name="test-logic-app"></a>Logische app testen

1. Als uw logische app nog niet is ingeschakeld, selecteert u **overzicht** in het menu van de logische app. Selecteer **inschakelen** op de werk balk.

1. Selecteer **uitvoeren** op de werk balk van de ontwerp functie. Met deze stap wordt de logische app hand matig gestart.

1. Activeer uw logische app door een HTTP POST-aanvraag te verzenden naar de URL in uw HTTP-aanvraag trigger.
Neem uw bericht inhoud op met uw aanvraag. U kunt een hulp programma zoals [postman](https://www.getpostman.com/apps)gebruiken om de aanvraag te verzenden.

   Voor dit artikel verzendt de aanvraag een IDoc-bestand, dat moet de XML-indeling hebben en de naam ruimte bevatten voor de SAP-actie die u gebruikt, bijvoorbeeld:

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Nadat u uw HTTP-aanvraag hebt verzonden, moet u wachten op de reactie van de logische app.

   > [!NOTE]
   > Mogelijk is er een time-out opgetreden voor uw logische app als alle stappen die vereist zijn voor het antwoord niet binnen de [time-outlimiet](./logic-apps-limits-and-config.md)van de aanvraag vallen. Als dit probleem optreedt, worden aanvragen mogelijk geblokkeerd. Lees hoe u [uw Logic apps kunt controleren en controleren](../logic-apps/monitor-logic-apps.md)voor meer informatie over het vaststellen van problemen.

U hebt nu een logische app gemaakt die kan communiceren met uw SAP-server. Nu u een SAP-verbinding hebt ingesteld voor uw logische app, kunt u andere beschik bare SAP-acties, zoals BAPI en RFC, verkennen.

## <a name="receive-message-from-sap"></a>Bericht ontvangen van SAP

In dit voor beeld wordt een logische app gebruikt die wordt geactiveerd wanneer de app een bericht van een SAP-systeem ontvangt.

### <a name="add-an-sap-trigger"></a>Een SAP-trigger toevoegen

1. Maak in de Azure Portal een lege logische app, waarmee de Logic Apps Designer wordt geopend.

1. Voer in het zoekvak `sap` als uw filter in. Selecteer in de lijst **Triggers** **Wanneer een bericht wordt ontvangen van SAP**.

   ![SAP-trigger toevoegen](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   U kunt ook het tabblad **onderneming** selecteren en vervolgens de trigger selecteren:

   ![SAP-trigger toevoegen vanuit het tabblad onderneming](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Als uw verbinding al bestaat, gaat u door met de volgende stap, zodat u uw SAP-actie kunt instellen. Als u echter om de verbindings gegevens wordt gevraagd, geeft u de informatie op zodat u nu een verbinding met uw on-premises SAP-server kunt maken.

   1. Geef een naam op voor de verbinding.

   1. Als u de gegevens gateway gebruikt, voert u de volgende stappen uit:

      1. Selecteer in de sectie **gegevens gateway** , onder **abonnement**, eerst het Azure-abonnement voor de gegevens gateway resource die u hebt gemaakt in de Azure portal voor uw data Gateway-installatie.

      1. Onder **verbindings gateway** selecteert u uw gegevens gateway resource in Azure.

   1. Ga door met het verstrekken van informatie over de verbinding. Volg voor de eigenschap **aanmeldings type** de stap op basis van het feit of de eigenschap is ingesteld op **toepassings server** of **groep**:

      * Voor **toepassings server** zijn deze eigenschappen, die meestal optioneel worden weer gegeven, vereist:

        ![Een SAP-toepassings server verbinding maken](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Voor **groep** zijn deze eigenschappen, die meestal optioneel worden weer gegeven, vereist:

        ![SAP Message server-verbinding maken](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Standaard wordt met sterk typen gecontroleerd op ongeldige waarden door XML-validatie uit te voeren op basis van het schema. Dit gedrag kan u helpen bij het detecteren van eerder gemaakte problemen. De optie voor **veilig typen** is beschikbaar voor achterwaartse compatibiliteit en controleert alleen de lengte van de teken reeks. Meer informatie over de [optie voor veilig typen](#safe-typing).

   1. Wanneer u klaar bent, selecteert u **Maken**.

      Logic Apps stelt uw verbinding in en test deze om te controleren of de verbinding goed werkt.

1. Geef de [vereiste para meters](#parameters) op op basis van de configuratie van uw SAP-systeem. 

   U kunt [de berichten die u ontvangt van uw SAP-server filteren door een lijst met SAP-acties op te geven](#filter-with-sap-actions).

   U kunt een SAP-actie selecteren in de bestands kiezer:

   ![SAP-actie toevoegen aan de logische app](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   U kunt ook hand matig een actie opgeven:

   ![Voer hand matig een SAP-actie in die u wilt gebruiken](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Hier volgt een voor beeld waarin wordt getoond hoe de actie wordt weer gegeven wanneer u de trigger zo instelt dat er meer dan één bericht wordt ontvangen.

   ![Trigger voorbeeld dat meerdere berichten ontvangt](media/logic-apps-using-sap-connector/example-trigger.png)

   Zie [bericht schema's voor IDOC-bewerkingen](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations) voor meer informatie over de SAP-actie.

1. Sla de logische app nu op zodat u berichten van uw SAP-systeem kunt ontvangen. Selecteer **Opslaan** op de werkbalk van de ontwerper.

Uw logische app is nu klaar om berichten te ontvangen van uw SAP-systeem.

> [!NOTE]
> De SAP-trigger is geen polling-trigger, maar is in plaats daarvan een webhook-trigger. Als u de gegevens gateway gebruikt, wordt de trigger alleen vanuit de gegevens gateway aangeroepen wanneer er een bericht bestaat. er is dus geen polling nodig.

#### <a name="parameters"></a>Parameters

De SAP-connector accepteert samen met eenvoudige teken reeks-en nummer invoer de volgende tabel parameters ( `Type=ITAB` invoer):

* Tabel richting para meters, zowel invoer als uitvoer, voor oudere SAP-releases.

* Para meters wijzigen, waardoor de tabel richtings parameters worden vervangen door nieuwere SAP-releases.

* Hiërarchische tabel parameters

#### <a name="filter-with-sap-actions"></a>Filteren met SAP-acties

U kunt desgewenst de berichten die uw logische app ontvangt van uw SAP-server filteren door een lijst, of matrix, te leveren met een of meer SAP-acties. Deze matrix is standaard leeg, wat betekent dat uw logische app alle berichten van uw SAP-server ontvangt zonder te filteren. 

Wanneer u het matrix filter instelt, ontvangt de trigger alleen berichten van de opgegeven SAP-actie typen en worden alle andere berichten van uw SAP-server geweigerd. Dit filter is echter niet van invloed op of het type van de ontvangen Payload zwak of sterk is.

SAP-actie filters worden uitgevoerd op het niveau van de SAP-adapter voor uw on-premises gegevens gateway. Zie [test IDocs verzenden naar Logic apps vanuit SAP](#test-sending-idocs-from-sap)voor meer informatie.

Als u geen IDoc-pakketten van SAP kunt verzenden naar de trigger van de logische app, raadpleegt u het afkeurings bericht voor transactionele RFC (tRFC)-oproep in het dialoog venster SAP tRFC (T-code SM58). In de SAP-interface worden mogelijk de volgende fout berichten weer geven die zijn afgekapt als gevolg van de limieten voor subtekenreeksen in het veld **status tekst** .

* `The RequestContext on the IReplyChannel was closed without a reply being`: Onverwachte fouten treden op wanneer de catch-all-handler voor het kanaal het kanaal beëindigt vanwege een fout en het kanaal opnieuw opbouwt om andere berichten te verwerken.

  * Als u wilt bevestigen dat uw logische app de IDoc heeft ontvangen, [voegt u een reactie actie toe](../connectors/connectors-native-reqres.md#add-a-response-action) die een `200 OK` status code retourneert. De IDoc wordt getransporteerd via tRFC, die geen respons lading toestaat.

  * Als u in plaats daarvan het IDoc moet afwijzen, reageert u met een andere HTTP-status code dan `200 OK` zodat de SAP-adapter een uitzonde ring voor uw naam terugstuurt naar SAP. 

* `The segment or group definition E2EDK36001 was not found in the IDoc meta`: Verwachte fouten worden veroorzaakt door andere fouten, zoals de fout bij het genereren van een IDoc XML-nettolading, omdat de bijbehorende segmenten niet worden vrijgegeven door SAP, waardoor de vereiste meta gegevens voor het segment type voor de conversie ontbreken. 

  * Neem contact op met de ABAP-Engineer voor uw SAP-systeem om deze segmenten te kunnen vrijgeven door SAP.
### <a name="asynchronous-request-reply-for-triggers"></a>Asynchrone aanvraag-antwoord voor triggers

De SAP-connector ondersteunt het [asynchrone antwoord patroon](/azure/architecture/patterns/async-request-reply) van Azure voor Logic apps triggers. U kunt dit patroon gebruiken om succes volle aanvragen te maken die anders zouden zijn mislukt met het standaard patroon voor synchrone antwoorden op aanvraag-antwoord. 

> [!TIP]
> In Logic apps met meerdere reactie acties moeten alle reactie acties gebruikmaken van hetzelfde antwoord patroon voor aanvragen en antwoorden. Als uw logische app bijvoorbeeld gebruikmaakt van een switch-besturings element met meerdere mogelijke reactie acties, moet u alle antwoord acties configureren voor het gebruik van hetzelfde aanvraag/antwoord patroon, ofwel synchroon of asynchroon. 

Als u asynchrone reacties op uw reactie actie inschakelt, kan uw logische app reageren met een `202 Accepted` antwoord wanneer een aanvraag voor verwerking is geaccepteerd. Het antwoord bevat een locatie-header die u kunt gebruiken om de uiteindelijke status van uw aanvraag op te halen.

Voor het configureren van een asynchroon patroon van een aanvraag/antwoord voor uw logische app met behulp van de SAP-connector:

1. Open uw logische app in de **ontwerp functie voor Logic apps**.

1. Controleer of de SAP-connector de trigger is voor uw logische app.

1. Open de **reactie** actie van de logische app. Selecteer in de titel balk van de actie het menu (**...**) &gt; **Instellingen**.

1. Schakel in de **instellingen** voor uw antwoord actie de wissel knop onder **asynchroon antwoord** in. Selecteer gereed.

1. Sla de wijzigingen in uw logische app op.

## <a name="find-extended-error-logs"></a>Uitgebreide foutenlogboeken zoeken

Controleer de uitgebreide logboeken van de SAP-adapter voor volledige fout berichten. U kunt ook [een uitgebreid logboek bestand inschakelen voor de SAP-connector](#extended-sap-logging-in-on-premises-data-gateway).

Voor on-premises gegevens gateway releases van juni 2020 en hoger kunt u [Gateway logboeken inschakelen in de app-instellingen](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app). 

* Het standaard logboek registratie niveau is **waarschuwing**.

* Als u  **extra logboek registratie** inschakelt in de **Diagnostische** instellingen van de on-premises gegevens gateway-app, wordt het logboek registratie niveau uitgebreid naar **informatie**.

* Als u het niveau van logboek registratie wilt **verhogen, werkt u de** volgende instelling in het configuratie bestand bij. Normaal gesp roken bevindt het configuratie bestand zich op `C:\Program Files\On-premises data gateway\Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config` .

```json
<setting name="SapTraceLevel" serializeAs="String">
   <value>Verbose</value>
</setting>
```

Voor on-premises gegevens gateway releases van 2020 april en eerder zijn Logboeken standaard uitgeschakeld.

### <a name="extended-sap-logging-in-on-premises-data-gateway"></a>Uitgebreide SAP-logboek registratie bij on-premises gegevens gateway

Als u een [on-premises gegevens gateway gebruikt voor Logic apps](../logic-apps/logic-apps-gateway-install.md), kunt u een uitgebreid logboek bestand voor de SAP-connector configureren. U kunt uw on-premises gegevens gateway gebruiken om Event Tracing for Windows-gebeurtenissen (ETW) om te leiden naar roulerende logboek bestanden die zijn opgenomen in de logboek bestanden van uw gateway. 

U kunt [alle configuratie-en service logboeken van uw gateway exporteren](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app) naar een zip-bestand in de instellingen van de gateway-app.

> [!NOTE]
> Uitgebreide logboek registratie kan van invloed zijn op de prestaties van uw Logic apps wanneer altijd is ingeschakeld. Het is een best practice om uitgebreide logboek bestanden uit te scha kelen nadat u klaar bent met het analyseren van een probleem en het oplossen van problemen.

#### <a name="capture-etw-events"></a>ETW-gebeurtenissen vastleggen

Geavanceerde gebruikers kunnen optioneel ETW-gebeurtenissen rechtstreeks vastleggen. U kunt vervolgens [uw gegevens in azure Diagnostics gebruiken in Event hubs](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md) of [uw gegevens verzamelen in azure monitor logboeken](/azure/azure-monitor/agents/diagnostics-extension-logs). Zie voor meer informatie de [Aanbevolen procedures voor het verzamelen en opslaan van gegevens](/azure/architecture/best-practices/monitoring#collecting-and-storing-data). U kunt [PerfView](https://github.com/Microsoft/perfview/blob/master/README.md) gebruiken om te werken met de resulterende etl-bestanden of u kunt uw eigen programma schrijven. In deze walkthrough wordt gebruikgemaakt van PerfView:

1. Selecteer in het menu **PerfView verzamelen Collect** &gt;  om de gebeurtenissen vast te leggen.

1. Voer in het veld **aanvullende provider** in `*Microsoft-LobAdapter` om de SAP-provider op te geven voor het vastleggen van SAP-adapter gebeurtenissen. Als u deze informatie niet opgeeft, omvat de tracering alleen algemene ETW-gebeurtenissen.

1. Behoud de andere standaard instellingen. Als u wilt, kunt u de bestands naam of-locatie in het veld **gegevens bestand** wijzigen.

1. Selecteer **verzameling starten** om te beginnen met de tracering.

1. Nadat u het probleem hebt gereproduceerd of voldoende analyse gegevens hebt verzameld, selecteert u **verzameling stoppen**.

Als u uw gegevens wilt delen met een andere partij, zoals ondersteunings technici van Azure, comprimeert u het ETL-bestand.

De inhoud van uw tracering weer geven:

1. Selecteer in PerfView **bestand** &gt; **openen** en selecteer het ETL-bestand dat u zojuist hebt gegenereerd.

1. Ga in de PerfView Sidebar naar het gedeelte **Events** onder uw ETL-bestand.

1. Filter onder **filteren** op `Microsoft-LobAdapter` om alleen relevante gebeurtenissen en gateway processen weer te geven.

### <a name="test-your-logic-app"></a>Uw logische app testen

1. Als u uw logische app wilt activeren, verzendt u een bericht van uw SAP-systeem.

1. Selecteer **overzicht** in het menu van de logische app. Bekijk de **uitvoerings geschiedenis** voor nieuwe uitvoeringen voor uw logische app.

1. Open de meest recente uitvoering, waarin het bericht wordt weer gegeven dat vanuit uw SAP-systeem is verzonden in de sectie trigger uitvoer.

### <a name="test-sending-idocs-from-sap"></a>Verzenden van IDocs vanuit SAP testen

Als u IDocs van SAP naar uw logische app wilt verzenden, moet u de volgende minimale configuratie configureren:

> [!IMPORTANT]
> Gebruik deze stappen alleen wanneer u uw SAP-configuratie test met uw logische app. Productie omgevingen vereisen aanvullende configuratie.

1. [Een RFC-doel configureren in SAP](#create-rfc-destination)

1. [Een ABAP-verbinding maken met uw RFC-doel](#create-abap-connection)

1. [Een ontvanger poort maken](#create-receiver-port)

1. [Een zender poort maken](#create-sender-port)

1. [Een logische systeem partner maken](#create-logical-system-partner)

1. [Een partner profiel maken](#create-partner-profiles)

1. [Testen van berichten verzenden](#test-sending-messages)

#### <a name="create-rfc-destination"></a>RFC-doel maken

1. Als u de **configuratie van instellingen voor een RFC-verbinding** wilt openen, gebruikt u in uw SAP-interface de **sm59** -transactie code (T-code) met het voor voegsel **/n** .

1. Selecteer **TCP/IP-verbindingen**  >  **maken**.

1. Maak een nieuwe RFC-doel met de volgende instellingen:
    
    * Voer een naam in voor uw **RFC-doel**.
    
    * Selecteer op het tabblad **technische instellingen** bij **activerings type** de optie **geregistreerd server programma**. Voer een waarde in voor de **programma-id**. In SAP wordt de trigger van uw logische app geregistreerd met behulp van deze id.
    
    * Op het tabblad **Unicode** , voor **communicatie type met doel systeem**, selecteert u **Unicode**.

1. Sla uw wijzigingen op.

1. Registreer uw nieuwe **programma-id** bij Azure Logic apps.

1. Als u de verbinding wilt testen, selecteert u in de SAP-interface, onder uw nieuwe **RFC-doel**, de optie **verbinding testen**.

#### <a name="create-abap-connection"></a>ABAP-verbinding maken

1. Als u de **configuratie van instellingen voor een RFC-verbinding** wilt openen, gebruikt u in uw SAP-interface de **sm59** _ transactie code (T-code) met het voor voegsel _ */n**.

1. Selecteer **ABAP-verbindingen**  >  **maken**.

1. Voor **RFC-doel** voert u de id in voor [het test-SAP-systeem](#create-rfc-destination).

1. Sla uw wijzigingen op.

1. Als u de verbinding wilt testen, selecteert u **verbindings test**.

#### <a name="create-receiver-port"></a>Ontvanger poort maken

1. Als u de **poorten in IDOC-verwerkings** instellingen wilt openen, gebruikt u in uw SAP-interface de **we21** -transactie code (T-code) met het voor voegsel **/n** .

1. Selecteer **poorten**  >  **transactionele RFC**  >  **Create**.

1. In het dialoog venster instellingen dat wordt geopend, selecteert u **eigen poort naam**. Voer een **naam** in voor de test poort. Sla uw wijzigingen op.

1. In de instellingen voor uw nieuwe ontvanger poort, voor **RFC-doel**, voert u de id in voor [de test-RFC-bestemming](#create-rfc-destination).

1. Sla uw wijzigingen op.

#### <a name="create-sender-port"></a>Poort van afzender maken

1.  Als u de **poorten in IDOC-verwerkings** instellingen wilt openen, gebruikt u in uw SAP-interface de **we21** -transactie code (T-code) met het voor voegsel **/n** .

1. Selecteer **poorten**  >  **transactionele RFC**  >  **Create**.

1. In het dialoog venster instellingen dat wordt geopend, selecteert u **eigen poort naam**. Voer voor uw test poort een **naam** in die begint met **SAP**. Alle poort namen van de afzender moeten beginnen met de letters **SAP**, bijvoorbeeld **SAPTEST**. Sla uw wijzigingen op.

1. Voer in de instellingen voor de nieuwe zender poort voor **RFC-doel** de id in voor [uw ABAP-verbinding](#create-abap-connection).

1. Sla uw wijzigingen op.

#### <a name="create-logical-system-partner"></a>Een logische systeem partner maken

1. Als u de **wijzigings weergave logische systemen wilt openen: overzichts** instellingen in uw SAP-interface, gebruikt u de **bd54** -transactie code (T-code).

1. Accepteer het waarschuwings bericht dat wordt weer gegeven: **Waarschuwing: de tabel is kruislings-client**

1. Selecteer in de lijst met de bestaande logische systemen **nieuwe vermeldingen**.

1. Voer voor het nieuwe logische systeem een **Log.System** -id en een korte **naam** beschrijving in. Sla uw wijzigingen op.

1. Wanneer de **prompt voor Workbench** wordt weer gegeven, maakt u een nieuwe aanvraag door een beschrijving op te geven, of als u al een aanvraag hebt gemaakt, slaat u deze stap over.

1. Nadat u de workbench-aanvraag hebt gemaakt, koppelt u die aanvraag aan de tabel update-aanvraag. Sla de wijzigingen op om te bevestigen dat de tabel is bijgewerkt.

#### <a name="create-partner-profiles"></a>Partner profielen maken

Voor productie omgevingen moet u twee partner profielen maken. Het eerste profiel is voor de afzender, dat wil zeggen uw organisatie en het SAP-systeem. Het tweede profiel is voor de ontvanger, de logische app.

1. Als u de instellingen van de **partner profielen** wilt openen, gebruikt u in uw SAP-interface de **we20** -transactie code (T-code) met het voor voegsel **/n** .

1. Onder **partner profielen** selecteert u **partner type LS**  >  **Create**.

1. Maak een nieuw Partner profiel met de volgende instellingen:

    * Voer [de id van uw logische systeem partner](#create-logical-system-partner)in als **partner nummer**.

    * Voor **Parts. Typ** **ls**.

    * Voer voor **agent** de id in voor het SAP-gebruikers account dat moet worden gebruikt wanneer u programma-id's registreert voor Azure Logic apps of andere niet-SAP-systemen.

1. Sla uw wijzigingen op. Als u [de logische systeem partner](#create-logical-system-partner)nog niet hebt gemaakt, krijgt u de fout melding **een geldig partner nummer**.

1. Selecteer in de instellingen van uw partner profiel onder **uitgaande parmtrs.** de optie **uitgaande para meter maken**.

1. Maak een nieuwe uitgaande para meter met de volgende instellingen:

    * Voer uw **bericht type** in, bijvoorbeeld **CREMAS**.

    * Voer de [id van de poort van de ontvanger](#create-receiver-port)in.

    * Voer een IDoc-grootte in voor het **pakket. Grootte**. Als u [IDocs een voor een wilt verzenden vanuit SAP](#receive-idoc-packets-from-sap), selecteert u **onmiddellijk door geven IDOC**.

1. Sla uw wijzigingen op.

#### <a name="test-sending-messages"></a>Testen van berichten verzenden

1. Als u het **test programma voor IDOC-verwerkings** instellingen wilt openen, gebruikt u in uw SAP-interface de **we19** -transactie code (T-code) met het voor voegsel **/n** .

1. Selecteer onder **sjabloon voor testen** de optie **via bericht type** en voer uw bericht type in, bijvoorbeeld **CREMAS**. Selecteer **Maken**.

1. Bevestig het **IDOC-type?** bericht door **door gaan** te selecteren.

1. Selecteer het knoop punt **EDIDC** . Voer de juiste waarden in voor uw ontvanger en de poorten van de afzender. Selecteer **Doorgaan**.

1. Selecteer **standaard uitgaande verwerking**.

1. Selecteer **door gaan** als u de verwerking van uitgaande IDOC wilt starten. Wanneer de verwerking is voltooid, wordt de **IDOC verzonden naar het SAP-systeem of het externe programma** bericht weer gegeven.

1.  Als u de verwerkings fouten wilt controleren, gebruikt u de **SM58** -transactie code (T-code) met het voor voegsel **/n** .

## <a name="receive-idoc-packets-from-sap"></a>IDoc pakketten ontvangen van SAP

U kunt SAP instellen voor het [verzenden van IDocs in pakketten](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html), die batches of groepen van IDocs zijn. Voor het ontvangen van IDoc-pakketten, de SAP-connector en de specifiek de trigger, hebt u geen extra configuratie nodig. Als u elk item in een IDoc-pakket echter wilt verwerken nadat de trigger het pakket heeft ontvangen, zijn er aanvullende stappen vereist om het pakket te splitsen in afzonderlijke IDocs.

Hier volgt een voor beeld waarin wordt uitgelegd hoe u afzonderlijke IDocs uit een pakket kunt ophalen met behulp van de [ `xpath()` functie](./workflow-definition-language-functions-reference.md#xpath):

1. Voordat u begint, hebt u een logische app met een SAP-trigger nodig. Als u deze logische app nog niet hebt, volgt u de vorige stappen in dit onderwerp om [een logische app in te stellen met een SAP-trigger](#receive-message-from-sap).

   Bijvoorbeeld:

   ![SAP-trigger toevoegen aan de logische app](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Haal de hoofd naam ruimte op uit de XML-IDoc die uw logische app van SAP ontvangt. Als u deze naam ruimte uit het XML-document wilt extra heren, voegt u een stap toe die een lokale teken reeks variabele maakt en slaat die naam ruimte op met behulp van een `xpath()` expressie:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Hoofd naam ruimte ophalen van IDoc](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Als u een afzonderlijke IDoc wilt extra heren, voegt u een stap toe waarmee een matrix variabele wordt gemaakt en wordt de IDoc-verzameling opgeslagen met behulp van een andere `xpath()` expressie:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Matrix van items ophalen](./media/logic-apps-using-sap-connector/get-array.png)

   De matrix variabele maakt elk IDoc beschikbaar voor uw logische app door het inventariseren van de verzameling. In dit voor beeld stuurt de logische app elke IDoc naar een SFTP-server met behulp van een lus:

   ![IDoc naar SFTP-server verzenden](./media/logic-apps-using-sap-connector/loop-batch.png)

   Elke IDoc moet de hoofd naam ruimte bevatten. Dit is de reden waarom de bestands inhoud in een- `<Receive></Receive` element samen met de hoofd naam ruimte wordt verpakt voordat de IDOC naar de downstream-app of de sftp-server in dit geval wordt verzonden.

U kunt de Quick Start-sjabloon voor dit patroon gebruiken door deze sjabloon te selecteren in de Logic Apps Designer wanneer u een nieuwe logische app maakt.

![Sjabloon voor batch Logic app selecteren](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Schema's genereren voor artefacten in SAP

In dit voor beeld wordt een logische app gebruikt die u kunt activeren met een HTTP-aanvraag. Voor het genereren van de schema's voor de opgegeven IDoc en BAPI, verzendt het SAP-actie **schema** een aanvraag naar een SAP-systeem.

Deze SAP-actie retourneert een [XML-schema](#sample-xml-schemas), niet de inhoud of gegevens van het XML-document zelf. Schema's die in het antwoord worden geretourneerd, worden geüpload naar een integratie account met behulp van de Azure Resource Manager-connector. Schema's bevatten de volgende onderdelen:

* De structuur van het aanvraag bericht. Gebruik deze informatie om uw BAPI- `get` lijst te maken.

* De structuur van het antwoord bericht. Gebruik deze informatie voor het parseren van het antwoord. 

Als u het aanvraag bericht wilt verzenden, gebruikt u de algemene SAP-actie **bericht verzenden naar SAP** of de BAPI-acties van de doel **oproep** .

### <a name="sample-xml-schemas"></a>XML-voor beeld-schema's

Als u een XML-schema voor het maken van een voorbeeld document wilt genereren, raadpleegt u de volgende voor beelden. In deze voor beelden ziet u hoe u kunt werken met veel typen nettoladingen, waaronder:

* [RFC-aanvragen](#xml-samples-for-rfc-requests)

* [BAPI-aanvragen](#xml-samples-for-bapi-requests)

* [IDoc aanvragen](#xml-samples-for-idoc-requests)

* Eenvoudige of complexe XML-schema gegevens typen

* Tabel parameters

* Optioneel XML-gedrag

U kunt beginnen met het XML-schema met een optionele XML-begin. De SAP-connector werkt met of zonder de XML-begin.

```xml

<?xml version="1.0" encoding="utf-8">

```

#### <a name="xml-samples-for-rfc-requests"></a>XML-voorbeelden voor RFC-aanvragen

Het volgende voor beeld is een eenvoudige RFC-aanroep. De RFC-naam is `STFC_CONNECTION` . Deze aanvraag gebruikt de standaard naam ruimte `xmlns=` , maar u kunt wel naam ruimte aliassen toewijzen en gebruiken, zoals `xmmlns:exampleAlias=` . De naam ruimte waarde is de naam ruimte voor alle Rfc's in SAP voor micro soft-Services. De aanvraag bevat een eenvoudige invoer parameter `<REQUTEXT>` .

```xml

<STFC_CONNECTION xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <REQUTEXT>exampleInput</REQUTEXT>
</STFC_CONNECTION>

```

Het volgende voor beeld is een RFC-aanroep met een tabel parameter. Deze voorbeeld aanroep en de bijbehorende groep test Rfc's zijn beschikbaar als onderdeel van alle SAP-systemen. De naam van de tabel parameter is `TCPICDAT` . Het tabel regel type is `ABAPTEXT` en dit element wordt herhaald voor elke rij in de tabel. Dit voor beeld bevat één regel, die wordt genoemd `LINE` . Aanvragen met een tabel parameter kunnen een wille keurig aantal velden bevatten, waarbij het getal een positief geheel getal is (*n*). 

```xml

<STFC_WRITE_TO_TCPIC xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <RESTART_QNAME>exampleQName</RESTART_QNAME>
    <TCPICDAT>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput1</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput2</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput3</LINE>
      </ABAPTEXT>
    </TCPICDAT>
</STFC_WRITE_TO_TCPIC>

```

Het volgende voor beeld is een RFC-aanroep met een tabel parameter die een anoniem veld heeft. Een anoniem veld is wanneer aan het veld geen naam is toegewezen. Complexe typen worden gedeclareerd onder een afzonderlijke naam ruimte, waarbij de declaratie een nieuwe standaard waarde voor het huidige knoop punt en alle onderliggende elementen definieert. In het voor beeld wordt de hex-code gebruikt `x002F` als escape teken voor het symbool */* , omdat dit symbool wordt gereserveerd in de SAP-veld naam.

```xml

<RFC_XML_TEST_1 xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <IM_XML_TABLE>
    <RFC_XMLCNT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
      <_x002F_AnonymousField>exampleFieldInput</_x002F_AnonymousField>
    </RFC_XMLCNT>
  </IM_XML_TABLE>
</RFC_XML_TEST_1>

```

Het volgende voor beeld bevat voor voegsels voor de naam ruimten. U kunt alle voor voegsels tegelijk declareren of u kunt een wille keurig aantal voor voegsels declareren als kenmerken van een knoop punt. De naam ruimte-alias van de RFC `ns0` wordt gebruikt als de hoofd-en-para meters voor het basis type.

> [!NOTE]
> complexe typen worden gedeclareerd onder een andere naam ruimte voor typen RFC'S met de alias `ns3` in plaats van de reguliere RFC-naam ruimte met de alias `ns0` .

```xml

<ns0:BBP_RFC_READ_TABLE xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Rfc/" xmlns:ns3="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc/">
  <ns0:DELIMITER>0</ns0:DELIMITER>
  <ns0:QUERY_TABLE>KNA1</ns0:QUERY_TABLE>
  <ns0:ROWCOUNT>250</ns0:ROWCOUNT>
  <ns0:ROWSKIPS>0</ns0:ROWSKIPS>
  <ns0:FIELDS>
    <ns3:RFC_DB_FLD>
      <ns3:FIELDNAME>KUNNR</ns3:FIELDNAME>
    </ns3:RFC_DB_FLD>
  </ns0:FIELDS>
</ns0:BBP_RFC_READ_TABLE>

```

#### <a name="xml-samples-for-bapi-requests"></a>XML-voorbeelden voor BAPI-aanvragen

De volgende XML-voor beelden zijn voorbeeld aanvragen om [de BAPI-methode](#call-bapi-action)aan te roepen.

> [!NOTE]
> Met SAP kunnen zakelijke objecten beschikbaar worden gesteld aan externe systemen door ze te beschrijven in reactie op RFC `RPY_BOR_TREE_INIT` , die Logic apps problemen zonder invoer filter. Logic Apps inspecteert de uitvoer tabel `BOR_TREE` . Het `SHORT_TEXT` veld wordt gebruikt voor namen van bedrijfs objecten. Bedrijfs objecten die niet door SAP in de uitvoer tabel worden geretourneerd, zijn niet toegankelijk voor Logic Apps.
> 
> Als u aangepaste zakelijke objecten gebruikt, moet u ervoor zorgen dat u deze zakelijke objecten publiceert en vrijgeeft in SAP. Als u dit niet doet, worden uw aangepaste bedrijfs objecten niet vermeld in de uitvoer tabel `BOR_TREE` . U hebt geen toegang tot uw aangepaste bedrijfs objecten in Logic Apps totdat u de zakelijke objecten van SAP beschikbaar maakt. 

In het volgende voor beeld wordt een lijst met banken opgehaald met behulp van de BAPI-methode `GETLIST` . Dit voor beeld bevat het zakelijke object voor een bank, `BUS1011` . 

```xml

<GETLIST xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_CTRY>US</BANK_CTRY>
  <MAX_ROWS>10</MAX_ROWS>
</GETLIST>

```

In het volgende voor beeld wordt een bank object gemaakt met behulp van de- `CREATE` methode. In dit voor beeld wordt hetzelfde zakelijk object gebruikt als in het vorige voor beeld `BUS1011` . Wanneer u de `CREATE` methode gebruikt om een bank te maken, moet u ervoor zorgen dat u de wijzigingen doorvoert, omdat deze methode niet standaard wordt doorgevoerd.

> [!TIP]
> Zorg ervoor dat uw XML-document een validatie regel volgt die in uw SAP-systeem is geconfigureerd. In dit voorbeeld document moet de Bank sleutel ( `<BANK_KEY>` ) bijvoorbeeld een bank Routeringnummer zijn, ook wel een ABA nummer genoemd, in de Verenigde Staten.

```xml

<CREATE xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_ADDRESS>
    <BANK_NAME xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleBankName</BANK_NAME>
    <REGION xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleRegionName</REGION>
    <STREET xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleStreetAddress</STREET>
    <CITY xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">Redmond</CITY>
  </BANK_ADDRESS>
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</CREATE>

```

In het volgende voor beeld worden Details voor een bank opgehaald met behulp van het SWIFT-nummer, de waarde voor `<BANK_KEY>` . 

```xml

<GETDETAIL xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</GETDETAIL>

```

#### <a name="xml-samples-for-idoc-requests"></a>XML-voorbeelden voor IDoc-aanvragen

Als u een gewoon SAP IDoc XML-schema wilt genereren, gebruikt u de **SAP-aanmeldings** toepassing en de T-code `WE-60` . Open de SAP-documentatie via de grafische gebruikers interface en Genereer XML-schema's in XSD-indeling voor uw IDoc-typen en-extensies. Raadpleeg de [SAP-documentatie](https://help.sap.com/viewer/index)voor een uitleg van algemene SAP-indelingen en-nettoladingen en de ingebouwde dialoog vensters.

In dit voor beeld worden het hoofd knooppunt en de naam ruimten gedeclareerd. De URI in de voorbeeld code, `http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send` en declareert de volgende configuratie:

* `/IDoc` is de hoofd notitie voor alle IDocs
* `/3` is de record type versie voor algemene segment definities
* `/ORDERS05` is het type IDoc
* `//` is een leeg segment, omdat er geen IDoc-extensie is
* `/700` is de SAP-versie
* `/Send` is de actie voor het verzenden van de informatie naar SAP

```xml

<ns0:Send xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send" xmlns:ns3="http://schemas.microsoft.com/2003/10/Serialization" xmlns:ns1="http://Microsoft.LobServices.Sap/2007/03/Types/Idoc/Common/" xmlns:ns2="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700">
  <ns0:idocData>

```

U kunt het `idocData` knoop punt herhalen om een batch IDocs te verzenden in één aanroep. In het onderstaande voor beeld is er één besturings record, `EDI_DC40` en meerdere gegevens records.

```xml

<...>
  <ns0:idocData>
    <ns2:EDI_DC40>
      <ns1:TABNAM>EDI_DC40</ns1:TABNAM>
<...>
      <ns1:ARCKEY>Cor1908207-5</ns1:ARCKEY>
    </ns2:EDI_DC40>
    <ns2:E2EDK01005>
      <ns2:DATAHEADERCOLUMN_SEGNAM>E23DK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:CURCY>USD</ns2:CURCY>
    </ns2:E2EDK01005>
    <ns2:E2EDK03>
<...>
  </ns0:idocData>

```

Het volgende voor beeld is een voor beeld van een IDoc-besturings record, die gebruikmaakt van het voor voegsel `EDI_DC` . U moet de waarden bijwerken zodat deze overeenkomen met uw SAP-installatie-en IDoc-type. Uw IDoc-client code kan bijvoorbeeld niet worden `800` . Neem contact op met uw SAP-team om ervoor te zorgen dat u de juiste waarden voor uw SAP-installatie gebruikt.

```xml

<ns2:EDI_DC40>
  <ns:TABNAM>EDI_DC40</ns1:TABNAM>
  <ns:MANDT>800</ns1:MANDT>
  <ns:DIRECT>2</ns1:DIRECT>
  <ns:IDOCTYP>ORDERS05</ns1:IDOCTYP>
  <ns:CIMTYP></ns1:CIMTYP>
  <ns:MESTYP>ORDERS</ns1:MESTYP>
  <ns:STD>X</ns1:STD>
  <ns:STDVRS>004010</ns1:STDVRS>
  <ns:STDMES></ns1:STDMES>
  <ns:SNDPOR>SAPENI</ns1:SNDPOR>
  <ns:SNDPRT>LS</ns1:SNDPRT>
  <ns:SNDPFC>AG</ns1:SNDPFC>
  <ns:SNDPRN>ABAP1PXP1</ns1:SNDPRN>
  <ns:SNDLAD></ns1:SNDLAD>
  <ns:RCVPOR>BTSFILE</ns1:RCVPOR>
  <ns:RCVPRT>LI</ns1:RCVPRT>

```

Het volgende voor beeld is een voorbeeld gegevens record met gewone segmenten. In dit voor beeld wordt de SAP-datum notatie gebruikt. Documenten met een sterk wacht woord kunnen gebruikmaken van systeem eigen XML-datum notaties, zoals `2020-12-31 23:59:59` .

```xml

<ns2:E2EDK01005>
  <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
    <ns2:CURCY>USD</ns2:CURCY>
    <ns2:BSART>OR</ns2:BSART>
    <ns2:BELNR>1908207-5</ns2:BELNR>
    <ns2:ABLAD>CC</ns2:ABLAD>
  </ns2>
  <ns2:E2EDK03>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK03</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:IDDAT>002</ns2:IDDAT>
      <ns2:DATUM>20160611</ns2:DATUM>
  </ns2:E2EDK03>

```

Het volgende voor beeld is een gegevens record met gegroepeerde segmenten. De record bevat een bovenliggend knoop punt van de groep, `E2EDKT1002GRP` en meerdere onderliggende knoop punten, inclusief `E2EDKT1002` en `E2EDKT2001` . 

```xml

<ns2:E2EDKT1002GRP>
  <ns2:E2EDKT1002>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT1002</ns2:DATAHEADERCOLUMN_SEGNAM>
      <NS2:TDID>ZONE</ns2:TDID>
  </ns2:E2EDKT1002>
  <ns2:E2EDKT2001>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT2001</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDLINE>CRSD</ns2:TDLINE>
  </ns2:E2EDKT2001>
</ns2:E2EDKT1002GRP>

```

De aanbevolen methode is om een IDoc-id te maken voor gebruik met tRFC. U kunt deze trans actie-id instellen `tid` met behulp van de [bewerking IDOC verzenden](/connectors/sap/#send-idoc) in de API van de SAP-connector.

Het volgende voor beeld is een alternatieve methode voor het instellen van de trans actie-id of `tid` . In dit voor beeld worden het laatste knoop punt van het gegevens record segment en het gegevens knooppunt IDoc gesloten. Vervolgens wordt de GUID, `guid` , die wordt gebruikt als de tRFC-id voor het detecteren van duplicaten. 

```xml

    </E2STZUM002GRP>
  </idocData>
  <guid>8820ea40-5825-4b2f-ac3c-b83adc34321c</guid>
</Send>

```

### <a name="add-an-http-request-trigger"></a>Een HTTP-aanvraag trigger toevoegen

1. Maak in de Azure Portal een lege logische app, waarmee de Logic Apps Designer wordt geopend.

1. Voer in het zoekvak `http request` als uw filter in. Selecteer in de lijst **Triggers** **Wanneer een HTTP-aanvraag wordt ontvangen**.

   ![HTTP-aanvraag trigger toevoegen](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Sla de logische app nu op zodat u een eind punt-URL kunt genereren voor uw logische app.
Selecteer **Opslaan** op de werkbalk van de ontwerper.

   De eind punt-URL wordt nu weer gegeven in de trigger, bijvoorbeeld:

   ![URL voor eind punt genereren](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Een SAP-actie toevoegen om schema's te genereren

1. Selecteer in de Logic Apps Designer, onder de trigger, de optie **nieuwe stap**.

   ![Nieuwe stap toevoegen aan logische app](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Voer in het zoekvak `sap` als uw filter in. Selecteer in de lijst **acties** de optie **schema's genereren**.
  
   ![Actie ' schema's genereren ' toevoegen aan logische app](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   U kunt ook het tabblad **onderneming** selecteren en de SAP-actie selecteren.

   ![SAP-verzend actie selecteren op het tabblad onderneming](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Als uw verbinding al bestaat, gaat u door met de volgende stap, zodat u uw SAP-actie kunt instellen. Als u echter om de verbindings gegevens wordt gevraagd, geeft u de informatie op zodat u nu een verbinding met uw on-premises SAP-server kunt maken.

   1. Geef een naam op voor de verbinding.

   1. Selecteer in de sectie **gegevens gateway** , onder **abonnement**, eerst het Azure-abonnement voor de gegevens gateway resource die u hebt gemaakt in de Azure portal voor uw data Gateway-installatie. 
   
   1. Onder **verbindings gateway** selecteert u uw gegevens gateway resource in Azure.

   1. Ga door met het verstrekken van informatie over de verbinding. Volg voor de eigenschap **aanmeldings type** de stap op basis van het feit of de eigenschap is ingesteld op **toepassings server** of **groep**:
   
      * Voor **toepassings server** zijn deze eigenschappen, die meestal optioneel worden weer gegeven, vereist:

        ![Een SAP-toepassings server verbinding maken](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Voor **groep** zijn deze eigenschappen, die meestal optioneel worden weer gegeven, vereist:

        ![SAP Message server-verbinding maken](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Standaard wordt met sterk typen gecontroleerd op ongeldige waarden door XML-validatie uit te voeren op basis van het schema. Dit gedrag kan u helpen bij het detecteren van eerder gemaakte problemen. De optie voor **veilig typen** is beschikbaar voor achterwaartse compatibiliteit en controleert alleen de lengte van de teken reeks. Meer informatie over de [optie voor veilig typen](#safe-typing).

   1. Wanneer u klaar bent, selecteert u **Maken**.

      Logic Apps stelt uw verbinding in en test deze om te controleren of de verbinding goed werkt.

1. Geef het pad op naar het artefact waarvoor u het schema wilt genereren.

   U kunt de SAP-actie selecteren via de bestands kiezer:

   ![SAP-actie selecteren](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   U kunt de actie ook hand matig invoeren:

   ![SAP-actie hand matig invoeren](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Als u schema's wilt genereren voor meer dan één artefact, geeft u de SAP-actie Details voor elk artefact op, bijvoorbeeld:

   ![Selecteer Nieuw item toevoegen](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Twee items weer geven](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Zie [bericht schema's voor IDOC-bewerkingen](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)voor meer informatie over de SAP-actie.

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

### <a name="test-your-logic-app"></a>Uw logische app testen

1. Selecteer op de werk balk van de ontwerp functie **uitvoeren** om een uitvoering voor uw logische app te activeren.

1. Open de uitvoering en controleer de uitvoer voor de actie **Schema's genereren** .

   In de uitvoer worden de gegenereerde schema's voor de opgegeven lijst met berichten weer gegeven.

### <a name="upload-schemas-to-an-integration-account"></a>Schema's uploaden naar een integratie account

U kunt de gegenereerde schema's ook downloaden of opslaan in opslag plaatsen, zoals een blob-, opslag-of integratie account. Integratie accounts bieden een eersteklas ervaring met andere XML-acties, dus in dit voor beeld ziet u hoe u schema's uploadt naar een integratie account voor dezelfde logische app met behulp van de Azure Resource Manager-connector.

1. Selecteer in de Logic Apps Designer, onder de trigger, de optie **nieuwe stap**.

1. Voer in het zoekvak `Resource Manager` als uw filter in. Selecteer **een resource maken of bijwerken**.

   ![Azure Resource Manager actie selecteren](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Voer de Details voor de actie in, met inbegrip van uw Azure-abonnement, Azure-resource groep en integratie account. Om SAP-tokens toe te voegen aan de velden, klikt u in de vakken voor die velden en selecteert u in de lijst met dynamische inhoud die wordt weer gegeven.

   1. Open de lijst **nieuwe para meter toevoegen** en selecteer de velden **locatie** en **Eigenschappen** .

   1. Geef details op voor deze nieuwe velden, zoals wordt weer gegeven in dit voor beeld.

      ![Details invoeren voor Azure Resource Manager actie](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   Met de SAP-actie **Schema's genereren** worden schema's gegenereerd als een verzameling, zodat de ontwerp functie automatisch een **voor elke** lus toevoegt aan de actie. Hier volgt een voor beeld waarin wordt getoond hoe deze actie wordt weer gegeven:

   ![Azure Resource Manager actie met ' voor elke ' lus](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > De schema's maken gebruik van base64-gecodeerde indeling. Als u de schema's naar een integratie account wilt uploaden, moeten ze worden gedecodeerd met behulp van de `base64ToString()` functie. Hier volgt een voor beeld waarin de code voor het element wordt weer gegeven `"properties"` :
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

### <a name="test-your-logic-app"></a>Uw logische app testen

1. Selecteer op de werk balk van de ontwerp functie **uitvoeren** om de logische app hand matig te activeren.

1. Als de uitvoering is geslaagd, gaat u naar het integratie account en controleert u of de gegenereerde schema's bestaan.

## <a name="enable-secure-network-communications"></a>Veilige netwerk communicatie inschakelen

Voordat u begint, moet u ervoor zorgen dat u voldoet aan de eerder vermelde [vereisten](#prerequisites), die alleen van toepassing zijn wanneer u de gegevens gateway en Logic apps uitvoert in een multi tenant-Azure:

* Zorg ervoor dat de on-premises gegevens gateway is geïnstalleerd op een computer die zich in hetzelfde netwerk bevindt als uw SAP-systeem.

* Voor eenmalige aanmelding wordt de gegevens gateway uitgevoerd als een gebruiker die is toegewezen aan een SAP-gebruiker.

* De SNC-bibliotheek die de aanvullende beveiligings functies biedt, wordt geïnstalleerd op dezelfde computer als de gegevens gateway. Enkele voor beelden zijn [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos en NTLM.

   Als u SNC voor uw aanvragen naar of van het SAP-systeem wilt inschakelen, schakelt u het selectie vakje **SNC gebruiken** in de SAP-verbinding in en geeft u de volgende eigenschappen op:

   ![SAP-SNC configureren in verbinding](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Eigenschap | Beschrijving |
   |----------| ------------|
   | **Pad naar SNC-bibliotheek** | De naam of het pad van de SNC-bibliotheek ten opzichte van de NCo-installatie locatie of het absolute pad. Voor beelden zijn `sapsnc.dll` of `.\security\sapsnc.dll` of `c:\security\sapsnc.dll` . |
   | **SNC SSO** | Wanneer u verbinding maakt via SNC, wordt de SNC-identiteit meestal gebruikt voor het verifiëren van de oproepende functie. Een andere optie is om te overschrijven zodat gebruikers-en wachtwoord gegevens kunnen worden gebruikt voor het verifiëren van de oproepende functie, maar de regel wordt nog steeds versleuteld. |
   | **Mijn naam SNC** | In de meeste gevallen kan deze eigenschap worden wegge laten. De geïnstalleerde SNC-oplossing kent meestal een eigen SNC-naam. Alleen voor oplossingen die ondersteuning bieden voor meerdere identiteiten, moet u mogelijk de identiteit opgeven die moet worden gebruikt voor deze specifieke bestemming of server. |
   | **Naam van SNC-partner** | De naam voor de back-end-SNC. |
   | **SNC-kwaliteit van beveiliging** | De Quality of service die moet worden gebruikt voor SNC-communicatie van deze specifieke bestemming of server. De standaard waarde wordt gedefinieerd door het back-end-systeem. De maximum waarde wordt gedefinieerd door het beveiligings product dat voor SNC wordt gebruikt. |
   |||

   > [!NOTE]
   > Stel de omgevings variabelen SNC_LIB en SNC_LIB_64 niet in op de computer waar u de gegevens gateway en de SNC-bibliotheek hebt. Als deze instelling is ingesteld, hebben ze voor rang op de waarde van de SNC-bibliotheek die via de connector is door gegeven.

## <a name="safe-typing"></a>Veilig typen

Wanneer u uw SAP-verbinding maakt, wordt standaard een sterk type gebruikt om te controleren op ongeldige waarden door XML-validatie uit te voeren op basis van het schema. Dit gedrag kan u helpen bij het detecteren van eerder gemaakte problemen. De optie voor **veilig typen** is beschikbaar voor achterwaartse compatibiliteit en controleert alleen de lengte van de teken reeks. Als u kiest voor **veilig typen**, worden het type DATS en het type TIM'S in SAP behandeld als teken reeksen in plaats van als XML-equivalenten, `xs:date` en `xs:time` , waar `xmlns:xs="http://www.w3.org/2001/XMLSchema"` . Een veilige type is van invloed op het gedrag voor het genereren van het schema, het bericht verzenden voor de payload ' verzonden ' en het antwoord ' is ontvangen ', en de trigger. 

Wanneer sterk type wordt gebruikt (**veilig typen** is niet ingeschakeld), wijst het schema de DATS-en Tim's-typen toe aan meer eenvoudige XML-typen:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Wanneer u berichten verzendt met een sterk type, voldoet het DATS-en TIM'S-antwoord aan de overeenkomende XML-type-indeling:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Wanneer **veilig typen** is ingeschakeld, wijst het schema de typen DATS en Tim's toe aan XML-teken reeks velden met alleen lengte beperkingen, bijvoorbeeld:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

Wanneer berichten worden verzonden met een **veilig type** ingeschakeld, ziet het DATS-en Tim's-antwoord er als volgt uit:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Geavanceerde scenario's

### <a name="change-language-headers"></a>Taal headers wijzigen

Wanneer u verbinding maakt met SAP vanuit Logic Apps, is de standaard taal voor de verbinding Engels. U kunt de taal voor uw verbinding instellen met behulp van [de standaard `Accept-Language` -http-header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4) met uw inkomende aanvragen.

> [!TIP]
> De meeste webbrowsers voegen een `Accept-Language` koptekst toe op basis van de instellingen van de gebruiker. De webbrowser past deze header toe wanneer u een nieuwe SAP-verbinding maakt in de Logic Apps Designer. Als u geen SAP-verbindingen in de voorkeurs taal van uw webbrowser wilt maken, moet u de instellingen van uw webbrowser bijwerken om uw voorkeurs taal te gebruiken of uw SAP-verbinding maken met behulp van Azure Resource Manager in plaats van de Logic Apps Designer. 

U kunt bijvoorbeeld een aanvraag met de `Accept-Language` koptekst naar uw logische app verzenden met behulp van de **HTTP-aanvraag** trigger. Alle acties in uw logische app ontvangen de header. SAP gebruikt vervolgens de opgegeven talen in de bijbehorende systeem berichten, zoals BAPI-fout berichten.

De SAP-verbindings parameters voor een logische app hebben geen taal eigenschap. Als u de `Accept-Language` header gebruikt, wordt mogelijk de volgende fout weer geven: **Controleer de account gegevens en/of de machtigingen en probeer het opnieuw.** In dit geval controleert u in plaats daarvan de fout logboeken van het SAP-onderdeel. De fout treedt eigenlijk op in het SAP-onderdeel dat gebruikmaakt van de-header, waardoor u mogelijk een van deze fout berichten krijgt:

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`
* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### <a name="confirm-transaction-explicitly"></a>Trans actie expliciet bevestigen

Wanneer u trans acties verzendt naar SAP vanuit Logic Apps, vindt deze uitwisseling plaats in twee stappen zoals beschreven in het SAP-document, [transactionele RFC server-Program ma's](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). De actie **verzenden naar SAP** verwerkt standaard zowel de stappen voor de functie overdracht als voor de transactie bevestiging in één aanroep. De SAP-connector biedt u de mogelijkheid om deze stappen uit te voeren. U kunt een IDoc verzenden en in plaats van de trans actie automatisch te bevestigen, kunt u de actie voor de expliciete **trans actie-id bevestigen** gebruiken.

Deze mogelijkheid om de trans actie-ID-bevestiging te ontkoppelen is handig wanneer u trans acties niet wilt dupliceren in SAP, bijvoorbeeld in scenario's waarin storingen optreden vanwege oorzaken van problemen met het netwerk. Door de trans actie-ID afzonderlijk te bevestigen, wordt de trans actie slechts één keer in uw SAP-systeem uitgevoerd.

Hier volgt een voor beeld waarin dit patroon wordt weer gegeven:

1. Maak een lege logische app en voeg een HTTP-trigger toe.

1. Voeg de actie **IDOC verzenden** toe vanuit de SAP-connector. Geef de details op voor de IDoc die u naar uw SAP-systeem verzendt.

1. Als u de trans actie-ID expliciet wilt bevestigen in een afzonderlijke stap, selecteert u **Nee** in het veld **TID bevestigen** . Voor het optionele **trans actie-ID-GUID** veld kunt u de waarde hand matig opgeven of de connector automatisch genereren en deze GUID retour neren in de reactie van de actie IDOC verzenden.

   ![Eigenschappen van IDOC-actie verzenden](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Als u de trans actie-ID expliciet wilt bevestigen, voegt u de actie **trans actie-id bevestigen** toe en zorgt u ervoor dat u [geen dubbele IDocs naar SAP verzendt](#avoid-sending-duplicate-idocs). Klik in het vak **trans actie-id** zodat de lijst met dynamische inhoud wordt weer gegeven. Selecteer in de lijst de waarde voor de **trans actie-id** die wordt geretourneerd door de actie **IDOC verzenden** .

   ![Bewerking trans actie-ID bevestigen](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Nadat deze stap is uitgevoerd, wordt de huidige trans actie als voltooid aan beide uiteinden, aan de zijde van de SAP-connector en aan SAP-systeem zijde gemarkeerd.

#### <a name="avoid-sending-duplicate-idocs"></a>Vermijd het verzenden van dubbele IDocs

Als u een probleem ondervindt met dubbele IDocs die worden verzonden naar SAP vanuit uw logische app, voert u de volgende stappen uit om een teken reeks variabele te maken die als uw IDoc-trans actie-id fungeert. Het maken van deze trans actie-id helpt dubbele netwerk verzendingen te voor komen wanneer er problemen optreden, zoals tijdelijke storingen, netwerk problemen of verloren bevestigingen.

> [!NOTE]
> SAP-systemen verg eten een trans actie-id na een opgegeven periode of 24 uur standaard. Als gevolg hiervan kan SAP nooit een trans actie-id bevestigen als de ID of de GUID onbekend is.
> Als de bevestiging voor een trans actie-id mislukt, wordt met deze fout aangegeven dat communicatie met het SAP-systeem is mislukt voordat SAP de bevestiging kon bevestigen.

1. Voeg in de ontwerp functie voor Logic Apps de bewerking actie **variabele initialiseren** toe aan uw logische app. 

1. Configureer de volgende instellingen in de editor voor de actie **variabele initialiseren**. Sla vervolgens uw wijzigingen op.

    1. Voer bij **naam** een naam in voor de variabele. Bijvoorbeeld `IDOCtransferID`.

    1. Selecteer bij **type** de optie **teken reeks** als het type variabele.

    1. Voor **waarde**, selecteert u in het tekstvak **begin waarde invoeren** om het menu dynamische inhoud te openen. Selecteer het tabblad **expressies** . Voer de functie in de lijst met functies in `guid()` . Selecteer **OK** om uw wijzigingen op te slaan. Het veld **waarde** is nu ingesteld op de `guid()` functie, waardoor een GUID wordt gegenereerd.

1. Nadat u de variabele actie hebt **geïnitialiseerd** , voegt u de actie **IDOC verzenden** toe.

1. Configureer de volgende instellingen in de editor voor de actie **IDOC verzenden**. Sla vervolgens uw wijzigingen op.

    1. Voor het **type IDOC** selecteert u uw bericht type en geeft u in het **bericht invoer IDOC** het bericht op.

    1. Voor **SAP release versie** selecteert u de waarden van uw SAP-configuratie.

    1. Selecteer de waarden van uw SAP-configuratie voor de versie van de **record typen**.

    1. Selecteer **Nee** bij **TID bevestigen**.

    1. Selecteer **nieuwe para meter toevoegen**  >  **trans actie-ID-GUID**. Selecteer het tekstvak om het menu met dynamische inhoud te openen. Selecteer op het tabblad **variabelen** de naam van de variabele die u hebt gemaakt. Bijvoorbeeld `IDOCtransferID`.

1. Selecteer op de titel balk van de actie **IDOC verzenden** **...**  >  **Instellingen**. Voor het **beleid voor opnieuw proberen** wordt het aanbevolen om **standaard** &gt; **gereed** te selecteren. U kunt in plaats daarvan echter een aangepast beleid configureren voor uw specifieke behoeften. Voor aangepast beleid wordt het aanbevolen om ten minste één nieuwe poging te configureren voor het oplossen van tijdelijke netwerk storingen.

1. Nadat de actie **IDOC heeft verzonden**, moet u de actie **-id bevestigen**.

1. Configureer de volgende instellingen in de editor voor de actie **trans actie-id bevestigen**. Sla vervolgens uw wijzigingen op.

    1. Voer voor **trans actie-id** de naam van de variabele opnieuw in. Bijvoorbeeld `IDOCtransferID`.

1. U kunt eventueel ook de ontdubbeling valideren in uw test omgeving. Herhaal de actie **IDOC verzenden** met dezelfde **trans actie-id-** GUID die u in de vorige stap hebt gebruikt. Wanneer u dezelfde IDoc twee keer verzendt, kunt u valideren dat SAP de duplicatie van de tRFC-aanroep kan identificeren en de twee aanroepen kan omzetten naar één binnenkomend IDoc-bericht.

## <a name="known-issues-and-limitations"></a>Bekende problemen en beperkingen

Dit zijn de bekende problemen en beperkingen voor de beheerde SAP-connector (niet ISE):

* De SAP-trigger biedt geen ondersteuning voor gegevens gateway clusters. In sommige failover-gevallen kan het knoop punt van de gegevens gateway dat met het SAP-systeem communiceert, afwijken van het actieve knoop punt, wat leidt tot onverwacht gedrag. Voor scenario's voor verzenden worden gegevens gateway clusters ondersteund.

* SAP-router teken reeksen worden momenteel niet ondersteund door de SAP-connector. De on-premises gegevens gateway moet bestaan op hetzelfde LAN als het SAP-systeem dat u wilt verbinden.

## <a name="connector-reference"></a>Connector-verwijzing

Voor meer technische informatie over deze connector, zoals triggers, acties en limieten, zoals beschreven in het Swagger-bestand van de connector, raadpleegt u de [referentie pagina van de connector](/connectors/sap/). Aanvullende documentatie voor Logic Apps wordt gegeven voor de volgende acties:

* [BAPI aanroepen](#call-bapi-action)

* [IDOC verzenden](#send-idoc-action)

> [!NOTE]
> Voor Logic apps in een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), maakt de ISE-versie van deze connector gebruik van de [ISE-bericht limieten](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) in plaats daarvan.

### <a name="call-bapi-action"></a>BAPI-actie aanroepen

De actie [BAPI aanroepen ( `CallBapi` )](
https://docs.microsoft.com/connectors/sap/#call-bapi-(preview)) roept de BAPI-methode op uw SAP-server aan. 

U moet de volgende para meters met uw gesprek gebruiken: 

* **Zakelijk object** ( `businessObject` ), een vervolg keuzemenu dat kan worden doorzocht.

* **Methode** ( `method` ), waarmee de beschik bare methoden worden gevuld nadat u een **zakelijk object** hebt geselecteerd. De beschik bare methoden variëren, afhankelijk van het geselecteerde **zakelijk object**.

* De **invoer van BAPI-para meters** ( `body` ), waarin u het XML-document aanroept dat de invoer parameter waarden van de BAPI-methode voor de aanroep bevat, of de URI van de opslag-blob die uw BAPI-para meters bevat.

Zie voor meer informatie over het gebruik van de actie BAPI aanroepen de [XML-voor beelden van BAPI-aanvragen](#xml-samples-for-bapi-requests).

> [!TIP]
> Als u de Logic Apps Designer gebruikt om uw BAPI-aanvraag te bewerken, kunt u de volgende zoek functies gebruiken: 
> 
> * Selecteer een object in de ontwerp functie om een vervolg keuzelijst met beschik bare methoden weer te geven.
> * Zakelijke object typen filteren op tref woord met behulp van de Doorzoek bare lijst van de BAPI API-aanroep.

### <a name="send-idoc-action"></a>IDoc-actie verzenden

Met de actie [IDOC verzenden ( `SendIDoc` )](/connectors/sap/) wordt het IDOC-bericht naar uw SAP-server verzonden.

U moet de volgende para meters met uw gesprek gebruiken: 

* **IDOC type met optionele extensie** ( `idocType` ), een vervolg keuzelijst die kan worden doorzocht.

    * De optionele para meter **SAP release versie** ( `releaseVersion` ) vult waarden in nadat u het type IDOC hebt geselecteerd en is afhankelijk van het geselecteerde type IDOC.

* **Invoer IDOC-bericht** ( `body` ), waarin u het XML-document met de IDOC-Payload aanroept, of de URI van de opslag-blob die uw IDOC XML-document bevat. Dit document moet voldoen aan het SAP IDOC XML-schema volgens de documentatie van WE60 IDoc of het gegenereerde schema voor de bijbehorende URI voor de SAP IDoc-actie.

Zie het [overzicht voor het verzenden van IDOC-berichten naar uw SAP-server](#send-idoc-messages-to-sap-server)voor gedetailleerde voor beelden van het gebruik van de actie IDOC verzenden.

 `confirmTid` Zie de [procedure voor het expliciet bevestigen van de trans actie](#confirm-transaction-explicitly)voor informatie over het gebruik van een optionele para meter confirm TID ().

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met on-premises systemen](../logic-apps/logic-apps-gateway-connection.md) vanuit Azure Logic apps.

* Meer informatie over het valideren, transformeren en gebruiken van andere bericht bewerkingen met de [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md).