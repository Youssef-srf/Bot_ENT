import os
import time
import requests
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys

# --- Configuration (récupérée depuis GitHub Secrets) ---
URL_ENT = "https://entv26.univh2c.ma/dossierPedago/notes"
USERNAME = os.getenv("ENT_USER")
PASSWORD = os.getenv("ENT_PASS")
TELEGRAM_TOKEN = os.getenv("TG_TOKEN")
CHAT_ID = os.getenv("TG_CHAT_ID")
PROMOTION_CODE = os.getenv("PROMO_CODE")  # Ex: "CMIAE1/25", "LISI3/24", etc.


def send_telegram(message):
    """Envoie un message via le bot Telegram"""
    url = f"https://api.telegram.org/bot{TELEGRAM_TOKEN}/sendMessage"
    payload = {"chat_id": CHAT_ID, "text": message, "parse_mode": "HTML"}
    try:
        requests.post(url, json=payload, timeout=10)
    except Exception as e:
        print(f"Erreur d'envoi Telegram : {e}")


def get_diff_summary(old_content, new_content):
    """Retourne les lignes ajoutées/modifiées entre l'ancien et le nouveau contenu"""
    old_lines = set(old_content.splitlines())
    new_lines = new_content.splitlines()
    added = [line for line in new_lines if line.strip() and line not in old_lines]
    if added:
        return "\n".join(added[:20])  # Limite à 20 lignes pour ne pas spammer
    return "(contenu modifié)"


def check_notes():
    # --- Configuration du navigateur invisible ---
    chrome_options = Options()
    chrome_options.add_argument("--headless")
    chrome_options.add_argument("--no-sandbox")
    chrome_options.add_argument("--disable-dev-shm-usage")

    service = Service(ChromeDriverManager().install())
    driver = webdriver.Chrome(service=service, options=chrome_options)

    try:
        print("1. Ouverture de la page ENT...")
        driver.get(URL_ENT)
        time.sleep(3)

        print("2. Remplissage des identifiants et connexion...")
        driver.find_element(By.NAME, "username").send_keys(USERNAME)
        champ_mdp = driver.find_element(By.NAME, "password")
        champ_mdp.send_keys(PASSWORD)
        champ_mdp.send_keys(Keys.RETURN)
        time.sleep(5)

        # --- Vérification que la connexion a bien réussi ---
        if "notes" not in driver.current_url and "dossierPedago" not in driver.current_url:
            print("ERREUR : Échec de connexion à l'ENT.")
            send_telegram("⚠️ <b>CheckNotes</b> : Impossible de se connecter à l'ENT. Vérifie tes identifiants.")
            return

        # --- Clic sur le lien de la promotion (si PROMO_CODE est défini) ---
        if PROMOTION_CODE:
            print(f"3. Clic sur la promotion ({PROMOTION_CODE})...")
            try:
                lien_promo = driver.find_element(By.XPATH, f"//a[contains(text(), '{PROMOTION_CODE}')]")
                lien_promo.click()
                time.sleep(3)
            except Exception:
                print(f"Attention : Le lien '{PROMOTION_CODE}' n'a pas été trouvé, on reste sur la vue globale.")
        else:
            print("3. Aucun PROMO_CODE défini, on reste sur la vue globale.")

        print("4. Lecture des notes...")
        element_notes = None
        try:
            element_notes = driver.find_element(
                By.XPATH,
                "//table[contains(@class,'table')]//ancestor::div[contains(@class,'container')][1]"
            )
        except Exception:
            pass

        if element_notes is None:
            containers = driver.find_elements(By.CLASS_NAME, "container")
            if containers:
                element_notes = containers[-1]
            else:
                raise Exception("Aucun élément '.container' trouvé. La structure de l'ENT a peut-être changé.")

        current_content = element_notes.text

        # --- Logique de comparaison ---
        print("5. Comparaison avec l'ancien fichier...")
        if os.path.exists("last_notes.txt"):
            with open("last_notes.txt", "r", encoding="utf-8") as f:
                old_content = f.read()
        else:
            old_content = ""

        if current_content != old_content:
            print("=> CHANGEMENT DÉTECTÉ !")
            diff = get_diff_summary(old_content, current_content)
            message = (
                "🔔 <b>Alerte ENT — Nouvelles notes !</b>\n\n"
                f"<b>Nouveautés détectées :</b>\n<pre>{diff}</pre>\n\n"
                "👉 Connecte-toi sur l'ENT pour voir le détail."
            )
            send_telegram(message)
            with open("last_notes.txt", "w", encoding="utf-8") as f:
                f.write(current_content)
        else:
            print("=> Rien de nouveau. Aucune modification.")

    except Exception as e:
        print(f"Erreur pendant l'exécution : {e}")
        send_telegram(f"⚠️ <b>CheckNotes</b> : Erreur inattendue.\n<code>{e}</code>")
    finally:
        print("Fermeture du navigateur.")
        driver.quit()


if __name__ == "__main__":
    check_notes()
