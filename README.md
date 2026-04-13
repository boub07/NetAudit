# 🛡️ NetAudit — Outil d'Audit de Conformité Réseau

> Projet réalisé dans le cadre du cursus **IMT Nord Europe**  
> Automatisation de l'audit de sécurité sur équipements Cisco IOS via Python & SSH

---

## 🎯 Problème résolu

Dans un parc réseau, vérifier manuellement que chaque routeur respecte les règles de sécurité (HTTP désactivé, Telnet banni, sessions avec timeout…) est **long, coûteux et source d'erreurs humaines**.

**NetAudit automatise cette vérification** : en quelques secondes, le script audite N équipements et produit un rapport de conformité clair, avec un score global.

---

## ⚙️ Fonctionnement

```
Inventaire YAML  ──►  Connexion SSH (netmiko)  ──►  show running-config
                                                              │
                                               Vérification des règles
                                                              │
                                               Rapport Rich (tableau + score)
```

### Deux modes d'exécution

| Mode | Utilisation | Commande |
|------|------------|---------|
| **Dry Run** (simulation) | Test sans réseau — configs fictives intégrées | `dry_run=True` |
| **SSH Réel** | Connexion aux vrais équipements | `dry_run=False` |

---

## 🔐 Règles de sécurité vérifiées

| Règle | Criticité | Pattern recherché |
|-------|-----------|-------------------|
| Serveur HTTP désactivé | 🔴 CRITIQUE | `no ip http server` |
| Serveur HTTPS désactivé | 🔴 CRITIQUE | `no ip http secure-server` |
| Telnet interdit (SSH uniquement) | 🔴 CRITIQUE | `transport input ssh` |
| Timeout de session défini | 🟡 MOYEN | `exec-timeout` |
| Banner de connexion présente | 🟢 FAIBLE | `banner motd` |

> **Extensibilité** : ajouter une règle = ajouter une ligne dans le dictionnaire `REGLES_SECURITE`. Le cœur du programme ne change pas.

---

## 🚀 Installation & Lancement

### Prérequis

```bash
pip install netmiko pyyaml rich
```

### Lancer l'audit (mode simulation)

```bash
git clone https://github.com/TON_PSEUDO/NetAudit.git
cd NetAudit
python main.py
```

### Exemple de sortie

```
═══════════════════════════════════════
   🔍 NetAudit — Démarrage de l'audit
═══════════════════════════════════════
Mode : ⚙️  Simulation (Dry Run)

▶ Audit de Router-Paris (192.168.1.1)
  ↳ Analyse terminée (5 règles vérifiées)
▶ Audit de Router-Lyon (192.168.1.2)
  ↳ Analyse terminée (5 règles vérifiées)
...

╭──────────────────┬──────┬──────────────────────────────┬───────────┬──────────────╮
│ Équipement       │ Site │ Règle vérifiée               │ Criticité │    Statut    │
├──────────────────┼──────┼──────────────────────────────┼───────────┼──────────────┤
│ Router-Paris     │ Paris│ Serveur HTTP désactivé       │ CRITIQUE  │    ✅ OK     │
│ Router-Lyon      │ Lyon │ Serveur HTTP désactivé       │ CRITIQUE  │ ❌ VULNÉRABLE│
...

Score global de conformité : 53% (8/15 règles respectées)
```

---

## 🧪 Stratégie de test

### 1. Mode Dry Run (ce dépôt)
Les configs fictives dans `CONFIGS_SIMULEES` reproduisent des cas réels :
- **Router-Paris** : bien configuré → score élevé
- **Router-Lyon** : HTTP actif, Telnet autorisé → vulnérabilités détectées
- **Router-Lille** : configuration très permissive → score bas

Cela permet de valider l'algorithme d'analyse **sans aucun équipement réseau**.

### 2. Mode SSH réel (Cisco Packet Tracer / GNS3)
Passer à `dry_run=False` et renseigner les équipements dans `INVENTAIRE` suffit à basculer sur de vraies connexions SSH. Testé sur routeur **Cisco 2911** sous Packet Tracer avec SSH v2 activé.

---

## 📁 Structure du projet

```
NetAudit/
├── main.py          # Script principal (audit + rapport)
├── README.md        # Cette documentation
└── (inventory.yaml) # À créer pour la prod : liste des équipements
```

---

## 🔮 Évolutions possibles

- [ ] Lecture de l'inventaire depuis `inventory.yaml`
- [ ] Export du rapport en PDF ou CSV
- [ ] Envoi d'alerte par e-mail si score < seuil
- [ ] Interface web (Flask/FastAPI)
- [ ] Support multi-constructeurs (Juniper, HP…)

---

## 🛠️ Stack technique

- **Python 3.10+**
- **Netmiko** — abstraction SSH multi-constructeurs
- **Rich** — rendu terminal professionnel (tableaux, couleurs)
- **PyYAML** — gestion de l'inventaire

---

## 👤 Auteur

**[Ton Prénom NOM]** — Étudiant IMT Nord Europe  
Projet réalisé en autonomie dans le cadre de [NOM DU MODULE/COURS]

---

*"L'automatisation ne remplace pas l'ingénieur réseau — elle lui libère du temps pour les vrais problèmes."*
