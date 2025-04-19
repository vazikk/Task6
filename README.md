# Task6
1) Вывод CPU в реалтайм
   ```
   #!/bin/bash
   while true; do
    echo $(top -bn1 | grep "Cpu(s)" | sed "s/.*, *\([0-9.]*\)%* id.*/\1/" | awk '{print 100 - $1}') > /var/www/script/cpu.txt
     sleep 1
   done

   ```
   ![Image alt](https://github.com/vazikk/Task6/blob/main/image1.png) <br>

_____________________________________________
_____________________________________________

2) Создание демона который будет писать в файл логи, очищать файл придостижение какого-то объёма, записывать лог об удачных очитсках и распределение отдльеных логов по другим файлам.<br>
Демон: <br>
```
[Unit]
Description=NGINX Logger Service
After=network.target

[Service]
ExecStart=/var/www/script/2.sh
Restart=always
User=www-data
StandardOutput=syslog
StandardError=syslog

[Install]
WantedBy=multi-user.target
```
Скрипт: <br>
```
#!/bin/bash
ACCESS="/var/log/nginx/access.log"
ERROR="/var/log/nginx/error.log"
MAX=1000000
CLEAN="/var/log/nginx/clean.log"
CUST="/var/log/nginx/demon.log"
five="/var/log/nginx/500.log"
four="/var/log/nginx/400.log"

while true; do
        tail -n 0 -F  "$ACCESS" >> "$CUST" &
        tail -n 0 -F  "$ERROR" >> "$CUST" &

        if [[ -f "$CUST" && $(stat -c%s "$CUST") -gt $MAX ]]; then
        echo "$(date) - Файл очищен: $CUST" >> "$CLEAN"
                 > "$CUST"  # Очистка файла
        fi

        tail -n 0 -F "$CUST" | awk '$9 ~ /^4[0-9][0-9]$/ { print }' >> "$four"

        tail -n 0 -F "$CUST" | awk '$9 ~ /^5[0-9][0-9]$/ { print }' >> "$five"

    sleep 5
done
```
<br>
добавление логов: <br>

   ![Image alt](https://github.com/vazikk/Task6/blob/main/image2.png) <br>
   ![Image alt](https://github.com/vazikk/Task6/blob/main/image3.png) <br>
Отчситка и запись: <br>
   ![Image alt](https://github.com/vazikk/Task6/blob/main/image4.png) <br>
   ![Image alt](https://github.com/vazikk/Task6/blob/main/image5.png) <br>

Раскидывание логов: <br>
   ![Image alt](https://github.com/vazikk/Task6/blob/main/image6.png) <br>

