import requests
import urllib.parse

# Configuración inicial
API_KEY = "7ff71ad5-92dd-413f-978e-0e3621ebb69f"
GEOCODE_URL = "https://graphhopper.com/api/1/geocode?"
ROUTE_URL = "https://graphhopper.com/api/1/route?"

def obtener_coordenadas(ubicacion, key):
    url = GEOCODE_URL + urllib.parse.urlencode({"q": ubicacion, "limit": "1", "key": key, "locale": "es"})
    respuesta = requests.get(url)
    
    if respuesta.status_code == 200:
        datos = respuesta.json()
        if "hits" in datos and len(datos["hits"]) > 0:
            lat = datos["hits"][0]["point"]["lat"]
            lng = datos["hits"][0]["point"]["lng"]
            nombre_lugar = datos["hits"][0].get("name", ubicacion)
            return respuesta.status_code, lat, lng, nombre_lugar
            
    return respuesta.status_code, None, None, None

def obtener_ruta(lat1, lon1, lat2, lon2, key):
    params = {
        "point": [f"{lat1},{lon1}", f"{lat2},{lon2}"],
        "locale": "es",
        "key": key,
        "vehicle": "car"
    }
    url = ROUTE_URL + urllib.parse.urlencode(params, doseq=True)
    respuesta = requests.get(url)
    
    if respuesta.status_code == 200:
        return respuesta.json()
    return None

def principal():
    print("==================================================")
    print("      CALCULADOR DE RUTAS Y NARRATIVA (ESPAÑOL)   ")
    print("==================================================")
    print("Escriba 's' o 'salir' en cualquier momento para terminar.\n")
    
    while True:
        orig_input = input("Ingrese el lugar de origen: ").strip()
        if orig_input.lower() in ["s", "salir"]:
            print("¡Hasta luego!")
            break
            
        dest_input = input("Ingrese el lugar de destino: ").strip()
        if dest_input.lower() in ["s", "salir"]:
            print("¡Hasta luego!")
            break
            
        print("\nBuscando ubicaciones...")
        status_orig, lat1, lon1, nombre1 = obtener_coordenadas(orig_input, API_KEY)
        status_dest, lat2, lon2, nombre2 = obtener_coordenadas(dest_input, API_KEY)
        
        if status_orig != 200 or lat1 is None:
            print(f"❌ No se pudo encontrar el origen: '{orig_input}'. Intente nuevamente.\n")
            continue
        if status_dest != 200 or lat2 is None:
            print(f"❌ No se pudo encontrar el destino: '{dest_input}'. Intente nuevamente.\n")
            continue
            
        print(f"✔ Origen encontrado: {nombre1}")
        print(f"✔ Destino encontrado: {nombre2}")
        print("Calculando la ruta óptima...\n")
        
        datos_ruta = obtener_ruta(lat1, lon1, lat2, lon2, API_KEY)
        
        if datos_ruta and "paths" in datos_ruta and len(datos_ruta["paths"]) > 0:
            # CORRECCIÓN: Se extrae el primer elemento de la lista 'paths' usando [0]
            path = datos_ruta["paths"][0]
            
            distancia_km = path["distance"] / 1000.0
            tiempo_min = path["time"] / 60000.0
            
            print("=" * 50)
            print("                RESUMEN DEL VIAJE")
            print("=" * 50)
            print(f"• Distancia total: {distancia_km:.2f} km")
            print(f"• Tiempo estimado: {tiempo_min:.2f} minutos")
            print("=" * 50)
            
            print("\n--- Narrativa del Viaje (Instrucciones Paso a Paso) ---")
            if "instructions" in path:
                for idx, instruccion in enumerate(path["instructions"], start=1):
                    texto_instruccion = instruccion.get("text", "")
                    dist_paso = instruccion.get("distance", 0) / 1000.0
                    print(f"{idx}. {texto_instruccion} ({dist_paso:.2f} km)")
            print("=" * 50 + "\n")
        else:
            print("❌ No se pudo calcular una ruta viable entre estos dos puntos.\n")

if __name__ == "__main__":
    principal()
