---
title: Azure Active Directory Domain Services verwijderen | Microsoft Docs
description: Meer informatie over het uitschakelen of verwijderen van een Azure Active Directory Domain Services beheerd domein met behulp van de Azure Portal
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: a5126abd6643eba7f63b2bf4ca984bb9892b2d7a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619807"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Een door Azure Active Directory Domain Services beheerd domein verwijderen met de Azure Portal

Als u een door Azure Active Directory Domain Services (Azure AD DS) beheerd domein niet meer nodig hebt, kunt u het verwijderen. Er is geen optie voor het uitschakelen of tijdelijk uitschakelen van een door Azure AD DS beheerd domein. Als u het beheerde domein verwijdert, wordt de Azure AD-Tenant niet verwijderd.

Dit artikel laat u zien hoe u de Azure Portal kunt gebruiken om een beheerd domein te verwijderen.

> [!WARNING]
> **Verwijdering is permanent en kan niet worden omgekeerd.**
> 
> Wanneer u een beheerd domein verwijdert, worden de volgende stappen uitgevoerd:
>   * Domein controllers voor het beheerde domein worden ongedaan gemaakt en uit het virtuele netwerk verwijderd.
>   * Gegevens op het beheerde domein worden permanent verwijderd. Deze gegevens omvatten aangepaste organisatie-eenheden, Gpo's, aangepaste DNS-records, service-principals, Gmsa's, enzovoort die u hebt gemaakt.
>   * Computers die zijn gekoppeld aan het beheerde domein, verliezen hun vertrouwens relatie met het domein en moeten uit het domein worden verwijderd.
>       * U kunt zich niet aanmelden bij deze computers met behulp van de referenties voor bedrijfs AD. In plaats daarvan moet u de lokale beheerders referenties voor de computer gebruiken.

## <a name="delete-the-managed-domain"></a>Het beheerde domein verwijderen

Als u een beheerd domein wilt verwijderen, voert u de volgende stappen uit:

1. Zoek en selecteer **Azure AD Domain Services** in de Azure-portal.
1. Selecteer de naam van uw beheerde domein, zoals *aaddscontoso.com*.
1. Selecteer **Verwijderen** op de pagina **Overzicht**. Om het verwijderen te bevestigen, typt u de domein naam van het beheerde domein opnieuw en selecteert u vervolgens **verwijderen**.

Het kan 15-20 minuten of langer duren voordat het beheerde domein is verwijderd.

## <a name="next-steps"></a>Volgende stappen

Overweeg [feedback te delen][feedback] voor de functies die u wilt zien in azure AD DS.

Zie [een Azure Active Directory Domain Services beheerd domein maken en configureren][create-instance]als u opnieuw aan de slag wilt gaan met Azure AD DS.

<!-- INTERNAL LINKS -->
[feedback]: https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160593%3fcategory_id%3d160593
[create-instance]: tutorial-create-instance.md