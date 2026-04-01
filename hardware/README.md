# 🛠️ Matériel & Fabrication (Hardware)

Ce dossier contient tous les fichiers techniques nécessaires pour commander et fabriquer votre passerelle **TOUG**. 

> [!IMPORTANT]
> Les fichiers sont organisés selon le **Parcours (Pack)** que vous avez choisi dans le tutoriel complet :  
> 👉 **[Article HACF - TOUG : passerelle PAC Aldes T.One - la fabrication de A à Z (partie 2)](https://forum.hacf.fr/t/article-toug-passerelle-pac-aldes-t-one-la-fabrication-de-a-a-z-partie-2/77292)**

---

### 📋 Correspondance des fichiers par parcours

| Votre Choix | 🛠️ Étape 1 : Le PCB (JLCPCB) | 🛒 Étape 2 : Composants (LCSC) | 🔌 Étape 3 : Câblage (LCSC) |
| :--- | :--- | :--- | :--- |
| **🥇 Parcours OR** | `jlcpcb/gerber_or_argent.zip` | `lcsc/bom_or_all_inclusive.csv` | `lcsc/bom_cables_all_inclusive.csv` |
| **🥈 Parcours ARGENT** | `jlcpcb/gerber_or_argent.zip` | `lcsc/bom_argent_access.csv` | `lcsc/bom_cables_access.csv` |
| **🥉 Parcours BRONZE** | Dossier complet `jlcpcb/bronze/` | *(Composants soudés en usine)* | `lcsc/bom_cables_access.csv` |

---

### 📁 Détails des dossiers

#### 1. [easyeda/](./easyeda/)
Contient le fichier source `TOUG.json`. Importez-le dans l'éditeur en ligne **EasyEDA** si vous souhaitez modifier le schéma électronique ou le tracé du PCB.

#### 2. [jlcpcb/](./jlcpcb/)
* **`gerber_or_argent.zip`** : À envoyer sur le site de JLCPCB pour faire fabriquer la plaque (PCB nu).
* **Sous-dossier `bronze/`** : Contient le trio indissociable (Gerber, BOM, CPL) nécessaire pour l'option avec **assemblage CMS** (composants déjà soudés par le fabricant).

#### 3. [lcsc/](./lcsc/)
Fichiers à importer dans l'outil "Upload BOM" de LCSC pour remplir votre panier automatiquement :
* **`bom_..._all_inclusive.csv`** : La liste complète des composants pour le Pack All Inclusive.
* **`bom_..._access.csv`** : Uniquement les composants spécifiques au Pack Access du Parcours Argent.
* **`bom_cables_...csv`** : Les connecteurs (boîtiers et contacts) pour fabriquer les câbles.

---

### 💡 Aide au montage
N'oubliez pas de suivre le **[tutoriel complet de fabrication](https://forum.hacf.fr/t/article-toug-passerelle-pac-aldes-t-one-la-fabrication-de-a-a-z-partie-2/77292)** sur HACF pour les instructions de soudure et les pièges à éviter !