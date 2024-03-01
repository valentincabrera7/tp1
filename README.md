"""
# Formato de registro: Nombre de usuario y contraseña
# Utilizar una funcion para almacenar la informacion y otra funcion para mostrar la informacion
# Utilizar un diccionario para almacenar dicha informacion, con el par usuario-contraseña(clave-valor)
# Utilizar otra funcion para el login de usuarios, verificando que el usuario-contraseña coincidan
"""

import os # si importamos a google colab
import json # para importar archivos json
from pathlib import Path # para ver la ruta en la cual estamos parados (para ver donde esta tp1.py), es buena practica ejecutar el path desde el archivo que voy a crear

def login(base_de_datos: list[dict]) -> bool: # la funcion del login es validar/verificar
    # le pasamos la base_de_datos porque tenemos que verificar algo 
    validacion = False # de entrada no esta logeado con esta variable
    print("Ingreso al sistema")
    usuario = input("Usuario: ")
    for diccionario in base_de_datos:
        if usuario == diccionario.get("usuario"):
            print("Usuario encontrado")
            password = input("Contraseña: ")
            if password == diccionario.get("contraseña"):
                print("Contraseña correcta")
                validacion = True # cambia a True solo cuando la contraseña es correcta
                break
            else:
                print("Contraseña incorrecta")
                break
        else:
            print("Usuario incorrecto")
            break
    return validacion

def crear_usuario(base_de_datos: list[dict]): # base_de_datos tiene una lista vacia que va a tener diccionarios
    diccionario = {}
    diccionario["usuario"] = input("Ingrese un usuario nuevo: ") # crear claves
    diccionario["contraseña"] = (input("Ingrese una contraseña: "))
    base_de_datos.append(diccionario) # no le paso el diccionario porque no es una lista y no usa append
    try: # siempre que manejamos archivos usamos try-except
        with open(ruta_archivo, "w", encoding="UTF-8") as archivo:
            json.dump(base_de_datos, archivo, indent=4)
    except Exception as error:
        print(f"Ocurrio el error: {error}")
    return base_de_datos


def cargar_datos():  # le pasamos la variable global creada en main(), que seria la ruta del archivo 
    if ruta_archivo.exists(): # siempre que manejo archivos tengo que usar try-except
        try: # primero, intentamos abrir el archivo 
            with open(ruta_archivo) as archivo:
                base_de_datos = json.load(archivo) # aca creamos la base_de_datos para que no se pierda el archivo y porque estoy descargando el archivo
                return base_de_datos
        except json.decoder.JSONDecodeError: # si hay un error en la carga de json
            print("El archivo existe, pero no es un formato JSON")
            return False # para trabajar desde main()
    else:
        print("No existe el archivo, se creeara uno nuevo")
        return False


def mostrar_informacion(base_de_datos):
    print(base_de_datos)

def preguntar_si_crear_usuario(base_de_datos): # mas usuarios
    while True:
        nuevos_datos = input("¿Desea crear un usuario?: ")
        if nuevos_datos.lower().strip()[0] == "s":
            crear_usuario(base_de_datos)
            continue
        elif nuevos_datos.lower().strip() == "n":
            print("No se ha creado usuario")
            break
        else:
            break
    return base_de_datos

def main(): # si yo cargo los datos en main, en cargar datos, tengo que enviarle ruta_archivo como parametro
    base_de_datos = cargar_datos() # la base_de_datos creada en la funcion cargar_datos
    if not base_de_datos:
        base_de_datos = [] # si no hay base de datos, creamos una nueva vacia
        preguntar_si_crear_usuario(base_de_datos)
        crear_usuario(base_de_datos) # crear_usuario debe recibir como parametro la base de datos 
    else: # si existe la base de datos, hago un login
        hay_login = login(base_de_datos)
        if not hay_login:
            print("No puede acceder al sistema. Intente nuevamente")
        print("Bienvenido al sistema")
        preguntar_si_crear_usuario(base_de_datos) # ya tiene la lista pero necesita el login
        mostrar_informacion(base_de_datos)

if "COLAB_GPU" in os.environ: # esta variable es global, para que funcione va en una funcion
    BASE_DIR = Path(os.getcwd()) # google colab, os.getcwd es una funcion 
else:
    BASE_DIR = Path(__file__).resolve().parent # ejecutar en visual studio code u otro entorno  
    ruta_archivo = BASE_DIR / "usuarios.json"

main()


# dump convierte archivos python a json
# dumps los devuelve en una cadena json 
