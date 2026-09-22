# 📡 Analyse de Performance du Système Wi-Fi FPV / Télémétrie

Ce document détaille les performances du système de transmission basé sur un ESP32-C5 (TX), ses différentes configurations de réception (RX), et les pistes d'améliorations futures.

## 1. Caractéristiques d'Émission (TX) de l'ESP32-C5

Le système actuel force l'utilisation du **5 GHz sur le canal 36**, ce qui est stratégique : cette bande (5170-5250 MHz) est explicitement autorisée par l'ARCEP pour les drones avec une limite de 200 mW EIRP. 

Voici la puissance brute d'émission de la puce avant le gain de l'antenne lollipop (~2 dBi). 
Avec l'antenne, le système culmine à environ **45-50 mW EIRP**, ce qui est parfaitement conforme et laisse même une marge réglementaire.

```
xychart-beta
    title "ESP32-C5-WROOM-1U — Puissance d'émission 5 GHz selon le mode (données constructeur)"
    x-axis ["802.11a (6 Mbps)", "802.11n HT20 (MCS7)", "802.11n HT40 (MCS7)", "802.11ac/ax"]
    y-axis "Puissance TX (dBm)" 0 --> 20
    bar [16.5, 14.5, 13.5, 14.5]
```

## 2. Le Projet de Base (Comparaison des Récepteurs)

Le vrai levier d'optimisation de ce projet se situe au niveau de la **réception (RX)**. Le tableau suivant compare le système de base selon qu'on utilise un simple smartphone ou le récepteur cible sous Kali Linux.

| Configuration RX | Équipement | Sensibilité / Gain | Portée Théorique (LOS) | Portée Réelle Estimée (Divisé par ~2.5) |
| :--- | :--- | :--- | :--- | :--- |
| **Base (Actuelle)** | Smartphone standard (gratuit) | Antennes internes médiocres | ~ 230 m | **~ 90 m** |
| **Base (Cible)** | Clé CHANEVE RTL8812AU + 2x 5 dBi | Très haute sensibilité (-90 dBm) | ~ 920 m | **~ 360 m** |

**Bilan du projet de base :** L'ajout de la carte réseau CHANEVE (avec ses antennes omnidirectionnelles 5 dBi d'origine) augmente le budget de liaison d'environ 12 dB par rapport au smartphone. En radiofréquence, cela se traduit par un multiplicateur de portée de x4. **On gagne donc près de 690m de portée théorique uniquement en améliorant le module VRX.**

## 3. Piste d'Amélioration : Amplification du Signal (TX)

Pour aller plus loin, particulièrement pour le vol à travers des obstacles (arbres, murs), la piste d'amélioration consiste à booster l'émission en ajoutant un **amplificateur de signal**.

*   **Le principe :** Insérer un amplificateur RF entre l'ESP32 et l'antenne Lollipop du drone.
*   **L'impact (Pénétration) :** Un obstacle n'arrête pas un signal, il l'atténue. En passant par exemple d'une émission de 50 mW à 1 ou 2 Watts, le signal dispose d'une "réserve d'énergie" massive. Même après avoir été fortement atténué par des murs ou du feuillage, le signal résiduel sera suffisamment fort pour être décodé par la puce RTL8812AU.
*   **Portée :** La combinaison de l'ampli TX et du récepteur haute sensibilité repousse la portée théorique à plus de 2 kilomètres en champ libre.

## 4. Évolution des Performances et de l'Investissement

Voici l'évolution du système, de sa version la plus basique (avec téléphone) vers son potentiel maximal (avec ampli), en conservant les antennes omnidirectionnelles d'origine de la clé.

```
xychart-beta
    title "Évolution du Coût Total du Système (€)"
    x-axis ["Base (Tél. gratuit)", "Base (RX Chaneve)", "Piste (RX + Ampli TX)"]
    y-axis "Prix total (€)" 0 --> 100
    bar [30, 54, 89]
```

```
xychart-beta
    title "Évolution de la Portée Théorique Maximale (mètres)"
    x-axis ["Base (Tél. gratuit)", "Base (RX Chaneve)", "Piste (Ampli TX)"]
    y-axis "Portée (m)" 0 --> 2500
    bar [230, 920, 2200]
```

## 5. Positionnement sur le Marché FPV (Prix / Performances)

Le projet de base (ESP32 + Clé CHANEVE RTL8812AU à **~54€ au total**) se positionne comme une alternative "Low Cost / High Range" face aux solutions du marché.

```
xychart-beta
    title "Comparaison des Coûts d'entrée : Ton système face au marché FPV (€)"
    x-axis ["Ton Système (Base cible)", "Analogique FPV", "Walksnail / HDZero", "DJI O3 (Air Unit)"]
    y-axis "Coût estimé (€)" 0 --> 300
    bar [54, 110, 180, 280]
```

### Analyse de l'Efficacité Économique (Portée par Euro investi)
*Basé sur la portée théorique pour le ratio.*

```
xychart-beta
    title "Indice de Valeur : Mètres de portée théorique gagnés par Euro (m/€)"
    x-axis ["Ton Système (Base cible)", "Analogique FPV", "Walksnail", "DJI O3"]
    y-axis "Ratio (m/€)" 0 --> 25
    bar [17.0, 9.1, 13.8, 14.2]
```

**Conclusion :** Même sans antenne unidirectionnelle, le combo ESP32 + RTL8812AU offre le meilleur rapport "Mètres de portée par Euro investi" du marché, pulvérisant l'analogique d'entrée de gamme et tenant tête aux systèmes numériques fermés et onéreux. L'ajout futur d'un amplificateur TX permettra de transformer ce système économique en un outil capable de forte pénétration (Bando/Forêt).