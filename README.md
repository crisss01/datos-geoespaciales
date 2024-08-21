# datos-geoespaciales
#!/bin/bash

# Tarea Final - Datos Geoespaciales

# ARCHIVO DE TRATAMIENTO DE DATOS

# Nivel Basico

# 1) ELECCION RANGO DE TIEMPO

# para trabajar los datos los alterare de la sgte forma
# primero eliminare la primera fila de informacion
# para notar el número de filas en total, usamos el comando AWK:

more datos.txt | awk 'END {print NR}'

# la consola arrojó que son 286135 datos
# borraremos la primera fila 

tail -286134 datos.txt > solodatos.txt

# ahora cambio las comas por espacios para trabajar mas comodamente en el archivo de texto "solodatos.txt"
# podemos usar el programa SED que puede transformar texto, línea por línea.

more solodatos.txt | sed 's/,/ /g' > datsincomas.txt

# con este sgte comando restringiremos las coordenadas para tener informacion de la region del biobio solamente

more datsincomas.txt | awk '{ if ($1*1 >= -38 && $1*1 <= -36 && $2*1 >= -74 && $2*1 <= -71) print $0 }' > biobio.txt



# para seleccionar un rango de tiempo deseado. En este caso elegire solo el mes de febrero

# limites de mis datos
inicio="2023-02-01"
fin="2023-02-28"

# ciclo recorre un rango de fechas desde 'inicio' hasta 'fin', 
# agrega cada fecha al archivo febrero.txt y avanza un día a la vez 
# para seguir generando fechas hasta alcanzar la fecha de finalización.

actual="$inicio"
while [[ "$(date -d "$actual" +%s)" -lt "$(date -d "$fin" +%s)" ]]; do
    echo "$actual" >> febrero.txt
    actual=$(date -d "$actual + 1 day" +%Y-%m-%d)
done
#se genera un vector de fechas llamado febrero.txt

#----------------------
# se busca hacer un archivo por dia, en estos se desea mostrar latitud, longitud y brillo por dia por separado.

archivo="febrero.txt"
data=biobio.txt
sum=000

while IFS= read -r febrero; do
    echo "$febrero"
    sum=$((sum+001)) 
    echo "$sum"
    x=$(printf "%03d" $sum) #Formatea el valor del contador sum en una cadena con tres dígitos, completando con ceros a la izquierda si es necesario.
    grep "$febrero" "$data" | awk '{print $2, $1, $3}' >> dia$x.txt # grep busca la fecha en los datos del biobio, awk redirige y guarda .
done < "$archivo"

# procesa cada fecha en el archivo $archivo, incrementa un contador, formatea el contador en una cadena de tres dígitos 
# y utiliza grep y awk para extraer información relacionada con cada fecha de un archivo $data 
# y guardarla en archivos de texto separados con nombres como dia001.txt, dia002.txt, etc.

