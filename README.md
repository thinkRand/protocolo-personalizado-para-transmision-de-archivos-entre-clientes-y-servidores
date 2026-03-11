# Protocolo Personalizado de Transmisión de Archivos

Proyecto de entrevista técnica para puesto de Backend Junior. El reto consistía en **aprender Go desde cero** y aplicarlo para construir un sistema cliente-servidor con protocolo propio en un mes.

## ¿Qué hace?

Es un sistema de chat tipo "sala" donde los clientes se unen a canales y pueden enviar archivos que se redistribuyen automáticamente a todos los miembros del canal.

**Flujo básico:**
1. Cliente se conecta al servidor
2. Se une a un canal (`unir canal1`)
3. Envía un archivo (`enviar canal1 archivo.txt`)
4. El servidor recibe el archivo y lo reenvía a todos los demás clientes en ese canal

## Estructura del proyecto

```
protocoloSimple/     → El protocolo de comunicación (códigos, validaciones, formato)
servidor/            → Servidor TCP que gestiona canales y redistribuye archivos
cliente/             → Cliente interactivo para enviar archivos
clienteVecino/       → Cliente para recibir archivos (mismo código, diferente uso)
```

## El protocolo (ProtocoloSimple)

Inventé un protocolo de texto simple para coordinar cliente-servidor:

**Ejemplo de mensajes:**
```
cuc canal1           → Cliente pide unirse a canal1
sua                  → Servidor acepta
cea canal1 foto.jpg 2048  → Cliente quiere enviar archivo de 2048 bytes
sea                  → Servidor aprueba el envío
[datos del archivo]  → Cliente envía bytes
sar                  → Servidor confirma recepción
cae                  → Cliente confirma que terminó de enviar
sft                  → Servidor indica fin de transmisión
```

Los códigos son abreviaturas:
- `cuc` = **C**liente **U**nirse **C**anal
- `sua` = **S**ervidor **U**nir **A**ceptado
- `cea` = **C**liente **E**nviar **A**rchivo
- etc.

## Aspectos técnicos destacados

**Concurrencia con Go:**
- Cada cliente conectado genera 2 goroutines (lectura y escritura)
- Uso de canales de Go (`chan`) para sincronización sin mutex explícitos
- El servidor maneja múltiples clientes simultáneos sin bloquearse

**Validación:**
- Verificación de bytes recibidos vs. bytes esperados
- Manejo de desconexiones (EOF) y limpieza de recursos
- Estados del cliente: `CONECTADO` → `UNIDO_A_CANAL` → `ATENDIENDO_ORDEN`

## Sobre el reto

**Condiciones:**
- Lenguaje Go (nunca lo había usado antes)
- Protocolo personalizado (no HTTP, no gRPC)
- Sistema distribuido real con concurrencia

**Aprendizaje:**
Este proyecto me obligó a entender goroutines, channels, networking TCP en Go, y diseño de protocolos binarios/texto. Los comentarios en el código reflejan ese proceso de aprendizaje en tiempo real.
