---
title: Azure Firewall Premium-preview implementeren en configureren
description: Meer informatie over het implementeren en configureren van Azure Firewall Premium.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: fa106fac683619706f4be330ad1c4bff7b56f2dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721783"
---
# <a name="deploy-and-configure-azure-firewall-premium-preview"></a>Azure Firewall Premium-preview implementeren en configureren

> [!IMPORTANT]
> Azure Firewall Premium is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

 Azure Firewall Premium preview is een firewall van de volgende generatie met mogelijkheden die vereist zijn voor zeer gevoelige en gereglementeerde omgevingen. Het bevat de volgende functies:

- **TLS-inspectie** -Hiermee wordt het uitgaande verkeer gedecodeerd, worden de gegevens verwerkt, worden de gegevens versleuteld en verzonden naar de bestemming.
- **Id** : een netwerk aanval en preventie systeem (id) biedt u de mogelijkheid om netwerk activiteiten te bewaken voor schadelijke activiteiten, informatie over deze activiteit te registreren, deze te rapporteren en optioneel te proberen om deze te blok keren.
- **URL-filtering** : Hiermee wordt de FQDN-filter functie van Azure firewall uitgebreid om een volledige URL te bekijken. Bijvoorbeeld `www.contoso.com/a/c` in plaats van `www.contoso.com` .
- **Webcategorieën** : beheerders kunnen gebruikers toegang tot website categorieën toestaan of weigeren, zoals gokken websites, websites voor sociale media en anderen.

Zie [Azure Firewall Premium-functies](premium-features.md)voor meer informatie.

U gebruikt een sjabloon voor het implementeren van een test omgeving met een centraal VNet (10.0.0.0/16) met drie subnetten:
- een worker-subnet (10.0.10.0/24)
- een Azure Bastion-subnet (10.0.20.0/24)
- een firewall-subnet (10.0.100.0/24)

In deze test omgeving wordt één centraal VNet gebruikt voor eenvoud. Voor productie doeleinden is een [hub-en spoke-topologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) met een peered VNets meer gangbaar.

:::image type="content" source="media/premium-deploy/premium-topology.png" alt-text="Centrale VNet-topologie":::

De virtuele machine van de werk nemer is een client die HTTP/S-aanvragen via de firewall verzendt.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="deploy-the-infrastructure"></a>De infrastructuur implementeren

De sjabloon implementeert een volledige test omgeving voor Azure Firewall Premium ingeschakeld met id, TLS-inspectie, URL-filtering en webcategorieën:

- een nieuw Azure Firewall Premium-en firewall-beleid met vooraf gedefinieerde instellingen voor eenvoudige validatie van de kern mogelijkheden (id, TLS-inspectie, URL-filtering en webcategorieën)
- Hiermee worden alle afhankelijkheden, waaronder Key Vault en een beheerde identiteit, geïmplementeerd. In een productie omgeving zijn deze resources mogelijk al gemaakt en niet nodig in dezelfde sjabloon.
- genereert een zelf-ondertekende basis certificerings instantie en implementeert deze op de gegenereerde Key Vault
-  genereert een afgeleide tussenliggende CA en implementeert deze op een virtuele test machine van Windows (WorkerVM)
- Er wordt ook een bastion-host (BastionHost) geïmplementeerd en deze kan worden gebruikt om verbinding te maken met de Windows-test machine (WorkerVM)



[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-premium%2Fazuredeploy.json)

## <a name="test-the-firewall"></a>De firewall testen

Nu kunt u id, TLS-inspectie, webfiltering en web-categorieën testen.

### <a name="add-firewall-diagnostics-settings"></a>Diagnostische instellingen voor Firewall toevoegen

Als u Firewall logboeken wilt verzamelen, moet u Diagnostische instellingen toevoegen om firewall logboeken te verzamelen.

1. Selecteer de **DemoFirewall** en selecteer vervolgens **Diagnostische instellingen** onder **bewaking**.
2. Selecteer **Diagnostische instellingen toevoegen**.
3. Typ *FW-diag* voor naam van de **Diagnostische instelling**.
4. Onder **logboek** selecteert u **AzureFirewallApplicationRule** en **AzureFirewallNetworkRule**.
5. Selecteer onder **doel gegevens** **verzenden naar log Analytics werk ruimte**.
6. Selecteer **Opslaan**.

### <a name="idps-tests"></a>ID-tests

Als u id wilt testen, moet u uw eigen interne webserver implementeren met een geschikt server certificaat. Zie [Azure Firewall Premium preview-certificaten](premium-certificates.md)voor meer informatie over de vereisten voor het Azure Firewall Premium preview-certificaat.

U kunt gebruiken `curl` om verschillende HTTP-headers te beheren en schadelijk verkeer te simuleren.

#### <a name="to-test-idps-for-http-traffic"></a>ID voor HTTP-verkeer testen:

1. Open een opdracht prompt venster op de virtuele WorkerVM-machine.
2. Typ de volgende opdracht achter de opdracht prompt:

   `curl -A "BlackSun" <your web server address>`
3. U ziet uw webserver reactie.
4. Ga naar de regel logboeken firewall-netwerk op de Azure Portal om een waarschuwing te vinden die vergelijkbaar is met het volgende bericht:

   :::image type="content" source="media/premium-deploy/alert-message.png" alt-text="Waarschuwings bericht":::

   > [!NOTE]
   > Het kan enige tijd duren voordat de gegevens in de logboeken worden weer gegeven. Geef deze mini maal 20 minuten de tijd om te zorgen dat de logboeken de gegevens gaan weer geven.
5. Voeg een handtekening regel toe voor hand tekening 2008983:

   1. Selecteer de **DemoFirewallPolicy** en selecteer  onder instellingen **id (preview-versie)**.
   1. Selecteer het tabblad **handtekening regels** .
   1. Typ bij **hand tekening-id** in het vak Openen de tekst *2008983*.
   1. Onder **modus** selecteert u **weigeren**.
   1. Selecteer **Opslaan**.
   1. Wacht tot de implementatie is voltooid voordat u doorgaat.



6. Voer de opdracht opnieuw uit op WorkerVM `curl` :

   `curl -A "BlackSun" <your web server address>`

   Omdat de HTTP-aanvraag nu wordt geblokkeerd door de firewall, ziet u de volgende uitvoer nadat de time-out voor de verbinding is verlopen:

   `read tcp 10.0.100.5:55734->10.0.20.10:80: read: connection reset by peer`

7. Ga naar de controle Logboeken in de Azure Portal en zoek het bericht voor de geblokkeerde aanvraag.
<!---8. Now you can bypass the IDPS function using the **Bypass list**.

   1. On the **IDPS (preview)** page, select the **Bypass list** tab.
   2. Edit **MyRule** and set **Destination** to *10.0.20.10, which is the ServerVM private IP address.
   3. Select **Save**.
1. Run the test again: `curl -A "BlackSun" http://server.2020-private-preview.com` and now you should get the `Hello World` response and no log alert. --->

#### <a name="to-test-idps-for-https-traffic"></a>ID voor HTTPS-verkeer testen

Herhaal deze krul proeven met behulp van HTTPS in plaats van HTTP. Bijvoorbeeld:

`curl --ssl-no-revoke -A "BlackSun" <your web server address>`

U moet dezelfde resultaten zien als bij de HTTP-tests.

### <a name="tls-inspection-with-url-filtering"></a>TLS-inspectie met URL-filtering

Gebruik de volgende stappen om de TLS-inspectie met URL-filtering te testen.

1. Bewerk de regels van de toepassing firewall beleid en voeg een nieuwe regel `AllowURL` met de naam toe aan de `AllowWeb` regel verzameling. Configureer de doel `www.nytimes.com/section/world` -URL, IP-adres van bron **\* *_, doel type _* URL (preview)**, selecteer **TLS-inspectie (preview)** en protocollen **http, https**.

3. Wanneer de implementatie is voltooid, opent u een browser op WorkerVM en gaat u naar en controleert u of `https://www.nytimes.com/section/world` het HTML-antwoord op de verwachte manier wordt weer gegeven in de browser.
4. In de Azure Portal kunt u de volledige URL weer geven in de toepassings regel bewakings logboeken:

      :::image type="content" source="media/premium-deploy/alert-message-url.png" alt-text="Waarschuwings bericht met de URL":::

Sommige HTML-pagina's kunnen er onvolledig uitzien omdat ze verwijzen naar andere Url's die worden geweigerd. Om dit probleem op te lossen, kan de volgende aanpak worden uitgevoerd:

- Als de HTML-pagina koppelingen naar andere domeinen bevat, kunt u deze domeinen toevoegen aan een nieuwe toepassings regel die toegang tot deze FQDN-namen toestaat.
- Als de HTML-pagina koppelingen naar suburl's bevat, kunt u de regel wijzigen en een sterretje toevoegen aan de URL. Bijvoorbeeld: `targetURLs=www.nytimes.com/section/world*`

   U kunt ook een nieuwe URL toevoegen aan de regel. Bijvoorbeeld: 

   `www.nytimes.com/section/world, www.nytimes.com/section/world/*`

### <a name="web-categories-testing"></a>Testen van webcategorieën

We gaan een toepassings regel maken om toegang tot sport websites toe te staan.
1. Open de resource groep vanuit de portal en selecteer **DemoFirewallPolicy**.
2. Selecteer **toepassings regels** en voeg vervolgens **een regel verzameling toe**.
3. Voor **naam** typt u *GeneralWeb*, **Priority** *103* en **regel verzamelings groep** selecteert u **DefaultApplicationRuleCollectionGroup**.
4. Onder **regels** voor **naam** typt u *AllowSports*, **bron** *\** , **protocol** *http, https*, **TLS-inspectie** selecteren, **doel type** *webcategorieën selecteren (preview-versie)*, **doel** selecteren *sport*.
5. Selecteer **Toevoegen**.

      :::image type="content" source="media/premium-deploy/web-categories.png" alt-text="Sport webcategorie":::
6. Wanneer de implementatie is voltooid, gaat u naar  **WorkerVM** en opent u een webbrowser en bladert u naar `https://www.nfl.com` .

   De webpagina NFL wordt weer gegeven en in het logboek voor toepassings regels wordt aangegeven dat een **webcategorie: sport** regel is gevonden en de aanvraag is toegestaan.

## <a name="next-steps"></a>Volgende stappen

- [Azure Firewall Premium preview in het Azure Portal](premium-portal.md)