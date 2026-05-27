# Analyse Détaillée des Captures Wi-Fi (PCAP)

## Vue d'ensemble des fichiers

| Fichier | Taille | Format | Contenu Principal | Sécurité |
|---------|--------|--------|-------------------|----------|
| **dot11-sample.pcap** | 45 KB | PCAP | Phases connexion 802.11 | Beacon, Auth, Assoc, EAPOL |
| **Network_Join_Nokia_Mobile.pcap** | 162 KB | PCAP | Client rejoignant réseau | WPA handshake |
| **wpa-Induction.pcap** | 176 KB | PCAP | Données WPA chiffrées | WPA/WPA2 encrypted |
| **wpa3.pcapng** | 31 KB | PCAPNG | Connexion WPA3 pure | WPA3 SAE |
| **wpa3_transition_wpa3client_24ghz.pcapng** | 22 KB | PCAPNG | Client WPA3 sur 2.4GHz | WPA3 mixed mode |
| **mistap-5-6ghz.pcapng** | 1.3 MB | PCAPNG | Multi-band 5/6 GHz | Wi-Fi 6E/7 |
| **airtool_ap45-5-6ghz.pcapng** | 6.4 MB | PCAPNG | Capture avec outil professionnel | High-speed bands |

---

## 1. dot11-sample.pcap - Phases de Connexion 802.11

### Objectif
Démonstration complète des trames échangées lors d'une connexion Wi-Fi standard.

### Contenu Attendu

#### Phase 1 : Découverte (Beacon / Probe)

**Beacon Frames :** 
```
Source: AP (AA:BB:CC:DD:EE:FF)
Type: Management (0x80)
Subtype: Beacon (0x08)
Contenu: 
  - SSID: "TestNetwork"
  - Supported Rates: 1, 2, 5.5, 11 Mbps (802.11b)
  - DS Parameter Set: Channel 6
  - TIM (Traffic Indication Map)
  - RSSI/Power: -30 dBm (signal fort)
```

**Probe Request/Response :**
```
Probe Request:
  Source: Client (11:22:33:44:55:66)
  SSID Request: "TestNetwork"
  Supported Rates: Client capabilities
  
Probe Response:
  Source: AP (AA:BB:CC:DD:EE:FF)
  Contenu: Similaire à Beacon
```

#### Phase 2 : Authentification (802.11 Open)

**Frame 1 - Auth Request :**
```
From: Client → AP
Frame Type: Management (0x80)
Subtype: Auth (0x0B)
Auth Seq: 1
Status: 0 (Ok)
```

**Frame 2 - Auth Response :**
```
From: AP → Client
Frame Type: Management (0x80)
Subtype: Auth (0x0B)
Auth Seq: 2
Status: 0 (Success)
```

#### Phase 3 : Association

**Frame 1 - Association Request :**
```
From: Client → AP
Frame Type: Management (0x80)
Subtype: Association Request (0x00)
SSID: "TestNetwork"
Supported Rates: Client rates
Listen Interval: 10
Capabilities:
  - ESS Capabilities
  - Privacy (si WPA/WPA2)
```

**Frame 2 - Association Response :**
```
From: AP → Client
Frame Type: Management (0x80)
Subtype: Association Response (0x01)
Status Code: 0 (Success)
Association ID (AID): 1
Supported Rates: AP rates
```

#### Phase 4 : Sécurité (4-Way Handshake WPA/WPA2)

**EAPOL Frames (Extensible Authentication Protocol Over LAN) :**

```
FRAME 1 - AP → Client (EAPOL Key)
  Key Descriptor Type: HMAC-MD5/AES
  Key Info:
    - Pairwise Key Cipher: AES
    - Group Key Cipher: TKIP/AES
    - Replay Counter: 0
  Key Data:
    - ANonce (AP Nonce) : 32 bytes random
    - No encrypted key material yet
  
FRAME 2 - Client → AP (EAPOL Key)
  Key Info:
    - ACK bit SET (acknowledgment)
  Key Data:
    - SNonce (Supplicant Nonce) : 32 bytes random
    - Client capabilities
  MIC (Message Integrity Check) : Calculée sur clé provisoire
  
FRAME 3 - AP → Client (EAPOL Key - avec GTK)
  Key Info:
    - Install bit SET (install encryption)
  Key Data:
    - GTK (Group Temporal Key) : clé groupe chiffrée
    - Key Encryption Key (KEK)
  Encrypted avec PTK
  
FRAME 4 - Client → AP (EAPOL Key - ACK)
  Simple acknowledgement
  Confirm installation PTK
```

### Trame Déauthentication (Deauth)

```
Type: Management (0x80)
Subtype: Deauthentication (0x0C)
Reason Code: 7 (Class 3 frame received from nonauthenticated STA)
           ou 8 (Disassociated because of inactivity)
           ou 15 (4-Way Handshake timeout)
```

### Analysis Points

**Observations Clés :**
1. **Ordre des frames** : Beacon → Probe → Auth → Assoc → EAPOL (4 frames)
2. **Timing** : Delais entre frames (généralement <100 ms)
3. **Unicast vs Multicast** : Beacon = multicast; Auth/Assoc = unicast
4. **Séquence Numbers** : Numéros séquences croissants pour retransmission
5. **Retry bit** : Indique retransmission si connexion instable
6. **BSSID constant** : Même AP throughout (ou roaming = nouveau BSSID)

---

## 2. Network_Join_Nokia_Mobile.pcap - Client Mobile Rejoignant Réseau

### Objectif
Capture réelle d'un appareil mobile (Nokia) se connectant.

### Caractéristiques Spécifiques

**Mobile Device Behavior :**

1. **Scanning actif :**
   - Probe Requests sur tous les canaux (1-11 puis 36-165 5GHz)
   - Envoie en multicast "Any SSID?"
   - Attend Probe Responses

2. **Association sélective :**
   - Tests multiples candidats AP
   - Choix basé sur SSID match + signal strength
   - RSSI: -30 dBm excellent, -70 dBm acceptable, -90 dBm faible

3. **Optimisations mobiles :**
   - Listen Interval > 10 (économise batterie)
   - Power Management bit SET dans capability
   - Réduction de fréquence de beacons

**Phases Observées :**

```
1. Scan (Probe activity)  ← 1-2 secondes
2. Find AP               ← détection réseau cible
3. Authenticate          ← 100-200 ms
4. Associate             ← 50-100 ms
5. 4-Way Handshake       ← 200-500 ms
6. IP Assignment (DHCP)  ← Non visible dans Wi-Fi, layer 3
7. Connected state       ← Beacon listening commence
```

### Points d'Analyse

**Handshake Quality :**
- Tous 4 frames EAPOL présents et ordonnés ?
- Pas de retransmissions handshake ?
- Pas de timeouts entre frames (> 1 seconde = problème) ?

**Signal Quality :**
- RSSI constant ou fluctuant ?
- Pas de déconnexions soudaines ?
- Roaming vers autre AP ?

**Déauthentification :**
- Client se déconnecte gracieusement ?
- Forced deauth par AP ?
- Inactivity timeout ?

---

## 3. wpa-Induction.pcap - Données WPA Chiffrées

### Objectif
Capture avec trafic de données chiffré WPA/WPA2.

### Contenu Attendu

**Management Frames (Déchiffrables):**
- Beacons ✓
- Auth/Assoc ✓
- EAPOL handshake ✓

**Data Frames (Chiffrées - sans clé) :**

```
802.11 Data Frame (LLC encapsulated)
┌─────────────────────────────────────┐
│ 802.11 Header (24 bytes)            │
│  - From/To/BSSID addresses          │
│  - Sequence number                  │
│  - QoS info (if present)            │
├─────────────────────────────────────┤
│ IV (Initialization Vector) [4 bytes]│
│ Protected Payload (chiffré AES)     │
│ MIC/ICV [8 bytes, chiffré]          │
└─────────────────────────────────────┘
```

**Indicateurs Frame Chiffré :**
```
Frame Control flags:
  - Protected Frame bit = 1
  - MoreFrag bit (si fragmentation)
  
Payload appears as random bytes (dû à AES)
```

### Tentatives de Déchiffrement

**Sans clé PSK :**
```
$ wireshark -r wpa-Induction.pcap
→ Voir frames chiffrées mais contenu "???"
```

**Avec clé (si connue) :**
```
Preferences → Protocols → IEEE 802.11 → Decryption
→ Ajouter SSID:Password ou PMK hex
→ Frames se déchiffrent, voir TCP/IP/HTTP
```

**Crackage hors-ligne (aircrack-ng) :**
```
aircrack-ng -w wordlist.txt -b AA:BB:CC:DD:EE:FF capture.cap
→ Si handshake valide: 10-30 min (GPU: secondes)
→ Retourne : PSK trouvé ✓ ou Non trouvé ✗
```

### Points d'Analyse

1. **Présence Handshake :**
   - Tous 4 messages EAPOL visibles ?
   - BSSID et Client MAC clairs ?

2. **Qualité Données :**
   - Combien de données frames (payload size distribué) ?
   - Direction (AP→Client ou Client→AP) ?
   - Fragmentation présente ?

3. **Vulnérabilités (si WPA2-PSK) :**
   - Mot de passe faible (< 8 caractères) → facile crackage
   - Réutilisation clé (déchiffrement complet possible avec key recovery)
   - KRACK: Retransmission frame 3 → reinstallation clé

---

## 4. wpa3.pcapng - Connexion WPA3 Pure

### Objectif
Capture d'une connexion WPA3 SAE uniquement (pas WPA2 compat).

### Protocole SAE (Simultaneous Authentication of Equals)

**Remplacement 4-Way Handshake :**

```
WPA2 4-Way:                    WPA3 SAE:
┌──────────────────────┐      ┌──────────────────────┐
│1. AP→Client: ANonce  │      │1. Client→AP: Commit  │
│2. Client→AP: SNonce  │      │   (elliptic curve)   │
│3. AP→Client: GTK     │      │                      │
│4. Client→AP: ACK     │      │2. AP→Client: Confirm │
│                      │      │   (MAC auth)         │
└──────────────────────┘      │                      │
                              │3. Client→AP: Confirm │
                              │   (ACK)              │
                              └──────────────────────┘
```

**Avantages SAE :**
- ✅ Aucune transmission PSK dérivée
- ✅ PMK calculée localement (pas capturable)
- ✅ Résistant dictionary attack hors-ligne
- ✅ Protection faible password intrinsèque
- ✅ Forward secrecy

### Trames Observées

**Pre-Authentication (optionnel) :**
```
FILS Frame (Protocol : 0x88C7)
ou EAPOL Frame (Protocol : 0x88E1)
```

**SAE Commit :**
```
Element ID: 60 (RSN)
AKMP Suite: SAE (00:0F:AC:08)
Commit Payload:
  - Scalar (secret temporaire)
  - Element (point courbe elliptique)
```

**SAE Confirm :**
```
MAC(PMK, "SAE Confirm Seed", MAC_ADDR, NONCE, COUNTER)
Unique pour chaque authentification
Impossible rejouer capture précédente
```

### Points d'Analyse

1. **Absence Handshake Traditionnel :**
   - Pas de 4 frames EAPOL classiques
   - SAE Commit/Confirm à la place
   - Association directement après

2. **Sécurité Supérieure :**
   - Aucune vulnérabilité KRACK
   - Mot de passe visible dans données brutes ? NON
   - Crackable hors-ligne ? NON
   - Status: 🔒 Très sûr même mot de passe faible

3. **Backwards Compatibility :**
   - Support mixed-mode WPA2/WPA3 ?
   - À vérifier dans RSN info element

---

## 5. wpa3_transition_wpa3client_24ghz.pcapng - Mode Transition WPA2/WPA3

### Objectif
Capture client WPA3 se connectant à un AP en mode mixed (WPA2/WPA3).

### Configuration Mixed Mode

**AP Config :**
```
WPA Version: Both WPA2 & WPA3
  - Cipher Suite: CCMP (AES)
  - Key Management: PSK + SAE
  - Transition Disable: 0 (mixed mode actif)
```

**Client Behavior :**
```
Identifie WPA2 + WPA3 available
Préfère WPA3 SAE si supporté
Fallback WPA2-PSK sinon
```

### Trames Attendues

**Beacon WPA/RSN IE (Info Element) :**
```
IE: RSN Information Element
└─ Version: 1
└─ Group Cipher Suite: CCMP
└─ Pairwise Cipher Suites:
    - CCMP (WPA2)
└─ Authentication Key Management Suites:
    - WPA2-PSK (0x02)
    - SAE (0x08)
└─ Capabilities:
    - Preauth: No
    - PTK-Rekey: Capable
    - Transition Disable: [SET/UNSET]
```

### Selection Protocol

```
Client analyzes AP capabilities:
├─ WPA3 SAE available ? YES
│  └─→ Utilise SAE (mode sécurisé)
│      └─→ Commit/Confirm SAE
│          └─→ Association
│
└─ WPA3 SAE available ? NO
   └─→ Fallback WPA2-PSK
       └─→ 4-Way Handshake
           └─→ Association
```

### Points d'Analyse

1. **Methode Authentification Utilisée :**
   - Trames visibles: SAE ou 4-Way ?
   - Timing différent ?
   - PMK capturée ou pas ?

2. **Security Downgrade :**
   - Transition Disable bit : Prévient downgrade forcé
   - Client ignore beacon WPA2-only ?

3. **Compatibility Issues :**
   - AP et client négocient bien ?
   - Pas d'incompatibilité SAE ?
   - Timeouts ou retransmissions ?

---

## 6. mistap-5-6ghz.pcapng - Multi-Bande 5GHz & 6GHz

### Objectif
Capture sur bandes haute fréquence, démonstration Wi-Fi 6E (802.11ax avec 6 GHz).

### Spécifications

**Bandes Capturées :**

| Bande | Canaux | Largeur | Notes |
|-------|--------|---------|-------|
| 5 GHz | 36-165 | 20/40/80/160 MHz | UNII-1,2,3,4 |
| 6 GHz | 1-233 | 20/40/80/160/320 MHz | Nouveau, spectre vierge |

**Canaux 5 GHz (UNII) :**
```
UNII-1:  36-48 (5150-5250 MHz)   Faible puissance, intérieur
UNII-2:  52-144 (5250-5725 MHz)  DFS required, outdoor
UNII-3:  149-165 (5725-5850 MHz) Haute puissance, intérieur/outdoor
```

**Canaux 6 GHz (nouveau) :**
```
Bande basse:    6 GHz-1 à 6 GHz-45  (5925-6425 MHz)   Very High Throughput (VHT)
Bande haute:    6 GHz-47 à 6 GHz-233 (6425-7125 MHz)  EHT (802.11be)
```

### Capture Points

**Operation Channel :**
```
Primary Channel: 36 (5GHz) ou 1 (6GHz)
Secondary Channel: ≤ 160/320 MHz
Current Channel: Listed dans Management Frames
```

**Frames Observés :**

1. **Beacons multi-band :**
```
AP announces both 5 & 6 GHz capability
└─ Co-located AP same BSSID or different
└─ Clients peuvent choisir bande
```

2. **Client Association :**
```
Client Probe: "Any SSID?" → Both bands
AP Probe Response: "Available on Ch 36 & Ch 1"
Client chooses (généralement 6 GHz if available due to performance)
```

3. **Data Transfer :**
```
High throughput data sur bande 6 GHz
└─ Large frames (up to 11 KB)
└─ High bit rate (1.2+ Gbps)
```

### MLO (Multi-Link Operation) Indicators

**Wi-Fi 6E+ Features :**
```
IE: Multi-Link element
└─ Links negotiated:
   ├─ Link 0: 5 GHz band
   ├─ Link 1: 6 GHz band
   └─ Simultaneous transmit possible

Frame agregation across bands
Enhanced throughout
```

### Points d'Analyse

1. **Bande Préférée :**
   - Client préfère 6 GHz si available ?
   - Signal strength (RSSI) bandes différentes ?
   - Roaming entre bandes observé ?

2. **Channel Info :**
   - Largeur canal utilisée ?
   - DFS triggers sur UNII-2 ?
   - TPC power reduction ?

3. **Performance :**
   - Packet size (1500 bytes standard) ?
   - Fragmentation threshold ?
   - Retransmit rate (good < 5%, bad > 20%) ?

---

## 7. airtool_ap45-5-6ghz.pcapng - Capture Professionnelle (6.4 MB)

### Objectif
Capture longue durée avec outil professionnel Airtool (Ekahau / similar).

### Contenu Attendu

**Données Enrichies :**
```
Standard PCAP + éléments propriétaires:
  - Signal strength par client
  - Timing précis (nanoseconds)
  - Informations AP additionnelles
  - Data rate par frame
  - Channel bandwidth usage
  - Interference markers
```

### Structure Fichier

**Volume :** 6.4 MB = ~1-2 heures capture haute densité

**Contenu Typique :**
```
1. Multiple APs observés (non juste 1)
2. Nombreux clients (10-100+)
3. Trafic continu (download, streaming)
4. Transitions entre APs (roaming)
5. Problèmes réseau (interférences, timeouts)
```

**Frames Très Variés :**

| Type | % Trafic | Importance |
|------|----------|-----------|
| Beacon | 5-10% | Découverte AP |
| Data | 70-80% | Trafic réel |
| ACK/BA | 10-20% | Contrôle flux |
| Mgmt | 2-5% | Assoc/Deauth |
| Probe | <1% | Scanning |

### Analyse Professionnelle

**Metrics Clés :**

```
1. Air Time Utilization:
   - % temps canal occupé
   - AP ↔ Client contention
   - Hidden node problem

2. Signal Quality:
   - RSSI distribution
   - SNR (Signal-to-Noise Ratio)
   - Coverage gaps

3. Throughput Analysis:
   - Goodput (données utiles)
   - TCP Retransmits
   - Latency histogram

4. Roaming Events:
   - Frequency & timing
   - AP pairs involved
   - Success vs failure

5. Security Observations:
   - EAP authentication logs
   - Re-authentication intervals
   - Certificate chain (if 802.1X)
```

### Points d'Analyse

1. **Performance Baseline :**
   - Typical throughput per client ?
   - Min/max RSSI ranges ?
   - Acceptable latency ? (< 50 ms)

2. **Problem Identification :**
   - Excessive retransmits (> 20%) ?
   - Clients struggling on edge coverage ?
   - Interference patterns ?
   - Co-channel interference AP → ?

3. **Security Posture :**
   - All clients WPA3 capable ?
   - Enterprise 802.1X success rate ?
   - Deauth attacks observed ?
   - KRACK symptoms ?

4. **Design Recommendations :**
   - AP placement optimization ?
   - Channel plan revisions ?
   - Band steering (2.4 → 5/6 GHz) ?
   - Client firmware updates needed ?

---

## Techniques d'Analyse PCAP Avancées

### 1. Décryptage WPA2/WPA3

**Avec Wireshark :**
```
Edit → Preferences → Protocols → IEEE 802.11 → Decryption keys
Format: ssid:password ou Key Type:0:hex_pmk
```

**Résultat :**
- Voir contenu IP (HTTP, HTTPS TLS, DNS)
- Analyser TCP handshake
- Identify applicati protocols
- Detect anomalies (DDoS, data exfil, etc)

### 2. Extraction Handshake

**Pour Crackage :**
```bash
# aircrack-ng extract
aircrack-ng -1 /path/to/capture.cap -w output.cap

# Convertir PCAPNG → CAP
editcap input.pcapng output.cap

# Valider handshake
aircrack-ng -a 2 input.cap --show
```

### 3. Forensics Timeline

**Reconstruct Event Sequence :**
```
1. Timestamp first Probe Request (client looking)
2. Timestamp AP Beacon received
3. Timestamp Auth Request/Response
4. Timestamp Association
5. Timestamp EAPOL Key exchange
6. Timestamp first Data frame
7. Timestamp Deauth frame
```

**Delta Time Analysis :**
- Auth latency: ~50-100 ms
- Association latency: ~20-50 ms
- Handshake latency: ~100-500 ms
- Total connection time: 500-2000 ms

### 4. Anomaly Detection

**Red Flags :**
- ❌ Multiple auth failures (WPA mismatch)
- ❌ Excessive deauthentication
- ❌ Suspicious MAC addresses (spoofing)
- ❌ Unencrypted data transmission
- ❌ Rogue AP detection (SSID clones)
- ❌ WEP/Open networks without reason
- ❌ EAPOL timeout chains
- ❌ Interleaved SAE/4-Way (downgrade attack)

### 5. Statistics & Visualization

**Wireshark Tools :**
```
Statistics → Wireless
├─ Capture Summary (frame counts)
├─ Airtime usage (% per AP)
├─ Clients list (MAC, RSSI, frames)
└─ Channels histogram

Statistics → Packet Lengths
└─ Distribution graphs
```

### 6. Grep / CLI Analysis

**Extract specific frames (tshark required):**
```bash
# Beacon frames only
tshark -r capture.pcap -Y 'wlan.fc.type == 0 && wlan.fc.subtype == 8'

# EAPOL handshake
tshark -r capture.pcap -Y 'eapol.keydes.type'

# Data frames
tshark -r capture.pcap -Y 'wlan.fc.type == 2'

# Deauth events
tshark -r capture.pcap -Y 'wlan.fc.type == 0 && wlan.fc.subtype == 12'
```

---

## Résumé Comparative

### Sécurité Observée par Fichier

| Fichier | Type | Sécurité | Niveau Risque |
|---------|------|----------|---------------|
| dot11-sample | Lab/Demo | WPA2 4-Way | Bas (exemple) |
| Nokia Mobile | Réel | WPA2-PSK | Moyen (PSK crackable) |
| wpa-Induction | Lab | WPA2 Encrypted | Bas (handshake for cracking) |
| wpa3.pcapng | Lab | WPA3 SAE | Très Haut 🔒 |
| wpa3_transition | Lab | WPA2+WPA3 Mixed | Haut (best of both) |
| mistap-5-6ghz | Professionnel | 802.11ax/be | Très Haut (modern) |
| airtool-6.4MB | Professionnel | Multi-AP, Multi-Client | Contexte-dépendant |

### Capacités Attaque par Scénario

| Attaque | dot11 | Nokia | wpa-Ind | wpa3 | transition | mistap | airtool |
|---------|-------|-------|---------|------|------------|--------|---------|
| **Passive interception** | ✓ | ✓ | ✓ (chiffré) | ✗ | ✓ | ✗ | ✗ |
| **Dictionary crack** | ✓ | ✓ | ✓ | ✗ | ✓ | ✗ | ✗ |
| **KRACK** | ✓ | ✓ | ✓ | ✗ | ✓ | ✗ | ✗ |
| **Evil Twin** | ✓ | ✓ | ✓ | Détectable | ✓ | ✓ | ✓ |
| **Deauth DoS** | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| **SAE timing attack** | N/A | N/A | N/A | Théorique | Possible | Possible | Possible |

---

## Recommandations

### Pour Déploiements Neufs
1. **Obligation WPA3 minimum** (SAE + AES-GCMP)
2. **Support 6 GHz** (Wi-Fi 6E minimum)
3. **802.1X Enterprise** pour données sensibles
4. **Certificats serveur** pour prévenir Evil Twin

### Transition Existants
1. **Phase 1 :** Mixed WPA2/WPA3 (les deux en simultané)
2. **Phase 2 :** SAE-only pour clients modernes
3. **Phase 3 :** Dépréciation WPA2 dans 2-3 ans
4. **Monitoring :** Capture régulière & analyse

### Paramètres de Sécurité Recommandés

```
☑ WPA3-Personal (SAE)
☑ AES-GCMP-256 (ou CCMP si nécessaire)
☑ PMF (Protected Management Frames) mandatory
☑ Transition Disable enabled
☑ Hidden SSID (sécurité supplémentaire)
☑ Client isolation (si café/public)
☑ 802.1X backend (Enterprise)
```

---

## Outils pour Reproduction

**Logiciels nécessaires pour analyse complète :**

1. **Wireshark** - Visualisation & décryption
2. **Aircrack-ng** - Extraction & crackage
3. **tshark** - Analysis CLI
4. **hashcat** - GPU-accelerated cracking
5. **Kali Linux** - Environnement complet
6. **Ekahau** (professionnel) - Airtool équivalent

**Commandes récapitulatives :**

```bash
# Analyser PCAP
wireshark capture.pcap

# Extraire handshake
editcap input.pcapng output.cap
aircrack-ng -1 output.cap

# Tester crackage (hypothétique)
aircrack-ng -a 2 -b AA:BB:CC:DD:EE:FF \
            -w /usr/share/wordlists/rockyou.txt \
            capture.cap

# CLI analysis
tshark -r capture.pcap -Y 'wlan.fc.type == 2' | head -20
```

---

**Document généré le :** 27 mai 2026  
**Version :** 2.0  
**Niveau d'expertise :** Avancé  
**Confidentialité :** Educational Use
