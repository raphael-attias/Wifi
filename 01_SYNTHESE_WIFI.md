# Synthèse Complète sur le Wi-Fi

## Table des matières
1. [Les Normes Wi-Fi](#les-normes-wifi)
2. [Bandes de Fréquence et Canaux](#bandes-de-fréquence-et-canaux)
3. [Architecture Réseau Wi-Fi](#architecture-réseau-wifi)
4. [Sécurité des Réseaux Wi-Fi](#sécurité-des-réseaux-wifi)
5. [Normes Récentes et Évolutions](#normes-récentes-et-évolutions)
6. [Outils d'Analyse et de Capture](#outils-danalyse-et-de-capture)

---

## Les Normes Wi-Fi

Le Wi-Fi est défini par la norme IEEE 802.11, un ensemble de protocoles régissant les réseaux sans fil (WLAN - Wireless Local Area Network). L'évolution des normes a permis une augmentation progressive des débits et de l'efficacité énergétique.

### Évolution Chronologique

#### 802.11a (1999)
- **Bande de fréquence :** 5 GHz
- **Débit maximum :** 54 Mbit/s
- **Caractéristiques :** Première norme pour la bande 5 GHz, meilleure immunité aux interférences mais portée réduite

#### 802.11b (1999)
- **Bande de fréquence :** 2,4 GHz
- **Débit maximum :** 11 Mbit/s
- **Caractéristiques :** Popularité initiale du Wi-Fi grand public, meilleure portée que 802.11a

#### 802.11g (2003)
- **Bande de fréquence :** 2,4 GHz
- **Débit maximum :** 54 Mbit/s
- **Caractéristiques :** Fusion des technologies 802.11a et 802.11b, rétro-compatible avec 802.11b

#### 802.11n - Wi-Fi 4 (2009)
- **Bandes de fréquence :** 2,4 GHz et 5 GHz (simultanément possible)
- **Débit maximum :** 600 Mbit/s (avec MIMO 4x4)
- **Innovations majeures :**
  - Technologie MIMO (Multiple Input Multiple Output) : multiplication des chaînes d'antennes
  - Augmentation de la largeur de bande (jusqu'à 40 MHz)
  - Meilleure efficacité énergétique

#### 802.11ac - Wi-Fi 5 (2013)
- **Bande de fréquence :** 5 GHz exclusivement
- **Débit maximum :** 3,5 Gbit/s (théorique)
- **Innovations :**
  - Largeurs de canal de 80 et 160 MHz
  - 8 chaînes MIMO
  - Technologie Beamforming pour diriger le signal
  - Performances améliorées pour les appareils multimédias

#### 802.11ax - Wi-Fi 6 (2021)
- **Bandes de fréquence :** 2,4 GHz et 5 GHz
- **Débit maximum :** >10 Gbit/s
- **Améliorations clés :**
  - OFDMA (Orthogonal Frequency-Division Multiple Access) : meilleure utilisation du spectre
  - TWT (Target Wake Time) : économie d'énergie
  - 1024-QAM (meilleure modulation)
  - Efficacité 4x supérieure à 802.11ac

#### 802.11be - Wi-Fi 7 (2024)
- **Bandes de fréquence :** 2,4 GHz, 5 GHz et **6 GHz** (nouvelles)
- **Débit maximum :** 30 Gbit/s
- **Caractéristiques révolutionnaires :**
  - Utilisation du spectre 6 GHz (320 MHz de largeur maximale)
  - Multi-bande simultanée sur 2,4/5/6 GHz
  - Latence ultra-faible
  - Amélioration massive de la capacité réseau

---

## Bandes de Fréquence et Canaux

### Caractéristiques Générales des Bandes

| Bande | Portée | Débit | Interférences | Canaux | Usage |
|-------|--------|-------|----------------|--------|-------|
| **2,4 GHz** | Très bonne | Modéré | Élevées (ISM) | 11-13 (pays dépendant) | Réseaux larges, compatibilité |
| **5 GHz** | Modérée | Excellent | Faibles | 22-23 | Haut débit, entreprise |
| **6 GHz** | Faible | Excellent | Très faibles | ~60 | Très haut débit (Wi-Fi 7) |

### Bande 2,4 GHz

**Caractéristiques :**
- Plage de fréquence : 2 400 - 2 500 MHz
- **Canaux non-chevauchants en Europe :** 1, 6, 11 (USA : 1-11, certains pays : 1-13)
- Espacement entre canaux : 5 MHz
- Largeur de canal utilisée : 20 MHz (22 MHz nominalement)

**Avantages :**
- Excellente pénétration des obstacles (murs, étages)
- Plus grande portée que 5 GHz et 6 GHz
- Rétro-compatible avec les anciens appareils

**Inconvénients :**
- Interférences avec Bluetooth, fours à micro-ondes, téléphones DECT
- Seulement 3 canaux non-chevauchants
- Saturation dans les zones urbaines

### Bande 5 GHz

**Caractéristiques :**
- Plage de fréquence : 5 150 - 5 850 MHz
- **Canaux disponibles (Europe) :** 32-48, 52-144, 149-165 (selon régulations)
- Largeur de canal : 20 MHz (40/80/160 MHz possible avec Wi-Fi 5/6)
- 22-23 canaux non-chevauchants

**Avantages :**
- Spectre moins encombré que 2,4 GHz
- Débits plus élevés
- Davantage de canaux disponibles
- Moins d'interférences avec appareils domestiques

**Inconvénients :**
- Pénétration réduite des obstacles
- Portée plus courte que 2,4 GHz
- Moins compatible avec les anciens appareils

### Bande 6 GHz (Wi-Fi 6E / Wi-Fi 7)

**Caractéristiques :**
- Plage de fréquence : 5 925 - 7 125 MHz (actuellement, selon régions)
- Canaux disponibles : 7 canaux de 160 MHz ou 14 canaux de 80 MHz
- **Spectre : Quasi-vierge pour le Wi-Fi**
- Disponibilité : FCC (USA) depuis 2020, progressif en Europe et mondialement

**Avantages :**
- Spectre pratiquement vierge (pas d'interférences)
- Supportes des largeurs de canal jusqu'à 320 MHz
- Débit et latence optimaux
- ~2x plus de canaux qu'en 5 GHz

**Limitations :**
- Portée très réduite
- Nécessite Wi-Fi 6E minimum (802.11ax) ou Wi-Fi 7 (802.11be)
- Couverture limitée en intérieur

### Tableau Comparatif Canaux

**2,4 GHz - Canaux réglementaires :**
```
Canal  Fréquence (MHz)  Région
1      2412            Mondial
2      2417            Mondial
3      2422            Mondial
...
11     2462            USA
12     2467            Europe (limité)
13     2472            Japon
14     2484            Japon (adhoc uniquement)
```

**5 GHz - Principaux Canaux (exemple) :**
```
Canal  Fréquence (MHz)  Largeur  Région
36     5180           20 MHz   Mondial
40     5200           20 MHz   Mondial
44     5220           20 MHz   Mondial
...
149    5745           20 MHz   USA
165    5825           20 MHz   USA
```

---

## Architecture Réseau Wi-Fi

### Modèles d'Architecture

#### 1. Architecture Infrastructure (Plus courante)

**Composants principaux :**
- **AP (Access Point / Point d'Accès) :** Entité centrale qui gère les connexions
- **Clients (Stations) :** Appareils qui se connectent à l'AP
- **Serveur de Distribution :** Routeur ou commutateur permettant l'accès au réseau câblé

**Flux de données :**
```
Client ←→ AP ←→ Serveur Distribution ←→ Réseau filaire
```

**Caractéristiques :**
- Clients communiquent uniquement via l'AP (pas directement entre eux)
- AP gère l'authentification et l'association
- Meilleure gestion du trafic et de la sécurité

#### 2. Architecture Ad-hoc

- Pas de point d'accès central
- Les appareils communiquent directement entre eux
- Format P2P (peer-to-peer)
- Moins courant dans les déploiements modernes

### Processus d'Association et d'Authentification

#### Phase 1 : Détection et Scan
1. **Scanning actif ou passif :**
   - Client écoute les **Beacon Frames** envoyés par les AP
   - Beacon contient : SSID, vitesses supportées, mécanismes de sécurité (WPA2/WPA3), canaux

2. **Sélection de l'AP :**
   - Client choisit l'AP basé sur SSID/BSSID, signal, sécurité

#### Phase 2 : Authentification (Open System ou Shared Key)
1. **Authentification ouvert (par défaut) :**
   - Échange de trames d'authentification sans vérification de crédentials
   - Requête/Réponse simple

2. **Authentification partagée (WEP, obsolète) :**
   - Challenge-réponse basé sur clé WEP

#### Phase 3 : Association
1. Client envoie **Association Request** contenant :
   - SSID
   - Vitesses supportées
   - Informations de capacité
   
2. AP répond par **Association Response** contenant :
   - Association ID (AID)
   - Vitesse connectée
   - Confirmations de compatibilité

#### Phase 4 : Activation de la Sécurité

**Avec WPA2/WPA3 (4-way Handshake ou SAE) :**
1. **PMK (Pairwise Master Key) :** Dérivée du mot de passe PSK
2. **EAPOL (EAP over LAN) :** Échange de clés
3. **PTK (Pairwise Transient Key) :** Clé de chiffrement de session
4. **GTK (Group Temporal Key) :** Clé de groupe pour multicast

### Le Modèle 802.1X (Authentification Entreprise)

**Entités :**
```
Client (Supplicant) ←→ AP (Authenticator) ←→ Serveur RADIUS (AAA)
```

**Processus :**
1. Client envoie EAP Start
2. AP initie EAP Request/Identity
3. Client répond avec identité
4. Tunnel TLS établi (EAP-TLS)
5. Certificat client validé par RADIUS
6. PAC (Port Access Control) ouvert pour client
7. PMKID attribué

---

## Sécurité des Réseaux Wi-Fi

### Évolution des Protocoles de Sécurité

#### WEP (Wired Equivalent Privacy) - ❌ OBSOLÈTE
- **Période :** 1997-2004
- **Chiffrement :** RC4 40 ou 104 bits
- **Authentification :** Clé partagée
- **Problèmes critiques :**
  - IV (Initialisation Vector) de 24 bits seulement → réutilisation rapide
  - Vulnérable au FMS attack (cryptanalyse)
  - Pas d'intégrité des données (pas de MIC pour tous les paquets)
  - Facilement crackable en quelques minutes
- **Verdict :** Complètement compromise, ne JAMAIS utiliser

#### WPA (Wi-Fi Protected Access) - ⚠️ DÉPRÉCIÉE
- **Période :** 2003-2006
- **Chiffrement :** TKIP (Temporal Key Integrity Protocol)
- **Authentification :** PSK (Pre-Shared Key) ou 802.1X
- **Améliorations vs WEP :**
  - Gestion dynamique des clés
  - MIC (Message Integrity Check) obligatoire
  - Contre-mesures Anti-replay améliorées
- **Faiblesses :**
  - TKIP reste basé sur RC4 (heritage faible)
  - Vulnérable à PTKIP attack
  - Crackable avec Aircrack-ng après capture de handshake
- **Verdict :** À éviter, transition vers WPA2

#### WPA2 (802.11i) - ✅ STANDARD ACTUEL
- **Période :** 2004-2022 (largement déployé)
- **Chiffrement :** AES-CCMP (128-bit)
- **Authentification :** 
  - PSK (WPA2-Personal / WPA2-PSK)
  - 802.1X EAP (WPA2-Enterprise)
- **Caractéristiques :**
  - **4-Way Handshake :** Processus d'échange de clés sécurisé
  - Dérivation de clés robuste (PBKDF2)
  - Chiffrement AES militaire
  - Intégrité garantie (MIC)
  - Protection anti-replay

**Processus 4-Way Handshake WPA2 :**
```
Client                              AP
  |                                 |
  |  ← Message 1 (ANonce + Info)    |
  |                                 |
  |  → Message 2 (SNonce, MIC)      |
  |  (contient PTK calculée)        |
  |                                 |
  |  ← Message 3 (GTK, etc)         |
  |                                 |
  |  → Message 4 (ACK)              |
  |                                 |
  └─ Session cryptée maintenant ────┘
```

**Vulnérabilités connues :**
- **KRACK (2016) :** Key Reinstallation Attack → Windows/macOS patchés
- Nécessite capture du handshake complet pour crackage hors-ligne

**Verdict :** Toujours sûr avec mot de passe fort (12+ caractères) ; utiliser 802.1X/EAP-TLS en entreprise

#### WPA3 (802.11ax-2021) - 🔒 NOUVEAU STANDARD SÉCURISÉ
- **Période :** 2021 - Présent (adoption croissante)
- **Chiffrement :** Identique à WPA2 (AES-CCMP ou AES-GCMP)
- **Authentification :**
  - **SAE (Simultaneous Authentication of Equals) :** Remplace PSK
  - 802.1X Enterprise (identique à WPA2)

**Améliorations Majeures WPA3 vs WPA2 :**

| Aspect | WPA2 | WPA3 |
|--------|------|------|
| Authentification | 4-Way Handshake | SAE (Dragonfly) |
| Vulnérabilité mot de passe | Brute force hors-ligne | Protection renforcée |
| Faible mot de passe | Crackable | Protégé |
| Cryptage données | AES-CCMP | AES-CCMP/GCMP |
| Réseaux ouverts | Aucun chiffrement | OWE (Opportunistic Wireless Encryption) |
| Post-quantique (futur) | Non | Architecture prête |

**SAE (Dragonfly Protocol) :**
```
Client                              AP
  |                                 |
  | Commit (courbe elliptique)      |
  |─────────────────────────────────→|
  |                                 |
  | Confirm (MAC authentifié)       |
  |←─────────────────────────────────|
  |                                 |
  | Confirm-ACK                     |
  |─────────────────────────────────→|
  |                                 |
  └─ PMK dérivée (pas de capture) ──┘
```

**Avantages SAE :**
- ✅ Aucune transmission de PMK calculable
- ✅ Protégé contre les attaques hors-ligne
- ✅ Protection intrinsèque des faibles mots de passe
- ✅ Forward Secrecy (données passées restent sûres)
- ✅ Resistance au KRACK

**OWE (Enhanced Open) :**
- Chiffrement opportuniste sur réseaux ouverts (cafés, aéroports)
- Pas de mot de passe nécessaire
- Chiffrement automatique E2E
- Protection contre MITM

**Verdict :** RECOMMANDÉ pour tout nouveau déploiement ; obligation de mise à jour dès que possible

### Modes de Sécurité

#### PSK (Pre-Shared Key) - Mode Personnel
- **Utilisation :** Petit réseau, SOHO (Small Office Home Office)
- **Configuration :** Mot de passe unique
- **Sécurité :** Dépend de la force du mot de passe
- **WPA2-PSK :** 8-63 caractères
- **Recommandation :** Min 12-16 caractères alphanumériques + spéciaux

#### 802.1X / EAP - Mode Entreprise
- **Utilisation :** Réseaux d'entreprise, sécurité haute
- **Variantes :**
  - **EAP-TLS :** Certificats client + serveur (le plus sûr)
  - **EAP-TTLS :** Tunnel TLS avec authentification par mot de passe
  - **EAP-PEAP :** Tunnel de sécurité + authentification interne
- **Avantages :**
  - Authentification par utilisateur
  - Pas de PSK partagée
  - Audit des connexions
  - Révocation centralisée

#### Gestion des Clés

**Dérivation clé WPA2 PSK :**
```
PMK = PBKDF2(HMAC-SHA1, Mot de passe, SSID, 4096 iterations, 256 bits)
PTK = PRF-480(PMK, "Pairwise key expansion", MIN(AP-MAC, Client-MAC), MAX(AP-MAC, Client-MAC), MIN(ANonce, SNonce), MAX(ANonce, SNonce))
```

**Dérivation clé WPA3 SAE :**
```
PMK = SAE-Confirm() [pas de transmission directe, dérivée localement]
PTK = KDF(PMK, ...)
```

### Attaques Courantes et Mitigations

| Attaque | WPA2 | WPA3 | Mitigation |
|---------|------|------|-----------|
| **Dictionary Attack (hors-ligne)** | ✗ Vulnérable | ✓ Résistant | SAE / Mot de passe fort |
| **KRACK (Key Reinstallation)** | ✗ Affecté | ✓ Immunity | Mise à jour firmware |
| **Rogue AP / Evil Twin** | ✗ Vulnérable | ✓ Détectable | Certificat serveur + pinning |
| **EAPOL Fragmentation** | ✗ Ancien | ✓ Corrigé | WAE (Wi-Fi Alliance Enhancements) |
| **Password Guessing** | ✗ Possible | ✓ Rate-limited | SAE limite tentatives |

---

## Normes Récentes et Évolutions

### IEEE 802.11be Wi-Fi 7 (2024)

**Standardisation :**
- Publication : Mars 2024
- Versions ultérieures :
  - IEEE Std 802.11bf™-2025 (26 septembre 2025)
  - IEEE Std 802.11bk™-2025 (5 septembre 2025)

**Spécifications Techniques :**

| Paramètre | 802.11ax (Wi-Fi 6) | 802.11be (Wi-Fi 7) |
|-----------|--------------------|--------------------|
| Bandes | 2,4 & 5 GHz | 2,4 & 5 & 6 GHz |
| Débit théorique | 10+ Gbps | 30+ Gbps |
| Largeur max canal | 160 MHz | 320 MHz |
| Modulation | 1024-QAM | 4096-QAM |
| Latence | ~30 ms | ~1-2 ms |
| Efficacité | OFDMA | OFDMA + MLO |

**Innovations Wi-Fi 7 :**

1. **MLO (Multi-Link Operation) :**
   - Utilisation simultanée de multiples bandes
   - Agrégation de bande passante de 2,4/5/6 GHz
   - Switching automatique
   - Latence ultra-réduite

2. **Spectre 6 GHz :**
   - 320 MHz de largeur de canal max
   - Quasi pas d'interférences
   - Spectre "propre"

3. **Améliorations Physiques :**
   - 4096-QAM (vs 1024-QAM Wi-Fi 6)
   - Codage multiuser amélioré
   - Réduction du délai de symbole

4. **Cas d'usage :**
   - Réalité virtuelle / Métavers (latence < 1ms)
   - 8K/16K vidéo sans compression
   - Cloud gaming
   - Réseaux industriels

### Trajectoire Future

**Court terme (2024-2026) :**
- Adoption croissante Wi-Fi 6E (2,4/5/6 GHz compatible WiFi 6)
- Premiers appareils Wi-Fi 7 commerciaux
- Coexistence WPA2/WPA3

**Moyen terme (2026-2028) :**
- Déploiement massif Wi-Fi 7 en entreprise
- Réseaux 6E/7 standards
- Phased-out WPA2 en conformité nouvelles normes

**Long terme (post-2028) :**
- Sécurité post-quantique intégrée
- IA/ML pour optimisation adaptatif réseau
- Fusion 5G/Wi-Fi (6G research)

---

## Outils d'Analyse et de Capture

### Outils Principaux

#### 1. Wireshark 🔬

**Description :**
Analyseur de protocoles réseau graphique opensource permettant l'inspection détaillée des paquets Wi-Fi.

**Caractéristiques :**
- Capture en temps réel sur interfaces filaire/sans-fil
- Décodage de ~800 protocoles
- Filtres puissants (Wireshark Display Language)
- Exportation en multiple formats (PCAP, CSV, JSON)
- Support 802.11, WPA/WPA2/WPA3 (limité pour décryption)

**Décryption Wi-Fi dans Wireshark :**
```
Édition → Préférences → Protocoles → IEEE 802.11 → Décryption
- Ajouter clé PSK au format : ssid:password ou hex PSK
- Ou clé PMK hexadécimale directe
- Wireshark déchiffrera EAPOL et données si clé connue
```

**Limitations :**
- Nécessite clé de chiffrement connue
- Pas d'attaque active (déauthentification, handshake forcing)
- Interface peut être gourmande pour très grandes captures

**Installation :**
```bash
# Linux/Debian
sudo apt install wireshark

# macOS
brew install wireshark

# Windows
Télécharger depuis wireshark.org
```

#### 2. Aircrack-ng Suite 🛠️

**Description :**
Suite complète opensource d'outils pour l'audit de sécurité Wi-Fi, permettant capture, analyse et crackage.

**Composants clés :**

| Outil | Fonction |
|-------|----------|
| **airmon-ng** | Active mode moniteur sur interface |
| **airodump-ng** | Scan/capture SSID, BSSID, clients, canaux |
| **aireplay-ng** | Injection de paquets (déauth, faux clients) |
| **aircrack-ng** | Crackage WPA/WPA2 PSK (hors-ligne) |
| **airtun-ng** | Tunnel virtuel après crackage |
| **airbase-ng** | Création rogue AP pour tests |

**Processus typ. Crackage WPA2 PSK :**

```bash
# 1. Activer mode moniteur
sudo airmon-ng start wlan0
# → Crée wlan0mon

# 2. Scan réseaux
sudo airodump-ng wlan0mon
# Note BSSID et canal du réseau cible

# 3. Capture handshake
sudo airodump-ng --bssid AA:BB:CC:DD:EE:FF --channel 6 -w capture wlan0mon

# 4. Forcer handshake (déauth)
sudo aireplay-ng --deauth 10 -a AA:BB:CC:DD:EE:FF wlan0mon
# Attend reconnexion client pour capture handshake

# 5. Crackage hors-ligne
aircrack-ng -w /usr/share/wordlists/rockyou.txt -b AA:BB:CC:DD:EE:FF capture-01.cap
```

**Limitations :**
- Nécessite mode moniteur (certaines cartes ne le supportent pas)
- Crackage PSK nécessite handshake complet
- Lent pour long dictionnaire
- WPA3 SAE n'est pas crackable de cette manière

**Installation :**
```bash
sudo apt install aircrack-ng  # Debian/Ubuntu
brew install aircrack-ng      # macOS
```

#### 3. tcpdump 📡

**Description :**
Capture de paquets en ligne de commande, sans interface graphique.

**Utilisation :**
```bash
# Capture interface eth0
tcpdump -i eth0 -w capture.pcap

# Filtres spécifiques
tcpdump -i wlan0 "tcp port 80"

# 802.11 frames (mode moniteur nécessaire)
tcpdump -i wlan0mon -w wifi-capture.pcap
```

**Avantages :**
- Léger, très performant
- Zéro overhead GUI
- Idéal serveurs distants/embeddes
- Scripts bash/automation faciles

**Limitations :**
- Pas d'interface graphique
- Courbe d'apprentissage (synthaxe complexe)
- Pas d'analyse en temps réel visuelle

#### 4. Kali Linux - Environnement Complet

**Description :**
Distribution Linux spécialisée sécurité incluant tous les outils Wi-Fi.

**Outils pré-installés :**
- Aircrack-ng suite
- Wireshark
- tcpdump
- hashcat (crackage GPU)
- cowpatty (WPA PSK crack altnatif)
- hostapd (AP virtuel)
- dnsmasq (DHCP/DNS)

**Mode moniteur sur Kali :**
```bash
sudo airmon-ng start wlan0
# Ou méthode alternative
sudo ip link set wlan0 down
sudo iw wlan0 set monitor control
sudo ip link set wlan0 up
sudo tcpdump -i wlan0
```

**Virtualisation :**
Peut tourner sur VirtualBox/VMware avec USB passthrough pour cartes Wi-Fi.

#### 5. Hashcat - Crackage GPU Accéléré

**Description :**
Crackeur de mots de passe utilisant GPU pour accélération massive.

**Modes WPA/WPA2 :**
```bash
hashcat -m 22000 capture.hc22000 wordlist.txt --workload-profile 3
# -m 22000 = WPA-PBKDF2-PMKID+EAPOL (Fast mode)
# -m 22001 = WPA-PMK (Cracking, plus lent)
```

**Avantages GPU :**
- 10-100x plus rapide que CPU seul
- NVIDIA CUDA > AMD OpenCL > CPU
- Faisable crackage billions de possibilités

#### 6. Autres Outils Utiles

| Outil | Usage |
|-------|-------|
| **tshark** | Version CLI Wireshark |
| **hostapd** | Créer AP virtuel Wi-Fi |
| **iwconfig** | Configuration interface wireless |
| **iw** | Gestion Wi-Fi moderne (iproute2) |
| **hcxdumptool** | Capture optimisée PMKID/handshake |
| **medusa** | Brute-force EAP authentications |
| **wifite2** | Automatisation audits Wi-Fi |

### Workflow Complet d'Analyse

```
1. CAPTURE PACKETS
   ↓
   Wireshark / tcpdump / airodump-ng
   → Générer fichier .pcap / .pcapng

2. ANALYSE STATIQUE
   ↓
   Wireshark UI / tshark filters
   → Identifier phasesvconnexion, chiffrement, SSIDs

3. EXTRACTION DONNÉES
   ↓
   Wireshark export / tshark parsing
   → Extraire handshakes, beacons, EAPOL frames

4. DÉCHIFFREMENT (Si clé connue)
   ↓
   Wireshark decryption key + tshark decryption
   → Voir contenu en clair des paquets

5. CRACKAGE PSK (Si nécessaire)
   ↓
   aircrack-ng / hashcat
   → Retrouver mot de passe à partir handshake

6. RAPPORTAGE
   ↓
   Export JSON/CSV + Screenshots
   → Documentation finding, recommandations
```

---

## Références et Sources

- [IEEE 802.11 Standard - lecrabeinfo.net](https://lecrabeinfo.net/tutoriels/ieee-802-11-a-b-g-n-ac-les-normes-wi-fi/)
- [Dell - Wi-Fi Standards Overview](https://www.dell.com/support/contents/fr-gq/article/product-support/self-support-knowledgebase/networking-wifi-and-bluetooth/wi-fi-network-standards-overview)
- [Comparaison WPA2 vs WPA3 - Ajax Systems](https://ajax.systems/blog/wpa2-vs-wpa3-understanding-wi-fi-security/)
- [WPA3 Security Insights - NetSpot](https://www.netspotapp.com/blog/wifi-security/what-is-wpa3.html)
- [Bandes Wi-Fi Expliquées - Intel](https://www.intel.com/content/www/us/en/products/docs/wireless/2-4-vs-5ghz.html)
- [Canaux Wi-Fi - NETGEAR Support](https://kb.netgear.com/29396/What-is-the-difference-between-2-4-GHz-5-GHz-and-6-GHz-wireless-frequencies)
- [Aircrack-ng Official Documentation](https://www.aircrack-ng.org/)
- [Wireshark Protocol Analyzer](https://www.wireshark.org/)
- [Architecture Wi-Fi - OpenClassrooms](https://openclassrooms.com/fr/courses/4776036-protegez-vos-systemes-numeriques-connectes-grace-aux-12-bonnes-pratiques-de-l-anssi/)

---

**Document généré le :** 27 mai 2026  
**Version :** 1.0  
**Niveau d'expertise :** Intermédiaire à Avancé
