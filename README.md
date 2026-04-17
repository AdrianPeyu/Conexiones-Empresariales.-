# Taller Técnico: Operación Escudo 
**Módulo:** Sistemas Informáticos 

**UD6:** Seguridad y Gestión de Recursos en Red  
**Alumno:** Adrián Cabezas Castro

---

## 1. Fundamentos de Seguridad y Auditoría

### Reto de Investigación 1: Anatomía de Syslog
El estándar **Syslog** es el sistema vital de mensajería en entornos Unix/Linux. Su funcionamiento se basa en la clasificación de eventos mediante dos variables:
* **Facilidad (Facility):** Determina el origen o categoría del mensaje (ej. `auth` para autenticación, `cron` para tareas programadas, `daemon` para servicios).
* **Prioridad/Severidad (Severity):** Define la importancia del evento, en una escala del 0 (Emergency) al 7 (Debug).

**Análisis de Seguridad:**
Es una **negligencia grave** que el archivo `/var/log/auth.log` tenga permisos de lectura para usuarios no privilegiados. Este archivo contiene información crítica de enumeración: nombres de usuarios existentes en el sistema, IPs de origen y patrones de acceso. Un atacante podría usar estos datos para perfeccionar un ataque de ingeniería social o de fuerza bruta.

**Diferenciación de Registros:**
Un intento fallido por **SSH** se identifica por etiquetas como `sshd` y mensajes del tipo `Failed password for [user] from [IP]`. En cambio, un fallo **local** (frente a la pantalla) suele registrarse a través de `login` o `PAM` y carece de dirección IP y puerto efímero de origen.

### Reto de Investigación 2: Cumplimiento y Log Management
A nivel empresarial y legal (especialmente bajo el **RGPD**), la gestión centralizada de registros es fundamental. Almacenar los logs en un servidor externo seguro ofrece:
1.  **Integridad:** Evita que un atacante que ha comprometido el sistema local borre sus huellas (limpieza de logs).
2.  **Análisis Forense:** Permite reconstruir incidentes incluso si la máquina original es destruida o queda inoperativa.

**Referencias Bibliográficas (IEEE):**
* [1] W. Acosta Lugo, *Guía de uso de BBDD Académicas para Ciclos Formativos*, Sevilla, España: Departamento de Informática, 2026.
* [2] M. B. Alonso Alegre Díez, "Gestión de Logs," Trabajo Fin de Máster, Univ. Internacional de La Rioja (UNIR), 2016. [En línea]. Disponible en: https://reunir.unir.net/bitstream/handle/123456789/3618/ALONSO-ALEGRE%20DIEZ%2C%20MARIA%20BEGO%C3%91A.pdf

---

## 2. Fase Práctica: Hardening y Vigilancia

### Reto A: Auditoría de Accesos e Intrusiones
Se han generado 5 intentos fallidos de conexión SSH para auditar el sistema.

**Captura del análisis forense manual:**
*(Inserta aquí tu captura de pantalla donde se vea el comando: tail -n 20 /var/log/auth.log)*
> **Nota:** En la captura se observa la IP atacante y el mensaje `Failed password`.

**Automatización:**
El script de monitorización se encuentra en la ruta `scripts/check_intruders.sh`.  
Para ejecutarlo: `chmod +x scripts/check_intruders.sh && ./scripts/check_intruders.sh`

---

### Reto B: Configuración del "Escudo" Perimetral (UFW)
Se ha aplicado una política de **Defensa en Profundidad** con configuración de sigilo.

1.  **Denegación por defecto:** `sudo ufw default deny incoming`
2.  **Bloqueo de ICMP (Ping):** Se ha modificado `/etc/ufw/before.rules` para cambiar la regla de `ACCEPT` a `DROP` en los paquetes `echo-request`.
3.  **Apertura SSH:** `sudo ufw allow 22/tcp`

**Evidencias de Verificación:**
*(Inserta aquí captura de `sudo ufw status verbose`)*

**Prueba Cruzada:**
* **Resultado Ping:** El host remoto recibe "Time out" (Invisible).
* **Resultado SSH:** Conexión establecida correctamente.
*(Inserta aquí captura de pantalla comparando ambos resultados)*

---

## 3. Estructura del Proyecto
* `/scripts`: Contiene el script Bash de auditoría.
* `README.md`: Documentación técnica.
