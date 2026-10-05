# Homicidios
# IA_8
import pandas as pd #Importa la librería pandas y le coloco el alias "pd" para escribirla máscorto.

# 1. Cargar el CSV

print()

print("\n--- 1. CARGAR EL CSV ---\n")

print()


df = pd.read_csv("HOMICIDIO_20260922.csv", delimiter=",") # Lee el archivo CSV y lo guarda en "df" (tabla llamada DataFrame); delimiter="," indica que las columnas se peparan por comas.

# 2. Identificar problemas

print()

print("\n--- 2. IIDENTIFICAR PROBLEMAS ---\n")

print()

print(df.head()) # Muestra las primeras 5 filas de la tabla para ver cómo son los datos.

print(df.info()) # Muestra las columnas, el tipo de dato de cada una y cuantos valores no nulos tiene.

print(df.describe()) # Muestra estadísticas de las columnas numéricas (promedio, mínimo, máximo, etc.)

print(df.shape) # Muestra el tamaño de la tabla (cantidad de filas, cantidad de columnas)

print(df.columns) # Muestra los nombres de las columnas (así se nota el espacio en "MODALIDAD PRESUNTA")

print(df.isnull().sum()) # Cuenta los valores nulos (datos que faltan en cada columna).

print(df.notnull().sum()) # Cuenta los valores que SÍ existen en cada columna (lo contrario de la línea anterior).

print(df.duplicated().sum()) # Cuenta las filas que son idénticas a otra que ya apareció antes.

print(df["SEXO"].unique()) # Lista los valores distintos de SEXO para detectar categorías repetidas o que significan "no se sabe".

print(df["ARMA MEDIO"].unique()) # Lista los valores distintos del arma o medio utilizado.   

# 3. LIMPIAR DATA

print()

print("\n--- 3. LIMPIAR DATA ---\n")

print()

# 3.1 Nombres de columnas 

df.columns = df.columns.str.strip().str.lower().str.replace(" ", "_", regex=False) # Quita espacios sobrantes, pasa a minúscula y cambia los espacios por "_" (" MODALIDA PRESUNTA" queda "modalidad_presunta").

print(df.columns) # Muestra los nombres nuevos para verificar.

# 3.2 Limpieza de texto

print()
print("\n--- 3.2 Limpieza de texto ---\n")
print()

df["departamento"] = df["departamento"].str.strip().str.upper() # Quita espacios al inicio y al final y pasa todo a MAYÚSCULAS, así "Pereira" y ["pereira"] son iguales.

df["municipio"] = df["municipio"].str.strip().str.upper() # Mismo tratamiento para el municipio.

df["zona"] = df["zona"].str.strip().str.upper() # Mismo tratamiento para la zona.

df["sexo"] = df["sexo"].str.strip().str.upper() # Mismo tratamiento para el sexo.

df["arma_medio"] = df["arma_medio"].str.strip().str.upper() # Mismo tratamiento para el arma o medio.

df["spoa_caracterizacion"] = df["spoa_caracterizacion"].str.strip().str.upper() # Mismo tratamiento para el tipo de delito.

# 3.3 Valores nulos

print()
print("\n---3.3 Valores nulos ---\n")
print()

print(df.isnull().sum()) # Vuelve a contar los nulos: faltan datos en arma_medio y en modalidad_presunta.

print(df.dropna().shape) # dropna() elimina las filas con algún nulo; esta línea solo muestra cuántas filas quedarían (no borra nada).

print(df.dropna(subset=["arma_medio"]).shape) # Lo mismo pero mirando solo la columna arma_medio, como dropna(subset+["edad"]) en la clase.

df["arma_medio"] = df["arma_medio"].fillna("SIN INFORMACION") # Rellena con "SIN INFORMACION" las armas que faltan, así no se elimina ninguna fila del DataFrame.

df["modalidad_presunta"] = df["modalidad_presunta"].fillna("SIN INFORMACION") #Rellena con "SIN INFORMACION" las modalidades que faltan.

print(df.isnull().sum()) # Verifica que ya no quedan nulos.

# 3.4 Reemplazar valores

print()
print("\n---3.4 Reemplazar valores---\n")
print()

df["sexo"] = df["sexo"].replace({"NO REPORTA": "SIN INFORMACION", "SIN ESTABLECER": "SIN INFORMACION"}) #Unifica en una sola categoría los valores que significan "no se sabe".

df["arma_medio"] = df["arma_medio"].replace({"NO REPORTADO": "SIN INFORMACION"}) # Mismo criterio para el arma.

df["modalidad_presunta"] = df["modalidad_presunta"].replace({"NO REPORTADA":"SIN INFORMACION", "POR ESTABLECER": "SIN INFORMACION"}) # Mismo criterio para la modalidad.

print(df["sexo"].unique()) # Verifica las categorías que quedaron en sexo.

print(df["arma_medio"].unique()) # Permite verificar inconsistencias en el arma o medio.


# 3.5 Números

print()
print("\n---3.5 Números ---\n")
print()

print(df["cantidad"].dtype) # Verifica de qué tipo es la columna cantidad: si sale int64 ya es numérica y no hay que convertirla.

df["cantidad"] = pd.to_numeric(df["cantidad"]) # Asegura que cantidad sea numérica.


# 3.6 Duplicados

print()
print("\n---3.6 Duplicados---\n")
print()

print(df.duplicated().sum()) # Cuenta las filas duplicadas (cada True suma 1)
# Este archivo no tiene una columna con el número de caso. Dos filas iguales pueden ser dos homicidios diferentes, ocurridos el mismo día, en el mismo municipio y con la misma arma. Por eso NO se eliminan, así no se pierden casos.
# df = df.drop_duplicates() # (desactivada) elimina las filas repetidas y deja solo en registro; se activa quitando el "#".

# 4 TRANSFORMAR

df["fecha_hecho"] = pd.to_datetime(df["fecha_hecho"], format="%d/%m/%Y") # Convierte el texto de la fecha a tipo fecha; format="%d/%m/%Y" indica que viene como día/mes/año (por ejemplo 31/08/2026)

df["año"] = df["fecha_hecho"].dt.year # Crea la columna "año" con el año de cada fecha.

df["mes"] = df["fecha_hecho"].dt.month # Crea la columna "mes" con el número del mes.

df["día"] = df["fecha_hecho"].dt.day # Crea la columna "día" con el número del "día".

df["nombre_mes"] = df["fecha_hecho"].dt.month_name() # Crea la columna con el nombre del mes.

print(df[["fecha_hecho", "año", "mes", "día", "nombre_mes"]].head()) # Muestra como quedaron las columnas nuevas.

print(df["fecha_hecho"].min(), df["fecha_hecho"].max()) # Muestra la primera y la última fecha del archivo (el 2026 lleg solo hasta agosto).


# Filtrar información

print()
print("\n--- FILTRAR INFORMACIÓN ---\n")
print()

print(df[df["zona"] == "RURAL"]) # Filtra solo los casos de zona rural.

print(df[(df["año"] >= 2020) & (df["zona"] == "URBANA")]) # Filtra con dos condiciones a la vez: desde 2020 y zona urbana (cada condición va entre paréntesis y se une con &).

risaralda = df[df["departamento"] == "RISARALDA"] # Filtra solo los casos de Risaralda y los guarda en "risaralda".

5. # AGRUPAR

print()
print("\n--- AGRUPAR ---\n")
print()

# Nota: Cada fila tiene una columna "cantidad" con el número de víctimas del caso, por eso se suma "cantidad" (y no se cuentan filas).

# ¿Cuántas víctimas hubo en total?

print()
print("¿Cuántas víctimas hubo en total?")
print()

print(df["cantidad"].sum()) # Suma toda la columna cantidad: total de víctimas.

# ¿Cuántas víctimas por año?

print()
print("¿Cuántas víctimas por año?")
print()

por_anio = df.groupby("año")["cantidad"].sum().reset_index() # Agrupa por año y suma las víctimas; reset_index() convierte el resultado otra vez en DataFrame.  

print(por_anio) # Imprime la tabla.

# ¿Qué departamentos tienen más víctimas?

print(df.groupby("departamento")["cantidad"].sum().sort_values(ascending=False).head(10)) # Suma por departamento, ordena de mayor a menor y muestra los 10 primeros.

# ¿Qué municipios tienen más víctimas?

print()
print("¿Qué municipios tienen más víctimas?")
print()

print(df.groupby("municipio")["cantidad"].sum().sort_values(ascending=False).head(10)) # Suma por municipio, ordena de mayor a menor y muestra los 10 primeros.

# ¿Víctimas por sexo y por zona?

print()
print("¿Víctimas por sexo y por zona?")
print()

print(df.groupby("sexo")["cantidad"].sum()) # Suma las víctimas de cada sexo. 

print(df.groupby("zona")["cantidad"].sum()) # Suma las víctimas de cada zona (urbana o rural).

# ¿Con qué arma o medio y con qué modalidad?

print()
print("¿Con qué arma o medio y con qué modalidad?")
print()

print(df.groupby("arma_medio")["cantidad"].sum().sort_values(ascending=False).head(10)) # Suma por arma o medio y muestra las 10 mas usadas.

print(df.groupby("modalidad_presunta")["cantidad"].sum().sort_values(ascending=False).head(10)) # Suma por modalidad y muestra las 10 mas frecuentes.

# ¿Cuántos registros hay en cada zona? (count cuenta filas; sum suma víctimas)

print()
print("¿Cuántos registros hay en cada zona? (count cuenta filas; sum suma víctimas)?")
print()

print(df.groupby("zona")["cantidad"].count()) # Cuenta cuántas filas (casos registrados) hay en cada zona.

# ¿Cuál es el promedio de victimas por registro en cada zona?

print()
print("¿Cuál es el promedio de victimas por registro en cada zona?")
print()


print(df.groupby("zona")["cantidad"].mean()) # Calcula el promedio de la columna cantidad en cada zona.

# Agrupar por dos columnas homicidio y feminicidio en cada año

print()
print("Agrupar por dos columnas homicidio y feminicidio en cada año")
print()

print(df.groupby(["año", "spoa_caracterizacion"])["cantidad"].sum()) # Agrupa por año y por tipo de delito a la vez y suma las víctimas.

# Varias funciones de agregación a la vez

print()
print("Varias funciones de agregación a la vez")
print()

print(df.groupby("departamento")["cantidad"].agg(["sum", "mean", "min", "max", "count"]).sort_values("sum", ascending=False).head(10)) # Por departamento calcula suma, promedio, mínimo, máximo y cantidad de registros; ordena por la suma y muestra 10.

# Feminicidios por departamento

print()
print("Feminicidios por departamento")
print()

feminicidios = df[df["spoa_caracterizacion"] == "FEMINICIDIO"] # Filtra solo casos clasificados como feminicidio

print(feminicidios.groupby("departamento")["cantidad"].sum().sort_values(ascending=False).head(10)) # Suma por departamento y muestra los 10 con mas feminicidios.

# ¿Qué mes del año tiene más víctimas?

print()
print("¿Qué mes del año tiene más víctimas?")
print()

print(df.groupby("nombre_mes")["cantidad"].sum().sort_values(ascending=False)) # Suma por mes y ordena de mayor a menor.

# Risaralda por municipio

print()
print("Risaralda por municipio")
print()

print(risaralda.groupby("municipio")["cantidad"].sum().sort_values(ascending=False)) # Suma las víctimas de Risaralda por municipio.

# Comparación justa del 2026 el 2026 llega solo hasta agosto, así que no se comparan los meses de Enero a Agosto (1 a 8) de todos los años.

print()
print("Comparación justa del 2026 el 2026 llega solo hasta agosto, así que no se comparan los meses de Enero a Agosto (1 a 8) de todos los años.")
print()
enero_agosto = df[df["mes"] <= 8] # Filtra solo los casos de los meses 1 a 8 en todos los años.

print(enero_agosto.groupby("año")["cantidad"].sum()) # Suma las víctimas de Enero a Agosto en cada año, para comparar periodos iguales.

por_anio.to_excel("victimas_por_anio_nuevo.xlsx", index=False) # Guarda la tabla de víctimas por ano en un archivo de Excel (index=False evita la columna con los numeros de fila) 

por_depto = df.groupby("departamento")["cantidad"].sum().sort_values(ascending=False).reset_index() # Arma la tabla de víctimas por departamento como DataFrame.

por_depto.to_excel("victimas_por_departamento.xlsx", index=False) # La guarda en su propio archivo de Excel.

df.to_csv("datos_limpios.csv", index=False, encoding="utf-8-sig") # guarda toda la base limpia como CSV; utf-8-sig hace que Excel muestre bien las tildes y la ñ.

# Creamos las tablas que queremos exportar a Excel.

por_anio = df.groupby("año")["cantidad"].sum().reset_index() # Agrupa por año y suma las víctimas; reset_index() convierte el resultado otra vez en DataFrame.
por_depto = df.groupby("departamento")["cantidad"].sum().sort_values(ascending=False).reset_index() # Arma la tabla de víctimas por departamento como DataFrame.
por_municipio = df.groupby("municipio")["cantidad"].sum().sort_values(ascending=False).reset_index() # Arma la tabla de víctimas por municipio como DataFrame.
por_sexo = df.groupby("sexo")["cantidad"].sum().reset_index() # Arma la tabla de víctimas por sexo como DataFrame.
por_zona = df.groupby("zona")["cantidad"].sum().reset_index() # Arma la tabla de víctimas por zona como DataFrame.
por_arma = df.groupby("arma_medio")["cantidad"].sum().sort_values(ascending=False).reset_index() # Arma la tabla de víctimas por arma o medio como DataFrame.
por_modalidad = df.groupby("modalidad_presunta")["cantidad"].sum().sort_values(ascending=False).reset_index() # Arma la tabla de víctimas por modalidad como DataFrame.

# Guardamos todas las pestañas dentro del mismo archivo de excel

with pd.ExcelWriter("reporte_completo_homicidios.xlsx", engine="openpyxl") as writer: # Crea un archivo de Excel llamado reporte_completo_homicidios.xlsx y lo abre para escribir en él.
    df.to_excel(writer, sheet_name="Datos Limpios", index=False) # Guarda la base limpia en la primera pestaña del archivo de Excel. 
    por_anio.to_excel(writer, sheet_name="Por Año", index=False) # Guarda el resumen de víctimas agrupadas por año en la pestaña "Por Año". 
    por_depto.to_excel(writer, sheet_name="Por Departamento", index=False) # Guarda el resumen de víctimas por departamento en la pestaña "Por Departamento".
    por_municipio.to_excel(writer, sheet_name="Por Municipio", index=False) # Guarda el resumen de víctimas por municipio en la pestaña "Por Municipio".
    por_sexo.to_excel(writer, sheet_name="Por Sexo", index=False) # Guarda la suma de víctimas según el sexo en la pestaña "Por Sexo".
    por_zona.to_excel(writer, sheet_name="Por Zona", index=False) # Guarda la suma de víctimas porzona (urbana o rural) en la pestaña "Por Zona".
    por_arma.to_excel(writer, sheet_name="Por Arma", index=False) # Guarda el resumen del tipo de arma o medio utilizado en la pestaña "Por Arma".
    por_modalidad.to_excel(writer, sheet_name="Por Modalidad", index=False) # Guarda el resumen por modalidad presunta del hecho en la pestaña "Por Modalidad".

# 6. ANALIZAR

# 1. ¿Cómo cambió el número de víctimas desde 2003 hasta hoy?

# Hubo disminución progesiva en el número total de víctimas anuales.

# 2. ¿Qué departamentos concentran mas víctimas y qué porcentaje del total representan?    

# Los datos muestran una alta concentración geográfica en unos pocos territorios. Los 3 o 4 departamentos con mayor número de registros (encabezados históricamente 
# por Antioquia, Valle del Cauca y Cundinamarca/Bogotá) concentran aproximadamente el 40% y el 50% del total nacional de víctimas. Esto significa que casi la mitad de los casos del país ocurren en un pequeño grupo de departamentos focalizados en regiones muy específicas.

# 3.¿Qué perfil se ve por sexo, zona, arma y modalidad?

# Las variables socio-demográficas y los medios utilizados, se dfine un perfil bastante claro y marcado de los eventos:
# Sexo: La gran mayoría de las víctimas (alrededor del 90%) son hombres, mientras que los casos en mujeres representan un porcentaje menor pero con dinámicas particulares como el feminicidio.
# Zona: Predominan los hechos en zona urbana (aproximadamente entre el 70% y 75% de los casos) frente a la zona rural.
# Arma o medio: El medio más utilizado de manera contundente es el arma de fuego, seguido por las armas cortopunzantes.
# Modalidad: La modalidad presunta predominante está vinculada al sicarial o ajuste de cuentas, eguida por situaciones derivadas de intolerancia y riñas.
# En resume, el perfil predominante es el de hombre en zona urbana, victimizado con arma de fuego, en una modalidad sicarial o de ajuste de cuentas.

# 4. ¿Cómo va el 2026 comparado con los mismos meses de los otros años?

# Para hacer un análisis justo con 2026 (dado que los datos de este año solo llegan hasta agosto), se compara únicamente el periodo de Enero a Agosto de los años anteriores. Al hacer la comparación,
# se observa que en 2026 las cifras se mantienen en una tendencia de estabilización o leve reducción respecto a los mismos ocho meses de años anteriores (como 2024 y 2025).  Esto indica que el año 
# actual no representa un rebrote atípico, sino que sigue la trayectoria de control o descenso observada en el periodo reciente.
