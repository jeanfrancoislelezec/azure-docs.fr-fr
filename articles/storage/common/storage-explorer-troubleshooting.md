---
title: Guide de résolution des problèmes de l’Explorateur de stockage Azure | Microsoft Docs
description: Vue d’ensemble des deux fonctionnalités de débogage d’Azure
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: delhan
ms.openlocfilehash: 531ca6d781ae62aacd85dce600e3ea8b46ccf360
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32777075"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guide de résolution des problèmes de l’Explorateur de stockage Azure

L’Explorateur Stockage Microsoft Azure est une application autonome qui vous permet d’utiliser facilement les données du Stockage Azure sur Windows, macOS et Linux. L’application peut se connecter aux comptes de stockage hébergés sur Azure, sur les clouds nationaux et sur Azure Stack.

Ce guide résume les solutions aux problèmes couramment rencontrés dans l’Explorateur de stockage.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Erreur : certificat auto-signé dans la chaîne d’approbation (et erreurs similaires)

Les erreurs de certificat sont provoquées par une des deux situations suivantes :

1. L’application est connectée via un « proxy transparent » ; dans cette configuration, un serveur (par exemple, le serveur de votre société) intercepte le trafic HTTPS, le déchiffre, puis le chiffre à l’aide d’un certificat auto-signé.
2. Vous exécutez une application qui injecte un certificat SSL auto-signé dans les messages HTTPS que vous recevez. Les exemples d’applications qui injectent des certificats incluent un antivirus et un logiciel d’inspection de trafic réseau.

Lorsque l’Explorateur de stockage voit un certificat auto-signé ou non approuvé, il ne peut plus savoir si le message HTTPS reçu a été modifié. Si vous avez une copie du certificat auto-signé, vous pouvez ordonner à l’Explorateur de stockage de lui faire confiance en procédant comme suit :

1. Obtenez une copie Base-64 chiffrée X.509 (.cer) du certificat
2. Cliquez sur **Modifier** > **Certificats SSL** > **Importer les certificats**, puis utilisez le sélecteur de fichiers pour rechercher, sélectionner et ouvrir le fichier .cer

Ce problème peut également provenir de plusieurs certificats (racine et intermédiaires). Les deux certificats doivent être ajoutés pour résoudre l’erreur.

Si vous ne savez pas d'où provient le certificat, vous pouvez essayer de le savoir en procédant comme suit :

1. Installez Open SSL.

    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (n’importe quelle version légère devrait suffire)
    * Mac et Linux : doit être inclus dans votre système d’exploitation
2. Exécutez Open SSL.

    * Windows : ouvrez le répertoire d’installation, cliquez sur **/bin/**, puis double-cliquez sur **openssl.exe**.
    * Mac et Linux : exécutez **openssl** à partir d’un terminal.
3. Exécutez `s_client -showcerts -connect microsoft.com:443`
4. Recherchez les certificats auto-signés. Si vous ne savez pas avec certitude lesquels sont auto-signés, recherchez ceux dont le sujet `("s:")` et l’émetteur `("i:")` sont identiques.
5. Après avoir trouvé les certificats auto-signés pour chacun d’eux, copiez et collez tout depuis **---BEGIN CERTIFICATE---** jusqu’à **---END CERTIFICATE---** (les deux inclus) dans un nouveau fichier .cer.
6. Ouvrez l’Explorateur de stockage, cliquez sur **Modifier** > **Certificats SSL** > **Importer les certificats**, puis utilisez le sélecteur de fichiers pour rechercher, sélectionner et ouvrir les fichiers .cer que vous avez créés.

Si vous ne trouvez aucun certificat auto-signé à l’aide des étapes précédentes, contactez-nous par l’intermédiaire de l’outil de commentaires pour obtenir de l’aide. Vous pouvez choisir de lancer l’Explorateur de stockage à partir de la ligne de commande avec l’indicateur `--ignore-certificate-errors`. Une fois lancé avec cet indicateur, Explorateur de stockage ignore les erreurs de certificat.

## <a name="sign-in-issues"></a>Problèmes de connexion

Si vous ne parvenez pas à vous connecter, essayez les méthodes de résolution des problèmes suivantes :

* Si vous êtes sous macOS et que la fenêtre de connexion n’apparaît jamais sur la boîte de dialogue « En attente d’authentification... », suivez [ces étapes](#Resetting-the-Mac-Keychain).
* Redémarrez l’Explorateur de stockage
* Si la fenêtre d’authentification est vide, patientez au moins une minute avant de fermer la boîte de dialogue d’authentification.
* Vérifiez que vos paramètres de proxy et de certificat sont correctement configurés pour votre ordinateur et pour l’Explorateur de stockage
* Si vous êtes sur Windows et que vous avez accès à Visual Studio 2017 sur le même ordinateur et avec le même ID de connexion, essayez de vous connecter à Visual Studio 2017

Si aucune de ces méthodes ne fonctionne, [ouvrez un problème sur GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="unable-to-retrieve-subscriptions"></a>Impossible de récupérer les abonnements

Si vous ne parvenez pas à récupérer vos abonnements après vous être connecté avec succès, essayez l’une des méthodes de résolution de problèmes suivantes :

* Vérifiez que votre compte a accès aux abonnements attendus. Vous pouvez vérifier que vous avez accès en vous connectant au portail de l’environnement Azure que vous essayez d’utiliser.
* Assurez-vous que vous vous êtes connecté à l’aide de l’environnement approprié (Azure, Azure Chine, Azure Allemagne, Azure Gouvernement des États-Unis ou environnement personnalisé).
* Si vous vous trouvez derrière un proxy, vérifiez que vous avez correctement configuré le proxy de l’Explorateur de stockage.
* Essayez de supprimer et de rajouter le compte.
* Regardez la console Outils de développement (Aide > Activer/désactiver les outils de développement) pendant que l’Explorateur de stockage charge des abonnements. Recherchez des messages d’erreur (en rouge) ou un message contenant le texte « Impossible de charger des abonnements pour le locataire. » Si vous voyez des messages préoccupants, [ouvrez un ticket d’incident sur GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cannot-remove-attached-account-or-storage-resource"></a>Impossible de supprimer le compte ou la ressource de stockage joints

Si vous ne pouvez pas supprimer un compte joint ou une ressource de stockage joints via l’interface utilisateur, vous pouvez supprimer manuellement toutes les ressources jointes en supprimant les dossiers suivants :

* Windows : `%AppData%/StorageExplorer`
* MacOS : `/Users/<your_name>/Library/Applicaiton Support/StorageExplorer`
* Linux : `~/.config/StorageExplorer`

> [!NOTE]
>  Fermez l’Explorateur de stockage avant de supprimer les dossiers ci-dessus.

> [!NOTE]
>  Si vous avez importé des certificats SSL, sauvegardez le contenu du répertoire `certs`. Vous pourrez utiliser la sauvegarde plus tard pour réimporter vos certificats SSL.

## <a name="proxy-issues"></a>Problèmes de proxy

Tout d’abord, vérifiez que les informations suivantes que vous avez entrées sont toutes correctes :

* URL de proxy et numéro de port
* Nom d’utilisateur et mot de passe si requis par le proxy

### <a name="common-solutions"></a>Solutions courantes

Si vous rencontrez encore des problèmes, essayez les méthodes de résolution des problèmes suivantes :

* Si vous pouvez vous connecter à Internet sans utiliser votre proxy, vérifiez que l’Explorateur de stockage fonctionne sans les paramètres de proxy activés. Si c’est le cas, le dysfonctionnement est peut-être lié à vos paramètres de proxy. Contactez l’administrateur de votre proxy pour identifier les problèmes.
* Vérifiez que les autres applications utilisant le serveur proxy fonctionnent normalement.
* Vérifiez que vous pouvez vous connecter au portail de l’environnement Azure que vous essayez d’utiliser.
* Vérifiez que vous pouvez recevoir des réponses de vos points de terminaison de service. Entrez une des URL de point de terminaison dans votre navigateur. Si vous pouvez vous connecter, vous devez recevoir une réponse XML InvalidQueryParameterValue ou similaire.
* Si quelqu’un d’autre utilise également l’Explorateur de stockage avec votre serveur proxy, vérifiez que cette personne peut se connecter. Si elle le peut, vous devrez peut-être contacter l’administrateur de votre serveur proxy.

### <a name="tools-for-diagnosing-issues"></a>Outils pour diagnostiquer les problèmes

Si vous disposez d’outils de mise en réseau, tels que Fiddler pour Windows, vous pouvez peut-être diagnostiquer les problèmes comme suit :

* Si vous devez passer par votre serveur proxy, vous devez peut-être configurer votre outil de mise en réseau pour vous connecter via le proxy.
* Vérifiez le numéro de port utilisé par votre outil de mise en réseau.
* Entrez l’URL de l’hôte local et le numéro de port de l’outil de mise en réseau en tant que paramètres de proxy dans l’Explorateur de stockage. Si cette opération est effectuée correctement, l’outil de mise en réseau démarre la journalisation des demandes réseau effectuées par l’Explorateur de stockage sur les points de terminaison de service et de gestion. Par exemple, si vous entrez https://cawablobgrs.blob.core.windows.net/ pour votre point de terminaison d’objets blob dans un navigateur, vous recevez une réponse semblable à la suivante, qui suggère que la ressource existe, bien que vous ne puissiez pas y accéder.

![Exemple de code](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Contacter l’administrateur du serveur proxy

Si vos paramètres de proxy sont corrects, vous devrez peut-être contacter l’administrateur de votre serveur proxy, puis :

* Vérifier que votre proxy ne bloque pas le trafic vers les points de terminaison de gestion ou de ressources Azure.
* Vérifier le protocole d’authentification utilisé par votre serveur proxy. L’Explorateur de stockage ne prend pas en charge les proxys NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Message d’erreur indiquant qu’il est impossible de récupérer les enfants

Si vous êtes connecté à Azure par le biais d’un proxy, vérifiez que vos paramètres de proxy sont corrects. Si le propriétaire de l’abonnement ou du compte vous a accordé l’accès à une ressource, vérifiez que vous êtes habilité à lire ou à répertorier cette ressource.

## <a name="issues-with-sas-url"></a>Problèmes liés à l’URL SAP
Si vous vous connectez à un service à l’aide d’une URL SAP et que vous rencontrez cette erreur :

* Vérifiez que l’URL fournit les autorisations nécessaires pour lire ou répertorier les ressources.
* Vérifiez que l’URL n’a pas expiré.
* Si l’URL SAP est basée sur une stratégie d’accès, vérifiez que cette dernière n’a pas été révoquée.

Si vous avez attaché accidentellement une URL de SAP non valide et que vous n’arrivez pas à la détacher, suivez les étapes suivantes :
1.  Lors de l’exécution de l’Explorateur de stockage, appuyez sur F12 pour ouvrir la fenêtre Outils de développement.
2.  Cliquez sur l’onglet Application, puis cliquez sur Stockage local > file:// dans l’arborescence à gauche.
3.  Recherchez la clé associée au type de service de l’URI SAS problématique. Par exemple, si l’URI SAS incorrect concerne un conteneur d’objets blob, recherchez la clé nommée `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4.  La valeur de la clé doit être un tableau JSON. Recherchez l’objet associé à l’URI incorrecte, et supprimez-le.
5.  Appuyez sur Ctrl+R pour recharger l’Explorateur de stockage.

## <a name="linux-dependencies"></a>Dépendances Linux

Pour les distributions Linux autres que Ubuntu 16.04, vous devez peut-être installer manuellement quelques dépendances. En général, les packages suivants sont nécessaires :
* [.NET Core 2.x](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libsecret`
* `libgconf-2-4`
* GCC à jour

En fonction de votre distribution, vous devez peut-être installer d’autres packages. Les [Notes de publication](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) de l’Explorateur Stockage contiennent des étapes propres à certaines distributions.

## <a name="resetting-the-mac-keychain"></a>Réinitialiser le trousseau Mac
Il peut arriver que le trousseau macOS soit dans un état qui provoque des problèmes dans la bibliothèque d’authentification de l’Explorateur Stockage. Pour modifier l’état du trousseau, suivez ces étapes :
1. Fermez l’Explorateur Stockage.
2. Ouvrez le trousseau (**cmd + espace**, tapez keychain et appuyez sur Entrée).
3. Sélectionnez le trousseau « login ».
4. Cliquez sur l’icône de cadenas pour verrouiller le trousseau (le verrou s’anime alors en position fermée ; cela peut prendre quelques secondes, selon les applications ouvertes).

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Lancez l’Explorateur Stockage.
6. Une fenêtre contextuelle s’affiche, avec un message du type « Service hub wants to access the keychain » (« Le hub de service souhaite accéder au trousseau »). Entrez le mot de passe de votre compte Administrateur Mac et cliquez sur **Toujours autoriser** (ou **Autoriser** si **Toujours autoriser** n’est pas proposé).
7. Essayez de vous connecter.

## <a name="next-steps"></a>Étapes suivantes

Si aucune des solutions ne vous convient, [ouvrez un ticket d’incident sur GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Vous pouvez aussi accéder rapidement à GitHub à l’aide du bouton « Faire part d’un problème à GitHub » en bas à gauche de l’écran.

![Commentaires](./media/storage-explorer-troubleshooting/feedback-button.PNG)
