<!-- markdownlint-disable MD002 MD041 -->

Ce didacticiel vous apprend à créer une application Xamarin qui utilise l’API Microsoft Graph pour récupérer des informations de calendrier pour un utilisateur.

> [!TIP]
> Si vous préférez télécharger simplement le didacticiel terminé, vous pouvez télécharger ou cloner le [référentiel GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin).

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, [Visual Studio](https://visualstudio.microsoft.com/vs/) doit être installé sur un ordinateur exécutant Windows 10 avec [le mode développeur activé](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development). Si vous ne disposez pas de Visual Studio, reportez-vous au lien précédent pour obtenir les options de téléchargement.

Xamarin doit également être installé dans le cadre de votre installation de Visual Studio. Consultez la rubrique [installation de Xamarin](/xamarin/cross-platform/get-started/installation) pour obtenir des instructions sur l’installation et la configuration de Xamarin.

Si vous le souhaitez, vous pouvez également avoir accès à un Mac avec Visual Studio pour Mac installé. Si vous n’avez pas accès, vous pouvez continuer à effectuer ce didacticiel, mais ne pourrez pas terminer les sections spécifiques d’iOS.

Vous devez également disposer d’un compte Microsoft personnel disposant d’une boîte aux lettres sur Outlook.com ou d’un compte professionnel ou scolaire Microsoft. Si vous n’avez pas de compte Microsoft, vous disposez de deux options pour obtenir un compte gratuit :

- Vous pouvez vous [inscrire pour obtenir un nouveau compte Microsoft personnel](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).
- Vous pouvez vous [inscrire au programme pour les développeurs office 365](https://developer.microsoft.com/office/dev-program) pour obtenir un abonnement gratuit à Office 365.

> [!NOTE]
> Ce didacticiel a été écrit avec Visual Studio 2019 version 16.5.2 et Visual Studio pour Mac version 8.5.1. Les deux ordinateurs ont la plateforme du kit de développement logiciel (SDK) Android installée. Les étapes de ce guide peuvent fonctionner avec d’autres versions, mais cela n’a pas été testé.

## <a name="feedback"></a>Commentaires

Veuillez fournir des commentaires sur ce didacticiel dans le [référentiel GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin).
