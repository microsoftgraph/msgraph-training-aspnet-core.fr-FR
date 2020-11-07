---
ms.openlocfilehash: ed1bb3d97791ecfdd3b63a1bb4a1dfd63f2f03bf
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822384"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="8c9eb-101">Ce didacticiel vous apprend à créer une application Web ASP.NET principale qui utilise l’API Microsoft Graph pour récupérer des informations de calendrier pour un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="8c9eb-101">This tutorial teaches you how to build an ASP.NET Core web app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="8c9eb-102">Si vous préférez télécharger simplement le didacticiel terminé, vous pouvez télécharger ou cloner le [référentiel GitHub](https://github.com/microsoftgraph/msgraph-training-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="8c9eb-102">If you prefer to just download the completed tutorial, you can download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-aspnet-core).</span></span> <span data-ttu-id="8c9eb-103">Consultez le fichier Lisez-moi dans le dossier **Demo** pour obtenir des instructions sur la configuration de l’application avec un ID d’application et une clé secrète.</span><span class="sxs-lookup"><span data-stu-id="8c9eb-103">See the README file in the **demo** folder for instructions on configuring the app with an app ID and secret.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8c9eb-104">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="8c9eb-104">Prerequisites</span></span>

<span data-ttu-id="8c9eb-105">Avant de commencer ce didacticiel, le kit de développement [logiciel .net Core](https://dotnet.microsoft.com/download) doit être installé sur votre ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="8c9eb-105">Before you start this tutorial, you should have the [.NET Core SDK](https://dotnet.microsoft.com/download) installed on your development machine.</span></span> <span data-ttu-id="8c9eb-106">Si vous ne disposez pas du kit de développement logiciel (SDK), visitez le lien précédent pour obtenir les options de téléchargement.</span><span class="sxs-lookup"><span data-stu-id="8c9eb-106">If you do not have the SDK, visit the previous link for download options.</span></span>

<span data-ttu-id="8c9eb-107">Vous devez également disposer d’un compte Microsoft personnel disposant d’une boîte aux lettres sur Outlook.com ou d’un compte professionnel ou scolaire Microsoft.</span><span class="sxs-lookup"><span data-stu-id="8c9eb-107">You should also have either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span> <span data-ttu-id="8c9eb-108">Si vous n’avez pas de compte Microsoft, vous disposez de deux options pour obtenir un compte gratuit :</span><span class="sxs-lookup"><span data-stu-id="8c9eb-108">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="8c9eb-109">Vous pouvez vous [inscrire pour obtenir un nouveau compte Microsoft personnel](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span><span class="sxs-lookup"><span data-stu-id="8c9eb-109">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="8c9eb-110">Vous pouvez vous [inscrire au programme pour les développeurs office 365](https://developer.microsoft.com/office/dev-program) pour obtenir un abonnement gratuit à Office 365.</span><span class="sxs-lookup"><span data-stu-id="8c9eb-110">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

> [!NOTE]
> <span data-ttu-id="8c9eb-111">Ce didacticiel a été écrit avec .NET Core SDK version 3.1.201.</span><span class="sxs-lookup"><span data-stu-id="8c9eb-111">This tutorial was written with .NET Core SDK version 3.1.201.</span></span> <span data-ttu-id="8c9eb-112">Les étapes de ce guide peuvent fonctionner avec d’autres versions, mais cela n’a pas été testé.</span><span class="sxs-lookup"><span data-stu-id="8c9eb-112">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="8c9eb-113">Commentaires</span><span class="sxs-lookup"><span data-stu-id="8c9eb-113">Feedback</span></span>

<span data-ttu-id="8c9eb-114">Veuillez fournir des commentaires sur ce didacticiel dans le [référentiel GitHub](https://github.com/microsoftgraph/msgraph-training-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="8c9eb-114">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-aspnet-core).</span></span>
