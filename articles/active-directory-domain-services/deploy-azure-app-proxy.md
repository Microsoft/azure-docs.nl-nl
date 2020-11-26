---
title: Azure AD-toepassingsproxy implementeren voor Azure AD Domain Services | Microsoft Docs
description: Meer informatie over het bieden van beveiligde toegang tot interne toepassingen voor externe werk nemers door het implementeren en configureren van Azure Active Directory-toepassingsproxy in een Azure Active Directory Domain Services beheerd domein
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: 7c30bca947788e93e57f462094d51f00e338c0b8
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96171650"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-active-directory-domain-services-managed-domain"></a>Azure AD-toepassingsproxy implementeren voor beveiligde toegang tot interne toepassingen in een Azure Active Directory Domain Services beheerd domein

Met Azure AD Domain Services (Azure AD DS) kunt u verouderde toepassingen die on-premises worden uitgevoerd, optillen naar Azure. Azure Active Directory-toepassings proxy (AD) helpt u vervolgens externe werk nemers te ondersteunen door de interne toepassingen die deel uitmaken van een Azure AD DS beheerd domein veilig te publiceren zodat ze via internet kunnen worden benaderd.

Als u geen ervaring hebt met de Azure-AD-toepassingsproxy en meer informatie wilt, raadpleegt u [veilige externe toegang tot interne toepassingen bieden](../active-directory/manage-apps/application-proxy.md).

Dit artikel laat u zien hoe u een Azure AD-toepassingsproxy-connector kunt maken en configureren om beveiligde toegang te bieden tot toepassingen in een beheerd domein.

## <a name="before-you-begin"></a>Voordat u begint

U hebt de volgende resources en bevoegdheden nodig om dit artikel te volt ooien:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
    * Een **Azure AD Premium licentie** is vereist voor het gebruik van de Azure AD-toepassingsproxy.
* Een door Azure Active Directory Domain Services beheerd domein dat in uw Azure AD-tenant is ingeschakeld en geconfigureerd.
    * [Maak en configureer zo nodig een door Azure Active Directory Domain Services beheerd domein][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>Een aan een domein gekoppelde Windows-VM maken

Als u verkeer wilt door sturen naar toepassingen die in uw omgeving worden uitgevoerd, installeert u het onderdeel Azure AD-toepassingsproxy-connector. Deze Azure AD-toepassingsproxy-connector moet worden geïnstalleerd op een virtuele Windows Server-machine (VM) die is gekoppeld aan het beheerde domein. Voor sommige toepassingen kunt u meerdere servers implementeren waarop elke connector is geïnstalleerd. Deze implementatie optie biedt meer Beschik baarheid en helpt zwaarere verificatie belasting te verwerken.

De virtuele machine waarop de Azure AD-toepassingsproxy-connector wordt uitgevoerd, moet zich op dezelfde of een peered virtueel netwerk als uw beheerde domein. De virtuele machines die vervolgens worden gehost op de toepassingen die u publiceert met behulp van de toepassings proxy, moeten ook worden geïmplementeerd op hetzelfde virtuele Azure-netwerk.

Voer de volgende stappen uit om een virtuele machine voor de Azure AD-toepassingsproxy-connector te maken:

1. [Een aangepaste organisatie-eenheid maken](create-ou.md). U kunt machtigingen voor het beheren van deze aangepaste organisatie-eenheid overdragen aan gebruikers binnen het beheerde domein. De Vm's voor Azure AD-toepassingsproxy en die uw toepassingen uitvoeren, moeten deel uitmaken van de aangepaste organisatie-eenheid, niet de standaard-organisatie-eenheid voor *Aad DC-computers* .
1. [Domein: Voeg de virtuele machines samen][create-join-windows-vm], zowel die van de Azure AD-toepassingsproxy-connector als de computers die uw toepassingen uitvoeren, aan het beheerde domein. Maak deze computer accounts in de aangepaste organisatie-eenheid uit de vorige stap.

## <a name="download-the-azure-ad-application-proxy-connector"></a>De Azure AD-toepassingsproxy-connector downloaden

Voer de volgende stappen uit om de Azure AD-toepassingsproxy-connector te downloaden. Het installatie bestand dat u downloadt, wordt in de volgende sectie gekopieerd naar uw app proxy-VM.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een gebruikers account met *beheerders* machtigingen voor ondernemingen in azure AD.
1. Zoek en selecteer **Azure Active Directory** bovenaan de portal en kies vervolgens **bedrijfs toepassingen**.
1. Selecteer **toepassings proxy** in het menu aan de linkerkant. Als u uw eerste connector wilt maken en app-proxy wilt inschakelen, selecteert u de koppeling om **een connector te downloaden**.
1. Accepteer de licentie voorwaarden en de privacyverklaring op de download pagina en selecteer vervolgens **voor waarden accepteren & downloaden**.

    ![De Azure AD-app proxy connector downloaden](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>De Azure AD-toepassingsproxy-connector installeren en registreren

Wanneer een virtuele machine gereed is om te worden gebruikt als Azure AD-toepassingsproxy-connector, kopieert u het installatie bestand dat u hebt gedownload van de Azure Portal en voert u het uit.

1. Kopieer het installatie bestand voor de Azure AD-toepassingsproxy-connector naar uw VM.
1. Voer het installatie bestand uit, zoals *AADApplicationProxyConnectorInstaller.exe*. Ga akkoord met de licentie voorwaarden van de software.
1. Tijdens de installatie wordt u gevraagd de connector te registreren bij de toepassings proxy in uw Azure AD-adres lijst.
   * Geef de referenties voor een globale beheerder op in uw Azure AD-adres lijst. De referenties van de globale beheerder van Azure AD kunnen afwijken van uw Azure-referenties in de portal

        > [!NOTE]
        > Het globale beheerders account dat wordt gebruikt voor het registreren van de connector, moet behoren tot dezelfde map als waarin u de Application proxy-service inschakelt.
        >
        > Als het Azure AD-domein bijvoorbeeld *contoso.com* is, moet de globale beheerder `admin@contoso.com` of een andere geldige alias in dat domein zijn.

   * Als verbeterde beveiliging van Internet Explorer is ingeschakeld voor de virtuele machine waarop u de connector installeert, wordt het registratie scherm mogelijk geblokkeerd. Als u toegang wilt toestaan, volgt u de instructies in het fout bericht of schakelt u de verbeterde beveiliging van Internet Explorer uit tijdens het installatie proces.
   * Zie [problemen met toepassings proxy oplossen](../active-directory/manage-apps/application-proxy-troubleshoot.md)als de registratie van de connector mislukt.
1. Aan het einde van de installatie wordt een notitie weer gegeven voor omgevingen met een uitgaande proxy. Als u de Azure AD-toepassingsproxy-connector wilt configureren om te werken via de uitgaande proxy, voert u het meegeleverde script uit, zoals `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1` .
1. Op de pagina Toepassings proxy in de Azure Portal wordt de nieuwe connector weer gegeven met de status *actief*, zoals wordt weer gegeven in het volgende voor beeld:

    ![De nieuwe Azure AD-toepassingsproxy-connector wordt weer gegeven als actief in de Azure Portal](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Om hoge Beschik baarheid te bieden voor toepassingen die worden geverifieerd via de Azure AD-toepassingsproxy, kunt u connectors op meerdere Vm's installeren. Herhaal de stappen in de vorige sectie om de connector te installeren op andere servers die zijn gekoppeld aan het beheerde domein.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>Op resources gebaseerde Kerberos-beperkte delegatie inschakelen

Als u eenmalige aanmelding wilt gebruiken voor uw toepassingen met geïntegreerde Windows-verificatie (IWA), geeft u de Azure AD-toepassingsproxy connectors toestemming om gebruikers te imiteren en tokens namens hen te verzenden en ontvangen. Als u deze machtigingen wilt verlenen, configureert u Kerberos-beperkte delegering (KCD) voor de connector om toegang te krijgen tot bronnen in het beheerde domein. Aangezien u geen domein beheerders rechten hebt in een beheerd domein, kunnen traditionele KCD op account niveau niet worden geconfigureerd in een beheerd domein. Gebruik in plaats daarvan KCD op basis van resources.

Zie [Configure Kerberos-beperkte delegering (KCD) in azure Active Directory Domain Services](deploy-kcd.md)voor meer informatie.

> [!NOTE]
> U moet zijn aangemeld bij een gebruikers account dat lid is van de groep *Azure AD DC-Administrators* in uw Azure AD-Tenant om de volgende Power shell-cmdlets uit te voeren.
>
> De computer accounts voor de virtuele machine van de app-connector en toepassings-Vm's moeten zich in een aangepaste OE bevinden, waar u machtigingen hebt voor het configureren van op resources gebaseerde KCD. U kunt geen op resources gebaseerde KCD configureren voor een computer account in de container ingebouwde *Aad DC-computers* .

Gebruik [Get-ADComputer][Get-ADComputer] om de instellingen op te halen voor de computer waarop de Azure AD-toepassingsproxy-connector is geïnstalleerd. Voer de volgende cmdlets uit vanaf de virtuele machine voor het beheer van het domein en u bent aangemeld als gebruikers account dat lid is van de groep *Azure AD DC-Administrators* .

In het volgende voor beeld wordt informatie opgehaald over het computer account met de naam *appproxy.aaddscontoso.com*. Geef uw eigen computer naam op voor de Azure AD-toepassingsproxy-VM die u in de vorige stappen hebt geconfigureerd.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

Gebruik de Power shell-cmdlet [set-ADComputer][Set-ADComputer] voor elke toepassings server waarop de apps achter Azure worden uitgevoerd AD-toepassingsproxy voor het configureren van KCD op basis van een resource. In het volgende voor beeld worden de Azure AD-toepassingsproxy-connector machtigingen verleend voor het gebruik van de *appserver.aaddscontoso.com* -computer:

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

Als u meerdere Azure AD-toepassingsproxy-connectors implementeert, moet u op resources gebaseerde KCD configureren voor elk connector exemplaar.

## <a name="next-steps"></a>Volgende stappen

Met Azure AD-toepassingsproxy geïntegreerd met Azure AD DS kunt u toepassingen publiceren zodat gebruikers er toegang toe hebben. Zie [toepassingen publiceren met Azure AD-toepassingsproxy](../active-directory/manage-apps/application-proxy-add-on-premises-application.md)voor meer informatie.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer