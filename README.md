### Задача:
«Куберизировать» приложение Flask+Redis из предыдущей практики и обновить
1. Разворачиваем на vm мини-кластер k8s - minikube (Предварительно увеличиваем ресурсы vm)
2. (опционально: Подключаемся к кластеру с хостовой машины через приложение Lens)
3. Собираем образы будущих контейнеров и прокидываем их внутрь minikube
4. Создаем манифесты – описания приложений и сервисов
5. Разворачиваем deployment/ReplicaSet с application-серверами и БД и сервисы
6. Активируем балансировщик входящих запросов (служба LoadBalancer)
7. Проверяем работу сервисов
8. Обновляем веб сервисы на новую версию (RollingUpdate)


### Ход выполнения:
1. Установка minikube на Ubuntu 24.04

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
sudo dpkg -i minikube_latest_amd64.deb
sudo usermod -aG docker user && newgrp docker
sudo swapoff -a
minikube start --vm-driver=docker
```

![k8s_1](https://github.com/user-attachments/assets/0d73138c-c1bb-4c92-b63e-1992ffb57af9)


2. Проверка minikube и k8s кластера на вм

![k8s_2](https://github.com/user-attachments/assets/40098400-470c-4b68-9c04-bd759f99cbb5)
![k8s_3](https://github.com/user-attachments/assets/88dfb0f9-a173-4b36-b412-8f4679ee4a93)

3. Итоговая структура каталогов

![k8s_4](https://github.com/user-attachments/assets/840dbacc-86e4-4d5d-a26e-8ec722880984)

4. Модифицируем приложение Flask

![k8s_5](https://github.com/user-attachments/assets/78a6476c-be69-4ef4-b415-95af1a8f4bb8)

5. Готовим образы контейнеров и делаем их доступными в кластере k8s

```
minikube image build -t flask:v1 flask_redis/
minikube image load redis:alpine
minikube image ls
```

6. Готовим описания (манифесты) для каждого сервиса - Flask

![k8s_6](https://github.com/user-attachments/assets/07047c6c-dad9-49d3-9ea8-c5532f23df77)

7. Готовим описания (манифесты) для каждого сервиса - Flask-SERVICE

![k8s_7](https://github.com/user-attachments/assets/9db877c8-a5f4-41f8-b1ce-5630bc62c1e5)

8. Готовим описания (манифесты) для каждого сервиса - Redis

![k8s_8](https://github.com/user-attachments/assets/20bc6f68-5fa7-4003-a452-47c3cd4dad4f)

9. Готовим описания (манифесты) для каждого сервиса - Redis-SERVICE

![k8s_9](https://github.com/user-attachments/assets/d678f9fe-4323-40a4-a02a-36ebad0e5994)

10. Применяем манифесты k8s

![k8s_10](https://github.com/user-attachments/assets/4fed1860-ed17-4c99-acb2-bf6a498ea6e3)
![k8s_11](https://github.com/user-attachments/assets/9158dbd2-e053-4ce6-9068-ea0892b3014a)


11. Обновляем приложение на новую версию

![k8s_12](https://github.com/user-attachments/assets/9189770e-2ddc-4795-b6f7-9e3e915e2180)

12. Обновляем поды на новую версию

![image](https://github.com/user-attachments/assets/d802bcc5-46cc-40ac-b45e-029bd2dadbb4)

13. Наблюдаем, как пересоздаются реплики подов на новую версию

![k8s_13](https://github.com/user-attachments/assets/02fcd17c-4037-49e3-80b1-eade934b1109)
![k8s_14](https://github.com/user-attachments/assets/94ab7d6e-190b-4410-9a1b-45a56e19edd2)
