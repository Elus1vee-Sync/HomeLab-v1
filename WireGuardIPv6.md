```mermaid
sequenceDiagram
    autonumber
    actor Cliente as Cliente Remoto<br/>(Móvil/PC fuera de casa)
    participant Net as Internet IPv6 Pública
    participant MT as MikroTik de Casa<br/>(Servidor WireGuard)
    participant LAN as Red Local Doméstica<br/>(NAS, Domótica, etc.)

    Note over Cliente, MT: Fase 1: Inicio del Túnel sobre IPv6
    Cliente->>Net: Tráfico de control UDP cifrado (Puerto 51820)
    Net->>MT: Destino: [Dirección_IPv6_Pública_MikroTik]
    MT-->>Cliente: Handshake OK -> Canal Seguro IPv6 Establecido
    
    rect rgb(20, 35, 50)
        Note over Cliente, LAN: Fase 2: Navegación e Intercambio de Datos
        Cliente->>MT: Envío de paquete (Ej: Acceder a un PC local o una Web)
        Note over MT: El MikroTik recibe el paquete IPv6 encapsulado,<br/>lo descifra y extrae la petición interna.
        
        alt Acceso a recursos de Casa
            MT->>LAN: Tráfico redirigido a la IP local (Ej: 192.168.1.100)
            LAN-->>MT: Respuesta del servidor local
        else Navegación Segura por Internet
            MT->>Net: Tráfico NATeado hacia Internet exterior (IPv4 o IPv6)
            Net-->>MT: Respuesta de la página Web externa
        end

        MT-->>Cliente: Paquete de respuesta cifrado de vuelta por el túnel IPv6
    end
