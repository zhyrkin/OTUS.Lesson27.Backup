# OTUS.Lesson27.Backup

Домашнее задание:
1) Настроить удаленный бэкап каталога /etc c сервера client при помощи borgbackup. Резервные копии должны соответствовать следующим критериям:  
- директория для резервных копий /var/backup. Это должна быть отдельная точка монтирования. В данном случае для демонстрации размер не принципиален, достаточно будет и 2GB; (Студент самостоятельно настраивает)  
- репозиторий для резервных копий должен быть зашифрован ключом или паролем - на усмотрение студента;  
- имя бэкапа должно содержать информацию о времени снятия бекапа;  
- глубина бекапа должна быть год, хранить можно по последней копии на конец месяца, кроме последних трех. Последние три месяца должны содержать копии на каждый день. Т.е. должна быть правильно настроена политика удаления старых бэкапов;  
- резервная копия снимается каждые 5 минут. Такой частый запуск в целях демонстрации;  
- написан скрипт для снятия резервных копий. Скрипт запускается из соответствующей Cron джобы, либо systemd timer-а - на усмотрение студента;  
- настроено логирование процесса бекапа. Для упрощения можно весь вывод перенаправлять в logger с соответствующим тегом. Если настроите не в syslog, то обязательна ротация логов.

Тестовый стенд:
 2 VM (2CPU, 2Gb RAM, 6Gb HDD + 2Gb HDD на VM server, OS Debain12 )

Замечания к работе:
1) Я никак не смог автоматизировать инициализацию репозитория с клиента с помощью плейбука,   
borg ни в какую не хочет использовать переменные окружения для команды borg init. Ни средствами ansible, даже если их задать через cli, всегда надо ввести BORG_PASSPHRASE.
Поэтому последняя таска завершится с ошибкой и после выполнения плейбука необходимо руками инициализировать репозиторий 
    borg init --encryption=repokey borg@{{ server_ip }}:/var/backup/client


2) Логгирование процесса или причины неудач отлично отображается в журнале, поэтому не вижу смысла логгировать дополнительно процесс в syslog  
мар 13 19:49:45 deb12 systemd[1]: Starting borg-backup.service - Borg Backup...  
░░ Subject: Начинается запуск юнита borg-backup.service  
░░ Defined-By: systemd  
░░ Support: https://www.debian.org/support  
░░   
░░ Начат процесс запуска юнита borg-backup.service.  
мар 13 19:49:48 deb12 borg[12641]: ------------------------------------------------------------------------------  
мар 13 19:49:48 deb12 borg[12641]: Repository: ssh://borg@10.200.3.95/var/backup/client  
мар 13 19:49:48 deb12 borg[12641]: Archive name: etc-2025-03-13_19:49:45  
мар 13 19:49:48 deb12 borg[12641]: Archive fingerprint: fcd36df82c4ba6902fe7c74b726657edadec7c497de302ba8e6a7d577a8c12a1  
мар 13 19:49:48 deb12 borg[12641]: Time (start): Thu, 2025-03-13 19:49:46  
мар 13 19:49:48 deb12 borg[12641]: Time (end):   Thu, 2025-03-13 19:49:47  
мар 13 19:49:48 deb12 borg[12641]: Duration: 0.83 seconds  
мар 13 19:49:48 deb12 borg[12641]: Number of files: 504  
мар 13 19:49:48 deb12 borg[12641]: Utilization of max. archive size: 0%  
мар 13 19:49:48 deb12 borg[12641]: ------------------------------------------------------------------------------  
мар 13 19:49:48 deb12 borg[12641]:                        Original size      Compressed size    Deduplicated size  
мар 13 19:49:48 deb12 borg[12641]: This archive:                2.07 MB            928.62 kB            926.20 kB  
мар 13 19:49:48 deb12 borg[12641]: All archives:                2.06 MB            928.00 kB            980.44 kB  
мар 13 19:49:48 deb12 borg[12641]:                        Unique chunks         Total chunks  
мар 13 19:49:48 deb12 borg[12641]: Chunk index:                     486                  496  
мар 13 19:49:48 deb12 borg[12641]: ------------------------------------------------------------------------------  
мар 13 19:49:52 deb12 systemd[1]: borg-backup.service: Deactivated successfully.  
░░ Subject: Unit succeeded  
░░ Defined-By: systemd  
░░ Support: https://www.debian.org/support  
░░   
░░ The unit borg-backup.service has successfully entered the 'dead' state.  
мар 13 19:49:52 deb12 systemd[1]: Finished borg-backup.service - Borg Backup.  
░░ Subject: Запуск юнита borg-backup.service завершен  
░░ Defined-By: systemd  
░░ Support: https://www.debian.org/support  
░░   
░░ Процесс запуска юнита borg-backup.service был завершен.  
░░   
░░ Результат: done.  
мар 13 19:49:52 deb12 systemd[1]: borg-backup.service: Consumed 3.935s CPU time.  
░░ Subject: Потребленные юнитом ресурсы  
░░ Defined-By: systemd  
░░ Support: https://www.debian.org/support  
░░   
░░ Юнит borg-backup.service завершен. Приводится статистика по потребленным им ресурсам.  
