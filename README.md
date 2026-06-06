# Ataque MitM mediante ARP Spoofing
## Practica de Laboratorio - Seguridad de la Informacion | ITLA

> **Entorno:** Laboratorio academico controlado y autorizado  
> **Matricula:** 20250730  
> **Fecha:** Junio 2025

---

## Objetivo

Demostrar como un atacante puede posicionarse como intermediario (Man-in-the-Middle) entre una victima y su gateway, envenenando las tablas ARP de ambos dispositivos para interceptar y reenviar su trafico sin que ninguno lo detecte.

---

## Topologia

```
  [Kali Linux - Atacante]
   IP: 202.50.73.10/24
   MAC: AA:BB:CC:DD:EE:FF
          |
     [eth0 / Fa0/1]
          |
 +--------+--------+
 |  SWITCH CISCO   |
 +--------+--------+
      |         |
  [Fa0/2]   [Fa0/3]
      |           |
[PC Victima]  [Gateway/Router]
202.50.73.20  202.50.73.1

Flujo normal:  Victima <-----------> Gateway
Flujo MitM:   Victima <--> Kali <--> Gateway
```

| Dispositivo  | IP              | Rol               |
|--------------|-----------------|-------------------|
| Kali Linux   | 202.50.73.10/24 | Atacante (MitM)   |
| PC Victima   | 202.50.73.20/24 | Victima           |
| Gateway/Router | 202.50.73.1/24 | Gateway objetivo |
| Switch S1    | 202.50.73.254   | Infraestructura   |

> Red base derivada de matricula **20250730** → `202.50.73.0/24`

---

## Requisitos

- **SO:** Kali Linux 2023.1+
- **Python:** 3.8+
- **Libreria:** Scapy
- **Privilegios:** root (sudo)

---

## Instalacion

```bash
git clone https://github.com/TU_USUARIO/ataque2-mitm-arp.git
cd ataque2-mitm-arp

sudo apt update && sudo apt install python3-scapy -y
```

---

## Uso

```bash
# Ataque MitM entre victima y gateway
sudo python3 arp_mitm.py -i eth0 -v1 202.50.73.20 -gw 202.50.73.1

# Con intervalo de 1 segundo entre paquetes ARP
sudo python3 arp_mitm.py -i eth0 -v1 202.50.73.20 -gw 202.50.73.1 --interval 1

# Ver contramedidas
python3 arp_mitm.py --contramedida
```

> **Detener:** `Ctrl+C` — el script restaura automaticamente las tablas ARP.

### Verificacion del ataque (en la victima)

```bash
# Ver tabla ARP envenenada (la MAC del gateway sera la del atacante)
arp -a

# Windows
arp -a
```

### Captura de trafico (en Kali, mientras el ataque esta activo)

```bash
# Wireshark
wireshark -i eth0 -k

# tcpdump
sudo tcpdump -i eth0 -w captura_mitm.pcap
```

---

## Evidencias

Guardar en `/evidencias`:

| Evidencia | Descripcion |
|-----------|-------------|
| `arp_antes.png` | `arp -a` en la victima antes del ataque |
| `arp_despues.png` | `arp -a` en la victima con la MAC envenenada |
| `wireshark_arp_poison.png` | Paquetes ARP Reply falsos en Wireshark |
| `trafico_interceptado.png` | Trafico HTTP/ICMP capturado en Kali |
| `dai_config.png` | Configuracion DAI aplicada como contramedida |

---

## Contramedidas

```ios
! DYNAMIC ARP INSPECTION en el switch Cisco
Switch(config)# ip arp inspection vlan 1
Switch(config)# interface GigabitEthernet0/1
Switch(config-if)# ip arp inspection trust
Switch(config-if)# exit

! Verificar
Switch# show ip arp inspection vlan 1
Switch# show ip arp inspection statistics
```

---

## Estructura del Repositorio

```
ataque2-mitm-arp/
├── README.md
├── arp_mitm.py
├── Ataque2_MitM_ARP_20250730.docx
└── evidencias/
    ├── arp_antes.png
    ├── arp_despues.png
    ├── wireshark_arp_poison.png
    ├── trafico_interceptado.png
    └── dai_config.png
```

---

---

## Autor

**Matricula:** 20250730  
**Institucion:** ITLA — Instituto Tecnologico de las Americas   
**Fecha:** Junio 2025
