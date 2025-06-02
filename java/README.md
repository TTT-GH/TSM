# TSM Java

## Installation
[Voir le processus d'installation](https://github.com/TTT-GH/TModeler/tree/main/java/README.md)

---

## 🧱 Initialisation de la synchronisation

Pour activer TSM dans votre application, il suffit de configurer une fois le système via `TModeler.initialize()` :
Voici un exemple complet :

```java
private void initTmodeler() {
    TModeler.initialize(this, new TModelerCallback() {

        @Override
        public List<Tdb> onLoadBases(List<Tdb> bases) {
            // Ajout de la base principale
            bases.add(Tdb.connect("FirstTdb"));
            return bases;
        }

        // === 🔧 Paramètres réseau pour la synchronisation ===

        @Override
        public String onLoadServerHost() {
            return "http://192.168.236.174:8000"; // hôte du serveur
        }

        @Override
        public String onLoadBaseUrl() {
            return "api"; // point d'entrée des routes API
        }

        @Override
        public String onLoadVersion() {
            return "v1"; // version des API
        }

        @Override
        public String onLoadMediaHost() {
            return "media"; // sous-domaine ou dossier média
        }

        @Override
        public String onLoadMediaTmpDir() {
            return "tmp"; // répertoire temporaire pour les fichiers média
        }

        @Override
        public String onLoadMediaDir(String dir) {
            return super.onLoadMediaDir(dir); // surcharge possible
        }

        // === 🌐 Réaction au changement de connexion réseau ===

        @Override
        public void onServerAccessChange(boolean connected) {
            if (connected) {
                // Lancement de la synchronisation automatique
                TSyncMaster.listener(new TSyncCallback() {
                    @Override
                    public void syncDone() {
                        // Tous les modèles ont été synchronisés
                        // On peut ici notifier l'utilisateur ou relancer une action
                    }
                }).sync(1000); // synchronisation toutes les 1000 ms (1 seconde)
            }
        }

        // === ✅ Préparation des modèles à synchroniser ===

        @Override
        public void onPrepareSync() {
            // Communication des modèles modifiés à TSM
            Chanel.tms.commit();
            ChanelContext.tms.commit();
            ChanelEditor.tms.commit();

            Messager.tms.commit();
            MessagerRole.tms.commit();

            Message.tms.commit();

            Messaging.tms.commit();
            MessagingMember.tms.commit();
            MessagingType.tms.commit();
        }
    });
}
```

---

## 🔄 Configuration de la synchronisation pour chaque modèle

Chaque modèle de données (ex. Message, Messager, etc.) peut définir ses propres paramètres de synchronisation, notamment :

L’URL de l’API REST cible pour sa synchronisation ;

Une fonction de rappel (onSyncDone) qui sera déclenchée à la fin de la synchronisation du modèle ;

Des autres options comme le token, le type de sync, etc.

Cette configuration se fait en surchargeant la méthode onSync() dans la classe du modèle concerné.

---
Exemple : synchronisation du modèle Message
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
Exemple : synchronisation du modèle Messager
```java

public class Messager extends TModel<Messager> {
    //...
    @Override
    public TServerInfos onSync() {
        return super.onSync().with(
            "im/messagers/", // API dédiée au modèle `Messager`
            null,
            () -> {
                Log.i("SYNC", "Messagers synchronisés !");
            }
        );
    }
    //...
}
```

---

## 🧠 À retenir

- Cette méthode permet à chaque modèle de déclarer sa stratégie de synchronisation.
- Le troisième argument est une interface d'observation appelée automatiquement à la fin de la synchronisation du modèle.
- Cela permet une synchronisation différenciée et granulaire, parfaite pour les architectures modulaires ou multi-domaine.
- Le même mécanisme est automatiquement généré en version Python grâce à l'intégration TM + TSM + THC.

---