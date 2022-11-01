```
docker run -d --name bt \
-e SIGNUPS_ALLOWED=true \
-e WEBSOCKET_ENABLED=true \
-e ROCKET_WORKERS=40 \
-e DOMAIN=https://bt.26hk.cn  \
-e SMTP_HOST=smtp.office365.com  \
-e SMTP_FROM=hk@clarifyffg.onmicrosoft.com  \
-e SMTP_PORT=587  \
-e SMTP_SECURITY=starttls \
-e SMTP_USERNAME=hk@clarifyffg.onmicrosoft.com \
-e SMTP_PASSWORD=7SobJRJ3SC7Xpx  \
-e ADMIN_TOKEN=@8yJ9pYp*Xz43z%F \
-v /www/wwwroot/bt.26hk.cn/ :/data/ \
-p 6280:80  \
-p 3012:3012  \
vaultwarden/server:latest
```
