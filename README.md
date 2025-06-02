# 🔄 TSM - TTT Synchronizer Master

**TSM** est un moteur de synchronisation automatique conçu pour orchestrer la réplication fluide, continue et sécurisée des modèles de données dans des applications multi-devices ou distribuées.

> Grâce à **TSM**, les modèles gérés par TModeler peuvent être synchronisés sans effort avec des serveurs distants, en temps réel ou à intervalles réguliers.

---

## 🚀 Principales Fonctionnalités

- **Synchronisation automatique des modèles** : chaque modèle déclare ses endpoints, et TSM gère le reste.
- **Observation des changements** : toute modification locale d’un modèle est interceptée et préparée pour la synchronisation.
- **Période de synchronisation réglable** : définissez une fréquence adaptée à votre application.
- **Suivi multithread** : synchronisation possible sur plusieurs fenêtres / activités simultanément.
- **Événements de cycle** : écoute du début et de la fin de chaque cycle de synchronisation.
- **Support offline / online** : les changements sont stockés en attente et poussés dès que le réseau est rétabli.
- **Compatible TM et THC** : conçu pour interagir directement avec TModeler et THC.

---

## ⚙️ Exemple de configuration d’un projet avec TSM

```java
public class Message extends TModel<Message> {
    //...
    @Override
    public TServerInfos onSync() {
        return super.onSync().with(
            "im/messages/", // URL d'accès à l'API pour les messages
            null,           // (optionnel) une fonction d'autorisation ou un token custom
            () -> {         // callback exécuté quand la synchronisation du modèle est terminée
                Log.i("SYNC", "Messages synchronisés !");
            }
        );
    }
    //...
}
```

---

## 📦 Synchronisation automatique

- Une fois tous les éléments déclarés :
- Le modèle détecte automatiquement toute modification.
- Les données sont envoyées au serveur à la prochaine itération du cycle.
- Les nouvelles données distantes sont aussi récupérées automatiquement.

---

# **TSM** : [Voir la documentation](java/README.md)

---