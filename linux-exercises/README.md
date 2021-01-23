# Linux exercises

## Ejercicios CLI

## Ejercicio 1:

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
Comprobamos el contenido del fichero file2.txt:
```bash
cat foo/dummy/file2.txt
```
En el caso de esta ultima instrucción no nos va a devolver nada porque está vacío.

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

## Ejercicio 2:
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

## Ejercicio 3:
```bash
#!/bin/bash
if [ $# -eq 0 ]
  then
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

## Ejercicio 4:
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
