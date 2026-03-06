# Implementación de VPN Site-to-Site Route-Based (VTI) con IPsec IKEv2

## Video demostrativo

[![Ver video en YouTube](https://img.youtube.com/vi/bcc4OhnbHvE/maxresdefault.jpg)](https://youtu.be/bcc4OhnbHvE)

---

## I-) Descripción general
En este laboratorio se implementa una VPN Site-to-Site basada en enrutamiento (route-based) utilizando IPsec con IKEv2 mediante una Virtual Tunnel Interface (VTI). A diferencia de una VPN policy-based, aquí el túnel se comporta como una interfaz lógica (Tunnel10) y el tráfico se enruta hacia la red remota utilizando rutas estáticas (o podría usarse enrutamiento dinámico).
El objetivo es asegurar la comunicación entre las redes 10.9.18.0/24 (Sitio A) y 10.9.19.0/24 (Sitio B) a través de una red intermedia compuesta por múltiples routers (simulación de Internet).

---

## II-)Plan de direccionamiento IP

La topología se diseñó intencionalmente distinta a la práctica IKEv1, agregando un segmento intermedio con un router CORE y un segundo ISP:

<img width="688" height="585" alt="Screenshot 2026-03-05 214711" src="https://github.com/user-attachments/assets/00df3530-2287-46ee-8ae5-94073fa9e59a" />

Este esquema permite separar claramente las redes LAN de las conexiones WAN utilizadas para el establecimiento del túnel VPN.

---

## III-) Direccionamiento IP

<img width="433" height="313" alt="Screenshot 2026-03-05 214807" src="https://github.com/user-attachments/assets/440842bf-2462-4ba8-840e-6feaadfc0a4f" />

---

## IV-) Parámetros criptográficos utilizados (IKEv2/IPsec)

<img width="450" height="309" alt="Screenshot 2026-03-05 214906" src="https://github.com/user-attachments/assets/a0b94eb6-a0a5-4169-bd53-6196a19874bc" />

<img width="428" height="316" alt="Screenshot 2026-03-05 214929" src="https://github.com/user-attachments/assets/bca24d71-dccd-497d-a450-edf591156154" />

---

## V-) Configuración de conectividad (WAN) y ruteo previo 
Antes de levantar la VPN, se configuraron las interfaces WAN y el enrutamiento en los routers intermedios (ISP/CORE/ISP2) para garantizar conectividad entre los peers (R1 y R2). Este paso es crítico, ya que IKEv2 requiere alcance IP entre los endpoints WAN para negociar las asociaciones de seguridad.

<img width="337" height="191" alt="Screenshot 2026-03-05 215046" src="https://github.com/user-attachments/assets/dee8230c-8962-41a7-84fd-08e8f22743f6" />

---

## VI-) Implementación de la VPN IPsec basada en políticas

En esta VPN, el túnel lógico Tunnel10 representa el enlace seguro. La protección IPsec se aplica con un IPsec profile enlazado al túnel mediante tunnel protection ipsec profile. El tráfico entre LANs se enruta a través del túnel usando rutas estáticas hacia la red remota.

<img width="566" height="560" alt="Screenshot 2026-03-05 215153" src="https://github.com/user-attachments/assets/8623dc11-120c-421d-81f3-87b17c0466ad" />

<img width="555" height="560" alt="Screenshot 2026-03-05 215216" src="https://github.com/user-attachments/assets/a9d1cbba-0429-476e-9684-f32c969fd888" />

---

## VII-) Pruebas de funcionamiento

Para validar la conectividad se realizaron pruebas ICMP entre hosts:
PC-A (10.9.18.10) → PC-B (10.9.19.10)

PC-B (10.9.19.10) → PC-A (10.9.18.10)

<img width="422" height="97" alt="Screenshot 2026-03-05 215344" src="https://github.com/user-attachments/assets/eba6a7f2-9044-48ae-bd73-6baa620744e5" />

Es normal que algunos paquetes iniciales fallen mientras se completa la negociación IKEv2 y se establecen las SAs.

---

## VIII-) Verificación de estado de la VPN

Para confirmar que la VPN se estableció correctamente se utilizaron comandos de verificación en los routers con el comando:
show crypto ikev2 sa

<img width="760" height="186" alt="Screenshot 2026-03-05 215505" src="https://github.com/user-attachments/assets/643f424f-bfe3-4601-9730-073f81bde35d" />

<img width="785" height="194" alt="Screenshot 2026-03-05 215515" src="https://github.com/user-attachments/assets/8e0c715d-2f24-4559-bda7-e8163f9e3519" />

Un estado READY confirma que la negociación fue exitosa y la sesión está operativa.

Posteriormente se utilizó el comando:
show crypto ipsec sa
Este comando muestra estadísticas del túnel IPsec, incluyendo los contadores encaps y decaps, los cuales representan los paquetes cifrados y descifrados respectivamente.
El incremento de estos contadores confirma que el tráfico entre las redes está siendo protegido correctamente mediante IPsec.

<img width="620" height="370" alt="Screenshot 2026-03-05 215606" src="https://github.com/user-attachments/assets/3a193162-68ba-4d15-9803-dd79914740eb" />

<img width="646" height="381" alt="Screenshot 2026-03-05 215617" src="https://github.com/user-attachments/assets/c7eb6a77-061f-4a24-a315-fe1c97f1940c" />

---

## IX-) Conclusión

Se implementó correctamente una VPN route-based mediante VTI + IPsec IKEv2, permitiendo comunicación segura entre las redes 10.9.18.0/24 y 10.9.19.0/24 a través de una red intermedia simulada. La verificación con comandos de estado (IKEv2 SA READY), contadores IPsec (encaps/decaps) y conectividad end-to-end confirma el funcionamiento y la correcta protección del tráfico.
