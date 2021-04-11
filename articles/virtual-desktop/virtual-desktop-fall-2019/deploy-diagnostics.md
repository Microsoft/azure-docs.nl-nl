---
title: Het diagnostische hulp programma voor Windows virtueel bureau blad (klassiek) implementeren-Azure
description: Het hulp programma Diagnostics UX implementeren voor het virtuele bureau blad van Windows (klassiek).
author: Heidilohr
ms.topic: how-to
ms.date: 12/15/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: ed010b3eed875c8b14892a588be13a32fbbd95ac
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445088"
---
# <a name="deploy-the-windows-virtual-desktop-classic-diagnostics-tool"></a>Het Windows-hulp programma voor virtueel bureau blad (klassiek) implementeren

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten.

Het hulp programma voor diagnostische gegevens voor Windows Virtual Desktop kan voor u worden uitgevoerd:

- Zoek naar diagnostische activiteiten (beheer, verbinding of feed) voor één gebruiker gedurende een periode van één week.
- Informatie over de sessie-host verzamelen voor verbindings activiteiten vanuit uw Log Analytics-werk ruimte.
- Bekijk de prestatie gegevens van de virtuele machine (VM) voor een bepaalde host.
- Bekijk welke gebruikers zijn aangemeld bij de sessiehost.
- Bericht verzenden naar actieve gebruikers op een specifieke sessiehost.
- Gebruikers afmelden bij een sessiehost.

## <a name="prerequisites"></a>Vereisten

U moet een Azure Active Directory app-registratie en een Log Analytics-werk ruimte maken voordat u de Azure Resource Manager sjabloon voor het hulp programma kunt implementeren. U of de beheerder heeft deze machtigingen nodig om het volgende te doen:

- Eigenaar van het Azure-abonnement
- Machtiging voor het maken van resources in uw Azure-abonnement
- Machtiging voor het maken van een Azure AD-app
- RDS-eigenaar of Inzender rechten

U moet deze twee Power shell-modules ook installeren voordat u aan de slag gaat:

- [Azure PowerShell-module](/powershell/azure/install-az-ps)
- [Azure AD-module](/powershell/azure/active-directory/install-adv2)

Zorg ervoor dat uw abonnement-ID gereed is voor wanneer u zich aanmeldt.

Nadat u alles in de juiste volg orde hebt, kunt u de Azure AD-App-registratie maken.

## <a name="create-an-azure-active-directory-app-registration"></a>Een Azure Active Directory app-registratie maken

In deze sectie wordt uitgelegd hoe u Power shell kunt gebruiken om de Azure Active Directory-app te maken met een Service-Principal en om API-machtigingen voor te krijgen.

>[!NOTE]
>De API-machtigingen zijn Windows virtueel bureau blad, Log Analytics-en Microsoft Graph API-machtigingen worden toegevoegd aan de Azure Active Directory-toepassing.

1. Open PowerShell als administrator.
2. Meld u aan bij Azure met een account met eigenaar-of Inzender machtigingen voor het Azure-abonnement dat u wilt gebruiken voor het diagnostische hulp programma:
   ```powershell
   Login-AzAccount
   ```
3. Meld u aan bij Azure AD met hetzelfde account:
   ```powershell
   Connect-AzureAD
   ```
4. Ga naar de [github-opslag plaats van RDS-sjablonen](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) en voer het **CreateADAppRegistrationforDiagnostics.ps1** script uit in Power shell.
5.  Wanneer u wordt gevraagd uw app een naam te geven, voert u een unieke app-naam in.


Nadat het script is uitgevoerd, moeten de volgende dingen worden weer gegeven in de uitvoer:

-  Een bericht waarin wordt bevestigd dat uw app nu een functie toewijzing van een service-principal heeft.
-  De client-ID en de geheime sleutel van de client die u nodig hebt voor het implementeren van het diagnostische hulp programma.

Nu u uw app hebt geregistreerd, is het tijd om uw Log Analytics-werk ruimte te configureren.

## <a name="configure-your-log-analytics-workspace"></a>Uw Log Analytics-werk ruimte configureren

Voor de beste ervaring raden we u aan om uw Log Analytics-werk ruimte te configureren met de volgende prestatie meter items waarmee u instructies van de gebruikers ervaring in een externe sessie kunt afleiden. Zie [drempel waarden voor het prestatie meter item](deploy-diagnostics.md#windows-performance-counter-thresholds)voor een lijst met aanbevolen prestatie meter items met aanbevolen drempel waarden.

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>Een Azure Log Analytics-werk ruimte maken met behulp van Power shell

U kunt een Power shell-script uitvoeren om een Log Analytics-werk ruimte te maken en de aanbevolen prestatie meter items voor Windows te configureren om de gebruikers ervaring en de prestaties van de app te bewaken.

>[!NOTE]
>Als u al een bestaande Log Analytics-werk ruimte hebt gemaakt zonder het Power shell-script dat u wilt gebruiken, gaat u verder met het [valideren van de script resultaten in de Azure Portal](#validate-the-script-results-in-the-azure-portal).

Het Power shell-script uitvoeren:

1.  Open Power shell als beheerder.
2.  Ga naar de [github-opslag plaats van RDS-sjablonen](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) en voer het **CreateLogAnalyticsWorkspaceforDiagnostics.ps1** script uit in Power shell.
3. Voer de volgende waarden in voor de para meters:

    - Voer voor **ResourceGroupName** de naam in voor de resource groep.
    - Voer voor **LogAnalyticsWorkspaceName** een unieke naam in voor uw log Analytics-werk ruimte.
    - Voer bij **locatie** de Azure-regio in die u gebruikt.
    - Voer de **Azure-abonnements-id** in die u kunt vinden in de Azure portal onder **abonnementen**.

4. Voer de referenties in van een gebruiker met gedelegeerde beheerders toegang.
5. Meld u aan bij de Azure Portal met de referenties van dezelfde gebruiker.
6. Noteer de LogAnalyticsWorkspace-ID voor later.
7. Als u de Log Analytics-werk ruimte instelt met het Power shell-script, moeten de prestatie meter items al zijn geconfigureerd en kunt u verder gaan om [de script resultaten in de Azure portal te valideren](#validate-the-script-results-in-the-azure-portal). Als dat niet het geval is, gaat u verder met de volgende sectie.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>Windows-prestatie meter items configureren in uw bestaande Log Analytics-werk ruimte

Deze sectie is voor gebruikers die een bestaande Azure Log Analytics-werk ruimte willen gebruiken die is gemaakt zonder het Power shell-script in de vorige sectie. Als u het script nog niet hebt gebruikt, moet u de aanbevolen Windows-prestatie meter items hand matig configureren.

U kunt als volgt de aanbevolen prestatie meter items hand matig configureren:

1. Open uw Internet browser en meld u aan bij de [Azure Portal](https://portal.azure.com/) met uw beheerders account.
2. Ga vervolgens naar **log Analytics-werk ruimten** om de geconfigureerde Windows-prestatie meter items te controleren.
3. Selecteer in de sectie **instellingen** de optie  **Geavanceerde instellingen**.
4. Daarna gaat u naar **gegevens**  >  **prestatie meter items van Windows** en voegt u de volgende tellers toe:

    -   Logische schijf ( \* ) \\ % beschik bare ruimte
    -   Logische schijf (C:) \\ Gem. wachtrij lengte voor schijven
    -   Beschik \* \\ bare MB geheugen ()
    -   Processor informatie ( \* ) \\ processor tijd
    -   Gebruikers invoer vertraging per sessie ( \* ) \\ maximale invoer vertraging

Meer informatie over de prestatie meter items [in Windows-en Linux-prestatie gegevens bronnen in azure monitor](../../azure-monitor/agents/data-sources-performance-counters.md).

>[!NOTE]
>Alle extra tellers die u configureert, worden niet weer gegeven in het diagnostische hulp programma zelf. Als u deze wilt weer geven in het diagnostische hulp programma, moet u het configuratie bestand van het hulp programma configureren. Instructies voor het uitvoeren van een geavanceerd beheer vindt u op een later tijdstip in GitHub.

## <a name="validate-the-script-results-in-the-azure-portal"></a>De resultaten van het script valideren in de Azure Portal

Voordat u doorgaat met de implementatie van het diagnostische hulp programma, kunt u het beste controleren of uw Azure Active Directory-toepassing API-machtigingen heeft en uw Log Analytics-werk ruimte de vooraf geconfigureerde Windows-prestatie meter items heeft.

### <a name="review-your-app-registration"></a>Uw app-registratie controleren

Om ervoor te zorgen dat de app-registratie API-machtigingen heeft:

1. Open een browser en maak verbinding met de [Azure Portal](https://portal.azure.com/) met uw beheerders account.
2. Ga naar **Azure Active Directory**.
3. Ga naar **app-registraties** en selecteer **alle toepassingen**.
4. Zoek naar uw Azure AD-App-registratie met dezelfde app-naam die u hebt opgegeven in stap 5 van [een registratie van een Azure Active Directory-app maken](deploy-diagnostics.md#create-an-azure-active-directory-app-registration).

### <a name="review-your-log-analytics-workspace"></a>Uw Log Analytics-werk ruimte controleren

Zorg ervoor dat uw Log Analytics-werk ruimte de vooraf geconfigureerde Windows-prestatie meter items heeft:

1. Ga in het [Azure Portal](https://portal.azure.com/)naar **log Analytics-werk ruimten** om de geconfigureerde Windows-prestatie meter items te controleren.
2. Selecteer onder **instellingen** de optie **Geavanceerde instellingen**.
3. Daarna gaat u naar **gegevens**  >  **Windows-prestatie meter items**.
4. Zorg ervoor dat de volgende prestatie meter items zijn geconfigureerd:

   - Logische schijf ( \* ) \\ % beschik bare ruimte: geeft de hoeveelheid beschik bare ruimte van de totale bruikbare ruimte op de schijf als een percentage.
   - Logische schijf (C:) \\ Gem. wachtrij lengte voor schijven: de lengte van de aanvraag voor het overdragen van schijven voor uw C-station. De waarde mag niet langer zijn dan 2 gedurende een korte periode.
   - Beschik \* bare MB geheugen () \\ : het beschik bare geheugen voor het systeem in mega bytes.
   - Processor informatie ( \* ) \\ processor tijd: het percentage van de verstreken tijd dat de processor nodig heeft om een niet-inactieve thread uit te voeren.
   - Gebruikers invoer vertraging per sessie ( \* ) \\ maximale invoer vertraging

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Verbinding maken met virtuele machines in uw Log Analytics-werk ruimte

Als u de status van virtuele machines wilt kunnen bekijken, moet u de Log Analytics-verbinding inschakelen. Volg deze stappen om uw Vm's te verbinden:

1. Open een browser en meld u aan bij de [Azure Portal](https://portal.azure.com/) met uw beheerders account.
2. Ga naar uw Log Analytics-werk ruimte.
3. Selecteer in het linkerdeel venster onder gegevens bronnen voor werk ruimte de optie **virtuele machines**.
4. Selecteer de naam van de virtuele machine waarmee u verbinding wilt maken.
5. Selecteer **Verbinding maken**.

## <a name="deploy-the-diagnostics-tool"></a>Het diagnosehulpprogramma implementeren

Voor het implementeren van de Azure resource management-sjabloon voor het diagnostische hulp programma:

1.  Ga naar de [github Azure RDS-Templates-pagina](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy).
2.  Implementeer de sjabloon in Azure en volg de instructies in de sjabloon. Zorg ervoor dat u over de volgende informatie beschikt:

    -   Client-Id
    -   Client-Secret
    -   Log Analytics-werkruimte-id

3.  Zodra de invoer parameters zijn opgegeven, accepteert u de voor waarden en selecteert u vervolgens **aanschaffen**.

De implementatie duurt 2 tot 3 minuten. Nadat de implementatie is voltooid, gaat u naar de resource groep en controleert u of de resources van de web-app en het app service-plan aanwezig zijn.

Daarna moet u de omleidings-URI instellen.

### <a name="set-the-redirect-uri"></a>De omleidings-URI instellen

De omleidings-URI instellen:

1.  Ga in het [Azure Portal](https://portal.azure.com/)naar **app Services** en zoek de toepassing die u hebt gemaakt.
2.  Ga naar de pagina overzicht en kopieer de URL die u hier vindt.
3.  Navigeer naar **app-registraties** en selecteer de app die u wilt implementeren.
4.  Selecteer in het linkerdeel venster onder sectie beheren de optie **verificatie**.
5.  Voer de gewenste omleidings-URI in het tekstvak **omleidings-URI** in en selecteer vervolgens **Opslaan** in de linkerbovenhoek van het menu.
6. Selecteer **Web** in de vervolg keuzelijst onder Type.
7. Voer de URL van de overzichts pagina van de app in en voeg **/Security/signin-callback** toe aan het eind. Bijvoorbeeld: `https://<yourappname>.azurewebsites.net/security/signin-callback`.

   > [!div class="mx-imgBorder"]
   > ![De omleidings-URI-pagina](../media/redirect-uri-page.png)

8. Ga nu naar uw Azure-resources, selecteer de Azure-app Services-resource met de naam die u hebt ingevoerd in de sjabloon en navigeer naar de URL die eraan is gekoppeld. (Als de naam van de app die u in de sjabloon hebt gebruikt `contosoapp45` , bijvoorbeeld is, is de bijbehorende URL <http://contoso.azurewebsites.net> ).
9. Meld u aan met het juiste Azure Active Directory gebruikers account.
10.   Selecteer **Accepteren**.

## <a name="distribute-the-diagnostics-tool"></a>Het hulp programma voor diagnostische gegevens distribueren

Voordat u het hulp programma voor diagnostische gegevens beschikbaar maakt voor uw gebruikers, moet u ervoor zorgen dat ze over de volgende machtigingen beschikken:

- Gebruikers hebben lees toegang nodig voor log Analytics. Zie [aan de slag met rollen, machtigingen en beveiliging met Azure monitor](../../azure-monitor/roles-permissions-security.md)voor meer informatie.
-  Gebruikers hebben ook lees toegang nodig voor de Windows-Tenant voor virtueel bureau blad (functie voor RDS-lezer). Zie voor meer informatie [gedelegeerde toegang in virtueel bureau blad van Windows](delegated-access-virtual-desktop-2019.md).

U moet uw gebruikers ook de volgende informatie geven:

- De URL van de app
- De namen van de Tenant groep afzonderlijke tenants waartoe ze toegang hebben.

## <a name="use-the-diagnostics-tool"></a>Het hulp programma voor diagnostische gegevens gebruiken

Nadat u zich hebt aangemeld bij uw account met behulp van de informatie die u hebt ontvangen van uw organisatie, moet u de UPN gereed maken voor de gebruiker waarvoor u een query wilt uitvoeren voor activiteiten. Een zoek opdracht geeft u alle activiteiten onder het opgegeven type activiteit die in de afgelopen week zijn gebeurd.

### <a name="how-to-read-activity-search-results"></a>Zoek resultaten voor activiteiten lezen

Activiteiten worden gesorteerd op tijds tempel, met de nieuwste activiteit eerst. Als de resultaten een fout retour neren, controleert u eerst of er een service fout is opgetreden. Maak voor service fouten een ondersteunings ticket met de informatie over de activiteit om het probleem op te lossen. Alle andere fout typen kunnen meestal worden opgelost door de gebruiker of de beheerder. Zie [problemen identificeren en onderzoeken](diagnostics-role-service-2019.md#common-error-scenarios)voor een overzicht van de meest voorkomende fout scenario's en hoe u deze kunt oplossen.

>[!NOTE]
>Service fouten worden "externe fouten" genoemd in de gekoppelde documentatie. Dit wordt gewijzigd tijdens het bijwerken van de Power shell-verwijzing.

Verbindings activiteiten kunnen meer dan één fout bevatten. U kunt het type activiteit uitvouwen om eventuele andere fouten te zien die de gebruiker heeft bereikt. Selecteer de naam van de fout code om een dialoog venster te openen voor meer informatie hierover.

### <a name="investigate-the-session-host"></a>De sessiehost onderzoeken

Zoek en selecteer in de zoek resultaten de sessiehost waarover u informatie wilt.

U kunt de status van de sessie-host analyseren:

- Op basis van een vooraf gedefinieerde drempel waarde kunt u de status gegevens van de sessie-host ophalen die Log Analytics query's.
- Als er geen activiteit is of als de sessiehost niet is verbonden met Log Analytics, is de informatie niet beschikbaar.

U kunt ook communiceren met gebruikers op de sessiehost:

- U kunt zich afmelden of een bericht verzenden naar een aangemelde gebruiker.
- De gebruiker die u oorspronkelijk hebt gezocht, is standaard geselecteerd, maar u kunt ook extra gebruikers selecteren om berichten te verzenden of meerdere gebruikers tegelijk af te melden.

### <a name="windows-performance-counter-thresholds"></a>Drempel waarden voor prestatie meter items voor Windows

- Logische schijf ( \* ) \\ % beschik bare ruimte:

    - Hiermee wordt het percentage van de totale bruikbare ruimte op de logische schijf weer gegeven die vrij is.
    - Drempel waarde: minder dan 20% is gemarkeerd als beschadigd.

- Logische schijf (C:) \\ Gem. lengte van de wachtrij:

    - Geeft de voor waarden van het opslag systeem aan.
    - Drempel waarde: hoger dan 5 is gemarkeerd als beschadigd.

- Beschik \* \\ bare mega bytes () geheugen ():

    - Het beschik bare geheugen voor het systeem.
    - Drempel waarde: minder dan 500 mega bytes, gemarkeerd als beschadigd.

- Processor informatie ( \* ) \\ processor tijd:

    - Drempel waarde: hoger dan 80% is gemarkeerd als beschadigd.

- [Gebruikers invoer vertraging per sessie ( \* ) \\ maximale invoer vertraging](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/):

    - Drempel waarde: hoger dan 2000 MS is gemarkeerd als beschadigd.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het bewaken van activiteiten Logboeken bij het [gebruik van diagnostische gegevens met log Analytics](diagnostics-log-analytics-2019.md).
- Meer informatie over veelvoorkomende fout scenario's en hoe u deze kunt oplossen bij het [identificeren en diagnosticeren van problemen](diagnostics-role-service-2019.md).