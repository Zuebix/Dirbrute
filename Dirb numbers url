import hashlib
import requests
from bs4 import BeautifulSoup
import time
import urllib3
import concurrent.futures

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

def get_page_content(url):
    response = requests.get(url, verify=False)
    if response.status_code == 200:
        return response.text
    return None

def generate_hash(content):
    return hashlib.md5(content.encode('utf-8')).hexdigest()

def check_page_changes(i, previous_hash):
    url = f'http://url/{i}'
    print(f"Vérification de l'URL : {url}")
    content = get_page_content(url)
    if content is None:
        return (i, False, previous_hash)

    soup = BeautifulSoup(content, 'html.parser')
    target_div = soup.find('div', class_='text-center col-md-6 col-12')
    if target_div is None:
        return (i, False, previous_hash)

    target_html = str(target_div)
    current_hash = generate_hash(target_html)

    if previous_hash == current_hash:
        return (i, False, current_hash)
    else:
        return (i, True, current_hash)

previous_hashes = [None] * 50000
stop_script = False

while not stop_script:
    with concurrent.futures.ThreadPoolExecutor(max_workers=11) as executor:
        results = list(executor.map(lambda x: check_page_changes(x, previous_hashes[x - 1]), range(1, 50001)))

    for i, has_changed, new_hash in results:
        if has_changed:
            print(f"La page http://url/{i} a été modifiée.")
            previous_hashes[i - 1] = new_hash
            stop_script = True
            break

    time.sleep(3600)  # Attendre une heure (3600 secondes) avant de réexécuter la vérification, si stop_script est False
