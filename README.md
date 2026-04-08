#   Estructura de la Investigación
# 1. Arquitectura de Virtualización en Windows 11

//Aislamiento de Núcleo y VBS: Explicar el impacto de las funciones de seguridad de Windows 11 en la virtualización.\\
Impacto en la virtualización

-Mayor seguridad: Protegen el sistema contra malware y ataques al kernel.

-Aislamiento de procesos críticos: Evitan accesos no autorizados a memoria sensible.

-Menor rendimiento: Puede afectar la velocidad de máquinas virtuales.

-Compatibilidad: Algunos programas como VirtualBox o VMware Workstation pueden presentar conflictos


//Activación de VT-x/AMD-V: Procedimiento para habilitar el soporte de hardware y cómo verificarlo desde el sistema operativo.\\

-Se activa al ingresar a la BIOS y activar Intel Virtualization Technology (VT-x) o SVM Mode (AMD-V), guardar cambios y reiniciar

-Para verificar accedemos al administrador de tareas luego a CPU y verificamos si virtualización esta habilitada 

(observar imagen "Virtualizacion")

# 2. GNS3 VM: El Motor de Simulación

//KVM (Kernel-based Virtual Machine): Investigar qué es y por qué es obligatorio que aparezca como "True" en el servidor GNS3 para un rendimientoprofesional.\\

-KVM es una tecnología de virtualización integrada en Linux que permite ejecutar máquinas virtuales con alto rendimiento usando directamente el hardware del procesador

-Porque permite que los dispositivos virtuales funcionen de forma mas eficiente

//Configuración de Recursos: Definir criterios para asignar CPU y RAM a la GNS3 VM sin desestabilizar Windows 11.\\

-Es importante asignar CPU y RAM de forma equilibrada para evitar que el sistema se vuelva lento o inestable como: 

CPU:
Asignar entre 50% y 70% de los núcleos disponibles
Ejemplo: si tienes 8 núcleos → usar 4 a 6

RAM:
Usar entre 40% y 60% de la memoria total
Ejemplo: 16 GB → asignar 6 a 10 GB

# 3. Integración con VirtualBox (Local)

//Configuración de Red: Pasos para crear y configurar el adaptador Host-Only para la comunicación GUI-Server.\\

-Los pasos son los siguientes:

Crear un adaptador Host-Only en el software de virtualización (ej. VirtualBox o VMware Workstation).

Configurar una red interna (ejemplo: 192.168.56.0/24).

Asignar una IP al host (ej. 192.168.56.1).

Configurar la GNS3 VM con otra IP del mismo rango (ej. 192.168.56.101).

Verificar la conectividad (ping entre host y VM).

(Observar imagen "Host-Only y Promiscuous Mode")

//Modo Promiscuo: Explicar técnicamente por qué es necesario para el tráfico de Capa 2.\\

-En la Capa 2, los dispositivos trabajan con direcciones MAC y envían tráfico específico dentro de la red. Sin modo promiscuo, la interfaz solo recibe tráfico dirigido a su MAC y se pierden paquetes importantes en simulaciones

-Con modo promiscuo. Se capturan todos los frames Ethernet, permite analizar y reenviar tráfico de switches y routers virtuales

# 4. Integración con VMware ESXi (Remoto)

//Arquitectura Cliente-Servidor: Cómo conectar el GUI de GNS3 de la laptop a un servidor ESXi físico.\\

-Configurar red entre laptop y servidor ESXi (puede ser LAN o VPN). luego importamos la máquina virtual de GNS3 en el servidor físico. Abrir GNS3 en la laptop, vamos a Preferences → Server → Remote Server y ingresamo la IP del servidor ESXi y puerto del GNS3 VM, hacemos ping desde GUI a GNS3 VM y nos aseguramos de que el tráfico de control fluya correctamente

(Observar imagen "Multi-Hypervisor y ESXi remoto")


//Seguridad en vSwitch: Investigar la configuración de "Políticas de Seguridad" (Promiscuous mode, MAC address changes) en el port group de ESXi.\\

-Cada port group tiene Políticas de Seguridad que controlan el tráfico y la visibilidad de la red

Promiscuous Mode: Permite que la VM reciba todo el tráfico de la red, no solo el dirigido a su MAC

MAC Address Changes: Controla si la VM puede cambiar su dirección MAC virtual

Forged Transmits:Controla si la VM puede enviar paquetes con MAC diferente a la asignada

# 5. Matriz de Solución de Errores (Troubleshooting)

//Crear una tabla con al menos 3 errores comunes (ej: KVM not available, uBridge permissions, Firewall blocking port 3080) y su solución técnica\\


| ❌ Error común                   | 📝 Causa                                                           | 🛠️ Solución técnica                                                                                      |                       |        
| ------------------------------- | ------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | ----------------------------- |
| **KVM not available**           | KVM no está habilitado en BIOS o VT-x/AMD-V desactivado            | Entrar al BIOS/UEFI → Activar **Intel VT-x** o **AMD-V** → Reiniciar → Verificar con `egrep -c '(vmx      |   
| **uBridge permissions**         | GNS3 uBridge no tiene permisos de administrador para abrir sockets | Ejecutar uBridge como **administrador/root** o ajustar permisos del binario (`chmod +x` en Linux)         |                              |
| **Firewall blocking port 3080** | El firewall bloquea la comunicación entre GUI y GNS3 VM            | Abrir el puerto 3080 en el firewall de Windows o del servidor (`netsh advfirewall firewall add rule ...`) |                             |

