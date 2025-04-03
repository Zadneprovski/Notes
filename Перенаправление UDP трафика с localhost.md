Для выполнения вышеописанных действий вам понадобятся следующие пакеты:

1. **iptables**: Утилита для настройки правил брандмауэра в Linux.
2. **socat**: Универсальный инструмент для передачи данных между двумя точками.

```bash
sudo apt update
sudo apt install iptables socat
```

Для перенаправления UDP трафика с localhost на другой ip и при этом использовать исходящий порт 5555, можно воспользоваться iptables и утилитой socat. Вот пример, как это можно сделать:

1. **Настроим iptables для изменения исходящего порта:**

```bash
# Создаем новую цепочку
sudo iptables -t nat -N CHANGE-PORT

# Добавляем правило в эту цепочку для изменения исходящего порта на 5555
sudo iptables -t nat -A CHANGE-PORT -p udp --dport <destination_port> -j SNAT --to-source :5555

# Применяем эту цепочку к исходящим пакетам, идущим на <destination_ip>:<destination_port>
sudo iptables -t nat -A OUTPUT -p udp --dport <destination_port> -d <destination_ip> -j CHANGE-PORT
```

Замените `<destination_ip>` и `<destination_port>` на нужные вам значения.

2. **Используем socat для перенаправления трафика:**

```bash
socat UDP4-RECVFROM:<local_port>,fork UDP4-SENDTO:<destination_ip>:<destination_port>
```

Замените `<local_port>` на порт на localhost, с которого хотите перенаправить трафик, а `<destination_ip>:<destination_port>` на нужный вам IP и порт назначения.

Таким образом, `socat` будет перенаправлять трафик, а `iptables` будет изменять исходящий порт на 5555.

**Полный пример:**

Допустим, вам нужно перенаправить трафик с `localhost:12345` на `192.168.1.100:54321`, и исходящий трафик должен использовать порт 5555.

1. Настроим iptables:

```bash
sudo iptables -t nat -N CHANGE-PORT
sudo iptables -t nat -A CHANGE-PORT -p udp --dport 54321 -j SNAT --to-source :5555
sudo iptables -t nat -A OUTPUT -p udp --dport 54321 -d 192.168.1.100 -j CHANGE-PORT
```
2. Запустим socat:

```bash
socat UDP4-RECVFROM:12345,fork UDP4-SENDTO:192.168.1.100:54321
```
После выполнения этих команд UDP трафик, поступающий на `localhost:12345`, будет перенаправлен на `192.168.1.100:54321`, при этом исходящий трафик будет использовать порт 5555.

### Выполнить с помощью python

```python
import subprocess

def check_installation():
    required_commands = ["iptables", "socat"]
    for command in required_commands:
        if subprocess.call(["which", command], stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL) != 0:
            raise EnvironmentError(f"{command} is not installed or not found in PATH.")

def setup_iptables(destination_ip, destination_port):
    commands = [
        ["sudo", "iptables", "-t", "nat", "-N", "CHANGE-PORT"],
        ["sudo", "iptables", "-t", "nat", "-A", "CHANGE-PORT", "-p", "udp", "--dport", str(destination_port), "-j", "SNAT", "--to-source", ":5555"],
        ["sudo", "iptables", "-t", "nat", "-A", "OUTPUT", "-p", "udp", "--dport", str(destination_port), "-d", destination_ip, "-j", "CHANGE-PORT"]
    ]
    for command in commands:
        subprocess.run(command, check=True)

def start_socat(local_port, destination_ip, destination_port):
    command = [
        "socat",
        f"UDP4-RECVFROM:{local_port},fork",
        f"UDP4-SENDTO:{destination_ip}:{destination_port}"
    ]
    subprocess.run(command, check=True)

def main():
    check_installation()

    local_port = 12345
    destination_ip = "192.168.1.100"
    destination_port = 54321

    setup_iptables(destination_ip, destination_port)
    start_socat(local_port, destination_ip, destination_port)

if __name__ == "__main__":
    main()
```