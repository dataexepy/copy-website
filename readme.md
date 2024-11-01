#### Usage
`python3 app.py https://redtiger.shop https://redtiger.shop`
this will create a dir at *../app.py*

Requirements:
    Python 3
    BeautifulSoup4
    Requests

Licensed under *Mozilla Public License, version 2.0*

This is old, A# Titre du projet

## Présentation

Ce projet est une application web qui permet de télécharger des ressources (images, CSS, JavaScript) à partir d'une page web et de les sauvegarder dans un répertoire local.

## Installation

1. Cloner le projet : `git clone https://github.com/votre_nom_d_utilisateur/projet.git`
2. Installer les dépendances : `pip install -r requirements.txt`
3. Exécuter le projet : `python app.py`

## Utilisation

1. Lancer l'application en exécutant `python app.py`.
2. Entrer l'URL de la page web à partir de laquelle vous souhaitez télécharger les ressources.
3. L'application téléchargera les ressources et les sauvegardera dans le répertoire du projet.

## Contribution

Les contributions sont les bienvenues ! Si vous souhaitez contribuer au projet, veuillez suivre ces étapes :

1. Cloner le projet : `git clone https://github.com/votre_nom_d_utilisateur/projet.git`
2. Créer une branche : `git checkout -b nom_de_la_branche`
3. Effectuer les modifications nécessaires.
4. Soumettre une pull request.

## Licence

Ce projet est sous licence MIT. Veuillez consulter le fichier [LICENSE](LICENSE) pour plus d'informations.

## Contact

Si vous avez des questions ou des suggestions, n'hésitez pas à me contacter à l'adresse email : votre_email@example.comimport os
import requests
from bs4 import BeautifulSoup

# Définir les variables de configuration
site_name = "https://www.example.com"
project_name = "projet"
project_path = os.path.join(os.getcwd(), project_name)

# Créer le répertoire de projet si il n'existe pas
if not os.path.exists(project_path):
    os.makedirs(project_path)

# Définir les listes pour stocker les liens visités et les erreurs
visited_links = []
error_links = []

def save_assets(html_text):
    # Analyser le HTML pour trouver les liens vers les ressources
    soup = BeautifulSoup(html_text, 'html.parser')
    links = soup.find_all(['link', 'script', 'img'])

    # Télécharger les ressources et les sauvegarder dans le répertoire de projet
    for link in links:
        href = link.get('href')
        if href and href not in visited_links:
            try:
                response = requests.get(href, stream=True)
                response.raise_for_status()
            except requests.RequestException as e:
                error_links.append(href)
                print(f"Erreur lors du téléchargement de {href}: {e}")
                continue

            # Sauvegarder la ressource dans le répertoire de projet
            file_name = os.path.basename(href)
            file_path = os.path.join(project_path, file_name)
            with open(file_path, 'wb') as f:
                for chunk in response.iter_content(1024):
                    f.write(chunk)

            visited_links.append(href)

def crawl(link):
    # Télécharger la page web et l'analyser pour trouver les liens vers les ressources
    try:
        response = requests.get(link)
        response.raise_for_status()
    except requests.RequestException as e:
        error_links.append(link)
        print(f"Erreur lors du téléchargement de {link}: {e}")
        return

    # Sauvegarder la page web dans le répertoire de projet
    file_name = os.path.basename(link)
    file_path = os.path.join(project_path, file_name)
    with open(file_path, 'w') as f:
        f.write(response.text)

    # Télécharger les ressources et les sauvegarder dans le répertoire de projet
    save_assets(response.text)

    # Rechercher les liens vers d'autres pages web et les télécharger
    soup = BeautifulSoup(response.text, 'html.parser')
    links = soup.find_all('a')
    for link in links:
        href = link.get('href')
        if href and href not in visited_links:
            crawl(href)

# Débuter la recherche à partir de la page web spécifiée
crawl(site_name + "/")

# Afficher les liens visités et les erreurs
print("Liens visités:")
for link in visited_links:
    print(link)

print("Erreurs:")
for link in error_links:
    print(link)nd right now most of the website works in some different ways. Might not work.
Try at own risk.
Created for some personal usage.
