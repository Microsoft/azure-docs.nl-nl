---
title: Azure Active Directory Connect Health Veelgestelde vragen-Azure | Microsoft Docs
description: Deze veelgestelde vragen vindt u antwoorden op vragen over Azure AD Connect Health. In deze Veelgestelde vragen worden vragen behandeld over het gebruik van de service, zoals het factureringsmodel, de mogelijkheden, beperkingen en ondersteuning.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6185c5408f74b914ce5ad47634999786ba1d7ab6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367993"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Veelgestelde vragen over Azure AD Connect Health
In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Active Directory (Azure AD) Connect Health. Deze veelgestelde vragen worden behandeld in het gebruik van de service, waaronder het facturerings model, de mogelijkheden, beperkingen en ondersteuning.

## <a name="general-questions"></a>Algemene vragen
**V: ik beheer meerdere Azure AD-mappen. Hoe kan ik overschakelen naar de versie die Azure Active Directory Premium heeft?**

Als u wilt scha kelen tussen verschillende Azure AD-tenants, selecteert u de naam van de momenteel aangemelde **gebruiker** in de rechter bovenhoek en kiest u vervolgens het juiste account. Als het account hier niet wordt vermeld, selecteert u **Afmelden** en gebruikt u vervolgens de globale beheerders referenties van de directory met Azure Active Directory Premium (P1 of P2) ingeschakeld om u aan te melden.

**V: welke versie van identiteits rollen wordt ondersteund door Azure AD Connect Health?**

De volgende tabel bevat de rollen en ondersteunde besturingssysteem versies.

|Rol| Besturings systeem/versie|
|--|--|
|Active Directory Federation Services (AD FS)| <ul><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> <li> Windows Server 2019  </li> </ul>|
|Azure AD Connect | Versie 1.0.9125 of hoger|
|Active Directory Domain Services (AD DS)| <ul><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> <li> Windows Server 2019  </li> </ul>|

Windows Server Core-installaties worden niet ondersteund.

Houd er rekening mee dat de functies die door de service worden door gegeven, mogelijk verschillen op basis van de rol en het besturings systeem. Met andere woorden, alle functies zijn mogelijk niet beschikbaar voor alle versies van het besturings systeem. Zie de beschrijvingen van functies voor meer informatie.

**V: hoeveel licenties heb ik nodig voor het bewaken van mijn infra structuur?**

* Voor de eerste Connect Health-Agent is ten minste één Azure AD Premium (P1 of P2)-licentie vereist.
* Elke aanvullende geregistreerde agent vereist 25 extra Azure AD Premium (P1 of P2)-licenties.
* Het aantal agents is gelijk aan het totale aantal agents dat is geregistreerd voor alle bewaakte rollen (AD FS, Azure AD Connect en/of AD DS).
* Voor AAD Connect Health Licensing is het niet nodig om de licentie toe te wijzen aan specifieke gebruikers. U hoeft alleen het vereiste aantal geldige licenties te hebben.

Licentie gegevens vindt u ook op de [Azure AD-pagina met prijzen](https://aka.ms/aadpricing).

Voorbeeld:

| Geregistreerde agents | Vereiste licenties | Voor beeld van bewakings configuratie |
| ------ | --------------- | --- |
| 1 | 1 | 1 Azure AD Connect server |
| 2 | 26| 1 Azure AD Connect server en 1 domein controller |
| 3 | 51 | 1 Active Directory Federation Services (AD FS) server, 1 AD FS proxy en 1 domein controller |
| 4 | 76 | 1 AD FS server, 1 AD FS proxy en 2 domein controllers |
| 5 | 101 | 1 Azure AD Connect server, 1 AD FS server, 1 AD FS proxy en 2 domein controllers |

**V: ondersteunt Azure AD Connect Health Azure Duitsland-Cloud?**

Azure AD Connect Health wordt niet ondersteund in de Duitse Cloud, met uitzonde ring van de [functie synchronisatie fouten rapport](how-to-connect-health-sync.md#object-level-synchronization-error-report).

| Rollen | Functies | Ondersteund in de Duitse Cloud |
| ------ | --------------- | --- |
| Connect Health voor synchronisatie | Bewaking/inzichten/waarschuwingen/analyse | No |
|  | Synchronisatie fout rapport | Yes |
| Connect Health voor ADFS | Bewaking/inzichten/waarschuwingen/analyse | No |
| Status van verbinding maken voor toevoegen | Bewaking/inzichten/waarschuwingen/analyse | No |

Om ervoor te zorgen dat de verbindings status van de agent wordt gesynchroniseerd, moet u de vereisten voor de [installatie](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) dienovereenkomstig configureren.

## <a name="installation-questions"></a>Installatie vragen

**V: wat is de impact van het installeren van de Azure AD Connect Health-Agent op afzonderlijke servers?**

De gevolgen van het installeren van de Microsoft Azure AD Connect Health-Agent, AD FS, Web Application proxy-servers, Azure AD Connect (synchronisatie)-servers, domein controllers zijn mini maal ten opzichte van de CPU, het geheugen verbruik, de netwerk bandbreedte en de opslag.

De volgende getallen zijn een benadering:

* CPU-verbruik: ~ 1-5% toename.
* Geheugen gebruik: tot 10% van het totale systeem geheugen.

> [!NOTE]
> Als de agent niet kan communiceren met Azure, slaat de agent de gegevens lokaal op voor een gedefinieerde maximum limiet. De agent overschrijft de gegevens in cache op basis van een ' minst recent service '.
>
>

* Lokale buffer opslag voor Azure AD Connect Health Agents: ~ 20 MB.
* Voor AD FS-servers wordt u aangeraden een schijf ruimte van 1.024 MB (1 GB) in te richten voor het AD FS controle kanaal voor Azure AD Connect Health Agents om alle controle gegevens te verwerken voordat deze worden overschreven.

**V: moet ik mijn servers opnieuw opstarten tijdens de installatie van de Azure AD Connect Health Agents?**

Nee. Voor de installatie van de agents is het niet nodig om de server opnieuw op te starten. Voor de installatie van bepaalde vereiste stappen kan het echter nodig zijn om de server opnieuw op te starten.

Voor de installatie van .NET 4,5 Framework in Windows Server 2008 R2 moet de server bijvoorbeeld opnieuw worden opgestart.

**V: werkt Azure AD Connect Health via een Pass-Through HTTP-proxy?**

Ja. Voor doorlopende bewerkingen kunt u de Health-Agent configureren voor het gebruik van een HTTP-proxy voor het door sturen van uitgaande HTTP-aanvragen.
Meer informatie over het [configureren van HTTP-proxy voor Health-Agents](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Als u een proxy moet configureren tijdens de registratie van de agent, moet u mogelijk de proxy-instellingen van Internet Explorer vooraf wijzigen.

1. Internet Explorer openen > **instellingen**  >  **Internet opties**  >  **verbindingen**  >  **LAN-instellingen**.
2. Selecteer **een proxy server voor uw LAN gebruiken**.
3. Selecteer **Geavanceerd** als u verschillende proxy poorten voor http en HTTPS/Secure hebt.

**V: ondersteunt Azure AD Connect Health basis verificatie bij het maken van verbinding met HTTP-proxy's?**

Nee. Een mechanisme voor het opgeven van een wille keurige gebruikers naam en wacht woord voor basis verificatie wordt momenteel niet ondersteund.

**V: welke firewall poorten moet ik openen om de Azure AD Connect Health Agent te laten werken?**

Zie de [sectie vereisten](how-to-connect-health-agent-install.md#requirements) voor de lijst met Firewall poorten en andere connectiviteits vereisten.

**V: Waarom worden er twee servers met dezelfde naam in de Azure AD Connect Health portal weer geven?**

Wanneer u een agent van een server verwijdert, wordt de server niet automatisch verwijderd uit de portal van Azure AD Connect Health. Als u een agent hand matig verwijdert van een server of de server zelf verwijdert, moet u de server vermelding hand matig verwijderen uit de Azure AD Connect Health Portal.

U kunt de installatie kopie van een server wijzigen of een nieuwe server maken met dezelfde gegevens (zoals de naam van de computer). Als u de reeds geregistreerde server niet uit de Azure AD Connect Health Portal hebt verwijderd en u de agent op de nieuwe server hebt geïnstalleerd, ziet u mogelijk twee vermeldingen met dezelfde naam.

In dit geval moet u de vermelding die bij de oudere server hoort hand matig verwijderen. De gegevens voor deze server zijn verouderd.

**V: kan ik de Azure AD Connect Health-Agent installeren op Windows Server Core?**

Nee.  Installatie op Server Core wordt niet ondersteund.

## <a name="health-agent-registration-and-data-freshness"></a>Registratie en gegevens versheid van de Health-Agent

**V: wat zijn veelvoorkomende redenen voor het registreren van fouten in de Health-Agent en hoe los ik problemen op?**

De status agent kan niet worden geregistreerd door de volgende mogelijke redenen:

* De agent kan niet communiceren met de vereiste eind punten omdat het verkeer wordt geblokkeerd door een firewall. Dit is met name gebruikelijk op Web Application proxy-servers. Zorg ervoor dat u de toegestane uitgaande communicatie met de vereiste eind punten en poorten hebt toegestaan. Zie de [sectie vereisten](how-to-connect-health-agent-install.md#requirements) voor meer informatie.
* Uitgaande communicatie wordt onderhevig aan een TLS-inspectie door de netwerklaag. Dit zorgt ervoor dat het certificaat dat door de agent wordt gebruikt, wordt vervangen door de controle server/entiteit en de stappen voor het volt ooien van de registratie van de agent mislukken.
* De gebruiker heeft geen toegang om de registratie van de agent uit te voeren. Globale beheerders hebben standaard toegang. U kunt Azure [RBAC (op rollen gebaseerd toegangs beheer)](how-to-connect-health-operations.md#manage-access-with-azure-rbac) gebruiken om de toegang tot andere gebruikers te delegeren.

**V: Ik ontvang een waarschuwing dat Health Service gegevens niet up-to-date zijn. Hoe kan ik het probleem op te lossen?**

De waarschuwing wordt door Azure AD Connect Health gegenereerd wanneer deze niet alle gegevens punten van de server in de afgelopen twee uur ontvangt. [Meer informatie](how-to-connect-health-data-freshness.md).

## <a name="operations-questions"></a>Vragen over bewerkingen
**V: moet ik de controle op de Web Application proxy-servers inschakelen?**

Nee, controle hoeft niet te worden ingeschakeld op de Web Application proxy-servers.

**V: hoe Waarschuwingen Azure AD Connect Health ik opgelost?**

Azure AD Connect Health waarschuwingen worden opgelost met een succes voorwaarde. Azure AD Connect Health Agents kunnen de geslaagde voor waarden regel matig detecteren en rapporteren aan de service. Voor enkele waarschuwingen is de onderdrukking op basis van tijd. Met andere woorden, als dezelfde fout situatie niet binnen 72 uur wordt waargenomen bij het genereren van waarschuwingen, wordt de waarschuwing automatisch opgelost.

**V: Ik ontvang een waarschuwing dat de test verificatie aanvraag (synthetische trans actie) geen token kan verkrijgen. Hoe kan ik het probleem op te lossen?**

Azure AD Connect Health voor AD FS deze waarschuwing wordt gegenereerd wanneer de Health-Agent die is geïnstalleerd op een AD FS server, geen token kan verkrijgen als onderdeel van een synthetische trans actie die wordt geïnitieerd door de Health-Agent. De Health-Agent gebruikt de lokale systeem context en probeert een token op te halen voor een self-Relying Party. Dit is een catch-all-test om ervoor te zorgen dat AD FS zich in de status van tokens uitgeven bevindt.

Meestal mislukt deze test omdat de status agent de naam van de AD FS-Farm niet kan omzetten. Dit kan gebeuren als de AD FS servers zich achter een netwerk taak verdeler bevinden en de aanvraag wordt gestart vanaf een knoop punt dat zich achter de load balancer bevindt (in plaats van een gewone client die vóór de load balancer is). Dit kan worden opgelost door het bijwerken van het "hosts"-bestand onder "C:\Windows\System32\drivers\etc" om het IP-adres van de AD FS server of een loop back-IP-adres (127.0.0.1) voor de naam van de AD FS Farm (zoals sts.contoso.com) op te laten bevatten. Het toevoegen van het hostbestand verkort de netwerk aanroep, waardoor de status agent het token kan ophalen.

**V: Ik heb een e-mail ontvangen waarin staat dat mijn computers niet worden gerepareerd voor de recente Ransomware-aanvallen. Waarom heb ik dit e-mail bericht?**

Azure AD Connect Health-Service heeft alle machines gescand die worden bewaakt om ervoor te zorgen dat de vereiste patches zijn geïnstalleerd. Het e-mail bericht is verzonden naar de Tenant beheerders als ten minste één computer geen essentiële patches heeft. De volgende logica is gebruikt om deze bepaling te maken.
1. Alle hotfixes zoeken die op de computer zijn geïnstalleerd.
2. Controleer of er ten minste één van de HotFixes uit de gedefinieerde lijst aanwezig is.
3. Zo ja, dan is de computer beveiligd. Als dat niet het geval is, is de computer kwetsbaar voor de aanval.

U kunt het volgende Power shell-script gebruiken om deze controle hand matig uit te voeren. De bovenstaande logica wordt geïmplementeerd.

```powershell
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```

**V: Waarom worden de Power shell <i>-cmdlet Get-MsolDirSyncProvisioningError</i> minder synchronisatie fouten weer gegeven in het resultaat?**

<i>Get-MsolDirSyncProvisioningError</i> retourneert alleen DirSync-inrichtings fouten. Naast de status van de Connect Health-Portal worden ook andere synchronisatie fout typen weer gegeven, zoals export fouten. Dit is consistent met Azure AD Connect Delta resultaat. Lees meer over [Azure AD Connect synchronisatie fouten](./tshoot-connect-sync-errors.md).

**V: Waarom worden er geen ADFS-controles gegenereerd?**

Gebruik de Power shell-cmdlet <i>Get-AdfsProperties-audit level</i> om te controleren of de controle logboeken niet zijn uitgeschakeld. Lees meer over [AD FS-controle logboeken](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016). Als er geavanceerde controle-instellingen zijn gepusht naar de ADFS-server, worden wijzigingen met auditpol.exe overschreven (gebeurtenis als de toepassing is gegenereerd, is niet geconfigureerd). In dit geval stelt u het lokale beveiligings beleid in op het vastleggen van gegenereerde fouten en geslaagde toepassingen.

**V: wanneer wordt het agent certificaat automatisch vernieuwd voordat het verloopt?**
De agent certificering wordt automatisch verlengd **6 maanden** voor de verval datum. Als deze niet wordt vernieuwd, zorgt u ervoor dat de netwerk verbinding van de agent stabiel is. Het probleem kan ook worden opgelost door de agent services opnieuw te starten of naar de nieuwste versie bij te werken.



## <a name="related-links"></a>Verwante koppelingen
* [Azure AD Connect Health (Engelstalig)](./whatis-azure-ad-connect.md)
* [Installatie van Azure AD Connect Health-Agent](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health bewerkingen](how-to-connect-health-operations.md)
* [Azure AD Connect Health gebruiken met AD FS](how-to-connect-health-adfs.md)
* [Azure AD Connect Health gebruiken voor synchronisatie](how-to-connect-health-sync.md)
* [Azure AD Connect Health gebruiken met AD DS](how-to-connect-health-adds.md)
* [Versiegeschiedenis van Azure AD Connect Health](reference-connect-health-version-history.md)
