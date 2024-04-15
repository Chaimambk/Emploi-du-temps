import sqlite3
from Base_de_donnée import convert_to_float

# Fonction pour charger les données de chaque table
def charger_donnees_de_la_base():
    conn = sqlite3.connect('base_de_donnee.db')
    cursor = conn.cursor()

    # Charger les données de la table Enseignant
    cursor.execute("SELECT * FROM Enseignant")
    donnees_enseignants = cursor.fetchall()

    # Charger les données de la table Contraintes_enseignants
    cursor.execute("SELECT * FROM Contraintes_enseignants")
    donnees_contraintes = cursor.fetchall()

    # Charger les données de la table Indisponibilites_enseignants
    cursor.execute("SELECT * FROM Indisponibilites_enseignants")
    donnees_indisponibilites = cursor.fetchall()

    # Charger les données de la table Promo
    cursor.execute("SELECT * FROM Promo")
    donnees_promo = cursor.fetchall()

    # Charger les données de la table Matiere
    cursor.execute("SELECT * FROM Matiere")
    donnees_matiere = cursor.fetchall()

    # Charger les données de la table Salle_De_Classe
    cursor.execute("SELECT * FROM Salle_De_Classe")
    donnees_salles = cursor.fetchall()

    # Charger les données de la table Horaire
    cursor.execute("SELECT ID_Matiere, ID_Promo, ID_Salle_De_Classe, Jour_Semaine, Heure_Debut, Heure_Fin FROM Horaire")
    donnees_horaire = cursor.fetchall()

    # Fermer la connexion à la base de données
    conn.close()

    # Retourner les données chargées
    return donnees_enseignants, donnees_contraintes, donnees_indisponibilites, donnees_promo, donnees_matiere, donnees_salles, donnees_horaire

# Fonction pour exécuter l'algorithme d'ordonnancement pour un seul enseignant
def executer_algorithm_d_ordonnancement_enseignant(data, id_enseignant):
    donnees_enseignants, donnees_contraintes, donnees_indisponibilites, donnees_promo, donnees_matiere, donnees_salles, donnees_horaire = data

    # Initialisez un dictionnaire pour stocker les horaires déjà pris pour chaque jour de la semaine
    emploi_du_temps_par_jour = {
        'Lundi': [],
        'Mardi': [],
        'Mercredi': [],
        'Jeudi': [],
        'Vendredi': []
    }

    # Parcourez les données d'horaire
    for cours in donnees_horaire:
        # Vérifiez si le cours est pour l'enseignant spécifique
        if cours[0] != id_enseignant:
            continue

        # Récupérez les informations nécessaires pour le cours
        id_matiere, id_promo, id_salle, jour_semaine, heure_debut, heure_fin = cours
        # Récupérez le nom de l'enseignant
        nom_enseignant = trouver_nom_enseignant(id_enseignant, donnees_enseignants)

        # Récupérez le nom de la matière
        nom_matiere = trouver_nom_matiere(id_matiere, donnees_matiere)

        # Récupérez le nom de la promotion
        nom_promo = trouver_nom_promo(id_promo, donnees_promo)

        # Affichez les détails du cours
        print(
            f"{nom_enseignant} donne cours de {nom_matiere} à la promotion {nom_promo} le {jour_semaine} de {heure_debut} à {heure_fin}")

        # Vérifiez si le cours respecte les contraintes de capacité des salles
        if not verifier_capacite_salle(id_salle, id_promo, donnees_salles, donnees_promo):
            continue

            # Modifier l'appel de la fonction verifier_limite_enseignement dans executer_algorithm_d_ordonnancement_enseignant
        if not verifier_limite_enseignement(id_enseignant, emploi_du_temps_par_jour[jour_semaine], jour_semaine,
                                                donnees_contraintes):
             continue

        # Vérifiez si le cours respecte les contraintes de limite de cours par étudiant
        if not verifier_limite_cours_etudiant(id_promo, emploi_du_temps_par_jour[jour_semaine]):
            continue

        # Vérifiez si le cours respecte les plages horaires spécifiées
        if not verifier_plage_horaire_cours(jour_semaine, heure_debut):
            continue

        # Vérifiez si le cours respecte les pauses déjeuner
        if not verifier_pause_dejeuner(jour_semaine, heure_debut, heure_fin):
            continue

        # Afficher l'emploi du temps pour ce jour avant d'ajouter un nouveau cours
        print(f"Emploi du temps pour {jour_semaine}: {emploi_du_temps_par_jour[jour_semaine]}")

        # Vérifiez si le cours respecte les salles consécutives
        if not verifier_salles_consecutives(emploi_du_temps_par_jour[jour_semaine], id_salle, heure_debut):
            continue

        # Vérifiez si le cours respecte les contraintes de chevauchement avec les cours déjà planifiés
        if not verifier_chevauchement_cours(emploi_du_temps_par_jour[jour_semaine], heure_debut, heure_fin):
            continue

        # Si toutes les contraintes sont respectées, ajoutez le cours à l'emploi du temps
        emploi_du_temps_par_jour[jour_semaine].append((heure_debut, heure_fin))

    return emploi_du_temps_par_jour

def trouver_nom_enseignant(id_enseignant, donnees_enseignants):
    for enseignant in donnees_enseignants:
        if enseignant[0] == id_enseignant:
            return enseignant[1]  # Supposons que le nom de l'enseignant soit stocké à l'index 1 dans les données de l'enseignant
    return "Nom inconnu"  # Retourner une chaîne par défaut si l'enseignant n'est pas trouvé

def trouver_nom_matiere(id_matiere, donnees_matiere):
    for matiere in donnees_matiere:
        if matiere[0] == id_matiere:
            return matiere[1]  # Supposons que le nom de la matière soit stocké à l'index 1 dans les données de la matière
    return "Matière inconnue"  # Retourner une chaîne par défaut si la matière n'est pas trouvée

def trouver_nom_promo(id_promo, donnees_promo):
    for promo in donnees_promo:
        if promo[0] == id_promo:
            return promo[1]  # Supposons que le nom de la promotion soit stocké à l'index 1 dans les données de la promotion
    return "Promotion inconnue"  # Retourner une chaîne par défaut si la promotion n'est pas trouvée


# Fonction pour vérifier le chevauchement avec les cours déjà planifiés pour un enseignant
def verifier_chevauchement_cours(emplois_du_temps_jour, heure_debut_nouveau_cours, heure_fin_nouveau_cours):
    if not emplois_du_temps_jour:
        return True  # Pas de cours précédent, donc pas de chevauchement

    # Convertir les heures de début et de fin du nouveau cours en format float
    heure_debut_nouveau_cours_float = convert_to_float(heure_debut_nouveau_cours)
    heure_fin_nouveau_cours_float = convert_to_float(heure_fin_nouveau_cours)

    # Parcourir les emplois du temps pour le jour spécifié
    for emploi in emplois_du_temps_jour:
        heure_debut_precedente, heure_fin_precedente = emploi

        # Convertir les heures de début et de fin des cours déjà planifiés en format float
        heure_debut_precedente_float = convert_to_float(heure_debut_precedente)
        heure_fin_precedente_float = convert_to_float(heure_fin_precedente)

        # Vérifier s'il y a un chevauchement entre les cours
        if (heure_debut_nouveau_cours_float >= heure_debut_precedente_float and heure_debut_nouveau_cours_float < heure_fin_precedente_float) or \
                (heure_fin_nouveau_cours_float > heure_debut_precedente_float and heure_fin_nouveau_cours_float <= heure_fin_precedente_float) or \
                (heure_debut_nouveau_cours_float <= heure_debut_precedente_float and heure_fin_nouveau_cours_float >= heure_fin_precedente_float):
            return False  # Il y a un chevauchement

    return True  # Pas de chevauchement avec les cours déjà planifiés


# Fonction pour vérifier les salles consécutives
def verifier_salles_consecutives(emplois_du_temps_jour, id_salle_actuelle, heure_debut_actuel):
    if not emplois_du_temps_jour:
        return True  # Pas de cours précédent, donc pas de violation

    # Parcourez les emplois du temps pour le jour spécifié
    for emploi in emplois_du_temps_jour:
        heure_debut_precedente, heure_fin_precedente = emploi

        # Convertir les heures de début et de fin en format float
        heure_debut_precedente_float = convert_to_float(heure_debut_precedente)
        heure_fin_precedente_float = convert_to_float(heure_fin_precedente)
        heure_debut_actuel_float = convert_to_float(heure_debut_actuel)

        # Vérifier si les heures de début et de fin se chevauchent
        if heure_debut_precedente_float <= heure_debut_actuel_float < heure_fin_precedente_float:
            return False  # Violation de la contrainte

    return True  # Pas de violation

# Fonction pour vérifier la capacité de la salle
def verifier_capacite_salle(id_salle, id_promo, donnees_salles, donnees_promo):
    salle = trouver_salle_par_id(id_salle, donnees_salles)
    promo = trouver_promo_par_id(id_promo, donnees_promo)

    if salle is None or promo is None:
        return False  # Salle ou promo non valide

    capacite_salle = salle[2]
    nombre_etudiants = promo[1]

    if nombre_etudiants > capacite_salle:
        return False  # La capacité de la salle est dépassée

    return True  # La salle peut accueillir tous les étudiants inscrits au cours


def verifier_limite_enseignement(id_enseignant, emplois_du_temps_jour, jour_semaine, donnees_contraintes):
    if not emplois_du_temps_jour:
        return True  # Pas de cours planifiés pour cet enseignant ce jour-là

    # Trouver la contrainte pour l'enseignant spécifié
    contrainte = trouver_contrainte_par_id_enseignant(id_enseignant, donnees_contraintes)

    # Vérifier si une contrainte a été trouvée
    if contrainte is not None:
        # Récupérer la limite d'enseignement
        limite_enseignement = contrainte[2]

        # Calculez le nombre total d'heures d'enseignement pour l'enseignant spécifié ce jour-là
        heures_enseignement = sum(
            [convert_to_float(cours[1]) - convert_to_float(cours[0]) for cours in emplois_du_temps_jour])

        # Vérifiez si le nombre total d'heures d'enseignement dépasse la limite
        if heures_enseignement >= limite_enseignement:
            return False  # La limite d'enseignement est dépassée
        else:
            return True  # L'enseignant respecte la limite d'enseignement
    else:
        return False  # Aucune contrainte trouvée pour l'enseignant

# Fonction pour vérifier la limite de cours par étudiant
def verifier_limite_cours_etudiant(id_promo, emplois_du_temps_jour):
    heures_cours = 0

    # Parcourez l'emploi du temps pour compter les heures de cours de l'étudiant
    for emploi in emplois_du_temps_jour:
        heure_debut, heure_fin = emploi
        heures_cours += convert_to_float(heure_fin) - convert_to_float(heure_debut)

    # Vérifiez si le nombre total d'heures de cours dépasse la limite
    if heures_cours > 30:
        return False  # La limite de cours est dépassée
    else:
        return True  # L'étudiant respecte la limite de cours

# Fonction pour vérifier si le cours est dans la plage horaire spécifiée
def verifier_plage_horaire_cours(jour_semaine, heure_debut):
    # Convertir l'heure de début en format float
    heure_debut_float = convert_to_float(heure_debut)

    # Vérifier si le jour de la semaine est du lundi au vendredi et si l'heure de début est dans la plage horaire spécifiée
    if jour_semaine in ['Lundi', 'Mardi', 'Mercredi', 'Jeudi', 'Vendredi'] and 8.5 <= heure_debut_float <= 20.0:
        return True  # Le cours est dans la plage horaire spécifiée
    else:
        return False  # Le cours est en dehors de la plage horaire spécifiée

# Fonction pour vérifier si le cours chevauche une plage horaire de déjeuner
def verifier_pause_dejeuner(jour_semaine, heure_debut, heure_fin):
    # Convertir les heures de début et de fin en format float
    heure_debut_float = convert_to_float(heure_debut)
    heure_fin_float = convert_to_float(heure_fin)

    # Définir les plages horaires du déjeuner
    plage_dejeuner = [(12.0, 13.0), (13.0, 14.0)]  # Par exemple, de 12h00 à 14h00

    # Vérifier si le cours chevauche l'une des plages horaires du déjeuner
    for plage in plage_dejeuner:
        if heure_debut_float <= plage[0] <= heure_fin_float or heure_debut_float <= plage[1] <= heure_fin_float:
            return True  # Le cours chevauche une plage horaire de déjeuner

    return False  # Le cours ne chevauche pas une plage horaire de déjeuner

# Fonctions utilitaires pour trouver des détails à partir d'identifiants
def trouver_enseignant_par_id(id_enseignant, donnees_enseignants):
    for enseignant in donnees_enseignants:
        if enseignant[0] == id_enseignant:
            return enseignant
    return None

def trouver_matiere_par_id(id_matiere, donnees_matiere):
    for matiere in donnees_matiere:
        if matiere[0] == id_matiere:
            return matiere
    return None

def trouver_promo_par_id(id_promo, donnees_promo):
    for promo in donnees_promo:
        if promo[0] == id_promo:
            return promo
    return None

def trouver_salle_par_id(id_salle, donnees_salles):
    for salle in donnees_salles:
        if salle[0] == id_salle:
            return salle
    return None

def trouver_contrainte_par_id_enseignant(id_enseignant, donnees_contraintes):
    for contrainte in donnees_contraintes:
        if contrainte[1] == id_enseignant:
            return contrainte
    return None

# Fonction pour afficher l'emploi du temps généré par jour
def afficher_emploi_du_temps_par_jour(emploi_du_temps_par_jour):
    for jour, emplois_du_temps_jour in emploi_du_temps_par_jour.items():
        print(jour)
        if emplois_du_temps_jour:
            for emploi in emplois_du_temps_jour:
                heure_debut, heure_fin = emploi
                print(f"Heure début : {heure_debut}, Heure fin : {heure_fin}")
        else:
            print("Aucun cours n'a été ajouté à l'emploi du temps pour ce jour.")

# Charger les données de la base de données
donnees_enseignants, donnees_contraintes, donnees_indisponibilites, donnees_promo, donnees_matiere, donnees_salles, donnees_horaire = charger_donnees_de_la_base()

# Exécuter l'algorithme d'ordonnancement pour un seul enseignant dont l'identifiant est 1
emploi_du_temps_par_jour = executer_algorithm_d_ordonnancement_enseignant((donnees_enseignants, donnees_contraintes, donnees_indisponibilites, donnees_promo, donnees_matiere, donnees_salles, donnees_horaire), 1)

# Afficher l'emploi du temps par jour
afficher_emploi_du_temps_par_jour(emploi_du_temps_par_jour)
