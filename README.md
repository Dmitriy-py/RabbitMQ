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



