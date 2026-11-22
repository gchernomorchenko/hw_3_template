4балла
Собрал образ ipr-3 из Dockerfile, проверил логи через docker logs ipr-3
2026-11-22 17:11:54,558 listening port=8000 storage=sqlite
2026-11-22 17:13:40,813 http 172.17.0.1 "GET / HTTP/1.1" 200 -
2026-11-22 17:14:27,342 http 172.17.0.1 "POST /increment HTTP/1.1" 200 -
2026-11-22 17:14:53,128 http 172.17.0.1 "GET /readyz HTTP/1.1" 200 -

6баллов
Настроил compose.yaml опубликовал порт под APPPORT 127.0.0.1:8080:8000, сделал приветствие greetings GREETINGS, 
добавил example env 
через curl -fsS http://127.0.0.1:8080/readyz; получил
{"status": "ready", "storage": "sqlite"}
curl -fsS -X POST http://127.0.0.1:8080/readyz
{"greeting": "Hello from Compose!", "count": 1, "hostname": "32516bb61d73", "storage": "sqlite"}
через compose down и compose up -d проверил, что счетчик сохранился, хотя hostname другой
{"greeting": "Hello from Compose!", "count": 1, "hostname": "b24e2c7d4738", "storage": "sqlite"}

8баллов
Задал STORAGE=redis и REDIS_URL=redis://redis:6379/0, подключил именованный volume к /data сервиса Redis и включил запись, добавил healthcheck обоих сервисов
после POST /increment  count был 2, после compose down и compose up  -d через GET снова 2
выполнил compose stop redis. GET /healthz вернул 200, а GET /readyz и POST /increment — вернул 503
 curl -fsS -X POST http://127.0.0.1:8080/increment; echo
curl: (22) The requested URL returned error: 503
curl -i -s http://127.0.0.1:8080/healthz; echo
HTTP/1.0 200 OK
после повторного перезапуска стало curl -fsS -X POST http://127.0.0.1:8080/increment; echo
{"greeting": "Hello from Compose!", "count": 3, "hostname": "dd4018c7b701", "storage": "redis"}
