<!-- markdownlint-disable MD002 MD041 -->

Ce didacticiel vous apprend à créer une application Xamarin qui utilise l’API Microsoft Graph pour récupérer les informations de calendrier d’un utilisateur.

> [!TIP]
> Si vous préférez simplement télécharger le didacticiel terminé, vous pouvez télécharger ou cloner [le référentiel GitHub terminé.](https://github.com/microsoftgraph/msgraph-training-xamarin)

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez avoir installé Visual Studio sur un ordinateur exécutant Windows 10 le mode développeur [est allumé.](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) [](https://visualstudio.microsoft.com/vs/) Si vous n’avez pas Visual Studio, consultez le lien précédent pour obtenir les options de téléchargement.

Xamarin doit également être installé dans le cadre de votre installation Visual Studio’installation. Voir [Installation de Xamarin](/xamarin/cross-platform/get-started/installation) pour obtenir des instructions sur l’installation et la configuration de Xamarin.

Si vous le souhaitez, vous devez également avoir accès à un Mac Visual Studio pour Mac installé. Si vous n’avez pas accès, vous pouvez toujours effectuer ce didacticiel, mais vous ne pourrez pas compléter les sections spécifiques d’iOS.

Vous devez également avoir un compte Microsoft personnel avec une boîte aux lettres sur Outlook.com, ou un compte scolaire ou scolaire Microsoft. Si vous n’avez pas de compte Microsoft, deux options s’offrent à vous pour obtenir un compte gratuit :

- Vous pouvez [vous inscrire à un nouveau compte Microsoft personnel.](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)
- Vous pouvez [vous inscrire au programme Office 365 développeur](https://developer.microsoft.com/office/dev-program) pour obtenir un abonnement Office 365 gratuit.

> [!NOTE]
> Ce didacticiel a été Visual Studio version 2019 16.10.3 et Visual Studio pour Mac version 8.5.1. La plateforme 28 du SDK Android est installée sur les deux ordinateurs. Les étapes de ce guide peuvent fonctionner avec d’autres versions, mais elles n’ont pas été testées.

## <a name="feedback"></a>Commentaires

N’hésitez pas à nous faire part de vos commentaires sur ce didacticiel [dans GitHub référentiel.](https://github.com/microsoftgraph/msgraph-training-xamarin)
