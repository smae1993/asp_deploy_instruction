# asp_deploy_instruction

## for ubuntu

first create a service:
```
sudo nano /etc/systemd/system/YourApp.service
```

with content:
```
[Unit]
Description=YourAppName
After=network.target

[Service]
WorkingDirectory=/www/wwwroot/yourAppReleaseFolder
ExecStart=/usr/bin/dotnet /www/wwwroot/youtAppReleaseFolder/YourApp.dll
Restart=always
RestartSec=10
SyslogIdentifier=YourApp
User=www
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false
Environment=ASPNETCORE_URLS=http://0.0.0.0:1111 <- your port


[Install]
WantedBy=multi-user.target
```


after run these commands:
```
sudo systemctl daemon-reload
sudo systemctl enable YourApp
sudo systemctl start YourApp
```

for checking service run this command:
```
journalctl -u YourApp -f
```

and then set nginx config for reverse proxy:
```
location / {
    proxy_pass http://127.0.0.1:1111;  <- your port
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection keep-alive;
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
}
```

for editing service:
```
sudo systemctl edit YourApp.service
```

for restarting service:
```
sudo systemctl daemon-reload
sudo systemctl restart YourApp
```


for database migration create a script and run it in database:
```
dotnet ef migrations script -o update.sql
```
