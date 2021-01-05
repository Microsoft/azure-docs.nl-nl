---
title: Persoonlijke Cloud bevoegdheden escaleren
titleSuffix: Azure VMware Solution by CloudSimple
description: Beschrijft hoe u bevoegdheden in uw privécloud kunt escaleren voor beheer functies in vCenter
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c2215bd9a60fdcf76077c3a1f2a91631dc0dbe88
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895819"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Persoonlijke Cloud vCenter-bevoegdheden escaleren vanuit de CloudSimple-Portal

Voor beheerders toegang tot uw persoonlijke Cloud-vCenter kunt u uw CloudSimple-bevoegdheden tijdelijk escaleren.  Met verhoogde bevoegdheden kunt u VMware-oplossingen installeren, identiteits bronnen toevoegen en gebruikers beheren.

Nieuwe gebruikers kunnen worden gemaakt in het vCenter-SSO-domein en hebben toegang tot de vCenter.  Wanneer u nieuwe gebruikers maakt, voegt u deze toe aan de CloudSimple ingebouwde groepen voor toegang tot vCenter.  Zie [CloudSimple Private Cloud permission model van VMware vCenter](./learn-private-cloud-permissions.md)(Engelstalig) voor meer informatie.

> [!CAUTION]
> Breng geen wijzigingen aan in de configuratie voor beheer onderdelen. Acties die worden uitgevoerd tijdens de status van de escalated privilege kunnen een nadelige invloed hebben op uw systeem of ervoor zorgen dat uw systeem niet meer beschikbaar is.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Bevoegdheden escaleren

1. Toegang tot de [CloudSimple-Portal](access-cloudsimple-portal.md).

2. Open de pagina **resources** , selecteer de privécloud waarvoor u bevoegdheden wilt escaleren.

3. Klik onder aan de pagina samen vatting onder **vSphere bevoegdheden wijzigen** op **escaleren**.

    ![VSphere-bevoegdheid wijzigen](media/escalate-private-cloud-privilege.png)

4. Selecteer het gebruikers type vSphere.  Alleen `CloudOwner@cloudsimple.local` lokale gebruikers kunnen worden geëscaleerd.

5. Selecteer de tijds interval voor escaleren in de vervolg keuzelijst. Kies de kortste periode waarmee u de taak kunt volt ooien.

6. Schakel het selectie vakje in om te bevestigen dat u de Risico's begrijpt.

    ![Het dialoog venster bevoegdheden escaleren](media/escalate-private-cloud-privilege-dialog.png)

7. Klik op **OK**.

8. Het escalatie proces kan een paar minuten duren. Na het voltooien klikt u op **OK**.

De escalatie van bevoegdheden begint en eindigt tot het einde van het geselecteerde interval.  U kunt zich aanmelden bij de privécloud van uw persoonlijke Cloud om beheer taken uit te voeren.

> [!IMPORTANT]
> Er kan slechts één gebruiker escalated privileges hebben.  U moet de bevoegdheden van de gebruiker deactiveren voordat u de bevoegdheden van een andere gebruiker kunt escaleren.

> [!CAUTION]
> Nieuwe gebruikers moeten alleen worden toegevoegd aan de *Cloud-eigenaar-groep*, *Cloud-Global-cluster-admin groep*, *Cloud-Global-Storage-admin-Group*, Cloud-Global: *Network-Administrator-* Group of Cloud-Global:- *beheer groep*.  Gebruikers die zijn toegevoegd aan de groep *Administrators* , worden automatisch verwijderd.  Alleen service accounts moeten worden toegevoegd aan de groep *Administrators* en service accounts moeten worden gebruikt om u aan te melden bij de vSphere-webgebruikersinterface.

## <a name="extend-privilege-escalation"></a>Escalatie van bevoegdheden uitbreiden

Als u meer tijd nodig hebt om uw taken te volt ooien, kunt u de escalatie periode van de bevoegdheid uitbreiden.  Kies het extra tijds interval voor het escaleren waarmee u de beheer taken kunt volt ooien.

1. Selecteer op de **resources**  >  **persoonlijke** Clouds in de CloudSimple-Portal de privécloud waarvoor u de escalatie van bevoegdheden wilt uitbreiden.

2. Klik onder aan het tabblad samen vatting op **uitbrei ding van bevoegdheden uitbreiden**.

    ![Escalatie van bevoegdheden uitbreiden](media/de-escalate-private-cloud-privilege.png)

3. Selecteer een tijds interval voor escaleren in de vervolg keuzelijst. Bekijk de nieuwe eind tijd van de escalatie.

4. Klik op **Opslaan** om het interval uit te breiden.

## <a name="de-escalate-privileges"></a>De bevoegdheden voor het escaleren

Als uw beheer taken zijn voltooid, moet u uw bevoegdheden deactiveren.  

1. Selecteer op de **resources**  >  **persoonlijke** Clouds in de CloudSimple-Portal de privécloud waarvoor u de bevoegdheden wilt deescaleren.

2. Klik op **deactiveren**.

3. Klik op **OK**.

> [!IMPORTANT]
> Als u fouten wilt voor komen, meldt u zich af bij vCenter en meldt u zich opnieuw aan na de deescalatie van bevoegdheden.

## <a name="next-steps"></a>Volgende stappen

* [VCenter-identiteits bronnen instellen voor het gebruik van Active Directory](./set-vcenter-identity.md)
* Back-upoplossing installeren voor [virtuele machines met een back-up van werk belasting](./backup-workloads-veeam.md)
