---
title: 'ExpressRoute: aangepaste waarschuwingen configureren voor aangekondigde routes'
description: In dit artikel wordt beschreven hoe u Azure Automation en Logic Apps kunt gebruiken om het aantal routes te bewaken dat is geadverteerd van de ExpressRoute-gateway naar on-premises netwerken om te voor komen dat de limieten voor de 1000-routes worden bereikt.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 05/29/2020
ms.author: duau
ms.openlocfilehash: 2291d1fa7f890296c59661060f5a823d8eb194ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654387"
---
# <a name="configure-custom-alerts-to-monitor-advertised-routes"></a>Aangepaste waarschuwingen configureren voor het bewaken van aangekondigde routes

Dit artikel helpt u bij het gebruik van Azure Automation en Logic Apps om voortdurend het aantal routes te bewaken dat is geadverteerd van de ExpressRoute-gateway naar on-premises netwerken. Bewaking kan helpen voor komen dat de limiet van 1000 routes worden bereikt] (expressroute-faq's. MD # hoe-vele-voor voegsels-kunnen worden aangekondigd-van-a-vnet-naar-on-premises-on-expressroute-private-peering).

Met **Azure Automation** kunt u de uitvoering van een aangepast Power shell-script automatiseren dat is opgeslagen in een *runbook*. Wanneer u de configuratie in dit artikel gebruikt, bevat het runbook een Power shell-script dat een of meer ExpressRoute-gateway query's doorzoekt. Er wordt een gegevensset verzameld met de resource groep, de ExpressRoute-gateway naam en het aantal netwerk voorvoegsels die on-premises zijn geadverteerd.

**Azure Logic apps** plant een aangepaste werk stroom die het Azure Automation runbook aanroept. De uitvoering van het runbook wordt uitgevoerd met behulp van een taak. Nadat het verzamelen van gegevens is uitgevoerd, worden de gegevens door de werk stroom Azure Logic Apps geclassificeerd en, op basis van match criteria voor het aantal netwerk voorvoegsels boven of onder een vooraf gedefinieerde drempel waarde, verzendt informatie naar een e-mail adres van bestemming.

### <a name="workflow"></a><a name="workflow"></a>Werkstroom

Het instellen van een aangepaste waarschuwing is gebaseerd op drie hoofd stappen:

1. Een Automation-account maken met een ' uitvoeren als-account en machtigingen.

2. Runbooks maken en configureren.

3. Maak een logische app waarmee het Automation-account wordt geactiveerd en een e-mail bericht met waarschuwingen wordt verzonden als het aantal groter is dan een drempel waarde (bijvoorbeeld 160).

## <a name="before-you-begin"></a><a name="before"></a>Voordat u begint

Controleer voordat u met de configuratie begint of u aan de volgende criteria hebt voldaan:

* Uw implementatie bevat ten minste één ExpressRoute-gateway.

* U hebt een basis memorandum van [Run as-accounts](../automation/manage-runas-account.md) in azure Automation.

* U bent bekend met [Azure Logic apps](../logic-apps/logic-apps-overview.md).

* U bent bekend met het gebruik van Azure PowerShell. Azure PowerShell is vereist voor het verzamelen van de netwerk voorvoegsels in de ExpressRoute-gateway. Zie de [Azure PowerShell documentatie](/powershell/azure/)voor meer informatie over Azure PowerShell in het algemeen.

### <a name="notes-and-limitations"></a><a name="limitations"></a>Opmerkingen en beperkingen

* De aangepaste waarschuwing die in dit artikel wordt besproken, is een invoeg toepassing voor een betere werking en controle. Het is geen vervanging voor de systeem eigen waarschuwingen in ExpressRoute.
* Het verzamelen van gegevens voor ExpressRoute-gateways wordt op de achtergrond uitgevoerd. Runtime kan langer duren dan verwacht. Het terugkeer patroon van de werk stroom moet correct zijn ingesteld om taak Queuing te voor komen.
* Implementaties op basis van scripts of ARM-sjablonen kunnen sneller plaatsvinden dan de aangepaste alarm trigger. Dit kan leiden tot een toename van het aantal netwerk voorvoegsels in de ExpressRoute-gateway boven de limiet van 1000 routes.

## <a name="create-and-configure-accounts"></a><a name="accounts"></a>Accounts maken en configureren

Wanneer u in de Azure Portal een Automation-account maakt, wordt automatisch een [uitvoeren als](../automation/automation-security-overview.md#run-as-accounts) -account gemaakt. Dit account voert de volgende acties uit:

* Hiermee maakt u een Azure Active Directory-toepassing (Azure AD) met een zelfondertekend certificaat. Het uitvoeren als-account zelf bevat een certificaat dat elk jaar standaard moet worden vernieuwd.

* Hiermee maakt u een Service-Principal-account voor de toepassing in azure AD.

* Wijst zichzelf de rol Inzender (Azure RBAC) toe aan het Azure-abonnement dat wordt gebruikt. Deze rol beheert Azure Resource Manager resources met runbooks.

U hebt bevoegdheden en machtigingen nodig om een Automation-account te maken. Zie [machtigingen die zijn vereist voor het maken van een Automation-account](../automation/automation-create-standalone-account.md#permissions-required-to-create-an-automation-account)voor meer informatie.

### <a name="1-create-an-automation-account"></a><a name="about"></a>1. een Automation-account maken

Een Automation-account maken met de machtiging uitvoeren als. Zie [een Azure Automation-account maken](../automation/automation-quickstart-create-account.md)voor instructies.

:::image type="content" source="./media/custom-route-alert-portal/create-account.png" alt-text="Automation-account toevoegen" lightbox="./media/custom-route-alert-portal/create-account-expand.png":::

### <a name="2-assign-the-run-as-account-a-role"></a><a name="about"></a>2. Wijs het run as-account toe aan een rol

Standaard wordt de rol **Inzender** toegewezen aan de service-principal die wordt gebruikt door het **Run as** -account. U kunt de standaard rol laten toewijzen aan de Service-Principal, of u kunt machtigingen beperken door een [ingebouwde rol](../role-based-access-control/built-in-roles.md) (bijvoorbeeld lezer) of een [aangepaste rol](../active-directory/roles/custom-create.md)toe te wijzen.

 Gebruik de volgende stappen om te bepalen welke rol u wilt toewijzen aan de service-principal die wordt gebruikt door uw uitvoeren als-account:

1. Navigeer naar uw Automation-account. Navigeer naar **account instellingen** en selecteer vervolgens **uitvoeren als-accounts**.

2. Selecteer **rollen** om de roldefinitie weer te geven die wordt gebruikt.

   :::image type="content" source="./media/custom-route-alert-portal/run-as-account-permissions.png" alt-text="Rol toewijzen":::

## <a name="create-and-configure-runbooks"></a><a name="runbooks"></a>Runbooks maken en configureren

### <a name="1-install-modules"></a><a name="install-modules"></a>1. modules installeren

Als u Power shell-cmdlets wilt uitvoeren in Azure Automation runbooks, moet u enkele extra Azure PowerShell AZ-modules installeren. Gebruik de volgende stappen om de modules te installeren:

1. Open uw Azure Automation-account en navigeer naar **modules**.

   :::image type="content" source="./media/custom-route-alert-portal/navigate-modules.png" alt-text="Navigeren naar modules":::

2. Zoek in de galerie en importeer de volgende modules: **AZ. accounts**, **AZ. Network**, **AZ. Automation** en **AZ. profile**.

   :::image type="content" source="./media/custom-route-alert-portal/import-modules.png" alt-text="Modules zoeken en importeren" lightbox="./media/custom-route-alert-portal/import-modules-expand.png":::
  
### <a name="2-create-a-runbook"></a><a name="create"></a>2. een runbook maken

1. Als u uw Power shell-runbook wilt maken, gaat u naar uw Automation-account. Onder **proces automatisering** selecteert u de tegel **Runbooks** en selecteert u vervolgens **een runbook maken**.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook.png" alt-text="Runbook maken.":::

2. Selecteer **maken** om het runbook te maken.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook-2.png" alt-text="Selecteer maken.":::

3. Selecteer het zojuist gemaakte runbook en selecteer vervolgens **bewerken**.

   :::image type="content" source="./media/custom-route-alert-portal/edit-runbook.png" alt-text="Runbook bewerken":::

4. Plak het Power shell-script in **bewerken**. Het [voorbeeld script](#script) kan worden gewijzigd en gebruikt voor het bewaken van ExpressRoute-gateways in een of meer resource groepen.

   In het voorbeeld script ziet u de volgende instellingen:

    * De matrix **$rgList** bevat de lijst met resource groepen met ExpressRoute-gateways. U kunt de op lijst gebaseerde ExpressRoute-gateways aanpassen.
    * De variabele **$thresholdNumRoutes** de drempel waarde definiëren voor het aantal netwerk voorvoegsels dat wordt geadverteerd vanuit een ExpressRoute-gateway naar de on-premises netwerken.

#### <a name="example-script"></a><a name="script"></a>Voorbeeldscript

```powershell
  ################# Input parameters #################
# Resource Group Name where the ExR GWs resides in
$rgList= @('ASH-Cust10-02','ASH-Cust30')  
$thresholdNumRoutes = 160
###################################################

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process | Out-Null

Try {

   $conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
   while(!($connectionResult) -And ($logonAttempt -le 5))
   {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =  Connect-AzAccount `
                               -ServicePrincipal `
                               -ApplicationId $conn.ApplicationId `
                               -Tenant $conn.TenantId `
                               -CertificateThumbprint $conn.CertificateThumbprint `
                               -Subscription $conn.SubscriptionId `
                               -Environment AzureCloud 
                               
        Start-Sleep -Seconds 10
    }
} Catch {
    if (!$conn)
    {
        $ErrorMessage = "Service principal not found."
        throw $ErrorMessage
    } 
    else
    {
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Get the name of the Azure subscription
$subscriptionName=(Get-AzSubscription -SubscriptionId $conn.SubscriptionId).Name

#write-Output "<br>$(Get-Date) - selection of the Azure subscription: $subscriptionName" 
Select-AzSubscription -SubscriptionId $conn.SubscriptionId | Out-Null


$GtwList = @()
$results= @()

foreach ($rgName in $rgList)
{
## Collect all the ExpressRoute gateways in a Resource Group
$GtwList=Get-AzVirtualNetworkGateway -ResourceGroupName $rgName 

## For each ExpressRoute gateway, get the IP addresses of the BGP peers and collect the number of routes advertised 
foreach ($gw in $GtwList) {
  
  $peers = Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName


 if ($peers[0].State -eq 'Connected') {
   $routes1=$null
   $routes1 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[0].Neighbor
 }
  if ($peers[1].State -eq 'Connected') {
  
   $routes2=$null
   $routes2 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[1].Neighbor
 }
 
  $sampleTime=(Get-Date).ToString("dd-MM-yyyy HH:mm:ss")
  if ($routes1.Count -eq $routes2.Count)
  {
     
     if ($routes1.Count -lt $thresholdNumRoutes){
       $status='OK'
       $alertMsg='number of routes below threshold'
     } 
     else {
       $status='ALERT'
       $alertMsg='number of routes above threshold'
     }
  } 
  else
  {
     $status='WARNING'
     $alertMsg='check ER Gateway'
  }
  
  $obj = [psCustomObject]@{
            resourceGroup =$rgName
            nameGtw  = $gw.Name
            peer1 = $peers[0].Neighbor
            peer2 = $peers[1].Neighbor
            numRoutesPeer1=  $routes1.Count
            numRoutesPeer2=  $routes2.Count
            time=$sampleTime
            status=$status
            alertMessage = $alertMsg
        }
  $results += $obj
} ### end foreach gateways in each resource group
} ### end foreach resource group
$jsonResults= ConvertTo-Json $results -Depth 100
Write-Output  $jsonResults
 
 ```

### <a name="3-save-and-publish-the-runbook"></a><a name="publish"></a>3. het runbook opslaan en publiceren

1. Selecteer **Opslaan** om een concept kopie van het runbook op te slaan.
2. Selecteer **publiceren** om het runbook te publiceren als de officiële versie van het runbook in het Automation-account.

   :::image type="content" source="./media/custom-route-alert-portal/save-publish-runbook.png" alt-text="Sla het runbook op en publiceer het.":::

Wanneer u het Power shell-script uitvoert, wordt een lijst met waarden verzameld:
 
* Resourcegroep

* Naam van ExpressRoute-gateway

* IP-adres van de eerste BGP-peer (Peer1)

* IP-adres van de tweede BGP-peer (peer2)

* Aantal netwerk voorvoegsels dat is geadverteerd van de ExpressRoute-gateway naar de eerste BGP-peer (Peer1)

* Aantal netwerk voorvoegsels dat is geadverteerd van de ExpressRoute-gateway naar de tweede BGP-peer (peer2)

* Tijdstempel

* Status, geclassificeerd als:

  * OK als het aantal routes kleiner is dan een drempel waarde
  * ' ALERT ' als het aantal routes indien hoger dan een drempel waarde
  * WAARSCHUWING als het aantal netwerk voorvoegsels dat is geadverteerd naar de twee BGP-peer, afwijkt

* Waarschuwings bericht voor een uitgebreide beschrijving van de status (OK, waarschuwing, waarschuwing)

Het Power shell-script converteert de verzamelde gegevens naar een JSON-uitvoer. Het runbook maakt gebruik van de Power shell [-cmdlet write-output](/powershell/module/Microsoft.PowerShell.Utility/Write-Output)  als uitvoer stroom voor het communiceren van informatie naar de client.

### <a name="4-validate-the-runbook"></a><a name="validate"></a>4. het runbook valideren

Zodra het runbook is gemaakt, moet het worden gevalideerd. Selecteer **starten** en controleer de uitvoer en fouten voor de verschillende taak stromen.

:::image type="content" source="./media/custom-route-alert-portal/validate-runbook.png" alt-text="Het runbook valideren" lightbox="./media/custom-route-alert-portal/validate-runbook-expand.png":::

## <a name="create-and-configure-a-logic-app"></a><a name="logic"></a>Een logische app maken en configureren

Azure Logic Apps is de orchestrator van elk proces van verzamelen en acties. In de volgende secties bouwt u een werk stroom met behulp van een logische app.

### <a name="workflow"></a>Werkstroom

In deze werk stroom bouwt u een logische app die regel matig ExpressRoute-gateways bewaakt. Als er nieuwe items bestaan, stuurt de logische app een e-mail voor elk item. Wanneer u bent klaar, ziet uw logische app eruit als deze werkstroom op hoog niveau:

:::image type="content" source="./media/custom-route-alert-portal/logic-apps-workflow.png" alt-text="Logic Apps werk stroom":::

### <a name="1-create-a-logic-app"></a>1. een logische app maken

Maak in **Logic app Designer** een logische app met behulp van de sjabloon voor de **lege logische app** . Zie voor stappen [Logic apps maken](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app).

:::image type="content" source="./media/custom-route-alert-portal/blank-template.png" alt-text="Lege sjabloon":::

### <a name="2-add-a-trigger"></a>2. een trigger toevoegen

Elke logische app wordt gestart door een trigger. Een trigger wordt geactiveerd wanneer een bepaalde gebeurtenis plaatsvindt of wanneer aan een bepaalde voor waarde wordt voldaan. Steeds wanneer de trigger wordt geactiveerd, maakt de Azure Logic Apps-engine een exemplaar van een logische app dat wordt gestart en de werkstroom uitvoert.

Als u regel matig een logische app wilt uitvoeren die is gebaseerd op een vooraf gedefinieerde tijd schema, voegt u het ingebouwde **terugkeer patroon: schema** toe aan uw werk stroom. Typ **schema** in het zoekvak. Selecteer **Triggers**. Selecteer in de lijst triggers de optie **terugkeer patroon**.

:::image type="content" source="./media/custom-route-alert-portal/schedule.png" alt-text="Terugkeer patroon: schema":::

In de trigger plannings schema kunt u de tijd zone en een terugkeer patroon instellen voor het herhalen van die werk stroom. Het interval en de frequentie bepalen samen de planning van de trigger voor uw logische app. Houd rekening met de volgende factoren als u een redelijke minimale terugkeer frequentie wilt instellen:

* Het Power shell-script in het Automation-runbook neemt af en toe. De runtime is afhankelijk van het aantal ExpressRoute-gateways dat moet worden bewaakt. Een te korte frequentie van het terugkeer patroon zorgt ervoor dat taak wachtrijen.

* Het Power shell-script wordt uitgevoerd als een taak op de achtergrond. Het wordt niet onmiddellijk gestart; deze wordt uitgevoerd na een vertraging van een variabele.

* Een te korte frequentie van het terugkeer patroon genereert een onnodige belasting voor uw Azure ExpressRoute-gateways.

Aan het einde van de werk stroom configuratie kunt u de consistentie van de terugkeer frequentie controleren door de werk stroom een paar keer uit te voeren en vervolgens de uitkomst te controleren in de **geschiedenis van uitvoeringen**.

:::image type="content" source="./media/custom-route-alert-portal/recurrence.png" alt-text="Scherm afbeelding toont het interval en de frequentie waarden van het terugkeer patroon." lightbox="./media/custom-route-alert-portal/recurrence-expand.png":::

### <a name="3-create-a-job"></a><a name="job"></a>3. een taak maken

Een logische app heeft toegang tot andere apps, services en het platform, hoewel connectors. De volgende stap in deze werk stroom is het selecteren van een connector voor toegang tot het Azure Automation-account dat eerder is gedefinieerd.

1. Selecteer in **Logic apps Designer** onder **recurrence** een **nieuwe stap**. Onder **Kies een actie** en het zoekvak, selecteert u **alle**.
2. Typ **Azure Automation** en zoek in het zoekvak. Selecteer **taak maken**. **Maak een taak** die wordt gebruikt voor het starten van het Automation-runbook dat eerder is gemaakt.

   :::image type="content" source="./media/custom-route-alert-portal/create-job.png" alt-text="Taak maken":::

3. Meld u aan met een service-principal. U kunt een bestaande Service-Principal gebruiken, maar u kunt ook een nieuwe maken. Zie [de portal gebruiken om een Azure AD-Service-Principal te maken die toegang heeft tot bronnen](../active-directory/develop/howto-create-service-principal-portal.md)om een nieuwe service-principal te maken. Selecteer **verbinding maken met Service-Principal**.

   :::image type="content" source="./media/custom-route-alert-portal/sign-in.png" alt-text="Scherm opname van de sectie ' recurrence ' met de actie ' Connect with Service Principal ' gemarkeerd.":::

4. Typ de **naam** van een verbinding, voeg uw **client-ID** (toepassings-id), **client geheim** en uw **Tenant-id** toe. Ten slotte selecteert u **Create**.

   :::image type="content" source="./media/custom-route-alert-portal/connect-service-principal.png" alt-text="Verbinding maken met Service-Principal":::

5. Op de pagina **taak maken** moet de service-principal de rol ' lezer ' hebben voor de **resource groep** die als host fungeert voor het Automation-account en ' Automation-taak operator ' op het **Automation-account**. Controleer bovendien of u de **runbooknaam** hebt toegevoegd als een nieuwe para meter.

   :::image type="content" source="./media/custom-route-alert-portal/roles.png" alt-text="Scherm opname toont taak waarden in terugkeer patroon, waar u de Runbooknaam kunt controleren." lightbox="./media/custom-route-alert-portal/roles-expand.png":::

### <a name="4-get-the-job-output"></a><a name="output"></a>4. de taak uitvoer ophalen

1. Selecteer **Nieuwe stap**. Zoek naar "Azure Automation". Selecteer in de lijst **acties** de optie **taak uitvoer ophalen**.

   :::image type="content" source="./media/custom-route-alert-portal/get-output.png" alt-text="Taakuitvoer ophalen":::

2. Geef op de pagina **taak uitvoer ophalen** de vereiste informatie op voor toegang tot het Automation-account. Selecteer het **abonnement, de resource groep** en het **Automation-account** dat u wilt gebruiken. Klik in het vak **taak-id** . Wanneer de lijst met **dynamische inhoud** wordt weer gegeven, selecteert u **taak-id**.

   :::image type="content" source="./media/custom-route-alert-portal/job-id.png" alt-text="Taak-id" lightbox="./media/custom-route-alert-portal/job-id-expand.png":::

### <a name="5-parse-the-json"></a><a name="parse"></a>5. de JSON parseren

De informatie in de uitvoer van de actie ' Azure Automation taak maken ' (vorige stappen) genereert een JSON-object. Logic Apps **JSON parseren** is een ingebouwde actie om gebruikers vriendelijke tokens te maken op basis van de eigenschappen en hun waarden in JSON-inhoud. U kunt deze eigenschappen vervolgens gebruiken in uw werk stroom.

1. Een actie toevoegen. Selecteer **nieuwe stap** onder **taak uitvoer ophalen->actie**.
2. Typ in het zoekvak **een actie kiezen** de tekst ' JSON parseren ' om te zoeken naar connectors die deze actie aanbieden. Selecteer in de lijst **acties** de actie **JSON parseren** voor de gegevens bewerkingen die u wilt gebruiken.

   :::image type="content" source="./media/custom-route-alert-portal/parse-json.png" alt-text="JSON parseren":::

3. Klik in het vak **inhoud** . Wanneer de lijst met dynamische inhoud wordt weer gegeven, selecteert u **inhoud**.

   :::image type="content" source="./media/custom-route-alert-portal/content.png" alt-text="Scherm afbeelding toont het dialoog venster JSON parseren met geselecteerde inhoud." lightbox="./media/custom-route-alert-portal/content-expand.png":::

4. Voor het parseren van een JSON is een schema vereist. Het schema kan worden gegenereerd met de uitvoer van het Automation-runbook. Open een nieuwe webbrowser sessie, voer het Automation-runbook uit en pak de uitvoer. Ga terug naar de **Logic apps bewerking JSON-gegevens parseren** . Selecteer aan de onderkant van de pagina **voor beeld Payload gebruiken om een schema te genereren**.

   :::image type="content" source="./media/custom-route-alert-portal/sample-payload.png" alt-text="Voorbeeld lading gebruiken om een schema te genereren":::

5. Plak de uitvoer van het Automation-runbook en selecteer **gereed** om een voor beeld-JSON-nettolading in te **voegen of te plakken**.

   :::image type="content" source="./media/custom-route-alert-portal/paste-payload.png" alt-text="Voorbeeld lading plakken" lightbox="./media/custom-route-alert-portal/paste-payload-expand.png":::

6. Er wordt automatisch een schema gegenereerd door de JSON-invoer lading te parseren.

   :::image type="content" source="./media/custom-route-alert-portal/generate-schema.png" alt-text="Schema genereren" lightbox="./media/custom-route-alert-portal/generate-schema-expand.png":::

### <a name="6-define-and-initialize-a-variable"></a><a name="define-variable"></a>6. een variabele definiëren en initialiseren

In deze stap van de werk stroom maken we een voor waarde voor het verzenden van een waarschuwing per e-mail. Voor een flexibele, aangepaste opmaak van een bericht in de hoofd tekst van een e-mail wordt een hulp variabele geïntroduceerd in de werk stroom.

1. Selecteer **nieuwe stap** onder de **actie taak uitvoer ophalen**. Zoek en selecteer in het zoekvak **variabelen**.

   :::image type="content" source="./media/custom-route-alert-portal/variables.png" alt-text="Scherm afbeelding toont het dialoog venster een actie kiezen met variabele in het zoekvak en de geselecteerde variabelen.":::

2. Selecteer in de lijst **acties** de actie **variabele initialiseren** .

   :::image type="content" source="./media/custom-route-alert-portal/initialize-variables.png" alt-text="Variabelen initialiseren":::

3. Geef de naam van de variabele op. Selecteer bij **type** de optie **teken reeks**. De **waarde** van de variabele wordt later in de werk stroom toegewezen.

   :::image type="content" source="./media/custom-route-alert-portal/string.png" alt-text="Scherm afbeelding toont JSON parseren die is gekoppeld aan de initialisatie variabele, waar u een naam, type en waarde kunt invoeren." lightbox="./media/custom-route-alert-portal/string-expand.png":::

### <a name="7-create-a-for-each-action"></a><a name="cycles-json"></a>7. een ' voor elke ' actie maken

Zodra de JSON is geparseerd, wordt de inhoud in *de uitvoer van de* **geparseerde JSON-gegevens** opgeslagen. Als u de uitvoer wilt verwerken, kunt u een lus ' voor elke ' herhalen van een of meer acties op elk item in de matrix.

1. Selecteer **een actie toevoegen** onder **variabele initialiseren**. Typ in het zoekvak ' voor elke ' als uw filter.

   :::image type="content" source="./media/custom-route-alert-portal/control.png" alt-text="Scherm afbeelding toont het dialoog venster een actie kiezen met voor elk in het zoekvak en het geselecteerde besturings element.":::

2. Selecteer in de lijst **acties** de actie **voor elk besturings element**.

   :::image type="content" source="./media/custom-route-alert-portal/for-each.png" alt-text="Voor elk besturings element":::

3. Klik in het tekstvak **Selecteer een uitvoer van de vorige stappen** . Wanneer de lijst met **dynamische inhoud** wordt weer gegeven, selecteert u de **hoofd tekst** van de geparseerde JSON.

   :::image type="content" source="./media/custom-route-alert-portal/body.png" alt-text="In de scherm afbeelding wordt de geïnitialiseerde variabele voor elke weer gegeven, die het tekstvak selecteer een uitvoer van vorige stappen bevat.":::

4. We willen voor elk element van de JSON-hoofd tekst een voor waarde instellen. Selecteer in de actie groep de optie **besturings element**.

   :::image type="content" source="./media/custom-route-alert-portal/condition-control.png" alt-text="Beheer":::

5. Selecteer in de lijst **acties** de optie **voor waarde-besturings element**. De Condition-Control is een controle structuur die de gegevens in uw werk stroom vergelijkt met specifieke waarden of velden. U kunt vervolgens verschillende acties opgeven die worden uitgevoerd op basis van het feit of de gegevens voldoen aan de voor waarde.

   :::image type="content" source="./media/custom-route-alert-portal/condition.png" alt-text="Besturings element voor waarde":::

6. Wijzig in de basis van de **voor waarde** de logische bewerking in **of**.

   :::image type="content" source="./media/custom-route-alert-portal/condition-or.png" alt-text="Of" lightbox="./media/custom-route-alert-portal/condition-or-expand.png":::

7. Controleer de waarde voor het aantal netwerk-prefixen dat een ExpressRoute-gateway adverteert aan de twee BGP-peers. Het aantal routes is beschikbaar in ' numRoutePeer1 ' en ' numRoutePeer2 ' in **dynamische inhoud**. Typ in het vak waarde de waarde voor **numRoutePeer1**.

   :::image type="content" source="./media/custom-route-alert-portal/peer-1.png" alt-text="numRoutesPeer1":::

8. Als u een andere rij wilt toevoegen aan uw voor waarde, kiest u **toevoegen-> rij toevoegen**. In het tweede vak, van **dynamische inhoud**, selecteert u **numRoutePeer2**.

   :::image type="content" source="./media/custom-route-alert-portal/peer-2.png" alt-text="numRoutesPeer2":::

9. De logische voor waarde is waar wanneer een van de twee dynamische variabelen, numRoute1 of numRoute2, groter is dan de drempel waarde. In dit voor beeld wordt de drempel waarde vastgesteld op 800 (80% van de maximum waarde van 1000 routes). U kunt de drempel waarde aanpassen aan uw vereisten. Voor consistentie moet de waarde dezelfde waarde zijn die wordt gebruikt in het runbook Power shell-script.

   :::image type="content" source="./media/custom-route-alert-portal/logic-condition.png" alt-text="Logische voor waarde":::

10. Onder **Indien waar**, kunt u de acties opmaken en de actie maken om de waarschuwing per e-mail te verzenden. Kies in * * een actie, zoek en selecteer **variabelen**.

    :::image type="content" source="./media/custom-route-alert-portal/condition-if-true.png" alt-text="Indien waar":::

11. Selecteer **een actie toevoegen** in variabelen. Selecteer in de lijst **acties** de optie **variabele instellen**.

    :::image type="content" source="./media/custom-route-alert-portal/condition-set-variable.png" alt-text="Scherm afbeelding van de sectie ' Varia bles ' waarvoor het tabblad ' acties ' is geselecteerd en ' variabele instellen ' is gemarkeerd.":::

12. In **naam** selecteert u de variabele met de naam **EmailBody** die u eerder hebt gemaakt. Plak voor **waarde** het HTML-script dat vereist is om de waarschuwings-e-mail op te maken. Gebruik de **dynamische inhoud** om de waarden van de JSON-hoofd tekst op te neemt. Na het configureren van deze instellingen is het resultaat dat de variabele **emailBody** alle informatie bevat die betrekking heeft op de waarschuwing, in HTML-indeling.

    :::image type="content" source="./media/custom-route-alert-portal/paste-script.png" alt-text="Variabele instellen":::

### <a name="8-add-the-email-connector"></a><a name="email"></a>8. de e-mail Connector toevoegen

Logic Apps biedt veel e-mailconnectors. In dit voor beeld voegen we een Outlook-Connector toe om de waarschuwing per e-mail te verzenden. Selecteer **een actie toevoegen** onder **variabele instellen**. Typ ' e-mail verzenden ' in het zoekvak om **een actie te kiezen**.

1. Selecteer **Office 365 Outlook**.

   :::image type="content" source="./media/custom-route-alert-portal/email.png" alt-text="E-mail verzenden":::

2. Selecteer in de lijst **acties** de optie **een E-mail verzenden (v2)**.

   :::image type="content" source="./media/custom-route-alert-portal/email-v2.png" alt-text="Een e-mail verzenden (v2)":::

3. Meld u aan om een verbinding te maken met Office 365 Outlook.

   :::image type="content" source="./media/custom-route-alert-portal/office-365.png" alt-text="Aanmelden":::

4. Klik in het veld **hoofd tekst** op **dynamische inhoud toevoegen**. Voeg in het deel venster dynamische inhoud de variabele **emailBody** toe. Vul het **onderwerp** en het veld **in** .

   :::image type="content" source="./media/custom-route-alert-portal/emailbody.png" alt-text="Hoofdtekst":::

5. Met de actie **een E-mail verzenden (v2)** wordt de werk stroom instelling voltooid.

   :::image type="content" source="./media/custom-route-alert-portal/send-email-v2.png" alt-text="E-mail verzenden v2" lightbox="./media/custom-route-alert-portal/send-email-v2-expand.png":::

### <a name="9-workflow-validation"></a><a name="validation"></a>9. werk stroom validatie

De laatste stap is het valideren van de werk stroom. Selecteer in **Logic apps overzicht** de optie **trigger uitvoeren**. Selecteer **terugkeer patroon**. De werk stroom kan worden bewaakt en gecontroleerd in de **uitvoerings geschiedenis**.

:::image type="content" source="./media/custom-route-alert-portal/trigger.png" alt-text="Trigger uitvoeren":::

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Logic apps](../logic-apps/logic-apps-overview.md)voor meer informatie over het aanpassen van de werk stroom.
