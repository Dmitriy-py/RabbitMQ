# Домашнее задание к занятию «Очереди RabbitMQ»

##  ` Дмитрий Климов `

## Задание 1. Установка RabbitMQ

### Используя Vagrant или VirtualBox, создайте виртуальную машину и установите RabbitMQ. Добавьте management plug-in и зайдите в веб-интерфейс.

Итогом выполнения домашнего задания будет приложенный скриншот веб-интерфейса RabbitMQ.

## Ответ:

![Снимок экрана (1126)](https://github.com/user-attachments/assets/513fa4b4-cb55-48cb-a2be-7aaf873d363e)

## Задание 2. Отправка и получение сообщений

### Используя приложенные скрипты, проведите тестовую отправку и получение сообщения. Для отправки сообщений необходимо запустить скрипт producer.py.

### Для работы скриптов вам необходимо установить Python версии 3 и библиотеку Pika. Также в скриптах нужно указать IP-адрес машины, на которой запущен RabbitMQ, заменив localhost на нужный IP.

$ pip install pika

Зайдите в веб-интерфейс, найдите очередь под названием hello и сделайте скриншот. После чего запустите второй скрипт consumer.py и сделайте скриншот результата выполнения скрипта

В качестве решения домашнего задания приложите оба скриншота, сделанных на этапе выполнения.

## Ответ:

### ` producer.py `

```
python

import pika

credentials = pika.PlainCredentials('myuser', 'mypassword')  # Заменить на свои учетные данные
parameters = pika.ConnectionParameters('localhost',
                                         credentials=credentials)
connection = pika.BlockingConnection(parameters)
channel = connection.channel()

channel.queue_declare(queue='hello')

channel.basic_publish(exchange='',
                      routing_key='hello',
                      body='Hello Netology!')
print(" [x] Sent 'Hello Netology!'")
connection.close()
```

### ` consumer.py `

```
import pika

credentials = pika.PlainCredentials('myuser', 'mypassword')
parameters = pika.ConnectionParameters('localhost',
                                           credentials=credentials)
connection = pika.BlockingConnection(parameters)

channel = connection.channel()

channel.queue_declare(queue='hello')

def callback(ch, method, properties, body):
    print(f" [x] Received {body.decode()}")

channel.basic_consume(queue='hello', on_message_callback=callback, auto_ack=True)

print(' [*] Waiting for messages. To exit press CTRL+C')
channel.start_consuming()
```

![Снимок экрана (1128)](https://github.com/user-attachments/assets/a954e377-4a85-4c3f-a006-cac7c428ed21)

![Снимок экрана (1129)](https://github.com/user-attachments/assets/47925613-8c86-4175-94af-eca77d922b80)

![Снимок экрана (1133)](https://github.com/user-attachments/assets/8092faa6-115f-4786-a923-1f27a1af29e5)


## Задание 3. Подготовка HA кластера

Используя Vagrant или VirtualBox, создайте вторую виртуальную машину и установите RabbitMQ. Добавьте в файл hosts название и IP-адрес каждой машины, чтобы машины могли видеть друг друга по имени.

Пример содержимого hosts файла:

$ cat /etc/hosts
192.168.0.10 rmq01
192.168.0.11 rmq02
После этого ваши машины могут пинговаться по имени.

Затем объедините две машины в кластер и создайте политику ha-all на все очереди.

В качестве решения домашнего задания приложите скриншоты из веб-интерфейса с информацией о доступных нодах в кластере и включённой политикой.

Также приложите вывод команды с двух нод:

$ rabbitmqctl cluster_status
Для закрепления материала снова запустите скрипт producer.py и приложите скриншот выполнения команды на каждой из нод:

$ rabbitmqadmin get queue='hello'
После чего попробуйте отключить одну из нод, желательно ту, к которой подключались из скрипта, затем поправьте параметры подключения в скрипте consumer.py на вторую ноду и запустите его.

Приложите скриншот результата работы второго скрипта.


## Ответ:

### ` Vagrantfile `

```
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"

  config.vm.define "rmq01" do |rmq01|
    rmq01.vm.hostname = "rmq01"
    rmq01.vm.network "private_network", ip: "192.168.56.10"
    rmq01.vm.provider "virtualbox" do |vb|
      vb.memory = "4024"
      vb.cpus = 2
    end
    rmq01.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y rabbitmq-server
      sudo rabbitmq-plugins enable rabbitmq_management
      sudo rabbitmqctl add_user admin admin
      sudo rabbitmqctl set_user_tags admin administrator
      sudo rabbitmqctl set_permissions -p / admin ".*" ".*" ".*"
    SHELL
  end

  config.vm.define "rmq02" do |rmq02|
    rmq02.vm.hostname = "rmq02"
    rmq02.vm.network "private_network", ip: "192.168.56.11"
    rmq02.vm.provider "virtualbox" do |vb|
      vb.memory = "4024"
      vb.cpus = 2
    end
    rmq02.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y rabbitmq-server
      sudo rabbitmq-plugins enable rabbitmq_management
      sudo rabbitmqctl add_user admin admin
      sudo rabbitmqctl set_user_tags admin administrator
      sudo rabbitmqctl set_permissions -p / admin ".*" ".*" ".*"
    SHELL
  end

end
```

![Снимок экрана (1136)](https://github.com/user-attachments/assets/cc608539-008b-4825-8f7e-046d19778669)

![Снимок экрана (1138)](https://github.com/user-attachments/assets/4464cbb3-26de-4ce5-9dde-d675512eae7b)

![Снимок экрана (1140)](https://github.com/user-attachments/assets/feb44921-7744-498a-9fbd-9d9f282d625c)

![Снимок экрана (1139)](https://github.com/user-attachments/assets/31c0de6d-bd25-4bb1-b790-2f49b52e81b9)

![Снимок экрана (1141)](https://github.com/user-attachments/assets/3392520c-54b8-4e99-ab2a-16c631442f76)

![Снимок экрана (1144)](https://github.com/user-attachments/assets/aff95e7e-7525-46f3-b4c0-5266256032c3)

![Снимок экрана (1145)](https://github.com/user-attachments/assets/9929a749-2830-405a-981d-b411307953f2)
