import sqlite3
from faker import Faker
import random

# Connexion à la base de données SQLite
conn = sqlite3.connect('base_de_donnee.db')
cursor = conn.cursor()

# Initialisation de Faker
fake = Faker()

# Création des tables
cursor.execute('''CREATE TABLE IF NOT EXISTS Enseignant (
                    ID_Enseignant INTEGER PRIMARY KEY,
                    Nom VARCHAR(255),
                    Prenom VARCHAR(255)
                )''')

cursor.execute('''CREATE TABLE IF NOT EXISTS Contraintes_enseignants (
                    ID_contrainte INTEGER PRIMARY KEY,
                    ID_enseignant INTEGER,
                    Nombre_heures_max INTEGER,
                    FOREIGN KEY (ID_enseignant) REFERENCES Enseignant(ID_Enseignant)
                )''')

cursor.execute('''CREATE TABLE IF NOT EXISTS Indisponibilites_enseignants (
                    ID_indisponibilite INTEGER PRIMARY KEY,
                    ID_contrainte INTEGER,
                    Jour VARCHAR(20),
                    FOREIGN KEY (ID_contrainte) REFERENCES Contraintes_enseignants(ID_contrainte)
                )''')

cursor.execute('''CREATE TABLE IF NOT EXISTS Promo (
                    ID_promo INTEGER PRIMARY KEY,
                    Niveau VARCHAR(10)
                )''')

cursor.execute('''CREATE TABLE IF NOT EXISTS Matiere (
                    ID_Matiere INTEGER PRIMARY KEY,
                    ID_Enseignant INTEGER,
                    ID_Promo INTEGER,
                    Nom_Matiere VARCHAR(20),
                    FOREIGN KEY (ID_Enseignant) REFERENCES Enseignant(ID_Enseignant),
                    FOREIGN KEY (ID_Promo) REFERENCES Promo(ID_promo)
                )''')

cursor.execute('''CREATE TABLE IF NOT EXISTS Salle_De_Classe (
                    ID_Salle_De_Classe INTEGER PRIMARY KEY,
                    Capacite_maximale INTEGER,
                    Types_de_salle VARCHAR(50),
                    Ordinateurs_disponibles BOOLEAN,
                    Videoprojecteurs BOOLEAN
                )''')

cursor.execute('''CREATE TABLE IF NOT EXISTS Horaire (
                    ID_Horaire INTEGER PRIMARY KEY,
                    ID_Enseignant INTEGER,
                    ID_Matiere INTEGER,
                    ID_Promo INTEGER,
                    ID_Salle_De_Classe INTEGER,
                    Jour_Semaine VARCHAR(20),
                    Heure_Debut TIME,
                    Heure_Fin TIME,
                    FOREIGN KEY (ID_Enseignant) REFERENCES Enseignant(ID_Enseignant),
                    FOREIGN KEY (ID_Matiere) REFERENCES Matiere(ID_Matiere),
                    FOREIGN KEY (ID_Promo) REFERENCES Promo(ID_promo),
                    FOREIGN KEY (ID_Salle_De_Classe) REFERENCES Salle_De_Classe(ID_Salle_De_Classe)
                )''')

# Peuplement des tables avec des données aléatoires
for _ in range(10):
    nom = fake.last_name()
    prenom = fake.first_name()
    cursor.execute("INSERT INTO Enseignant (Nom, Prenom) VALUES (?, ?)", (nom, prenom))

for _ in range(10):
    id_enseignant = random.randint(1, 10)
    nombre_heures_max = random.randint(10, 20)
    cursor.execute("INSERT INTO Contraintes_enseignants (ID_enseignant, Nombre_heures_max) VALUES (?, ?)", (id_enseignant, nombre_heures_max))

jours_semaine = ["Lundi", "Mardi", "Mercredi", "Jeudi", "Vendredi"]
for _ in range(20):
    id_contrainte = random.randint(1, 10)
    jour = fake.day_of_week()
    cursor.execute("INSERT INTO Indisponibilites_enseignants (ID_contrainte, Jour) VALUES (?, ?)", (id_contrainte, jour))

niveaux = ["L1", "L2", "L3", "M1", "M2"]
for niveau in niveaux:
    cursor.execute("INSERT INTO Promo (Niveau) VALUES (?)", (niveau,))

matieres =["Mathématiques", "Informatique", "Microéconomie", "Macroéconomie", "Organisation & Management", "Harmonisation", "Grands problèmes économiques contemporains", "Français", "Initiation en base de donnée" ]
for _ in range(20):
    id_enseignant = random.randint(1, 10)
    id_promo = random.randint(1, 5)
    Nom_Matiere = random.choice(matieres)
    cursor.execute("INSERT INTO Matiere (ID_Enseignant, ID_Promo, Nom_Matiere) VALUES (?, ?,?)", (id_enseignant, id_promo, Nom_Matiere))

for _ in range(5):
    capacite_max = random.randint(10, 60)
    type_salle = fake.random_element(elements=("Amphi", "Salle de classe"))
    ordinateurs_disponibles = fake.boolean()
    videoprojecteurs = fake.boolean()
    cursor.execute("INSERT INTO Salle_De_Classe (Capacite_maximale, Types_de_salle, Ordinateurs_disponibles, Videoprojecteurs) VALUES (?, ?, ?, ?)", (capacite_max, type_salle, ordinateurs_disponibles, videoprojecteurs))

heures_debut = ["08:00:00", "09:00:00", "10:00:00", "11:00:00", "13:00:00", "14:00:00", "15:00:00"]
heures_fin = ["10:00:00", "11:00:00", "12:00:00", "13:00:00", "15:00:00", "16:00:00", "17:00:00"]


def convert_to_float(time_string):
    # Diviser l'heure en heures, minutes et secondes
    hours, minutes, seconds = map(int, time_string.split(':'))

    # Calculer le nombre total d'heures écoulées depuis minuit
    total_hours = hours + (minutes / 60) + (seconds / 3600)

    return total_hours




for _ in range(6):
    id_enseignant = random.randint(1, 10)
    id_matiere = random.randint(1, 20)
    id_promo = random.randint(1, 5)
    id_salle_de_classe = random.randint(1, 5)
    jour_semaine = random.choice(jours_semaine)
    heure_debut = random.choice(heures_debut)
    heure_fin = random.choice(heures_fin)
    float_heure_debut = convert_to_float(heure_debut)
    float_heure_fin = convert_to_float(heure_fin)

    while float_heure_fin < float_heure_debut or float_heure_fin - float_heure_debut > 4 or float_heure_debut == float_heure_fin:
        heure_debut = random.choice(heures_debut)
        heure_fin = random.choice(heures_fin)
        float_heure_debut = convert_to_float(heure_debut)
        float_heure_fin = convert_to_float(heure_fin)

        # Fonction pour vérifier la cohérence de l'horaire


    cursor.execute("INSERT INTO Horaire (ID_Enseignant, ID_Matiere, ID_Promo, ID_Salle_De_Classe, Jour_Semaine, Heure_Debut, Heure_Fin) VALUES (?, ?, ?, ?, ?, ?, ?)", (id_enseignant, id_matiere, id_promo, id_salle_de_classe, jour_semaine, heure_debut, heure_fin))

# Affichage des données de chaque table
cursor.execute("SELECT * FROM Enseignant")
print("Contenu de la table Enseignant:")
for row in cursor.fetchall():
    print(row)

cursor.execute("SELECT * FROM Contraintes_enseignants")
print("\nContenu de la table Contraintes_enseignants:")
for row in cursor.fetchall():
    print(row)

cursor.execute("SELECT * FROM Indisponibilites_enseignants")
print("\nContenu de la table Indisponibilites_enseignants:")
for row in cursor.fetchall():
    print(row)

cursor.execute("SELECT * FROM Promo")
print("\nContenu de la table Promo:")
for row in cursor.fetchall():
    print(row)

cursor.execute("SELECT * FROM Matiere")
print("\nContenu de la table Matiere:")
for row in cursor.fetchall():
    print(row)

cursor.execute("SELECT * FROM Salle_De_Classe")
print("\nContenu de la table Salle_De_Classe:")
for row in cursor.fetchall():
    print(row)

cursor.execute("SELECT * FROM Horaire")
print("\nContenu de la table Horaire:")
for row in cursor.fetchall():
    print(row)
# Commit des transactions
conn.commit()
# Fermeture de la connexion
conn.close()

print("Peuplement de la base de données et affichage des données terminés avec succès.")
