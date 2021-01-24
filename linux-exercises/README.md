# Linux exercises

## Ejercicios CLI

## Linux Ejercicio 1:

Creamos los directorios:
```bash
mkdir -p foo/dummy foo/empty
```
Comprobamos la estructura creada:
```bash
tree foo
```
La estructura se ha creado correctamente:
```bash
foo
├── dummy
└── empty
```

Creamos el fichero file1.txt con el contenido 'Me encanta la bash!!':

```bash
echo 'Me encanta la bash!!' > foo/dummy/file1.txt
```
Comprobamos que el fichero se ha creado y su contenido:
```bash
cat foo/dummy/file1.txt
```
El resultado obtenido es:
```bash
Me encanta la bash!!
```
Creamos el fichero file2.txt vacío:
```bash
touch foo/dummy/file2.txt
```
Creamos un script de bash para comprobar que el fichero file2.txt está vacio y lo llamamos checkEmpty.sh:
```bash
#!/bin/bash
if [[ -s $1 ]] ; then
echo "$1 has data."
else
echo "$1 is empty."
fi ;
```
Le damos permisos de ejecución:
```bash
chmod +x ./checkEmpty.sh
```
Lanzamos el script pasándole como parámetro el fichero file2.txt:
```bash
./checkEmpty.sh 'foo/dummy/file2.txt'
```
La respuesta obtenida es:
```bash
foo/dummy/file2.txt is empty.
```
Comprobamos que los ficheros estan en la carpeta correspondiente:
```bash
tree foo
```
El resultado que obtenemos es:
```bash
foo
├── dummy
│   ├── file1.txt
│   └── file2.txt
└── empty
```

## Linux Ejercicio 2:
Volcamos el contenido de file1.txt a file2.txt:
```bash
cat foo/dummy/file1.txt >> foo/dummy/file2.txt
```
Comprobamos el contenido de file2.txt:
```bash
cat foo/dummy/file2.txt
```
El resultado obtenido es:
```bash
Me encanta la bash!!
```
Movemos el fichero file2 a la carpeta foo/empty:
```bash
mv foo/dummy/file2.txt foo/empty
```
Comprobamos el contenido de file2.txt en la nueva ubicación:
```bash
cat foo/empty/file2.txt
```
El resultado obtenido es:
```bash
Me encanta la bash!!
```
Comprobamos el contenido de file1.txt sigue siendo el mismo:
```bash
cat foo/dummy/file1.txt
```
El resultado obtenido es:
```bash
Me encanta la bash!!
```
Comprobamos la estructura despues de mover el fichero:
```bash
tree foo
```
```bash
foo
├── dummy
│   └── file1.txt
└── empty
    └── file2.txt
```

## Linux Ejercicio 3:
Creamos un script de bash llamado createStructure.sh con el siguiente contenido:
```bash
#!/bin/bash
if [ $# -eq 0 ]; then
    content='Que me gusta la bash!!!!'
else
    content=$1
fi
mkdir -p foo/dummy foo/empty
echo $content > foo/dummy/file1.txt
touch foo/dummy/file2.txt
cat foo/dummy/file1.txt >> foo/dummy/file2.txt
mv foo/dummy/file2.txt foo/empty   
```
Le damos permisos de ejecución:
```bash
chmod +x ./createStructure.sh
```
y lo ejecutamos:
```bash
./createStructure.sh
```

Comprobamos la estructura y ficheros creados:
```bash
tree foo
```
```bash
foo
├── dummy
│   └── file1.txt
└── empty
    └── file2.txt
```
La estructura es correcta.

Comprobamos el contenido de los ficheros:
```bash
cat foo/dummy/file1.txt
```
Y:
```bash
cat foo/empty/file2.txt
```
En ambos caso obtenemos el texto:
```bash
Que me gusta la bash!!!!
```

## Linux Ejercicio 4:
Creamos un script de bash llamado downloadWeb.sh con el siguiente contenido:
```bash
#!/bin/bash
if [ $# -eq 0 ]
  then
    echo "Error: please provide argument."
    exit 1
elif [ $# -gt 1 ]
  then
    echo "Error: too many arguments."
    exit 1

else
    searchWord=$1
fi
curl -s https://www.galdakao.eus > downloadedWeb.txt

grep -n $searchWord downloadedWeb.txt  | cut -d : -f 1
```

Le damos permisos de ejecución:
```bash
chmod +x ./downloadWeb.sh
```
Lo ejecutamos sin pasarle ningún parametro:
```bash
./downloadWeb.sh
```
Muestra el siguiente mensaje de error:
```bash
Error: please provide argument.
```
Lo ejecutamos pasándole más de un parámetro:
```bash
./downloadWeb.sh Bienvenidos a la web
```
Muestra el siguiente mensaje:
```bash
Error: too many arguments.
```
Lo ejecutamos pasándole un único parámetro:
```bash
./downloadWeb.sh Galdakao
```
Muestra las líneas en las que se encuentra la palabra buscada:
```bash
12
23
63
77
119
181
205
588
621
650
658
690
760
784
817
825
863
928
990
998
1031
1060
1068
1100
1126
1134
1163
1171
1195
1228
1273
1298
1306
1331
1494
1557
1628
1637
1717
1741
Binary file downloadedWeb.txt matches
```