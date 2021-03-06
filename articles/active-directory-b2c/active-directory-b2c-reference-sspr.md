---
title: Réinitialisation de mot de passe en libre-service | Microsoft Docs
description: Explique comment configurer la réinitialisation de mot de passe en libre-service pour vos clients dans Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/17/2018
ms.author: davidmu
ms.openlocfilehash: 5b75455ad604b594a5f85fea8299d35a7d02c848
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2018
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Configurer la réinitialisation de mot de passe en libre-service pour vos clients
La fonctionnalité de réinitialisation de mot de passe en libre-service permet à vos clients qui se sont inscrits pour obtenir des comptes locaux de réinitialiser eux-mêmes leurs mots de passe. Cela réduit considérablement la charge pesant sur votre personnel de support, surtout si votre application est utilisée régulièrement par des millions de clients. Pour l’instant, l’utilisation d’une adresse e-mail vérifiée est la seule méthode de récupération prise en charge.

> [!NOTE]
> Cet article s’applique à un mot de passe libre-service utilisé dans le contexte d’une stratégie de connexion. Si vous avez besoin de stratégies de réinitialisation de mot de passe entièrement personnalisables appelées à partir de votre application, consultez [cet article](active-directory-b2c-reference-policies.md#create-a-password-reset-policy).
> 
> 

Par défaut, la réinitialisation de mot de passe en libre-service n’est pas activée pour votre répertoire. Pour l’activer, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur d’abonnements. Il s’agit du compte professionnel ou scolaire, ou du compte Microsoft que vous avez utilisé pour créer votre annuaire.
2. Ouvrez **Azure Active Directory** (dans la barre de navigation sur le côté gauche).
4. Définissez l’option **Réinitialisation du mot de passe en libre-service activée** sur **Tout**. 
5. Cliquez sur **Enregistrer** dans la partie supérieure de la page. Vous avez terminé !

Pour tester, utilisez la fonctionnalité « Exécuter maintenant » sur une stratégie de connexion (qui comporte des comptes locaux en tant que fournisseur d’identité). Dans la page de connexion au compte local (où vous entrez une adresse e-mail et un mot de passe, ou un nom d’utilisateur et un mot de passe), cliquez sur **Votre compte n’est pas accessible ?** pour vérifier l’expérience client.

> [!NOTE]
> Les pages de réinitialisation de mot de passe libre-service sont personnalisables à l’aide de la [fonctionnalité de personnalisation de la société](../active-directory/customize-branding.md).
> 
> 

