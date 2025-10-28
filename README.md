# -SSH-

# Практические кейсы по работе с SSH

## Кейс 1. Установка и настройка SSH-сервера

### 1. Создание файла:
```bash
nano setup_ssh.sh
```

### 2. Основной скрипт:
```bash
#!/bin/bash

# Установка SSH-сервера
sudo apt update
sudo apt install openssh-server -y

# Запуск и настройка автозапуска
sudo systemctl start ssh
sudo systemctl enable ssh

# Проверка статуса
echo "=== Статус SSH-сервера ==="
sudo systemctl status ssh

echo "=== Проверка порта ==="
sudo ss -tlnp | grep :22
```

### 3. Запуск:
```bash
chmod +x setup_ssh.sh
./setup_ssh.sh
```

### Проверка результата:
```bash
sudo systemctl status ssh
```
**Вывод `"active (running)"`** - SSH-сервер работает.

---

## Кейс 2. Подключение к удаленному серверу

### 1. Создание файла:
```bash
nano connect_ssh.sh
```

### 2. Основной скрипт:
```bash
#!/bin/bash

# Подключение к удаленному серверу по SSH
echo "=== Подключение к удаленному серверу ==="

# Запрос данных для подключения
read -p "Введите имя пользователя: " username
read -p "Введите IP-адрес сервера: " ip_address

# Подключение по SSH
echo "Подключаемся к $username@$ip_address..."
ssh $username@$ip_address
```

### 3. Запуск:
```bash
chmod +x connect_ssh.sh
./connect_ssh.sh
```

### Пример:
```bash
ssh user@192.168.1.180
```

### Проверка результата:
Вывод - приглашение командной строки удаленного сервера.

---

## Кейс 3. Копирование файлов с помощью SCP

### 1. Создание файла:
```bash
nano scp_copy.sh
```

### 2. Основной скрипт:
```bash
#!/bin/bash

# Копирование файлов с помощью SCP
echo "=== Копирование файлов через SCP ==="

# Создаем тестовый файл
echo "Создаем тестовый файл..."
cat > test_file.txt << EOF
Кто мечтает быть пилотом,
Очень смелый, видно, тот.
Потому что только смелый
Сам полезет в самолёт.
Потому что только смелых
Уважает высота,
Потому что в самолёте
Всё зависит от винта.
EOF

echo "Файл test_file.txt создан"

# Запрос данных для подключения
read -p "Введите имя пользователя: " username
read -p "Введите IP-адрес сервера: " ip_address

# Копируем файл на удаленный сервер
echo "Копируем файл на удаленный сервер..."
scp test_file.txt $username@$ip_address:/tmp/

# Копируем файл обратно на локальную машину
echo "Копируем файл обратно на локальную машину..."
scp $username@$ip_address:/tmp/test_file.txt test_file_back.txt

# Проверяем результат
echo "=== Проверка результата ==="
echo "Содержимое скопированного файла:"
cat test_file_back.txt
```

### 3. Запуск:
```bash
chmod +x scp_copy.sh
./scp_copy.sh
```

### Проверка результата:
- Файл `test_file_back.txt` будет создан на локальной машине
- Содержимое должно совпадать с оригинальным стихотворением

---

## Кейс 4. Работа с ключами SSH

### 1. Создание файла:
```bash
nano ssh_keys.sh
```

### 2. Основной скрипт:
```bash
#!/bin/bash

# Работа с ключами SSH
echo "=== Генерация и настройка SSH-ключей ==="

# Генерация пары ключей
echo "1. Генерируем пару SSH-ключей..."
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N ""

# Запрос данных для подключения
read -p "Введите имя пользователя: " username
read -p "Введите IP-адрес сервера: " ip_address

# Копируем публичный ключ на сервер
echo "2. Копируем публичный ключ на удаленный сервер..."
ssh-copy-id $username@$ip_address

# Подключаемся без пароля
echo "3. Подключаемся к серверу без пароля..."
ssh $username@$ip_address "echo 'Успешное подключение без пароля!'"
```

### 3. Запуск:
```bash
chmod +x ssh_keys.sh
./ssh_keys.sh
```

### Проверка результата:
- При подключении по SSH пароль запрашиваться не должен
- Ключи будут созданы в папке `~/.ssh/`

---

## Кейс 5. Настройка файла конфигурации SSH

### 1. Создание файла:
```bash
nano ssh_config.sh
```

### 2. Основной скрипт:
```bash
#!/bin/bash

# Настройка файла конфигурации SSH
echo "=== Настройка SSH конфигурации ==="

# Создаем папку .ssh если её нет
mkdir -p ~/.ssh
chmod 700 ~/.ssh

# Создаем файл конфигурации
echo "1. Создаем файл конфигурации ~/.ssh/config..."
touch ~/.ssh/config
chmod 600 ~/.ssh/config

# Запрос данных для подключения
read -p "Введите имя пользователя: " username
read -p "Введите IP-адрес сервера: " ip_address
read -p "Введите алиас для сервера (например: myserver): " alias

# Редактируем файл конфигурации
echo "2. Редактируем файл конфигурации..."
cat >> ~/.ssh/config << EOF

Host $alias
    HostName $ip_address
    User $username
    Port 22
    IdentityFile ~/.ssh/id_rsa
EOF

echo "3. Подключаемся с использованием алиаса..."
ssh $alias "echo 'Успешное подключение через алиас $alias!'"
```

### 3. Запуск:
```bash
chmod +x ssh_config.sh
./ssh_config.sh
```

### Проверка результата:
```bash
# Просмотр конфигурации
cat ~/.ssh/config

# Подключение по алиасу
ssh алиас_сервера
```

---

## Кейс 6. Перенаправление портов (Port Forwarding)

### 1. Создание файла:
```bash
nano port_forwarding.sh
```

### 2. Основной скрипт:
```bash
#!/bin/bash

# Перенаправление портов (Port Forwarding)
echo "=== Настройка перенаправления портов ==="

# Запрос данных для подключения
read -p "Введите имя пользователя: " username
read -p "Введите IP-адрес сервера: " ip_address
read -p "Введите локальный порт: " local_port
read -p "Введите удаленный порт: " remote_port

# Настраиваем перенаправление портов
echo "1. Настраиваем перенаправление порта $local_port на $ip_address:$remote_port..."
echo "Для остановки нажмите Ctrl+C"

# Запускаем перенаправление портов
ssh -L $local_port:localhost:$remote_port $username@$ip_address
```

### 3. Запуск:
```bash
chmod +x port_forwarding.sh
./port_forwarding.sh
```

---

## Кейс 7. Управление сеансами SSH с помощью tmux/screen

### 1. Создание файла:
```bash
nano tmux_setup.sh
```

### 2. Основной скрипт:
```bash
#!/bin/bash

# Управление сеансами SSH с помощью tmux
echo "=== Установка и настройка tmux ==="

# Запрос данных для подключения
read -p "Введите имя пользователя: " username
read -p "Введите IP-адрес сервера: " ip_address

# Устанавливаем tmux на удаленном сервере
echo "1. Устанавливаем tmux на удаленном сервере..."
ssh $username@$ip_address "sudo apt update && sudo apt install tmux -y"

# Создаем сеанс tmux и запускаем долгосрочную задачу
echo "2. Создаем сеанс tmux с долгосрочной задачей..."
ssh $username@$ip_address "tmux new-session -d -s mysession 'while true; do echo \"Сеанс активен: \$(date)\" >> /tmp/long_task.log; sleep 10; done'"

# Проверяем создание сеанса
echo "3. Проверяем создание сеанса..."
ssh $username@$ip_address "tmux list-sessions"

# Показываем как подключиться к сеансу
echo "4. Для подключения к сеансу выполните:"
echo "   ssh $username@$ip_address"
echo "   tmux attach-session -t mysession"

# Проверяем работу задачи
echo "5. Проверяем работу долгосрочной задачи..."
ssh $username@$ip_address "tail -5 /tmp/long_task.log"
```

### 3. Запуск:
```bash
chmod +x tmux_setup.sh
./tmux_setup.sh
```

### Основные команды tmux:
```bash
# Подключиться к сеансу
tmux attach-session -t mysession

# Отключиться от сеанса (Ctrl+B, затем D)
# Список сеансов
tmux list-sessions

# Создать новый сеанс
tmux new-session -s имя_сеанса

# Убить сеанс
tmux kill-session -t имя_сеанса
```

### Проверка результата:
- Сеанс tmux создан и работает в фоне
- Задача продолжает работать после отключения от SSH
- Можно переподключиться и увидеть работающую задачу

---

## Кейс 8. Безопасность SSH

### 1. Создание файла:
```bash
nano ssh_security.sh
```

### 2. Основной скрипт:
```bash
#!/bin/bash

# Настройка безопасности SSH
echo "=== Настройка безопасности SSH ==="

# Создаем резервную копию конфигурации
echo "1. Создаем резервную копию конфигурации..."
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup

# Запрос параметров безопасности
read -p "Введите новый порт для SSH (например: 2222): " ssh_port
read -p "Введите разрешенные IP-адреса (например: 192.168.1.0/24): " allowed_ips

# Настраиваем новый конфигурационный файл
echo "2. Настраиваем параметры безопасности..."
sudo tee /etc/ssh/sshd_config > /dev/null << EOF
# Безопасная конфигурация SSH
Port $ssh_port
Protocol 2
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
AllowUsers *@$allowed_ips
ClientAliveInterval 300
ClientAliveCountMax 2
MaxAuthTries 3
LoginGraceTime 60
EOF

# Перезапускаем SSH сервер
echo "3. Перезапускаем SSH сервер..."
sudo systemctl restart ssh

# Настраиваем firewall
echo "4. Настраиваем firewall..."
sudo ufw allow $ssh_port
sudo ufw deny 22

# Проверяем статус
echo "5. Проверяем статус SSH сервера..."
sudo systemctl status ssh

echo "6. Новый порт SSH: $ssh_port"
echo "7. Разрешенные IP: $allowed_ips"
echo "8. Вход по паролю: ЗАПРЕЩЕН"
echo "9. Вход по ключам: РАЗРЕШЕН"
```

### 3. Запуск:
```bash
chmod +x ssh_security.sh
./ssh_security.sh
```

### Проверка результата:
```bash
# Подключение с указанием порта
ssh user@ip_address -p 2222

# Проверить конфигурацию
sudo sshd -t

# Посмотреть открытые порты
sudo ss -tlnp | grep 2222
```

---

## Кейс 9. Установка и использование SSH-агента

### 1. Создание файла:
```bash
nano ssh_agent.sh
```

### 2. Основной скрипт:
```bash
#!/bin/bash

# Настройка и использование SSH-агента
echo "=== Настройка SSH-агента ==="

# Запускаем SSH-агент
echo "1. Запускаем SSH-агент..."
eval "$(ssh-agent -s)"

# Добавляем ключи в SSH-агент
echo "2. Добавляем ключи в SSH-агент..."
ssh-add ~/.ssh/id_rsa

# Проверяем добавленные ключи
echo "3. Проверяем добавленные ключи..."
ssh-add -l

# Запрос данных для подключения
read -p "Введите имя пользователя: " username
read -p "Введите IP-адрес сервера: " ip_address

# Подключаемся с использованием SSH-агента
echo "4. Подключаемся к серверу с использованием SSH-агента..."
ssh $username@$ip_address "echo 'Успешное подключение через SSH-агент!'"

# Показываем команды для ручного управления
echo "5. Команды для управления SSH-агентом:"
echo "   ssh-agent -s          # Запустить агент"
echo "   ssh-add ~/.ssh/id_rsa # Добавить ключ"
echo "   ssh-add -l            # Список ключей"
echo "   ssh-add -D            # Удалить все ключи"
```

### 3. Запуск:
```bash
chmod +x ssh_agent.sh
./ssh_agent.sh
```

### Проверка результата:
```bash
# Проверить что агент работает
echo "$SSH_AGENT_PID"

# Проверить добавленные ключи
ssh-add -l

# Подключиться без ввода пароля ключа
ssh user@ip_address
```

---

## Кейс 10. Создание и использование бастион-сервера

### 1. Создание файла:
```bash
nano bastion_setup.sh
```

### 2. Основной скрипт:
```bash
#!/bin/bash

# Настройка бастион-сервера
echo "=== Настройка бастион-сервера ==="

# Запрос данных для подключения
read -p "Введите имя пользователя на бастион-сервере: " bastion_user
read -p "Введите IP-адрес бастион-сервера: " bastion_ip
read -p "Введите имя пользователя на целевом сервере: " target_user
read -p "Введите IP-адрес целевого сервера: " target_ip

# Генерируем ключи если их нет
echo "1. Проверяем наличие SSH-ключей..."
if [ ! -f ~/.ssh/id_rsa ]; then
    ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N ""
fi

# Копируем ключ на бастион-сервер
echo "2. Копируем ключ на бастион-сервер..."
ssh-copy-id $bastion_user@$bastion_ip

# Копируем ключ с бастиона на целевой сервер
echo "3. Копируем ключ на целевой сервер через бастион..."
ssh $bastion_user@$bastion_ip "ssh-copy-id $target_user@$target_ip"

# Подключаемся к целевому серверу через бастион
echo "4. Подключаемся к целевому серверу через бастион..."
ssh -J $bastion_user@$bastion_ip $target_user@$target_ip "echo 'Успешное подключение через бастион-сервер!'"

# Показываем команды для ручного подключения
echo "5. Команда для подключения через бастион:"
echo "   ssh -J $bastion_user@$bastion_ip $target_user@$target_ip"
```

### 3. Запуск:
```bash
chmod +x bastion_setup.sh
./bastion_setup.sh
```

### Настройка в ~/.ssh/config:
```bash
Host bastion
    HostName bastion_ip
    User bastion_user

Host internal_server
    HostName target_ip
    User target_user
    ProxyJump bastion
```

### Проверка результата:
```bash
# Подключение через конфиг
ssh internal_server
```
```
