---
ms.openlocfilehash: 73ba9c271c9c6675ffbb22ce4f800ffb652c715d
ms.sourcegitcommit: 6341ad07cd5b03269e7fd20cd3212e48baee7c07
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2021
ms.locfileid: "49942133"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="9e6e2-101">Ce didacticiel vous apprend à créer une application web core ASP.NET qui utilise l’API Microsoft Graph pour récupérer les informations de calendrier d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="9e6e2-101">This tutorial teaches you how to build an ASP.NET Core web app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="9e6e2-102">Si vous préférez simplement télécharger le didacticiel terminé, vous pouvez télécharger ou cloner le référentiel [GitHub.](https://github.com/microsoftgraph/msgraph-training-aspnet-core)</span><span class="sxs-lookup"><span data-stu-id="9e6e2-102">If you prefer to just download the completed tutorial, you can download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-aspnet-core).</span></span> <span data-ttu-id="9e6e2-103">Consultez le fichier README dans le dossier **de** démonstration pour obtenir des instructions sur la configuration de l’application avec un ID d’application et une secret.</span><span class="sxs-lookup"><span data-stu-id="9e6e2-103">See the README file in the **demo** folder for instructions on configuring the app with an app ID and secret.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9e6e2-104">Configuration requise</span><span class="sxs-lookup"><span data-stu-id="9e6e2-104">Prerequisites</span></span>

<span data-ttu-id="9e6e2-105">Avant de commencer ce didacticiel, le [SDK .NET Core](https://dotnet.microsoft.com/download) doit être installé sur votre ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="9e6e2-105">Before you start this tutorial, you should have the [.NET Core SDK](https://dotnet.microsoft.com/download) installed on your development machine.</span></span> <span data-ttu-id="9e6e2-106">Si vous n’avez pas le SDK, consultez le lien précédent pour obtenir les options de téléchargement.</span><span class="sxs-lookup"><span data-stu-id="9e6e2-106">If you do not have the SDK, visit the previous link for download options.</span></span>

<span data-ttu-id="9e6e2-107">Vous devez également avoir un compte Microsoft personnel avec une boîte aux lettres sur Outlook.com ou un compte scolaire ou scolaire Microsoft.</span><span class="sxs-lookup"><span data-stu-id="9e6e2-107">You should also have either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span> <span data-ttu-id="9e6e2-108">Si vous n’avez pas de compte Microsoft, deux options s’offrent à vous pour obtenir un compte gratuit :</span><span class="sxs-lookup"><span data-stu-id="9e6e2-108">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="9e6e2-109">Vous pouvez [vous inscrire à un nouveau compte Microsoft personnel.](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)</span><span class="sxs-lookup"><span data-stu-id="9e6e2-109">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="9e6e2-110">Vous pouvez vous inscrire au programme pour les développeurs [Office 365](https://developer.microsoft.com/office/dev-program) pour obtenir un abonnement Office 365 gratuit.</span><span class="sxs-lookup"><span data-stu-id="9e6e2-110">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

> [!NOTE]
> <span data-ttu-id="9e6e2-111">Ce didacticiel a été écrit avec le SDK .NET Core version 5.0.102.</span><span class="sxs-lookup"><span data-stu-id="9e6e2-111">This tutorial was written with .NET Core SDK version 5.0.102.</span></span> <span data-ttu-id="9e6e2-112">Les étapes de ce guide peuvent fonctionner avec d’autres versions, mais elles n’ont pas été testées.</span><span class="sxs-lookup"><span data-stu-id="9e6e2-112">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="9e6e2-113">Commentaires</span><span class="sxs-lookup"><span data-stu-id="9e6e2-113">Feedback</span></span>

<span data-ttu-id="9e6e2-114">Veuillez nous faire part de vos commentaires sur ce didacticiel dans [le référentiel GitHub.](https://github.com/microsoftgraph/msgraph-training-aspnet-core)</span><span class="sxs-lookup"><span data-stu-id="9e6e2-114">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-aspnet-core).</span></span>
