# Linux Network Report

## Part 1. Инструмент ipcalc

### 1.1. Сети и маски

- **1) Адрес сети 192.167.38.54/13:**

  - ![Адрес сети 192.167.38.54/13, определённый с помощью ipcalc](images/image.png)

- **2) Перевод масок:**

  - 255.255.255.0 в двоичном виде: `11111111.11111111.11111111.00000000`
  - 255.255.255.0 в префиксном виде: `/24`
  - /15 в обычном виде: `255.254.0.0`
  - /15 в двоичном виде: `11111111.11111110.00000000.00000000`
  - 11111111.11111111.11111111.11110000 в обычном виде: `255.255.255.240`
  - 11111111.11111111.11111111.11110000 в префиксном виде: `/28`

  - ![Перевод маски /15 через ipcalc](images/image%201.png)

- **3) Минимальный и максимальный хост в сети 12.167.38.4:**

  - Маска /8:
    - HostMin: `12.0.0.1`
    - HostMax: `12.255.255.254`
    - ![ipcalc для 12.167.38.4/8](images/image%202.png)

  - Маска 11111111.11111111.00000000.00000000 (/16):
    - HostMin: `12.167.0.1`
    - HostMax: `12.167.255.254`
    - ![ipcalc для 12.167.38.4/16](images/image%203.png)

  - Маска 255.255.254.0 (/23):
    - HostMin: `12.167.38.1`
    - HostMax: `12.167.39.254`
    - ![ipcalc для 12.167.38.4/23](images/image%204.png)

  - Маска /4:
    - HostMin: `0.0.0.1`
    - HostMax: `15.255.255.254`
    - ![ipcalc для 12.167.38.4/4](images/image%205.png)

### 1.2. localhost

- Диапазон адресов localhost: `127.0.0.1 — 127.255.255.255`
- **Можно** обратиться к localhost: `127.0.0.2`, `127.1.0.1`
- **Нельзя** обратиться к localhost: `194.34.23.100`, `128.0.0.1`

### 1.3. Диапазоны и сегменты сетей

- **1) Публичные и частные IP-адреса:**

  - ![Диапазоны частных сетей](images/image%206.png)

  - Частные: `10.0.0.45`, `192.168.4.2`, `172.20.250.4`, `172.16.255.255`, `10.10.10.10`
  - Публичные: `134.43.0.2`, `172.0.2.1`, `192.172.0.1`, `172.68.0.2`, `192.169.168.1`

- **2) Возможные адреса шлюза для сети 10.10.0.0/18:**

  - ![ipcalc для сети 10.10.0.0/18](images/image%207.png)

  - Могут быть шлюзом: `10.10.0.2`, `10.10.10.10`, `10.10.1.255`
  - Не может быть шлюзом: `10.0.0.1` (не входит в сеть), `10.10.100.1` (выходит за пределы сети /18)

## Part 2. Статическая маршрутизация между двумя машинами

- Вывод команды `ip a` на ws1 и ws2 — список сетевых интерфейсов:

  - ![Вывод ip a на ws1 и ws2](images/image%208.png)

- Содержимое файла `/etc/netplan/00-installer-config.yaml` на ws1 (адрес 192.168.100.10/16):

  - ![Netplan конфиг ws1](images/image%209.png)
  - ![Netplan конфиг ws1 — продолжение](images/image%2010.png)

- Содержимое файла `/etc/netplan/00-installer-config.yaml` на ws2 (адрес 172.24.116.8/12):

  - ![Netplan конфиг ws2](images/image%2011.png)

- Вывод команды `netplan apply` на ws1 и ws2:

  - ![Применение netplan apply на ws1](images/image%209.png)

### 2.1. Добавление статического маршрута вручную

- Добавление статического маршрута на ws1 командой `ip r add`:

  - ![Статический маршрут на ws1 через ip r add](images/image%2012.png)

- Добавление статического маршрута на ws2 командой `ip r add`:

  - ![Статический маршрут на ws2 через ip r add](images/image%2013.png)

- Пинг ws2 с ws1:

  - ![Пинг ws2 с ws1](images/image%2014.png)

- Пинг ws1 с ws2:

  - ![Пинг ws1 с ws2](images/image%2015.png)

### 2.2. Добавление статического маршрута с сохранением

- Содержимое изменённого файла `/etc/netplan/00-installer-config.yaml` на ws1 с постоянным маршрутом:

  - ![Netplan с постоянным маршрутом на ws1](images/image%2016.png)

- Содержимое изменённого файла `/etc/netplan/00-installer-config.yaml` на ws2 с постоянным маршрутом:

  - ![Netplan с постоянным маршрутом на ws2](images/image%2017.png)

- Применение `netplan apply` и пинг ws2 с ws1:

  - ![netplan apply и пинг ws2 с ws1](images/86fea1ed-7025-49e5-902f-39116243058c.png)

- Применение `netplan apply` и пинг ws1 с ws2:

  - ![netplan apply и пинг ws1 с ws2](images/image%2018.png)

## Part 3. Утилита iperf3

### 3.1. Скорость соединения

- 8 Mbps = **1 MB/s** (8 / 8 = 1)
- 100 MB/s = **800 000 Kbps** (100 × 8 × 1000 = 800 000)
- 1 Gbps = **1000 Mbps**

### 3.2. Утилита iperf3

- Запуск iperf3 в режиме сервера на ws2 и клиента на ws1, измерение скорости соединения:

  - ![iperf3 сервер на ws2 и клиент на ws1](images/image%2019.png)
  - ![Результат измерения скорости iperf3](images/image%2020.png)

## Part 4. Сетевой экран

### 4.1. Утилита iptables

- Содержимое файла `/etc/firewall.sh` на ws1 (стратегия: сначала DROP, затем ACCEPT):

  - ![Файл firewall.sh на ws1](images/image%2021.png)

- Содержимое файла `/etc/firewall.sh` на ws2 (стратегия: сначала ACCEPT, затем DROP):

  - ![Файл firewall.sh на ws2](images/image%2022.png)

- Запуск файла фаервола на ws1 командами `chmod +x /etc/firewall.sh` и `/etc/firewall.sh`:

  - ![Запуск firewall.sh на ws1](images/image%2023.png)

- Запуск файла фаервола на ws2:

  - ![Запуск firewall.sh на ws2](images/image%2024.png)

- **Разница между стратегиями:** iptables обрабатывает правила строго сверху вниз и останавливается на первом совпадении. На ws1 первым стоит правило DROP — пинг блокируется, правило ACCEPT никогда не достигается. На ws2 первым стоит правило ACCEPT — пинг разрешается, правило DROP никогда не достигается. Таким образом, результат определяется **порядком правил**, а не их наличием.

### 4.2. Утилита nmap

- Пинг ws2 с ws1 — ws2 не пингуется (DROP стоит первым на ws2... нет, ws1 не отвечает):

  - ![Пинг ws1 с ws2 — машина не отвечает](images/image%2025.png)

- Вывод nmap для машины которая не пингуется — `Host is up`:

  - ![nmap ws2 с ws1 — Host is up](images/image%2026.png)
  - ![nmap ws1 с ws2 — Host is up](images/image%2027.png)

## Part 5. Статическая маршрутизация сети

- Схема сети:

  - ![Схема сети Part 5](images/image%2028.png)

### 5.1. Настройка адресов машин

- Содержимое `/etc/netplan/00-installer-config.yaml` для ws11 (10.10.0.2/18):

  - ![Netplan конфиг ws11](images/image%2029.png)

- Содержимое `/etc/netplan/00-installer-config.yaml` для ws21 (10.20.0.10/26):

  - ![Netplan конфиг ws21](images/image%2030.png)

- Содержимое `/etc/netplan/00-installer-config.yaml` для ws22 (10.20.0.20/26):

  - ![Netplan конфиг ws22](images/image%2031.png)

- Содержимое `/etc/netplan/00-installer-config.yaml` для r1 (10.10.0.1/18, 10.100.0.11/16):

  - ![Netplan конфиг r1](images/image%2032.png)

- Содержимое `/etc/netplan/00-installer-config.yaml` для r2 (10.100.0.12/16, 10.20.0.1/26):

  - ![Netplan конфиг r2](images/e007142e-276f-45a6-99cc-008d43704a54.png)

- Пинг ws22 с ws21 — проверка связи внутри сети 10.20.0.0/26:

  - ![Пинг ws22 с ws21](images/image%2033.png)

- Пинг r1 с ws11 — проверка связи внутри сети 10.10.0.0/18:

  - ![Пинг r1 с ws11](images/image%2034.png)

### 5.2. Включение переадресации IP-адресов

- Временное включение переадресации командой `sysctl -w net.ipv4.ip_forward=1` на r1:

  - ![sysctl ip_forward на r1](images/image%2035.png)

- Временное включение переадресации командой `sysctl -w net.ipv4.ip_forward=1` на r2:

  - ![sysctl ip_forward на r2](images/image%2036.png)

- Постоянное включение переадресации через `/etc/sysctl.conf` на r1:

  - ![/etc/sysctl.conf на r1 с net.ipv4.ip_forward = 1](images/image%2037.png)

- Постоянное включение переадресации через `/etc/sysctl.conf` на r2:

  - ![/etc/sysctl.conf на r2 с net.ipv4.ip_forward = 1](images/image%2038.png)

### 5.3. Установка маршрута по умолчанию

- Содержимое `/etc/netplan/00-installer-config.yaml` для ws11 с маршрутом по умолчанию через r1:

  - ![Netplan ws11 с default route](images/image%2039.png)

- Содержимое `/etc/netplan/00-installer-config.yaml` для ws21 с маршрутом по умолчанию через r2:

  - ![Netplan ws21 с default route](images/image%2040.png)

- Содержимое `/etc/netplan/00-installer-config.yaml` для ws22 с маршрутом по умолчанию через r2:

  - ![Netplan ws22 с default route](images/image%2041.png)

- Вывод `ip r` на ws11 — маршрут по умолчанию добавлен:

  - ![ip r на ws11](images/image%2042.png)

- Вывод `ip r` на ws21:

  - ![ip r на ws21](images/image%2043.png)

- Вывод `ip r` на ws22:

  - ![ip r на ws22](images/image%2044.png)

- Пинг r2 с ws11 и перехват трафика командой `tcpdump -tn -i enp0s8` на r2:

  - ![Пинг r2 с ws11 и tcpdump на r2](images/image%2045.png)
  - ![tcpdump на r2 — пинг доходит](images/image%2046.png)

### 5.4. Добавление статических маршрутов

- Содержимое изменённого `/etc/netplan/00-installer-config.yaml` для r1 (маршрут в 10.20.0.0/26 через r2):

  - ![Netplan r1 со статическим маршрутом в 10.20.0.0/26](images/image%2047.png)

- Содержимое изменённого `/etc/netplan/00-installer-config.yaml` для r2 (маршрут в 10.10.0.0/18 через r1):

  - ![Netplan r2 со статическим маршрутом в 10.10.0.0/18](images/image%2048.png)
  - ![Netplan r2 — продолжение](images/image%2049.png)

- Вывод `ip r` на r1 — таблица маршрутизации:

  - ![ip r на r1](images/image%2050.png)

- Вывод `ip r` на r2 — таблица маршрутизации:

  - ![ip r на r2](images/image%2051.png)

- Вывод команд `ip r list 10.10.0.0/18` и `ip r list 0.0.0.0/0` на ws11:

  - ![ip r list на ws11](images/image%2052.png)

- **Почему для 10.10.0.0/18 выбран маршрут, отличный от 0.0.0.0/0:** Используется принцип **Longest Prefix Match** — маршрутизатор выбирает наиболее специфичный маршрут, то есть с наибольшей длиной маски. Маршрут `10.10.0.0/18` имеет маску `/18`, что длиннее `/0` маршрута по умолчанию, поэтому для пакетов в локальную сеть используется прямой маршрут через интерфейс, а не шлюз.

### 5.5. Построение списка маршрутизаторов

- Запуск `tcpdump -tnv -i enp0s8` на r1 и `traceroute 10.20.0.10` на ws11:

  - ![traceroute от ws11 до ws21](images/image%2053.png)
  - ![tcpdump на r1 во время traceroute](images/image%2054.png)
  - ![tcpdump на r1 — полный вывод](images/image%2055.png)

- **Принцип работы traceroute:** Утилита отправляет UDP-пакеты с последовательно увеличивающимся значением TTL (начиная с 1). Каждый маршрутизатор уменьшает TTL на 1, и когда TTL достигает 0 — отбрасывает пакет и отправляет ICMP-сообщение `Time Exceeded` обратно отправителю. Так traceroute узнаёт адрес каждого промежуточного хопа: TTL=1 → ответ от r1, TTL=2 → ответ от r2, TTL=3 → ответ от ws21. В дампе на r1 видны UDP-пакеты от ws11 с TTL=1,2,3 и соответствующие ICMP `time exceeded` ответы.

### 5.6. Использование протокола ICMP при маршрутизации

- Перехват ICMP-трафика на r1 командой `tcpdump -n -i enp0s8 icmp` и пинг несуществующего IP с ws11 командой `ping -c 1 10.30.0.111`:

  - ![tcpdump icmp на r1 и ping несуществующего IP с ws11](images/image%2056.png)

## Part 6. Динамическая настройка IP с помощью DHCP

- Содержимое файла `/etc/dhcp/dhcpd.conf` на r2:

  - ![/etc/dhcp/dhcpd.conf на r2](images/image%2057.png)

- Содержимое файла `/etc/resolv.conf` на r2 с прописанным `nameserver 8.8.8.8`:

  - ![/etc/resolv.conf на r2](images/image%2058.png)

- Перезапуск и статус службы DHCP на r2 командой `systemctl restart isc-dhcp-server`:

  - ![systemctl restart и status isc-dhcp-server на r2](images/image%2059.png)

- Вывод `ip a` на ws21 после перезагрузки — машина получила адрес по DHCP:

  - ![ip a на ws21 — полученный DHCP адрес](images/image%2060.png)

- Пинг ws22 с ws21:

  - ![Пинг ws22 с ws21](images/image%2061.png)

- Пинг ws21 с ws22:

  - ![Пинг ws21 с ws22](images/image%2062.png)

- Содержимое `/etc/netplan/00-installer-config.yaml` на ws11 с указанием MAC-адреса `10:10:10:10:10:BA` и `dhcp4: true`:

  - ![Netplan ws11 с macaddress и dhcp4](images/image%2063.png)

- Содержимое файла `/etc/dhcp/dhcpd.conf` на r1 с жёсткой привязкой к MAC-адресу ws11:

  - ![/etc/dhcp/dhcpd.conf на r1 с MAC-привязкой](images/image%2064.png)

- Содержимое файла `/etc/resolv.conf` на r1:

  - ![/etc/resolv.conf на r1](images/image%2065.png)

- Перезапуск и статус службы DHCP на r1:

  - ![systemctl restart и status isc-dhcp-server на r1](images/image%2066.png)

- Вывод `ip a` на ws11 после перезагрузки — машина получила фиксированный адрес 10.10.0.2 по MAC-привязке:

  - ![ip a на ws11 — адрес получен по MAC-привязке](images/image%2067.png)
  - ![Перезагрузка ws11 и проверка адреса](images/image%2068.png)

- Вывод `ip a` на ws21 **до** обновления адреса:

  - ![ip a на ws21 до обновления](images/image%2069.png)

- Освобождение IP на ws21 командой `sudo dhclient -r enp0s8`:

  - ![dhclient -r на ws21 — освобождение адреса](images/image%2070.png)

- Получение нового IP на ws21 командой `sudo dhclient enp0s8` и вывод `ip a` **после** обновления:

  - ![dhclient на ws21 — получение нового адреса](images/image%2071.png)

- **Использованные опции DHCP:**
  - `dhclient -r` — опция **Release**: освобождает текущий IP-адрес, отправляет DHCP серверу сообщение о том, что адрес больше не используется
  - `dhclient` — опция **Discover**: запускает полный цикл получения нового IP-адреса (DISCOVER → OFFER → REQUEST → ACK)

## Part 7. NAT

- Содержимое изменённого файла `/etc/apache2/ports.conf` на ws22 (`Listen 0.0.0.0:80`):

  - ![ports.conf на ws22](images/image%2072.png)

- Содержимое изменённого файла `/etc/apache2/ports.conf` на r1 (`Listen 0.0.0.0:80`):

  - ![ports.conf на r1](images/image%2073.png)

- Запуск Apache командой `service apache2 start` на ws22 и r1:

  - ![service apache2 start на ws22 и r1](images/image%2074.png)
  - ![Статус Apache на ws22 и r1](images/image%2075.png)

- Содержимое файла `/etc/firewall.sh` на r2 — версия 1 (только базовые правила, FORWARD DROP):

  - ![firewall.sh на r2 версия 1](images/image%2076.png)

- Проверка: ws22 не пингуется с r1 после применения правила FORWARD DROP:

  - ![Пинг ws22 с r1 — нет ответа](images/image%2077.png)

- Содержимое файла `/etc/firewall.sh` на r2 — версия 2 (добавлено разрешение ICMP):

  - ![firewall.sh на r2 версия 2 с ICMP](images/image%2078.png)

- Проверка: ws22 пингуется с r1 после добавления правила разрешения ICMP:

  - ![Пинг ws22 с r1 — ответ есть](images/image%2079.png)

- Содержимое файла `/etc/firewall.sh` на r2 — финальная версия с SNAT и DNAT:

  - ![firewall.sh на r2 финальная версия с SNAT и DNAT](images/image%2080.png)

- Применение файла фаервола командами `chmod +x /etc/firewall.sh` и `/etc/firewall.sh`:

  - ![Запуск firewall.sh на r2](images/image%2081.png)

- Проверка SNAT: подключение с ws22 к Apache на r1 командой `telnet 10.10.0.1 80`:

  - ![telnet с ws22 к r1:80 — проверка SNAT](images/image%2082.png)

- Проверка DNAT: подключение с r1 к Apache на ws22 через r2 командой `telnet 10.100.0.12 8080`:

  - ![telnet с r1 к r2:8080 — проверка DNAT](images/image%2083.png)

## Part 8. Дополнительно. Знакомство с SSH Tunnels

- Запуск Apache на ws22 только на localhost — содержимое `/etc/apache2/ports.conf` со строкой `Listen localhost:80`:

  - ![ports.conf на ws22 с Listen localhost:80](images/image%2084.png)

- **Local TCP forwarding** с ws21 до ws22: команда `ssh -L 8080:localhost:80 bernarbe@10.20.0.20` на ws21 и проверка `telnet 127.0.0.1 8080` во втором терминале:

  - ![Local TCP forwarding с ws21 и проверка telnet](images/image%2085.png)

- **Remote TCP forwarding** с ws11 до ws22: команда `ssh -R 8080:localhost:80 bernarbe@10.20.0.20` на ws11 и проверка `telnet 127.0.0.1 8080` во втором терминале:

  - ![Remote TCP forwarding с ws11 и проверка telnet](images/image%2086.png)
