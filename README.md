# Mission Wi-Fi - Documentation Complète

## 📋 Vue d'ensemble

Cette mission couvre une analyse complète du Wi-Fi, couvrant les normes, la sécurité, les protocoles, et l'analyse pratique de captures réseau (PCAP).

**Générée le :** 27 mai 2026

---

## 📚 Documents Créés

### 1. **01_SYNTHESE_WIFI.md** - Référence Théorique Complète
**Durée lecture :** ~45 min | **Niveau :** Intermédiaire à Avancé

Contient:
- ✅ Évolution chronologique normes 802.11 (a/b/g/n/ac/ax/be)
- ✅ Bandes de fréquence (2.4 GHz, 5 GHz, 6 GHz) avec canaux détaillés
- ✅ Architecture Wi-Fi infrastructure
- ✅ Processus d'authentification et association
- ✅ Protocoles de sécurité (WEP ❌, WPA ⚠️, WPA2 ✅, WPA3 🔒)
- ✅ Détails techniques: 4-Way Handshake, SAE, EAPOL
- ✅ Outils d'analyse (Wireshark, Aircrack-ng, tcpdump)
- ✅ Wi-Fi 7 (802.11be) et évolutions futures

**À utiliser pour :**
- Comprendre fondamentaux Wi-Fi
- Référence normes/standards
- Comparaisons WPA2 vs WPA3
- Contexte sécurité réseaux

---

### 2. **02_ANALYSE_PCAP_DETAILLEE.md** - Analyse Fichiers Captures

Analyse de chaque fichier PCAP avec détails:

| Fichier | Contenu | Points-clés |
|---------|---------|-----------|
| **dot11-sample.pcap** (45 KB) | Phases connexion 802.11 | Beacon → Probe → Auth → Assoc → EAPOL |
| **Network_Join_Nokia_Mobile.pcap** (162 KB) | Client mobile rejoignant | Behavior scanning actif, timing |
| **wpa-Induction.pcap** (176 KB) | Données WPA chiffrées | Données avec clé, handshake |
| **wpa3.pcapng** (31 KB) | Connexion WPA3 pure | SAE Commit/Confirm, sécurité 🔒 |
| **wpa3_transition_wpa3client_24ghz.pcapng** (22 KB) | Mode WPA2/WPA3 mixed | Negotiation, selection client |
| **mistap-5-6ghz.pcapng** (1.3 MB) | Multi-band 5/6 GHz | Wi-Fi 6E, canaux, MLO |
| **airtool_ap45-5-6ghz.pcapng** (6.4 MB) | Capture professionnelle | Longue durée, multi-AP/client |

**Inclut :**
- Trames détaillées par phase
- Protocoles EAPOL décortiqués
- Attaques applicables vs défenses
- Indicateurs d'anomalies
- Recommandations sécurité

**À utiliser pour :**
- Comprendre contenu fichiers PCAP
- Analyser trafic Wi-Fi spécifique
- Identifier vulnérabilités
- Vérifier sécurité déploiement

---

### 3. **03_GUIDE_PRATIQUE_OUTILS.md** - Hands-On Avec Outils

Guide commandes étape par étape:

**Sections :**
1. Installation environnement (Kali, aircrack-ng, Wireshark)
2. Capture (mode moniteur, tcpdump, airodump-ng, Wireshark)
3. Analyse (Wireshark GUI, tshark CLI, décryptage, validation handshake)
4. Crackage PSK (aircrack-ng, hashcat GPU, validation)
5. Exploitation (Deauth, Rogue AP, Evil Twin)
6. 3 cas d'usage réels (test réseau perso, pentest autorisé, forensics)
7. Troubleshooting courant

**À utiliser pour :**
- Mettre en pratique la théorie
- Capturez votre propre réseau
- Testez sécurité existant
- Reproduction labs/CTF
- Référence commandes rapide

---

## 🔧 Checkliste Installation Rapide

```bash
# Linux/Debian/Kali
sudo apt-get update
sudo apt-get install -y \
  aircrack-ng \
  wireshark \
  tcpdump \
  hashcat \
  tshark \
  hostapd \
  dnsmasq

# Vérifier
aircrack-ng --version
wireshark --version
hashcat --version

# Cartes Wi-Fi compatible (nécessaire mode moniteur)
lsusb | grep -i "wireless\|realtek\|atheros\|intel"
```

---

## 🎬 Labs Rapides (15 min chacun)

### Lab 1: Vérifier Sécurité Propre AP
```bash
# 1. Activer mode moniteur
sudo airmon-ng start wlan0

# 2. Lister réseaux
sudo airodump-ng wlan0mon

# 3. Voir sécurité (WPA2/WPA3/etc)
# Colonne "ENC" → WPA2 ou WPA3

# 4. Voir clients connectés
# Colonne sous chaque AP
```

### Lab 2: Capturer Handshake Personnel
```bash
# 1. Capture en boucle (~30 sec)
sudo airodump-ng --bssid YOUR_BSSID -c 6 -w capture wlan0mon &

# 2. Forcer reconnexion client
sudo aireplay-ng --deauth 5 -a YOUR_BSSID wlan0mon

# 3. Vérifier capture (Ctrl+C capture)
aircrack-ng capture-01.cap --show
# Si "1 handshake" → ✓ Succès
```

### Lab 3: Analyser Fichier PCAP
```bash
# 1. Ouvrir Wireshark
wireshark capture.pcap &

# 2. Filtrer beacons
wlan.fc.type == 0 && wlan.fc.subtype == 8

# 3. Filtrer EAPOL
eapol

# 4. Compter frames
tshark -r capture.pcap -Y 'wlan.fc.type == 2' | wc -l
```

---

## 📊 Comparatif Sécurité Protocoles

| Protocole | Sécurité | Crackable? | Vulnérabilités | Recommandation |
|-----------|----------|-----------|-----------------|----------------|
| **WEP** | ❌ Nulle | ✓ Oui (secondes) | RC4 faible, IV court, FMS | ❌ BANNIR |
| **WPA** | ⚠️ Faible | ✓ Oui (hours) | TKIP, RC4 legacy | ❌ DÉPRÉCIÉE |
| **WPA2-PSK** | ✅ Bon | ✓ Oui (GPUs) | KRACK, dict attack | ✅ ACCEPTABLE |
| **WPA2-Enterprise (EAP-TLS)** | ✅✅ Excellent | ✗ Non (certs) | Certificat spoofing? | ✅ RECOMMANDÉ |
| **WPA3-SAE** | 🔒 Excellent | ✗ Non (SAE) | Timing attack théo. | 🔒 OBLIGATOIRE |
| **WPA3-Enterprise (EAP-TLS)** | 🔒🔒 Excellissime | ✗ Non | Aucune connue | 🔒 GOLD STANDARD |

---

## ⚠️ Avertissements Légaux

### ✅ Légal
```
✓ Tester VOTRE propre réseau
✓ Environnement lab/de développement
✓ Pentest avec contrat SIGNÉ
✓ Recherche académique autorisée
✓ Divulgation responsable vulnérabilités
```

### ❌ ILLÉGAL
```
✗ Accès réseau tiers sans permission
✗ Déauthentification DoS non-autorisée
✗ Rogue AP / Evil Twin sans consentement
✗ Interception données sans warrant
✗ Crackage PSK réseau d'autrui
✗ Non-respect RGPD/CCPA données personnelles
```

**Conséquences :** 5-20 ans prison + amende (selon juridiction)

---

## 🔗 Ressources Externes

### Documentation
- [IEEE 802.11 Standards](https://www.ieee802.org/11/)
- [Wi-Fi Alliance WPA3 Specs](https://www.wi-fi.org/discover-wi-fi/security)
- [Aircrack-ng Wiki](https://www.aircrack-ng.org/doku.php)
- [Wireshark Official Docs](https://www.wireshark.org/docs/)

### Formation
- [HackTheBox](https://www.hackthebox.com/) - Labs pratiques
- [TryHackMe](https://tryhackme.com/) - Chemins guidés
- [YouTube - NetworkChuck](https://www.youtube.com/@NetworkChuck) - Tutoriels Wi-Fi
- [IPPSec](https://www.youtube.com/c/IppSec) - Networking avancé

### Communautés
- [r/WiFi](https://www.reddit.com/r/WiFi/) - Communauté Reddit
- [Aircrack-ng GitHub Issues](https://github.com/aircrack-ng/aircrack-ng/issues)
- [Wireshark Forums](https://ask.wireshark.org/)

---

## 📝 Checklist Avant Commencer

- [ ] Lire ce README entièrement
- [ ] Choisir niveau (débutant/intermédiaire/avancé)
- [ ] Installer outils recommandés (Kali/Wireshark/aircrack)
- [ ] Disposer carte Wi-Fi compatible mode moniteur
- [ ] Lire chapitre sécurité/légal
- [ ] Préparer environnement lab (VM ou petit AP test)
- [ ] Commencer par 01_SYNTHESE_WIFI
- [ ] Progresser vers labs pratiques

---

## 🚀 Prochaines Étapes

1. **Court terme (1 mois)** : Maîtriser théorie (synthèse + PCAP)
2. **Moyen terme (3-6 mois)** : Pratique intensive (labs + projets)
3. **Long terme (6+ mois)** : Spécialisation (pentest / research / enseignement)

---

**Dernière mise à jour :** 27 mai 2026  
**Format :** Markdown  
**Licence :** Educational Use Only
