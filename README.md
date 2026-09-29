# Wazuh como plataforma SIEM/XDR de código abierto

Detección de ataques y respuesta activa automatizada en un laboratorio virtual.

**Curso:** SI-985 Seguridad de Tecnología de Información — Universidad Privada de Tacna (UPT)
**Docente:** Dr. Renzo Alberto Taco Coayla · **Proyecto:** Unidad I 2026-II
**Integrantes:** Anampa Pancca, David Jordan · [Integrante 2] · [Integrante 3]

## ¿Qué es?

Wazuh es una plataforma gratuita y de código abierto que integra SIEM y XDR para vigilar equipos y servidores en tiempo real. Este proyecto instala un servidor Wazuh, conecta un agente en un equipo de prueba y demuestra que el sistema detecta un ataque de fuerza bruta contra SSH y bloquea la IP atacante de forma automática (respuesta activa).

## Arquitectura

| Componente | Función | Puerto habitual |
|---|---|---|
| Agente | Recolecta eventos y ejecuta la respuesta activa | se conecta al servidor |
| Servidor | Decodifica eventos, aplica reglas y genera alertas | 1514, 1515, 55000 |
| Indexer | Almacena e indexa las alertas | 9200 |
| Dashboard | Interfaz web | 443 |

## Laboratorio

Red solo anfitrión (por ejemplo 192.168.56.0/24), aislada de equipos ajenos.

| Máquina | Rol | Sistema |
|---|---|---|
| wazuh-server | Servidor todo en uno (4 vCPU, 8 GB RAM) | Ubuntu Server 22.04 |
| victima-linux | Equipo protegido con agente y SSH | Ubuntu Server 22.04 |
| atacante | Origen del ataque | Kali Linux |

## Reproducir el prototipo

1. Instalar el servidor con el asistente oficial (sustituir `<VERSIÓN>` por la de la guía de instalación rápida de Wazuh):
   ```bash
   curl -sO https://packages.wazuh.com/<VERSIÓN>/wazuh-install.sh
   sudo bash ./wazuh-install.sh -a
   ```
2. Entrar al Dashboard por HTTPS y copiar el comando de despliegue del agente; ejecutarlo en `victima-linux`.
3. Activar la respuesta activa en `/var/ossec/etc/ossec.conf` (ver `config/ossec-active-response.xml`) y reiniciar: `sudo systemctl restart wazuh-manager`.
4. Lanzar el ataque, solo contra equipos propios del laboratorio:
   ```bash
   hydra -l usuario_prueba -P lista_contrasenas.txt ssh://192.168.56.20 -t 4
   ```
5. Verificar la alerta en el Dashboard y el bloqueo en el agente:
   ```bash
   sudo tail -f /var/ossec/logs/active-responses.log
   sudo iptables -L -n
   ```

## Indicadores

| Indicador | Meta | Resultado |
|---|---|---|
| Tiempo de detección | < 60 s | [medir] |
| Tiempo de contención | ≤ 10 s | [medir] |
| Cobertura de agentes | 100 % | [medir] |
| Accesos tras el bloqueo | 0 | [medir] |

## Estructura del repositorio

```
proyecto-wazuh/
├── README.md
├── docs/            (informe Word y PDF, presentación)
├── config/          (ossec-active-response.xml)
├── evidencias/      (capturas y registros)
└── referencias/     (bibliografía APA 7)
```

## Aviso ético

Las herramientas ofensivas se usan únicamente en el laboratorio aislado, sobre equipos propios y con autorización del docente.

## Enlace

Informe, presentación y evidencias: [pegar enlace de Google Drive u OneDrive]
