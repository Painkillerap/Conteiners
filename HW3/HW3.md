# Домашняя работа №3
## Введение в Docker
### Установка Docker

1. Обновляем список и версию пакетов в системе;
    ```
    apt update 
    apt upgrade
    ```
    ![apt update](Scrin/Skrin1.png)

2.  Устанавливаем необходимые пакеты для использования репозитория по HTTPS;
    ```
    apt install apt-transport-https ca-certificates curl software-properties-common
    ```

3. Загружаем официальный GPG-ключ Docker и добавляем его в систему;
    ```
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    ```


4. Добавляем репозиторий Docker в список источников пакетов в системе;
    ```
    echo "deb [signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    ```

5. Обновляем список пакетов, чтобы включить информацию о пакетах Docker из добавленного репозитория;
    ```
    apt update
    ```
    ![](Scrin/Skrin2.png)
    ![](Scrin/Skrin3.png)

6. Устанавливаем Docker;
    ```
    apt install docker-ce
    ```
    ![](Scrin/Skrin4.png)
    ![](Scrin/Skrin5.png)

7. Добавляем текущего пользователя в группу docker, чтобы избежать использования sudo для запуска Docker команд;
    ```
    sudo usermod -aG docker $USER
    ```

8. Перезагружаем группы пользователя, чтобы изменения в группе docker вступили в силу;
    ```
    newgrp docker
    ```

9. Проверяем версию установленного Docker.
    ```
    docker --version
    ```

### Тестирование контейнера с использованием образа "cowsay"

1. Запускаем контейнер с использованием образа "cowsay";
    ```
    docker run docker/whalesay cowsay Hello, Docker!
    ```
    ![](Scrin/Skrin6.png)


### Хранение данных в контейнерах Docker: Руководство с пояснениями

1. Запускаем контейнер из образа Ubuntu и входим в него;
    ```
    docker run -it -h GB --name gb-test ubuntu:22.10
    ```

2. Смотрим содержимое корневой директории;
    ```
    ls -l
    ```

3. Создадем новую директорию в корне:
    ```
    mkdir /example
    ```

4. Создаем файл "passwords.txt";
    ```
    touch /example/passwords.txt
    ```

5. Добавляем в него какие-либо данные.
    ```
    echo "123test" >> /example/passwords.txt
    ```

6. Перейдем в директорию example;
    ```
    cd example/
    ```

7. Просмотрим содержимое файла passwords.txt;
    ```
    cat passwords.txt
    ```

8. Выходим из контейнера;
    ```
    exit
    ```

9. Останавливаем контейнер;
    ```
    docker stop gb-test
    ```

10. Запускаем контейнер;
    ```
    docker start gb-test
    ```

11. Выполняем команду bash внутри контейнера;
    ```
    docker exec -it gb-test bash
    ```

12. Просмотрим содержимое файла passwords.txt;
    ```
    cat /example/passwords.txt
    ```
    Наши данные сохранятся, так как мы не пересоздавали контейнер.

13. Выходим из контейнера;
    ```
    exit
    ```

14. Останавливаем контейнер;
    ```
    docker stop gb-test
    ```

15. Удалаяем контейнер;
    ```
    docker rm gb-test
    ```

16. Запускаем контейнер из образа Ubuntu и входим в него;
    ```
    docker run -it -h GB --name gb-test ubuntu:22.10
    ```

17. Просмотрим содержимое файла passwords.txt;
    ```
    cat /example/passwords.txt
    ```
    ![](Scrin/Skrin7.png)
    В этот раз наши данные будут утеряны, так как контейнер был удален.

### Использование внешнего хранилища

1. Создаем директорию;
    ```
    mkdir testfolder
    ```

2. Монтируем еее к контейнеру;
    ```
    docker run -it -h GB --name gb-test -v /home/al/testfolder:/otherway ubuntu:22.10
    ```

3. Добавляем данные в подмонтированную директорию;
    ```
    echo "$HOSTNAME" >> /otherway/test.txt
    ```

4. Просмотрим содержимое файла test.txt в контейнере;
    ```
    cat otherway/test.txt
    ```

5. Выходим из контейнера;
    ```
    exit
    ```

6. Проверяем доступность данных с локальной системы;
    ```
    cat testfolder/test.txt
    ```

7. Удаляем контейнер;
    ```
    docker rm gb-test
    ```

8. Создаем его снова, подмонтировав директорию;
    ```
    docker run -it -h GB --name gb-test -v /home/al/testfolder:/otherway ubuntu:22.10
    ```

9. Просмотрим содержимое файла test.txt в контейнере;
    ```
    cat otherway/test.txt
    ```
    ![](Scrin/Skrin8.png)
    Данные по-прежнему доступны. Самый надежный способ хранения данных в контейнерах - использование внешних хранилищ. Важно избегать хранения важных данных внутри контейнеров, чтобы предотвратить потерю информации.

### Часть-2 Хранение данных в контейнерах Docker: Практическое руководство

1. Создаём папку, которую мы будем готовы смонтировать в контейнер.В этой папке создаём файл test.txt и наполняем его данными. Создаём вторую такую же директорию с таким же файлом но с другим наполнением.Создаём контейнер из образа ubuntu:22.10 и задайте ему имя и hostname монтируем ранее созданную папку с хоста в контейнер,смотрим содержимое, выходим из контейнера, останавливаем и удаляем его;
    ```
    mkdir -p /root/docker-mount-example/
    touch ~/docker-mount-example/test.txt
    echo "This is the host test.txt file" > ~/docker-mount-example/test.txt
    mkdir -p /root/docker-mount-example2/
    touch ~/docker-mount-example2/test.txt
    echo "This is the root test.txt file" > ~/docker-mount-example2/test.txt
    docker run -it -h GB --name gb-test -v ~/docker-mount-example:/container-mount ubuntu:22.10
    cat /container-mount/test.txt
    exit
    docker stop gb-test && docker rm gb-test
    ```

2. Монтируем созданный ранее текстовый файл из домашней директории внутрь смонтированной папки в контейнере и перемонтируем в контейнер другой текстовый файл из другой хостовой директории. Выводим результат на экран;
    ```
    docker run -it -h GB --name gb-test -v ~/docker-mount-example:/container-mount -v ~/docker-mount-example2/test.txt:/container-mount/test.txt ubuntu:22.10
    cat /container-mount/test.txt
    ```
    ![](Scrin/Skrin9.png)
    Мы создали контейнер и монтировали папку docker-mount-example внутрь контейнера. Затем мы монтировали файл test.txt из домашней директории внутрь этой папки в контейнере. При просмотре содержимого файла в контейнере, мы видим данные из файла в домашней директории.