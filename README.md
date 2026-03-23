# TP-Grupal-
TP 1 GRUPAL INTRO 
#!/bin/bash

## Función que crea el entorno de trabajo
crear_entorno() {
## Guardamos la ruta en una variable llamada CARPETA
      CARPETA="$HOME/EPNro1"

## Verificamos que la carpeta exista
      if [ -d "$CARPETA" ];
      then
           echo "El entorno ya existe en $CARPETA"
## Si no existe, la creamos
      else
           mkdir -p "$CARPETA/entrada" "$CARPETA/salida" "$CARPETA/procesado"
## Confirmamos
           echo "Entorno correctamente creado"
      fi
}

if [ "$1" == "-d" ]; then ##Verifica si el primer argumento al ejecutar el script es -d
   echo "Borrando entorno y matando procesos..." 
   CARPETA="$HOME/EPNro1"  ##Define la ruta de la carpeta creada por el script
   rm -rf "$CARPETA"  ## La borra completamente junto con todo su contenido
   pkill -f  consolidar.sh  ##Mata cualquier proceso en ejecución que tenga consolidar.sh en su linea de ejecución
   echo "Entorno eliminado"
   exit 0  ##Termina el script de manera exitosa
fi

## Realizamos un bucle infinito para mantener el menú activo
while true;
do
      echo "1) Crear entorno"
      echo "2) Procesar archivos"
      echo "3) Mostrar listado de alumnos ordenado por padrón"
      echo "4) Mostrar top 10 notas más altas"
      echo "5) Buscar alumno por padrón"
      echo "6) Salir"

## Leemos la opción del usuario
      read -p "Elegí una opción:" opcion
## Validamos que no esté vacío
      if [ -z "$opcion" ]; then
         echo "No ingresaste ninguna opción"
   continue
      fi

## Evaluamos la opción que eligió el usuario
      case $opcion in
          1) crear_entorno ;; ## Ejecuta la función

          2)export FILENAME="consolidado"
            bash ./consolidar.sh  &
            echo "Proceso ejecutándose en background..." ;;

          3)export FILENAME="consolidado" ## Exporto el nombre del archivo final
             CARPETA="$HOME/EPNro1"  ## Define la carpeta base del enotrno
             CARPETA_SALIDA="$CARPETA/salida"   ## Define carpeta salida donde se encuentra consolidado.txt
             if [ -f "$CARPETA_SALIDA/$FILENAME.txt" ]; then  ##Vefifica si el archivo $FILENAME existe en la carpeta salida, -f devuelve verdadero solo si es un archivo regular
                echo "Listado de alumnos ordenado por padrón:"
                sort -k1,1n "$CARPETA_SALIDA/$FILENAME.txt"  ##Ordena el archivo por la primera columna Nro_Padron de forma numérica ascendente -k1 selecciona columna 1, -n orden numerico
             else
                echo "Archivo $FILENAME no encontrado en salida"  ##Si no existe el archivo muestra error
             fi;;

          4) FILENAME="consolidado"  ##Define el nombre del archivo final
             CARPETA="$HOME/EPNro1"      ##Define la carpeta base
             CARPETA_SALIDA="$CARPETA/salida"  ##Define carpeta salida donde se encuentra consolidado.txt
             if [ -f "$CARPETA_SALIDA/$FILENAME.txt" ]; then  ##Verifica si el archivo consolidado.txt existe en la carpeta salida, -f devuelve verdadero solo si es un archivo regular
                echo "Top 10 notas más altas:"  ##Muestra mensaje antes de mostrar los resultados
                sort -k5,5nr "$CARPETA_SALIDA/$FILENAME.txt" | head -n 10  ##Ordena por quinta columna de forma numérica descendente, head -n 10 muestra las primeras 10 lineas, las 10 notas más altas
             else
                echo "Archivo $FILENAME no encontrado en salida"
             fi;;

          5) ##Define el nombre del archivo final y la carpeta donde se encuentra consolidado.txt
             FILENAME="consolidado"
             CARPETA="$HOME/EPNro1"
             CARPETA_SALIDA="$CARPETA/salida"

             ##Verifica que consolidado.txt exista, sino muestra error
             if [ -f "$CARPETA_SALIDA/$FILENAME.txt" ]; then
                read -p "Ingrese el número de padrón que busca:" PADRON  ##Pide al usuario el número que quiere buscar, el valor inresado se guarda en la variable $PADRON
                grep "^$PADRON " "$CARPETA_SALIDA/$FILENAME.txt" || echo "Padrón no encontrado"  ##Grep busca lineas que comiencen con el número de padrón ingresado, ^ indica inicio de linea para que solo encuentre coincidencias exactas >
             else
                echo "Archivo $FILENAME.txt no encontrado en salida"
             fi ;;

          6)echo "Saliendo del menú..." 
            exit 0 ;;          ## Sale del programa sin errores
          *) echo "Opción inválida" ;;      ## Tira error
      esac
done
