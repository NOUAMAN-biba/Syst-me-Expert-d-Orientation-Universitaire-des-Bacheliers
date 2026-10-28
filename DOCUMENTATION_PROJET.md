# Documentation du Mini-Projet : Système Expert d'Orientation Universitaire Post-Bac (Maroc)

**Module :** Fondements avancés de l'Intelligence Artificielle (Master IA – S1)  
**Enseignante :** Pr. Hasna ELALAOUI  
**Année universitaire :** 2026-2027  
**Date limite de livraison :** Dimanche 13 Décembre 2026 à Minuit  
**Livrables attendus :**
1. Code source Prolog complet (`.pl`)
2. Code de l'interface utilisateur IHM
3. Rapport détaillé (`.pdf`)

---

## 1. Contexte & Objectif du Projet

Chaque année au Maroc, les bacheliers font face à un choix complexe parmi une offre de formations très diversifiée (Grandes écoles d'ingénieurs, commerce, facultés de médecine, instituts techniques, prépas CPGE, BTS, OFPPT, universités...).  
Le but de ce projet est de concevoir un **système expert d'orientation explicable et interactif** en **Prolog**, capable de :
* Recueillir le profil complet de l'élève (type de bac, moyenne générale et régionale, notes par matière clé, centres d'intérêt, contraintes de ville et de budget, préférence public/privé).
* Interroger une base de connaissances exhaustive sur les formations post-bac au Maroc.
* Proposer un classement hiérarchisé de recommandations (Top 5) catégorisées en :
  * **Ambitieux** (seuil élevé ou notes proches de la limite, concours sélectif)
  * **Réaliste** (adéquation équilibrée entre le profil scolaire et les critères de sélection)
  * **Sûr** (profil très supérieur au seuil ou filière à accès ouvert / garanti)
* Fournir une **justification explicable** ("Pourquoi cette formation ?", points forts, avertissements sur les matières faibles, alternatives de repli).

---

## 2. Structure de la Base de Connaissances (`formations.csv`)

Le fichier `formations.csv` a été totalement revu et enrichi pour passer de 60 à **100 formations réelles et représentatives** couvrant l'ensemble du territoire marocain et tous les profils de bacheliers.

### Colonnes du fichier (12 attributs) :
1. **`ID`** : Identifiant numérique unique (1 à 100).
2. **`Name`** : Nom de l'établissement et de la filière (ex. *ENSA Marrakech*, *CPGE TSI Moulay Youssef*, *ISTA Sidi Maarouf*).
3. **`Type`** : Type d'établissement (`ecole_ingenieur`, `commerce`, `science_technique`, `technique`, `prepa`, `medecine`, `dentaire`, `paramedical`, `formation_professionnelle`, `education`, `architecture`, `design`, `journalisme`, `tourisme`, `droit`, `lettres_sciences_humaines`, `universite_sciences`, `militaire`, `polytechnique`).
4. **`City`** : Ville d'implantation normalisée en minuscules (`casablanca`, `rabat`, `marrakech`, `fes`, `tanger`, `agadir`, `oujda`, `settat`, `meknes`, `kenitra`, etc.).
5. **`AcceptedBacs`** : Séries de bac éligibles séparées par des points-virgules :
   * `sm` : Sciences Mathématiques (A et B)
   * `spc` : Physique - Chimie
   * `svt` : Sciences de la Vie et de la Terre
   * `eco` : Sciences Économiques
   * `tgc` : Techniques de Gestion et Comptabilité
   * `ste` : Sciences et Technologies Électriques
   * `stm` : Sciences et Technologies Mécaniques
   * `lettres` : Lettres
   * `humaines` : Sciences Humaines
   * `arts_appliques` : Arts Appliqués
   * `toutes` : Toutes séries confondues
6. **`Threshold`** : Seuil indicatif de présélection sur 20 (ex: `16.5` pour médecine, `14.5` pour ENSA, `10.0` pour les facultés ouvertes).
7. **`KeySubjects`** : Matières décisives pour réussir dans cette filière (`maths`, `physique`, `svt`, `francais`, `anglais`, `arabe`, `economie`, `dessin`, `pedagogie`, etc.).
8. **`SelectionMode`** : Mode de sélection (`tawjihi`, `cursussup`, `concours`, `dossier`, `ouvert`).
9. **`Duration`** : Durée du cursus en années (2 ans pour BTS/OFPPT, 3 ans pour Licence/EST/ESEF, 5 ans pour ENSA/ENCG, 6 à 7 ans pour Médecine/Architecture).
10. **`Debouches`** : Mots-clés des métiers et débouchés professionnels associés pour le calcul d'affinité avec les centres d'intérêt de l'élève.
11. **`Status`** : Statut juridique de l'établissement (`public` ou `prive`).
12. **`AnnualFees`** : Frais de scolarité annuels en Dirhams marocains (0 DH pour les universités et grandes écoles publiques, ~800 DH pour BTS/OFPPT, 25 000 à 105 000 DH pour les écoles privées/partenariats).

---

## 3. Typologie des 100 Formations intégrées

La base couvre équitablement l'ensemble des besoins des étudiants marocains :
* **Écoles d'ingénieurs publiques (12 ENSA + 3 ENSAM) :** Marrakech, Agadir, Tanger, Fès, Rabat, Casablanca, Meknès, Oujda, Kénitra, El Jadida, Safi, Khouribga, Tétouan, Berrechid, Al Hoceima.
* **Écoles de management (10 ENCG + ISCAE) :** Casablanca, Settat, Tanger, Agadir, Marrakech, Fès, Oujda, Kénitra, El Jadida, Dakhla.
* **Facultés des Sciences et Techniques (7 FST) :** Settat, Mohammedia, Fès, Marrakech, Tanger, Béni Mellal, Errachidia.
* **Écoles Supérieures de Technologie (6 EST) :** Casablanca, Salé, Fès, Agadir, Oujda, Safi.
* **Classes Préparatoires aux Grandes Écoles (6 CPGE) :**
  * *MPSI / PCSI :* Mohamed V (Casa), Moulay Idriss (Fès).
  * *TSI (Technique) :* Moulay Youssef (Rabat), Reda Slaoui (Agadir).
  * *ECT (Économie) :* Al Khansaa (Casa), Omar Ibn Al Khattab (Meknès).
* **Santé & Paramédical (12 formations) :**
  * Facultés de Médecine et Pharmacie (Casablanca, Rabat, Marrakech, Fès, Oujda).
  * Facultés de Médecine Dentaire (Rabat, Casablanca).
  * Instituts ISPITS (Casablanca, Rabat, Marrakech, Fès, Agadir).
* **Formations Courtes Professionnalisantes Bac+2 (9 formations) :**
  * **OFPPT / ISTA :** Développement Digital, Infrastructure Cloud/Cyber, Gestion PME, Électromécanique, Logistique.
  * **BTS :** DSI (Informatique), Comptabilité & Gestion, Bâtiment & Génie Civil, Électrotechnique.
* **Éducation & Enseignement (4 formations) :** ESEF Agadir, ESEF Kénitra, ENS Marrakech, ENS Rabat.
* **Instituts Spécialisés d'État (10 formations) :**
  * IAV Hassan II (Agronomie / Vétérinaire).
  * ISIC Rabat (Journalisme & Médias).
  * ISITT Tanger (Tourisme & Hôtellerie).
  * ENA (Architecture à Rabat, Marrakech, Fès).
  * ENSAD Casablanca & INBA Tétouan (Arts & Design).
  * AIAC Nouaceur (Aéronautique), ERN Casablanca (Marine), ARM Meknès (Militaire).
  * INPT (Télécoms), INSEA (Statistique / Data), ESI (Sciences de l'information).
* **Facultés Universitaires à Accès Ouvert - Formations de sécurité (9 filières) :**
  * FSJES : Droit en Français (Rabat), Droit en Arabe (Marrakech), Économie-Gestion (Casablanca).
  * FLSH : Études Anglaises (Rabat), Études Françaises (Casablanca), Sociologie-Histoire (Fès).
  * Facultés des Sciences (FS) : SMI Informatique (Marrakech), SMA Mathématiques (Rabat), SVI Biologie (Casablanca).
* **Universités Privées & Partenariats d'Excellence (6 formations) :**
  * UM6P Benguerir (Ingénierie & SAP+D Architecture).
  * UIR Rabat (Informatique / Réseaux).
  * Al Akhawayn Ifrane (Pluridisciplinaire anglo-saxon).
  * EMSI Casablanca (Ingénierie privée).
  * ESITH Casablanca (Textile & Logistique).

---

## 4. Architecture Technique du Système Expert

```
[ formations.csv ]
        │
        ▼ (script Python : csv_to_prolog.py)
[ knowledge_base.pl ] (Faits Prolog formatés)
        │
        ▼
[ expert_system.pl ] (Moteur de règles & inférence Prolog)
   ├── 1. Filtrage d'éligibilité (Bac, Budget)
   ├── 2. Calcul du score d'adéquation (Moyenne, Matières clés, Intérêts, Ville)
   ├── 3. Classification (Ambitieux / Réaliste / Sûr)
   └── 4. Moteur d'explication (Pourquoi ? Points forts, Alertes risques)
        │
        ▼
[ Interface IHM ] (Streamlit en Python relié via PySwip OU Interface Web moderne / CLI interactive)
```

### Règles d'Inférence Principales :
1. **Éligibilité stricte :**
   * Le baccalauréat du candidat doit appartenir à la liste `AcceptedBacs` (ou `toutes`).
   * Le budget annuel maximum indiqué par l'étudiant doit être supérieur ou égal à `AnnualFees`.
2. **Évaluation du Risque :**
   * Si la note de l'élève dans une des `KeySubjects` est inférieure à 12/20 $\rightarrow$ Déclencher une alerte "Matière clé fragile".
   * Si la moyenne de l'élève est inférieure ou trop proche du seuil indicatif (écart $< 0.5$) $\rightarrow$ Classer comme choix à risque / Ambitieux.
3. **Catégorisation des Recommandations :**
   * **Ambitieux :** Moyenne entre $[\text{Seuil} - 0.5, \text{Seuil} + 0.8]$ ou filière ultra-sélective sur concours (CPGE, Médecine, ENA).
   * **Réaliste :** Moyenne $\ge \text{Seuil} + 0.5$ avec notes satisfaisantes en matières clés ($\ge 13/20$) et affinité de centres d'intérêt.
   * **Sûr :** Moyenne $\ge \text{Seuil} + 2.0$ ou filières à accès ouvert / formations courtes assurées (Faculté, BTS, OFPPT).
4. **Moteur d'Explication :**
   * Structure de la justification :
     * *Raison positive :* Bac correspondant, moyenne au-dessus du seuil (+X pts), correspondance avec le domaine souhaité.
     * *Avertissement :* Ville en 2e choix, note clé moyenne, concours éliminatoire.
     * *Repli conseillé :* Alternative sécurisée en cas d'échec au concours.

---

## 5. Prochaines Étapes Opérationnelles

1. **Génération de la base de faits Prolog :**
   * Écrire un script Python `build_kb.py` qui lit `formations.csv` et génère proprement `knowledge_base.pl`.
2. **Développement du moteur de règles Prolog :**
   * Implémenter les prédicats de recommandation, scoring, filtrage et explication dans `expert_system.pl`.
3. **Création de l'Interface Utilisateur (IHM) :**
   * Proposer une IHM élégante et conviviale (ex: Web avec Streamlit ou interface interactive) pour recueillir les réponses de l'étudiant et afficher les Top 5 recommandations avec badges colorés (*Ambitieux*, *Réaliste*, *Sûr*).
4. **Rédaction du Rapport Final PDF :**
   * Formalisation théorique de la logique propositionnelle/prédicats, architecture du système expert, captures d'écran des tests et conclusion.
