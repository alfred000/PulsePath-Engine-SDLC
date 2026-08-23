# Service de Notification Transversal [CORE-NOT] : Alarmes Locales, Push & Popups
**Priorité (MoSCoW) :** Must Have  
**Module associé :** `:core:notification-service` (Moteur d'Acheminement des Alertes)

### Description
**En tant qu'** architecte de la solution PulsePath,  
**Je veux** centraliser la distribution, la priorisation et le routage de l'ensemble des signaux de communication vers l'utilisateur,  
**Afin de** distribuer immédiatement les alertes critiques, de masquer les données de santé sur écran verrouillé et de suspendre les notifications de confort pendant ses heures de sommeil.

---

## 🏗️ Positionnement Architectural & Découpage Étanche

Le module `:core:notification-service` est un composant horizontal pur qui sert de haut-parleur technique au système :
*   **Agnosticisme métier :** Il ignore l'origine ou la logique de création des conseils (détenue par `:feature:coaching`) ou du suivi des habitudes (détenue par `:feature:habits`). Il expose des contrats d'ingestion génériques.
*   **Tuyauterie de distribution :** Il se charge exclusivement de déterminer la route optimale (Popup interne, alarme native ou push distant) et d'appliquer les barrières de sécurité et temporelles avant affichage.

---

## 🛠️ Critères d'Acceptation & Spécifications (Functional Design)

### [REQ-NOT-01] : Routage Dynamique (Foreground vs Background) & Liens Profonds
Le système doit adapter le canal de délivrance d'un message selon l'état d'activité immédiat de l'appareil de l'utilisateur :
*   *Critère 1 :* Si l'application est active au premier plan (Foreground), l'intercepteur privilégie une *Fenêtre Contextuelle In-App* (Popup) discrète pour ne pas interrompre les saisies en cours dans le journal.
*   *Critère 2 :* Si l'application est fermée ou l'appareil en veille (Background), le service route le message vers le centre de notifications natif du système d'exploitation.
*   *Critère 3 :* L'interaction utilisateur (clic) sur une alerte doit exécuter un protocole de lien profond (Deep Linking) ouvrant l'application à l'écran exact lié au contexte de l'alerte.

### [REQ-NOT-02] : Anonymisation Obligatoire sur Écran Verrouillé (BR-NOT-01)
Pour garantir le secret médical et respecter les normes réglementaires européennes sur les données de santé (RGPD / RGPD-BE), toute notification acheminée via un canal push externe ne doit afficher aucune valeur brute à l'écran :
*   *Critère 1 (Texte Interdit) :* *"Votre poids est en hausse à 82 kg"* ou *"Vous avez dépassé votre budget de 500 kcal"*.
*   *Critère 2 (Texte Neutre Réglementaire) :* *"PulsePath : Un nouvel insight métabolique est disponible sur votre tableau de bord"* ou *"Rappel : C'est le moment de documenter votre journal"*.
*   *Critère 3 :* La payload masquée transitant par les serveurs tiers contient uniquement des identifiants cryptographiques neutres décodés localement à l'ouverture sécurisée de la session.

### [REQ-NOT-03] : Politique de Préservation du Sommeil et File d'Attente (BR-NOT-02)
Sauf cas de force majeure explicitement qualifié d'alerte de sécurité critique par l'API (déshydratation sévère), le service de notification a l'interdiction d'émettre des signaux sonores, des vibrations ou des push visuels sur le terminal de l'utilisateur pendant sa nuit :
*   *Critère 1 :* La fenêtre de silence est active entre **22:00 et 07:00** (basée sur le fuseau horaire local défini dans `:feature:profile`).
*   *Critère 2 :* Les messages d'optimisation ou de confort générés durant cette plage sont automatiquement interceptés, empilés dans une file d'attente locale (Queue), et délivrés de manière groupée au réveil de l'utilisateur à **07:15**.

---

## 💻 Spécification Technique du Service (C# Layout Blueprint)

```csharp
// C# .NET Core Clean Architecture - Interface du Service de Communication
// Emplacement : PulsePath.Core.Contracts.Notifications.INotificationService.cs

namespace PulsePath.Core.Contracts.Notifications;

public interface INotificationService
{
    /// <summary>
    /// Enregistre un rappel local autonome et déconnecté sur le planificateur de l'appareil.
    /// </summary>
    Task ScheduleLocalReminderAsync(Guid userId, string reminderId, DateTimeOffset executionTime, string messageTemplate);

    /// <summary>
    /// Route une alerte dynamique en appliquant les filtres d'anonymisation et de respect du sommeil.
    /// </summary>
    Task RouteIncomingAlertAsync(Guid userId, NotificationPayloadDto alertPayload);
}
```

