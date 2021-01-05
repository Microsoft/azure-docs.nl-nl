---
title: Apparaten beheren met de Azure Portal | Microsoft Docs
description: Meer informatie over het gebruik van de Azure Portal voor het beheren van apparaten.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 09/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: hafowler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f142aa4ca203a65c45741f87853579ca60e1879
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831751"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Apparaat-id's beheren in Azure Portal

Azure AD biedt een centrale plek voor het beheren van apparaat-id's.

Op de pagina **alle apparaten** kunt u het volgende doen:

- Apparaten identificeren, zoals:
   - Apparaten die zijn toegevoegd aan of geregistreerd in azure AD.
   - Apparaten die zijn geïmplementeerd met [Windows auto pilot](/windows/deployment/windows-autopilot/windows-autopilot).
   - Printers met [universeel afdrukken](/universal-print/fundamentals/universal-print-getting-started)
- U kunt taken voor het beheren van apparaat-id's uitvoeren, zoals inschakelen, uitschakelen, verwijderen of beheren.
   - [Printers](/universal-print/fundamentals/) en [Windows auto pilot](/windows/deployment/windows-autopilot/windows-autopilot) -apparaten hebben beperkte beheer opties in azure AD. Ze moeten worden beheerd vanuit hun respectieve beheer interfaces.
- Configureer de instellingen voor uw apparaat-id.
- Enterprise State Roaming in-of uitschakelen.
- Audit logboeken met betrekking tot het apparaat controleren

[![Weer gave alle apparaten in de Azure Portal](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox)

U kunt de portal voor apparaten openen met de volgende stappen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Blader naar **Azure Active Directory**  >  **apparaten**.

## <a name="manage-devices"></a>Apparaten beheren

Er zijn twee locaties voor het beheren van apparaten in azure AD:

- **Azure Portal**  >  **Azure Active Directory**  >  **Apparaten**
- **Azure Portal**  >  **Azure Active Directory**  >  **Gebruikers** > een gebruiker selecteren > **apparaten**

Met beide opties kunnen beheerders het volgende doen:

- Apparaten zoeken.
- Bekijk de details van het apparaat, waaronder:
    - Apparaatnaam
    - Apparaat-ID
    - Besturings systeem en versie
    - Relatietype
    - Eigenaar
    - Beheer en naleving van mobiele apparaten
    - BitLocker-herstel sleutel
- Beheer taken voor apparaat-id's, zoals inschakelen, uitschakelen, verwijderen of beheren.
   - [Printers](/universal-print/fundamentals/) en [Windows auto pilot](/windows/deployment/windows-autopilot/windows-autopilot) -apparaten hebben beperkte beheer opties in azure AD. Ze moeten worden beheerd vanuit hun respectieve beheer interfaces.

> [!TIP]
> - Hybride Azure AD-gekoppelde Windows 10-apparaten hebben geen eigenaar. Als u op zoek bent naar een apparaat op basis van de eigenaar en u dit niet hebt gevonden, zoekt u op de apparaat-ID.
>
> - Als u een apparaat ziet dat ' hybride Azure AD join ' met de status ' in behandeling ' heeft in de kolom geregistreerd, geeft dit aan dat het apparaat is gesynchroniseerd vanuit Azure AD Connect en wacht op het volt ooien van de registratie van de client. Meer informatie over het [plannen van uw hybride Azure AD-koppelings implementatie](hybrid-azuread-join-plan.md). Meer informatie vindt u in het artikel, [Veelgestelde vragen over apparaten](faq.md).
>
> - Voor sommige iOS-apparaten kunnen de apparaatnamen die apostrofs bevatten mogelijk andere tekens gebruiken die eruitzien als apostrofs. Het is dus lastig om dergelijke apparaten te doorzoeken: als u de zoek resultaten niet op de juiste manier ziet, moet u ervoor zorgen dat de zoek reeks overeenkomt met het apostrof-teken.

### <a name="manage-an-intune-device"></a>Een intune-apparaat beheren

Als u intune-beheerder bent, kunt u apparaten beheren waarop MDM is gemarkeerd **Microsoft intune**. Als het apparaat niet is inge schreven bij Microsoft Intune, wordt de optie beheren grijs weer gegeven.

### <a name="enable-or-disable-an-azure-ad-device"></a>Een Azure AD-apparaat in-of uitschakelen

Als u apparaten wilt in-of uitschakelen, hebt u twee opties:

- De werk balk op de pagina **alle apparaten** nadat u een of meer apparaten hebt geselecteerd.
- De werk balk na inzoomen op een specifiek apparaat.

> [!IMPORTANT]
> - U moet een globale beheerder of een beheerder van een Cloud apparaat in azure AD zijn om een apparaat in of uit te scha kelen. 
> - Als u een apparaat uitschakelt, kan een apparaat niet worden geverifieerd met Azure AD, waardoor wordt voor komen dat het apparaat toegang heeft tot uw Azure AD-resources die zijn beveiligd met voorwaardelijke toegang op basis van apparaten of met behulp van Windows hello voor bedrijven-referenties.
> - Als u een apparaat uitschakelt, worden zowel het primaire vernieuwings token (PRT) als een vernieuwings token (RT) op het apparaat ingetrokken.
> - Printers kunnen niet worden in-of uitgeschakeld in azure AD.

### <a name="delete-an-azure-ad-device"></a>Een Azure AD-apparaat verwijderen

Als u een apparaat wilt verwijderen, hebt u twee opties:

- De werk balk op de pagina **alle apparaten** nadat u een of meer apparaten hebt geselecteerd.
- De werk balk na inzoomen op een specifiek apparaat.

> [!IMPORTANT]
> - U moet de rol beheerder van Cloud apparaat, intune-beheerder of globale beheerder in azure AD toewijzen om een apparaat te kunnen verwijderen.
> - Printers en Windows auto pilot-apparaten kunnen niet worden verwijderd in azure AD
> - Een apparaat verwijderen:
>    - Hiermee voor komt u dat een apparaat toegang heeft tot uw Azure AD-resources.
>    - Hiermee verwijdert u alle details die zijn gekoppeld aan het apparaat, bijvoorbeeld BitLocker-sleutels voor Windows-apparaten.  
>    - Vertegenwoordigt een niet-herstel bare activiteit en wordt niet aanbevolen, tenzij dit vereist is.

Als een apparaat wordt beheerd door een andere beheer instantie (bijvoorbeeld Microsoft Intune), moet u ervoor zorgen dat het apparaat is gewist/buiten gebruik is gesteld voordat het apparaat in azure AD wordt verwijderd. Lees hoe u [verouderde apparaten beheert](manage-stale-devices.md) voordat u apparaten verwijdert.

### <a name="view-or-copy-device-id"></a>Apparaat-ID weer geven of kopiëren

U kunt een apparaat-ID gebruiken om de details van de apparaat-ID op het apparaat te controleren of door Power shell te gebruiken tijdens het oplossen van problemen. Als u de Kopieer optie wilt openen, klikt u op het apparaat.

![Een apparaat-ID weer geven](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>BitLocker-sleutels weer geven of kopiëren

U kunt de BitLocker-sleutels weer geven en kopiëren zodat gebruikers versleutelde stations kunnen herstellen. Deze sleutels zijn alleen beschikbaar voor Windows-apparaten die zijn versleuteld en waarvan de sleutels zijn opgeslagen in azure AD. U kunt deze sleutels vinden wanneer u gegevens van een apparaat opent door **herstel sleutel weer geven** te selecteren. Als u **herstel sleutel weer geven** selecteert, wordt er een controle logboek gegenereerd, dat u in de categorie kunt vinden `KeyManagement` .

![BitLocker-sleutels weer geven](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

Als u de BitLocker-sleutels wilt bekijken of kopiëren, moet u eigenaar zijn van het apparaat of een gebruiker zijn die ten minste een van de volgende rollen heeft:

- Cloudapparaatbeheerder
- Hoofdbeheerder
- Helpdeskbeheerder
- Intune-servicebeheerder
- Beveiligingsbeheer
- Beveiligingslezer

### <a name="device-list-filtering-preview"></a>Filteren van apparaten lijst (preview-versie)

Voorheen kon u de lijst met apparaten alleen filteren op activiteit en status ingeschakeld. Met deze preview-versie kunt u nu de lijst met apparaten filteren op de volgende kenmerken op een apparaat:

- Ingeschakelde status
- Status conform
- Koppelings type (Azure AD toegevoegd, hybride Azure AD toegevoegd, Azure AD geregistreerd)
- Tijdstempel van activiteit
- Besturingssysteem
- Apparaattype (printers, beveiligde Vm's, gedeelde apparaten, geregistreerde apparaten)

De functie voor beeld filtering inschakelen in de weer gave **alle apparaten** :

![Preview-functionaliteit voor filteren inschakelen](./media/device-management-azure-portal/device-filter-preview-enable.png)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Blader naar **Azure Active Directory**  >  **apparaten**.
1. Selecteer de banner waarin wordt **vermeld, probeer de nieuwe verbeteringen voor het filteren van apparaten uit. Klik om de preview-versie in te scha kelen.**

U kunt nu **filters toevoegen** aan uw weer gave **alle apparaten** .

## <a name="configure-device-settings"></a>Apparaatinstellingen configureren

Als u apparaat-id's wilt beheren met de Azure AD-Portal, moeten die apparaten zijn [geregistreerd of zijn gekoppeld](overview.md) aan Azure AD. Als beheerder kunt u het proces voor het registreren en toevoegen van apparaten beheren door de volgende apparaatinstellingen te configureren.

U moet een van de volgende rollen zijn toegewezen om Apparaatinstellingen weer te geven of te beheren in de Azure Portal:

- Globale beheerder
- Beheerder van Cloud apparaat
- Algemene lezer
- Directory-lezer

![Apparaatinstellingen met betrekking tot Azure AD](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **Gebruikers kunnen apparaten toevoegen aan Azure AD** : met deze instelling kunt u de gebruikers selecteren die hun apparaten als aan Azure AD gekoppelde apparaten kunnen registreren. De standaardwaarde is **alle**.

> [!NOTE]
> **Gebruikers mogen apparaten toevoegen aan de Azure AD** -instelling is alleen van toepassing op Azure AD-deelname op Windows 10.

- **Aanvullende lokale beheerders op aan Azure AD gekoppelde apparaten** : u kunt de gebruikers selecteren die lokale beheerders rechten op een apparaat krijgen. Deze gebruikers worden toegevoegd aan de rol van het *apparaat Administrators* in azure AD. Globale beheerders in azure AD en eigen aren van apparaten krijgen standaard lokale beheerders rechten. Deze optie is een Premium Edition-mogelijkheid die beschikbaar is via producten als Azure AD Premium of de Enter prise Mobility Suite (EMS).
- **Gebruikers kunnen hun apparaten registreren bij Azure AD** . u moet deze instelling configureren zodat Windows 10 Personal-, Ios-, Android-en macOS-apparaten kunnen worden geregistreerd bij Azure AD. Als u **geen** selecteert, mogen apparaten zich niet registreren bij Azure AD. Inschrijving met Microsoft Intune of het beheer van mobiele apparaten (MDM) voor Microsoft 365 vereist registratie. Als u een van deze services hebt geconfigureerd, is **Alles** **geselecteerd en is er geen beschikbaar** .
- **Multi-factor Authentication vereisen voor het toevoegen van apparaten** : u kunt kiezen of gebruikers een extra verificatie factor moeten opgeven om toe te voegen aan Azure AD of hun apparaat te registreren. De standaard waarde is **Nee**. U wordt aangeraden multi-factor Authentication te vereisen bij het registreren of toevoegen van een apparaat. Voordat u multi-factor Authentication inschakelt voor deze service, moet u ervoor zorgen dat multi-factor Authentication is geconfigureerd voor de gebruikers die hun apparaten registreren. Zie aan de slag [met Azure AD-multi-factor Authentication](../authentication/concept-mfa-howitworks.md)voor meer informatie over de verschillende azure AD multi-factor Authentication Services. 

> [!NOTE]
> **Vereisen dat multi-factor Authentication wordt toegevoegd** aan de instelling apparaten is van toepassing op apparaten die zijn toegevoegd aan Azure AD (met enkele uitzonde ringen) of Azure AD is geregistreerd. Deze instelling is niet van toepassing op hybride Azure AD gekoppelde apparaten, [Azure AD gekoppelde virtuele machines in azure](/azure/active-directory/devices/howto-vm-sign-in-azure-ad-windows#enabling-azure-ad-login-in-for-windows-vm-in-azure) en aan Azure AD gekoppelde apparaten met behulp van de [zelf-implementatie modus van Windows auto pilot](/mem/autopilot/self-deploying).

- **Maximum aantal apparaten** : met deze instelling kunt u het maximum aantal aan Azure AD gekoppelde of Azure AD geregistreerde apparaten selecteren dat een gebruiker in azure AD kan hebben. Als een gebruiker dit quotum bereikt, kunnen ze geen extra apparaten toevoegen totdat een of meer van de bestaande apparaten worden verwijderd. De standaard waarde is **50**.

> [!NOTE]
> De instelling **maximum aantal apparaten** is van toepassing op apparaten die zijn geregistreerd bij Azure AD of Azure AD. Deze instelling is niet van toepassing op hybride apparaten die deel uitmaken van Azure AD.

- [Enterprise State Roaming](enterprise-state-roaming-overview.md)

## <a name="audit-logs"></a>Auditlogboeken

Apparaat-activiteiten zijn beschikbaar via de activiteiten Logboeken. Deze logboeken bevatten activiteiten die worden geactiveerd door de Device Registration service en door gebruikers:

- Apparaten maken en toevoegen van eigen aren/gebruikers op het apparaat
- Wijzigingen in Apparaatinstellingen
- Apparaat-bewerkingen, zoals het verwijderen of bijwerken van een apparaat

Uw ingangs punt voor de controle gegevens is **controle logboeken** in het gedeelte **activiteit** van de pagina **apparaten** .

Het controle logboek heeft een standaard lijst weergave waarin het volgende wordt weer gegeven:

- De datum en tijd waarop de gebeurtenis is opgetreden
- De doelen
- De initiator/Actor (wie) van een activiteit
- De activiteit (wat)

:::image type="content" source="./media/device-management-azure-portal/63.png" alt-text="Scherm afbeelding van een tabel in de sectie activiteit van de pagina apparaten waarin de datum, het doel, de actor en de activiteit voor vier audit logboeken worden weer gegeven." border="false":::

U kunt de lijstweergave aanpassen door te klikken op **Kolommen** op de werkbalk.

:::image type="content" source="./media/device-management-azure-portal/64.png" alt-text="Scherm afbeelding van de werk balk van de pagina apparaten. Het kolommen item is gemarkeerd." border="false":::

Als u de gerapporteerde gegevens wilt beperken tot een niveau dat geschikt is voor u, kunt u de controlegegevens filteren met de volgende velden:

- Categorie
- Resourcetype van activiteit
- Activiteit
- Datumbereik
- Doel
- Gestart door (actor)

Naast de filters kunt u zoeken naar specifieke vermeldingen.

:::image type="content" source="./media/device-management-azure-portal/65.png" alt-text="Scherm afbeelding van filter besturings elementen voor controle gegevens, met categorie, resource type activiteit, activiteit, datum bereik, doel en actor velden en een zoek veld." border="false":::

## <a name="next-steps"></a>Volgende stappen

[Verouderde apparaten beheren in azure AD](manage-stale-devices.md)

[Enterprise State Roaming](enterprise-state-roaming-overview.md)
