## Темы для дискуссий

### План

* В архив с прошивкой класть хеш-сумму, для проверки целостности перед прошивкой
  * Готово @p0isk
* В фаил /etc/os-release и переменную GITHUB_VERSION писать имя бранча+хэш и дату, как сделано в Majestic
  * Готово @p0isk
* Процессор и имя профиля указываются автоматически при сборке в файле /etc/hostname и доступны постоянно через /rom/..
  * У плат ..._${platform}_unknown_defconfig hostname не прописан. ..._gk7205v200_fpv_defconfig имеет отличный hostname @p0isk
* Добавить ключи в sysupgrade для обновления web-ui и majestic


### Безопасность

* При первых входах по SSH и WEB предлагать (настойчиво) пользователю сменить пароль, дабы не нарваться на CVE.
  * По вебу сделано themactep @p0isk
* Реализовать интеграцию авторизации httpd на использование стандартных passwd/shadow из /etc

### Унификация ядра

* Включить опции ROOT_NFS и PNP_DHCP во всех ядрах

### Обновление системы

#### Ядро:
* Добавить через mkimage имя процессора, например Linux-4.9.37-hi3516ev200
  * Готово @p0isk
* По нему проверять и дате проверять пригодность для обновления.
  * Готово @p0isk

### Обновление majestic
* Обновляются и проверяются только бинарник и укороченный конфиг.
* Есть кнопка Восстановить настройки, нужна ли Восстановить стример?
* Помимо ETag можно использовать Last-Modified

### Обновление web-интерфейса
* Перезаписывать только те файлы, которые изменились.
  * Сделано themactep @p0isk
* Удалять файлы, которые больше не используются в интерфейсе из /var/www или оверлея?
  * Сделано themactep @p0isk

#### Ветка разработки
* Отображать поле для номера коммита. Если пусто, то берём последний.
  * Неактуально? @p0isk