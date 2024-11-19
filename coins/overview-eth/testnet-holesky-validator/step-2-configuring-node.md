# Paso 2: Configurar el nodo

## :hammer\_pick: Configurar el nodo

### Accediendo al nodo
**Usando un servidor Ubuntu**: 
Comience conectándose con su cliente SSH.

```bash
ssh nombredeusuario@staking.node.ip.address
```

**Usando el escritorio de UBUNTU**: Tienes enfrente a tu nodo **local**. Solo abre una ventana terminal desde cualquier lado pulsando Ctrl+Alt+T.

### Actualizando el nodo

Asegurate de que todo los paquetes, parches y herramientas esté, descargados, despues reinicia el equipo.

```bash
sudo apt-get update -y && sudo apt dist-upgrade -y
sudo apt-get install git ufw curl ccze jq -y
sudo apt-get autoremove
sudo apt-get autoclean
sudo reboot
```

## :key: Configuración de seguridad

### Cree un usuario no root con privilegios sudo

<Detalles>

<summary>Crea un usuario llamado "Ethereum"</summary>

Crea un nuevo usuario llamado `ethereum`

```bash
sudo useradd -m -s /bin/bash ethereum
```

Establece una contraseña para el usuario

```bash
sudo passwd ethereum
```

Añade al usuario al grupo sudo

```bash
sudo usermod -aG sudo ethereum
```

Cierra e inicia sesión con este usuario.

**Usando el servidor de Ubuntu**: Usa los siguientes comandos.

```bash
exit
ssh ethereum@staking.node.ip.address
```

**Usando el escritorio de Ubuntu**: Puede encontrar el cierre de sesión en la esquina superior derecha debajo del ícono de encendido. Clickea la cuenta de  `ethereum` e ingresa la contraseña.

</Detalles>

{% Tipo de pista="Advertencia" %}
:fire:**Avido importante**: Asegúrate de haber iniciado sesión y ejecuta todos los pasos de esta guía como el usuario sin root, `ethereum`.
{% Fin de la pista %}

### Fortalecimiento del acceso SSH

{% Tipo de pista="información" %}
**Nodo local**? Puedes saltarte esta sesion de "fortalecimiento del acceso SSH".
{% Fin de la pista%}

<detalles>

<summary>Creando una nueva llave SSH</summary>

Crea un nuevo par de llaves SSH en **tu máquina(i.e. laptop)**. Corre esto en tu **máquina,** nunca de un nodo remoto. Actualiza el comentario con tu correo electrónico o un comentario.

```
ssh-keygen -t ed25519 -C "name@email.com"
```

Verás esto a continuación:

```
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/<myUserName>/.ssh/id_ed25519):
```

Aquí se le pedirá que escriba un nombre de archivo en el que guardar la clave privada SSH. Si presiona Enter, puede usar el nombre de archivo predeterminado `id_ed25519`

Luego, seras incitado a poner una contraseña.

```
Ingrese contraseña (Deje vacio si no desea poner contraseña):
```

:information\_source: Una **contraseña** añade una capa extra de protección para tu llave SSH. Cada vez que te conectes a tu nodo po SSH, Ingresa esta contraseña para desbloquear tu llave SSH.

:fire: Passphrase is highly recommended! Do not leave this empty for no passphrase.

:bulb:No olvides ni pierdas tu contraseña. Guarda esto en un administrador de contraseñas.

**Localización**: Tu par de llaves SSH está guardado en tu directorio local bajo: `~/.ssh`

**Nombre del archivo:** Si tu llave predeterminada es `id_ed25519`, entonces

* Tu **Llave privada SSH** es `id_ed25519`
* Tu **Llave pública SSH** es `id_ed25519.pub`

:fire: **Importante:** Haz muchos respaldos de tu **Llave privada SSH** en almacenamiento externo, como una USB, para propositos de recuperación. También respalda tu **contraseña**!

Verifica el contenido de tu llave privada SSH antes de moverla.

```
cat ~/.ssh/id_ed25519
```

Debería verse mas o menos como en el ejemplo.

```
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW
QyNTUxOQAAACBAblzWLb7/0o62FZf9YjLPCV4qFhbqiSH3TBvZXBiYNgAAAJCWunkulrp5
LgAAAAtzc2gtZWQyNTUxOQAAACBAblzWLb7/0o62FZf9YjLPCV4qFhbqiSH3TBvZXBiYNg
AAAEAxT+yCmifGWgbFnkauf0HyOAJANhYY5EElEX8fI+M4B0BuXNYtvv/SjrYVl/1iMs8J
XioWFuqJIfdMG9lcGJg2AAAACWV0aDJAZXRoMgECAwQ=
-----END OPENSSH PRIVATE KEY-----
```

</Detalles>

#### Transfiriendo la llave pública SSH a un nodo remoto

<Detalles>

<summary>Opción 1: Transfiriendola con una copia de ID</summary>

Funciona para Linux o MacOS. Usa la opción 2 para windows.

```bash
ssh-copy-id -i ~/.ssh/id_ed25519 ethereum@staking.node.ip.address
```

</detalles>

<detalles>

<summary>Opción 2: Copiando la llave manualmente</summary>

Primero, comience obteniendo su clave pública SSH.

Para Linux/Mac,

```
cat ~/.ssh/id_ed25519.pub
```

Para Windows,

Abre una ventana de comando (Windows Key + R, después `cmd`, para finalizar pulsa enter).

```
type %USERPROFILE%\.ssh\id_ed25519.pub
```

La salida debería verse algo así como:

```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAoc78lv+XDh2znunKXUF/9zBNJrM4Nh67yut9RN14SX name@email.com
```

Copia esto al portapapeles, conocida tambien como tu llave pública SSH.

En tu **nodo remoto**, corre lo siguiente:

```
mkdir -p ~/.ssh
nano ~/.ssh/authorized_keys
```

Primero, se crea un directorio llamado **.ssh**, después `Nano`es un editor de texto creado para una linea especial llamada **authorized\_keys**

Con nano abre authorized\_keys file, click derecho del mouse para pagar tu llave pública SSH en esta dirección.

Para salir y guardar, presiona `Ctrl` + `X`, despues `Y`, y al final`Enter`.

Verifica que tu llave pública SSH haya sido pegada correctamente.

```
cat ~/.ssh/authorized_keys
```

</detalles>

#### Disabling Password Authentication

<details>

<summary>Disabling root login and password based login</summary>

:information\_source: With SSH key authentication enabled, there's still the possibility to connect to your remote node with login and password, a much less secure and brute force-able attack vector.

Login via ssh with your new ethereum user

```
ssh ethereum@staking.node.ip.address
```

Edit the ssh configuration file

```
sudo nano /etc/ssh/sshd_config
```

Locate **PubkeyAuthentication** and update to yes. Delete the # in front.

```
PubkeyAuthentication yes
```

Locate **PasswordAuthentication** and update to no. Delete the # in front.

```
PasswordAuthentication no
```

Locate **PermitRootLogin** and update to prohibit-password. Delete the # in front.

```
PermitRootLogin prohibit-password
```

Locate **PermitEmptyPasswords** and update to no. Delete the # in front.

```
PermitEmptyPassword no
```

To exit and save, press `Ctrl` + `X`, then `Y`, then`Enter`.

Validate the syntax of your new SSH configuration.

```
sudo sshd -t
```

If no errors with the syntax validation, restart the SSH process.

```
sudo systemctl restart sshd
```

Verify the login still works.

```
ssh ethereum@staking.node.ip.address
```

**Optional**: Make logging in easier by updating your local ssh config.

To simplify the ssh command needed to log in to your server, consider updating on your local client machine the `$HOME/myUserName/.ssh/config` file:

```bash
Host ethereum-server
  User ethereum
  HostName <staking.node.ip.address>
  Port 22
```

This will allow you to log in with `ssh ethereum-server` rather than needing to pass through all ssh parameters explicitly.

</details>

### Synchronizing time with Chrony

chrony is an implementation of the Network Time Protocol and helps to keep your computer's time synchronized with NTP.

{% hint style="info" %}
Because the consensus client relies on accurate times to perform attestations and produce blocks, your node's time must be accurate to real NTP time within 0.5 seconds.
{% endhint %}

To install chrony:

```bash
sudo apt-get install chrony -y
```

To see the source of synchronization data.

```
chronyc sources
```

To view the current status of chrony.

```
chronyc tracking
```

### Setting Timezone

To pick your timezone run the following command:

```bash
sudo dpkg-reconfigure tzdata
```

Find your region using the simple text-based GUI.

In the event that you are using national system like India's `IST` select:

```
Asia/Kolkata
```

This will be appropriate for all locales in the country (`IST`, `GMT+0530`).

### Creating the jwtsecret file

A jwtsecret file contains a hexadecimal string that is passed to both Execution Layer client and Consensus Layer clients, and is used to ensure authenticated communications between both clients.

```bash
#store the jwtsecret file at /secrets
sudo mkdir -p /secrets

#create the jwtsecret file
openssl rand -hex 32 | tr -d "\n" | sudo tee /secrets/jwtsecret

#enable read access
sudo chmod 644 /secrets/jwtsecret
```

## :link: Network Configuration

The standard UFW - Uncomplicated firewall can be used to control network access to your node and protect against unwelcome intruders.

### Configure UFW Defaults

By default, deny all incoming traffic and allow outgoing traffic.

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

### Configure SSH Port 22

If your node is remote in the cloud, or at home but on a different headless server, you will need to enable SSH port 22 in order to connect.

```bash
# Allow ssh access for remote node
sudo ufw allow 22/tcp comment 'Allow SSH port'
```

If your node is local at home and you have **keyboard access** to it, it's good practice to deny SSH port 22.

```bash
# Deny ssh access for local node
sudo ufw deny 22/tcp comment 'Deny SSH port'
```

### Allow Execution Client Port 30303

Peering on port 30303, execution clients use this port for communication with other network peers.

```bash
sudo ufw allow 30303 comment 'Allow execution client port'
```

### Allow Consensus Client port

Consensus clients generally use port 9000 for communication with other network peers. Using tcp port 13000 and udp port 12000, Prysm uses a slightly different configuration.

```bash
# Lighthouse, Lodestar, Nimbus, Teku
sudo ufw allow 9000 comment 'Allow consensus client port'

# Lighthouse Quic Port https://lighthouse-blog.sigmaprime.io/Quic.html
sudo ufw allow 9001/udp comment 'Allow lighthouse client quic port'

# Prysm
sudo ufw allow 13000/tcp comment 'Allow consensus client port'
sudo ufw allow 12000/udp comment 'Allow consensus client port'
```

### Enable firewall

Finally, enable the firewall and review the configuration.

```bash
sudo ufw enable
sudo ufw status numbered 
```

Example of ufw status for a remote staking node configured for Prysm consensus client.

> ```csharp
>      To                         Action      From
>      --                         ------      ----
> [ 1] 22/tcp                     ALLOW IN    Anywhere
> [ 2] 9000                       ALLOW IN    Anywhere
> [ 3] 30303                      ALLOW IN    Anywhere
> [ 4] 22/tcp (v6)                ALLOW IN    Anywhere (v6)
> [ 5] 9000 (v6)                  ALLOW IN    Anywhere (v6)
> [ 6] 30303 (v6)                 ALLOW IN    Anywhere (v6)
> ```

### Configure Port Forwarding

**Port Forwarding Tip for Local Stakers at Home:** You'll need to forward ports to your validator.

For optimal connectivity, ensure Port Forwarding is setup for your router. Learn to port forward with guides found at [https://portforward.com/how-to-port-forward](https://portforward.com/how-to-port-forward/)

Verify port forwarding is working with the following.

**Option 1:** From the terminal on staking machine. Choose accordingly to your clients.

```bash
# Lighthouse, Lodestar, Nimbus, Teku
curl https://eth2-client-port-checker.vercel.app/api/checker?ports=30303,9000

# Prysm
curl https://eth2-client-port-checker.vercel.app/api/checker?ports=30303,12000,13000
```

**Result:** Open ports will be shown if reachable from public.

\
**Option 2:** Using the browser

* [https://www.yougetsignal.com/tools/open-ports/](https://www.yougetsignal.com/tools/open-ports/)
* or [https://canyouseeme.org](https://canyouseeme.org)

As an example, for Lighthouse, you would verify ports 9000 and 30303 are reachable.

### Optional: Whitelisting Connections

Whitelisting, which means permitting connections from a specific IP, can be setup via the following command.

```bash
sudo ufw allow from <your client machine>
# Example
# sudo ufw allow from 192.168.50.22
```

### :chains: **Install Fail2ban**

{% hint style="info" %}
Fail2ban is an intrusion-prevention system that monitors log files and searches for particular patterns that correspond to a failed login attempt. If a certain number of failed logins are detected from a specific IP address (within a specified amount of time), fail2ban blocks access from that IP address.
{% endhint %}

To install fail2ban:

```bash
sudo apt-get install fail2ban -y
```

Edit a config file that monitors SSH logins.

```bash
sudo nano /etc/fail2ban/jail.local
```

Add the following lines to the bottom of the file.

```bash
[sshd]
enabled = true
port = 22
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
```

To exit and save, press `Ctrl` + `X`, then `Y`, then`Enter`.

Restart fail2ban for settings to take effect.

```bash
sudo systemctl restart fail2ban
```
