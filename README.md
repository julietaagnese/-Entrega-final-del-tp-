# -Entrega-final-del-tp-
!pip install requests beautifulsoup4 pandas openpyxl
import requests
from bs4 import BeautifulSoup
import pandas as pd

# URL de la portada de La Nación
url = "https://www.lanacion.com.ar/"

# Palabras clave para identificar noticias reales
secciones_validas = [
    '/politica/', '/economia/', '/el-mundo/', '/sociedad/',
    '/deportes/', '/espectaculos/', '/opinion/', '/tecnologia/', '/lifestyle/'
]

# Hacer la solicitud HTTP
response = requests.get(url)
if response.status_code == 200:
    soup = BeautifulSoup(response.text, 'html.parser')

    noticias = []

    # Buscar todas las etiquetas <a> con href
    for a in soup.find_all('a', href=True):
        href = a['href']
        texto = a.get_text(strip=True)

        # Convertir a URL completa si es relativa
        if href.startswith('/'):
            href = url.rstrip('/') + href
        # Filtrar solo noticias de secciones válidas
        if any(seccion in href for seccion in secciones_validas) and texto:
            noticias.append({'Título': texto, 'Link': href})

    # Eliminar duplicados por link
    noticias_unicas = {n['Link']: n for n in noticias}.values()

    # Crear DataFrame
    df = pd.DataFrame(noticias_unicas)

    # Exportar a archivo Excel
    df.to_excel('noticias_lanacion.xlsx', index=False)
    print("✅ Archivo 'noticias_lanacion.xlsx' creado con éxito.")
else:
    print(f"❌ Error al acceder a la página: Código {response.status_code}") 
