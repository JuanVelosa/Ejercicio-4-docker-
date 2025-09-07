
# **Ejercicio cuatro plataformas 2**

> Ejercicio 4: Persistencia y Compartición de Datos
Objetivo: Demostrar el uso avanzado de volúmenes para persistencia y
compartición de datos.
Descripción:
Docker networking y volúmenes 2
> 1. Crea un contenedor writer que escriba la fecha actual cada 10 segundos en
un archivo /data/timestamp.log
> 2. Crea un contenedor reader que lea y muestre el contenido actual del archivo
cada vez que ejecutes un comando
> 3. Si detienes y eliminas ambos contenedores, al recrearlos deben poder
seguir accediendo a los datos anteriores
> 4. Implementa una solución que permita hacer backup del volumen sin
detener los contenedores


> Prueba de éxito:
Los datos persisten entre reinicios de contenedores
Puedes crear un backup del volumen y restaurarlo en otro volumen


## Como crear las imagenes
##

```bash #writer
docker build -f Dockerfile.writer -t writer:latest .
```

```bash #reader
docker build -f Dockerfile.reader -t reader:latest .
```

## Como correr los contenedores

```bash #writer
docker run -d --name writer -v ej4_data:/data writer:latest
```

```bash #reader
docker run -d --name reader -v ej4_data:/data reader:latest
```

## Como ver los logs desde reader

```bash
docker exec -it reader readts
```

## Como crear el backup

```bash
#El volumen de backup se crea en pwd. Se coje el contenido del backup y se mete en un tar.gz(algoritmo de compresion). 
docker run --rm \
  -v ej4_data:/data \
  -v "$PWD":/backup \
  alpine:3.20 sh -c "cd /data && tar czf /backup/ej4_backup.tar.gz ."
```

## Como restaurar el backup

```bash
# Recrear volumen
docker volume create ej4_data

# Restaurar logs. En otras palabra mapeamos el conetenido de la ruta anterior
docker run --rm \
  -v ej4_data:/data \
  -v "$PWD":/backup \
  alpine:3.20 sh -c "cd /data && tar xzf /backup/ej4_backup.tar.gz"

# Restaurar contenedores
docker run -d --name writer -v ej4_data:/data writer:latest

docker run -d --name reader -v ej4_data:/data reader:latest
```

# Simular una perdida de datos
```bash 
docker rm -f writer reader 
docker volume rm ej4_data
```