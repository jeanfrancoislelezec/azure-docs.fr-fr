---
title: Transmettre des informations d’identification vers Azure à l’aide d’une Configuration d'état souhaité
description: Découvrez comment transmettre des informations d’identification aux machines virtuelles Azure à l’aide de la Configuration d’état souhaité (DSC) PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: dacoulte
ms.openlocfilehash: 666253d16ac51dcc21174211f71794f8b0ede07d
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012544"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Transmission d’informations d’identification au gestionnaire DSCEextension Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Cet article traite de l’extension de la Configuration d’état souhaité (DSC) pour Azure. Vous trouverez une vue d’ensemble du gestionnaire d’extensions DSC dans [Introduction du gestionnaire d’extensions de la Configuration d’état souhaité Azure](dsc-overview.md).

## <a name="pass-in-credentials"></a>Transmettre des informations d’identification

Dans le cadre du processus de configuration, vous devrez peut-être configurer des comptes d’utilisateur, accéder à des services ou installer un programme dans un contexte utilisateur. Pour effectuer ces opérations, vous devez fournir les informations d’identification.

Vous pouvez utiliser DSC pour définir des configurations paramétrables. Dans le cadre d’une configuration paramétrable, les informations d’identification sont transmises à la configuration et stockées en toute sécurité dans des fichiers .mof. Le gestionnaire d’extensions Azure simplifie la gestion des informations d’identification en fournissant une gestion automatique des certificats.

Le script de configuration DSC suivant crée un compte d’utilisateur local avec le mot de passe spécifié :

```powershell
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )
    Node localhost {
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        }
    }
}
```

Il est important d’inclure le **nœud localhost** dans le cadre de la configuration. Le gestionnaire d’extensions recherche plus particulièrement l’instruction du **nœud localhost**. Si cette instruction est manquante, les étapes suivantes ne fonctionnent pas. Il est également important d’inclure la conversion de typecast **[PsCredential]**. Ce type spécifique déclenche l’extension de chiffrement des informations d’identification.

Pour publier ce script sur le Stockage Blob Azure :

`Publish-AzureRmVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Pour définir l’extension DSC Azure et fournir les informations d’identification :

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzureRmVM -Name 'example-1'

$vm = Set-AzureRmVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzureRmVM
```

## <a name="how-a-credential-is-secured"></a>Quel est le mode de sécurisation des informations d’identification

L’exécution de ce code invite à entrer les informations d’identification. Une fois les informations d’identification fournies, elles sont brièvement stockées en mémoire. Lorsque les informations d’identification sont publiées à l’aide de la cmdlet **Set-AzureRmVMDscExtension**, les informations d’identification sont transmises via le protocole HTTPS à la machine virtuelle. Dans la machine virtuelle, Azure stocke les informations d’identification chiffrées sur le disque à l’aide du certificat de machine virtuelle local. Les informations d’identification sont ensuite déchiffrées brièvement en mémoire, puis rechiffrées pour leur transmission à DSC.

Ce processus diffère de [l’utilisation des configurations sécurisées sans le gestionnaire d’extensions](/powershell/dsc/securemof). L’environnement Azure permet de transmettre des données de configuration en toute sécurité via des certificats. Lors de l’utilisation du gestionnaire d’extensions DSC, il est inutile de fournir une entrée **$CertificatePath** ou **$CertificateID**/ **$Thumbprint** dans **ConfigurationData**.

## <a name="next-steps"></a>Étapes suivantes

- Obtenir une [introduction au gestionnaire d’extensions Azure DSC](dsc-overview.md).
- Examinez le [modèle Azure Resource Manager pour l’extension DSC](dsc-template.md).
- Pour plus informations sur DSC PowerShell, reportez-vous au [centre de documentation PowerShell](/powershell/dsc/overview).
- Pour obtenir plus de fonctionnalités gérables avec DSC PowerShell ainsi que plus de ressources DSC, parcourez la [galerie PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).